---
title: Azure Monitor agent extension versions
description: This article describes the version details for the Azure Monitor agent virtual machine extension.
ms.topic: conceptual
author: shseth
ms.author: shseth
ms.date: 7/8/2022
ms.custom: references_region
ms.reviewer: shseth

---

# Azure Monitor agent extension versions
This article describes the version details for the Azure Monitor agent virtual machine extension. This extension deploys the agent on virtual machines, scale sets, and Arc-enabled servers (on premise servers with Azure Arc agent installed).  

We strongly recommended to update to the latest version at all times, or opt in to the [Automatic Extension Update](../../virtual-machines/automatic-extension-upgrade.md) feature.


## Version details
| Release Date | Release notes | Windows | Linux |  
|:---|:---|:---|:---| 
| May 2022 | <ul><li>Fixed issue where agent stops functioning due to faulty XPath query. With this version, only query related Windows events will fail, other data types will continue to be collected</li><li>Collection of Windows network troubleshooting logs added to 'CollectAMAlogs.ps1' tool</li><li>Linux support for Debian 11 distro</li><li>Fixed issue to list mount paths instead of device names for Linux disk metrics</li></ul> | 1.5.0.0 | 1.21.0 |
| April 2022 | <ul><li>Private IP information added in Log Analytics <i>Heartbeat</i> table for Windows and Linux</li><li>Fixed bugs in Windows IIS log collection (preview) <ul><li>Updated IIS site column name to match backend KQL transform</li><li>Added delay to IIS upload task to account for IIS buffering</li></ul></li><li>Fixed Linux CEF syslog forwarding for Sentinel</li><li>Removed 'error' message for Azure MSI token retrieval failure on Arc to show as 'Info' instead</li><li>Support added for Ubuntu 22.04, RHEL 8.5, 8.6, AlmaLinux and RockyLinux distros</li></ul> | 1.4.1.0<sup>Hotfix</sup> | 1.19.3 |
| March 2022 | <ul><li>Fixed timestamp and XML format bugs in Windows Event logs</li><li>Full Windows OS information in Log Analytics Heartbeat table</li><li>Fixed Linux performance counters to collect instance values instead of 'total' only</li></ul> | 1.3.0.0 | 1.17.5.0 |
| February 2022 | <ul><li>Bugfixes for the AMA Client installer (private preview)</li><li>Versioning fix to reflect appropriate Windows major/minor/hotfix versions</li><li>Internal test improvement on Linux</li></ul> | 1.2.0.0 | 1.15.3 | 
| January 2022 | <ul><li>Syslog RFC compliance for Linux</li><li>Fixed issue for Linux perf counters not flowing on restart</li><li>Fixed installation failure on Windows Server 2008 R2 SP1</li></ul> | 1.1.5.1<sup>Hotfix</sup> | 1.15.2.0<sup>Hotfix</sup> |  
| December 2021 | <ul><li>Fixed issues impacting Linux Arc-enabled servers</li><li>'Heartbeat' table > 'Category' column reports "Azure Monitor Agent" in Log Analytics for Windows</li></ul>  | 1.1.4.0 | 1.14.7.0<sup>2</sup> |  
| September 2021 | <ul><li>Fixed issue causing data loss on restarting the agent</li><li>Fixed issue for Arc Windows servers</li></ul> | 1.1.3.2<sup>Hotfix</sup> | 1.12.2.0 <sup>1</sup> |  
| August 2021 | Fixed issue allowing Azure Monitor Metrics as the only destination | 1.1.2.0 | 1.10.9.0<sup>Hotfix</sup> |  
| July 2021 | <ul><li>Support for direct proxies</li><li>Support for Log Analytics gateway</li></ul> [Learn more](https://azure.microsoft.com/updates/general-availability-azure-monitor-agent-and-data-collection-rules-now-support-direct-proxies-and-log-analytics-gateway/) | 1.1.1.0 | 1.10.5.0 | 
| June 2021 | General availability announced. <ul><li>All features except metrics destination now generally available</li><li>Production quality, security and compliance</li><li>Availability in all public regions</li><li>Performance and scale improvements for higher EPS</li></ul> [Learn more](https://azure.microsoft.com/updates/azure-monitor-agent-and-data-collection-rules-now-generally-available/) | 1.0.12.0 | 1.9.1.0 |  

<sup>Hotfix</sup> Do not use AMA Linux versions v1.10.7, v1.15.1 and AMA Windows v1.1.3.1, v1.1.5.0. Please use hotfix versions listed above.   
<sup>1</sup> Known issue: No data collected from Linux Arc-enabled servers  
<sup>2</sup> Known issue: Linux performance counters data stops flowing on restarting/rebooting the machine(s)

## Next steps

- [Install and manage the extension](azure-monitor-agent-manage.md).
- [Create a data collection rule](data-collection-rule-azure-monitor-agent.md) to collect data from the agent and send it to Azure Monitor.
