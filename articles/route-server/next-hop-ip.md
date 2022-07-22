---
title: 'Next Hop IP Support'
description: Learn about Next Hop IP support for Azure Route Server.
author: halkazwini
ms.author: halkazwini
ms.service: route-server
ms.topic: conceptual
ms.date: 07/18/2022
ms.custom: template-concept
---

# Next Hop IP support

Azure Route Server simplifies the exchange of routing information between any Network Virtual Appliance (NVA) that supports the Border Gateway Protocol (BGP) routing protocol and the Azure Software Defined Network (SDN) in the Azure Virtual Network (VNet) without the need to manually configure or maintain route tables. With the support for Next Hop IP in Azure Route Server, you can peer with NVAs deployed behind an Azure Internal Load Balancer (ILB). The internal load balancer lets you set up active-passive connectivity scenarios and leverage load balancing to improve connectivity performance.

:::image type="content" source="./media/next-hop-ip/route-server-next-hop.png" alt-text="Diagram of two NVAs behind a load balancer and a Route Server.":::

## Active-passive NVA connectivity

You can deploy a set of active-passive NVAs behind an internal load balancer to ensure symmetrical routing to and from the NVA. With the support for Next Hop IP, you can define the next hop for both the active and passive NVAs as the IP address of the internal load balancer and set up the load balancer to direct traffic towards the Active NVA instance. 

## Active-active NVA connectivity

You can deploy a set of active-active NVAs behind an internal load balancer to optimize connectivity performance. With the support for Next Hop IP, you can define the next hop for both NVA instances as the IP address of the internal load balancer. Traffic that reaches the load balancer will be sent to both NVA instances.
> [!NOTE]
> * Active-Active NVA connectivity may result in asymmetric routing.

## Next hop IP configuration

Next Hop IPs are set up in the BGP configuration of the target NVAs. The Next Hop IP isn't part of the Azure Route Server configuration.

## Next steps

- Learn how to [configure Azure Route Server](quickstart-configure-route-server-portal.md).
- Learn how to [monitor Azure Route Server](monitor-route-server.md).
