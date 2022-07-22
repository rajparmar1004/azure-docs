---
title: Group writeback portal operations (preview) in Azure Active Directory
description: The access points for group writeback to on-premises Active Directory in the Azure Active Directory admin center.
keywords:
author: barclayn
manager: rkarlin
ms.author: barclayn
ms.reviewer: jordan.dahl
ms.date: 07/21/2022
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
services: active-directory
ms.custom: "it-pro"

#Customer intent: As a new Azure AD identity administrator, user management is at the core of my work so I need to understand the user management tools such as groups, administrator roles, and licenses to manage users.
ms.collection: M365-identity-device-management
---

# Group writeback in the Azure Active Directory admin center (preview)

Group writeback is a valuable tool for administrators of Azure Active Directory (Azure AD) tenants being synced with on-premises Active Directory groups. Microsoft is now previewing new capabilities for group writeback. In this preview, you can specify in the Azure AD admin center which groups you want to write back and what you’d like each group to write back as. You can write Microsoft 365 groups back to on-premises Active Directory as Distribution, Mail-enabled Security, or Security groups, and write Security groups back as Security groups. Groups are written back with a scope of universal​.

>[!NOTE]
> If you were previously writing Microsoft 365 groups back to on-premises Active Directory as universal distribution groups, they will appear in the Azure portal as not enabled for writeback in both the **Groups** page and in the properties page for a group. These pages display a new property introduced for the preview, “writeback enabled”. This property is not set by the current version of group writeback to ensure backward compatibility with the legacy version of group writeback and to avoid breaking existing customer setups.

To understand the behavior of No writeback in the portal, check the properties of the group in MS Graph.


| Portal | MS Graph| Behavior|
|--------|---------|---------|
| No writeback | isEnabled=false | Group won't be written back to on-premises Active Directory|
| No writeback | IsEnabled = null & onPremisesGroupType = null | If a Microsoft 365 group – it will be written back to on-premises Active Directory as a distribution group. </br> If an Azure AD security group – it will not be written back to on premises Active Directory. |

By default, the **Group writeback state** of groups is set to **No writeback**. This means:

- **Microsoft 365 groups**: if the group ```IsEnabled = null``` and ```onPremisesGroupType = null```, to ensure backwards compatibility with older version of group writeback, the group is written back to your on-premises Active Directory as a distribution group.
- **Azure AD security groups**: if the group ```IsEnabled = null``` and ```onPremisesGroupType = null``` then the group is not written back to your on-premises Active Directory.

## Show writeback columns

From the **All groups** overview page, you can add the group writeback columns **Target writeback type** and **Writeback enabled** to the view.  The **Target writeback type** and **Writeback enabled** columns are available for the view whether or not you have writeback enabled in Azure AD Connect.

​:::image type="content" source="./media/groups-write-back-portal/all-groups-columns.png" alt-text="Screenshot of selecting columns for writeback in the All groups list." lightbox="media/groups-write-back-portal/all-groups-columns.png":::

## Writeback column settings

The **Writeback enabled** column allows you to turn off the writeback capability for individual groups. The **Target writeback type** column allows you to specify to which group type you want this cloud group written back in your on-premises Active Directory. For an Azure AD Microsoft 365 group, you can write it back as a security group, a distribution group, or a mail-enabled security group. For an Azure AD security group, you can write it back only as a security group.

:::image type="content" source="./media/groups-write-back-portal/all-groups-view.png" alt-text="Screenshot of writeback settings columns that are visible in the All groups page." lightbox="media/groups-write-back-portal/all-groups-view.png":::

## Writeback settings in group properties

You can also configure writeback settings for a group on the property page for the group. There's a **Group writeback state** setting that allows you to turn off writeback for the group or to specify the writeback group type. When **No writeback** is selected, the group isn't written back. If you select one of the other writeback types as an option (for example, Security), then you have:

- Enabled the group for writeback
- Targeted the writeback type as a security group

:::image type="content" source="./media/groups-write-back-portal/groups-properties-view.png" alt-text="Screenshot of changing writeback settings in the group properties." lightbox="media/groups-write-back-portal/groups-properties-view.png":::

## Next steps

- Check out the groups REST API documentation for the [preview writeback property on the settings template](/graph/api/resources/group?view=graph-rest-beta&preserve-view=true).
- For more about group writeback operations, see [Azure AD Connect group writeback](../hybrid/how-to-connect-group-writeback.md)
