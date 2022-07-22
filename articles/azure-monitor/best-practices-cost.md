---
title: Azure Monitor best practices - Cost management
description: Guidance and recommendations for reducing your cost for Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/31/2022
ms.reviewer: bwren

---

# Azure Monitor best practices - Cost management
This article provides guidance on reducing your cloud monitoring costs by implementing and managing Azure Monitor in the most cost effective manner. This includes leveraging cost saving features and ensuring that you're not paying for data collection that provides little value. It also provides guidance for regularly monitoring your usage so that you can proactively detect and identify sources responsible for excessive usage.


## Understand Azure Monitor charges
You should start by understanding the different ways that Azure Monitor charges and how to view your monthly bill. See [Azure Monitor cost and usage](usage-estimated-costs.md) for a complete description and the different tools available to analyze your charges.

## Configure workspaces
You can start using Azure Monitor with a single Log Analytics workspace using default options. As your monitoring environment grows though, you will need to make decisions about whether to have multiple services share a single workspace or create multiple workspaces, and you want to evaluate configuration options that allow you to reduce your monitoring costs.

### Configure pricing tier or dedicated cluster
By default, workspaces will use Pay-As-You-Go pricing with no minimum data volume. If you collect a sufficient amount of data though, you can significantly decrease your cost by using a [commitment tier](logs/cost-logs.md#commitment-tiers). You commit to a daily minimum of data collected in exchange for a lower rate.

[Dedicated clusters](logs/logs-dedicated-clusters.md) provide additional functionality and cost savings if you ingest at least 500 GB per day collectively among multiple workspaces in the same region. Unlike commitment tiers, workspaces in a dedicated cluster don't need to individually reach the 500 GB.

See [Azure Monitor Logs pricing details](logs/cost-logs.md) for details on commitment tiers and guidance on determining which is most appropriate for your level of usage. See [Usage and estimated costs](usage-estimated-costs.md#usage-and-estimated-costs) to view estimated costs for your usage at different pricing tiers.

### Optimize workspace configuration
As your monitoring environment becomes more complex, you will need to consider whether to create additional Log Analytics workspaces. This may be as you place resources in additional regions or as you implement additional services that use workspaces such as Azure Sentinel and Microsoft Defender for Cloud.

There can be cost implications with your workspace design, most notably when you combine different services such as operational data from Azure Monitor and security data from Microsoft Sentinel. See [Workspaces with Microsoft Sentinel](logs/cost-logs.md#workspaces-with-microsoft-sentinel) and [Workspaces with Microsoft Defender for Cloud](logs/cost-logs.md#workspaces-with-microsoft-defender-for-cloud) for a description of these implications and guidance on determining the most cost-effective solution for your environment.

## Configure tables in each workspace
Except for  [tables that don't incur charges](logs/cost-logs.md#data-size-calculation), all data in a Log Analytics workspace is billed at the same rate by default. You may be collecting data though that you query infrequently or that you need to archive for compliance but rarely access. You can significantly reduce your costs by configuring Basic Logs and by optimizing your data retention and archiving.

### Configure data retention and archiving
Data collected in a Log Analytics workspace is retained for 31 days at no charge (90 days if Azure Sentinel is enabled on the workspace). You can retain data beyond the default for trending analysis or other reporting, but there is a charge for this retention.

Your retention requirement may just be for compliance reasons or for occasional investigation or analysis of historical data. In this case, you should configure [Archived Logs](logs/data-retention-archive.md) which allows you to retain data long term (up to 7 years) at a significantly reduced cost. There is a cost to search archived data or temporarily restore it for analysis. If you require infrequent access to this data though, this cost will be more than offset by the reduced retention cost.

You can configure retention and archiving for all tables in a workspace or configure each table separately. This allows you to optimize your costs by setting only the retention you require for each data type. 

### Configure Basic Logs (preview)
You can save on data ingestion costs by configuring [certain tables](logs/basic-logs-configure.md#which-tables-support-basic-logs) in your Log Analytics workspace that you primarily use for debugging, troubleshooting and auditing as [Basic Logs](logs/basic-logs-configure.md). Tables configured for Basic Logs have a lower ingestion cost in exchange for reduced features. They can't be used for alerting, their retention is set to eight days, they support a limited version of the query language, and there is a cost for querying them. If you query these tables infrequently though, this query cost can be more than offset by the reduced ingestion cost.

The decision whether to configure a table for Basic Logs is based on the following criteria:

- The table currently supports Basic Logs.
- You don't require more than eight days of data retention for the table.
- You only require basic queries of the data using a limited version of the query language.
- The cost savings for data ingestion over a month exceed the expected cost for any expected queries

See [Query Basic Logs in Azure Monitor (Preview)](.//logs/basic-logs-query.md) for details on query limitations and [Configure Basic Logs in Azure Monitor (Preview)](logs/basic-logs-configure.md) for more details about them.

## Reduce the amount of data collected
The most straightforward strategy to reduce your costs for data ingestion and retention is to reduce the amount of data that you collect. Your goal should be to collect the minimal amount of data to meet your monitoring requirements. If you find that you're collecting data that's not being used for alerting or analysis, then you have an opportunity to reduce your monitoring costs by modifying your configuration to stop collecting data that you don't need.

The configuration change will vary depending on the data source. The following sections provide guidance for configuring common data sources to reduce the data they send to the workspace.

## Virtual machines
Virtual machines can vary significantly in the amount of data they collect, depending on the amount of telemetry generated by the applications and services they have installed. The following table lists the most common data collected from virtual machines and strategies for limiting them for each of the Azure Monitor agents.


| Source | Strategy | Log Analytics agent | Azure Monitor agent |
|:---|:---|:---|:---|
| Event logs | Collect only required event logs and levels. For example, *Information* level events are rarely used and should typically not be collected. For Azure Monitor agent, filter particular event IDs that are frequent but not valuable. | Change the [event log configuration for the workspace](agents/data-sources-windows-events.md) | Change the [data collection rule](agents/data-collection-rule-azure-monitor-agent.md).  Use [custom XPath queries](agents/data-collection-rule-azure-monitor-agent.md#filter-events-using-xpath-queries) to filter specific event IDs. |
| Syslog | Reduce the number of facilities collected and only collect required event levels. For example, *Info* and *Debug* level events are rarely used and should typically not be collected. | Change the [syslog configuration for the workspace](agents/data-sources-syslog.md). |  Change the [data collection rule](agents/data-collection-rule-azure-monitor-agent.md).  Use [custom XPath queries](agents/data-collection-rule-azure-monitor-agent.md#filter-events-using-xpath-queries) to filter specific events. |
| Performance counters | Collect only the performance counters required and reduce the frequency of collection. For Azure Monitor agent, consider sending performance data only to Metrics and not Logs. | Change the [performance counter configuration for the workspace](agents/data-sources-performance-counters.md). | Change the [data collection rule](agents/data-collection-rule-azure-monitor-agent.md).  Use [custom XPath queries](agents/data-collection-rule-azure-monitor-agent.md#filter-events-using-xpath-queries) to filter specific counters. |


### Use transformations to filter events
The bulk of data collection from virtual machines will be from Windows or Syslog events. While you can provide more filtering with the Azure Monitor agent, you still may be collecting records that provide little value. Use [transformations](essentials//data-collection-transformations.md) to implement more granular filtering and also to filter data from columns that provide little value. For example, you might have a Windows event that's valuable for alerting, but it includes columns with redundant or excessive data. You can create a transformation that allows the event to be collected but removes this excessive data.

See the section below on filtering data with transformations for a summary on where to implement filtering and transformations for different data sources.

### Multi-homing agents
You should be cautious with any configuration using multi-homed agents where a single virtual machine sends data to multiple workspaces since you may be incurring charges for the same data multiple times. If you do multi-home agents, ensure that you're sending unique data to each workspace.

You can also collect duplicate data with a single virtual machine running both the Azure Monitor agent and Log Analytics agent, even if they're both sending data to the same workspace. While the agents can coexist, each works independently without any knowledge of the other. You should continue to use the Log Analytics agent until you [migrate to the Azure Monitor agent](./agents/azure-monitor-agent-migration.md) rather than using both together unless you can ensure that each is collecting unique data.

See [Analyze usage in Log Analytics workspace](logs/analyze-usage.md) for guidance on analyzing your collected data to ensure that you aren't collecting duplicate data for the same machine.

## Application Insights
There are multiple methods that you can use to limit the amount of data collected by Application Insights.

* **Sampling**: [Sampling](app/sampling.md) is the primary tool you can use to tune the amount of data collected by Application Insights. Use sampling to reduce the amount of telemetry that's sent from your applications with minimal distortion of metrics.

* **Limit Ajax calls**: [Limit the number of Ajax calls](app/javascript.md#configuration) that can be reported in every page view or disable Ajax reporting. Note that disabling Ajax calls will disable [JavaScript correlation](app/javascript.md#enable-distributed-tracing).

* **Disable unneeded modules**: [Edit ApplicationInsights.config](app/configuration-with-applicationinsights-config.md) to turn off collection modules that you don't need. For example, you might decide that performance counters or dependency data aren't required.

* **Pre-aggregate metrics**: If you put calls to TrackMetric in your application, you can reduce traffic by using the overload that accepts your calculation of the average and standard deviation of a batch of measurements. Alternatively, you can use a [pre-aggregating package](https://www.myget.org/gallery/applicationinsights-sdk-labs).

* **Limit the use of custom metrics**: The Application Insights option to [Enable alerting on custom metric dimensions](app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) can increase costs because this can result in the creation of more pre-aggregation metrics.

* **Ensure use of updated SDKs**: Earlier version of the ASP.NET Core SDK and Worker Service SDK [collect a large number of counters by default](app/eventcounters.md#default-counters-collected) which collected as custom metrics. Use later versions to specify [only required counters](app/eventcounters.md#customizing-counters-to-be-collected).
## Resource logs
The data volume for [resource logs](essentials/resource-logs.md) varies significantly between services, so you should only collect the categories that are required. You may also not want to collect platform metrics from Azure resources since this data is already being collected in Metrics. Only configured your diagnostic data to collect metrics if you need metric data in the workspace for more complex analysis with log queries.

Diagnostic settings do not allow granular filtering of resource logs. You may require certain logs in a particular category but not others. In this case, use [transformations](essentials/data-collection-transformations.md) on the workspace to filter logs that you don't require. You can also filter out the value of certain columns that you don't require to save additional cost. 

## Other insights and services
See the documentation for other services that store their data in a Log Analytics workspace for recommendations on optimizing their data usage. Following 

- **Container insights** - [Understand monitoring costs for Container insights](containers/container-insights-cost.md#controlling-ingestion-to-reduce-cost)
- **Microsoft Sentinel** - [Reduce costs for Microsoft Sentinel](../sentinel/billing-reduce-costs.md)
- **Defender for Cloud** - [Setting the security event option at the workspace level](../defender-for-cloud/enable-data-collection.md#setting-the-security-event-option-at-the-workspace-level)



## Filter data with transformations (preview)
[Data collection rule transformations in Azure Monitor](essentials//data-collection-transformations.md) allow you to filter incoming data to reduce costs for data ingestion and retention. In addition to filtering records from the incoming data, you can filter out columns in the data, reducing its billable size as described in [Data size calculation](logs/cost-logs.md#data-size-calculation).

Use ingestion-time transformations on the workspace to further filter data for workflows where you don't have granular control. For example, you can select categories in a [diagnostic setting](essentials/diagnostic-settings.md) to collect resource logs for a particular service, but that category might send a variety of records that you don't need. Create a transformation for the table that service uses to filter out records you don't want.

You can also ingestion-time transformations to lower the storage requirements for records you want by removing columns without useful information. For example, you might have error events in a resource log that you want for alerting, but you don't require certain columns in those records that contain a large amount of data. Create a transformation for that table that removes those columns.

The following table for methods to apply transformations to different workflows.

> [!NOTE]
> Azure tables here refers to tables that are created and maintained by Microsoft and documented in the [Azure Monitor Reference](/azure/azure-monitor/reference/). Custom tables are created by custom applications and have a suffix of *_CL* ion their name.

| Source | Target | Description | Filtering method |
|:---|:---|:---|:---|
| Azure Monitor agent | Azure tables | Collect data from standard sources such as Windows events, syslog, and performance data and send to Azure tables in Log Analytics workspace. | Use XPath in DCR to collect specific data from client machine. Ingestion-time transformations in agent DCR are not yet supported. |
| Azure Monitor agent | Custom tables | Collecting data outside of standard data sources is not yet supported. | |
| Log Analytics agent | Azure tables | Collect data from standard sources such as Windows events, syslog, and performance data and send to Azure tables in Log Analytics workspace. | Configure data collection on the workspace. Optionally, create ingestion-time transformation in the workspace DCR to filter records and columns. |
| Log Analytics agent | Custom tables | Configure [custom logs](agents/data-sources-custom-logs.md) on the workspace to collect file based text logs. | Configure ingestion-time transformation in the workspace DCR to filter or transform incoming data. You must first migrate the custom table to the new logs ingestion API. |
| Data Collector API | Custom tables | Use [Data Collector API](logs/data-collector-api.md) to send data to custom tables in the workspace using REST API. | Configure ingestion-time transformation in the workspace DCR to filter or transform incoming data. You must first migrate the custom table to the new logs ingestion API. |
| Logs ingestion API | Custom tables<br>Azure tables | Use [Logs ingestion API](logs/logs-ingestion-api-overview.md) to send data to the workspace using REST API. | Configure ingestion-time transformation in the DCR for the custom log. |
| Other data sources | Azure tables | Includes resource logs from diagnostic settings and other Azure Monitor features such as Application insights, Container insights and VM insights. | Configure ingestion-time transformation in the workspace DCR to filter or transform incoming data. |


## Monitor workspace and analyze usage
Once you've configured your environment and data collection for cost optimization, you need to continue to monitor it to ensure that you don't experience unexpected increases in billable usage. You should also analyze your usage regularly to determine if you have additional opportunities to reduce your usage, such as further filtering out collected data that has not proven to be useful.


### Set a daily cap
A [daily cap](logs/daily-cap.md) disables data collection in a Log Analytics workspace for the rest of the day once your configured limit is reached. This should not be used as a method to reduce costs, but rather as a preventative measure to ensure that you don't exceed a particular budget. Daily caps are typically used by organizations that are particularly cost conscious. 

When data collection stops, you effectively have no monitoring of features and resources relying on that workspace. Rather than just relying on the daily cap alone, you can configure an alert rule to notify you when data collection reaches some level before the daily cap. This allows you to address any increases before data collection shuts down, or even to temporarily disable collection for less critical resources.

See [Set daily cap on Log Analytics workspace](logs/daily-cap.md) for details on how the daily cap works and how to configure one.
### Send alert when data collection is high
In order to avoid unexpected bills, you should be proactively notified anytime you experience excessive usage. This allows you to address any potential anomalies before the end of your billing period.

The following example is a [log alert rule](alerts/alerts-unified-log.md) that sends an alert if the billable data volume ingested in the last 24 hours was greater than 50 GB. Modify the **Alert Logic** to use a different threshold based on expected usage in your environment. You can also increase the frequency to check usage multiple times every day, but this will result in a higher charge for the alert rule.

| Setting | Value |
|:---|:---|
| **Scope** | |
| Target scope | Select your Log Analytics workspace. |
| **Condition** | |
| Query | `Usage \| where IsBillable \| summarize DataGB = sum(Quantity / 1000.)` |
| Measurement | Measure: *DataGB*<br>Aggregation type: Total<br>Aggregation granularity: 1 day |
| Alert Logic | Operator: Greater than<br>Threshold value: 50<br>Frequency of evaluation: 1 day |
| Actions | Select or add an [action group](alerts/action-groups.md) to notify you when the threshold is exceeded. |
| **Details** | |
| Severity| Warning |
| Alert rule name | Billable data volume greater than 50 GB in 24 hours |

See [Analyze usage in Log Analytics workspace](logs/analyze-usage.md) for details on using log queries like the one used here to analyze billable usage in your workspace.

## Analyze your collected data
When you detect an increase in data collection, then you need methods to analyze your collected data to identify the source of the increase. You should also periodically analyze data collection to determine if there's additional configuration that can decrease your usage further. This is particularly important when you add a new set of data sources, such as a new set of virtual machines or onboard a new service.

See [Analyze usage in Log Analytics workspace](logs/analyze-usage.md) for different methods to analyze your collected data and billable usage. This article includes a variety of log queries that will help you identify the source of any data increases and to understand your basic usage patterns.

## Next steps

- See [Azure Monitor cost and usage](usage-estimated-costs.md)) for a description of Azure Monitor and how to view and analyze your monthly bill.
- See [Azure Monitor Logs pricing details](logs/cost-logs.md) for details on how charges are calculated for data in a Log Analytics workspace and different configuration options to reduce your charges.
- See [Analyze usage in Log Analytics workspace](logs/analyze-usage.md) for details on analyzing the data in your workspace to determine to source of any higher than expected usage and opportunities to reduce your amount of data collected.
- See [Set daily cap on Log Analytics workspace](logs/daily-cap.md) to control your costs by setting a daily limit on the amount of data that may be ingested in a workspace.
