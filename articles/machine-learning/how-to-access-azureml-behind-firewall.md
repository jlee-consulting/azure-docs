---
title: Configure inbound and outbound network traffic
titleSuffix: Azure Machine Learning
description: 'How to configure the required inbound and outbound network traffic when using a secure Azure Machine Learning workspace.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 09/14/2021
ms.custom: devx-track-python
---

# Configure inbound and outbound network traffic

In this article, learn about the network communication requirements when securing Azure Machine Learning workspace in a virtual network (VNet). This includes how to configure Azure Firewall to control access to your Azure Machine Learning workspace and the public internet. To learn more about securing Azure Machine Learning, see [Enterprise security for Azure Machine Learning](concept-enterprise-security.md).

> [!NOTE]
> The information in this article applies to Azure Machine Learning workspace configured with a private endpoint.

> [!TIP]
> This article is part of a series on securing an Azure Machine Learning workflow. See the other articles in this series:
>
> * [Virtual network overview](how-to-network-security-overview.md)
> * [Secure the workspace resources](how-to-secure-workspace-vnet.md)
> * [Secure the training environment](how-to-secure-training-vnet.md)
> * [Secure the inference environment](how-to-secure-inferencing-vnet.md)
> * [Enable studio functionality](how-to-enable-studio-virtual-network.md)
> * [Use custom DNS](how-to-custom-dns.md)

## Required public internet access

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

## Azure Firewall

> [!IMPORTANT]
> Azure Firewall provides security _for Azure Virtual Network resources_. Some Azure Services, such as Azure Storage Accounts, have their own firewall settings that _apply to the public endpoint for that specific service instance_. The information in this document is specific to Azure Firewall.
> 
> For information on service instance firewall settings, see [Use studio in a virtual network](how-to-enable-studio-virtual-network.md#firewall-settings).

* For __inbound__ traffic to Azure Machine Learning compute cluster and compute instance, use [user-defined routes (UDRs)](../virtual-network/virtual-networks-udr-overview.md) to skip the firewall.

* For __outbound__ traffic, create __network__ and __application__ rules. 

These rule collections are described in more detail in [What are some Azure Firewall concepts](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts).

### Inbound configuration

[!INCLUDE [udr info for computes](../../includes/machine-learning-compute-user-defined-routes.md)]

### Outbound configuration

1. Add __Network rules__, allowing traffic __to__ and __from__ the following service tags:

    | Service tag | Protocol | Port |
    | ----- |:-----:|:-----:|
    | AzureActiveDirectory | TCP | * |
    | AzureMachineLearning | TCP | 443 |
    | AzureResourceManager | TCP | 443 |
    | Storage.region       | TCP | 443 |
    | AzureFrontDoor.FrontEnd</br>* Not needed in Azure China. | TCP | 443 | 
    | ContainerRegistry.region  | TCP | 443 |
    | MicrosoftContainerRegistry.region | TCP | 443 |
    | Keyvault.region | TCP | 443 |

    > [!TIP]
    > * ContainerRegistry.region is only needed for custom Docker images. This includes small modifications (such as additional packages) to base images provided by Microsoft.
    > * MicrosoftContainerRegistry.region is only needed if you plan on using the _default Docker images provided by Microsoft_, and _enabling user-managed dependencies_.
    > * Keyvault.region is only needed if your workspace was created with the [hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) flag enabled.
    > * For entries that contain `region`, replace with the Azure region that you're using. For example, `ContainerRegistry.westus`.

1. Add __Application rules__ for the following hosts:

    > [!NOTE]
    > This is not a complete list of the hosts required for all Python resources on the internet, only the most commonly used. For example, if you need access to a GitHub repository or other host, you must identify and add the required hosts for that scenario.

    | **Host name** | **Purpose** |
    | ---- | ---- |
    | **graph.windows.net** | Used by Azure Machine Learning compute instance/cluster. |
    | **anaconda.com**</br>**\*.anaconda.com** | Used to install default packages. |
    | **\*.anaconda.org** | Used to get repo data. |
    | **pypi.org** | Used to list dependencies from the default index, if any, and the index is not overwritten by user settings. If the index is overwritten, you must also allow **\*.pythonhosted.org**. |
    | **cloud.r-project.org** | Used when installing CRAN packages for R development. |
    | **\*pytorch.org** | Used by some examples based on PyTorch. |
    | **\*.tensorflow.org** | Used by some examples based on Tensorflow. |
    | **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | Used to retrieve VS Code server bits that are installed on the compute instance through a setup script.|
    | **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** | Used to retrieve websocket server bits that are installed on the compute instance. The websocket server is used to transmit requests from Visual Studio Code client (desktop application) to Visual Studio Code server running on the compute instance.|
    | **dc.applicationinsights.azure.com** | Used to collect metrics and diagnostics information when working with Microsoft support. |
    | **dc.applicationinsights.microsoft.com** | Used to collect metrics and diagnostics information when working with Microsoft support. |
    | **dc.services.visualstudio.com** | Used to collect metrics and diagnostics information when working with Microsoft support. | 
    

    For __Protocol:Port__, select use __http, https__.

    For more information on configuring application rules, see [Deploy and configure Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

1. To restrict outbound traffic for models deployed to Azure Kubernetes Service (AKS), see the [Restrict egress traffic in Azure Kubernetes Service](../aks/limit-egress-traffic.md) and [Deploy ML models to Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#connectivity) articles.

### Azure Kubernetes Services

When using Azure Kubernetes Service with Azure Machine Learning, the following traffic must be allowed:

* General inbound/outbound requirements for AKS as described in the [Restrict egress traffic in Azure Kubernetes Service](../aks/limit-egress-traffic.md) article.
* __Outbound__ to mcr.microsoft.com.
* When deploying a model to an AKS cluster, use the guidance in the [Deploy ML models to Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#connectivity) article.

## Other firewalls

The guidance in this section is generic, as each firewall has its own terminology and specific configurations. If you have questions, check the documentation for the firewall you are using.

If not configured correctly, the firewall can cause problems using your workspace. There are various host names that are used both by the Azure Machine Learning workspace. The following sections list hosts that are required for Azure Machine Learning.

### Microsoft hosts

The hosts in the following tables are owned by Microsoft, and provide services required for the proper functioning of your workspace. The tables list hosts for the Azure public, Azure Government, and Azure China 21Vianet regions.

**General Azure hosts**

| **Required for** | **Azure public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Azure portal | management.azure.com | management.azure.us | management.azure.cn |
| Azure Resource Manager | management.azure.com | management.usgovcloudapi.net | management.chinacloudapi.cn |

**Azure Machine Learning hosts**

> [!IMPORTANT]
> In the following table, replace `<storage>` with the name of the default storage account for your Azure Machine Learning workspace.

| **Required for** | **Azure public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*.azureml.ms | \*.ml.azure.us | \*.ml.azure.cn |
| Integrated notebook | \*.notebooks.azure.net | \*.notebooks.usgovcloudapi.net |\*.notebooks.chinacloudapi.cn |
| Integrated notebook | \<storage\>.file.core.windows.net | \<storage\>.file.core.usgovcloudapi.net | \<storage\>.file.core.chinacloudapi.cn |
| Integrated notebook | \<storage\>.dfs.core.windows.net | \<storage\>.dfs.core.usgovcloudapi.net | \<storage\>.dfs.core.chinacloudapi.cn |
| Integrated notebook | \<storage\>.blob.core.windows.net | \<storage\>.blob.core.usgovcloudapi.net | \<storage\>.blob.core.chinacloudapi.cn |
| Integrated notebook | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| Integrated notebook | \*.aznbcontent.net |  | |

**Azure Machine Learning compute instance and compute cluster hosts**

| **Required for** | **Azure public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Compute cluster/instance | graph.windows.net | graph.windows.net | graph.chinacloudapi.cn |
| Compute instance | \*.instances.azureml.net | \*.instances.azureml.us | \*.instances.azureml.cn |
| Compute instance | \*.instances.azureml.ms |  |  |
| Azure Storage Account | \*.blob.core.windows.net</br>\*.table.core.windows.net</br>\*.queue.core.windows.net | \*.blob.core.usgovcloudapi.net</br>\*.table.core.usgovcloudapi.net</br>\*.queue.core.usgovcloudapi.net | \*blob.core.chinacloudapi.cn</br>\*.table.core.chinacloudapi.cn</br>\*.queue.core.chinacloudapi.cn |
| Azure Key Vault | \*.vault.azure.net | \*.vault.usgovcloudapi.net | \*.vault.azure.cn |

> [!IMPORTANT]
> Your firewall must allow communication with \*.instances.azureml.ms over __TCP__ ports __18881, 443, and 8787__.

> [!TIP]
> The FQDN for Azure Key Vault is only needed if your workspace was created with the [hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) flag enabled.

**Docker images maintained by by Azure Machine Learning**

| **Required for** | **Azure public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Microsoft Container Registry | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |
| Azure Machine Learning pre-built images | viennaglobal.azurecr.io | viennaglobal.azurecr.io | viennaglobal.azurecr.io |

> [!TIP]
> * __Azure Container Registry__ is required for any custom Docker image. This includes small modifications (such as additional packages) to base images provided by Microsoft.
> * __Microsoft Container Registry__ is only needed if you plan on using the _default Docker images provided by Microsoft_, and _enabling user-managed dependencies_.
> * If you plan on using federated identity, follow the [Best practices for securing Active Directory Federation Services](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) article.

Also, use the information in the [inbound configuration](#inbound-configuration) section to add IP addresses for `BatchNodeManagement` and `AzureMachineLearning`.

For information on restricting access to models deployed to AKS, see [Restrict egress traffic in Azure Kubernetes Service](../aks/limit-egress-traffic.md).

**Support diagnostics**

For Microsoft Support to be able to diagnose any problems you run into with your workspace, you must allow outbound traffic to the following hosts:

* **dc.applicationinsights.azure.com**
* **dc.applicationinsights.microsoft.com**
* **dc.services.visualstudio.com**

For a list of IP addresses for these hosts, see [IP addresses used by Azure Monitor](../azure-monitor/app/ip-addresses.md).

### Python hosts

The hosts in this section are used to install Python packages, and are required during development, training, and deployment. 

> [!NOTE]
> This is not a complete list of the hosts required for all Python resources on the internet, only the most commonly used. For example, if you need access to a GitHub repository or other host, you must identify and add the required hosts for that scenario.

| **Host name** | **Purpose** |
| ---- | ---- |
| **anaconda.com**</br>**\*.anaconda.com** | Used to install default packages. |
| **\*.anaconda.org** | Used to get repo data. |
| **pypi.org** | Used to list dependencies from the default index, if any, and the index is not overwritten by user settings. If the index is overwritten, you must also allow **\*.pythonhosted.org**. |
| **\*pytorch.org** | Used by some examples based on PyTorch. |
| **\*.tensorflow.org** | Used by some examples based on Tensorflow. |

### R hosts

The hosts in this section are used to install R packages, and are required during development, training, and deployment.

> [!NOTE]
> This is not a complete list of the hosts required for all R resources on the internet, only the most commonly used. For example, if you need access to a GitHub repository or other host, you must identify and add the required hosts for that scenario.

| **Host name** | **Purpose** |
| ---- | ---- |
| **cloud.r-project.org** | Used when installing CRAN packages. |

### Azure Kubernetes Services

When using Azure Kubernetes Service with Azure Machine Learning, the following traffic must be allowed:

* General inbound/outbound requirements for AKS as described in the [Restrict egress traffic in Azure Kubernetes Service](../aks/limit-egress-traffic.md) article.
* __Outbound__ to mcr.microsoft.com.
* When deploying a model to an AKS cluster, use the guidance in the [Deploy ML models to Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#connectivity) article.

### Visual Studio Code hosts

The hosts in this section are used to install Visual Studio Code packages to establish a remote connection between Visual Studio Code and compute instances in your Azure Machine Learning workspace.

> [!NOTE]
> This is not a complete list of the hosts required for all Visual Studio Code resources on the internet, only the most commonly used. For example, if you need access to a GitHub repository or other host, you must identify and add the required hosts for that scenario.

| **Host name** | **Purpose** |
| ---- | ---- |
|  **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | Used to retrieve VS Code server bits that are installed on the compute instance through a setup script.|
| **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** |Used to retrieve websocket server bits that are installed on the compute instance. The websocket server is used to transmit requests from Visual Studio Code client (desktop application) to Visual Studio Code server running on the compute instance. |

## Next steps

This article is part of a series on securing an Azure Machine Learning workflow. See the other articles in this series:

* [Virtual network overview](how-to-network-security-overview.md)
* [Secure the workspace resources](how-to-secure-workspace-vnet.md)
* [Secure the training environment](how-to-secure-training-vnet.md)
* [Secure the inference environment](how-to-secure-inferencing-vnet.md)
* [Enable studio functionality](how-to-enable-studio-virtual-network.md)
* [Use custom DNS](how-to-custom-dns.md)

For more information on configuring Azure Firewall, see [Tutorial: Deploy and configure Azure Firewall using the Azure portal](../firewall/tutorial-firewall-deploy-portal.md).
