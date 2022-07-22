---
title: Manage workspaces in portal or Python SDK
titleSuffix: Azure Machine Learning
description: Learn how to manage Azure Machine Learning workspaces in the Azure portal or with the SDK for Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 03/08/2022
ms.topic: how-to
ms.custom: fasttrack-edit, FY21Q4-aml-seo-hack, contperf-fy21q4, sdkv1, event-tier1-build-2022
---

# Manage Azure Machine Learning workspaces in the portal or with the Python SDK

In this article, you create, view, and delete [**Azure Machine Learning workspaces**](concept-workspace.md) for [Azure Machine Learning](overview-what-is-azure-machine-learning.md), using the Azure portal or the [SDK for Python](/python/api/overview/azure/ml/)

As your needs change or requirements for automation increase you can also manage workspaces [using the CLI](v1/reference-azure-machine-learning-cli.md),  or [via the VS Code extension](how-to-setup-vs-code.md).

## Prerequisites

* An Azure subscription. If you don't have an Azure subscription, create a free account before you begin. Try the [free or paid version of Azure Machine Learning](https://azure.microsoft.com/free/) today.
* If using the Python SDK, [install the SDK](/python/api/overview/azure/ml/install).

## Limitations

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

* By default, creating a workspace also creates an Azure Container Registry (ACR).  Since ACR does not currently support unicode characters in resource group names, use a resource group that does not contain these characters.

* Azure Machine Learning does not support hierarchical namespace (Azure Data Lake Storage Gen2 feature) for the workspace's default storage account.

[!INCLUDE [application-insight](../../includes/machine-learning-application-insight.md)]

## Create a workspace

# [Python](#tab/python)

[!INCLUDE [sdk v1](../../includes/machine-learning-sdk-v1.md)]

* **Default specification.** By default, dependent resources and the resource group will be created automatically. This code creates a workspace named `myworkspace` and a resource group named `myresourcegroup` in `eastus2`.
    
    ```python
    from azureml.core import Workspace
    
    ws = Workspace.create(name='myworkspace',
                   subscription_id='<azure-subscription-id>',
                   resource_group='myresourcegroup',
                   create_resource_group=True,
                   location='eastus2'
                   )
    ```
    Set `create_resource_group` to False if you have an existing Azure resource group that you want to use for the workspace.

* <a name="create-multi-tenant"></a>**Multiple tenants.**  If you have multiple accounts, add the tenant ID of the Azure Active Directory you wish to use.  Find your tenant ID from the [Azure portal](https://portal.azure.com) under **Azure Active Directory, External Identities**.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **[Sovereign cloud](reference-machine-learning-cloud-parity.md)**. You'll need extra code to authenticate to Azure if you're working in a sovereign cloud.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **Use existing Azure resources**.  You can also create a workspace that uses existing Azure resources with the Azure resource ID format. Find the specific Azure resource IDs in the Azure portal or with the SDK. This example assumes that the resource group, storage account, key vault, App Insights, and container registry already exist.

   ```python
   import os
   from azureml.core import Workspace
   from azureml.core.authentication import ServicePrincipalAuthentication

   service_principal_password = os.environ.get("AZUREML_PASSWORD")

   service_principal_auth = ServicePrincipalAuthentication(
      tenant_id="<tenant-id>",
      username="<application-id>",
      password=service_principal_password)

                        auth=service_principal_auth,
                             subscription_id='<azure-subscription-id>',
                             resource_group='myresourcegroup',
                             create_resource_group=False,
                             location='eastus2',
                             friendly_name='My workspace',
                             storage_account='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.storage/storageaccounts/mystorageaccount',
                             key_vault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/mykeyvault',
                             app_insights='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.insights/components/myappinsights',
                             container_registry='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.containerregistry/registries/mycontainerregistry',
                             exist_ok=False)
   ```

For more information, see [Workspace SDK reference](/python/api/azureml-core/azureml.core.workspace.workspace).

If you have problems in accessing your subscription, see [Set up authentication for Azure Machine Learning resources and workflows](how-to-setup-authentication.md), as well as the [Authentication in Azure Machine Learning](https://aka.ms/aml-notebook-auth) notebook.

# [Portal](#tab/azure-portal)

1. Sign in to the [Azure portal](https://portal.azure.com/) by using the credentials for your Azure subscription. 

1. In the upper-left corner of Azure portal, select **+ Create a resource**.

      ![Create a new resource](./media/how-to-manage-workspace/create-workspace.gif)

1. Use the search bar to find **Machine Learning**.

1. Select **Machine Learning**.

1. In the **Machine Learning** pane, select **Create** to begin.

1. Provide the following information to configure your new workspace:

   Field|Description 
   ---|---
   Workspace name |Enter a unique name that identifies your workspace. In this example, we use **docs-ws**. Names must be unique across the resource group. Use a name that's easy to recall and to differentiate from workspaces created by others. The workspace name is case-insensitive.
   Subscription |Select the Azure subscription that you want to use.
   Resource group | Use an existing resource group in your subscription or enter a name to create a new resource group. A resource group holds related resources for an Azure solution. In this example, we use **docs-aml**. You need *contributor* or *owner* role to use an existing resource group.  For more information about access, see [Manage access to an Azure Machine Learning workspace](how-to-assign-roles.md).
   Region | Select the Azure region closest to your users and the data resources to create your workspace.
   | Storage account | The default storage account for the workspace. By default, a new one is created. |
   | Key Vault | The Azure Key Vault used by the workspace. By default, a new one is created. |
   | Application Insights | The application insights instance for the workspace. By default, a new one is created. |
   | Container Registry | The Azure Container Registry for the workspace. By default, a new one is _not_ initially created for the workspace. Instead, it is created once you need it when creating a Docker image during training or deployment. |

   :::image type="content" source="media/how-to-manage-workspace/create-workspace-form.png" alt-text="Configure your workspace.":::

1. When you're finished configuring the workspace, select **Review + Create**. Optionally, use the [Networking](#networking) and [Advanced](#advanced) sections to configure more settings for the workspace.

1. Review the settings and make any other changes or corrections. When you're satisfied with the settings, select **Create**.

   > [!Warning] 
   > It can take several minutes to create your workspace in the cloud.

   When the process is finished, a deployment success message appears. 
 
 1. To view the new workspace, select **Go to resource**.
 
---



### Networking	

> [!IMPORTANT]	
> For more information on using a private endpoint and virtual network with your workspace, see [Network isolation and privacy](how-to-network-security-overview.md).


# [Python](#tab/python)

The Azure Machine Learning Python SDK provides the [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) class, which can be used with [Workspace.create()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) to create a workspace with a private endpoint. This class requires an existing virtual network.

# [Portal](#tab/azure-portal)

1. The default network configuration is to use a __Public endpoint__, which is accessible on the public internet. To limit access to your workspace to an Azure Virtual Network you have created, you can instead select __Private endpoint__ as the __Connectivity method__, and then use __+ Add__ to configure the endpoint.	

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="Private endpoint selection":::	

1. On the __Create private endpoint__ form, set the location, name, and virtual network to use. If you'd like to use the endpoint with a Private DNS Zone, select __Integrate with private DNS zone__ and select the zone using the __Private DNS Zone__ field. Select __OK__ to create the endpoint. 	

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="Private endpoint creation":::	

1. When you are finished configuring networking, you can select __Review + Create__, or advance to the optional __Advanced__ configuration.

---



### Advanced

By default, metadata for the workspace is stored in an Azure Cosmos DB instance that Microsoft maintains. This data is encrypted using Microsoft-managed keys.

To limit the data that Microsoft collects on your workspace, select __High business impact workspace__ in the portal, or set `hbi_workspace=true ` in Python. For more information on this setting, see [Encryption at rest](concept-data-encryption.md#encryption-at-rest).

> [!IMPORTANT]	
> Selecting high business impact can only be done when creating a workspace. You cannot change this setting after workspace creation.	

#### Use your own key

You can provide your own key for data encryption. Doing so creates the Azure Cosmos DB instance that stores metadata in your Azure subscription. For more information, see [Customer-managed keys](concept-customer-managed keys.md).


Use the following steps to provide your own key:

> [!IMPORTANT]	
> Before following these steps, you must first perform the following actions:	
>
> Follow the steps in [Configure customer-managed keys](how-to-setup-customer-managed-keys.md) to:
> * Register the Azure Cosmos DB provider
> * Create and configure an Azure Key Vault
> * Generate a key

# [Python](#tab/python)

Use `cmk_keyvault` and `resource_cmk_uri` to specify the customer managed key.

```python
from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
               subscription_id='<azure-subscription-id>',
               resource_group='myresourcegroup',
               create_resource_group=True,
               location='eastus2'
               cmk_keyvault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/<keyvault-name>', 
               resource_cmk_uri='<key-identifier>'
               )

```

# [Portal](#tab/azure-portal)

1. Select __Customer-managed keys__, and then select __Click to select key__.

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="Customer-managed keys":::

1. On the __Select key from Azure Key Vault__ form, select an existing Azure Key Vault, a key that it contains, and the version of the key. This key is used to encrypt the data stored in Azure Cosmos DB. Finally, use the __Select__ button to use this key.

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="Select the key":::

---

### Download a configuration file

If you will be creating a [compute instance](quickstart-create-resources.md), skip this step.  The compute instance has already created a copy of this file for you.

# [Python](#tab/python)

If you plan to use code on your local environment that references this workspace (`ws`), write the configuration file:

```python
ws.write_config()
```

# [Portal](#tab/azure-portal)

If you plan to use code on your local environment that references this workspace, select  **Download config.json** from the **Overview** section of the workspace.  

   ![Download config.json](./media/how-to-manage-workspace/configure.png)

---

Place the file into  the directory structure with your Python scripts or Jupyter Notebooks. It can be in the same directory, a subdirectory named *.azureml*, or in a parent directory. When you create a compute instance, this file is added to the correct directory on the VM for you.

## Connect to a workspace

[!INCLUDE [sdk v1](../../includes/machine-learning-sdk-v1.md)]

In your Python code, you create a workspace object to connect to your workspace.  This code will read the contents of the configuration file to find your workspace.  You will get a prompt to sign in if you are not already authenticated.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

* <a name="connect-multi-tenant"></a>**Multiple tenants.**  If you have multiple accounts, add the tenant ID of the Azure Active Directory you wish to use.  Find your tenant ID from the [Azure portal](https://portal.azure.com) under **Azure Active Directory, External Identities**.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.from_config(auth=interactive_auth)
    ```

* **[Sovereign cloud](reference-machine-learning-cloud-parity.md)**. You'll need extra code to authenticate to Azure if you're working in a sovereign cloud.

   [!INCLUDE [sdk v1](../../includes/machine-learning-sdk-v1.md)]

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.from_config(auth=interactive_auth)
    ```
    
If you have problems in accessing your subscription, see [Set up authentication for Azure Machine Learning resources and workflows](how-to-setup-authentication.md), as well as the [Authentication in Azure Machine Learning](https://aka.ms/aml-notebook-auth) notebook.

## <a name="view"></a>Find a workspace

See a list of all the workspaces you can use.

# [Python](#tab/python)

[!INCLUDE [sdk v1](../../includes/machine-learning-sdk-v1.md)]

Find your subscriptions in the [Subscriptions page in the Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Copy the ID and use it in the code below to see all workspaces available for that subscription.

```python
from azureml.core import Workspace

Workspace.list('<subscription-id>')
```

The Workspace.list(..) method does not return the full workspace object. It includes only basic information about existing workspaces in the subscription. To get a full object for specific workspace, use Workspace.get(..).

# [Portal](#tab/azure-portal)

1. Sign in to the [Azure portal](https://portal.azure.com/).

1. In the top search field, type **Machine Learning**.  

1. Select **Machine Learning**.

   ![Search for Azure Machine Learning workspace](./media/how-to-manage-workspace/find-workspaces.png)

1. Look through the list of workspaces found. You can filter based on subscription, resource groups, and locations.  

1. Select a workspace to display its properties.

---

## Search for assets across a workspace (preview)

With the public preview search capability, you can search for machine learning assets such as jobs, models, components, environments, and data across all workspaces, resource groups, and subscriptions in your organization through a unified global view.

1. Start from [Azure Machine Learning studio](https://ml.azure.com).  
1. If a workspace is open, select either the **Microsoft** menu item or the **Microsoft** link in the breadcrumb at the top of the page.

:::image type="content" source="media/how-to-manage-workspace/back-to-main-page.png" alt-text="Screenshot shows two ways to get to the home page - menu item or breadcrumb link.":::

### Free text search

Type search text into the global search bar on the top of the studio **Microsoft** page and hit enter to trigger a 'contains' search.
A contains search scans across all metadata fields for the given asset and sorts results relevance.

:::image type="content" source="media/how-to-manage-workspace/start-search.png" alt-text="Screenshot shows the top search bar.":::

You can use the asset quick links to navigate to search results for jobs, models, components, environments, and datasets that you created.

Also,  you can change the scope of applicable subscriptions and workspaces via the 'Change' link in the search bar drop down.

:::image type="content" source="./media/how-to-manage-workspace/search-bar.png" alt-text="Search-bar list":::

### Structured search

Select any number of filters to create more specific search queries. The following filters are supported:

* Job:
* Model:
* Component:
* Tags:
* SubmittedBy:
* Environment:
* Dataset:

If an asset filter (job, model, component, environment, dataset) is present, results are scoped to those tabs. Other filters apply to all assets unless an asset filter is also present in the query. Similarly, free text search can be provided alongside filters, but are scoped to the tabs chosen by asset filters, if present.

> [!TIP]
> * Filters search for exact matches of text. Use free text queries for a contains search.
> * Quotations are required around values that include spaces or other special characters.  
> * If duplicate filters are provided, only the first will be recognized in search results.
> * Input text of any language is supported but filter strings must match the provided options (ex. submittedBy:).
> * The tags filter can accept multiple key:value pairs separated by a comma (ex. tags:"key1:value1, key2:value2").

### View search results

You can view your search results in the individual **Jobs**, **Models**, **Components**, **Environments**, and **Data** tabs. Select an asset to open its **Details** page in the context of the relevant workspace. Results from workspaces you don't have permissions to view are not displayed.

:::image type="content" source="./media/how-to-manage-workspace/results.png" alt-text="Results displayed after search":::

If you've used this feature in a previous update, a search result error may occur. Reselect your preferred workspaces in the Directory + Subscription + Workspace tab.

> [!IMPORTANT]	
> Search results may be unexpected for multiword terms in other languages (ex. Chinese characters). 	

## Delete a workspace

When you no longer need a workspace, delete it.  

[!INCLUDE [machine-learning-delete-workspace](../../includes/machine-learning-delete-workspace.md)]

If you accidentally deleted your workspace, you may still be able to retrieve your notebooks. For details, see [Failover for business continuity and disaster recovery](./how-to-high-availability-machine-learning.md#workspace-deletion).

# [Python](#tab/python)

[!INCLUDE [sdk v1](../../includes/machine-learning-sdk-v1.md)]

Delete the workspace `ws`:

```python
ws.delete(delete_dependent_resources=False, no_wait=False)
```

The default action is not to delete resources associated with the workspace, that is, container registry, storage account, key vault, and application insights.  Set `delete_dependent_resources` to True to delete these resources as well.

# [Portal](#tab/azure-portal)

In the [Azure portal](https://portal.azure.com/), select **Delete**  at the top of the workspace you wish to delete.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Delete workspace":::

---

## Clean up resources

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## Troubleshooting

* **Supported browsers in Azure Machine Learning studio**: We recommend that you use the most up-to-date browser that's compatible with your operating system. The following browsers are supported:
  * Microsoft Edge (The new Microsoft Edge, latest version. Not Microsoft Edge legacy)
  * Safari (latest version, Mac only)
  * Chrome (latest version)
  * Firefox (latest version)

* **Azure portal**: 
  * If you go directly to your workspace from a share link from the SDK or the Azure portal, you can't view the standard **Overview** page that has subscription information in the extension. In this scenario, you also can't switch to another workspace. To view another workspace, go directly to [Azure Machine Learning studio](https://ml.azure.com) and search for the workspace name.
  * All assets (Datasets, Experiments, Computes, and so on) are available only in [Azure Machine Learning studio](https://ml.azure.com). They're *not* available from the Azure portal.
  * Attempting to export a template for a workspace from the Azure portal may return an error similar to the following text: `Could not get resource of the type <type>. Resources of this type will not be exported.` As a workaround, use one of the templates provided at [https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices) as the basis for your template.

### Workspace diagnostics

[!INCLUDE [machine-learning-workspace-diagnostics](../../includes/machine-learning-workspace-diagnostics.md)]

### Resource provider errors

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]
 

### Deleting the Azure Container Registry

The Azure Machine Learning workspace uses Azure Container Registry (ACR) for some operations. It will automatically create an ACR instance when it first needs one.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## Examples

Examples of creating a workspace:
* Use Azure portal to [create a workspace and compute instance](quickstart-create-resources.md)

## Next steps

Once you have a workspace, learn how to [Train and deploy a model](tutorial-train-deploy-notebook.md).

To learn more about planning a workspace for your organization's requirements, see [Organize and set up Azure Machine Learning](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-resource-organization).

To check for problems with your workspace, see [How to use workspace diagnostics](how-to-workspace-diagnostic-api.md).

If you need to move a workspace to another Azure subscription, see [How to move a workspace](how-to-move-workspace.md).

For information on how to keep your Azure ML up to date with the latest security updates, see [Vulnerability management](concept-vulnerability-management.md).