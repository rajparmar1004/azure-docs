---
title: Create a Translator resource
titleSuffix: Azure Cognitive Services
description: This article will show you how to create an Azure Cognitive Services Translator resource and get a key and endpoint URL.
services: cognitive-services
author: laujan
ms.author: lajanuar
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 02/24/2022
---

# Create a Translator resource

In this article, you'll learn how to create a Translator resource in the Azure portal. [Azure Translator](translator-overview.md) is a cloud-based machine translation service that is part of the [Azure Cognitive Services](../what-are-cognitive-services.md) family of REST APIs. Azure resources are instances of services that you create. All API requests to Azure services require an **endpoint** URL and a read-only **key** for authenticating access.

## Prerequisites

To get started, you'll need an active [**Azure account**](https://azure.microsoft.com/free/cognitive-services/).  If you don't have one, you can [**create a free 12-month subscription**](https://azure.microsoft.com/free/).

## Create your resource

The Translator service can be accessed through two different resource types:

* [**Single-service**](https://portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) resource types enable access to a single service API key and endpoint.

* [**Multi-service**](https://portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) resource types enable access to multiple Cognitive Services using a single API key and endpoint.

> [!TIP]
> Create a Cognitive Services resource if you plan to access multiple cognitive services under a single endpoint/key. For Translator Service access only, create a Translator single-service resource. Please note that you'll need a single-service resource if you intend to use [Azure Active Directory authentication](../../active-directory/authentication/overview-authentication.md).

## Complete your project and instance details

1. **Subscription**. Select one of your available Azure subscriptions.

1. **Resource Group**. You can create a new resource group or add your resource to a pre-existing resource group that shares the same lifecycle, permissions, and policies.

1. **Resource Region**. Choose **Global** unless your business or application requires a specific region. If you're planning on using the Document Translation feature with managed identity authentication, choose a non-global region.

1. **Name**. Enter the name you have chosen for your resource. The name you choose must be unique within Azure.

   > [!NOTE]
   > If you are using a Translator feature that requires a custom domain endpoint, such as Document Translation, the value that you enter in the Name field will be the custom domain name parameter for the endpoint.

1. **Pricing tier**. Select a [pricing tier](https://azure.microsoft.com/pricing/details/cognitive-services/translator) that meets your needs:

   * Each subscription has a free tier.
   * The free tier has the same features and functionalities as paid plans and doesn't expire.
   * Only have one free subscription per account is allowed.
   * Document Translation isn't supported in the free tier. Select Standard S1 to try that feature.

1. If you've created a multi-service resource, you'll need to confirm additional usage details via the check boxes.

1. Select **Review + Create**.

1. Review the service terms and select **Create** to deploy your resource.

1. After your resource has successfully deployed, select **Go to resource**.

### Authentication keys and endpoint URL

All Cognitive Services API requests require an endpoint URL and a read-only key for authentication.

* **Authentication keys**. Your key is a unique string that is passed on every request to the Translation service. You can pass your key through a query-string parameter or by specifying it in the HTTP request header.

* **Endpoint URL**. Use the Global endpoint in your API request unless you need a specific Azure region or custom endpoint. *See* [Base URLs](reference/v3-0-reference.md#base-urls). The Global endpoint URL is `api.cognitive.microsofttranslator.com`.

## Get your authentication keys and endpoint

1. After your new resource deploys, select **Go to resource** or navigate directly to your resource page.
1. In the left rail, under *Resource Management*, select **Keys and Endpoint**.
1. Copy and paste your keys and endpoint URL in a convenient location, such as *Microsoft Notepad*.

:::image type="content" source="../media/cognitive-services-apis-create-account/get-cog-serv-keys.png" alt-text="Get key and endpoint.":::

## How to delete a  resource or resource group

> [!Warning]
> Deleting a resource group also deletes all resources contained in the group.

To remove a Cognitive Services or Translator resource, you can **delete the resource** or **delete the resource group**.

To delete the resource:

1. Navigate to your Resource Group in the Azure portal.
1. Select the resources to be deleted by selecting the adjacent check box.
1. Select **Delete** from the top menu near the right edge.
1. Type *yes* in the **Deleted Resources** dialog box.
1. Select **Delete**.

To delete the resource group:

1. Navigate to your Resource Group in the Azure portal.
1. Select the **Delete resource group** from the top menu bar near the left edge.
1. Confirm the deletion request by entering the resource group name and selecting **Delete**.

## How to get started with Translator

In our quickstart, you'll learn how to use the Translator service with REST APIs.

> [!div class="nextstepaction"]
> [Get Started with Translator](quickstart-translator.md)

## More resources

* [Microsoft Translator code samples](https://github.com/MicrosoftTranslator).  Multi-language Translator code samples are available on GitHub.
* [Microsoft Translator Support Forum](https://www.aka.ms/TranslatorForum)
* [Get Started with Azure (3-minute video)](https://azure.microsoft.com/get-started/?b=16.24)
