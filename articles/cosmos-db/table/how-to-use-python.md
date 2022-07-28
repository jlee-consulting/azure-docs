---
title: 'Quickstart: Table API with Python - Azure Cosmos DB'
description: This quickstart shows how to access the Azure Cosmos DB Table API from a Python application using the Azure Data Tables SDK
author: sakash279
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: quickstart
ms.date: 03/23/2021
ms.author: akshanka
ms.reviewer: mjbrown
ms.custom: devx-track-python, mode-api, devx-track-azurecli
---

# Quickstart: Build a Table API app with Python SDK and Azure Cosmos DB

[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

This quickstart shows how to access the Azure Cosmos DB [Table API](introduction.md) from a Python application. The Cosmos DB Table API is a schemaless data store allowing applications to store structured NoSQL data in the cloud. Because data is stored in a schemaless design, new properties (columns) are automatically added to the table when an object with a new attribute is added to the table. Python applications can access the Cosmos DB Table API using the [Azure Data Tables SDK for Python](https://pypi.org/project/azure-data-tables/) package.

## Prerequisites

The sample application is written in [Python3.6](https://www.python.org/downloads/), though the principles apply to all Python3.6+ applications. You can use [Visual Studio Code](https://code.visualstudio.com/) as an IDE.

If you don't have an [Azure subscription](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), create a [free account](https://azure.microsoft.com/free/dotnet) before you begin.

## Sample application

The sample application for this tutorial may be cloned or downloaded from the repository https://github.com/Azure-Samples/msdocs-azure-tables-sdk-python-flask. Both a starter and completed app are included in the sample repository.

```bash
git clone https://github.com/Azure-Samples/msdocs-azure-tables-sdk-python-flask.git
```

The sample application uses weather data as an example to demonstrate the capabilities of the Table API. Objects representing weather observations are stored and retrieved using the Table API, including storing objects with additional properties to demonstrate the schemaless capabilities of the Table API.

:::image type="content" source="./media/create-table-python/table-api-app-finished-application-720px.png" alt-text="A screenshot of the finished application showing data stored in a Cosmos DB table using the Table API." lightbox="./media/create-table-python/table-api-app-finished-application.png":::

## 1 - Create an Azure Cosmos DB account

You first need to create a Cosmos DB Tables API account that will contain the table(s) used in your application. This can be done using the Azure portal, Azure CLI, or Azure PowerShell.

### [Azure portal](#tab/azure-portal)

Log in to the [Azure portal](https://portal.azure.com/) and follow these steps to create an Cosmos DB account.

| Instructions    | Screenshot |
|:----------------|-----------:|
| [!INCLUDE [Create cosmos db account step 1](./includes/create-table-python/create-cosmos-db-acct-1.md)] | :::image type="content" source="./media/create-table-python/azure-portal-create-cosmos-db-account-table-api-1-240px.png" alt-text="A screenshot showing how to use the search box in the top tool bar to find Cosmos DB accounts in Azure." lightbox="./media/create-table-python/azure-portal-create-cosmos-db-account-table-api-1.png":::           |
| [!INCLUDE [Create cosmos db account step 2](./includes/create-table-python/create-cosmos-db-acct-2.md)] | :::image type="content" source="./media/create-table-python/azure-portal-create-cosmos-db-account-table-api-2-240px.png" alt-text="A screenshot showing the Create button location on the Cosmos DB accounts page in Azure." lightbox="./media/create-table-python/azure-portal-create-cosmos-db-account-table-api-2.png":::           |
| [!INCLUDE [Create cosmos db account step 3](./includes/create-table-python/create-cosmos-db-acct-3.md)] | :::image type="content" source="./media/create-table-python/azure-portal-create-cosmos-db-account-table-api-3-240px.png" alt-text="A screenshot showing the Azure Table option as the correct option to select." lightbox="./media/create-table-python/azure-portal-create-cosmos-db-account-table-api-3.png":::           |
| [!INCLUDE [Create cosmos db account step 4](./includes/create-table-python/create-cosmos-db-acct-4.md)] | :::image type="content" source="./media/create-table-python/azure-portal-create-cosmos-db-account-table-api-4-240px.png" alt-text="A screenshot showing how to fill out the fields on the Cosmos DB Account creation page." lightbox="./media/create-table-python/azure-portal-create-cosmos-db-account-table-api-4.png":::           |

### [Azure CLI](#tab/azure-cli)

Cosmos DB accounts are created using the [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) command. You must include the `--capabilities EnableTable` option to enable table storage within your Cosmos DB. As all Azure resources must be contained in a resource group, the following code snippet also creates a resource group for the Cosmos DB account.

Cosmos DB account names must be between 3 and 44 characters in length and may contain only lowercase letters, numbers, and the hyphen (-) character. Cosmos DB account names must also be unique across Azure.

Azure CLI commands can be run in the [Azure Cloud Shell](https://shell.azure.com/) or on a workstation with the [Azure CLI installed](/cli/azure/install-azure-cli).

It typically takes several minutes for the Cosmos DB account creation process to complete.

```azurecli
LOCATION='eastus'
RESOURCE_GROUP_NAME='rg-msdocs-tables-sdk-demo'
COSMOS_ACCOUNT_NAME='cosmos-msdocs-tables-sdk-demo-123'    # change 123 to a unique set of characters for a unique name
COSMOS_TABLE_NAME='WeatherData'

az group create \
    --location $LOCATION \
    --name $RESOURCE_GROUP_NAME

az cosmosdb create \
    --name $COSMOS_ACCOUNT_NAME \
    --resource-group $RESOURCE_GROUP_NAME \
    --capabilities EnableTable
```

### [Azure PowerShell](#tab/azure-powershell)

Azure Cosmos DB accounts are created using the [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) cmdlet. You must include the `-ApiKind "Table"` option to enable table storage within your Cosmos DB.  As all Azure resources must be contained in a resource group, the following code snippet also creates a resource group for the Azure Cosmos DB account.

Azure Cosmos DB account names must be between 3 and 44 characters in length and may contain only lowercase letters, numbers, and the hyphen (-) character.  Azure Cosmos DB account names must also be unique across Azure.

Azure PowerShell commands can be run in the [Azure Cloud Shell](https://shell.azure.com) or on a workstation with [Azure PowerShell installed](/powershell/azure/install-az-ps).

It typically takes several minutes for the Cosmos DB account creation process to complete.

```azurepowershell
$location = 'eastus'
$resourceGroupName = 'rg-msdocs-tables-sdk-demo'
$cosmosAccountName = 'cosmos-msdocs-tables-sdk-demo-123'  # change 123 to a unique set of characters for a unique name

# Create a resource group
New-AzResourceGroup `
    -Location $location `
    -Name $resourceGroupName

# Create an Azure Cosmos DB 
New-AzCosmosDBAccount `
    -Name $cosmosAccountName `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    -ApiKind "Table"
```

---

## 2 - Create a table

Next, you need to create a table within your Cosmos DB account for your application to use. Unlike a traditional database, you only need to specify the name of the table, not the properties (columns) in the table. As data is loaded into your table, the properties (columns) will be automatically created as needed.

### [Azure portal](#tab/azure-portal)

In the [Azure portal](https://portal.azure.com/), complete the following steps to create a table inside your Cosmos DB account.

| Instructions    | Screenshot |
|:----------------|-----------:|
| [!INCLUDE [Create cosmos db table step 1](./includes/create-table-python/create-cosmos-table-1.md)] | :::image type="content" source="./media/create-table-python/azure-portal-create-cosmos-db-table-api-1-240px.png" alt-text="A screenshot showing how to use the search box in the top tool bar to find your Cosmos DB account." lightbox="./media/create-table-python/azure-portal-create-cosmos-db-table-api-1.png":::           |
| [!INCLUDE [Create cosmos db table step 2](./includes/create-table-python/create-cosmos-table-2.md)] | :::image type="content" source="./media/create-table-python/azure-portal-create-cosmos-db-table-api-2-240px.png" alt-text="A screenshot showing the location of the Add Table button." lightbox="./media/create-table-python/azure-portal-create-cosmos-db-table-api-2.png":::           |
| [!INCLUDE [Create cosmos db table step 3](./includes/create-table-python/create-cosmos-table-3.md)] | :::image type="content" source="./media/create-table-python/azure-portal-create-cosmos-db-table-api-3-240px.png" alt-text="A screenshot showing how to New Table dialog box for an Cosmos DB table." lightbox="./media/create-table-python/azure-portal-create-cosmos-db-table-api-3.png":::           |

### [Azure CLI](#tab/azure-cli)

Tables in Cosmos DB are created using the [az cosmosdb table create](/cli/azure/cosmosdb/table#az-cosmosdb-table-create) command.

```azurecli
COSMOS_TABLE_NAME='WeatherData'

az cosmosdb table create \
    --account-name $COSMOS_ACCOUNT_NAME \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $COSMOS_TABLE_NAME \
    --throughput 400
```

### [Azure PowerShell](#tab/azure-powershell)

Tables in Cosmos DB are created using the [New-AzCosmosDBTable](/powershell/module/az.cosmosdb/new-azcosmosdbtable) cmdlet.

```azurepowershell
$cosmosTableName = 'WeatherData'

# Create the table for the application to use
New-AzCosmosDBTable `
    -Name $cosmosTableName `
    -AccountName $cosmosAccountName `
    -ResourceGroupName $resourceGroupName
```

---

## 3 - Get Cosmos DB connection string

To access your table(s) in Cosmos DB, your app will need the table connection string for the CosmosDB Storage account.  The connection string can be retrieved using the Azure portal, Azure CLI or Azure PowerShell.

### [Azure portal](#tab/azure-portal)

| Instructions    | Screenshot |
|:----------------|-----------:|
| [!INCLUDE [Get cosmos db table connection string step 1](./includes/create-table-python/get-cosmos-connection-string-1.md)] | :::image type="content" source="./media/create-table-python/azure-portal-cosmos-db-table-connection-string-1-240px.png" alt-text="A screenshot showing the location of the connection strings link on the Cosmos DB page." lightbox="./media/create-table-python/azure-portal-cosmos-db-table-connection-string-1.png":::           |
| [!INCLUDE [Get cosmos db table connection string step 2](./includes/create-table-python/get-cosmos-connection-string-2.md)] | :::image type="content" source="./media/create-table-python/azure-portal-cosmos-db-table-connection-string-2-240px.png" alt-text="A screenshot showing which connection string to select and use in your application." lightbox="./media/create-table-python/azure-portal-cosmos-db-table-connection-string-2.png":::           |

### [Azure CLI](#tab/azure-cli)

To get the primary connection string using Azure CLI, use the [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) command with the option `--type connection-strings`. This command uses a [JMESPath query](https://jmespath.org/) to display only the primary table connection string.

```azurecli
# This gets the primary connection string
az cosmosdb keys list \
    --type connection-strings \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $COSMOS_ACCOUNT_NAME \
    --query "connectionStrings[?description=='Primary Table Connection String'].connectionString" \
    --output tsv
```

### [Azure PowerShell](#tab/azure-powershell)

To get the primary connection string using Azure PowerShell, use the [Get-AzCosmosDBAccountKey](/powershell/module/az.cosmosdb/get-azcosmosdbaccountkey) cmdlet.

```azurepowershell
# This gets the primary connection string
$(Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $cosmosAccountName `
    -Type "ConnectionStrings")."Primary Table Connection String"
```

The connection string for your Cosmos DB account is considered an app secret and must be protected like any other app secret or password.

---

## 4 - Install the Azure Data Tables SDK for Python

After you've created a Cosmos DB account, your next step is to install the Microsoft [Azure Data Tables SDK for Python](https://pypi.python.org/pypi/azure-data-tables/). For details on installing the SDK, refer to the [README.md](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/tables/azure-data-tables/README.md) file in the Data Tables SDK for Python repository on GitHub.

Install the Azure Tables client library for Python with pip:

```bash
pip install azure-data-tables
```

---

## 5 - Configure the Table client in .env file

Copy your Azure Cosmos DB account connection string from the Azure portal, and create a TableServiceClient object using your copied connection string. Switch to folder `1-strater-app` or `2-completed-app`. Then, add the value of the corresponding environment variables in `.env` file.

```python
# Configuration Parameters
conn_str = "A connection string to an Azure Cosmos account."
table_name = "WeatherData"
project_root_path = "Project abs path"
```

The Azure SDK communicates with Azure using client objects to execute different operations against Azure. The `TableServiceClient` object is the object used to communicate with the Cosmos DB Table API. An application will typically have a single `TableServiceClient` overall, and it will have a `TableClient` per table.

```python
self.conn_str = os.getenv("AZURE_CONNECTION_STRING")
self.table_service = TableServiceClient.from_connection_string(self.conn_str)
```

---

## 6 - Implement Cosmos DB table operations

All Cosmos DB table operations for the sample app are implemented in the `TableServiceHelper` class located in *helper* file under the *webapp* directory. You will need to import the `TableServiceClient` class at the top of this file to work with objects in the `azure.data.tables` SDK package.

```python
from azure.data.tables import TableServiceClient
```

At the start of the `TableServiceHelper` class, create a constructor and add a member variable for the `TableClient` object to allow the `TableClient` object to be injected into the class.

```python
def __init__(self, table_name=None, conn_str=None):
    self.table_name = table_name if table_name else os.getenv("table_name")
    self.conn_str = conn_str if conn_str else os.getenv("conn_str")
    self.table_service = TableServiceClient.from_connection_string(self.conn_str)
    self.table_client = self.table_service.get_table_client(self.table_name)
```

### Filter rows returned from a table

To filter the rows returned from a table, you can pass an OData style filter string to the `query_entities` method. For example, if you wanted to get all of the weather readings for Chicago between midnight July 1, 2021 and midnight July 2, 2021 (inclusive) you would pass in the following filter string.

```odata
PartitionKey eq 'Chicago' and RowKey ge '2021-07-01 12:00 AM' and RowKey le '2021-07-02 12:00 AM'
```

You can view related OData filter operators on the azure-data-tables website in the section [Writing Filters](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/tables/azure-data-tables/samples#writing-filters).

When request.args parameter is passed to the `query_entity` method in the `TableServiceHelper` class, it creates a filter string for each non-null property value. It then creates a combined filter string by joining all of the values together with an "and" clause. This combined filter string is passed to the `query_entities` method on the `TableClient` object and only rows matching the filter string will be returned. You can use a similar method in your code to construct suitable filter strings as required by your application.

```python
def query_entity(self, params):
    filters = []
    if params.get("partitionKey"):
        filters.append("PartitionKey eq '{}'".format(params.get("partitionKey")))
    if params.get("rowKeyDateStart") and params.get("rowKeyTimeStart"):
        filters.append("RowKey ge '{} {}'".format(params.get("rowKeyDateStart"), params.get("rowKeyTimeStart")))
    if params.get("rowKeyDateEnd") and params.get("rowKeyTimeEnd"):
        filters.append("RowKey le '{} {}'".format(params.get("rowKeyDateEnd"), params.get("rowKeyTimeEnd")))
    if params.get("minTemperature"):
        filters.append("Temperature ge {}".format(params.get("minTemperature")))
    if params.get("maxTemperature"):
        filters.append("Temperature le {}".format(params.get("maxTemperature")))
    if params.get("minPrecipitation"):
        filters.append("Precipitation ge {}".format(params.get("minPrecipitation")))
    if params.get("maxPrecipitation"):
        filters.append("Precipitation le {}".format(params.get("maxPrecipitation")))
    return list(self.table_client.query_entities(" and ".join(filters)))
```

### Insert data using a TableEntity object

The simplest way to add data to a table is by using a `TableEntity` object. In this example, data is mapped from an input model object to a `TableEntity` object. The properties on the input object representing the weather station name and observation date/time are mapped to the `PartitionKey` and `RowKey` properties respectively which together form a unique key for the row in the table. Then the additional properties on the input model object are mapped to dictionary properties on the TableEntity object. Finally, the `create_entity` method on the `TableClient` object is used to insert data into the table.

Modify the `insert_entity` function in the example application to contain the following code.

```python
def insert_entity(self):
    entity = self.deserialize()
    return self.table_client.create_entity(entity)
    
@staticmethod
def deserialize():
    params = {key: request.form.get(key) for key in request.form.keys()}
    params["PartitionKey"] = params.pop("StationName")
    params["RowKey"] = "{} {}".format(params.pop("ObservationDate"), params.pop("ObservationTime"))
    return params
```

### Upsert data using a TableEntity object

If you try to insert a row into a table with a partition key/row key combination that already exists in that table, you will receive an error. For this reason, it is often preferable to use the `upsert_entity` instead of the `create_entity` method when adding rows to a table. If the given partition key/row key combination already exists in the table, the `upsert_entity` method will update the existing row. Otherwise, the row will be added to the table.

```python
def upsert_entity(self):
    entity = self.deserialize()
    return self.table_client.upsert_entity(entity)
    
@staticmethod
def deserialize():
    params = {key: request.form.get(key) for key in request.form.keys()}
    params["PartitionKey"] = params.pop("StationName")
    params["RowKey"] = "{} {}".format(params.pop("ObservationDate"), params.pop("ObservationTime"))
    return params
```

### Insert or upsert data with variable properties

One of the advantages of using the Cosmos DB Table API is that if an object being loaded to a table contains any new properties then those properties are automatically added to the table and the values stored in Cosmos DB. There is no need to run DDL statements like ALTER TABLE to add columns as in a traditional database.

This model gives your application flexibility when dealing with data sources that may add or modify what data needs to be captured over time or when different inputs provide different data to your application. In the sample application, we can simulate a weather station that sends not just the base weather data but also some additional values. When an object with these new properties is stored in the table for the first time, the corresponding properties (columns) will be automatically added to the table.

To insert or upsert such an object using the Table API, map the properties of the expandable object into a `TableEntity` object and use the `create_entity` or `upsert_entity` methods on the `TableClient` object as appropriate.

In the sample application, the `upsert_entity` function can also implement the function of insert or upsert data with variable properties

```python
def insert_entity(self):
    entity = self.deserialize()
    return self.table_client.create_entity(entity)

def upsert_entity(self):
    entity = self.deserialize()
    return self.table_client.upsert_entity(entity)

@staticmethod
def deserialize():
    params = {key: request.form.get(key) for key in request.form.keys()}
    params["PartitionKey"] = params.pop("StationName")
    params["RowKey"] = "{} {}".format(params.pop("ObservationDate"), params.pop("ObservationTime"))
    return params
```

### Update an entity

Entities can be updated by calling the `update_entity` method on the `TableClient` object. 

In the sample app, this object is passed to the `upsert_entity` method in the `TableClient` class. It updates that entity object and uses the `upsert_entity` method save the updates to the database. 

```python
def update_entity(self):
    entity = self.update_deserialize()
    return self.table_client.update_entity(entity)
    
@staticmethod
def update_deserialize():
    params = {key: request.form.get(key) for key in request.form.keys()}
    params["PartitionKey"] = params.pop("StationName")
    params["RowKey"] = params.pop("ObservationDate")
    return params
```

### Remove an entity

To remove an entity from a table, call the `delete_entity` method on the `TableClient` object with the partition key and row key of the object.
    
```python
def delete_entity(self):
    partition_key = request.form.get("StationName")
    row_key = request.form.get("ObservationDate")
    return self.table_client.delete_entity(partition_key, row_key)
```

## 7 - Run the code

Run the sample application to interact with the Cosmos DB Table API. The first time you run the application, there will be no data because the table is empty. Use any of the buttons at the top of application to add data to the table.

:::image type="content" source="./media/create-table-python/table-api-app-data-insert-buttons-480px.png" alt-text="A screenshot of the application showing the location of the buttons used to insert data into Cosmos DB using the Table A P I." lightbox="./media/create-table-python/table-api-app-data-insert-buttons.png":::

Selecting the **Insert using Table Entity** button opens a dialog allowing you to insert or upsert a new row using a `TableEntity` object.

:::image type="content" source="./media/create-table-python/table-api-app-insert-table-entity-480px.png" alt-text="A screenshot of the application showing the dialog box used to insert data using a TableEntity object." lightbox="./media/create-table-python/table-api-app-insert-table-entity.png":::

Selecting the **Insert using Expandable** Data button brings up a dialog that enables you to insert an object with custom properties, demonstrating how the Cosmos DB Table API automatically adds properties (columns) to the table when needed. Use the *Add Custom Field* button to add one or more new properties and demonstrate this capability.

:::image type="content" source="./media/create-table-python/table-api-app-insert-expandable-entity-480px.png" alt-text="A screenshot of the application showing the dialog box used to insert data using an object with custom fields." lightbox="./media/create-table-python/table-api-app-insert-expandable-entity.png":::

Use the **Insert Sample Data** button to load some sample data into your Cosmos DB Table.

:::image type="content" source="./media/create-table-python/table-api-app-sample-data-insert-480px.png" alt-text="A screenshot of the application showing the location of the sample data insert button." lightbox="./media/create-table-python/table-api-app-sample-data-insert.png":::

Select the **Filter Results** item in the top menu to be taken to the Filter Results page.  On this page, fill out the filter criteria to demonstrate how a filter clause can be built and passed to the Cosmos DB Table API.

:::image type="content" source="./media/create-table-python/table-api-app-filter-data-480px.png" alt-text="A screenshot of the application showing filter results page and highlighting the menu item used to navigate to the page." lightbox="./media/create-table-python/table-api-app-filter-data.png":::

## Clean up resources

When you are finished with the sample application, you should remove all Azure resources related to this article from your Azure account.  You can do this by deleting the resource group.

### [Azure portal](#tab/azure-portal)

A resource group can be deleted using the [Azure portal](https://portal.azure.com/) by doing the following.

| Instructions    | Screenshot |
|:----------------|-----------:|
| [!INCLUDE [Delete resource group step 1](./includes/create-table-python/remove-resource-group-1.md)] | :::image type="content" source="./media/create-table-python/azure-portal-remove-resource-group-1-240px.png" alt-text="A screenshot showing how to search for a resource group." lightbox="./media/create-table-python/azure-portal-remove-resource-group-1.png":::           |
| [!INCLUDE [Delete resource group step 2](./includes/create-table-python/remove-resource-group-2.md)] | :::image type="content" source="./media/create-table-python/azure-portal-remove-resource-group-2-240px.png" alt-text="A screenshot showing the location of the Delete resource group button." lightbox="./media/create-table-python/azure-portal-remove-resource-group-2.png":::           |
| [!INCLUDE [Delete resource group step 3](./includes/create-table-python/remove-resource-group-3.md)] | :::image type="content" source="./media/create-table-python/azure-portal-remove-resource-group-3-240px.png" alt-text="A screenshot showing the confirmation dialog for deleting a resource group." lightbox="./media/create-table-python/azure-portal-remove-resource-group-3.png":::           |

### [Azure CLI](#tab/azure-cli)

To delete a resource group using the Azure CLI, use the [az group delete](/cli/azure/group#az-group-delete) command with the name of the resource group to be deleted.  Deleting a resource group will also remove all Azure resources contained in the resource group.

```azurecli
az group delete --name $RESOURCE_GROUP_NAME
```

### [Azure PowerShell](#tab/azure-powershell)

To delete a resource group using Azure PowerShell, use the [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) command with the name of the resource group to be deleted.  Deleting a resource group will also remove all Azure resources contained in the resource group.

```azurepowershell
Remove-AzResourceGroup -Name $resourceGroupName
```

---

## Next steps

In this quickstart, you've learned how to create an Azure Cosmos DB account, create a table using the Data Explorer, and run an app.  Now you can query your data using the Table API.  

> [!div class="nextstepaction"]
> [Import table data to the Table API](table-import.md)