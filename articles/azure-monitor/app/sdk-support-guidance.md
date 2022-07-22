---
title: Application Insights SDK support guidance 
description: Support guidance for Application Insights legacy and preview SDKs
services: azure-monitor
ms.topic: conceptual
ms.date: 03/24/2022
ms.reviewer: vgorbenko
---

# Application Insights SDK support guidance

Microsoft announces feature deprecations or breaking changes at least one year in advance and strives to provide a seamless process for migration to the replacement experience.

For more information, review the [Azure SDK Lifecycle and Support Policy](https://azure.github.io/azure-sdk/policies_support.html).

> [!NOTE]
> Diagnostic tools often provide better insight into the root cause of a problem when the latest stable SDK version is used.

Support engineers are expected to provide SDK update guidance according to the following table, referencing the current SDK version in use and any alternatives.

|Current SDK version in use |Alternative version available |Update policy for support |
|---------|---------|---------|
|Latest stable minor version of a GA SDK                                        | Newer supported stable version                 | **UPDATE REQUIRED**    |
|Unsupported ([support policy](/lifecycle/faq/azure))                           | Any supported version                          | **UPDATE REQUIRED**    |
|Preview                                                                        | Stable version                                 | **UPDATE REQUIRED**    |
|Preview                                                                        | Older stable version                           | **UPDATE RECOMMENDED** |
|Preview                                                                        | Newer preview version, no older stable version | **UPDATE RECOMMENDED** |

> [!TIP]
> Switching to [auto-instrumentation](codeless-overview.md) eliminates the need for manual SDK updates.

> [!WARNING]
> Only commercially reasonable support is provided for Preview versions of the SDK. If a support incident requires escalation to development for further guidance, customers will be asked to use a fully supported SDK version to continue support. Commercially reasonable support does not include an option to engage Microsoft product development resources; technical workarounds may be limited or not possible.

To see the current version of Application Insights SDKs and previous versions release dates, reference the [release notes](release-notes.md).