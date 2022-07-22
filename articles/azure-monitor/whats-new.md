---
title: "What's new in Azure Monitor documentation"
description: "What's new in Azure Monitor documentation"
ms.topic: conceptual
ms.date: 04/04/2022
ms.reviewer: bwren
---

# What's new in Azure Monitor documentation

This article lists significant changes to Azure Monitor documentation.

## June, 2022

### General

| Article | Description |
|:---|:---|
| [Tutorial - Editing Data Collection Rules](essentials/data-collection-rule-edit.md) | New article|


### Application Insights

| Article | Description |
|:---|:---|
| [Application Insights logging with .NET](app/ilogger.md) | Connection string sample code has been added.|
| [Application Insights SDK support guidance](app/sdk-support-guidance.md) | Updated SDK supportability guidance. |
| [Azure AD authentication for Application Insights](app/azure-ad-authentication.md) | Azure AD authenticated telemetry ingestion has been reached general availability.|
| [Azure Application Insights for JavaScript web apps](app/javascript.md) | Our Java on-premises page has been retired and redirected to [Azure Monitor OpenTelemetry-based auto-instrumentation for Java applications](app/java-in-process-agent.md).|
| [Azure Application Insights Telemetry Data Model - Telemetry Context](app/data-model-context.md) | Clarified that Anonymous User ID is simply User.Id for easy selection in Intellisense.|
| [Continuous export of telemetry from Application Insights](app/export-telemetry.md) | On February 29, 2024, continuous export will be deprecated as part of the classic Application Insights deprecation.|
| [Dependency Tracking in Azure Application Insights](app/asp-net-dependencies.md) | The EventHub Client SDK and ServiceBus Client SDK information has been updated.|
| [Monitor Azure app services performance .NET Core](app/azure-web-apps-net-core.md) | Updated Linux troubleshooting guidance. |
| [Performance counters in Application Insights](app/performance-counters.md) | A prerequisite section has been added to ensure performance counter data is accessible.|

### Agents

| Article | Description |
|:---|:---|
| [Collect text and IIS logs with Azure Monitor agent (preview)](agents/data-collection-text-log.md) | Added troubleshooting section.|
| [Tools for migrating to Azure Monitor Agent from legacy agents](agents/azure-monitor-agent-migration-tools.md) | New article that explains how to install and use tools for migrating from legacy agents to the new Azure Monitor agent (AMA).|

### Visualizations
Azure Monitor Workbooks documentation previously resided on an external GitHub repository. We have migrated all Azure Workbooks content to the same repo as all other Azure Monitor content. 



## May, 2022

### General

| Article | Description |
|:---|:---|
| [Azure Monitor cost and usage](usage-estimated-costs.md) | Added standard web tests to table<br>Added explanation of billable GB calculation |
| [Azure Monitor overview](overview.md) | Updated overview diagram |

### Agents

| Article | Description |
|:---|:---|
| [Azure Monitor agent extension versions](agents/azure-monitor-agent-extension-versions.md) | Update to latest extension version |
| [Azure Monitor agent overview](agents/azure-monitor-agent-overview.md) | Added supported resource types |
| [Collect text and IIS logs with Azure Monitor agent (preview)](agents/data-collection-text-log.md) | Corrected error in data collection rule |
| [Overview of the Azure monitoring agents](agents/agents-overview.md) | Added new OS supported for agent |
| [Resource Manager template samples for agents](agents/resource-manager-agent.md) | Added Bicep examples |
| [Resource Manager template samples for data collection rules](agents/resource-manager-data-collection-rules.md) | Fixed bug in sample parameter file |
| [Rsyslog data not uploaded due to Full Disk space issue on AMA Linux Agent](agents/azure-monitor-agent-troubleshoot-linux-vm-rsyslog.md) | New article |
| [Troubleshoot the Azure Monitor agent on Linux virtual machines and scale sets](agents/azure-monitor-agent-troubleshoot-linux-vm.md) | New article |
| [Troubleshoot the Azure Monitor agent on Windows Arc-enabled server](agents/azure-monitor-agent-troubleshoot-windows-arc.md) | New article |
| [Troubleshoot the Azure Monitor agent on Windows virtual machines and scale sets](agents/azure-monitor-agent-troubleshoot-windows-vm.md) | New article |

### Alerts

| Article | Description |
|:---|:---|
| [IT Service Management Connector | Secure Webhook in Azure Monitor | Azure Configurations](alerts/itsm-connector-secure-webhook-connections-azure-configuration.md) | Added the workflow for ITSM management and removed all references to SCSM. |
| [Overview of Azure Monitor Alerts](alerts/alerts-overview.md) | Complete rewrite |
| [Resource Manager template samples for log query alerts](alerts/resource-manager-alerts-log.md) | Bicep samples for alerting have been added to the Resource Manager template samples articles. |
| [Supported resources for metric alerts in Azure Monitor](alerts/alerts-metric-near-real-time.md) | Added a newly supported resource type. |

### Application Insights

| Article | Description |
|:---|:---|
| [Application Map in Azure Application Insights](app/app-map.md) | Application Maps Intelligent View feature |
| [Azure Application Insights for ASP.NET Core applications](app/asp-net-core.md) | telemetry.Flush() guidance is now available. |
| [Diagnose with Live Metrics Stream](app/live-stream.md) | Updated information on using unsecure control channel. |
| [Migrate an Azure Monitor Application Insights classic resource to a workspace-based resource](app/convert-classic-resource.md) | Schema change documentation is now available here. |
| [Profile production apps in Azure with Application Insights Profiler](profiler/profiler-overview.md) | Profiler documentation now has a new home in the table of contents. |

All references to unsupported versions of .NET and .NET CORE have been scrubbed from Application Insights product documentation. See [.NET and >NET Core Support Policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) 
### Change Analysis

| Article | Description |
|:---|:---|
| [Navigate to a change using custom filters in Change Analysis](change/change-analysis-custom-filters.md) | New article |
| [Pin and share a Change Analysis query to the Azure dashboard](change/change-analysis-query.md) | New article |
| [Use Change Analysis in Azure Monitor to find web-app issues](change/change-analysis.md) | Added details  enabling for web app in-guest changes |
### Containers

| Article | Description |
|:---|:---|
| [Configure ContainerLogv2 schema (preview) for Container Insights](containers/container-insights-logging-v2.md) | New article describing new schema for container logs |
| [Enable Container insights](containers/container-insights-onboard.md) | General rewrite to improve clarity |
| [Resource Manager template samples for Container insights](containers/resource-manager-container-insights.md) | Added Bicep examples |
### Insights

| Article | Description |
|:---|:---|
| [Troubleshoot SQL Insights (preview)](insights/sql-insights-troubleshoot.md) | Added known issue for OS computer name. |
### Logs

| Article | Description |
|:---|:---|
| [Azure Monitor customer-managed key](logs/customer-managed-keys.md) | Update limitations and constraint. |
| [Design a Log Analytics workspace architecture](logs/workspace-design.md) | Complete rewrite to better describe decision criteria and include Sentinel considerations |
| [Manage access to Log Analytics workspaces](logs/manage-access.md) | Consolidated and rewrote all content on configuring workspace access |
| [Restore logs in Azure Monitor (Preview)](logs/restore.md) | Documented new Log Analytics table management configuration UI, which lets you configure a table's log plan and archive and retention policies. |

### Virtual Machines

| Article | Description |
|:---|:---|
| [Migrate from VM insights guest health (preview) to Azure Monitor log alerts](vm/vminsights-health-migrate.md) | New article describing process to replace VM guest health with alert rules |
| [VM insights guest health (preview)](vm/vminsights-health-overview.md) | Added deprecation statement |