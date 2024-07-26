---
title: Custom neural document model - Document Intelligence (formerly Form Recognizer)
titleSuffix: Azure AI services
description: Use the custom neural document model to train a model to extract data from structured, semistructured, and unstructured documents.
author: laujan
manager: nitinme
ms.service: azure-ai-document-intelligence
ms.topic: conceptual
ms.date: 05/23/2024
ms.author: lajanuar
ms.custom:
  - references_regions
  - ignite-2023
monikerRange: '>=doc-intel-3.0.0'
---


# Document Intelligence custom neural model

::: moniker range="doc-intel-4.0.0"
[!INCLUDE [preview-version-notice](includes/preview-notice.md)]

**This content applies to:**![checkmark](media/yes-icon.png) **v4.0 (preview)** | **Previous versions:** ![blue-checkmark](media/blue-yes-icon.png) [**v3.1 (GA)**](?view=doc-intel-3.1.0&preserve-view=tru) ![blue-checkmark](media/blue-yes-icon.png) [**v3.0 (GA)**](?view=doc-intel-3.0.0&preserve-view=tru)
::: moniker-end

::: moniker range="doc-intel-3.1.0"
**This content applies to:** ![checkmark](media/yes-icon.png) **v3.1 (GA)** | **Latest version:** ![purple-checkmark](media/purple-yes-icon.png) [**v4.0 (preview)**](?view=doc-intel-4.0.0&preserve-view=true) | **Previous versions:** ![blue-checkmark](media/blue-yes-icon.png) [**v3.0**](?view=doc-intel-3.0.0&preserve-view=true)
::: moniker-end

::: moniker range="doc-intel-3.0.0"
**This content applies to:** ![checkmark](media/yes-icon.png) **v3.0 (GA)** | **Latest versions:** ![purple-checkmark](media/purple-yes-icon.png) [**v4.0 (preview)**](?view=doc-intel-4.0.0&preserve-view=true) ![purple-checkmark](media/purple-yes-icon.png) [**v3.1**](?view=doc-intel-3.1.0&preserve-view=true)
::: moniker-end

Custom neural document models or neural models are a deep learned model type that combines layout and language features to accurately extract labeled fields from documents. The base custom neural model is trained on various document types that makes it suitable to be trained for extracting fields from structured, semi-structured, and unstructured documents. Custom neural models are available in the [v3.0 and later models](v3-1-migration-guide.md) The table below lists common document types for each category:

| Documents | Examples |
|---|--|
| Structured| surveys, questionnaires|
| Semi-structured | invoices, purchase orders |
| Unstructured | contracts, letters |

Custom neural models share the same labeling format and strategy as [custom template](concept-custom-template.md) models. Currently custom neural models only support a subset of the field types supported by custom template models.

## Model capabilities

 > [!IMPORTANT]
 > Starting with API version ```2024-02-29-preview``` custom neural models add support for overlapping fields and table cell confidence.

Custom neural models currently only support key-value pairs and selection marks and structured fields (tables), future releases include support for signatures.

| Form fields | Selection marks | Tabular fields | Signature | Region | Overlapping fields |
|:--:|:--:|:--:|:--:|:--:|:--:|
| Supported | Supported | Supported | Unsupported | Supported <sup>1</sup> | Supported <sup>2</sup> |

<sup>1</sup> Region labels in custom neural models use the results from the Layout API for specified region. This feature is different from template models where, if no value is present, text is generated at training time.
<sup>2</sup> Overlapping fields are supported starting with REST API version `2024-02-29-preview`. Overlapping fields have some limits. For more information, *see* [overlapping fields](#overlapping-fields).

### Build mode

The build custom model operation supports *template* and *neural* custom models. Previous versions of the REST API and client libraries only supported a single build mode that is now known as the *template* mode.

Neural models support documents that have the same information, but different page structures. Examples of these documents include United States W2 forms, which share the same information, but can vary in appearance across companies. For more information, *see* [Custom model build mode](concept-custom.md#build-mode).

## Supported languages and locales

*See* our [Language Support—custom models](language-support-custom.md) page for a complete list of supported languages.

## Overlapping fields

With the release of API versions **2024-02-29-preview** and  later, custom neural models will support overlapping fields:

To use the overlapping fields, your dataset needs to contain at least one sample with the expected overlap. To label an overlap, use **region labeling** to designate each of the spans of content (with the overlap) for each field. Labeling an overlap with field selection (highlighting a value) will fail in the studio as region labeling is the only supported labeling tool for indicating field overlaps. Overlap support includes:

* Complete overlap. The same set of tokens are labeled for two different fields.
* Partial overlap. Some tokens belong to both fields, but there are tokens that are only part of one field or the other.

Overlapping fields have some limits:

* Any token or word can only be labeled as two fields.
* overlapping fields in a table can't span table rows.
* Overlapping fields can only be recognized if at least one sample in the dataset contains overlapping labels for those fields.

To use overlapping fields, label your dataset with the overlaps and train the model with the API version ```2024-02-29-preview``` or later.

## Tabular fields adds table, row and cell confidence

With the release of API versions **2022-06-30-preview** and  later, custom neural models will support tabular fields (tables):

* Models trained with API version 2022-08-31, or later will accept tabular field labels.
* Documents analyzed with custom neural models using API version 2022-06-30-preview or later will produce tabular fields aggregated across the tables.
* The results can be found in the ```analyzeResult``` object's ```documents``` array that is returned following an analysis operation.

Tabular fields support **cross page tables** by default:

* To label a table that spans multiple pages, label each row of the table across the different pages in a single table.
* As a best practice, ensure that your dataset contains a few samples of the expected variations. For example, include samples where the entire table is on a single page and where tables span two or more pages.

Tabular fields are also useful when extracting repeating information within a document that isn't recognized as a table. For example, a repeating section of work experiences in a resume can be labeled and extracted as a tabular field.

Tabular fields provide **table, row and cell confidence** starting with the ```2024-02-29-preview``` API:

* Fixed or dynamic tables add confidence support for the following elements:

  * Table confidence, a measure of how accurately the entire table is recognized.
  * Row confidence, a measure of recognition of an individual row.
  * Cell confidence, a measure of recognition of an individual cell.

* The recommended approach is to review the accuracy in a top-down manner starting with the table first, followed by the row and then the cell.

See  [confidence and accuracy scores](concept-accuracy-confidence.md) to learn more about table, row, and cell confidence.


## Supported regions

As of October 18, 2022, Document Intelligence custom neural model training will only be available in the following Azure regions until further notice:

* Australia East
* Brazil South
* Canada Central
* Central India
* Central US
* East Asia
* East US
* East US2
* France Central
* Japan East
* South Central US
* Southeast Asia
* UK South
* West Europe
* West US2
* US Gov Arizona
* US Gov Virginia

:::moniker range="doc-intel-4.0.0"

> [!TIP]
> You can [copy a model](disaster-recovery.md#copy-api-overview) trained in one of the select regions listed to **any other region** and use it accordingly.
>
> Use the [**REST API**](/rest/api/aiservices/operation-groups?view=rest-aiservices-2024-02-29-preview&preserve-view=true) or [**Document Intelligence Studio**](https://formrecognizer.appliedai.azure.com/studio/custommodel/projects) to copy a model to another region.

:::moniker-end

:::moniker range="doc-intel-3.1.0"

> [!TIP]
> You can [copy a model](disaster-recovery.md#copy-api-overview) trained in one of the select regions listed to **any other region** and use it accordingly.
>
> Use the [**REST API**](/rest/api/aiservices/document-models/copy-model-to?view=rest-aiservices-2023-07-31&preserve-view=true&tabs=HTTP) or [**Document Intelligence Studio**](https://formrecognizer.appliedai.azure.com/studio/custommodel/projects) to copy a model to another region.

:::moniker-end

:::moniker range="doc-intel-3.0.0"

> [!TIP]
> You can [copy a model](disaster-recovery.md#copy-api-overview) trained in one of the select regions listed to **any other region** and use it accordingly.
>
> Use the [**REST API**](/rest/api/aiservices/document-models/copy-model-to?view=rest-aiservices-v3.0%20(2022-08-31)&preserve-view=true&tabs=HTTP) or [**Document Intelligence Studio**](https://formrecognizer.appliedai.azure.com/studio/custommodel/projects) to copy a model to another region.

:::moniker-end

## Input requirements

* For best results, provide one clear photo or high-quality scan per document.

* Supported file formats:

    |Model | PDF |Image: </br>jpeg/jpg, png, bmp, tiff, heif | Microsoft Office: </br> Word (docx), Excel (xlsx), PowerPoint (pptx), and HTML|
    |--------|:----:|:-----:|:---------------:|
    |Read            | ✔    | ✔    | ✔  |
    |Layout          | ✔  | ✔ | ✔ (2024-02-29-preview, 2023-10-31-preview, or later)  |
    |General&nbsp;Document| ✔  | ✔ |   |
    |Prebuilt        |  ✔  | ✔ |   |
    |Custom neural   |  ✔  | ✔ |   |

    &#x2731; Microsoft Office files are currently not supported for other models or versions.

* For PDF and TIFF, up to 2,000 pages can be processed (with a free tier subscription, only the first two pages are processed).

* The file size for analyzing documents is 500 MB for paid (S0) tier and 4 MB for free (F0) tier.

* Image dimensions must be between 50 x 50 pixels and 10,000 px x 10,000 pixels.

* If your PDFs are password-locked, you must remove the lock before submission.

* The minimum height of the text to be extracted is 12 pixels for a 1024 x 768 pixel image. This dimension corresponds to about `8`-point text at 150 dots per inch.

* For custom model training, the maximum number of pages for training data is 500 for the custom template model and 50,000 for the custom neural model.

* For custom extraction model training, the total size of training data is 50 MB for template model and 1G-MB for the neural model.

* For custom classification model training, the total size of training data is `1GB`  with a maximum of 10,000 pages.

## Best practices

Custom neural models differ from custom template models in a few different ways. The custom template or model relies on a consistent visual template to extract the labeled data. Custom neural models support structured, semi-structured, and unstructured documents to extract fields. When you're choosing between the two model types, start with a neural model, and test to determine if it supports your functional needs.

### Dealing with variations

Custom neural models can generalize across different formats of a single document type. As a best practice, create a single model for all variations of a document type. Add at least five labeled samples for each of the different variations to the training dataset.

### Field naming

When you label the data, labeling the field relevant to the value improves the accuracy of the key-value pairs extracted. For example, for a field value containing the supplier ID, consider naming the field *supplier_id*. Field names should be in the language of the document.

### Labeling contiguous values

Value tokens/words of one field must be either:

* In a consecutive sequence in natural reading order, without interleaving with other fields
* In a region that don't cover any other fields

### Representative data

Values in training cases should be diverse and representative. For example, if a field is named *date*, values for this field should be a date. Synthetic value like a random string can affect model performance.

## Current Limitations

* Custom neural model doesn't recognize values split across page boundaries.
* Custom neural unsupported field types are ignored if a dataset labeled for custom template models is used to train a custom neural model.
* Custom neural models are limited to 20 build operations per month. Open a support request if you need the limit increased. For more information, see [Document Intelligence service quotas and limits](service-limits.md).

## Training a model

Custom neural models are available in the [v3.0 and later models](v3-1-migration-guide.md).

| Document Type | REST API | SDK | Label and Test Models|
|--|--|--|--|
| Custom document | [Document Intelligence 3.1](/rest/api/aiservices/document-models/analyze-document?view=rest-aiservices-2023-07-31&preserve-view=true&tabs=HTTP)| [Document Intelligence SDK](quickstarts/get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true)| [Document Intelligence Studio](https://formrecognizer.appliedai.azure.com/studio)

The build operation to train model supports a new ```buildMode``` property, to train a custom neural model, set the ```buildMode``` to ```neural```.

:::moniker range="doc-intel-4.0.0"

```REST
https://{endpoint}/documentintelligence/documentModels:build?api-version=2024-02-29-preview

{
  "modelId": "string",
  "description": "string",
  "buildMode": "neural",
  "azureBlobSource":
  {
    "containerUrl": "string",
    "prefix": "string"
  }
}
```

:::moniker-end

:::moniker range="doc-intel-3.1.0"

```REST
https://{endpoint}/formrecognizer/documentModels:build?api-version=v3.1:2023-07-31

{
  "modelId": "string",
  "description": "string",
  "buildMode": "neural",
  "azureBlobSource":
  {
    "containerUrl": "string",
    "prefix": "string"
  }
}
```

:::moniker-end

:::moniker range="doc-intel-3.0.0"

```REST
https://{endpoint}/formrecognizer/documentModels/{modelId}:copyTo?api-version=2022-08-31

{
  "modelId": "string",
  "description": "string",
  "buildMode": "neural",
  "azureBlobSource":
  {
    "containerUrl": "string",
    "prefix": "string"
  }
}
```

:::moniker-end

## Next steps

Learn to create and compose custom models:

> [!div class="nextstepaction"]
> [**Build a custom model**](how-to-guides/build-a-custom-model.md)
> [**Compose custom models**](how-to-guides/compose-custom-models.md)
