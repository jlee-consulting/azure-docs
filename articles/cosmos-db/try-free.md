---
title: Try Azure Cosmos DB free
description: Try Azure Cosmos DB free of charge. No sign-up or credit card required. It's easy to test your apps, deploy, and run small workloads free for 30 days. Upgrade your account at any time during your trial.
author: seesharprun
ms.author: sidandrews
ms.reviewer: mjbrown
ms.service: cosmos-db
ms.custom: ignite-2022
ms.topic: overview
ms.date: 10/05/2021
---

# Try Azure Cosmos DB free

[!INCLUDE[NoSQL, MongoDB, Cassandra, Gremlin, Table, PostgreSQL](includes/appliesto-nosql-mongodb-cassandra-gremlin-table-postgresql.md)]

[Try Azure Cosmos DB](https://aka.ms/trycosmosdb) makes it easy to try out Azure Cosmos DB for free before you commit. There's no credit card required to get started. Your account is free for 30 days. After expiration, a new sandbox account can be created. You can extend beyond 30 days for 24 hours. You can upgrade your active Try Azure Cosmos DB account at any time during the 30 day trial period. If you're using the API for NoSQL, migrate your Try Azure Cosmos DB data to your upgraded account.

This article walks you through how to create your account, limits, and upgrading your account. This article also walks through how to migrate your data from your Try Azure Cosmos DB sandbox to your own account using the API for NoSQL.

## Try Azure Cosmos DB limits

The following table lists the limits for the [Try Azure Cosmos DB](https://aka.ms/trycosmosdb) for Free trial.

| Resource | Limit |
| --- | --- |
| Duration of the trial | 30 days (a new trial can be requested after expiration) After expiration, the information stored is deleted. Prior to expiration you can upgrade your account and migrate the information stored. |
| Maximum containers per subscription (API for NoSQL, Gremlin, Table) | 1 |
| Maximum containers per subscription (API for MongoDB) | 3 |
| Maximum throughput per container | 5,000 |
| Maximum throughput per shared-throughput database | 20,000 |
| Maximum total storage per account | 10 GB |

Try Azure Cosmos DB supports global distribution in only the Central US, North Europe, and Southeast Asia regions. Azure support tickets can't be created for Try Azure Cosmos DB accounts. However, support is provided for subscribers with existing support plans.

## Create your Try Azure Cosmos DB account

From the [Try Azure Cosmos DB home page](https://aka.ms/trycosmosdb), select an API. Azure Cosmos DB provides five APIs: NoSQL and MongoDB for document data, Gremlin for graph data, Azure Table, and Cassandra.

> [!NOTE]
> Not sure which API will best meet your needs? To learn more about the APIs for Azure Cosmos DB, see [Choose an API in Azure Cosmos DB](choose-api.md).

:::image type="content" source="media/try-free/try-cosmos-db-page.png" lightbox="media/try-free/try-cosmos-db-page.png" alt-text="Screenshot of the API options including NoSQL, MongoDB, and Cassandra on the Try Azure Cosmos DB page.":::

## Launch a Quick Start

Launch the Quickstart in Data Explorer in Azure portal to start using Azure Cosmos DB or get started with our documentation.

* [API for NoSQL Quickstart](nosql/quickstart-portal.md#create-container-database)
* [API for MongoDB Quickstart](mongodb/quickstart-python.md#learn-the-object-model)
* [API for Apache Cassandra](cassandra/adoption.md)
* [API for Apache Gremlin](gremlin/quickstart-console.md#add-a-graph)
* [API for Table](table/quickstart-dotnet.md)

You can also get started with one of the learning resources in Data Explorer.

:::image type="content" source="media/try-free/data-explorer.png" lightbox="media/try-free/data-explorer.png" alt-text="Screenshot of the Azure Cosmos DB Data Explorer landing page.":::

## Upgrade your account

Your account is free for 30 days. After expiration, a new sandbox account can be created. You can upgrade your active Try Azure Cosmos DB account at any time during the 30 day trial period. Here are the steps to start an upgrade.

1. Select the option to upgrade your current account in the Dashboard page or from the [Try Azure Cosmos DB](https://aka.ms/trycosmosdb) page.

    :::image type="content" source="media/try-free/upgrade-account.png" lightbox="media/try-free/upgrade-account.png" alt-text="Confirmation page for the account upgrade experience.":::

1. Select **Sign up for Azure Account** & create an Azure Cosmos DB account.

You can migrate your database from Try Azure Cosmos DB to your new Azure account if you're utilizing the API for NoSQL after you've signed up for an Azure account. Here are the steps to migrate.

### Create an Azure Cosmos DB account

[!INCLUDE [cosmos-db-create-dbaccount](includes/cosmos-db-create-dbaccount.md)]

Navigate back to the **Upgrade** page and select **Next** to move on to the third step and move your data.

> [!NOTE]
> You can have up to one free tier Azure Cosmos DB account per Azure subscription and must opt-in when creating the account. If you do not see the option to apply the free tier discount, this means another account in the subscription has already been enabled with free tier.

:::image type="content" source="media/try-free/sign-up-sign-in.png" lightbox="media/try-free/sign-up-sign-in.png" alt-text="Screenshot of the sign-in/sign-up experience to upgrade your current account.":::

## Migrate your Try Azure Cosmos DB data

If you're using the API for NoSQL, you can migrate your Try Azure Cosmos DB data to your upgraded account. Here’s how to migrate your Try Azure Cosmos DB database to your new Azure Cosmos DB API for NoSQL account.

### Prerequisites

* Must be using the Azure Cosmos DB API for NoSQL.
* Must have an active Try Azure Cosmos DB account and Azure account.
* Must have an Azure Cosmos DB account using the API for NoSQL in your Azure subscription.

### Migrate your data

1. Locate your **Primary Connection string** for the Azure Cosmos DB account you created for your data.

    1. Go to your Azure Cosmos DB Account in the Azure portal.

    1. Find the connection string of your new Azure Cosmos DB account within the **Keys** page of your new account.

        :::image type="content" source="media/try-free/migrate-data.png" lightbox="media/try-free/migrate-data.png" alt-text="Screenshot of the Keys page for an Azure Cosmos DB account.":::

1. Insert the connection string of the new Azure Cosmos DB account in the **Upgrade your account** page.

1. Select **Next** to move the data to your account.

1. Provide your email address to be notified by email once the migration has been completed.

## Delete your account

There can only be one free Try Azure Cosmos DB account per Microsoft account. You may want to delete your account or to try different APIs, you'll have to create a new account. Here’s how to delete your account.

1. Go to the [Try AzureAzure Cosmos DB](https://aka.ms/trycosmosdb) page.

1. Select Delete my account.

    :::image type="content" source="media/try-free/upgrade-account.png" lightbox="media/try-free/upgrade-account.png" alt-text="Confirmation page for the account upgrade experience.":::

## Next steps

After you create a Try Azure Cosmos DB sandbox account, you can start building apps with Azure Cosmos DB with the following articles:

* Use [API for NoSQL to build a console app using .NET](nosql/quickstart-dotnet.md) to manage data in Azure Cosmos DB.
* Use [API for MongoDB to build a sample app using Python](mongodb/quickstart-python.md) to manage data in Azure Cosmos DB.
* [Create a Jupyter notebook](notebooks-overview.md) and analyze your data.
* Learn more about [understanding your Azure Cosmos DB bill](understand-your-bill.md)
* Get started with Azure Cosmos DB with one of our quickstarts:
  * [Get started with Azure Cosmos DB for NoSQL](nosql/quickstart-portal.md#create-container-database)
  * [Get started with Azure Cosmos DB for MongoDB](mongodb/quickstart-python.md#learn-the-object-model)
  * [Get started with Azure Cosmos DB for Cassandra](cassandra/adoption.md)
  * [Get started with Azure Cosmos DB for Gremlin](gremlin/quickstart-console.md#add-a-graph)
  * [Get started with Azure Cosmos DB for Table](table/quickstart-dotnet.md)
* Trying to do capacity planning for a migration to Azure Cosmos DB? You can use information about your existing database cluster for [capacity planning](sql/estimate-ru-with-capacity-planner.md).
* If all you know is the number of vCores and servers in your existing database cluster, see [estimating request units using vCores or vCPUs](convert-vcore-to-request-unit.md).
* If you know typical request rates for your current database workload, see [estimating request units using Azure Cosmos DB capacity planner](estimate-ru-with-capacity-planner.md).
