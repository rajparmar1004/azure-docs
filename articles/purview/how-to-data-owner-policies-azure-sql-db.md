---
title: Provision access by data owner for Azure SQL DB (preview)
description: Step-by-step guide on how data owners can configure access for Azure SQL DB through Microsoft Purview access policies.
author: inward-eye
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: how-to
ms.date: 07/20/2022
ms.custom: references_regions, event-tier1-build-2022
---
# Provision access by data owner for Azure SQL DB (preview)

[!INCLUDE [feature-in-preview](includes/feature-in-preview.md)]

[Access policies](concept-data-owner-policies.md) allow you to manage access from Microsoft Purview to data sources that have been registered for *Data Use Management*.

This how-to guide describes how a data owner can delegate authoring policies in Microsoft Purview to enable access to Azure SQL DB. The following actions are currently enabled: *SQL Performance Monitoring*, *SQL Security Auditing* and *Read*. *Modify* is not supported at this point. 

## Prerequisites
[!INCLUDE [Access policies generic pre-requisites](./includes/access-policies-prerequisites-generic.md)]
- Create a new Azure SQL DB or use an existing one in one of the currently available regions for this preview feature. You can [follow this guide to create a new Azure SQL DB](/azure/azure-sql/database/single-database-create-quickstart).

**Enforcement of Microsoft Purview policies is available only in the following regions for Azure SQL DB**
- East US2
- West US3
- South Central US
- West Central US
- Canada Central
- Brazil South
- North Europe
- West Europe
- France Central
- UK South
- Central India
- Australia East

## Configuration
[!INCLUDE [Access policies generic configuration](./includes/access-policies-configuration-generic.md)]

### Azure SQL Database configuration
Each Azure SQL Database server needs a Managed Identity assigned to it.
You can use the following PowerShell script:
```powershell
Connect-AzAccount

$context = Get-AzSubscription -SubscriptionId xxxx-xxxx-xxxx-xxxx
Set-AzContext $context

Set-AzSqlServer -ResourceGroupName "RESOURCEGROUPNAME" -ServerName "SERVERNAME" -AssignIdentity
```
You will also need to enable external policy based authorization on the server.

```powershell
$server = Get-AzSqlServer -ResourceGroupName "RESOURCEGROUPNAME" -ServerName "SERVERNAME"

#Initiate the call to the REST API to set externalPolicyBasedAuthorization to true
Invoke-AzRestMethod -Method PUT -Path "$($server.ResourceId)/externalPolicyBasedAuthorizations/MicrosoftPurview?api-version=2021-11-01-preview" -Payload '{"properties":{"externalPolicyBasedAuthorization":true}}'

#Verify that the propery has been set
Invoke-AzRestMethod -Method GET -Path "$($server.ResourceId)/externalPolicyBasedAuthorizations/MicrosoftPurview?api-version=2021-11-01-preview"
```

### Register the data sources in Microsoft Purview
The Azure SQL DB resources need to be registered first with Microsoft Purview to later define access policies. You can follow these guides:

[Register and scan Azure SQL DB](./register-scan-azure-sql-database.md)

After you've registered your resources, you'll need to enable *Data Use Management*. Data Use Management can affect the security of your data, as it delegates to certain Microsoft Purview roles to manage access to the data sources. Secure practices related to Data Use Management are described in this guide:

[How to enable Data Use Management](./how-to-enable-data-use-management.md)

Once your data source has the **Data Use Management** toggle *Enabled*, it will look like this picture. This will enable the access policies to be used with the given SQL server and all its contained databases.
![Screenshot shows how to register a data source for policy.](./media/how-to-data-owner-policies-sql/register-data-source-for-policy-azure-sql-db.png)


## Create and publish a data owner policy

Execute the steps in the **Create a new policy** and **Publish a policy** sections of the [data-owner policy authoring tutorial](./how-to-data-owner-policy-authoring-generic.md#create-a-new-policy). The result will be a data owner policy similar to one of the examples shown in the images.

**Example #1: SQL Performance Monitor policy**. This policy assigns the Azure AD principal 'Mateo Gomez' to the *SQL Performance monitoring* role, in the scope of SQL server *relecloud-sql-srv2*. This policy has also been published to that server.

![Screenshot shows a sample data owner policy giving SQL Performance Monitor access to an Azure SQL Database.](./media/how-to-data-owner-policies-sql/data-owner-policy-example-azure-sql-db-performance-monitor.png)

**Example #2: SQL Security Auditor policy**. Similar to example 1, but choose the *SQL Security auditing* action (instead of *SQL Performance monitoring*), when authoring the policy.

**Example #3: Read policy**. This policy assigns the Azure AD principal 'Robert Murphy' to the *SQL Data reader* role, in the scope of SQL server *relecloud-sql-srv2*. This policy has also been published to that server.

![Screenshot shows a sample data owner policy giving Data Reader access to an Azure SQL Database.](./media/how-to-data-owner-policies-sql/data-owner-policy-example-azure-sql-db-data-reader.png)


>[!Important]
> - Publish is a background operation. It can take up to **4 minutes** for the changes to be reflected in this data source.
> - Changing a policy does not require a new publish operation. The changes will be picked up with the next pull.

### Test the policy

The Azure AD Accounts referenced in the access policies should now be able to connect to any database in the server to which the policies are published.

#### Force policy download
It is possible to force an immediate download of the latest published policies to the current SQL database by running the following command. The minimal permission required to run it is membership in ##MS_ServerStateManager##-server role.

```sql
-- Force immediate download of latest published policies
exec sp_external_policy_refresh reload
```  

#### Analyze downloaded policy state from SQL
The following DMVs can be used to analyze which policies have been downloaded and are currently assigned to Azure AD accounts. The minimal permission required to run them is VIEW DATABASE SECURITY STATE - or assigned Action Group *SQL Security Auditor*.

```sql

-- Lists generally supported actions
SELECT * FROM sys.dm_server_external_policy_actions

-- Lists the roles that are part of a policy published to this server
SELECT * FROM sys.dm_server_external_policy_roles

-- Lists the links between the roles and actions, could be used to join the two
SELECT * FROM sys.dm_server_external_policy_role_actions

-- Lists all Azure AD principals that were given connect permissions  
SELECT * FROM sys.dm_server_external_policy_principals

-- Lists Azure AD principals assigned to a given role on a given resource scope
SELECT * FROM sys.dm_server_external_policy_role_members

-- Lists Azure AD principals, joined with roles, joined with their data actions
SELECT * FROM sys.dm_server_external_policy_principal_assigned_actions
```

## Additional information

### Policy action mapping

This section contains a reference of how actions in Microsoft Purview data policies map to specific actions in Azure SQL DB.

| **Microsoft Purview policy action** | **Data source specific actions**     |
|-------------------------------------|--------------------------------------|
|                                     |                                      |
| *Read* |Microsoft.Sql/sqlservers/Connect |
||Microsoft.Sql/sqlservers/databases/Connect |
||Microsoft.Sql/Sqlservers/Databases/Schemas/Tables/Rows|
||Microsoft.Sql/Sqlservers/Databases/Schemas/Views/Rows |
|||
| *SQL Performance Monitor* |Microsoft.Sql/sqlservers/Connect |
||Microsoft.Sql/sqlservers/databases/Connect |
||Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerMetadata/rows/select |
||Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseMetadata/rows/select |
||Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerState/rows/select |
||Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseState/rows/select |
|||               
| *SQL Security Auditor* |Microsoft.Sql/sqlservers/Connect |
||Microsoft.Sql/sqlservers/databases/Connect |
||Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerSecurityState/rows/select |
||Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseSecurityState/rows/select |
||Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerSecurityMetadata/rows/select |
||Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseSecurityMetadata/rows/select |
|||

## Next steps
Check blog, demo and related how-to guides
* [Demo of access policy for Azure Storage](https://learn-video.azurefd.net/vod/player?id=caa25ad3-7927-4dcc-88dd-6b74bcae98a2)
* [Concepts for Microsoft Purview data owner policies](./concept-data-owner-policies.md)
* Blog: [Private preview: controlling access to Azure SQL at scale with policies in Purview](https://techcommunity.microsoft.com/t5/azure-sql-blog/private-preview-controlling-access-to-azure-sql-at-scale-with/ba-p/2945491)
* [Enable Microsoft Purview data owner policies on all data sources in a subscription or a resource group](./how-to-data-owner-policies-resource-group.md)
* [Enable Microsoft Purview data owner policies on an Arc-enabled SQL Server](./how-to-data-owner-policies-arc-sql-server.md)
