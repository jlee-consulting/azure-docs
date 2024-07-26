---
title: How to create and manage an Azure AI Studio hub
titleSuffix: Azure AI Studio
description: This article describes how to create and manage an Azure AI Studio hub.
manager: scottpolly
ms.service: azure-ai-studio
ms.custom:
  - ignite-2023
  - build-2024
ms.topic: how-to
ms.date: 5/21/2024
ms.reviewer: deeikele
ms.author: larryfr
author: Blackmist
---

# How to create and manage an Azure AI Studio hub

In AI Studio, hubs provide the environment for a team to collaborate and organize work, and help you as a team lead or IT admin centrally set up security settings and govern usage and spend. You can create and manage a hub from the Azure portal or from the AI Studio. 

In this article, you learn how to create and manage a hub in AI Studio with the default settings so you can get started quickly. Do you need to customize security or the dependent resources of your hub? Then use [Azure Portal](create-secure-ai-hub.md) or [template options](create-azure-ai-hub-template.md). 

> [!TIP]
> If you'd like to create your Azure AI Studio hub using a template, see the articles on using [Bicep](create-azure-ai-hub-template.md) or [Terraform](create-hub-terraform.md).

## Create a hub in AI Studio

To create a new hub, you need either the Owner or Contributor role on the resource group or on an existing hub. If you're unable to create a hub due to permissions, reach out to your administrator. If your organization is using [Azure Policy](../../governance/policy/overview.md), don't create the resource in AI Studio. Create the hub [in the Azure portal](#create-a-secure-hub-in-the-azure-portal) instead.

[!INCLUDE [Create Azure AI Studio hub](../includes/create-hub.md)]

## Create a secure hub in the Azure portal

If your organization is using [Azure Policy](../../governance/policy/overview.md), set up a hub that meets your organization's requirements instead of using AI Studio for resource creation. 

1. From the Azure portal, search for `Azure AI Studio` and create a new hub by selecting **+ New Azure AI hub**
1. Enter your hub name, subscription, resource group, and location details.
1. For **Azure AI services base models**, select an existing AI services resource or create a new one. Azure AI services include multiple API endpoints for Speech, Content Safety, and Azure OpenAI. 
    
    :::image type="content" source="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-create-basics.png" alt-text="Screenshot of the option to set hub basic information." lightbox="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-create-basics.png":::

1. Select the **Storage** tab to specify storage account settings.

    :::image type="content" source="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-create-resources.png" alt-text="Screenshot of the Create a hub with the option to set storage resource information." lightbox="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-create-resources.png"::: 

1. Select the **Networking** tab to set up Network isolation. Read more on [network isolation](configure-managed-network.md). For a walkthrough of creating a secure hub, see [Create a secure hub](create-secure-ai-hub.md).

    :::image type="content" source="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-create-networking.png" alt-text="Screenshot of the Create a hub with the option to set network isolation information." lightbox="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-create-networking.png":::  

1. Select the **Encryption** tab to set up data encryption. You can either use **Microsoft-managed keys** or enable **Customer-managed keys**. 

    :::image type="content" source="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-create-encryption.png" alt-text="Screenshot of the Create a hub with the option to select your encryption type." lightbox="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-create-encryption.png":::

1. Select the **Identity** tab. By default, **System assigned identity** is enabled, but you can switch to **User assigned identity** if existing storage, key vault, and container registry are selected in **Storage**.

    :::image type="content" source="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-create-identity.png" alt-text="Screenshot of the Create a hub with the option to select a managed identity." lightbox="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-create-identity.png":::

    > [!NOTE]
    > If you select **User assigned identity**, your identity needs to have the `Cognitive Services Contributor` role in order to successfully create a new hub.
    
1. Select the **Tags** tab to add tags.

    :::image type="content" source="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-create-tags.png" alt-text="Screenshot of the Create a hub with the option to add tags." lightbox="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-create-tags.png":::

1. Select **Review + create** > **Create**. 

## Manage your hub from the Azure portal

### Manage access control

Manage role assignments from **Access control (IAM)** within the Azure portal. Learn more about hub [role-based access control](../concepts/rbac-ai-studio.md).

To add grant users permissions: 
1. Select **+ Add** to add users to your hub.

1. Select the **Role** you want to assign.

    :::image type="content" source="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-rbac-role.png" alt-text="Screenshot of the page to add a role within the Azure portal hub view." lightbox="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-rbac-role.png":::

1. Select the **Members** you want to give the role to.  

    :::image type="content" source="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-rbac-members.png" alt-text="Screenshot of the add members page within the Azure portal hub view." lightbox="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-rbac-members.png":::

1. **Review + assign**. It can take up to an hour for permissions to be applied to users.

### Networking

Hub networking settings can be set during resource creation or changed in the **Networking** tab in the Azure portal view. Creating a new hub invokes a Managed Virtual Network. This streamlines and automates your network isolation configuration with a built-in Managed Virtual Network. The Managed Virtual Network settings are applied to all projects created within a hub. 

At hub creation, select between the networking isolation modes: **Public**, **Private with Internet Outbound**, and **Private with Approved Outbound**. To secure your resource, select either **Private with Internet Outbound** or **Private with Approved Outbound** for your networking needs. For the private isolation modes, a private endpoint should be created for inbound access. For more information on network isolation, see [Managed virtual network isolation](configure-managed-network.md). To create a secure hub, see [Create a secure hub](create-secure-ai-hub.md). 

At hub creation in the Azure portal, creation of associated Azure AI services, Storage account, Key vault, Application insights, and Container registry is given. These resources are found on the Resources tab during creation. 

To connect to Azure AI services (Azure OpenAI, Azure AI Search, and Azure AI Content Safety) or storage accounts in Azure AI Studio, create a private endpoint in your virtual network. Ensure the public network access (PNA) flag is disabled when creating the private endpoint connection. For more about Azure AI services connections, follow documentation [here](../../ai-services/cognitive-services-virtual-networks.md). You can optionally bring your own (BYO) search, but this requires a private endpoint connection from your virtual network.

### Encryption
Projects that use the same hub, share their encryption configuration. Encryption mode can be set only at the time of hub creation between Microsoft-managed keys and Customer-managed keys. 

From the Azure portal view, navigate to the encryption tab, to find the encryption settings for your hub. 
For hubs that use CMK encryption mode, you can update the encryption key to a new key version. This update operation is constrained to keys and key versions within the same Key Vault instance as the original key.

:::image type="content" source="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-manage-encryption.png" alt-text="Screenshot of the Encryption page of the hub in the Azure portal." lightbox="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-manage-encryption.png":::

### Update Azure Application Insights and Azure Container Registry

To use custom environments for Prompt Flow, you're required to configure an Azure Container Registry for your hub. To use Azure Application Insights for Prompt Flow deployments, a configured Azure Application Insights resource is required for your hub. Updating the workspace-attached Azure Container Registry or ApplicationInsights resources may break lineage of previous jobs, deployed inference endpoints, or your ability to rerun earlier jobs in the workspace. 

You can use the Azure Portal, Azure SDK/CLI options, or the infrastructure-as-code templates to update both Azure Application Insights and Azure Container Registry for the hub.

# [Azure portal](#tab/portal)

You can configure your hub for these resources during creation or update after creation. 

To update Azure Application Insights from the Azure portal, navigate to the **Properties** for your hub in the Azure portal, then select **Change Application Insights**.

:::image type="content" source="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-manage-update-associated-resources.png" alt-text="Screenshot of the properties page of the hub resource in the Azure portal." lightbox="~/reusable-content/ce-skilling/azure/media/ai-studio/resource-manage-update-associated-resources.png":::

# [Python SDK](#tab/python)

```python
from azure.ai.ml.entities import Hub

my_app_insights = "{APPLICATION_INSIGHTS_ARM_ID}"
my_container_registry = "{CONTAINER_REGISTRY_ARM_ID}"

# construct a basic hub
my_hub = Hub(name="myexamplehub", 
             location="East US", 
             application_insights=my_app_insights,
             container_registry=my_container_registry)

# update_dependent_resources is used to give consent to update the workspace dependent resources.
created_hub = ml_client.workspaces.begin_update(workspace=my_hub, update_dependent_resources=True).result()
```

# [Azure CLI](#tab/azurecli)

See flag documentation for [```az ml workspace update```](/cli/azure/ml/workspace#az-ml-workspace-update)

```azurecli
az ml workspace update -n "myexamplehub" -g "{MY_RESOURCE_GROUP}" -a "APPLICATION_INSIGHTS_ARM_ID" -u
```
---

## Next steps

- [Create a project](create-projects.md)
- [Learn more about Azure AI Studio](../what-is-ai-studio.md)
- [Learn more about hubs](../concepts/ai-resources.md)
