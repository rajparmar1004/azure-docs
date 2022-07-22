---
title: Implement disaster recovery using backup and restore in API Management
titleSuffix: Azure API Management
description: Learn how to use backup and restore to perform disaster recovery in Azure API Management.
services: api-management
author: dlepow

ms.service: api-management
ms.topic: how-to
ms.date: 10/03/2021
ms.author: apimpm 
ms.custom: devx-track-azurepowershell
---

# How to implement disaster recovery using service backup and restore in Azure API Management

By publishing and managing your APIs via Azure API Management, you're taking advantage of fault tolerance and infrastructure capabilities that you'd otherwise design, implement, and manage manually. The Azure platform mitigates a large fraction of potential failures at a fraction of the cost.

To recover from availability problems that affect the region that hosts your API Management service, be ready to reconstitute your service in another region at any time. Depending on your recovery time objective, you might want to keep a standby service in one or more regions. You might also try to maintain their configuration and content in sync with the active service according to your recovery point objective. The service backup and restore features provide the necessary building blocks for implementing disaster recovery strategy.

Backup and restore operations can also be used for replicating API Management service configuration between operational environments, for example, development and staging. Beware that runtime data such as users and subscriptions will be copied as well, which might not always be desirable.

This guide shows how to automate backup and restore operations and how to ensure successful authenticating of backup and restore requests by Azure Resource Manager.

> [!IMPORTANT]
> Restore operation doesn't change custom hostname configuration of the target service. We recommend to use the same custom hostname and TLS certificate for both active and standby services, so that, after restore operation completes, the traffic can be re-directed to the standby instance by a simple DNS CNAME change.
>
> Backup operation does not capture pre-aggregated log data used in reports shown on the **Analytics** blade in the Azure portal.

> [!WARNING]
> Each backup expires after 30 days. If you attempt to restore a backup after the 30-day expiration period has expired, the restore will fail with a `Cannot restore: backup expired` message.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## Authenticating Azure Resource Manager requests

> [!IMPORTANT]
> The REST API for backup and restore uses Azure Resource Manager and has a different authentication mechanism than the REST APIs for managing your API Management entities. The steps in this section describe how to authenticate Azure Resource Manager requests. For more information, see [Authenticating Azure Resource Manager requests](/rest/api/azure).

All of the tasks that you do on resources using the Azure Resource Manager must be authenticated with Azure Active Directory using the following steps:

-   Add an application to the Azure Active Directory tenant.
-   Set permissions for the application that you added.
-   Get the token for authenticating requests to Azure Resource Manager.

### Create an Azure Active Directory application

1. Sign in to the [Azure portal](https://portal.azure.com).
1. Using the subscription that contains your API Management service instance, navigate to the [Azure portal - App registrations](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) to register an app in Active Directory.
    > [!NOTE]
    > If the Azure Active Directory default directory isn't visible to your account, contact the administrator of the Azure subscription to grant the required permissions to your account.
1. Select **+ New registration**.
1. On the **Register an application** page, set the values as follows:
    
    * Set **Name** to a meaningful name.
    * Set **Supported account types** to **Accounts in this organizational directory only**. 
    * In **Redirect URI** enter a placeholder URL such as `https://resources`. It's a required field, but the value isn't used later. 
    * Select **Register**.

### Add permissions

1. Once the application is created, select **API permissions** > **+ Add a permission**.
1. Select **Microsoft APIs**.
1. Select **Azure Service Management**.

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/add-app-permission.png" alt-text="Screenshot that shows how to add app permissions."::: 

1. Click **Delegated Permissions** beside the newly added application, and check the box for **Access Azure Service Management as organization users (preview)**.

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/delegated-app-permission.png" alt-text="Screenshot that shows adding delegated app permissions.":::

1. Select **Add permissions**.

### Configure your app

Before calling the APIs that generate the backup and restore, you need to get a token. The following example uses the [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet package to retrieve the token.

> [!IMPORTANT]
> The [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet package and Azure AD Authentication Library (ADAL) have been deprecated. No new features have been added since June 30, 2020.   We strongly encourage you to upgrade, see the [migration guide](../active-directory/develop/msal-migration.md) for more details. 

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Replace `{tenant id}`, `{application id}`, and `{redirect uri}` using the following instructions:

1. Replace `{tenant id}` with the tenant ID of the Azure Active Directory application you created. You can access the ID by clicking **App registrations** -> **Endpoints**.

    ![Endpoints][api-management-endpoint]

2. Replace `{application id}` with the value you get by navigating to the **Settings** page.
3. Replace the `{redirect uri}` with the value from the **Redirect URIs** tab of your Azure Active Directory application.

    Once the values are specified, the code example should return a token similar to the following example:

    ![Token][api-management-arm-token]

    > [!NOTE]
    > The token may expire after a certain period. Execute the code sample again to generate a new token.

## Accessing Azure Storage

API Management uses an Azure Storage account that you specify for backup and restore operations. When running a backup or restore operation, you need to configure access to the storage account. API Management supports two storage access mechanisms: an Azure Storage access key (the default), or an API Management managed identity.

### Configure storage account access key

For steps, see [Manage storage account access keys](../storage/common/storage-account-keys-manage.md?tabs=azure-portal).

### Configure API Management managed identity

> [!NOTE]
> Using an API Management managed identity for storage operations during backup and restore requires API Management REST API version `2021-04-01-preview` or later.

1. Enable a system-assigned or user-assigned [managed identity for API Management](api-management-howto-use-managed-service-identity.md) in your API Management instance.

    * If you enable a user-assigned managed identity, take note of the identity's **Client ID**.
    * If you will back up and restore to different API Management instances, enable a managed identity in both the source and target instances.
1. Assign the identity the **Storage Blob Data Contributor** role, scoped to the storage account used for backup and restore. To assign the role, use the [Azure portal](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md) or other Azure tools.

## Calling the backup and restore operations

The REST APIs are [Api Management Service - Backup](/rest/api/apimanagement/current-ga/api-management-service/backup) and [Api Management Service - Restore](/rest/api/apimanagement/current-ga/api-management-service/restore).

> [!NOTE]
> Backup and restore operations can also be performed with PowerShell [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) and [_Restore-AzApiManagement_](/powershell/module/az.apimanagement/restore-azapimanagement) commands respectively.

Before calling the "backup and restore" operations described in the following sections, set the authorization request header for your REST call.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>Back up an API Management service

To back up an API Management service issue the following HTTP request:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

where:

-   `subscriptionId` - ID of the subscription that holds the API Management service you're trying to back up
-   `resourceGroupName` - name of the resource group of your Azure API Management service
-   `serviceName` - the name of the API Management service you're making a backup of specified at the time of its creation
-   `api-version` - a valid REST API version such as `2020-12-01` or `2021-04-01-preview`.

In the body of the request, specify the target storage account name, blob container name, backup name, and the storage access type. If the storage container doesn't exist, the backup operation creates it.

#### Access using storage access key

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessKey": "{access key for the account}"
}
```

#### Access using managed identity

> [!NOTE]
> Using an API Management managed identity for storage operations during backup and restore requires API Management REST API version `2021-04-01-preview` or later.

**Access using system-assigned managed identity**

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessType": "SystemAssignedManagedIdentity"
}
```

**Access using user-assigned managed identity**

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessType": "UserAssignedManagedIdentity",
    "clientId": "{client ID of user-assigned identity}"
}
```


Set the value of the `Content-Type` request header to `application/json`.

Backup is a long-running operation that may take more than a minute to complete. If the request succeeded and the backup process began, you receive a `202 Accepted` response status code with a `Location` header. Make `GET` requests to the URL in the `Location` header to find out the status of the operation. While the backup is in progress, you continue to receive a `202 Accepted` status code. A Response code of `200 OK` indicates successful completion of the backup operation.

### <a name="step2"> </a>Restore an API Management service

To restore an API Management service from a previously created backup, make the following HTTP request:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

where:

-   `subscriptionId` - ID of the subscription that holds the API Management service you're restoring a backup into
-   `resourceGroupName` - name of the resource group that holds the Azure API Management service you're restoring a backup into
-   `serviceName` - the name of the API Management service being restored into specified at its creation time
-   `api-version` - a valid REST API version such as `2020-12-01` or `2021-04-01-preview`

In the body of the request, specify the existing storage account name, blob container name, backup name, and the storage access type. 

#### Access using storage access key

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessKey": "{access key for the account}"
}
```

#### Access using managed identity

> [!NOTE]
> Using an API Management managed identity for storage operations during backup and restore requires API Management REST API version `2021-04-01-preview` or later.

**Access using system-assigned managed identity**

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessType": "SystemAssignedManagedIdentity"
}
```

**Access using user-assigned managed identity**

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessType": "UserAssignedManagedIdentity",
    "clientId": "{client ID of user-assigned identity}"
}
```

Set the value of the `Content-Type` request header to `application/json`.

Restore is a long-running operation that may take up to 30 or more minutes to complete. If the request succeeded and the restore process began, you receive a `202 Accepted` response status code with a `Location` header. Make 'GET' requests to the URL in the `Location` header to find out the status of the operation. While the restore is in progress, you continue to receive a `202 Accepted` status code. A response code of `200 OK` indicates successful completion of the restore operation.

> [!IMPORTANT]
> **The SKU** of the service being restored into **must match** the SKU of the backed-up service being restored.
>
> **Changes** made to the service configuration (for example, APIs, policies, developer portal appearance) while restore operation is in progress **could be overwritten**.

## Constraints when making backup or restore request

-   While backup is in progress, **avoid management changes in the service** such as SKU upgrade or downgrade, change in domain name, and more.
-   Restore of a **backup is guaranteed only for 30 days** since the moment of its creation.
-   **Changes** made to the service configuration (for example, APIs, policies, and developer portal appearance) while backup operation is in process **might be excluded from the backup and will be lost**.

-   [Cross-Origin Resource Sharing (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) should **not** be enabled on the Blob Service in the Azure Storage Account.
-   **The SKU** of the service being restored into **must match** the SKU of the backed-up service being restored.

## Storage networking constraints

### Access using storage access key

If the storage account is **[firewall][azure-storage-ip-firewall] enabled** and a storage key is used for access, then the customer must **Allow** the set of [Azure API Management control plane IP addresses][control-plane-ip-address] on their storage account for backup or restore to work. The storage account can be in any Azure region except the one where the API Management service is located. For example, if the API Management service is in West US, then the Azure Storage account can be in West US 2 and the customer needs to open the control plane IP 13.64.39.16 (API Management control plane IP of West US) in the firewall. This is because the requests to Azure Storage are not SNATed to a public IP from compute (Azure API Management control plane) in the same Azure region. Cross-region storage requests will be SNATed to the public IP address.

### Access using managed identity

If an API Management system-assigned managed identity is used to access a firewall-enabled storage account, ensure that the storage account [grants access to trusted Azure services](../storage/common/storage-network-security.md?tabs=azure-portal#grant-access-to-trusted-azure-services).

## What is not backed up
-   **Usage data** used for creating analytics reports **isn't included** in the backup. Use [Azure API Management REST API][azure api management rest api] to periodically retrieve analytics reports for safekeeping.
-   [Custom domain TLS/SSL](configure-custom-domain.md) certificates.
-   [Custom CA certificates](api-management-howto-ca-certificates.md), which includes intermediate or root certificates uploaded by the customer.
-   [Virtual network](api-management-using-with-vnet.md) integration settings.
-   [Managed identity](api-management-howto-use-managed-service-identity.md) configuration.
-   [Azure Monitor diagnostic](api-management-howto-use-azure-monitor.md) configuration.
-   [Protocols and ciphers](api-management-howto-manage-protocols-ciphers.md) settings.
-   [Developer portal](developer-portal-faq.md#is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management) content.

The frequency with which you perform service backups affect your recovery point objective. To minimize it, we recommend implementing regular backups and performing on-demand backups after you make changes to your API Management service.

## Next steps

Check out the following related resources for the backup/restore process:

-   [Automating API Management Backup and Restore with Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
- [How to move Azure API Management across regions](api-management-howto-migrate.md)

API Management **Premium** tier also supports [zone redundancy](../availability-zones/migrate-api-mgt.md), which provides resiliency and high availability to a service instance in a specific Azure region (location).

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: /rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
[control-plane-ip-address]: virtual-network-reference.md#control-plane-ip-addresses
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range