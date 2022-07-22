---
title: 'Quickstart: Build a Go app using Azure Cosmos DB SQL API account'
description: Gives a Go code sample you can use to connect to and query the Azure Cosmos DB SQL API
author: Duffney
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: golang
ms.topic: quickstart
ms.date: 3/4/2021
ms.author: jduffney
---

# Quickstart: Build a Go application using an Azure Cosmos DB SQL API account
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
>
> * [.NET](quickstart-dotnet.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Java](create-sql-api-java.md)
> * [Spring Data](create-sql-api-spring-data.md)
> * [Python](create-sql-api-python.md)
> * [Spark v3](create-sql-api-spark.md)
> * [Go](create-sql-api-go.md)
>


In this quickstart, you'll build a sample Go application that uses the Azure SDK for Go to manage a Cosmos DB SQL API account. Without a credit card or an Azure subscription, you can set up a free [Try Azure Cosmos DB account](https://aka.ms/trycosmosdb)

Azure Cosmos DB is a multi-model database service that lets you quickly create and query document, table, key-value, and graph databases with global distribution and horizontal scale capabilities. 

To learn more about Azure Cosmos DB, go to [Azure Cosmos DB](../introduction.md).

## Prerequisites

- A Cosmos DB Account. Your options are:
    * Within an Azure active subscription:
        * [Create an Azure free Account](https://azure.microsoft.com/free) or use your existing subscription 
        * [Visual Studio Monthly Credits](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Azure Cosmos DB Free Tier](../optimize-dev-test.md#azure-cosmos-db-free-tier)
    * Without an Azure active subscription:
        * [Try Azure Cosmos DB for free](https://aka.ms/trycosmosdb), a tests environment that lasts for 30 days.
        * [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) 
- [Go 1.16 or higher](https://golang.org/dl/)
- [Azure CLI](/cli/azure/install-azure-cli)


## Getting started

For this quickstart, you'll need to create an Azure resource group and a Cosmos DB account.

Run the following commands to create an Azure resource group:

```azurecli
az group create --name myResourceGroup --location eastus
```

Next create a Cosmos DB account by running the following command:

```
az cosmosdb create --name my-cosmosdb-account --resource-group myResourceGroup
```

### Install the package

Use the `go get` command to install the [azcosmos](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/sdk/data/azcosmos) package.

```bash
go get github.com/Azure/azure-sdk-for-go/sdk/data/azcosmos
```

## Key concepts

* A `Client` is a connection to an Azure Cosmos DB account.
* Azure Cosmos DB accounts can have multiple `databases`. A `DatabaseClient` allows you to create, read, and delete databases.
* Database within an Azure Cosmos Account can have multiple `containers`. A `ContainerClient` allows you to create, read, update, and delete containers, and to modify throughput provision.
* Information is stored as items inside containers. And the client allows you to create, read, update, and delete items in containers.

## Code examples

**Authenticate the client**

```go
var endpoint = "<azure_cosmos_uri>"
var key      = "<azure_cosmos_primary_key"

cred, err := azcosmos.NewKeyCredential(key)
if err != nil {
    log.Fatal("Failed to create a credential: ", err)
}

// Create a CosmosDB client
client, err := azcosmos.NewClientWithKey(endpoint, cred, nil)
if err != nil {
    log.Fatal("Failed to create cosmos client: ", err)
}

// Create database client
databaseClient, err := client.NewDatabase("<databaseName>")
if err != nil {
    log.Fatal("Failed to create database client:", err)
}

// Create container client
containerClient, err := client.NewContainer("<databaseName>", "<containerName>")
if err != nil {
    log.Fatal("Failed to create a container client:", err)
}
```

**Create a Cosmos DB database**

```go
import (
	"context"
	"log"
	"github.com/Azure/azure-sdk-for-go/sdk/data/azcosmos"
)

func createDatabase (client *azcosmos.Client, databaseName string) error {
//	databaseName := "adventureworks"

	// sets the name of the database
	databaseProperties := azcosmos.DatabaseProperties{ID: databaseName}

	// creating the database
	ctx := context.TODO()
	databaseResp, err := client.CreateDatabase(ctx, databaseProperties, nil)
	if err != nil {
		log.Fatal(err)
	}
	return nil
}
```

**Create a container**

```go
import (
	"context"
	"log"
	"github.com/Azure/azure-sdk-for-go/sdk/data/azcosmos"
)

func createContainer (client *azcosmos.Client, databaseName, containerName, partitionKey string) error {
//	databaseName = "adventureworks"
//	containerName = "customer"
//	partitionKey = "/customerId"
	
	databaseClient, err := client.NewDatabase(databaseName) // returns a struct that represents a database
	if err != nil {
		log.Fatal("Failed to create a database client:", err)
	}

	// Setting container properties
	containerProperties := azcosmos.ContainerProperties{
		ID: containerName,
		PartitionKeyDefinition: azcosmos.PartitionKeyDefinition{
			Paths: []string{partitionKey},
		},
	}

	// Setting container options 
	throughputProperties := azcosmos.NewManualThroughputProperties(400) //defaults to 400 if not set
	options := &azcosmos.CreateContainerOptions{
		ThroughputProperties: &throughputProperties,
	}

	ctx := context.TODO()
	containerResponse, err := databaseClient.CreateContainer(ctx, containerProperties, options)
	if err != nil {
		log.Fatal(err)

	}
	log.Printf("Container [%v] created. ActivityId %s\n", containerName, containerResponse.ActivityID)
	
	return nil
}
```

**Create an item**

```go
import (
	"context"
	"log"
	"github.com/Azure/azure-sdk-for-go/sdk/data/azcosmos"
)

func createItem(client *azcosmos.Client, databaseName, containerName, partitionKey string, item any) error {
//	databaseName = "adventureworks"
//	containerName = "customer"
//	partitionKey = "1"
/*
	item = struct {
		ID           string `json:"id"`
		CustomerId   string `json:"customerId"`
		Title        string
		FirstName    string
		LastName     string
		EmailAddress string
		PhoneNumber  string
		CreationDate string
	}{
		ID:           "1",
		CustomerId:   "1",
		Title:        "Mr",
		FirstName:    "Luke",
		LastName:     "Hayes",
		EmailAddress: "luke12@adventure-works.com",
		PhoneNumber:  "879-555-0197",
	}
*/
	// Create container client
	containerClient, err := client.NewContainer(databaseName, containerName)
	if err != nil {
		return fmt.Errorf("failed to create a container client: %s", err)
	}

	// Specifies the value of the partiton key
	pk := azcosmos.NewPartitionKeyString(partitionKey)

	b, err := json.Marshal(item)
	if err != nil {
		return err
	}
	// setting item options upon creating ie. consistency level
	itemOptions := azcosmos.ItemOptions{
		ConsistencyLevel: azcosmos.ConsistencyLevelSession.ToPtr(),
	}
	ctx := context.TODO()
	itemResponse, err := containerClient.CreateItem(ctx, pk, b, &itemOptions)

	if err != nil {
		return err
	}
	log.Printf("Status %d. Item %v created. ActivityId %s. Consuming %v Request Units.\n", itemResponse.RawResponse.StatusCode, pk, itemResponse.ActivityID, itemResponse.RequestCharge)

	return nil
}
```

**Read an item**

```go
import (
	"context"
	"log"
	"fmt"
	"github.com/Azure/azure-sdk-for-go/sdk/data/azcosmos"
)

func readItem(client *azcosmos.Client, databaseName, containerName, partitionKey, itemId string) error {
//	databaseName = "adventureworks"
//	containerName = "customer"
//	partitionKey = "1"
//	itemId = "1"

	// Create container client
	containerClient, err := client.NewContainer(databaseName, containerName)
	if err != nil {
		return fmt.Errorf("Failed to create a container client: %s", err)
	}

	// Specifies the value of the partiton key
	pk := azcosmos.NewPartitionKeyString(partitionKey)

	// Read an item
	ctx := context.TODO()
	itemResponse, err := containerClient.ReadItem(ctx, pk, itemId, nil)
	if err != nil {
		return err
	}

	itemResponseBody := struct {
		ID           string `json:"id"`
		CustomerId   string `json:"customerId"`
		Title        string
		FirstName    string
		LastName     string
		EmailAddress string
		PhoneNumber  string
		CreationDate string
	}{}

	err = json.Unmarshal(itemResponse.Value, &itemResponseBody)
	if err != nil {
		return err
	}

	b, err := json.MarshalIndent(itemResponseBody, "", "    ")
	if err != nil {
		return err
	}
	fmt.Printf("Read item with customerId %s\n", itemResponseBody.CustomerId)
	fmt.Printf("%s\n", b)

	log.Printf("Status %d. Item %v read. ActivityId %s. Consuming %v Request Units.\n", itemResponse.RawResponse.StatusCode, pk, itemResponse.ActivityID, itemResponse.RequestCharge)

	return nil
}
```

**Delete an item**

```go
import (
	"context"
	"log"
	"github.com/Azure/azure-sdk-for-go/sdk/data/azcosmos"
)

func deleteItem(client *azcosmos.Client, databaseName, containerName, partitionKey, itemId string) error {
//	databaseName = "adventureworks"
//	containerName = "customer"
//	partitionKey = "1"
//	itemId = "1"

	// Create container client
	containerClient, err := client.NewContainer(databaseName, containerName)
	if err != nil {
		return fmt.Errorf("Failed to create a container client: %s", err)
	}
	// Specifies the value of the partiton key
	pk := azcosmos.NewPartitionKeyString(partitionKey)

	// Delete an item
	ctx := context.TODO()
	res, err := containerClient.DeleteItem(ctx, pk, itemId, nil)
	if err != nil {
		return err
	}

	log.Printf("Status %d. Item %v deleted. ActivityId %s. Consuming %v Request Units.\n", res.RawResponse.StatusCode, pk, res.ActivityID, res.RequestCharge)

	return nil
}
```

## Run the code

To authenticate, you need to pass the Azure Cosmos account credentials to the application.

Get your Azure Cosmos account credentials by following these steps:

1. Sign in to the [Azure portal](https://portal.azure.com/).

1. Navigate to your Azure Cosmos account.

1. Open the **Keys** pane and copy the **URI** and **PRIMARY KEY** of your account. You'll add the URI and keys values to an environment variable in the next step.

After you've copied the **URI** and **PRIMARY KEY** of your account, save them to a new environment variable on the local machine running the application.

Use the values copied from the Azure portal to set the following environment variables:

# [Bash](#tab/bash)

```bash
export AZURE_COSMOS_ENPOINT=<Your_AZURE_COSMOS_URI>
export AZURE_COSMOS_KEY=<Your_COSMOS_PRIMARY_KEY>
```

# [PowerShell](#tab/powershell)

```powershell
$env:AZURE_COSMOS_ENDPOINT=<Your_AZURE_COSMOS_URI>
$env:AZURE_COSMOS_KEY=<Your_AZURE_COSMOS_URI>
```

---

Create a new Go module by running the following command:

```bash
go mod init azcosmos
```

```go

package main

import (
	"context"
	"encoding/json"
	"errors"
	"fmt"
	"log"
	"os"

	"github.com/Azure/azure-sdk-for-go/sdk/azcore"
	"github.com/Azure/azure-sdk-for-go/sdk/data/azcosmos"
)

func main() {
	endpoint := os.Getenv("AZURE_COSMOS_ENDPOINT")
	if endpoint == "" {
		log.Fatal("AZURE_COSMOS_ENDPOINT could not be found")
	}

	key := os.Getenv("AZURE_COSMOS_KEY")
	if key == "" {
		log.Fatal("AZURE_COSMOS_KEY could not be found")
	}

	var databaseName = "adventureworks"
	var containerName = "customer"
	var partitionKey = "/customerId"

	item := struct {
		ID           string `json:"id"`
		CustomerId   string `json:"customerId"`
		Title        string
		FirstName    string
		LastName     string
		EmailAddress string
		PhoneNumber  string
		CreationDate string
	}{
		ID:           "1",
		CustomerId:   "1",
		Title:        "Mr",
		FirstName:    "Luke",
		LastName:     "Hayes",
		EmailAddress: "luke12@adventure-works.com",
		PhoneNumber:  "879-555-0197",
	}

	cred, err := azcosmos.NewKeyCredential(key)
	if err != nil {
		log.Fatal("Failed to create a credential: ", err)
	}

	// Create a CosmosDB client
	client, err := azcosmos.NewClientWithKey(endpoint, cred, nil)
	if err != nil {
		log.Fatal("Failed to create cosmos db client: ", err)
	}
	
	err = createDatabase(client, databaseName)
	if err != nil {
		log.Printf("createDatabase failed: %s\n", err)
	}

	err = createContainer(client, databaseName, containerName, partitionKey)
	if err != nil {
		log.Printf("createContainer failed: %s\n", err)
	}

	err = createItem(client, databaseName, containerName, item.CustomerId, item)
	if err != nil {
		log.Printf("createItem failed: %s\n", err)
	}

	err = readItem(client, databaseName, containerName, item.CustomerId, item.ID)
	if err != nil {
		log.Printf("readItem failed: %s\n", err)
	}
	
	err = deleteItem(client, databaseName, containerName, item.CustomerId, item.ID)
	if err != nil {
		log.Printf("deleteItem failed: %s\n", err)
	}
}

func createDatabase(client *azcosmos.Client, databaseName string) error {
//	databaseName := "adventureworks"

	databaseProperties := azcosmos.DatabaseProperties{ID: databaseName}

	// This is a helper function that swallows 409 errors
	errorIs409 := func(err error) bool {
		var responseErr *azcore.ResponseError
		return err != nil && errors.As(err, &responseErr) && responseErr.StatusCode == 409
	}
	ctx := context.TODO()
	databaseResp, err := client.CreateDatabase(ctx, databaseProperties, nil)

	switch {
	case errorIs409(err):
		log.Printf("Database [%s] already exists\n", databaseName)
	case err != nil:
		return err
	default:
		log.Printf("Database [%v] created. ActivityId %s\n", databaseName, databaseResp.ActivityID)
	}
	return nil
}

func createContainer(client *azcosmos.Client, databaseName, containerName, partitionKey string) error {
//	databaseName = adventureworks
//	containerName = customer
//	partitionKey = "/customerId"

	databaseClient, err := client.NewDatabase(databaseName)
	if err != nil {
		return err
	}

	// creating a container
	containerProperties := azcosmos.ContainerProperties{
		ID: containerName,
		PartitionKeyDefinition: azcosmos.PartitionKeyDefinition{
			Paths: []string{partitionKey},
		},
	}

	// this is a helper function that swallows 409 errors
	errorIs409 := func(err error) bool {
		var responseErr *azcore.ResponseError
		return err != nil && errors.As(err, &responseErr) && responseErr.StatusCode == 409
	}

	// setting options upon container creation
	throughputProperties := azcosmos.NewManualThroughputProperties(400) //defaults to 400 if not set
	options := &azcosmos.CreateContainerOptions{
		ThroughputProperties: &throughputProperties,
	}
	ctx := context.TODO()
	containerResponse, err := databaseClient.CreateContainer(ctx, containerProperties, options)
	
	switch {
	case errorIs409(err):
		log.Printf("Container [%s] already exists\n", containerName)
	case err != nil:
		return err
	default:
		log.Printf("Container [%s] created. ActivityId %s\n", containerName, containerResponse.ActivityID)
	}
	return nil
}

func createItem(client *azcosmos.Client, databaseName, containerName, partitionKey string, item any) error {
//	databaseName = "adventureworks"
//	containerName = "customer"
//	partitionKey = "1"

/*	item = struct {
		ID           string `json:"id"`
		CustomerId   string `json:"customerId"`
		Title        string
		FirstName    string
		LastName     string
		EmailAddress string
		PhoneNumber  string
		CreationDate string
	}{
		ID:           "1",
		CustomerId:   "1",
		Title:        "Mr",
		FirstName:    "Luke",
		LastName:     "Hayes",
		EmailAddress: "luke12@adventure-works.com",
		PhoneNumber:  "879-555-0197",
		CreationDate: "2014-02-25T00:00:00",
	}
*/
	// create container client
	containerClient, err := client.NewContainer(databaseName, containerName)
	if err != nil {
		return fmt.Errorf("failed to create a container client: %s", err)
	}

	// specifies the value of the partiton key
	pk := azcosmos.NewPartitionKeyString(partitionKey)

	b, err := json.Marshal(item)
	if err != nil {
		return err
	}
	// setting the item options upon creating ie. consistency level
	itemOptions := azcosmos.ItemOptions{
		ConsistencyLevel: azcosmos.ConsistencyLevelSession.ToPtr(),
	}

	// this is a helper function that swallows 409 errors
	errorIs409 := func(err error) bool {
		var responseErr *azcore.ResponseError
		return err != nil && errors.As(err, &responseErr) && responseErr.StatusCode == 409
	}

	ctx := context.TODO()
	itemResponse, err := containerClient.CreateItem(ctx, pk, b, &itemOptions)
	
	switch {
	case errorIs409(err):
		log.Printf("Item with partitionkey value %s already exists\n", pk)
	case err != nil:
		return err
	default:
		log.Printf("Status %d. Item %v created. ActivityId %s. Consuming %v Request Units.\n", itemResponse.RawResponse.StatusCode, pk, itemResponse.ActivityID, itemResponse.RequestCharge)
	}
	
	return nil
}

func readItem(client *azcosmos.Client, databaseName, containerName, partitionKey, itemId string) error {
//	databaseName = "adventureworks"
//	containerName = "customer"
//	partitionKey = "1"
//	itemId = "1"

	// Create container client
	containerClient, err := client.NewContainer(databaseName, containerName)
	if err != nil {
		return fmt.Errorf("failed to create a container client: %s", err)
	}

	// Specifies the value of the partiton key
	pk := azcosmos.NewPartitionKeyString(partitionKey)

	// Read an item
	ctx := context.TODO()
	itemResponse, err := containerClient.ReadItem(ctx, pk, itemId, nil)
	if err != nil {
		return err
	}

	itemResponseBody := struct {
		ID           string `json:"id"`
		CustomerId   string `json:"customerId"`
		Title        string
		FirstName    string
		LastName     string
		EmailAddress string
		PhoneNumber  string
		CreationDate string
	}{}

	err = json.Unmarshal(itemResponse.Value, &itemResponseBody)
	if err != nil {
		return err
	}

	b, err := json.MarshalIndent(itemResponseBody, "", "    ")
	if err != nil {
		return err
	}
	fmt.Printf("Read item with customerId %s\n", itemResponseBody.CustomerId)
	fmt.Printf("%s\n", b)

	log.Printf("Status %d. Item %v read. ActivityId %s. Consuming %v Request Units.\n", itemResponse.RawResponse.StatusCode, pk, itemResponse.ActivityID, itemResponse.RequestCharge)

	return nil
}

func deleteItem(client *azcosmos.Client, databaseName, containerName, partitionKey, itemId string) error {
//	databaseName = "adventureworks"
//	containerName = "customer"
//	partitionKey = "1"
//	itemId = "1"

	// Create container client
	containerClient, err := client.NewContainer(databaseName, containerName)
	if err != nil {
		return fmt.Errorf("failed to create a container client:: %s", err)
	}
	// Specifies the value of the partiton key
	pk := azcosmos.NewPartitionKeyString(partitionKey)

	// Delete an item
	ctx := context.TODO()

	res, err := containerClient.DeleteItem(ctx, pk, itemId, nil)
	if err != nil {
		return err
	}

	log.Printf("Status %d. Item %v deleted. ActivityId %s. Consuming %v Request Units.\n", res.RawResponse.StatusCode, pk, res.ActivityID, res.RequestCharge)

	return nil
}

```
Create a new file named `main.go` and copy the code from the sample section above.

Run the following command to execute the app:

```bash
go run main.go
```

## Clean up resources

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## Next steps

In this quickstart, you've learned how to create an Azure Cosmos DB account, create a database, container, and an item entry. Now import more data to your Azure Cosmos DB account. 

Trying to do capacity planning for a migration to Azure Cosmos DB? You can use information about your existing database cluster for capacity planning.
* If all you know is the number of vcores and servers in your existing database cluster, read about [estimating request units using vCores or vCPUs](../convert-vcore-to-request-unit.md) 
* If you know typical request rates for your current database workload, read about [estimating request units using Azure Cosmos DB capacity planner](estimate-ru-with-capacity-planner.md)

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB for the SQL API](../import-data.md)
