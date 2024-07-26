---
title: Preview feature list
titleSuffix: Azure AI Search
description: Preview features are released so that customers can provide feedback on their design and utility. This article is a comprehensive list of all features currently in preview.

manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.custom:
  - build-2024
ms.topic: conceptual
ms.date: 06/25/2024
---

# Preview features in Azure AI Search

This article identifies all data plane and control plane features in public preview. This list is helpful for checking feature status. It also provides usage guidance and reminders to always upgrade to newer preview API versions as they roll out.

Preview API versions are cumulative and roll up to newer Preview versions. We recommend always using the latest preview APIs for full access to all preview features.

Preview features are removed from this list if they're retired or transition to general availability. For announcements regarding general availability and retirement, see [Service Updates](https://azure.microsoft.com/updates/?product=search) or [What's New](whats-new.md).

## Data plane preview features

|Feature&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Category | Description | Availability  |
|---------|------------------|-------------|---------------|
|  [**Scalar quantization**](vector-search-how-to-configure-compression-storage.md#option-1-configure-scalar-quantization) | Index | Compress vector index size in memory and on disk using built-in scalar quantization. | [Create or Update Index (preview)](/rest/api/searchservice/indexes/create-or-update?view=rest-searchservice-2024-05-01-preview&preserve-view=true) to add a `compressions` section to a vector profile. |
|  [**Narrow data types**](vector-search-how-to-configure-compression-storage.md#option-2-assign-narrow-data-types-to-vector-fields) | Index | Assign a smaller data type on vector fields, assuming incoming data is of that data type. | [Create or Update Index (preview)](/rest/api/searchservice/indexes/create-or-update?view=rest-searchservice-2024-05-01-preview&preserve-view=true) to specify a vector field definition. [Binary vector support](vector-search-how-to-index-binary-data.md) is added in 2024-05-01-preview.|
|  [**stored property**](vector-search-how-to-configure-compression-storage.md#option-3-set-the-stored-property-to-remove-retrievable-storage) | Index | Boolean that reduces storage of vector indexes by *not* storing retrievable vectors. | [Create or Update Index (preview)](/rest/api/searchservice/indexes/create-or-update?view=rest-searchservice-2024-05-01-preview&preserve-view=true) to set `stored` on a vector field. |
|  [**Vectorizers**](vector-search-integrated-vectorization.md) | Queries | Text-to-vector conversion during query execution. | [Create or Update Index (preview)](/rest/api/searchservice/indexes/create-or-update?view=rest-searchservice-2024-05-01-preview&preserve-view=true) to define a `vectorizer`. [Search POST (preview)](/rest/api/searchservice/documents/search-post?view=rest-searchservice-2024-05-01-preview&preserve-view=true) for `vectorQueries`. Vectorizers should be paired with an equivalent skill that supports integrated vectorization during indexing. Skills used for embeddings during indexing include AzureOpenAIEmbedding, Azure AI Vision multimodal, AML for models in the Azure AI Studio model catalog. There are vectorizers that correspond to each one of these embedding skills. Always use the same embedding model for both queries and indexing. |
|  [**Integrated vectorization**](vector-search-integrated-vectorization.md) | Index, skillset | Skills-driven data chunking and embedding during indexing. | [Create or Update Skillset (preview)](/rest/api/searchservice/skillsets/create-or-update?view=rest-searchservice-2024-05-01-preview&preserve-view=true) for AzureOpenAIEmbedding skill and the data chunking properties of the Text Split skill. |
| [**Import and vectorize data**](search-get-started-portal-import-vectors.md) | Azure portal | A wizard that creates a full indexing pipeline that includes data chunking and vectorization. The wizard creates all of the objects and configuration settings. | Available on all search services, in all regions. |
| [**AzureOpenAIEmbedding skill**](cognitive-search-skill-azure-openai-embedding.md) | Applied AI (skills) | A new skill type that calls Azure OpenAI embedding model to generate embeddings during queries and indexing. | [Create or Update Skillset (preview)](/rest/api/searchservice/skillsets/create-or-update?view=rest-searchservice-2024-05-01-preview&preserve-view=true). Also available in the portal through the [Import and vectorize data wizard](search-get-started-portal-import-vectors.md). |
| [**Azure AI Vision multimodal embedding skill**](cognitive-search-skill-vision-vectorize.md) | Applied AI (skills) | A new skill type that calls Azure AI Vision multimodal API to generate embeddings for text or images during indexing. | [Create or Update Skillset (preview)](/rest/api/searchservice/skillsets/create-or-update?view=rest-searchservice-2024-05-01-preview&preserve-view=true). |
| [**Text Split skill**](cognitive-search-skill-textsplit.md) | Applied AI (skills) | Text Split has two new chunking-related properties in preview: `maximumPagesToTake`, `pageOverlapLength`. | [Create or Update Skillset (preview)](/rest/api/searchservice/skillsets/create-or-update?view=rest-searchservice-2024-05-01-preview&preserve-view=true) adds support for the preview properties. These properties are also used in the portal through the [Import and vectorize data wizard](search-get-started-portal-import-vectors.md). |
| [**Azure Machine Learning (AML) skill**](cognitive-search-aml-skill.md) | Applied AI (skills) | AML skill integrates an inferencing endpoint from Azure Machine Learning. | [Create or Update Skillset (preview)](/rest/api/searchservice/skillsets/create-or-update?view=rest-searchservice-2024-05-01-preview&preserve-view=true). In previous preview APIs, it supports connections to deployed custom models in an AML workspace. Starting in the 2024-05-01-preview, you can use this skill in workflows that connect to embedding models in the Azure AI Studio model catalog. It's also available in the portal, in skillset design, assuming Azure AI Search and Azure Machine Learning services are deployed in the same subscription. |
| [**Incremental enrichment**](cognitive-search-incremental-indexing-conceptual.md) | Applied AI (skills) | Adds caching to an enrichment pipeline, allowing you to reuse existing output if a targeted modification, such as an update to a skillset or another object, doesn't change the content. Caching applies only to enriched documents produced by a skillset.| [Create or Update Indexer (preview)](/rest/api/searchservice/indexers/create-or-update?view=rest-searchservice-2024-05-01-preview&preserve-view=true). |
| [**Index projections**](index-projections-concept-intro.md) | Applied AI (skills) | A component of a skillset definition that defines the shape of a secondary index, supporting a one-to-many index pattern, where content from an enrichment pipeline can target multiple indexes.| [Create or Update Skillset (preview)](/rest/api/searchservice/skillsets/create-or-update?view=rest-searchservice-2024-05-01-preview&preserve-view=true). Also available in the portal through the [Import and vectorize data wizard](search-get-started-portal-import-vectors.md). |
|  [**OneLake files indexer**](search-how-to-index-onelake-files.md) | Indexer data source | New data source for extracting searchable data and metadata data from a [lakehouse](/fabric/onelake/create-lakehouse-onelake) on top of [OneLake](/fabric/onelake/onelake-overview) | [Create or Update Data Source (preview)](/rest/api/searchservice/data-sources/create-or-update?view=rest-searchservice-2024-05-01-preview&preserve-view=true). |
|  [**Azure Files indexer**](search-file-storage-integration.md) | Indexer data source | New data source for indexer-based indexing from [Azure Files](https://azure.microsoft.com/services/storage/files/) | [Create or Update Data Source (preview)](/rest/api/searchservice/data-sources/create-or-update?view=rest-searchservice-2024-05-01-preview&preserve-view=true). |
| [**SharePoint Online indexer**](search-howto-index-sharepoint-online.md) | Indexer data source | New data source for indexer-based indexing of SharePoint content. | [Sign up](https://aka.ms/azure-cognitive-search/indexer-preview) to enable the feature. [Create or Update Data Source (preview)](/rest/api/searchservice/data-sources/create-or-update?view=rest-searchservice-2024-05-01-preview&preserve-view=true) or the Azure portal. |
|  [**MySQL indexer**](search-howto-index-mysql.md) | Indexer data source | New data source for indexer-based indexing of Azure MySQL data sources.| [Sign up](https://aka.ms/azure-cognitive-search/indexer-preview) to enable the feature. [Create or Update Data Source (preview)](/rest/api/searchservice/data-sources/create-or-update?view=rest-searchservice-2024-05-01-preview&preserve-view=true), [.NET SDK 11.2.1](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype.mysql), and Azure portal. |
| [**Azure Cosmos DB for MongoDB indexer**](search-howto-index-cosmosdb.md) | Indexer data source | New data source for indexer-based indexing through the MongoDB APIs in Azure Cosmos DB. | [Sign up](https://aka.ms/azure-cognitive-search/indexer-preview) to enable the feature. [Create or Update Data Source (preview)](/rest/api/searchservice/data-sources/create-or-update?view=rest-searchservice-2024-05-01-preview&preserve-view=true) or the Azure portal. |
| [**Azure Cosmos DB for Apache Gremlin indexer**](search-howto-index-cosmosdb.md) | Indexer data source | New data source for indexer-based indexing through the Apache Gremlin APIs in Azure Cosmos DB. | [Sign up](https://aka.ms/azure-cognitive-search/indexer-preview) to enable the feature. [Create or Update Data Source (preview)](/rest/api/searchservice/data-sources/create-or-update?view=rest-searchservice-2024-05-01-preview&preserve-view=true). |
| [**Native blob soft delete**](search-howto-index-changed-deleted-blobs.md) | Indexer data source | Applies to the Azure Blob Storage indexer. Recognizes blobs that are in a soft-deleted state, and removes the corresponding search document during indexing. | [Create or Update Data Source (preview)](/rest/api/searchservice/data-sources/create-or-update?view=rest-searchservice-2024-05-01-preview&preserve-view=true). |
| [**Reset Documents**](search-howto-run-reset-indexers.md) | Indexer | Reprocesses individually selected search documents in indexer workloads. | [Reset Documents (preview)](/rest/api/searchservice/indexers/reset-docs?view=rest-searchservice-2024-05-01-preview&preserve-view=true). |
| [**speller**](speller-how-to-add.md) | Query | Optional spelling correction on query term inputs for simple, full, and semantic queries. | [Search Documents (preview)](/rest/api/searchservice/documents/search-post?view=rest-searchservice-2024-05-01-preview&preserve-view=true). |
| [**Normalizers**](search-normalizers.md) | Query |  Normalizers provide simple text preprocessing: consistent casing, accent removal, and ASCII folding, without invoking the full text analysis chain.| [Search Documents (preview)](/rest/api/searchservice/documents/search-post?view=rest-searchservice-2024-05-01-preview&preserve-view=true). |
| [**featuresMode parameter**](/rest/api/searchservice/documents/search-post?view=rest-searchservice-2024-05-01-preview&preserve-view=true) | Relevance (scoring) | Relevance score expansion to include details: per field similarity score, per field term frequency, and per field number of unique tokens matched. You can consume these data points in [custom scoring solutions](https://github.com/Azure-Samples/search-ranking-tutorial). | [Search Documents (preview)](/rest/api/searchservice/documents/search-post?view=rest-searchservice-2024-05-01-preview&preserve-view=true).|
| [**moreLikeThis**](search-more-like-this.md) | Query | Finds documents that are relevant to a specific document. This feature has been in earlier previews. | [Search Documents (preview)](/rest/api/searchservice/documents/search-post?view=rest-searchservice-2024-05-01-preview&preserve-view=true). |

## Control plane preview features

|Feature&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Category | Description | Availability  |
|---------|------------------|-------------|---------------|
| [**Search service under a user-assigned managed identity**](search-howto-managed-identities-data-sources.md) | Service | Configures a search service to use a previously created user-assigned managed identity. | [Services - Update](/rest/api/searchmanagement/services/update?view=rest-searchmanagement-2024-06-01-preview&preserve-view=true#identity), 2021-04-01-preview or the latest preview version. We recommend using the latest preview version. |

## Preview features in Azure SDKs

Each Azure SDK team releases beta packages on their own timeline. Check the change log for mentions of new features in beta packages:

+ [Change log for Azure SDK for .NET](https://github.com/Azure/azure-sdk-for-net/blob/Azure.Search.Documents_11.5.0-beta.5/sdk/search/Azure.Search.Documents/CHANGELOG.md)
+ [Change log for Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.5.11/sdk/search/azure-search-documents/CHANGELOG.md)
+ [Change log for Azure SDK for JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/search-documents_11.3.3/sdk/search/search-documents/CHANGELOG.md)
+ [Change log for Azure SDK for Python](https://github.com/Azure/azure-sdk-for-python/blob/azure-search-documents_11.3.0/sdk/search/azure-search-documents/CHANGELOG.md).

## Using preview features

Experimental features are available through the preview REST API first, followed by Azure portal, and then the Azure SDKs. 

The following statements apply to preview features:

+ Preview features are available under [Supplemental Terms of Use](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), without a service level agreement.
+ Preview features might undergo breaking changes if a redesign is required. 
+ Sometimes preview features don't make it into a GA release.

If you write code against a preview API, you should prepare to upgrade that code to newer API versions when they roll out. We maintain an [Upgrade REST APIs](search-api-migration.md) document to make that step easier.

## How to call a preview REST API

Preview REST APIs are accessed through the api-version parameter on the URI. Older previews are still operational but become stale over time and aren't updated with new features or bug fixes.

For data plane operation on content, [**`2024-05-01-preview`**](/rest/api/searchservice/search-service-api-versions#2024-05-01-Preview) is the most recent preview version. The following example shows the syntax for [Indexes GET (preview)](/rest/api/searchservice/indexes/get?view=rest-searchservice-2024-05-01-preview&preserve-view=true):

```rest
GET {endpoint}/indexes('{indexName}')?api-version=2024-05-01-Preview
```

For management operations on the search service, [**`2024-06-01-preview`**](/rest/api/searchmanagement/services/update?view=rest-searchmanagement-2024-06-01-preview&preserve-view=true) is the most recent preview version. The following example shows the syntax for Update Service 2024-06-01-preview version.

```rest
PATCH https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Search/searchServices/mysearchservice?api-version=2024-06-01-preview

{
  "tags": {
    "app-name": "My e-commerce app",
    "new-tag": "Adding a new tag"
  },
  "properties": {
    "replicaCount": 2
  }
}
```

## See also

+ [Quickstart: REST APIs](search-get-started-rest.md)
+ [Search REST API overview](/rest/api/searchservice/)
+ [Search REST API versions](/rest/api/searchservice/search-service-api-versions)
+ [Manage using the REST APIs](search-manage-rest.md)
+ [Management REST API overview](/rest/api/searchmanagement/)
+ [Management REST API versions](/rest/api/searchmanagement/management-api-versions)
