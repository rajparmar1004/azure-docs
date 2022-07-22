---
author: alexwolfmsft
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: include
ms.date: 07/08/2022
ms.author: alexwolf
---
1. Find the Table API **connection string** from the list of connection strings for the account with the [``az cosmosdb list-connection-strings``](/cli/azure/cosmosdb#az-cosmosdb-list-connection-strings) command.

    ```azurecli-interactive
    az cosmosdb list-connection-strings \
        --resource-group $resourceGroupName \
        --name $accountName 
    ```

1. Record the *PRIMARY KEY* values. You'll use these credentials later.