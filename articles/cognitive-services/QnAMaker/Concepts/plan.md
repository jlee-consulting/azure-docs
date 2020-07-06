---
title: Plan your app - QnA Maker
description: Planning your QnA Maker app requires understanding how QnA Maker works and interacts with other Azure services as well as some knowledge base concepts.
ms.topic: conceptual
ms.date: 07/2/2020
---

# Plan your QnA Maker app

Planning your QnA Maker app requires understanding how QnA Maker works and interacts with other Azure services as well as some knowledge base concepts.

## Azure resources

Each [Azure resource](azure-resources.md#resource-purposes) created with QnA Maker has a specific purpose. Because each resource has its own purpose, limits, and [pricing tier](azure-resources.md#pricing-tier-considerations), it is important to understand what these resources do as part of your planning process.

|Resource|Purpose|
|--|--|
| [QnA Maker](azure-resources.md#qna-maker-resource) resource|Authoring and query prediction|
| [Cognitive Search](azure-resources.md#cognitive-search-resource) resource|Data storage and search|
| [App Service resource and App Plan Service](azure-resources.md#app-service-and-app-service-plan) resource|Query prediction endpoint|
| [Application Insights](azure-resources.md#application-insights) resource|Query prediction telemetry|

### Resource planning

While you are learning authoring and query prediction, using the free tier, `F0`, of each resource works and will provide both the authoring and query prediction experience. When you move to a production, live, scenario, reevaluate your resource selection.

#### QnA Maker resource

A single QnA Maker resource can host more than one knowledge base. The number of knowledge bases is determined by the Cognitive Search pricing tier's quantity of supported indexes. Learn more about the [relationship of indexes to knowledge bases](azure-resources.md#index-usage).

#### Knowledge base size and throughput

When you plan to build a real app, plan your resources for the size of your knowledge base, and the query prediction requests you expect.

A knowledge base size is controlled by the:
* [Cognitive Search resource](../../../search/search-limits-quotas-capacity.md) pricing tier limits
* [QnA Maker limits](../limits.md)

The knowledge base query prediction request is controlled by the Web app plan and web app. Refer to [recommended settings](azure-resources.md#recommended-settings) to plan your pricing tier.

### Resource sharing

If you already have some of these resources in use, you may consider sharing resources. While some resources [can be shared](azure-resources.md#share-services-with-qna-maker), this is an advanced scenario.

All knowledge bases created in the same QnA Maker resource share the same **test** query prediction endpoint.

### Understanding impact of resource selection

Proper resource selection means your knowledge base answers query predictions successfully.

If your knowledge base isn't functioning properly, typically the issue is improper resource management.

Improper resource selection requires investigation to determine which [resource needs to change](azure-resources.md#when-to-change-a-pricing-tier).

## Knowledge bases

A knowledge base is directly tied its QnA Maker resource and holds the question and answer (QnA) pairs used to answer query prediction requests.

### Language considerations

The first knowledge base created on your QnA Maker resource sets the language for the resource. You can only have one language for a QnA Maker resource.

Structure your QnA Maker resources by language or use [Translator](../../translator/translator-info-overview.md) to change a query from another language into the knowledge base's language before sending the query to the query prediction endpoint.

### Ingesting data sources

Ingested [data sources](knowledge-base.md), used to create a knowledge base, can be one of the following:

* Public URL
* Private SharePoint URL
* File

The ingestion process converts [supported content types](content-types.md) to markdown. All further editing of the *answer* is done with markdown. After creating your knowledge base, you can edit [QnA pairs](question-answer-set.md) in the QnA Maker portal with [rich text authoring](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer).

### Data format considerations

Because the final format of a QnA pair is markdown, understanding the [markdown support](../reference-markdown-format.md) is important.

Linked images must be available from a public URL in order to display in the test pane of the QnA Maker portal as well as any client application because QnA Maker doesn't provide authentication for content, including images.

### Bot personality

Add a bot personality to your knowledge base with [chit-chat](../how-to/chit-chat-knowledge-base.md). This personality comes through with answers provided in a certain conversational tone such as *professional* and *friendly*. This chit-chat is provided as a conversational set, which you have total control to add, edit, and remove.

A bot personality is recommended if your bot connects to your knowledge base. If you are connecting to several services, one of which is your knowledge base, you can choose to continue using chit-chat in your knowledge base, but you should review how the bot service interacts to know if that is the correct architectural design for your use.

### Conversation flow with a knowledge base

Conversation flow usually begins with a salutation from a user, such as `Hi` or `Hello`. Your knowledge base can answer with a general answer, such as `Hi, how can I help you`, and it can also provide a selection of follow-up prompts, to continue the conversation.

You should design your conversational flow with a loop in mind so that a user knows how to use your bot and isn't abandoned by the bot in the conversation. [Follow-up prompts](../how-to/multiturn-conversation.md) provide linking between QnA pairs, which allow for the conversational flow.

### Authoring with collaborators

Collaborators may be other developers who share the full development stack of the knowledge base application or may be limited to just authoring the knowledge base.

Knowledge base authoring supports several [role-based access permissions](../how-to/collaborate-knowledge-base.md) you apply in the Azure portal to limit the scope of a collaborator's abilities.

## Integration with client applications

Integration with [client applications](integration-with-other-applications.md) means sending a query to the prediction runtime endpoint. A query is sent to your specific knowledge base with an SDK or REST-based request to your QnA Maker's web app endpoint.

In order to authenticate a client request correctly, the client application must send the correct credentials and knowledge base ID. If you are using an Azure Bot Service, configure the setting as part of the bot configuration in the Azure portal.

### Conversation flow in a client application

Conversation flow in a [client application](integration-with-other-applications.md), such as an Azure bot, may require functionality before and after interacting with the knowledge base.

If your client application supports conversation flow, either providing alternate means to handle follow-up prompts or chit-chit, design these early and make sure the query using in the client application is handled correctly, either by another service or sent to your knowledge base.

### Dispatching between QnA Maker and Language Understanding (LUIS)

A client application may provide several features, only one of which is answered by a knowledge base. Other features would still need to understand the conversational text, and extract meaning from it.

A common client application architecture is to use both QnA Maker and [Language Understanding (LUIS)](../../LUIS/what-is-luis.md) together. LUIS provides the text classification and extraction for any query, including to other services, while QnA Maker provides answers from your knowledge base.

In a [shared architecture](../choose-natural-language-processing-service.md), dispatching between the two services is done with the [Dispatch](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) tool from Bot Framework.

### Active learning from a client application

QnA Maker uses _active learning_ to improve your knowledge base by suggesting alternate questions to an answer. The client application is responsible for a part of this [active learning](active-learning-suggestions.md). The client application, through conversational prompts, can determine that the answer returned from the knowledge base was not the answer the user was looking for, and determine the better answer. The client application needs to [send that information back to the knowledge base](active-learning-suggestions.md#how-you-give-explicit-feedback-with-the-train-api) to improve the prediction quality.

### Providing a default answer

If your knowledge base doesn't find an answer, it returns the _default answer_. This answer is configurable from the QnA Maker portal, on the **Settings** page, or the [APIs](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body).

This default answer is different from the Azure bot default answer. The Azure bot default answer is configured in the Azure portal, for your bot, as part of configuration settings and is returned when the score threshold is not met.

## Prediction

The prediction is the response from your knowledge base and it includes more information than just the answer. To get a query prediction response, use the [GeneateAnswer API](query-knowledge-base.md).

### Prediction score fluctuations

A score can change based on several factors:

* Number of answers you requested in response to [GenerateAnswer](query-knowledge-base.md) with `top` property
* Variety of available alternate questions
* Filtering for metadata
* Query sent to `test` or `production` knowledge base

There is a [two-phase answer ranking](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer):
* Cognitive Search - first rank - In order for an answer to return from Cognitive Search, the number of _answers allowed_ needs to be high enough that the best answers are returned by Cognitive Search so they can pass into the QnA Maker ranker
* QnA Maker - second rank - apply featurization and machine learning to determine best answer.

### Service updates

Service updates are automatically managed by applying the [latest runtime updates](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

### Scaling, throughput and resiliency

Scaling, throughput and resiliency are determined by the [Azure resources](../how-to/set-up-qnamaker-service-azure.md), their pricing tiers, and any surrounding architecture such as [Traffic manager](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager).

### Analytics with Application Insights

All queries to your knowledge base are stored in Application Insights. Use our [top queries](../how-to/get-analytics-knowledge-base.md) to understand your metrics.

## Development lifecycle

The [development lifecycle](development-lifecycle-knowledge-base.md) of a knowledge base is ongoing: editing, testing, and publishing your knowledge base.

### Knowledge base development of QnA Maker pairs

Your [QnA pairs](question-answer-set.md) should be designed and developed based on your client application usage.

Each pair can contain:
* Metadata - filterable when querying. This allows you to tag your QnA pairs with additional information about the source, content, format, and purpose of your data.
* Follow-up prompts - determine a path through your knowledge base so the user arrives at the correct answer.
* Alternate questions - alternate questions are important to allow search to match to your answer from a variety of forms of the question. [Active learning suggestions](active-learning-suggestions.md) turn into alternate questions.

### DevOps development

Developing a knowledge base to insert into a DevOps pipeline requires that the knowledge base be isolated during [batch testing](../quickstarts/batch-testing.md).

A knowledge base shares the Cognitive Search index with all other knowledge bases on the QnA Maker resource. While the knowledge base is isolated by partition, sharing the index can cause a difference in the score when compared to the published knowledge base.

In order to have the _same score_ on the `test` and `production` knowledge bases, isolate a QnA Maker resource to a single knowledge base. In this architecture, the resource only needs to live to the length of the isolated batch test.

## Next step

* [Azure resources](../how-to/set-up-qnamaker-service-azure.md)
* [Question and answer pairs](question-answer-set.md)