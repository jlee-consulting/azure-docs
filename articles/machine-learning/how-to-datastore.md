---
title: Use datastores
titleSuffix: Azure Machine Learning
description: Learn how to use datastores to connect to Azure storage services during training with Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.author: yogipandey
author: ynpandey
ms.reviewer: nibaccam
ms.date: 01/28/2022
ms.custom: contperf-fy21q1, devx-track-python, data4ml


# Customer intent: As an experienced Python developer, I need to make my data in Azure storage available to my remote compute to train my machine learning models.
---

# Create datastores

In this article, learn how to connect to data storage services on Azure with Azure Machine Learning datastores.

## Prerequisites

- An Azure subscription. If you don't have an Azure subscription, create a free account before you begin. Try the [free or paid version of Azure Machine Learning](https://azure.microsoft.com/free/).

- The [Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/intro).

- An Azure Machine Learning workspace.

> [!NOTE]
> Azure Machine Learning datastores do **not** create the underlying storage accounts, rather they link an **existing** storage account for use in Azure Machine Learning. It is not a requirement to use Azure Machine Learning datastores - you can use storage URIs directly assuming you have access to the underlying data. 


## Create an Azure Blob datastore

# [CLI: Identity-based access](#tab/cli-identity-based-access)
Create the following YAML file (updating the values):

```yaml
# my_blob_datastore.yml
$schema: https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json
name: my_blob_ds # add name of your datastore here
type: azure_blob
description: here is a description # add a description of your datastore here
account_name: my_account_name # add storage account name here
container_name: my_container_name # add storage container name here
```

Create the Azure Machine Learning datastore in the CLI:

```azurecli
az ml datastore create --file my_blob_datastore.yml
```

# [CLI: Account key](#tab/cli-account-key)
Create the following YAML file (updating the values):

```yaml
# my_blob_datastore.yml
$schema: https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json
name: blob_example
type: azure_blob
description: Datastore pointing to a blob container.
account_name: mytestblobstore
container_name: data-container
credentials:
  account_key: XXXxxxXXXxXXXXxxXXXXXxXXXXXxXxxXxXXXxXXXxXXxxxXXxxXXXxXxXXXxxXxxXXXXxxxxxXXxxxxxxXXXxXXX
```

Create the Azure Machine Learning datastore in the CLI:

```azurecli
az ml datastore create --file my_blob_datastore.yml
```

# [CLI: SAS](#tab/cli-sas)
Create the following YAML file (updating the values):

```yaml
# my_blob_datastore.yml
$schema: https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json
name: blob_sas_example
type: azure_blob
description: Datastore pointing to a blob container using SAS token.
account_name: mytestblobstore
container_name: data-container
credentials:
  sas_token: ?xx=XXXX-XX-XX&xx=xxxx&xxx=xxx&xx=xxxxxxxxxxx&xx=XXXX-XX-XXXXX:XX:XXX&xx=XXXX-XX-XXXXX:XX:XXX&xxx=xxxxx&xxx=XXxXXXxxxxxXXXXXXXxXxxxXXXXXxxXXXXXxXXXXxXXXxXXxXX
```

Create the Azure Machine Learning datastore in the CLI:

```azurecli
az ml datastore create --file my_blob_datastore.yml
```

# [Python SDK: Identity-based access](#tab/sdk-identity-based-access)

```python
from azure.ai.ml.entities import AzureBlobDatastore
from azure.ai.ml import MLClient

ml_client = MLClient.from_config()

store = AzureBlobDatastore(
    name="",
    description="",
    account_name="",
    container_name=""
)

ml_client.create_or_update(store)
```

# [Python SDK: Account key](#tab/sdk-account-key)

```python
from azure.ai.ml.entities import AzureBlobDatastore
from azure.ai.ml import MLClient

ml_client = MLClient.from_config()

store = AzureBlobDatastore(
    name="blob-protocol-example",
    description="Datastore pointing to a blob container using wasbs protocol.",
    account_name="mytestblobstore",
    container_name="data-container",
    protocol="wasbs",
    credentials={
        "account_key": "XXXxxxXXXxXXXXxxXXXXXxXXXXXxXxxXxXXXxXXXxXXxxxXXxxXXXxXxXXXxxXxxXXXXxxxxxXXxxxxxxXXXxXXX"
    },
)

ml_client.create_or_update(store)
```

# [Python SDK: SAS](#tab/sdk-SAS)

```python
from azure.ai.ml.entities import AzureBlobDatastore
from azure.ai.ml import MLClient

ml_client = MLClient.from_config()

store = AzureBlobDatastore(
    name="blob-sas-example",
    description="Datastore pointing to a blob container using SAS token.",
    account_name="mytestblobstore",
    container_name="data-container",
    credentials={
        "sas_token": "?xx=XXXX-XX-XX&xx=xxxx&xxx=xxx&xx=xxxxxxxxxxx&xx=XXXX-XX-XXXXX:XX:XXX&xx=XXXX-XX-XXXXX:XX:XXX&xxx=xxxxx&xxx=XXxXXXxxxxxXXXXXXXxXxxxXXXXXxxXXXXXxXXXXxXXXxXXxXX"
    },
)

ml_client.create_or_update(store)
```
---

## Create an Azure Data Lake Gen2 datastore

# [CLI: Identity-based access](#tab/cli-adls-identity-based-access)
Create the following YAML file (updating the values):

```yaml
# my_adls_datastore.yml
$schema: https://azuremlschemas.azureedge.net/latest/azureDataLakeGen2.schema.json
name: adls_gen2_credless_example
type: azure_data_lake_gen2
description: Credential-less datastore pointing to an Azure Data Lake Storage Gen2.
account_name: mytestdatalakegen2
filesystem: my-gen2-container
```

Create the Azure Machine Learning datastore in the CLI:

```azurecli
az ml datastore create --file my_adls_datastore.yml
```

# [CLI: Service principal](#tab/cli-adls-sp)
Create the following YAML file (updating the values):

```yaml
# my_adls_datastore.yml
$schema: https://azuremlschemas.azureedge.net/latest/azureDataLakeGen2.schema.json
name: adls_gen2_example
type: azure_data_lake_gen2
description: Datastore pointing to an Azure Data Lake Storage Gen2.
account_name: mytestdatalakegen2
filesystem: my-gen2-container
credentials:
  tenant_id: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
  client_id: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
  client_secret: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

Create the Azure Machine Learning datastore in the CLI:

```azurecli
az ml datastore create --file my_adls_datastore.yml
```

# [Python SDK: Identity-based access](#tab/sdk-adls-identity-access)

```python
from azure.ai.ml.entities import AzureDataLakeGen2Datastore
from azure.ai.ml import MLClient

ml_client = MLClient.from_config()

store = AzureDataLakeGen2Datastore(
    name="",
    description="",
    account_name="",
    file_system=""
)

ml_client.create_or_update(store)
```

# [Python SDK: Service principal](#tab/sdk-adls-sp)

```python
from azure.ai.ml.entities import AzureDataLakeGen2Datastore
from azure.ai.ml import MLClient

ml_client = MLClient.from_config()

store = AzureDataLakeGen2Datastore(
    name="adls-gen2-example",
    description="Datastore pointing to an Azure Data Lake Storage Gen2.",
    account_name="mytestdatalakegen2",
    filesystem="my-gen2-container",
    credentials={
        "tenant_id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
        "client_id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
        "client_secret": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
    },
)

ml_client.create_or_update(store)
```
---

## Create an Azure Files datastore

# [CLI: Account key](#tab/cli-azfiles-account-key)
Create the following YAML file (updating the values):

```yaml
# my_files_datastore.yml
$schema: https://azuremlschemas.azureedge.net/latest/azureFile.schema.json
name: file_example
type: azure_file
description: Datastore pointing to an Azure File Share.
account_name: mytestfilestore
file_share_name: my-share
credentials:
  account_key: XxXxXxXXXXXXXxXxXxxXxxXXXXXXXXxXxxXXxXXXXXXXxxxXxXXxXXXXXxXXxXXXxXxXxxxXXxXXxXXXXXxXxxXX
```

Create the Azure Machine Learning datastore in the CLI:

```azurecli
az ml datastore create --file my_files_datastore.yml
```

# [CLI: SAS](#tab/cli-azfiles-sas)
Create the following YAML file (updating the values):

```yaml
# my_files_datastore.yml
$schema: https://azuremlschemas.azureedge.net/latest/azureFile.schema.json
name: file_sas_example
type: azure_file
description: Datastore pointing to an Azure File Share using SAS token.
account_name: mytestfilestore
file_share_name: my-share
credentials:
  sas_token: ?xx=XXXX-XX-XX&xx=xxxx&xxx=xxx&xx=xxxxxxxxxxx&xx=XXXX-XX-XXXXX:XX:XXX&xx=XXXX-XX-XXXXX:XX:XXX&xxx=xxxxx&xxx=XXxXXXxxxxxXXXXXXXxXxxxXXXXXxxXXXXXxXXXXxXXXxXXxXX
```

Create the Azure Machine Learning datastore in the CLI:

```azurecli
az ml datastore create --file my_files_datastore.yml
```

# [Python SDK: Account key](#tab/sdk-azfiles-accountkey)

```python
from azure.ai.ml.entities import AzureFileDatastore
from azure.ai.ml import MLClient

ml_client = MLClient.from_config()

store = AzureFileDatastore(
    name="file-example",
    description="Datastore pointing to an Azure File Share.",
    account_name="mytestfilestore",
    file_share_name="my-share",
    credentials={
        "account_key": "XXXxxxXXXxXXXXxxXXXXXxXXXXXxXxxXxXXXxXXXxXXxxxXXxxXXXxXxXXXxxXxxXXXXxxxxxXXxxxxxxXXXxXXX"
    },
)

ml_client.create_or_update(store)
```

# [Python SDK: SAS](#tab/sdk-azfiles-sas)

```python
from azure.ai.ml.entities import AzureFileDatastore
from azure.ai.ml import MLClient

ml_client = MLClient.from_config()

store = AzureFileDatastore(
    name="file-sas-example",
    description="Datastore pointing to an Azure File Share using SAS token.",
    account_name="mytestfilestore",
    file_share_name="my-share",
    credentials={
        "sas_token": "?xx=XXXX-XX-XX&xx=xxxx&xxx=xxx&xx=xxxxxxxxxxx&xx=XXXX-XX-XXXXX:XX:XXX&xx=XXXX-XX-XXXXX:XX:XXX&xxx=xxxxx&xxx=XXxXXXxxxxxXXXXXXXxXxxxXXXXXxxXXXXXxXXXXxXXXxXXxXX"
    },
)

ml_client.create_or_update(store)
```
---

## Create an Azure Data Lake Gen1 datastore

# [CLI: Identity-based access](#tab/cli-adlsgen1-identity-based-access)
Create the following YAML file (updating the values):

```yaml
# my_adls_datastore.yml
$schema: https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json
name: alds_gen1_credless_example
type: azure_data_lake_gen1
description: Credential-less datastore pointing to an Azure Data Lake Storage Gen1.
store_name: mytestdatalakegen1
```

Create the Azure Machine Learning datastore in the CLI:

```azurecli
az ml datastore create --file my_adls_datastore.yml
```

# [CLI: Service principal](#tab/cli-adlsgen1-sp)
Create the following YAML file (updating the values):

```yaml
# my_adls_datastore.yml
$schema: https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json
name: adls_gen1_example
type: azure_data_lake_gen1
description: Datastore pointing to an Azure Data Lake Storage Gen1.
store_name: mytestdatalakegen1 
credentials:
  tenant_id: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
  client_id: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
  client_secret: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

Create the Azure Machine Learning datastore in the CLI:

```azurecli
az ml datastore create --file my_adls_datastore.yml
```

# [Python SDK: Identity-based access](#tab/sdk-adlsgen1-identity-access)

```python
from azure.ai.ml.entities import AzureDataLakeGen1Datastore
from azure.ai.ml import MLClient

ml_client = MLClient.from_config()

store = AzureDataLakeGen1Datastore(
    name="",
    store_name="",
    description="",
)

ml_client.create_or_update(store)
```

# [Python SDK: Service principal](#tab/sdk-adlsgen1-sp)

```python
from azure.ai.ml.entities import AzureDataLakeGen1Datastore
from azure.ai.ml import MLClient

ml_client = MLClient.from_config()

store = AzureDataLakeGen1Datastore(
    name="adls-gen1-example",
    description="Datastore pointing to an Azure Data Lake Storage Gen1.",
    store_name="mytestdatalakegen1",
    credentials={
        "tenant_id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
        "client_id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
        "client_secret": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
    },
)

ml_client.create_or_update(store)
```

---

## Next steps

- [Read data in a job](how-to-read-write-data-v2.md#read-data-in-a-job)
- [Create data assets](how-to-create-register-data-assets.md#create-data-assets)
- [Data administration](how-to-administrate-data-authentication.md#data-administration)