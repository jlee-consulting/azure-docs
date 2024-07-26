---
title: Discover performance recommendations with Code Optimizations using Microsoft Copilot in Azure
description: Learn about scenarios where Microsoft Copilot in Azure can use Application Insight Code Optimizations to help optimize your apps.
ms.date: 11/20/2023
ms.topic: how-to
ms.service: copilot-for-azure
ms.custom:
  - ignite-2023
  - ignite-2023-copilotinAzure
  - build-2024
ms.author: jenhayes
author: JnHs
---

# Discover performance recommendations with Code Optimizations using Microsoft Copilot in Azure

Microsoft Copilot in Azure (preview) can provide [Code Optimizations](/azure/azure-monitor/insights/code-optimizations) for Application Insights resources that have an active [Application Insights Profiler](/azure/azure-monitor/profiler/profiler-settings). This lets you view recommendations tailored to your app to help optimize its performance.

When you ask Microsoft Copilot in Azure to provide these recommendations, it automatically pulls context from an open Application Insights blade or App Service blade to display available recommendations specific to that app. If the context isn't clear, you'll be prompted to choose an Application Insights resource from a resource selector page.

[!INCLUDE [scenario-note](includes/scenario-note.md)]

[!INCLUDE [preview-note](includes/preview-note.md)]

## Sample prompts

Here are a few examples of the kinds of prompts you can use with Code Optimizations. Modify these prompts based on your real-life scenarios, or try additional prompts about specific areas for optimization.

- "Show my code performance recommendations"
- "Any available app code optimizations?"
- "Code optimizations in my app"
- "My app code is running slow"
- "Make my app faster with a code change"

## Examples

In this example, Microsoft Copilot in Azure responds to the prompt, "**Any code performance optimizations?**" The response notes that there are 6 recommendations, providing the option to view either the top recommendation or all recommendations at once.

:::image type="content" source="media/optimize-code-application-insights/code-optimizations-show-recommendations.png" lightbox="media/optimize-code-application-insights/code-optimizations-show-recommendations.png" alt-text="Screenshot of Microsoft Copilot in Azure responding to a question about code optimizations.":::

When the **Review all** option is selected, Microsoft Copilot in Azure displays all recommendations. You can then select any recommendation to see more details.

## Next steps

- Explore [capabilities](capabilities.md) of Microsoft Copilot in Azure.
- Learn more about [Code Optimizations](/azure/azure-monitor/insights/code-optimizations).
