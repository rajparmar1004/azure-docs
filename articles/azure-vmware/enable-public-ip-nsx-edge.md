---
title: Enable Public IP to the NSX Edge for Azure VMware Solution (Preview)
description: This article shows how to enable internet access for your Azure VMware Solution.
ms.topic: how-to
ms.service: azure-vmware
ms.date: 05/12/2022
---

# Enable Public IP to the NSX Edge for Azure VMware Solution (Preview)

In this article, you'll learn how to enable Public IP to the NSX Edge for your Azure VMware Solution. 

>[!TIP]
>Before you enable Internet access to your Azure VMware Solution, review the [Internet connectivity design considerations](concepts-design-public-internet-access.md).

Public IP to the NSX Edge is a feature in Azure VMware Solution that enables inbound and outbound internet access for your Azure VMware Solution environment. 

>[!IMPORTANT]
>To enable this feature for your subscription, register the ```PIPOnNSXEnabled``` flag and follow these steps to [set up the preview feature in your Azure subscription](../azure-resource-manager/management/preview-features.md?tabs=azure-portal).

The Public IP is configured in Azure VMware Solution through the Azure portal and the NSX-T Data center interface within your Azure VMware Solution private cloud.

With this capability, you have the following features:
- A cohesive and simplified experience for reserving and using a Public IP down to the NSX Edge.
- The ability to receive up to 1000 or more Public IPs, enabling Internet access at scale.
- Inbound and outbound internet access for your workload VMs.
- DDoS Security protection against network traffic in and out of the Internet. 
- HCX Migration support over the Public Internet.

>[!TIP]
>To enable this feature for your subscription, register the ```PIPOnNSXEnabled``` flag and follow these steps to [set up the preview feature in your Azure subscription](../azure-resource-manager/management/preview-features.md?tabs=azure-portal).

## Reference   architecture    
The architecture shows Internet access to and from your Azure VMware Solution private cloud using a Public IP directly to the NSX Edge.
:::image type="content" source="media/public-ip-nsx-edge/architecture-internet-access-avs-public-ip.png" alt-text="Diagram that shows architecture of Internet access to and from your Azure VMware Solution Private Cloud using a Public IP directly to the NSX Edge." border="false" lightbox="media/public-ip-nsx-edge/architecture-internet-access-avs-public-ip.png":::

## Configure a Public IP in the Azure portal
1. Log on to the Azure portal.
1. Search for and select Azure VMware Solution.
2.	Select the Azure VMware Solution private cloud.    
1. In the left navigation, under **Workload Networking**, select **Internet connectivity**.   
4.	Select the **Connect using Public IP down to the NSX-T Edge** button. 

>[!TIP]
>Before selecting a Public IP, ensure you understand the implications to your existing environment. For more information, see [Internet connectivity design considerations](concepts-design-public-internet-access.md).
    
5.	Select **Public IP**.
    :::image type="content" source="media/public-ip-nsx-edge/public-ip-internet-connectivity.png" alt-text="Diagram that shows how to select public IP to the NSX Edge":::
6.	Enter the **Public IP name** and select a subnet size from the **Address space** dropdown and select **Configure**.     
7.	This Public IP should be configured within 20 minutes and will show the subnet.
   :::image type="content" source="media/public-ip-nsx-edge/public-ip-subnet-internet-connectivity.png" alt-text="Diagram that shows Internet connectivity in Azure VMware Solution.":::
1. If you don't see the subnet, refresh the list. If the refresh fails, try the configuration again.
    
9.	After configuring the Public IP, select the **Connect using the Public IP down to the NSX-T Edge** checkbox to disable all other Internet options. 
10.	Select **Save**. 

You have successfully enabled Internet connectivity for your Azure VMware Solution private cloud and reserved a Microsoft allocated Public IP. You can now configure this Public IP down to the NSX Edge for your workloads. The NSX-T Datacenter is used for all VM communication. There are several options for configuring your reserved Public IP down to the NSX Edge. 

There are three options for configuring your reserved Public IP down to the NSX Edge: Outbound Internet Access for VMs, Inbound Internet Access for VMs, and Gateway Firewall used to Filter Traffic to VMs at T1 Gateways.

### Outbound Internet access for VMs
 
A Sourced Network Translation Service (SNAT) with Port Address Translation (PAT) is used to allow many VMs to one SNAT service. This connection means you can provide Internet connectivity for many VMs.

**Add rule**
1.	From your Azure VMware Solution private cloud, select **vCenter Credentials**
2.	Locate your NSX-T URL and credentials.
3.	Log in to **VMWare NSX-T**.   
4.	Navigate to **NAT Rules**.
5.	Select the T1 Router.
1. select **ADD NAT RULE**.

**Configure rule**
  
1. Enter a name.
1. Select **SNAT**. 
1. Optionally enter a source such as a subnet to SNAT or destination.
1. Enter the translated IP.   This IP is from the range of Public IPs you reserved from the Azure VMware Solution Portal.
1. Optionally give the rule a higher priority number. This prioritization will move the rule further down the rule list to ensure more specific rules are matched first.
1. Click **SAVE**.

Logging can be enabled by way of the logging slider. For more information on NSX-T NAT configuration and options, see the 
[NSX-T NAT Administration Guide](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-7AD2C384-4303-4D6C-A44A-DEF45AA18A92.html)
### Inbound Internet Access for VMs
A Destination Network Translation Service (DNAT) is used to expose a VM on a specific Public IP address and/or a specific port. This service provides inbound internet access to your workload VMs.

**Log in VMware NSX-T**
1.	From your Azure VMware Solution private cloud, select **VMware credentials**.
2.	Locate your NSX-T URL and credentials.
3.	Log in to **VMware NSX-T**.

**Configure the DNAT rule**
  1. Name the rule.
   1. Select **DNAT** as the action.
   1. Enter the reserved Public IP in the destination match.
   1. Enter the VM Private IP in the translated IP. This IP is from the range of Public IPs reserved from the Azure VMware Solution Portal.
   1. Select **SAVE**. 
   1. Optionally, configure the Translated Port or source IP for more specific matches.
    
The VM is now exposed to the internet on the specific Public IP and/or specific ports.

### Gateway Firewall used to filter traffic to VMs at T1 Gateways
 
You can provide security protection for your network traffic in and out of the public Internet through your Gateway Firewall. 
1.	From your Azure VMware Solution Private Cloud, select **VMware credentials**
2.	Locate your NSX-T URL and credentials.
3.	Log in to **VMware NSX-T**.   
4.	From the NSX-T home screen, select **Gateway Policies**.     
5.	Select **Gateway Specific Rules**, choose the T1 Gateway and select **ADD POLICY**. 
6.	Select **New Policy** and enter a policy name. 
7.	Select the Policy and select **ADD RULE**. 
8.	Configure the rule.
     
     1. Select **New Rule**.
     1. Enter a descriptive name.
     1. Configure the source, destination, services, and action.
    
1. Select **Match External Address** to apply firewall rules to the external address of a NAT rule.

For example, the following rule is set to Match External Address, and this setting will allow SSH traffic inbound to the Public IP.
    :::image type="content" source="media/public-ip-nsx-edge/gateway-specific-rules-match-external-connectivity.png" alt-text="Screenshot Internet connectivity inbound Public IP." lightbox="media/public-ip-nsx-edge/gateway-specific-rules-match-external-connectivity-expanded.png":::
     
If **Match Internal Address** was specified, the destination would be the internal or private IP address of the VM. 
For more information on the NSX-T Gateway Firewall see the [NSX-T Gateway Firewall Administration Guide]( https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-A52E1A6F-F27D-41D9-9493-E3A75EC35481.html)
The Distributed Firewall could be used to filter traffic to VMs. This feature is outside the scope of this document. For more information, see [NSX-T Distributed Firewall Administration Guide]( https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/administration/GUID-6AB240DB-949C-4E95-A9A7-4AC6EF5E3036.html).

## Next steps 
[Internet connectivity design considerations (Preview)](concepts-design-public-internet-access.md)

[Enable Managed SNAT for Azure VMware Solution Workloads (Preview)](enable-managed-snat-for-workloads.md)

[Disable Internet access or enable a default route](disable-internet-access.md)

[Enable HCX access over the internet](enable-hcx-access-over-internet.md)
