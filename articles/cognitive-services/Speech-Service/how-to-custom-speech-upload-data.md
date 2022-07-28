---
title: "Upload training and testing datasets for Custom Speech - Speech service"
titleSuffix: Azure Cognitive Services
description: Learn about how to upload data to test or train a Custom Speech model.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 05/08/2022
ms.author: eur
zone_pivot_groups: speech-studio-cli-rest
---

# Upload training and testing datasets for Custom Speech 

You need audio or text data for testing the accuracy of Microsoft speech recognition or training your custom models. For information about the data types supported for testing or training your model, see [Training and testing datasets](how-to-custom-speech-test-and-train.md).

> [!TIP]
> You can also use the [online transcription editor](how-to-custom-speech-transcription-editor.md) to create and refine labeled audio datasets.

## Upload datasets

::: zone pivot="speech-studio"

To upload your own datasets in Speech Studio, follow these steps:

1. Sign in to the [Speech Studio](https://aka.ms/speechstudio/customspeech). 
1. Select **Custom Speech** > Your project name > **Speech datasets** > **Upload data**.
1. Select the **Training data** or **Testing data** tab.
1. Select a dataset type, and then select **Next**.
1. Specify the dataset location, and then select **Next**. You can choose a local file or enter a remote location such as Azure Blob public access URL.
1. Enter the dataset name and description, and then select **Next**.
1. Review your settings, and then select **Save and close**.

After your dataset is uploaded, go to the **Train custom models** page to [train a custom model](how-to-custom-speech-train-model.md)

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Speech-studio&Pillar=Speech&Product=Custom-speech&Page=Upload-training-and-testing-datasets&Section=Upload-datasets" target="_target">I ran into an issue</a>

::: zone-end

::: zone pivot="speech-cli"

[!INCLUDE [Map CLI and API kind to Speech Studio options](includes/how-to/custom-speech/cli-api-kind.md)]

To create a dataset and connect it to an existing project, use the `spx csr dataset create` command. Construct the request parameters according to the following instructions:

- Set the `project` parameter to the ID of an existing project. This is recommended so that you can also view and manage the dataset in Speech Studio. You can run the `spx csr project list` command to get available projects.
- Set the required `kind` parameter. The possible set of values for dataset kind are: Language, Acoustic, Pronunciation, and AudioFiles.
- Set the required `contentUrl` parameter. This is the location of the dataset.
- Set the required `language` parameter. The dataset locale must match the locale of the project. The locale can't be changed later. The Speech CLI `language` parameter corresponds to the `locale` property in the JSON request and response.
- Set the required `name` parameter. This is the name that will be displayed in the Speech Studio. The Speech CLI `name` parameter corresponds to the `displayName` property in the JSON request and response.

Here's an example Speech CLI command that creates a dataset and connects it to an existing project:

```azurecli-interactive
spx csr dataset create --kind "Acoustic" --name "My Acoustic Dataset" --description "My Acoustic Dataset Description" --project YourProjectId --content YourContentUrl --language "en-US"
```

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=CLI&Pillar=Speech&Product=Custom-speech&Page=Upload-training-and-testing-datasets&Section=Upload-datasets" target="_target">I ran into an issue</a>

You should receive a response body in the following format:

```json
{
  "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.0/datasets/e0ea620b-e8c3-4a26-acb2-95fd0cbc625c",
  "kind": "Acoustic",
  "contentUrl": "https://contoso.com/mydatasetlocation",
  "links": {
    "files": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.0/datasets/e0ea620b-e8c3-4a26-acb2-95fd0cbc625c/files"
  },
  "project": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.0/projects/70ccbffc-cafb-4301-aa9f-ef658559d96e"
  },
  "properties": {
    "acceptedLineCount": 0,
    "rejectedLineCount": 0
  },
  "lastActionDateTime": "2022-05-20T14:07:11Z",
  "status": "NotStarted",
  "createdDateTime": "2022-05-20T14:07:11Z",
  "locale": "en-US",
  "displayName": "My Acoustic Dataset",
  "description": "My Acoustic Dataset Description"
}
```

The top-level `self` property in the response body is the dataset's URI. Use this URI to get details about the dataset's project and files. You also use this URI to update or delete a dataset.

For Speech CLI help with datasets, run the following command:

```azurecli-interactive
spx help csr dataset
```

::: zone-end

::: zone pivot="rest-api"

[!INCLUDE [Map CLI and API kind to Speech Studio options](includes/how-to/custom-speech/cli-api-kind.md)]

To create a dataset and connect it to an existing project, use the [CreateDataset](https://eastus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateDataset) operation of the [Speech-to-text REST API v3.0](rest-speech-to-text.md). Construct the request body according to the following instructions:

- Set the `project` property to the URI of an existing project. This is recommended so that you can also view and manage the dataset in Speech Studio. You can make a [GetProjects](https://eastus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetProjects) request to get available projects.
- Set the required `kind` property. The possible set of values for dataset kind are: Language, Acoustic, Pronunciation, and AudioFiles.
- Set the required `contentUrl` property. This is the location of the dataset.
- Set the required `locale` property. The dataset locale must match the locale of the project. The locale can't be changed later. 
- Set the required `displayName` property. This is the name that will be displayed in the Speech Studio.

Make an HTTP POST request using the URI as shown in the following example. Replace `YourSubscriptionKey` with your Speech resource key, replace `YourServiceRegion` with your Speech resource region, and set the request body properties as previously described.

```azurecli-interactive
curl -v -X POST -H "Ocp-Apim-Subscription-Key: YourSubscriptionKey" -H "Content-Type: application/json" -d '{
  "kind": "Acoustic",
  "displayName": "My Acoustic Dataset",
  "description": "My Acoustic Dataset Description",
  "project": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.0/projects/70ccbffc-cafb-4301-aa9f-ef658559d96e"
  },
  "contentUrl": "https://contoso.com/mydatasetlocation",
  "locale": "en-US",
}'  "https://YourServiceRegion.api.cognitive.microsoft.com/speechtotext/v3.0/datasets"
```

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=REST&Pillar=Speech&Product=Custom-speech&Page=Upload-training-and-testing-datasets&Section=Upload-datasets" target="_target">I ran into an issue</a>

You should receive a response body in the following format:

```json
{
  "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.0/datasets/e0ea620b-e8c3-4a26-acb2-95fd0cbc625c",
  "kind": "Acoustic",
  "contentUrl": "https://contoso.com/mydatasetlocation",
  "links": {
    "files": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.0/datasets/e0ea620b-e8c3-4a26-acb2-95fd0cbc625c/files"
  },
  "project": {
    "self": "https://eastus.api.cognitive.microsoft.com/speechtotext/v3.0/projects/70ccbffc-cafb-4301-aa9f-ef658559d96e"
  },
  "properties": {
    "acceptedLineCount": 0,
    "rejectedLineCount": 0
  },
  "lastActionDateTime": "2022-05-20T14:07:11Z",
  "status": "NotStarted",
  "createdDateTime": "2022-05-20T14:07:11Z",
  "locale": "en-US",
  "displayName": "My Acoustic Dataset",
  "description": "My Acoustic Dataset Description"
}
```

The top-level `self` property in the response body is the dataset's URI. Use this URI to [get](https://eastus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetDataset) details about the dataset's project and files. You also use this URI to [update](https://eastus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/UpdateDataset) or [delete](https://eastus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteDataset) the dataset.

::: zone-end

> [!IMPORTANT] 
> Connecting a dataset to a Custom Speech project isn't required to train and test a custom model using the REST API or Speech CLI. But if the dataset is not connected to any project, you can't select it for training or testing in the [Speech Studio](https://aka.ms/speechstudio/customspeech). 

## Next steps

* [Test recognition quality](how-to-custom-speech-inspect-data.md)
* [Test model quantitatively](how-to-custom-speech-evaluate-data.md)
* [Train a custom model](how-to-custom-speech-train-model.md)
