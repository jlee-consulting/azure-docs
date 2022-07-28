---
title: Azure CLI Samples for Azure Cosmos DB API for MongoDB
description: Azure CLI Samples for Azure Cosmos DB API for MongoDB
author: seesharprun
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 02/21/2022
ms.author: sidandrews
ms.reviewer: mjbrown 
ms.custom: devx-track-azurecli
---

# Azure CLI samples for Azure Cosmos DB API for MongoDB

[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

The following tables include links to sample Azure CLI scripts for the Azure Cosmos DB MongoDB API  and to sample Azure CLI scripts that apply to all Cosmos DB APIs. Common samples are the same across all APIs.

These samples require Azure CLI version 2.30 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI](/cli/azure/install-azure-cli). If using Azure Cloud Shell, the latest version is already installed.

## MongoDB API Samples

|Task | Description |
|---|---|
| [Create an Azure Cosmos account, database and collection](../scripts/cli/mongodb/create.md)| Creates an Azure Cosmos DB account, database, and collection for MongoDB API. |
| [Create a serverless Azure Cosmos account, database and collection](../scripts/cli/mongodb/serverless.md)| Creates a serverless Azure Cosmos DB account, database, and collection for MongoDB API. |
| [Create an Azure Cosmos account, database with autoscale and two collections with shared throughput](../scripts/cli/mongodb/autoscale.md)| Creates an Azure Cosmos DB account, database with autoscale and two collections with shared throughput for MongoDB API. |
| [Perform throughput operations](../scripts/cli/mongodb/throughput.md) | Read, update and migrate between autoscale and standard throughput on a database and collection.|
| [Lock resources from deletion](../scripts/cli/mongodb/lock.md)| Prevent resources from being deleted with  resource locks.|
|||

## Common API Samples

These samples apply to all Azure Cosmos DB APIs. These samples use a SQL (Core) API account, but these operations are identical across all database APIs in Cosmos DB.

|Task | Description |
|---|---|
| [Add or fail over regions](../scripts/cli/common/regions.md) | Add a region, change failover priority, trigger a manual failover.|
| [Perform account key operations](../scripts/cli/common/keys.md) | List account keys, read-only keys, regenerate keys and list connection strings.|
| [Secure with IP firewall](../scripts/cli/common/ipfirewall.md)| Create a Cosmos account with IP firewall configured.|
| [Secure new account with service endpoints](../scripts/cli/common/service-endpoints.md)| Create a Cosmos account and secure with service-endpoints.|
| [Secure existing account with service endpoints](../scripts/cli/common/service-endpoints-ignore-missing-vnet.md)| Update a Cosmos account to secure with service-endpoints when the subnet is eventually configured.|
|||

## Next steps

Reference pages for all Azure Cosmos DB CLI commands are available in the [Azure CLI Reference](/cli/azure/cosmosdb).

For Azure CLI samples for other APIs see:

- [CLI Samples for Cassandra](../cassandra/cli-samples.md)
- [CLI Samples for Gremlin](../graph/cli-samples.md)
- [CLI Samples for SQL](../sql/cli-samples.md)
- [CLI Samples for Table](../table/cli-samples.md)
