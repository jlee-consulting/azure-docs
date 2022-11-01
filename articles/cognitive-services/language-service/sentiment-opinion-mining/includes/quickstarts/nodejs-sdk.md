---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 10/21/2022
ms.author: aahi
ms.custom: devx-track-js, ignite-fall-2021
---

[Reference documentation](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-latest) | [Additional samples](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/textanalytics/ai-text-analytics/samples) | [Package (npm)](https://www.npmjs.com/package/@azure/ai-text-analytics/v/5.1.0) | [Library source code](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)

Use this quickstart to create a sentiment analysis application with the client library for Node.js. In the following example, you will create a JavaScript application that can identify the sentiment(s) expressed in a text sample, and perform aspect-based sentiment analysis.

## Prerequisites

* Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services)
* [Node.js](https://nodejs.org/) v14 LTS or later

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=JAVASCRIPT&Pillar=Language&Product=Sentiment-analysis&Page=quickstart&Section=Prerequisites" target="_target">I ran into an issue</a>

## Setting up

[!INCLUDE [Create an Azure resource](../../../includes/create-resource.md)]

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=JAVASCRIPT&Pillar=Language&Product=Sentiment-analysis&Page=quickstart&Section=Create-an-azure-resource" target="_target">I ran into an issue</a>

[!INCLUDE [Get your key and endpoint](../../../includes/get-key-endpoint.md)]

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=JAVASCRIPT&Pillar=Language&Product=Sentiment-analysis&Page=quickstart&Section=Get-your-key-and-endpoint" target="_target">I ran into an issue</a>

[!INCLUDE [Create environment variables](../../../includes/environment-variables.md)]

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=JAVASCRIPT&Pillar=Language&Product=Sentiment-analysis&Page=quickstart&Section=Create-environment-variables" target="_target">I ran into an issue</a>

### Create a new Node.js application

In a console window (such as cmd, PowerShell, or Bash), create a new directory for your app, and navigate to it. 

```console
mkdir myapp 

cd myapp
```

Run the `npm init` command to create a node application with a `package.json` file. 

```console
npm init
```

### Install the client library

Install the npm packages:

```console
npm install @azure/ai-text-analytics@5.1.0
```

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=JAVASCRIPT&Pillar=Language&Product=Sentiment-analysis&Page=quickstart&Section=Set-up-the-environment" target="_target">I ran into an issue</a>

## Code example

Open the file and copy the below code. Remember to replace the `key` variable with the key for your resource, and replace the `endpoint` variable with the endpoint for your resource. 

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");

// This example requires environment variables named "LANGUAGE_KEY" and "LANGUAGE_ENDPOINT"
const languageKey = process.env.LANGUAGE_KEY;
const languageEndpoint = process.env.LANGUAGE_ENDPOINT;

// Authenticate the client with your key and endpoint.
const textAnalyticsClient = new TextAnalyticsClient(languageEndpoint,  new AzureKeyCredential(languageKey));

// Example method for detecting sentiment and opinions in text.
async function sentimentAnalysisWithOpinionMining(client){

  const sentimentInput = [
    {
      text: "The food and service were unacceptable. The concierge was nice, however.",
      id: "0",
      language: "en"
    }
  ];
  const results = await client.analyzeSentiment(sentimentInput, { includeOpinionMining: true });

  for (let i = 0; i < results.length; i++) {
    const result = results[i];
    console.log(`- Document ${result.id}`);
    if (!result.error) {
      console.log(`\tDocument text: ${sentimentInput[i].text}`);
      console.log(`\tOverall Sentiment: ${result.sentiment}`);
      console.log("\tSentiment confidence scores:", result.confidenceScores);
      console.log("\tSentences");
      for (const { sentiment, confidenceScores, opinions } of result.sentences) {
        console.log(`\t- Sentence sentiment: ${sentiment}`);
        console.log("\t  Confidence scores:", confidenceScores);
        console.log("\t  Mined opinions");
        for (const { target, assessments } of opinions) {
          console.log(`\t\t- Target text: ${target.text}`);
          console.log(`\t\t  Target sentiment: ${target.sentiment}`);
          console.log("\t\t  Target confidence scores:", target.confidenceScores);
          console.log("\t\t  Target assessments");
          for (const { text, sentiment } of assessments) {
            console.log(`\t\t\t- Text: ${text}`);
            console.log(`\t\t\t  Sentiment: ${sentiment}`);
          }
        }
      }
    } else {
      console.error(`\tError: ${result.error}`);
    }
  }
}
sentimentAnalysisWithOpinionMining(textAnalyticsClient);
```

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=JAVASCRIPT&Pillar=Language&Product=Sentiment-analysis&Page=quickstart&Section=Code-example" target="_target">I ran into an issue</a>

## Output

```console
- Document 0
  Document text: The food and service were unacceptable. The concierge was nice, however.
  Overall Sentiment: mixed
  Sentiment confidence scores: { positive: 0.47, neutral: 0, negative: 0.52 }
  Sentences
  - Sentence sentiment: negative
    Confidence scores: { positive: 0, neutral: 0, negative: 0.99 }
    Mined opinions
          - Target text: food
            Target sentiment: negative
            Target confidence scores: { positive: 0, negative: 1 }
            Target assessments
                  - Text: unacceptable
                    Sentiment: negative
          - Target text: service
            Target sentiment: negative
            Target confidence scores: { positive: 0, negative: 1 }
            Target assessments
                  - Text: unacceptable
                    Sentiment: negative
  - Sentence sentiment: positive
    Confidence scores: { positive: 0.94, neutral: 0.01, negative: 0.05 }
    Mined opinions
          - Target text: concierge
            Target sentiment: positive
            Target confidence scores: { positive: 1, negative: 0 }
            Target assessments
                  - Text: nice
                    Sentiment: positive
```

[!INCLUDE [clean up resources](../../../includes/clean-up-resources.md)]

[!INCLUDE [clean up environment variables](../../../includes/clean-up-variables.md)]

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=REST API&Pillar=Language&Product=Entity-linking&Page=quickstart&Section=Clean-up-resources" target="_target">I ran into an issue</a>

## Next steps

* [Sentiment analysis and opinion mining language support](../../language-support.md)
* [How to call the API](../../how-to/call-api.md)  
* [Reference documentation](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-latest)
* [Additional samples](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/textanalytics/ai-text-analytics/samples)
