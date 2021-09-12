---
title: 'Quickstart: Create an Azure Purview account using Azure PowerShell/Azure CLI (preview)'
description: This Quickstart describes how to create an Azure Purview account using Azure PowerShell/Azure CLI.
author: hophanms
ms.author: hophan
ms.date: 9/10/2021
ms.topic: quickstart
ms.service: purview
ms.custom:
  - mode-api
# Customer intent: As a data steward, I want create a new Azure Purview Account so that I can scan and classify my data.
---
# Quickstart: Create an Azure Purview account using Azure PowerShell/Azure CLI

In this Quickstart, you'll create an Azure Purview account using Azure PowerShell/Azure CLI.

> [!IMPORTANT]
> Azure Purview is currently in PREVIEW. The [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) include additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

Azure Purview is a data governance service that helps you manage and govern your data landscape. By connecting to data across your on-premises, multi-cloud, and software-as-a-service (SaaS) sources, Purview creates an up-to-date map of your information. It identifies and classifies sensitive data, and provides end to end linage. Data consumers are able to discover data across your organization, and data administrators are able to audit, secure, and ensure right use of your data.

For more information about Purview, [see our overview page](overview.md). For more information about deploying Purview across your organization, [see our deployment best practices](deployment-best-practices.md).

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

### Install PowerShell

 Install either Azure PowerShell or Azure CLI in your client machine to deploy the template: [Command-line deployment](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment)

## Create an Azure Purview account

1. Sign in with your Azure credential

    # [PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Connect-AzAccount
    ```
    
    # [Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az login
    ```
    
    ---

1. If you have multiple Azure subscriptions, select the subscription you want to use:

    # [PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```
    
    # [Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```
    
    ---

1. Create a resource group for your Purview account. You can skip this step if you already have one:

    # [PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroup `
      -Name myResourceGroup `
      -Location "East US"
    ```
    
    # [Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```
    
    ---

1. Create a Purview template file such as `purviewtemplate.json`. You can update `name`, `location`, and `capacity` (`4` or `16`):

    ```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [
        {
          "name": "<yourPurviewAccountName>",
          "type": "Microsoft.Purview/accounts",
          "apiVersion": "2020-12-01-preview",
          "location": "EastUs",
          "identity": {
            "type": "SystemAssigned"
          },
          "properties": {
            "networkAcls": {
              "defaultAction": "Allow"
            }
          },
          "dependsOn": [],
          "sku": {
            "name": "Standard",
            "capacity": "4"
          },
          "tags": {}
        }
      ],
      "outputs": {}
    }
    ```

1. Deploy Purview template

    # [PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName "<myResourceGroup>" -TemplateFile "<PATH TO purviewtemplate.json>"
    ```
    
    # [Azure CLI](#tab/azure-cli)
    
    To run this deployment command, you must have the [latest version](/cli/azure/install-azure-cli) of Azure CLI.
    
    ```azurecli
    az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
    ```
    
    ---

1. The deployment command returns results. Look for `ProvisioningState` to see whether the deployment succeeded.
    
## Next steps

In this quickstart, you learned how to create an Azure Purview account.

Follow these next articles to learn how to navigate the Purview Studio, create a collection, and grant access to Purview.

* [How to use the Purview Studio](use-purview-studio.md)
* [Create a collection](quickstart-create-collection.md)
* [Add users to your Azure Purview account](catalog-permissions.md)