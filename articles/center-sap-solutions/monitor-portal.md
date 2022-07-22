---
title: Monitor SAP system from the Azure portal (preview)
description: Learn how to monitor the health and status of your SAP system, along with important SAP metrics, using the Azure Center for SAP solutions (ACSS) within the Azure portal.
ms.service: azure-center-sap-solutions
ms.topic: how-to
ms.date: 07/19/2022
author: lauradolan
ms.author: ladolan
#Customer intent: As a developer, I want to set up monitoring for my Virtual Instance for SAP solutions, so that I can monitor the health and status of my SAP system in Azure Center for SAP solutions.
---

# Monitor SAP system from Azure portal (preview)

[!INCLUDE [Preview content notice](./includes/preview.md)]

In this how-to guide, you'll learn how to monitor the health and status of your SAP system with *Azure Center for SAP solutions (ACSS)* through the Azure portal. The following capabilities are available for your *Virtual Instance for SAP solutions* resource:

- Monitor your SAP system, along with its instances and VMs.
- Analyze important SAP infrastructure metrics.
- Create and/or register an instance of Azure Monitor for SAP solutions (AMS) to monitor SAP platform metrics.

## System health

The *health* of an SAP system within ACSS is based on the status of its underlying instances. Codes for health are also determined by the collective impact of these instances on the performance of the SAP system.

Possible values for health are:

- **Healthy**: the system is healthy.
- **Unhealthy**: the system is unhealthy.
- **Degraded**: the system shows signs of degradation and possible failure.
- **Unknown**: the health of the system is unknown.

## System status

The *status* of an SAP system within ACSS indicates the current state of the system. 

Possible values for status are:

- **Running**: the system is running.
- **Offline**: the system is offline.
- **Partially running**: the system is partially running.
- **Unavailable**: the system is unavailable.

## Instance properties

When you [check the health or status of your SAP system in the Azure portal](#check-health-and-status), the results for each instance are listed and color-coded.

### Color-coding for states

For ASCS and application server instances:

| Color code   | Status      | Health      |
| ------------ | ----------- | ----------- |
| Green        | Running     | Healthy     |
| Yellow       | Running     | Degraded    |
| Red          | Running     | Unhealthy   | 
| Gray         | Unavailable | Unknown     |

For database instances:

| Color code      | Status      |
| --------------- | ----------- |
| Green           | Running     |
| Yellow          | Unavailable |
| Red             | Unavailable |
| Gray            | Unavailable |

### Example scenarios

The following are different scenarios with the corresponding status and health values.

| Application instance state | ASCS instance state     | System status | System health |
| -------------------------- | ----------------------- | ------------- | ------------- |
| Running and healthy        | Running and healthy     | Running       | Healthy       |
| Running and degraded       | Running and healthy     | Running       | Degraded      |
| Running and unhealthy      | Running and healthy     | Running       | Unhealthy     |

## Health and status codes

When you [check the health or status of your SAP system in the Azure portal](#check-health-and-status), these values are displayed with corresponding symbols.

Depending on the type of instance, there are different color-coded scenarios with different status and health outcomes.

For ASCS and application server instances, the following color-coding applies:

## Check health and status

> [!NOTE]
> After creating your virtual Instance for SAP solutions (VIS), you might need to wait 2-5 minutes to see health and status information.
> 
> The average latency to get health and status information is about 30 seconds.

To check basic health and status settings:

1. Sign in to the [Azure portal](https://portal.azure.com).

1. In the search bar, enter `SAP on Azure`, then select **Azure Center for SAP solutions** in the results.

1. On the service's page, select **Virtual Instances for SAP solutions** in the sidebar menu.

1. On the page for the VIS, review the table of instances. There is an overview of health and status information for each VIS.

    :::image type="content" source="media/monitor-portal/all-vis-statuses.png" lightbox="media/monitor-portal/all-vis-statuses.png" alt-text="Screenshot of the ACSS service in the Azure portal, showing a page of all VIS resources with their health and status information.":::

1. Select the VIS you want to check.

1. On the **Overview** page for the VIS resource, select the **Properties** tab.

    :::image type="content" source="media/monitor-portal/vis-resource-overview.png" lightbox="media/monitor-portal/vis-resource-overview.png" alt-text="Screenshot of the VIS resource overview in the Azure portal, showing health and status information and the Properties tab highlighted.":::

1. On the properties page for the VIS, review the **SAP status** section to see the health of SAP instances. Review the **Virtual machines** section to see the health of VMs inside the VIS.

    :::image type="content" source="media/monitor-portal/properties-tab.png" lightbox="media/monitor-portal/properties-tab.png" alt-text="Screenshot of the Properties tab for the VIS resource overview, showing the SAP status and Virtual machines details.":::

To see information about ASCS instances:

1. Open the VIS in the Azure portal, as previously described.

1. In the sidebar menu, under **SAP resources**, select **Central service instances**.

1. Select an instance from the table to see its properties.

    :::image type="content" source="media/monitor-portal/ascs-vm-details.png" lightbox="media/monitor-portal/ascs-vm-details.png" alt-text="Screenshot of an ASCS instance in the Azure portal, showing health and status information for the VM.":::

To see information about SAP application server instances:

1. Open the VIS in the Azure portal, as previously described.

1. In the sidebar menu, under **SAP resources**, select **App server instances**.

1. Select an instance from the table to see its properties.
    
    :::image type="content" source="media/monitor-portal/app-server-vm-details.png" lightbox="media/monitor-portal/app-server-vm-details.png" alt-text="Screenshot of an Application Server instance in the Azure portal, showing health and status information for the VM.":::

## Monitor SAP infrastructure

ACSS enables you to analyze important SAP infrastructure metrics from the Azure portal. 

1. Sign in to the [Azure portal](https://portal.azure.com).

1. In the search bar, enter `SAP on Azure`, then select **Azure Center for SAP solutions** in the results.

1. On the service's page, select **SAP Virtual Instances** in the sidebar menu.

1. On the page for the VIS, select the VIS from the table.

1. On the overview page for the VIS, select the **Monitoring** tab.

    :::image type="content" source="media/monitor-portal/vis-resource-overview-monitoring.png" lightbox="media/monitor-portal/vis-resource-overview-monitoring.png" alt-text="Screenshot of the Monitoring tab for a VIS resource in the Azure portal, showing monitoring charts for CPU utilization and IOPS.":::

1. Review the monitoring charts, which include: 

    1. CPU utilization by the Application server and ASCS server
    
    1. IOPS percentage consumed by the Database server instance

    1. CPU utilization by the Database server instance

1. Select any of the monitoring charts to do more in-depth analysis with Azure Monitor metrics explorer.

## Configure Azure Monitor

You can also set up or register AMS to monitor SAP platform-level metrics.

1. Sign in to the [Azure portal](https://portal.azure.com).

1. In the search bar, enter `SAP on Azure`, then select **Azure Center for SAP solutions** in the results.

1. On the service's page, select **SAP Virtual Instances** in the sidebar menu.

1. On the page for the VIS, select the VIS from the table.

1. In the sidebar menu for the VIS, under **Monitoring**, select **Azure Monitor for SAP**.

1. Select whether you want to [create a new AMS instance](#create-new-ams-resource), or [register an existing AMS instance](#register-existing-ams-resource). If you don't see this option, you've already configured this setting.

    :::image type="content" source="media/monitor-portal/monitoring-setup.png" lightbox="media/monitor-portal/monitoring-setup.png" alt-text="Screenshot of AMS page inside a VIS resource in the Azure portal, showing the option to create or register a new instance.":::

1. After you create or register your AMS instance, you are redirected to the AMS instance.

### Create new AMS resource

To configure a new AMS resource:

1. On the **Create new AMS resource** page, select the **Basics** tab.

    :::image type="content" source="media/monitor-portal/ams-creation.png" lightbox="media/monitor-portal/ams-creation.png" alt-text="Screenshot of AMS creation page, showing the Basics tab and required fields.":::

1. Under **Project details**, configure your resource.

    1. For **Subscription**, select your Azure subscription.

    1. For **AMS resource group**, select the same resource group as the VIS.

    > [!IMPORTANT]
    > If you select a resource group that's different from the resource group of the VIS, the deployment fails.

1. Under **AMS instance details**, configure your AMS instance.

    1. For **Resource name**, enter a name for your AMS resource.

    1. For **Workload region**, select an Azure region for your workload.

1. Under **Networking**, configure networking information.

    1. For **Virtual network**, select a virtual network to use.

    1. For **Subnet**, select a subnet in your virtual network.

    1. For **Route All**, choose to enable or disable the option. When you enable this setting, all outbound traffic from the app is affected by your networking configuration.

1. Select the **Review + Create** tab.

### Register existing AMS resource

To register an existing **AMS resource**, select the instance from the drop-down menu on the **Register AMS** page.

> [!NOTE]
> You can only view and select the current version of AMS resources. AMS (classic) resources aren't available.

   :::image type="content" source="media/monitor-portal/ams-registration.png" lightbox="media/monitor-portal/ams-registration.png" alt-text="Screenshot of AMS registration page, showing the selection of an existing AMS resource.":::

## Unregister AMS from VIS

> [!NOTE]
> This operation only unregisters the AMS resource from the VIS. To delete the AMS resource, you need to delete the AMS instance.

To remove the link between your AMS resource and your VIS:

1. Sign in to the [Azure portal](https://portal.azure.com).

1. In the sidebar menu, under **Monitoring**, select **Azure Monitor for SAP**.

1. On the AMS page, select **Delete** to unregister the resource.

1. Wait for the confirmation message, **Azure Monitor for SAP solutions has been unregistered successfully**.

## Next steps

- [Get quality checks and insights for your VIS](get-quality-checks-insights.md)
