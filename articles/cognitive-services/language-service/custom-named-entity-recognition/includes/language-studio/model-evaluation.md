---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.custom: event-tier1-build-2022
ms.topic: include
ms.date: 05/09/2022
ms.author: aahi
---

1. Go to your project page in [Language Studio](https://aka.ms/languageStudio).

2. Select **Model performance** from the menu on the left side of the screen.
    
3. In this page you can only view the successfully trained models, F1 score of each model and [model expiry date](../../../concepts/model-lifecycle.md#custom-features). You can click on the model name for more details about its performance.

4. You can find the *model-level evaluation metrics* under **Overview**, and the *class-level evaluation metrics* under **Class performance metrics**. See [Evaluation metrics](../../concepts/evaluation-metrics.md#model-level-and-entity-level-evaluation-metrics) for more information.

    :::image type="content" source="../../media/model-details.png" alt-text="A screenshot of the model performance metrics in Language Studio." lightbox="../../media/model-details.png":::

5. The [confusion matrix](../../concepts/evaluation-metrics.md#confusion-matrix) for the model is located under **Test set confusion matrix**.

    :::image type="content" source="../../media/confusion-matrix.png" alt-text="A screenshot of a confusion matrix in Language Studio." lightbox="../../media/confusion-matrix.png":::
    
> [!NOTE]
> Entities that are neither labeled nor predicted in the test set will not be part of the displayed results.
