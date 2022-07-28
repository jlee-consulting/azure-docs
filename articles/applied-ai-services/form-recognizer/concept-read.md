---
title: Read OCR - Form Recognizer
titleSuffix: Azure Applied AI Services
description: Learn concepts related to Read OCR API analysis with Form Recognizer API—usage and limits.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/06/2022
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
---

# Form Recognizer Read OCR model

Form Recognizer v3.0 preview includes the new Read Optical Character Recognition (OCR) model. The Read OCR model extracts typeface and handwritten text including mixed languages in documents. The Read OCR model can detect lines, words, locations, and languages and is the core of all other Form Recognizer models. Layout, general document, custom, and prebuilt models all use the Read OCR model as a foundation for extracting texts from documents.

## Supported document types

| **Model**   | **Images**   | **PDF**  | **TIFF** | **Word**   | **Excel**  | **PowerPoint** | **HTML** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Read  | ✓  | ✓  | ✓  | ✓  | ✓  | ✓  | ✓  |

### Data extraction

| **Read model**   | **Text**   | **[Language detection](language-support.md#detected-languages-read-api)** |
| --- | --- | --- |
prebuilt-read  | ✓  |✓  |

## Development options

The following resources are supported by Form Recognizer v3.0:

| Feature | Resources | Model ID |
|----------|------------|------------|
|**Read model**| <ul><li>[**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST API**](how-to-guides/use-prebuilt-read.md?pivots=programming-language-rest-api)</li><li>[**C# SDK**](how-to-guides/use-prebuilt-read.md?pivots=programming-language-csharp)</li><li>[**Python SDK**](how-to-guides/use-prebuilt-read.md?pivots=programming-language-python)</li><li>[**Java SDK**](how-to-guides/use-prebuilt-read.md?pivots=programming-language-java)</li><li>[**JavaScript**](how-to-guides/use-prebuilt-read.md?pivots=programming-language-javascript)</li></ul>|**prebuilt-read**|

## Try Form Recognizer

Try extracting text from forms and documents using the Form Recognizer Studio. You'll need the following assets:

* An Azure subscription—you can [create one for free](https://azure.microsoft.com/free/cognitive-services/)

* A [Form Recognizer instance](https://portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) in the Azure portal. You can use the free pricing tier (`F0`) to try the service. After your resource deploys, select **Go to resource** to get your key and endpoint.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Screenshot: keys and endpoint location in the Azure portal.":::

### Form Recognizer Studio (preview)

> [!NOTE]
> Currently, Form Recognizer Studio doesn't support Microsoft Word, Excel, PowerPoint, and HTML file formats in the Read preview.

***Sample form processed with [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/read)***

:::image type="content" source="media/studio/form-recognizer-studio-read-v3p2-updated.png" alt-text="Screenshot: Read processing in Form Recognizer Studio.":::

1. On the Form Recognizer Studio home page, select **Read**

1. You can analyze the sample document or select the **+ Add** button to upload your own sample.

1. Select the **Analyze** button:

    :::image type="content" source="media/studio/form-recognizer-studio-read-analyze-v3p2-updated.png" alt-text="Screenshot: analyze read menu.":::

   > [!div class="nextstepaction"]
   > [Try Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/layout)

## Input requirements

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## Supported languages and locales

Form Recognizer preview version supports several languages for the read model. *See* our [Language Support](language-support.md) for a complete list of supported handwritten and printed languages.

## Data detection and extraction

### Pages

With the added support for Microsoft Word, Excel, PowerPoint, and HTML files, the page units in the model output are computed as shown:

 **File format**   | **Computed page unit**   | **Total pages**  |
| --- | --- | --- |
|Images | Each image = 1 page unit | Total images  |
|PDF | Each page in the PDF = 1 page unit | Total pages in the PDF |
|Word | Up to 3,000 characters = 1 page unit, Each embedded image = 1 page unit | Total pages of up to 3,000 characters each + Total embedded images |
|Excel | Each worksheet = 1 page unit, Each embedded image = 1 page unit | Total worksheets + Total images
|PowerPoint|  Each slide = 1 page unit, Each embedded image = 1 page unit | Total slides + Total images
|HTML| Up to 3,000 characters = 1 page unit, embedded or linked images not supported | Total pages of up to 3,000 characters each |

### Text lines and words

Read extracts print and handwritten style text as `lines` and `words`. The model outputs bounding `polygon` coordinates and `confidence` for the extracted words. The `styles` collection includes any handwritten style for lines if detected along with the spans pointing to the associated text. This feature applies to [supported handwritten languages](language-support.md).

For Microsoft Word, Excel, PowerPoint, and HTML file formats, Read will extract all embedded text as is. For any embedded images, it will run OCR on the images to extract text and append the text from each image as an added entry to the `pages` collection. These added entries will include the extracted text lines and words, their bounding polygons, confidences, and the spans pointing to the associated text.

### Language detection

Read adds [language detection](language-support.md#detected-languages-read-api) as a new feature for text lines. Read will predict all detected languages for text lines along with the `confidence` in the `languages` collection under `analyzeResult`.

### Select page (s) for text extraction

For large multi-page PDF documents, use the `pages` query parameter to indicate specific page numbers or page ranges for text extraction.

> [!NOTE]
> For Microsoft Word, Excel, PowerPoint, and HTML file formats, the Read API ignores the pages parameter and extracts all pages by default.

## Next steps

Complete a Form Recognizer quickstart:

> [!div class="checklist"]
>
> * [**REST API**](how-to-guides/use-prebuilt-read.md?pivots=programming-language-rest-api)
> * [**C# SDK**](how-to-guides/use-prebuilt-read.md?pivots=programming-language-csharp)
> * [**Python SDK**](how-to-guides/use-prebuilt-read.md?pivots=programming-language-python)
> * [**Java SDK**](how-to-guides/use-prebuilt-read.md?pivots=programming-language-java)
> * [**JavaScript**](how-to-guides/use-prebuilt-read.md?pivots=programming-language-javascript)</li></ul>

Explore our REST API:

> [!div class="nextstepaction"]
> [Form Recognizer API v3.0](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-2022-06-30-preview/operations/AnalyzeDocument)
