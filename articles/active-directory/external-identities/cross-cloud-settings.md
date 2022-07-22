---
title: Configure B2B collaboration Microsoft cloud settings - Azure AD
description: Use Microsoft cloud settings to enable cross-cloud B2B collaboration between sovereign (national) Microsoft Azure clouds.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/30/2022

ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: "it-pro"
ms.collection: M365-identity-device-management
---

# Configure Microsoft cloud settings for B2B collaboration (Preview)

> [!NOTE]
> Microsoft cloud settings are preview features of Azure Active Directory. For more information about previews, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

When Azure AD organizations in separate Microsoft Azure clouds need to collaborate, they can use Microsoft cloud settings to enable Azure AD B2B collaboration. B2B collaboration is available between the following global and sovereign Microsoft Azure clouds:

- Microsoft Azure global cloud and Microsoft Azure Government
- Microsoft Azure global cloud and Microsoft Azure China 21Vianet

To set up B2B collaboration between partner organizations in different Microsoft Azure clouds, each partner mutually agrees to configure B2B collaboration with each other. In each organization, an admin completes the following steps:

1. Configures their Microsoft cloud settings to enable collaboration with the partner's cloud.

1. Uses the partner's tenant ID to find and add the partner to their organizational settings.

1. Configures their inbound and outbound settings for the partner organization. The admin can either apply the default settings or configure specific settings for the partner.

After each organization has completed these steps, Azure AD B2B collaboration between the organizations is enabled.

> [!NOTE]
> B2B direct connect is not supported for collaboration with Azure AD tenants in a different Microsoft cloud.

## Before you begin

- **Obtain the partner's tenant ID.** To enable B2B collaboration with a partner's Azure AD organization in another Microsoft Azure cloud, you'll need the partner's tenant ID. Using an organization's domain name for lookup isn't available in cross-cloud scenarios.
- **Decide on inbound and outbound access settings for the partner.** Selecting a cloud in your Microsoft cloud settings doesn't automatically enable B2B collaboration. Once you enable another Microsoft Azure cloud, all B2B collaboration is blocked by default for organizations in that cloud. You'll need to add the tenant you want to collaborate with to your Organizational settings. At that point, your default settings go into effect for that tenant only. You can allow the default settings to remain in effect. Or, you can modify the inbound and outbound settings for the organization.
- **Obtain any required object IDs or app IDs.** If you want to apply access settings to specific users, groups, or applications in the partner organization, you'll need to contact the organization for information before configuring your settings. Obtain their user object IDs, group object IDs, or application IDs (*client app IDs* or *resource app IDs*) so you can target your settings correctly.

## Enable the cloud in your Microsoft cloud settings

In your Microsoft cloud settings, enable the Microsoft Azure cloud you want to collaborate with.

1. Sign in to the [Azure portal](https://portal.azure.com) using a Global administrator or Security administrator account. Then open the **Azure Active Directory** service.
1. Select **External Identities**, and then select **Cross-tenant access settings**.
1. Select **Microsoft cloud settings (Preview)**.
1. Select the checkboxes next to the external Microsoft Azure clouds you want to enable.

   ![Screenshot showing Microsoft cloud settings.](media/cross-cloud-settings/cross-cloud-settings.png)

> [!NOTE]
> Selecting a cloud doesn't automatically enable B2B collaboration with organizations in that cloud. You'll need to add the organization you want to collaborate with, as described in the next section.

## Add the tenant to your organizational settings

Follow these steps to add the tenant you want to collaborate with to your Organizational settings.

1. Sign in to the [Azure portal](https://portal.azure.com) using a Global administrator or Security administrator account. Then open the **Azure Active Directory** service.
1. Select **External Identities**, and then select **Cross-tenant access settings**.
1. Select **Organizational settings**.
1. Select **Add organization**.
1. On the **Add organization** pane, type the tenant ID for the organization (cross-cloud lookup by domain name isn't currently available).

   ![Screenshot showing adding an organization.](media/cross-cloud-settings/cross-tenant-add-organization.png)

1. Select the organization in the search results, and then select **Add**.
1. The organization appears in the **Organizational settings** list. At this point, all access settings for this organization are inherited from your default settings.

   ![Screenshot showing an organization added with default settings.](media/cross-cloud-settings/org-specific-settings-inherited.png)


1. If you want to change the cross-tenant access settings for this organization, select the **Inherited from default** link under the **Inbound access** or **Outbound access** column. Then follow the detailed steps in these sections:

   - [Modify inbound access settings](cross-tenant-access-settings-b2b-collaboration.md#modify-inbound-access-settings)
   - [Modify outbound access settings](cross-tenant-access-settings-b2b-collaboration.md#modify-outbound-access-settings)

## Next steps

See [Configure external collaboration settings](external-collaboration-settings-configure.md) for B2B collaboration with non-Azure AD identities, social identities, and non-IT managed external accounts.
