---
title: Author API Management policies using Microsoft Copilot in Azure
description: Learn about how Microsoft Copilot in Azure can generate Azure API Management policies based on your requirements.
ms.date: 05/28/2024
ms.topic: how-to
ms.service: copilot-for-azure
ms.custom:
  - ignite-2023
  - ignite-2023-copilotinAzure
  - build-2024
ms.author: jenhayes
author: JnHs
---

# Author API Management policies using Microsoft Copilot in Azure 

Microsoft Copilot in Azure (preview) can author [Azure API Management policies](/azure/api-management/api-management-howto-policies) based on your requirements. By using Microsoft Copilot in Azure, you can create policies quickly, even if you're not sure what code you need. This can be especially helpful when creating complex policies with many requirements.

To get help authoring API Management policies, start from the **Design** tab of an API you previously imported to your API Management instance. Be sure to use the [code editor view](/azure/api-management/set-edit-policies?tabs=editor#configure-policy-in-the-portal). Ask Microsoft Copilot in Azure to generate policy definitions for you, then copy the results right into the editor, making any desired changes. You can also ask questions to understand the different options or change the provided policy.

When you're working with API Management policies, you can also select a portion of the policy, right-click, and then select **Explain**. This will open Microsoft Copilot in Azure and paste your selection with a prompt to explain how that part of the policy works.

[!INCLUDE [scenario-note](includes/scenario-note.md)]

[!INCLUDE [preview-note](includes/preview-note.md)]

## Sample prompts

Here are a few examples of the kinds of prompts you can use to get help authoring API Management policies. Modify these prompts based on your real-life scenarios, or try additional prompts to create different kinds of policies.

- "Generate a policy to configure rate limiting with 5 requests per second"
- "Generate a policy to remove a 'X-AspNet-Version' header from the response"
- "Explain (selected policy or element) to me"

## Examples

When creating an API Management policy, you can say "**Generate a policy to configure rate limiting with 5 requests per second.**" Microsoft Copilot in Azure provides an example and explains how you might want to modify the provided based on your requirements.

:::image type="content" source="media/author-api-management-policies/api-management-policy-rate-limiting.png" lightbox="media/author-api-management-policies/api-management-policy-rate-limiting.png" alt-text="Screenshot of Microsoft Copilot in Azure generating a policy to configure rate limiting.":::

In this example, a policy is generated based on the prompt "Generate a policy to remove a 'X-AspNet-Version' header from the response."

:::image type="content" source="media/author-api-management-policies/api-management-policy-remove-header.png" lightbox="media/author-api-management-policies/api-management-policy-remove-header.png" alt-text="Screenshot of Microsoft Copilot in Azure generating a policy to remove a header.":::

When you have questions about a certain policy element, you can get more information by selecting a section of the policy, right-clicking, and selecting **Explain**.

:::image type="content" source="media/author-api-management-policies/api-management-policy-explain.png" lightbox="media/author-api-management-policies/api-management-policy-explain.png" alt-text="Screenshot of right-clicking a section of an API Management policy to get an explanation from Microsoft Copilot in Azure .":::

Microsoft Copilot in Azure explains how the code works, breaking down each specific section.

:::image type="content" source="media/author-api-management-policies/api-management-policy-explanation.png" alt-text="Screenshot of Microsoft Copilot in Azure providing information about a specific API Management policy.":::

## Next steps

- Explore [capabilities](capabilities.md) of Microsoft Copilot in Azure.
- Learn more about [Azure API Management](/azure/api-management/api-management-key-concepts).
