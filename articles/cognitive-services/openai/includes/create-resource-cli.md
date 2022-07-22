---
title: 'How to use the Azure CLI to create an Azure OpenAI resource and manage deployments'
titleSuffix: Azure OpenAI
description: Step by step guide for using the Azure CLI to deploy an OpenAI Resource and manage deployments
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: openai
ms.topic: include
ms.date: 6/30/2022
keywords: 

---

## Prerequisites

- An Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services)
- Access granted to service in the desired AzuFre subscription. This service is currently invite only. You can fill out a new use case request here: <https://aka.ms/oai/access>. Please open an issue on this repo to contact us if you have an issue
- Azure CLI. [Installation guide](/cli/azure/install-azure-cli)

## Sign in to the CLI

run the az login command to log in, `az login`

## Create a new Azure Resource Group
You must have an Azure resource group in order to create an OpenAI resource. When you create a new resource, you have the option to either create a new resource group, or use an existing one. This article shows how to create a new resource group. You can create a new resource group in the Azure CLI using the `az group create` command. The example below creates a new resource group in the eastus location. you can find the full [reference documentation here](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-create).

```azurecli
az group create \
--name OAIResourceGroup\
--location eastus
```

## Create a resource
Run the following command to create an OpenAI resource in the new resource group. In this example, we create a resource called MyOpenAIResource in the resource group called OAIResourceGroup. Make sure to update with your own values for the resource group, resource name and your Azure Subscription ID. You can find the full [reference documentation here](/cli/azure/cognitiveservices/account?view=azure-cli-latest&preserve-view=true#az-cognitiveservices-account-create).

```azurecli
az cognitiveservices account create \
-n MyOpenAIResource \
-g OAIResourceGroup \
-l eastus \
--kind OpenAI \
--sku s0 \
--subscription 00000000-0000-0000-0000-000000000000
 ```
    

## Retrieve information from your resource
Once your resource has been created, you can use the Azure CLI to find useful information about your service such as your REST API endpoint base URL and the access keys. Below are examples on how to do both. You can find the full [reference documentation here](/cli/azure/cognitiveservices/account?view=azure-cli-latest&preserve-view=true).

1.	**Retrieve your endpoint**: 

    ```azurecli
    az cognitiveservices account show \
    -n $myResourceName \
    -g $myResourceGroupName \
    | jq -r .properties.endpoint
    ```
1.	**Retrieve your primary API key**:
    ```azurecli
    az cognitiveservices account keys list \
    -n $myResourceName \
    -g $myResourceGroupName 
    | jq -r .key1
    ```

## Deploy a model

To deploy a model, you can use the Azure CLI to run the following command to deploy an instance of text-curie-001. In this example, we deploy a model called MyModel. Make sure to update with your own values. You don't need to change the `model-version`, `model-format` or `scale-settings-scale-type` values. You can find the full [reference documentation here](/cli/azure/cognitiveservices/account/deployment?view=azure-cli-latest&preserve-view=true).

```azurecli
az cognitiveservices account deployment create \
   -g $myResourceGroupName \
   -n $myResourceName \
   -deployment-name MyModel \
   --model-name text-curie-001 \
   --model-version "1"  \
   --model-format OpenAI \
   --scale-settings-scale-type "Standard"
```

## Delete a model from your resource

You can delete any model you've deployed from your resource. To do so, you can use the Azure CLI to run the following command. In this example, we delete a model called MyModel. Make sure to update with your own values. You can find the full [reference documentation here](/cli/azure/cognitiveservices/account/deployment?view=azure-cli-latest&preserve-view=true#az-cognitiveservices-account-deployment-delete).

```azurecli
az cognitiveservices account deployment delete \
  -g $myResourceGroupName \
  -n $myResourceName \
  --deployment-name MyModel
```

## Delete a resource
If you want to clean up and remove your OpenAI resource, you can delete it or the resource group. Deleting the resource group also deletes any other resources contained in the group.

To remove the resource group and its associated resources, use the `az group delete` command.

If you're not going to continue to use this application, delete your resource  with the following steps:

```azurecli
az cognitiveservices account delete \
--name MyOpenAIResource  \
-g OAIResourceGroup
```