---
title: Create a SQL pool by using Azure Resource Manager template
description: Learn how to create an Azure Synapse Analytics SQL pool by using Azure Resource Manager template.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 06/09/2020
---

# Create an Azure Synapse Analytics SQL pool by using Azure Resource Manager template

This template will create an Azure Synapse Analytics SQL pool with Transparent Data Encryption enabled. Synapse SQL pool refers to the enterprise data warehousing features that are generally available in Azure Synapse.


<!-- The second paragraph must be the following include file. You might need to change the file path of the include file depending on your content structure. This include is a paragraph that consistently introduces ARM concepts before doing a deployment and includes all our desired links to ARM content.-->

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

<!-- If your service has prerequisites you can include the free account link in that section. -->

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Prerequisites

<!-- If there aren't any prerequisites, just place "None" in the section. -->
None.

## Create an Azure Synapse Analytics SQL pool

<!-- The second H2 must start with "Create a". For example,  'Create a Key Vault', 'Create a virtual machine', etc. -->

### Review the template

<!-- The first sentence must be the following sentence. Use a link to the quickstart gallery that begins with https://azure.microsoft.com/resources/templates/.  -->

The template used in this quickstart is from [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates).

<!-- After the first sentence, add a JSON code fence that links to the quickstart template. Customers have provided feedback that they prefer to see the whole template. We recommend you include the entire template in your article. If your template is too long to show in the quickstart (more than 250 lines), you can instead add a sentence that says - `The template for this article is too long to show here. To view the template, see [azuredeploy.json](link to template's raw output)`.

The syntax for the code fence is: -->

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json":::

<!-- For visibility, use highlight for the template's "resources": section. -->

<!-- After the JSON code fence, a list of each resourceType from the JSON must exist with a link to the template reference starting with /azure/templates. List the resourceType links in the same order as in the template.

For example:

* [**Microsoft.KeyVault/vaults**](/azure/templates/microsoft.keyvault/vaults): create an Azure key vault.
* [**Microsoft.KeyVault/vaults/secrets**](/azure/templates/microsoft.keyvault/vaults/secrets): create an key vault secret.

The URL usually appears as, for example, https://docs.microsoft.com/azure/templates/Microsoft.Network/2019-11-01/loadBalancers for loadbalancer of Microsoft.Network. Remove the API version from the URL so that the URL redirects to the latest version.
-->

<!-- List additional quickstart templates. For example: [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular).
Notice the resourceType and sort elements in the URL.
-->

### Deploy the template

1. Select the following image to sign in to Azure and open the template. This template creates an Synapse SQL pool.
 [![Deploy to Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)
1. Enter or update the following values:

    * **Subscription**: Select an Azure subscription.
    * **Resource group**: Select **Create new** and enter a unique name for the resource group and select **OK**. A new resource group will facilitate resource clean up.
    * **Region**: Select a region.  For example, **Central US**.
    * **SQL Server name**: Accept the default name or enter a name for the SQL Server name.
    * **SQL Administrator login**: Enter the administrator username for the SQL Server.
    * **SQL Administrator password**: Enter the administrator password for the SQL Server.
    * **Data Warehouse Name**: Enter a SQL pool name.
    * **Transparent Data Encryption**: Accept the default, enabled. 
    * **Service Level Objective**: Accept the default, DW400c.
    * **Location**: Accept the default location of the resource group.
    * **Review and Create**: Select.
    * **Create**: Select.
<!--
<a href="https%3A%2F%2Fgithub.com%2FAzure%2Fazure-quickstart-templates%2Ftree%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create" target="_blank">
<img src="../../media/template-deployments/deploy-to-azure.svg"/>
</a>
-->

<!--
 One of the following options must be included:

  - **CLI**: In an Azure CLI Interactive code fence must contain **az deployment group create**. For example:

    ```azurecli-interactive
    read -p "Enter a project name that is used for generating resource names:" projectName &&
    read -p "Enter the location (i.e. centralus):" location &&
    templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri
    echo "Press [ENTER] to continue ..." &&
    read
    ```

  - **PowerShell**: In an Azure PowerShell Interactive code fence must contain **New-AzResourceGroupDeployment**. For example:

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

  - **Portal**: Use a button with description **Deploy to Azure**, and the shared image ../media/template-deployments/deploy-to-azure.svg. The template link starts with https://portal.azure.com/#create/Microsoft.Template/uri/.
  
    ```markdown
    [![Deploy to Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)
    ```

    The shared button image is in [GitHub](https://github.com/MicrosoftDocs/azure-docs-pr/blob/master/articles/media/template-deployments/deploy-to-azure.svg). To find more information about this deployment option, see [Use a deployment button to deploy templates from GitHub repository](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-to-azure-button).
 -->

## Review deployed resources

<!-- You can also use the title "Validate the deployment". -->

<!-- Include a portal screenshot of the resources or use interactive Azure CLI and Azure PowerShell commands to show the deployed resources. -->

You can either use the Azure portal to check the deployed resources, or use Azure CLI or Azure PowerShell script to list the deployed resources.

# [CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your Synapse SQL pool exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# [PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your SQL pool account exists"
(Get-AzResource -ResourceType "Microsoft.Sql/servers/databases" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---
## Clean up resources

<!-- Include a paragraph that explains how to delete unneeded resources. Add a portal screenshot or use interactive Azure CLI and Azure PowerShell commands to clean up the resources. -->


When no longer needed, delete the resource group by using Azure CLI or Azure PowerShell:

# [CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# [PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

<!--

Choose Azure CLI, Azure PowerShell, or Azure portal to delete the resource group.

Here are the samples for Azure CLI and Azure PowerShell:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

-->

## Next steps

<!-- You can either make the next steps similar to the next steps in your other quickstarts, or point users to the following tutorial.

If you want to include links to more information about the service, it's acceptable to use a paragraph and bullet points.
-->

In this quickstart, you created an Azure Synapse Analytics SQL pool using an Azure Resource Manager template and validated the deployment. To learn more about Azure Azure Synapse Analytics and Azure Resource Manager, continue on to the articles below.

- Read an [Overview of Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)
- Learn more about [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Create and deploy your first Azure Resource Manager template](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
