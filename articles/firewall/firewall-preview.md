---
title: Azure Firewall preview features
description: Learn about Azure Firewall preview features that are currently publicly available.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 07/15/2022
ms.author: victorh
---

# Azure Firewall preview features

The following Azure Firewall preview features are available publicly for you to deploy and test. Some of the preview features are available on the Azure portal, and some are only visible using a feature flag.

> [!IMPORTANT]
> These features are currently in PREVIEW.
> See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

## Feature flags

As new features are released to preview, some of them will be behind a feature flag. To enable the functionality in your environment, you must enable the feature flag on your subscription. These features are applied at the subscription level for all firewalls (VNet firewalls and SecureHub firewalls).  

This article will be updated to reflect the features that are currently in preview with instructions to enable them. When the features move to General Availability (GA), they'll be available to all customers without the need to enable a feature flag. 

## Preview features

The following features are available in preview.

### Network rule name logging (preview)

Currently, a network rule hit event shows the following attributes in the logs: 

   - Source and destination IP/port
   - Action (allow, or deny)

 With this new feature, the event logs for network rules also show the following attributes:
   - Policy name
   - Rule collection group
   - Rule collection
   - Rule name 

To enable the Network Rule name Logging feature, the following commands need to be run in Azure PowerShell. For the feature to immediately take effect, an operation needs to be run on the firewall. This can be a rule change (least intrusive), a setting change, or a stop/start operation. Otherwise, the firewall/s is updated with the feature within several days.

Run the following Azure PowerShell commands to configure Azure Firewall network rule name logging:

```azurepowershell
Connect-AzAccount 
Select-AzSubscription -Subscription "subscription_id or subscription_name" 
Register-AzProviderFeature -FeatureName AFWEnableNetworkRuleNameLogging -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network 
```

Run the following Azure PowerShell command to turn off this feature:

```azurepowershell
Unregister-AzProviderFeature -FeatureName AFWEnableNetworkRuleNameLogging -ProviderNamespace Microsoft.Network 
```

### IDPS Private IP ranges (preview)

In Azure Firewall Premium IDPS, private IP address ranges are used to identify if traffic is inbound, outbound, or internal (East-West). Each signature is applied on specific traffic direction, as indicated in the signature rules table. By default, only ranges defined by IANA RFC 1918 are considered private IP addresses. So traffic sent from a private IP address range to a private IP address range is considered internal. To modify your private IP addresses, you can now easily edit, remove, or add ranges as needed.

:::image type="content" source="media/firewall-preview/idps-private-ip.png" alt-text="Screenshot showing I D P S private IP address ranges.":::

### Structured firewall logs (preview)

Today, the following diagnostic log categories are available for Azure Firewall:
- Application rule log
- Network rule log
- DNS proxy log

These log categories use [Azure diagnostics mode](../azure-monitor/essentials/resource-logs.md#azure-diagnostics-mode). In this mode, all data from any diagnostic setting will be collected in the [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) table.

With this new feature, you'll be able to choose to use [Resource Specific Tables](../azure-monitor/essentials/resource-logs.md#resource-specific) instead of the existing [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) table. In case both sets of logs are required, at least two diagnostic settings need to be created per firewall.

In **Resource specific** mode, individual tables in the selected workspace are created for each category selected in the diagnostic setting. This method is recommended since it:
- makes it much easier to work with the data in log queries
- makes it easier to discover schemas and their structure
- improves performance across both ingestion latency and query times
- allows you to grant Azure RBAC rights on a specific table

New resource specific tables are now available in Diagnostic setting that allows you to utilize the following newly added categories:

- [Network rule log](/azure/azure-monitor/reference/tables/azfwnetworkrule) - Contains all Network Rule log data. Each match between data plane and network rule creates a log entry with the data plane packet and the matched rule's attributes.
- [NAT rule log](/azure/azure-monitor/reference/tables/azfwnatrule) - Contains all DNAT (Destination Network Address Translation) events log data. Each match between data plane and DNAT rule creates a log entry with the data plane packet and the matched rule's attributes.
- [Application rule log](/azure/azure-monitor/reference/tables/azfwapplicationrule) - Contains all Application rule log data. Each match between data plane and Application rule creates a log entry with the data plane packet and the matched rule's attributes.
- [Threat Intelligence log](/azure/azure-monitor/reference/tables/azfwthreatintel) - Contains all Threat Intelligence events.
- [IDPS log](/azure/azure-monitor/reference/tables/azfwidpssignature) - Contains all data plane packets that were matched with one or more IDPS signatures.
- [DNS proxy log](/azure/azure-monitor/reference/tables/azfwdnsquery) - Contains all DNS Proxy events log data.
- [Internal FQDN resolve failure log](/azure/azure-monitor/reference/tables/azfwinternalfqdnresolutionfailure) - Contains all internal Firewall FQDN resolution requests that resulted in failure.
- [Application rule aggregation log](/azure/azure-monitor/reference/tables/azfwapplicationruleaggregation) - Contains aggregated Application rule log data for Policy Analytics.
- [Network rule aggregation log](/azure/azure-monitor/reference/tables/azfwnetworkruleaggregation) - Contains aggregated Network rule log data for Policy Analytics.
- [NAT rule aggregation log](/azure/azure-monitor/reference/tables/azfwnatruleaggregation) - Contains aggregated NAT rule log data for Policy Analytics.

By default, the new resource specific tables are disabled. Open a support ticket to enable the functionality in your environment.

In addition, when setting up your log analytics workspace, you must select whether you want to work with the AzureDiagnostics table (default) or with Resource Specific Tables.

Additional KQL log queries were added to query structured firewall logs.

> [!NOTE]
> Existing Workbooks and any Sentinel integration will be adjusted to support the new structured logs when **Resource Specific** mode is selected.

### Policy Analytics (preview)

Policy Analytics provides insights, centralized visibility, and control to Azure Firewall. IT teams today are challenged to keep Firewall rules up to date, manage existing rules, and remove unused rules. Any accidental rule updates can lead to a significant downtime for IT teams. 

For large, geographically dispersed organizations, manually managing Firewall rules and policies is a complex and sometimes  error-prone process. The new Policy Analytics feature is the answer to this common challenge faced by IT teams.

You can now refine and update Firewall rules and policies with confidence in just a few steps in the Azure portal. You have granular control to define your own custom rules for an enhanced security and compliance posture. You can automate rule and policy management to reduce the risks associated with a manual process.

#### Pricing

Enabling Policy Analytics on a Firewall Policy associated with a single firewall is billed per policy as described on the [Azure Firewall Manager pricing](https://azure.microsoft.com/pricing/details/firewall-manager/) page. Enabling Policy Analytics on a Firewall Policy associated with more than one firewall is offered at no additional cost.

#### Key Policy Analytics features

- **Policy insight panel**: Aggregates insights and highlights relevant policy information.
- **Rule analytics**: Analyzes existing DNAT, Network, and Application rules to identify rules with low utilization or rules with low usage in a specific time window.
- **Traffic flow analysis**: Maps traffic flow to rules by identifying top traffic flows and enabling an integrated experience.
- **Single Rule analysis**: Analyzes a single rule to learn what traffic hits that rule to refine the access it provides and improve the overall security posture.

### Prerequisites

- An Azure Firewall Standard or Premium
- An Azure Firewall Standard or Premium policy attached to the Firewall
- The [network rule name logging preview feature](#network-rule-name-logging-preview) must be enabled to view network rules analysis
- The [structured firewall logs feature](#structured-firewall-logs-preview) must be enabled on Firewall Standard or Premium


### Enable Policy Analytics

#### Firewall with no Azure Diagnostics settings configured


1.	Once all prerequisites are met, select **Policy analytics (preview)** in the table of contents. 
2. Next, select **Configure Workspaces**.
3. In the pane that opens, select the **Enable Policy Analytics** checkbox. 
4. Next, choose a log analytics workspace. The log analytics workspace should be the same as the Firewall attached to the policy.
5. Select **Save** after you choose the log analytics workspace.
6. Go to the Firewall attached to the policy and enter the **Diagnostic settings** page. You'll see the **FirewallPolicySetting** added there as part of the policy analytics feature.
7. Select **Edit Setting**, and ensure the **Resource specific** toggle is checked, and the highlighted tables are checked. In the previous example, all logs are written to the log analytics workspace.

#### Firewall with Azure Diagnostics settings already configured

1. Ensure that the Firewall attached to the policy is connected to **Resource Specific** tables, and that the following three tables are enabled:
   - AZFWApplicationRuleAggregation
   - AZFWNetworkRuleAggregation
   - AZFWNatRuleAggregation
2. Next, select **Policy Analytics (preview)** in the table of contents. Once inside the feature, select **Configure Workspaces**. 
3. Now, select **Enable Policy Analytics**.
4. Next, choose a log analytics workspace. The log analytics workspace should be the same as the Firewall attached to the policy. 
5. Select **Save** after you choose the log analytics workspace.

   During the save process, you might see the following error message: **Failed to update Diagnostic Settings**

   You can disregard this error message if the policy was successfully updated.

## Next steps

To learn more about Azure Firewall, see [What is Azure Firewall?](overview.md).