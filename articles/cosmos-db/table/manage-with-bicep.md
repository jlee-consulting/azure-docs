---
title: Create and manage Azure Cosmos DB Table API with Bicep
description: Use Bicep to create and configure Azure Cosmos DB Table API. 
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
---

# Manage Azure Cosmos DB Table API resources using Bicep

[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

In this article, you learn how to use Bicep to help deploy and manage your Azure Cosmos DB Table API accounts and tables.

This article has examples for Table API accounts only. You can also find Bicep samples for [Cassandra](../cassandra/manage-with-bicep.md), [Gremlin](../graph/manage-with-bicep.md), [MongoDB](../mongodb/manage-with-bicep.md), [SQL](../sql/manage-with-bicep.md) articles.

> [!IMPORTANT]
>
> * Account names are limited to 44 characters, all lowercase.
> * To change the throughput values, redeploy the template with updated RU/s.
> * When you add or remove locations to an Azure Cosmos account, you can't simultaneously modify other properties. These operations must be done separately.

To create any of the Azure Cosmos DB resources below, copy the following example into a new bicep file. You can optionally create a parameters file to use when deploying multiple instances of the same resource with different names and values. There are many ways to deploy Azure Resource Manager templates including, [Azure CLI](../../azure-resource-manager/bicep/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) and [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md).

> [!TIP]
> To enable shared throughput when using Table API, enable account-level throughput in the Azure portal. Account-level shared throughput cannot be set using Bicep.

<a id="create-autoscale"></a>

## Azure Cosmos account for Table with autoscale throughput

Create an Azure Cosmos account for Table API with one table with autoscale throughput.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-table-autoscale/main.bicep":::

<a id="create-manual"></a>

## Azure Cosmos account for Table with standard provisioned throughput

Create an Azure Cosmos account for Table API with one table with standard throughput.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-table/main.bicep":::

## Next steps

Here are some additional resources:

* [Bicep documentation](../../azure-resource-manager/bicep/index.yml)
* [Install Bicep tools](../../azure-resource-manager/bicep/install.md)
