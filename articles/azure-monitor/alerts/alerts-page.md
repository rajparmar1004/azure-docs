---
title: View and manage your alert instances
description: The alerts page summarizes all alert instances in all your Azure resources generated in the last 30 days.
ms.topic: conceptual
ms.date: 2/23/2022
ms.reviewer: harelbr
---
# View and manage your alert instances

The alerts page summarizes all alert instances in all your Azure resources generated in the last 30 days. You can see all your different types of alerts from multiple subscriptions in a single pane, and you can find specific alert instances for troubleshooting purposes. 

You can get to the alerts page in any of the following ways:

- From the home page in the [Azure portal](https://portal.azure.com/), select **Monitor** > **Alerts**.  

  :::image type="content" source="media/alerts-managing-alert-instances/alerts-monitor-menu.png" alt-text="Screenshot of alerts link on monitor menu. ":::
  
- From a specific resource, go to the **Monitoring** section, and choose **Alerts**. The landing page is pre-filtered for alerts on that specific resource.

  :::image type="content" source="media/alerts-managing-alert-instances/alerts-resource-menu.png" alt-text="Screenshot of alerts link on a resource's menu.":::
## Alert rule recommendations (preview)

> [!NOTE]
> The alert rule recommendations feature is currently in preview and is only enabled for VMs.

If you don't have alert rules defined for the selected resource, either individually or as part of a resource group or subscription, you can [create a new alert rule](alerts-log.md#create-a-new-log-alert-rule-in-the-azure-portal), or enable recommended out-of-the-box alert rules in the Azure portal.

:::image type="content" source="media/alerts-managing-alert-instances/enable-recommended-alert-rules.jpg" alt-text="Screenshot of alerts page with link to recommended alert rules.":::

The system compiles a list of recommended alert rules based on:
- The resource provider’s knowledge of important signals and thresholds for monitoring the resource.
- Telemetry that tells us what customers commonly alert on for this resource.

To enable recommended alert rules:
1. On the **Alerts** page, select **Enable recommended alert rules**. The **Enable recommended alert rules** pane opens with a list of recommended alert rules based on your type of resource.  
1. In the **Alert me if** section, select all of the rules you want to enable. The rules are populated with the default values for the rule condition, such as the percentage of CPU usage that you want to trigger an alert. You can change the default values if you would like.
1. In the **Notify me by** section, select the way you want to be notified if an alert is fired.
1. Select **Enable**.

:::image type="content" source="media/alerts-managing-alert-instances/enable-recommended-rule-pane.jpg" alt-text="Screenshot of recommended alert rules pane."::: 

## The alerts summary pane

If you have alerts configured for this resource, the alerts summary pane summarizes the alerts fired in the last 24 hours. You can modify the list of alert instances by selecting filters such as **time range**, **subscription**, **alert condition**, **severity**, and more. Select an alert instance.

To see more details about a specific alert instance, select the alerts instance to open the **Alert Details** page. 
> [!NOTE]
> If you navigated to the alerts page by selecting a specific alert severity, the list is pre-filtered for that severity.   

:::image type="content" source="media/alerts-managing-alert-instances/alerts-page.png" alt-text="Screenshot of alerts page.":::
 
## The alerts details page

 The **Alerts details** page provides details about the selected alert. Select **Change user response** to change the user response to the alert. You can see all closed alerts in the **History** tab.  

:::image type="content" source="media/alerts-managing-alert-instances/alerts-details-page.png" alt-text="Screenshot of alerts details page.":::

## Next steps

- [Learn about Azure Monitor alerts](./alerts-overview.md)
- [Create a new alert rule](alerts-log.md)