---
title: 'How to generate embeddings with Azure OpenAI'
titleSuffix: Azure OpenAI
description: Learn how to generate embeddings with Azure OpenAI
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: openai
ms.topic: how-to
ms.date: 6/24/2022
author: ChrisHMSFT
ms.author: chrhoder
recommendations: false
keywords: 

---
# Learn how to generate embeddings with Azure OpenAI

An embedding is a special format of data representation that can be easily utilized by machine learning models and algorithms. The embedding is an information dense representation of the semantic meaning of a piece of text. Each embedding is a vector of floating point numbers, such that the distance between two embeddings in the vector space is correlated with semantic similarity between two inputs in the original format. For example, if two texts are similar, then their vector representations should also be similar.

## How to get embeddings

To obtain an embedding vector for a piece of text we make a request to the embeddings endpoint as shown in the following code snippets:

```console
curl https://YOUR_RESOURCE_NAME.openaiazure.com/openai/deployments/YOUR_DEPLOYMENT_NAME/embeddings?api-version=2022-06-01-preview\
  -H 'Content-Type: application/json' \
  -H 'api-key: YOUR_API_KEY' \
  -d '{"input": "Sample Document goes here"}'
```

## Best Practices

### Verify inputs don't exceed the maximum length

The maximum length of input text for our embedding models are 2048 tokens (approximately equivalent to around 2-3 pages of text). You should verify that your inputs don't exceed this limit before making a request.

### Choose the best model for your task

For the search models you can obtain embeddings in two ways. The `<search_model>-doc` model is used for longer pieces of text (to be searched over) and the `<search_model>-query` model is used for shorter pieces of text, typically queries or class labels in zero shot classification. You can read more about all of the embeddings models in our [Models](../concepts/models.md) guide.

### Replace newlines with a single space

Unless you are embedding code, we suggest replacing newlines (\n) in your input with a single space, as we have observed inferior results when newlines are present.

## Limitations & risks

Our embedding models may be unreliable or pose social risks in certain cases, and may cause harm in the absence of mitigations. Please review our Responsible AI content for more information on how to approach their use responsibly.

## Next steps

Learn more about the [underlying models that power Azure OpenAI](../concepts/models.md).