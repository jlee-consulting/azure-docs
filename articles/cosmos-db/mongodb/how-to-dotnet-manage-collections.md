---
title: Create a collection in Azure Cosmos DB MongoDB API using .NET
description: Learn how to work with a collection in your Azure Cosmos DB MongoDB API database using the .NET SDK.
author: alexwolfmsft
ms.author: alexwolf
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: how-to
ms.date: 07/22/2022
ms.custom: devx-track-dotnet
---

# Manage a collection in Azure Cosmos DB MongoDB API using .NET

[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Manage your MongoDB collection stored in Cosmos DB with the native MongoDB client driver.

> [!NOTE]
> The [example code snippets](https://github.com/Azure-Samples/cosmos-db-mongodb-api-dotnet-samples) are available on GitHub as a .NET project.

[MongoDB API reference documentation](https://docs.mongodb.com/drivers/csharp) | [MongoDB Package (NuGet)](https://www.nuget.org/packages/MongoDB.Driver)

## Name a collection

In Azure Cosmos DB, a collection is analogous to a table in a relational database. When you create a collection, the collection name forms a segment of the URI used to access the collection resource and any child docs.

Here are some quick rules when naming a collection:

* Keep collection names between 3 and 63 characters long
* Collection names can only contain lowercase letters, numbers, or the dash (-) character.
* Container names must start with a lowercase letter or number.

## Get collection instance

Use an instance of the **Collection** class to access the collection on the server.

* [MongoClient.Database.Collection](https://mongodb.github.io/node-mongodb-native/4.7/classes/Collection.html)

The following code snippets assume you've already created your [client connection](how-to-dotnet-get-started.md#create-mongoclient-with-connection-string).

## Create a collection

To create a collection, insert a document into the collection.

* [MongoClient.Database.Collection](https://mongodb.github.io/node-mongodb-native/4.5/classes/Db.html#collection)
* [MongoClient.Database.Collection.InsertOne](https://mongodb.github.io/node-mongodb-native/4.7/classes/Collection.html#insertOne)
* [MongoClient.Database.Collection.InsertMany](https://mongodb.github.io/node-mongodb-native/4.7/classes/Collection.html#insertMany)

:::code language="csharp" source="~/azure-cosmos-mongodb-dotnet/110-manage-collections/program.cs" id="create_collection":::

## Drop a collection

* [MongoClient.Db.dropCollection](https://mongodb.github.io/node-mongodb-native/4.7/classes/Db.html#dropCollection)

Drop the collection from the database to remove it permanently. However, the next insert or update operation that accesses the collection will create a new collection with that name.

:::code language="csharp" source="~/azure-cosmos-mongodb-dotnet/110-manage-collections/program.cs" id="drop_collection":::

## Get collection indexes

An index is used by the MongoDB query engine to improve performance to database queries.

* [MongoClient.Database.Collection.indexes](https://mongodb.github.io/node-mongodb-native/4.7/classes/Collection.html#indexes)

:::code language="csharp" source="~/azure-cosmos-mongodb-dotnet/110-manage-collections/program.cs" id="get_indexes":::


## See also

- [Get started with Azure Cosmos DB MongoDB API and .NET](how-to-dotnet-get-started.md)
- [Create a database](how-to-dotnet-manage-databases.md)
