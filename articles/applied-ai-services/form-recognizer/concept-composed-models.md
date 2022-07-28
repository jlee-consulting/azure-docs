---
title: Form Recognizer composed models
titleSuffix: Azure Applied AI Services
description: Learn about composed custom models
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/06/2022
ms.author: lajanuar
recommendations: false
---

# Composed custom models

**Composed models**. A composed model is created by taking a collection of custom models and assigning them to a single model built from your form types. When a document is submitted for analysis using a composed model, the service performs a classification to decide which custom model best represents the submitted document.

With composed models, you can assign multiple custom models to a composed model called with a single model ID. It's useful when you've trained several models and want to group them to analyze similar form types. For example, your composed model might include custom models trained to analyze your supply, equipment, and furniture purchase orders. Instead of manually trying to select the appropriate model, you can use a composed model to determine the appropriate custom model for each analysis and extraction.

* ```Custom form```and ```Custom document``` models can be composed together into a single composed model when they're trained with the same API version or an API version later than ```2021-06-30-preview```. For more information on composing custom template and custom neural models, see [compose model limits](#compose-model-limits).
* With the model compose operation, you can assign up to 100 trained custom models to a single composed model. To analyze a document with a composed model, Form Recognizer first classifies the submitted form, chooses the best-matching assigned model, and returns results.
* For **_custom template models_**, the composed model can be created using variations of a custom template or different form types. This operation is useful when incoming forms may belong to one of several templates.
* The response will include a ```docType``` property to indicate which of the composed models was used to analyze the document.

## Compose model limits

> [!NOTE]
> With the addition of **_custom neural model_** , there are a few limits to the compatibility of models that can be composed together.

### Composed model compatibility

 |Custom model type | API Version |Custom form 2021-06-30-preview (v3.0)| Custom document 2021-06-30-preview(v3.0) | Custom form GA version (v2.1) or earlier|
|--|--|--|--|--|
|**Custom template** (updated custom form)| 2021-06-30-preview |  &#10033;| ✓ | X |
|**Custom neural**| trained with current API version (2021-06-30-preview) |✓ |✓ | X |
|**Custom form**| Custom form GA version (v2.1) or earlier | X | X|  ✓|

**Table symbols**: ✔—supported; **X—not supported; ✱—unsupported for this API version, but will be supported in a future API version.

* To compose a model trained with a prior version of the API (v2.1 or earlier), train a model with the v3.0 API using the same labeled dataset. That addition will ensure that the v2.1 model can be composed with other models.

* Models composed with v2.1 of the API will continue to be supported, requiring no updates.

* The limit for maximum number of custom models that can be composed is 100.

## Development options

The following resources are supported by Form Recognizer **v3.0** (preview):

| Feature | Resources |
|----------|-------------|
|_**Custom model**_| <ul><li>[Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/custommodel/projects)</li><li>[REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-2022-06-30-preview/operations/AnalyzeDocument)</li><li>[C# SDK](quickstarts/try-v3-csharp-sdk.md)</li><li>[Java SDK](quickstarts/try-v3-java-sdk.md)</li><li>[JavaScript SDK](quickstarts/try-v3-javascript-sdk.md)</li><li>[Python SDK](quickstarts/try-v3-python-sdk.md)</li></ul>|
| _**Composed model**_| <ul><li>[Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/custommodel/projects)</li><li>[REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-2022-06-30-preview/operations/ComposeDocumentModel)</li><li>[C# SDK](/dotnet/api/azure.ai.formrecognizer.documentanalysis.documentmodeladministrationclient.startcreatecomposedmodel?view=azure-dotnet-preview&preserve-view=true)</li><li>[Java SDK](/java/api/com.azure.ai.formrecognizer.administration.documentmodeladministrationclient.begincreatecomposedmodel?view=azure-java-preview&preserve-view=true)</li><li>[JavaScript SDK](/javascript/api/@azure/ai-form-recognizer/documentmodeladministrationclient?view=azure-node-preview#@azure-ai-form-recognizer-documentmodeladministrationclient-begincomposemodel&preserve-view=true)</li><li>[Python SDK](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formtrainingclient?view=azure-python-preview#azure-ai-formrecognizer-formtrainingclient-begin-create-composed-model&preserve-view=true)</li></ul>|

The following resources are supported by Form Recognizer v2.1:

| Feature | Resources |
|----------|-------------------------|
|_**Custom model**_| <ul><li>[Form Recognizer labeling tool](https://fott-2-1.azurewebsites.net)</li><li>[REST API](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-forms-with-a-custom-model)</li><li>[Client library SDK](quickstarts/try-sdk-rest-api.md)</li><li>[Form Recognizer Docker container](containers/form-recognizer-container-install-run.md?tabs=custom#run-the-container-with-the-docker-compose-up-command)</li></ul>|
| _**Composed model**_ |<ul><li>[Form Recognizer labeling tool](https://fott-2-1.azurewebsites.net/)</li><li>[REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/Compose)</li><li>[C# SDK](/dotnet/api/azure.ai.formrecognizer.training.createcomposedmodeloperation?view=azure-dotnet&preserve-view=true)</li><li>[Java SDK](/java/api/com.azure.ai.formrecognizer.models.createcomposedmodeloptions?view=azure-java-stable&preserve-view=true)</li><li>[JavaScript SDK](/javascript/api/@azure/ai-form-recognizer/begincreatecomposedmodeloptions?view=azure-node-latest&preserve-view=true)</li><li>[Python SDK](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formtrainingclient?view=azure-python#azure-ai-formrecognizer-formtrainingclient-begin-create-composed-model&preserve-view=true)</li></ul>|


## Next steps

Learn to create and compose custom models:

> [!div class="nextstepaction"]
> [**Form Recognizer v2.1 (GA)**](compose-custom-models.md)
