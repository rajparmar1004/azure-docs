---
title: Use Azure Firewall to protect Azure Virtual Desktop
description: Learn how to use Azure Firewall to protect Azure Virtual Desktop deployments
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 07/14/2022
ms.author: victorh
---

# Use Azure Firewall to protect Azure Virtual Desktop deployments

Azure Virtual Desktop is a desktop and app virtualization service that runs on Azure. When an end user connects to an Azure Virtual Desktop environment, their session is run by a host pool. A host pool is a collection of Azure virtual machines that register to Azure Virtual Desktop as session hosts. These virtual machines run in your virtual network and are subject to the virtual network security controls. They need outbound Internet access to the Azure Virtual Desktop service to operate properly and might also need outbound Internet access for end users. Azure Firewall can help you lock down your environment and filter outbound traffic.

[ ![Azure Virtual Desktop architecture](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

Follow the guidelines in this article to provide additional protection for your Azure Virtual Desktop host pool using Azure Firewall.

## Prerequisites

 - A deployed Azure Virtual Desktop environment and host pool.
 - An Azure Firewall deployed with at least one Firewall Manager Policy

   For more information, see [Tutorial: Create a host pool by using the Azure portal](../virtual-desktop/create-host-pools-azure-marketplace.md)

To learn more about Azure Virtual Desktop environments see [Azure Virtual Desktop environment](../virtual-desktop/environment-setup.md).

## Host pool outbound access to Azure Virtual Desktop

The Azure virtual machines you create for Azure Virtual Desktop must have access to several Fully Qualified Domain Names (FQDNs) to function properly. Azure Firewall provides an Azure Virtual Desktop FQDN Tag to simplify this configuration. Use the following steps to allow outbound Azure Virtual Desktop platform traffic:

You will need to create an Azure Firewall Policy and create Rule Collections for Network Rules and Applications Rules. Give the Rule Collection a priority and an allow or deny action.

### Create network rules

| Name      | Source type | Source                    | Protocol | Destination ports | Destination type | Destination                       |
| --------- | ----------- | ------------------------- | -------- | ----------------- | ---------------- | --------------------------------- |
| Rule Name | IP Address  | VNet or Subnet IP Address | TCP      | 80                | IP Address       | 169.254.169.254, 168.63.129.16    |
| Rule Name | IP Address  | VNet or Subnet IP Address | TCP      | 443               | Service Tag      | AzureCloud, WindowsVirtualDesktop, AzureFrontDoor.Frontend |
| Rule Name | IP Address  | VNet or Subnet IP Address | TCP, UDP | 53                | IP Address       | *                                 |
|Rule name  | IP Address  | VNet or Subnet IP Address | TCP      | 1688              | IP address       | 20.118.99.244, 40.83.235.53 (azkms.core.windows.net)|
|Rule name  | IP Address  | VNet or Subnet IP Address | TCP      | 1688              | IP address       | 23.102.135.246 (kms.core.windows.net)|

> [!NOTE]
> Some deployments might not need DNS rules. For example, Azure Active Directory Domain controllers forward DNS queries to Azure DNS at 168.63.129.16.

### Create application rules

| Name      | Source type | Source                    | Protocol   | Destination type | Destination                                                                                 |
| --------- | ----------- | ------------------------- | ---------- | ---------------- | ------------------------------------------------------------------------------------------- |
| Rule Name | IP Address  | VNet or Subnet IP Address | Https:443  | FQDN Tag         | WindowsVirtualDesktop, WindowsUpdate, Windows Diagnostics, MicrosoftActiveProtectionService |

> [!IMPORTANT]
> We recommend that you don't use TLS inspection with Azure Virtual Desktop. For more information, see the [proxy server guidelines](../virtual-desktop/proxy-server-support.md#dont-use-ssl-termination-on-the-proxy-server).

## Host pool outbound access to the Internet

Depending on your organization needs, you might want to enable secure outbound internet access for your end users. If the list of allowed destinations is well-defined (for example, for [Microsoft 365 access](/microsoft-365/enterprise/microsoft-365-ip-web-service)), you can use Azure Firewall application and network rules to configure the required access. This routes end-user traffic directly to the internet for best performance. If you need to allow network connectivity for Windows 365 or Intune, see [Network requirments for Windows 365](/windows-365/requirements-network#allow-network-connectivity) and [Network endpoints for Intune](/mem/intune/fundamentals/intune-endpoints).

If you want to filter outbound user internet traffic by using an existing on-premises secure web gateway, you can configure web browsers or other applications running on the Azure Virtual Desktop host pool with an explicit proxy configuration. For example, see [How to use Microsoft Edge command-line options to configure proxy settings](/deployedge/edge-learnmore-cmdline-options-proxy-settings). These proxy settings only influence your end-user internet access, allowing the Azure Virtual Desktop platform outbound traffic directly via Azure Firewall.

## Control user access to the web

Admins can allow or deny user access to different website categories. Add a rule to your Application Collection from your specific IP address to web categories you want to allow or deny. Review all the [web categories](web-categories.md).

## Additional considerations

You might need to configure additional firewall rules, depending on your requirements:

- NTP server access

  By default, virtual machines running Windows connect to `time.windows.com` over UDP port 123 for time synchronization. Create a network rule to allow this access, or for a time server that you use in your environment.

## Next steps

- Learn more about Azure Virtual Desktop: [What is Azure Virtual Desktop?](../virtual-desktop/overview.md)
