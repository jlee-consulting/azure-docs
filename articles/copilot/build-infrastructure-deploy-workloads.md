---
title: Build infrastructure and deploy workloads using Microsoft Copilot in Azure
description: Learn how Microsoft Copilot in Azure can help you build custom infrastructure for your workloads and provide templates and scripts to help you deploy.
ms.date: 02/26/2024
ms.topic: how-to
ms.service: copilot-for-azure
ms.custom:
  - ignite-2023
  - ignite-2023-copilotinAzure
  - build-2024
ms.author: jenhayes
author: JnHs
---

# Build infrastructure and deploy workloads using Microsoft Copilot in Azure

Microsoft Copilot in Azure (preview) can help you quickly build custom infrastructure for your workloads and provide templates and scripts to help you deploy. By using Microsoft Copilot in Azure, you can often reduce your deployment time dramatically. Microsoft Copilot in Azure also helps you align to security and compliance standards and other best practices.

Throughout a conversation, Microsoft Copilot in Azure asks you questions to better understand your requirements and applications. Based on the provided information, it then provides several architecture options suitable for deploying that infrastructure. After you select an option, Microsoft Copilot in Azure provides detailed descriptions of the infrastructure, including how it can be configured. Finally, Microsoft Copilot in Azure provides templates and scripts using the language of your choice to deploy your infrastructure.

To get help building infrastructure and deploying workloads, start on the [More virtual machines and related solutions](https://portal.azure.com/#view/Microsoft_Azure_SolutionCenter/SolutionGroup.ReactView/groupid/defaultLandingVmBrowse) page in the Azure portal. You can reach this page from **Virtual machines** by selecting the arrow next to **Create**, then selecting **More VMs and related solutions**.

:::image type="content" source="media/build-infrastructure-deploy-workloads/workloads-more-vms.png" alt-text="Screenshot of the More VMs and related solutions option from the Virtual machines page in the Azure portal.":::

Once you're there, start the conversation by letting Microsoft Copilot in Azure know what you want to build and deploy.

[!INCLUDE [scenario-note](includes/scenario-note.md)]

[!INCLUDE [preview-note](includes/preview-note.md)]

## Sample prompts

The prompts you use can vary depending on the type of workload you want to deploy, and the stage of the conversation that you're in. Here are a few examples of the kinds of prompts you might use. Modify these prompts based on your real-life scenarios, or try additional prompts as the conversation continues.

- Starting the conversation:
  - "Help me deploy a website on Azure"
  - "Give me infrastructure for my new application."
- Requirement gathering stage:
  - "Give me examples of these requirements."
  - "What do you mean by security requirements?"
  - (or provide your requirements based on the questions)
- High level architecture stage:
  - "Let's go with option 1."
  - "Give me more details about option 1."
  - "Are there more options available?"
  - "Instead of SQL, use Cosmos DB."
  - "Can you give me comparison table for these options? Also include approximate cost."
- Detailed infrastructure building stage:
  - "I like this infrastructure. Give me an ARM template to deploy this."
  - "Can you include rolling upgrade mode Manual instead of Automatic for the VMSS?"
  - "Can you explain this design in more detail?"
  - "Are there any alternatives to private link?"
- Code building stage:
  - "Can you give me PS instead of ARM template?"
  - "Change VMSS instance count to 100 instead of 10."
  - "Explain this code in English."

## Examples

From the **[More virtual machines and related solutions](https://portal.azure.com/#view/Microsoft_Azure_SolutionCenter/SolutionGroup.ReactView/groupid/defaultLandingVmBrowse)** page, you can tell Microsoft Copilot in Azure "**I want to deploy a website on Azure**." Microsoft Copilot in Azure responds with a series of questions to better understand your scenario.

:::image type="content" source="media/build-infrastructure-deploy-workloads/workloads-deploy-website.png" lightbox="media/build-infrastructure-deploy-workloads/workloads-deploy-website.png" alt-text="Screenshot showing Microsoft Copilot in Azure providing options to deploy a website.":::

After you provide answers, Microsoft Copilot in Azure provides several options that might be a good fit. You can choose one of these or ask more questions.

:::image type="content" source="media/build-infrastructure-deploy-workloads/workloads-design-options.png" alt-text="Screenshot showing Microsoft Copilot in Azure Design options.":::

After you specify which option you'd like to use, Microsoft Copilot in Azure provides a step-by-step plan to walk you through the deployment. It gives you the option to change parts of the plan and also asks you to choose a development tool. In this example, Azure App Service is selected.

:::image type="content" source="media/build-infrastructure-deploy-workloads/workloads-app-service.png" alt-text="Screenshot showing Microsoft Copilot in Azure providing steps to deploy a website using Azure App Service.":::

Since the response in this example is ARM template, Microsoft Copilot in Azure creates a basic ARM template, then provides instructions for how to deploy it.

:::image type="content" source="media/build-infrastructure-deploy-workloads/workloads-create-template.png" alt-text="Screenshot showing Microsoft Copilot in Azure creating an ARM template.":::

:::image type="content" source="media/build-infrastructure-deploy-workloads/workload-deploy-template.png" alt-text="Screenshot showing Microsoft Copilot in Azure providing instructions for deploying the ARM template.":::

## Next steps

- Explore [capabilities](capabilities.md) of Microsoft Copilot in Azure.
- Learn more about [virtual machines in Azure](/azure/virtual-machines/overview).
