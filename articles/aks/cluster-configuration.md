---
title: Cluster configuration in Azure Kubernetes Services (AKS)
description: Learn how to configure a cluster in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 06/20/2020
---

# Configure an AKS cluster

As part of creating an AKS cluster, you may need to customize your cluster configuration to suit your needs. This article introduces a few options for customizing your AKS cluster.

## OS configuration (Preview)

AKS now supports Ubuntu 18.04 as the node operating system (OS) in preview. During the preview period, both Ubuntu 16.04 and Ubuntu 18.04 are available.

You must have the following resources installed:

- The Azure CLI, version 2.2.0 or later
- The aks-preview 0.4.35 extension

To install the aks-preview 0.4.35 extension or later, use the following Azure CLI commands:

```azurecli
az extension add --name aks-preview
az extension list
```

Register the `UseCustomizedUbuntuPreview` feature:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

It might take several minutes for the status to show as **Registered**. You can check the registration status by using the [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) command:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

When the status shows as registered, refresh the registration of the `Microsoft.ContainerService` resource provider by using the [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) command:

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### New clusters

Configure the cluster to use Ubuntu 18.04 when the cluster is created. Use the `--aks-custom-headers` flag to set the Ubuntu 18.04 as the default OS.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

If you want to create a regular Ubuntu 16.04 cluster, you can do so by omitting the custom `--aks-custom-headers` tag.

### Existing clusters

Configure a new node pool to use Ubuntu 18.04. Use the `--aks-custom-headers` flag to set the Ubuntu 18.04 as the default OS for that node pool.

```azure-cli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

If you want to create a regular Ubuntu 16.04 node pools, you can do so by omitting the custom `--aks-custom-headers` tag.

## Generation 2 virtual machines (Preview)
Azure supports [Generation 2 (Gen2) virtual machines (VMs)](../virtual-machines/windows/generation-2.md). Generation 2 VMs support key features that aren't supported in generation 1 VMs (Gen1). These features include increased memory, Intel Software Guard Extensions (Intel SGX), and virtualized persistent memory (vPMEM).

Generation 2 VMs use the new UEFI-based boot architecture rather than the BIOS-based architecture used by generation 1 VMs.
Only specific SKUs and sizes support Gen2 VMs. Check the [list of supported sizes](../virtual-machines/windows/generation-2.md#generation-2-vm-sizes), to see if your SKU supports or requires Gen2.

Additionally not all VM images support Gen2, on AKS Gen2 VMs will use the new [AKS Ubuntu 18.04 image](#os-configuration-preview). This image supports all Gen2 SKUs and sizes.

To use Gen2 VMs during preview, you'll require:
- The `aks-preview` CLI extension installed.
- The `Gen2VMPreview` feature flag registered.

Register the `Gen2VMPreview` feature:

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

It might take several minutes for the status to show as **Registered**. You can check the registration status by using the [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) command:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```

When the status shows as registered, refresh the registration of the `Microsoft.ContainerService` resource provider by using the [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) command:

```azurecli
az provider register --namespace Microsoft.ContainerService
```

To install the aks-preview CLI extension, use the following Azure CLI commands:

```azurecli
az extension add --name aks-preview
```

To update the aks-preview CLI extension, use the following Azure CLI commands:

```azurecli
az extension update --name aks-preview
```

### New clusters
Configure the cluster to use Gen2 VMs for the selected SKU when the cluster is created. Use the `--aks-custom-headers` flag to set Gen2 as the VM generation on a new cluster.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

If you want to create a regular cluster using Generation 1 (Gen1) VMs, you can do so by omitting the custom `--aks-custom-headers` tag. You can also choose to add more Gen1 or Gen2 VMs as per below.

### Existing clusters
Configure a new node pool to use Gen2 VMs. Use the `--aks-custom-headers` flag to set Gen2 as the VM generation for that node pool.

```azure-cli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

If you want to create regular Gen1 node pools, you can do so by omitting the custom `--aks-custom-headers` tag.

## Custom resource group name

When you deploy an Azure Kubernetes Service cluster in Azure, a second resource group gets created for the worker nodes. By default, AKS will name the node resource group `MC_resourcegroupname_clustername_location`, but you can also provide your own name.

To specify your own resource group name, install the aks-preview Azure CLI extension version 0.3.2 or later. Using the Azure CLI, use the `--node-resource-group` parameter of the `az aks create` command to specify a custom name for the resource group. If you use an Azure Resource Manager template to deploy an AKS cluster, you can define the resource group name by using the `nodeResourceGroup` property.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

The secondary resource group is automatically created by the Azure resource provider in your own subscription. You can only specify the custom resource group name when the cluster is created. 

As you work with the node resource group, keep in mind that you can't:

- Specify an existing resource group for the node resource group.
- Specify a different subscription for the node resource group.
- Change the node resource group name after the cluster has been created.
- Specify names for the managed resources within the node resource group.
- Modify or delete Azure-created tags of managed resources within the node resource group.

## Next steps

- Learn how to use `Kured` to [apply security and kernel updates to Linux nodes](node-updates-kured.md) in your cluster.
- See [Upgrade an Azure Kubernetes Service (AKS) cluster](upgrade-cluster.md) to learn how to upgrade your cluster to the latest version of Kubernetes.
- See the list of [Frequently asked questions about AKS](faq.md) to find answers to some common AKS questions.