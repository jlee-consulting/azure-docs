---
title: Responsible AI FAQ for Microsoft Copilot in Azure (preview)
description: Learn how Microsoft Copilot in Azure (preview) uses data and what to expect.
ms.date: 05/28/2024
ms.topic: conceptual
ms.service: copilot-for-azure
ms.custom:
  - ignite-2023
  - ignite-2023-copilotinAzure
  - build-2024
ms.author: jenhayes
author: JnHs
hideEdit: true
---

# Responsible AI FAQ for Microsoft Copilot in Azure (preview)

## What is Microsoft Copilot in Azure (preview)?

Microsoft Copilot in Azure is an AI companion that enables IT teams to operate, optimize, and troubleshoot applications and infrastructure more efficiently. With Microsoft Copilot in Azure, users can gain new insights into their workloads, unlock untapped Azure functionality and orchestrate tasks across both cloud and edge. Copilot leverages Large Language Models (LLMs), the Azure control plane and insights about a user’s Azure and Arc-enabled assets. All of this is carried out within the framework of Azure’s steadfast commitment to safeguarding the customer's data security and privacy. For an overview of how Microsoft Copilot in Azure works and a summary of Copilot capabilities, see [Microsoft Copilot in Azure (preview) overview](overview.md).

## Are Microsoft Copilot in Azure's results reliable?

Microsoft Copilot in Azure is designed to generate the best possible responses with the context it has access to. However, like any AI system, Microsoft Copilot in Azure's responses will not always be perfect. All of Microsoft Copilot in Azure's responses should be carefully tested, reviewed, and vetted before making changes to your Azure environment.

## How does Microsoft Copilot in Azure (preview) use data from my Azure environment?

Microsoft Copilot in Azure generates responses grounded in your Azure environment. Microsoft Copilot in Azure only has access to resources that you have access to and can only perform actions that you have the permissions to perform, after your confirmation. Microsoft Copilot in Azure will respect all existing access management and protections such as Azure Role-Based Action Control, Privileged Identity Management, Azure Policy, and resource locks.

## What data does Microsoft Copilot in Azure collect?

User-provided prompts and Microsoft Copilot in Azure's responses are not used to further train, retrain, or improve Azure OpenAI Service foundation models that generate responses. User-provided prompts and Microsoft Copilot in Azure's responses are collected and used to improve Microsoft products and services only when users have given explicit consent to include this information within feedback. We collect user engagement data, such as, number of chat sessions and session duration, the skill used in a particular session, thumbs up, thumbs down, feedback, etc. This information is retained and used as set forth in the [Microsoft Privacy Statement](https://privacy.microsoft.com/en-us/privacystatement).

## What should I do if I see unexpected or offensive content?

The Azure team has built Microsoft Copilot in Azure guided by our [AI principles](https://www.microsoft.com/ai/principles-and-approach) and [Responsible AI Standard](https://aka.ms/RAIStandardPDF). We have prioritized mitigating exposing customers to offensive content. However, you might still see unexpected results. We're constantly working to improve our technology in preventing harmful content.

If you encounter harmful or inappropriate content in the system, please provide feedback or report a concern by selecting the downvote button on the response.

## How current is the information Microsoft Copilot in Azure provides?

We frequently update Microsoft Copilot in Azure to ensure Microsoft Copilot in Azure provides the latest information to you. In most cases, the information Microsoft Copilot in Azure provides will be up to date. However, there might be some delay between new Azure announcements to the time Microsoft Copilot in Azure is updated.

## Do all Azure services have the same level of integration with Microsoft Copilot in Azure?

No. Some Azure services have richer integration with Microsoft Copilot in Azure. We will continue to increase the number of scenarios and services that Microsoft Copilot in Azure supports. To learn more about some of the current capabilities, see [Microsoft Copilot in Azure (preview) capabilities](capabilities.md) and the articles in the **Enhanced scenarios** section.
