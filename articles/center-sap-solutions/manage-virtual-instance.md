---
title: Manage a Virtual Instance for SAP solutions (preview)
description: Learn how to configure a Virtual Instance for SAP solutions (VIS) resource in Azure Center for SAP solutions (ACSS) through the Azure portal.
ms.service: azure-center-sap-solutions
ms.topic: how-to
ms.date: 07/19/2022
author: lauradolan
ms.author: ladolan
#Customer intent: As a developer, I want to configure my Virtual Instance for SAP solutions resource so that I can find system properties and connect to databases.
---

# Manage a Virtual Instance for SAP solutions (preview)

[!INCLUDE [Preview content notice](./includes/preview.md)]

In this article, you'll learn how to view the *Virtual Instance for SAP solutions (VIS)* resource created in *Azure Center for SAP solutions (ACSS)* through the Azure portal. You can use these steps to find your SAP system's properties and connect parts of the VIS to other resources like databases.

## Prerequisites

- An Azure subscription. 
- **Contributor** role access to the subscription or resource groups where you plan to deploy the SAP system.
- The ACSS application **Azure SAP Workloads Management** also needs  **Contributor** role access to the resource groups for the SAP system. There are two options to grant access:
    - If your Azure account has **Owner** or **User Access Admin** role access, you can automatically grant access to the application when deploying or registering the SAP system.
    - If your Azure account doesn't have **Owner** or **User Access Admin** role access, you can enable access for the ACSS application.

## Open VIS in portal

To configure your VIS in the Azure portal:

1. Open the [Azure portal](https://portal.azure.com) in a browser.
1. Sign in with your Azure account that has the necessary role access as described in the [prerequisites](#prerequisites).
1. In the search field in the navigation menu, enter and select **Azure Center for SAP solutions**.
1. On the **Azure Center for SAP solutions** overview page, search for and select **Virtual Instances for SAP solutions** in the sidebar menu.
1. On the **Virtual Instances for SAP solutions** page, select the VIS that you want to view.

    :::image type="content" source="media/configure-virtual-instance/select-vis.png" lightbox="media/configure-virtual-instance/select-vis.png" alt-text="Screenshot of Azure portal, showing the VIS page in the ACSS service with a table of available VIS resources.":::

## Monitor VIS

To see infrastructure-based metrics for the VIS, [open the VIS in the Azure portal](#open-vis-in-portal). On the **Overview** pane, select the **Monitoring** tab. You can see the following metrics:

- VM utilization by ASCS and Application Server instances. The graph shows CPU usage percentage for all VMs that support the ASCS and Application Server instances.
- VM utilization by the database instance. The graph shows CPU usage percentage for all VMs that support the database instance.
- IOPS consumed by the database instance's data disk. The graph shows the percentage of disk utilization by all VMs that support the database instance.

## View instance properties

To view properties for the instances within your VIS, first [open the VIS in the Azure portal](#open-vis-in-portal). 

In the sidebar menu, look under the section **SAP resources**:

- To see properties of ASCS instances, select **Central server instances**.
- To see properties of application server instances, select **App server instances**.
- To see properties of database instances, select **Databases**.

:::image type="content" source="media/configure-virtual-instance/sap-resources.png" lightbox="media/configure-virtual-instance/sap-resources.png" alt-text="Screenshot of VIS resource in Azure portal, showing SAP resources pages in the sidebar menu for ASCS, App server, and Database instances.":::

## Connect to HANA database

If you've deployed an SAP system using ACSS, [find the SAP system's main password and HANA database passwords](#find-sap-and-hana-passwords).

The HANA database username is either `system` or `SYSTEM` for:

- Distributed High Availability (HA) SAP systems
- Distributed non-HA systems
- Standalone systems

### Find SAP and HANA passwords

To retrieve the password:

1. [Open the VIS in the Azure portal](#open-vis-in-portal).
1. On the overview page, select the **Managed resource group**.

    :::image type="content" source="media/configure-virtual-instance/select-managed-resource-group.png" lightbox="media/configure-virtual-instance/select-managed-resource-group.png" alt-text="Screenshot of VIS resource in the Azure portal, showing selection of managed resource group on the overview page.":::

1. On the resource group's page, select the **Key vault** resource in the table.

    :::image type="content" source="media/configure-virtual-instance/select-key-vault.png" lightbox="media/configure-virtual-instance/select-key-vault.png" alt-text="Screenshot of managed resource group in the Azure portal, showing the selection of the key vault on the overview page.":::

1. On the key vault's page, select **Secrets** in the navigation menu under **Settings**.
1. Make sure that you have access to all the secrets. If you have correct permissions, you can see the SAP password file listed in the table, which hosts the global password for your SAP system.
1. Select the SAP password file name to open the secret's page.
1. Copy the **Secret value**.

If you get the warning **The operation 'List' is not enabled in this key vault's access policy.** with the message **You are unauthorized to view these contents.**:

1. Make sure that you're responsible to manage these secrets in your organization.
1. In the sidebar menu, under **Settings**, select **Access policies**.
1. On the access policies page for the key vault, select **+ Add Access Policy**.
1. In the pane **Add access policy**, configure the following settings.
    1. For **Configure from template (optional)**, select **Key, Secret, & Certificate Management**.
    1. For **Key permissions**, select the keys that you want to use.
    1. For **Secret permissions**, select the secrets that you want to use.
    1. For **Certificate permissions**, select the certificates that you want to use.
    1. For **Select principal**, assign your own account name.
1. Select **Add** to add the policy.
1. In the access policy's menu, select **Save** to save your settings.
1. In the sidebar menu, under **Settings**, select **Secrets**.
1. On the secrets page for the key vault, make sure you can now see the SAP password file.

## Delete VIS

When you delete a VIS, you also delete the managed resource group and all instances that are attached to the VIS. For example, the VIS, ASCS, Application Server, and Database instances are deleted.
Any Azure physical resources aren't deleted when you delete a VIS. For example, the VMs, disks, NICs, and other resources aren't deleted.

> [!WARNING]
> Deleting a VIS is a permanent action! It's not possible to restore a deleted VIS.

To delete a VIS:

1. [Open the VIS in the Azure portal](#open-vis-in-portal).
1. On the overview page's menu, select **Delete**.

    :::image type="content" source="media/configure-virtual-instance/delete-vis-button.png" lightbox="media/configure-virtual-instance/delete-vis-button.png" alt-text="Screenshot of VIS resource in the Azure portal, showing delete button in the overview page's menu..":::

1. In the deletion pane, make sure that you want to delete this VIS and related resources. You can see a count for each type of resource to be deleted.
    
    :::image type="content" source="media/configure-virtual-instance/delete-vis-prompt.png" lightbox="media/configure-virtual-instance/delete-vis-prompt.png" alt-text="Screenshot of deletion prompt pane for a VIS resource in the Azure portal, showing list of related resources and confirmation field to enable the delete button.":::

1. Enter **YES** in the confirmation field.
1. Select **Delete** to delete the VIS.
1. Wait for the deletion operation to complete for the VIS and related resources.

After you delete a VIS, you can register the SAP system again. Open ACSS in the Azure portal, and select **Register an existing SAP system**. 

## Next steps

- [Monitor SAP system from the Azure portal](monitor-portal.md)
- [Get quality checks and insights for your VIS](get-quality-checks-insights.md)
