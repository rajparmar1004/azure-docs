---
title: Partner Events overview for customers 
description: Send or receive from a SaaS or ERP system directly to/from Azure services with Azure Event Grid.
ms.topic: conceptual
ms.date: 03/31/2022
---

# Partner Events overview for customers - Azure Event Grid
Azure Event Grid's **Partner Events** allows customers to **subscribe to events** that originate in a registered system using the same mechanism they would use for any other event source on Azure, such as an Azure service. Those registered systems integrate with Event Grid are known as "partners".

This feature also enables customers to **send events** to partner systems that support receiving and routing events to customer's solutions/endpoints in their platform. Typically, partners are software-as-a-service (SaaS) or [ERP](https://en.wikipedia.org/wiki/Enterprise_resource_planning) providers, but they might be corporate platforms wishing to make their events available to internal teams.

They purposely integrate with Event Grid to realize end-to-end customer use cases that end on Azure (customers subscribe to events sent by partner) or end on a partner system (customers subscribe to Microsoft events sent by Azure Event Grid). Customers bank on Azure Event Grid to send events published by a partner to supported destinations such as webhooks, Azure Functions, Azure Event Hubs, or Azure Service Bus, to name a few.

Customers also rely on Azure Event Grid to route events that originate in Microsoft services, such as Azure Storage, Outlook, Teams, or Azure AD, to partner systems where customer's solutions can react to them.

With Partner Events, customers can build event-driven solutions across platforms and network boundaries to receive or send events reliably, securely and at a scale.

> [!NOTE]
> If you're new to Event Grid, see the following articles that provide you with knowledge on foundational concepts: 
> - [Overview](overview.md)
> - [Concepts](concepts.md)
> - [Event handlers](event-handlers.md)

## Receive events from a partner

You receive events from a partner in a [partner topic](concepts.md#partner-topics) that's' created on your behalf by a partner. Here are the high-level steps to subscribe to events from a partner.

1. [Authorize partner to create a partner topic](subscribe-to-partner-events.md#authorize-partner-to-create-a-partner-topic) in a resource group you designate. Authorizations are stored in partner configurations (Azure resources).
2. [Request partner to forward your events](subscribe-to-partner-events.md#request-partner-to-enable-events-flow-to-a-partner-topic) from its service to your partner topic. **Partner provisions a partner topic** in the specified resource group of your Azure subscription.
3. After the partner creates a partner topic in your Azure subscription and resource group, [activate](subscribe-to-partner-events.md#activate-a-partner-topic) your partner topic. 
4. [Subscribe to events](subscribe-to-partner-events.md#subscribe-to-events) by creating one or more event subscriptions on the partner topic.

:::image type="content" source="./media/partner-events-overview/receive-events-from-partner.png" alt-text="Steps to receive events from a partner":::

> [!NOTE]
> You must [register the Azure Event Grid resource provider](subscribe-to-partner-events.md#register-the-event-grid-resource-provider) with every Azure subscription where you want create Event Grid resources. Otherwise, operations to create resources will fail.

## Send events to a partner

The process to send events to a partner is similar to that of receiving events from a partner. You send events to a partner using a [partner destination](concepts.md#partner-destination) that's created by the partner upon your request. A partner destination is a kind of resource that contains information such as the partner's endpoint URL to which Event Grid sends events. Here are the steps to send events to a partner.

1. [Authorize partner](subscribe-to-partner-events.md#authorize-partner-to-create-a-partner-topic) to create a partner destination in a resource group you designate. Authorizations are stored in partner configurations.
2. **Request partner to create a partner destination** resource in the specified Azure resource group in your Azure subscription. Prior to creating a partner destination, the partner should configure its system to be able to receive and, if supported, route your Microsoft events within its platform.
1. After the partner creates a partner destination in your Azure subscription and resource group, [activate](deliver-events-to-partner-destinations.md#activate-a-partner-destination) your partner destination. 
1. [Subscribe to events](deliver-events-to-partner-destinations.md#create-an-event-subscription-using-partner-destination) using an event subscriptions on any kind of topic available to you: system topic (Azure services), custom topic or domain (your custom solutions) or a partner topic from another partner. When configuring your event subscription, select partner destination as the endpoint type and select the partner destination to which your events are going to start flowing.


:::image type="content" source="./media/partner-events-overview/send-events-to-partner.png" alt-text="Steps to send events to a partner":::

## Why should I use Partner Events?
You may want to use the Partner Events feature if you've one or more of the following requirements.

- You want to subscribe to events that originate in a [partner](#available-partners) system and route them to event handlers on Azure or to any application or service with a public endpoint.
- You want to take advantage of the rich set Event Grid's [destinations/event handlers](overview.md#event-handlers) that react to events from partners.
- You want to forward events raised by your custom application on Azure, an Azure service, or a Microsoft service to your application or service hosted by the [partner](#available-partners) system. For example, you may want to send Azure AD, Teams, SharePoint, or Azure Storage events to a partner system on which you're a tenant for processing. 
- You need a resilient push delivery mechanism with send-retry support and at-least once semantics.
- You want to use [Cloud Events 1.0](https://cloudevents.io/) schema for your events. 
  

## Available partners
A partner must go through an [onboarding process](onboard-partner.md) before a customer can start receiving or sending events to partners. Following is the list of available partners and whether their services were designed to send events to or receive events from Event Grid.

### Microsoft partners
| Partner              | Sends events to Azure?  | Receives events from Azure?   |
| :--------------------|:-----------------------:|:----------------------:|
| Microsoft Graph API* |   Yes                   | N/A                    |

#### Microsoft Graph API
Through Microsoft Graph API, you can get events from a diverse set of Microsoft services such as [Azure AD](azure-active-directory-events.md), [Microsoft Outlook](outlook-events.md), [Teams](teams-events.md), **SharePoint**, and so on. For a complete list of event sources, see [Microsoft Graph API's change notifications documentation](/graph/webhooks#supported-resources).

### Non-Microsoft partners
| Partner       | Sends events to Azure?  | Receives events from Azure?   |
| :------------ |:-----------------------:|:----------------------:|
| Auth0         |  Yes                    | N/A                    |

### Auth0

[Auth0](https://auth0.com) is a managed authentication platform for businesses to authenticate, authorize, and secure access for applications, devices, and users. You can create an [Auth0 partner topic](auth0-overview.md) to connect your Auth0 and Azure accounts. This integration allows you to react to, log, and monitor Auth0 events in real time. To try it out, see [Integrate Azure Event Grid with Auto0](auth0-how-to.md).

## Verified partners

A verified partner is a partner organization whose identity has been validated by Microsoft. Not all partners are verified as verification is requested by the partner. However, all partners in the [Event Grid Gallery](https://ms.portal.azure.com/#create/Microsoft.EventGridPartnerTopic) have been vetted as verification is required before they can have a presence on the Azure portal.

>[!IMPORTANT]
>You should only work with verified partners. However, there are valid cases where you might work with partners that haven't been verified. For example, the partner may be a team in your own company that's the owner of a platform solution that publishes events to corporate applications. 

## Resources managed by customers
You manage the following types of resources.

- **Partner topic** is the resource where you receive your events from the partner. 
- **Partner destination** is a resource that represents the partner system to which you can send events.
- **[Event subscriptions](subscribe-through-portal.md)** is where you select what events to forward to an Azure service, a partner destination or to a public webhook on Azure or elsewhere. 
- **Partner configurations** is the resource the holds your authorizations to partners to create partner resources.
 
## Grant authorization to create partner topics and destinations

You must authorize partners to create partner topics or partner destinations before they attempt to create those resources. If you don't grant your authorization, the partners' attempt to create the partner resource will fail. 

You consent the partner to create partner topics or partner destinations by creating a **partner configuration** resource. You add a partner authorization to a partner configuration identifying the partner and providing an authorization expiration time by which a partner topic/destination must be created. The only types of resources that partners can create with your permission are partner topics and partner destinations.

>[!IMPORTANT]
> A verified partner isn't an authorized partner. Even if a partner has been vetted by Microsoft, you still need to authorize it before the partner can create resources on your behalf. 

## Subscribe to events from a partner system
For detailed instructions on how to subscribe to events published by a partner, see [subscribe to partner events](subscribe-to-partner-events.md).


## Pricing
Partner Events are charged by the number of operations done when routing events to or from Event Grid. For more information on all types of operations that are used as the basis for billing and detailed price information, see [Event Grid pricing](https://azure.microsoft.com/pricing/details/event-grid/).

## Limits
See [Event Grid Service limits](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits) for detailed information about the limits in place for partner topics.


## Next steps
- [subscribe to partner events](subscribe-to-partner-events.md)
- [Auth0 partner topic](auth0-overview.md)
- [How to use the Auth0 partner topic](auth0-how-to.md)
