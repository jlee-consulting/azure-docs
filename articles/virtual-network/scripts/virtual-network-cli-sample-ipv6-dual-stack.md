---
title: Azure CLI script sample - Configure IPv6 frontend
titlesuffix: Azure Virtual Network
description: Use an Azure CLI script sample to configure IPv6 endpoints and deploy a dual stack (IPv4 + IPv6) application in Azure.
services: virtual-network
documentationcenter: na
author: mbender-ms
manager: twooley
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2022
ms.author: mbender 
ms.custom: devx-track-azurecli
---

# Configure IPv6 endpoints in virtual network script sample

This article shows you how to deploy a dual stack (IPv4 + IPv6) application in Azure that includes a dual stack virtual network with a dual stack subnet, a load balancer with dual (IPv4 + IPv6) front-end configurations, VMs with NICs that have a dual IP configuration, dual network security group rules ,and dual public IPs.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## Sample script

[!INCLUDE [cli-launch-cloud-shell-sign-in.md](../../../includes/cli-launch-cloud-shell-sign-in.md)]

### Run the script

:::code language="azurecli" source="~/azure_cli_scripts/virtual-network/virtual-network-cli-sample-ipv6-dual-stack/virtual-network-cli-sample-ipv6-dual-stack.sh" id="FullScript":::

> [!TIP]
> You can view the IPv6 dual stack virtual network in Azure portal on the virtual network page.
> The dual stack virtual network shows the two NICs with both IPv4 and IPv6 configurations in the dual stack subnet.

## Clean up deployment

[!INCLUDE [cli-clean-up-resources.md](../../../includes/cli-clean-up-resources.md)]

```azurecli
az group delete --name $resourceGroup
```

## Sample reference

This script uses the following commands to create a resource group, virtual machine, availability set, load balancer, and all related resources. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Creates a resource group in which all resources are stored. |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Creates an Azure virtual network and subnet. |
| [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) | Creates a public IP address with a static IP address and an associated DNS name. |
| [az network lb create](/cli/azure/network/lb#az-network-lb-create) | Creates an Azure load balancer. |
| [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) | Creates a load balancer probe. A load balancer probe is used to monitor each VM in the load balancer set. If any VM becomes inaccessible, traffic is not routed to the VM. |
| [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) | Creates a load balancer rule. In this sample, a rule is created for port 80. As HTTP traffic arrives at the load balancer, it is routed to port 80 one of the VMs in the LB set. |
| [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#az-network-lb-inbound-nat-rule-create) | Creates load balancer Network Address Translation (NAT) rule.  NAT rules map a port of the load balancer to a port on a VM. In this sample, a NAT rule is created for SSH traffic to each VM in the load balancer set.  |
| [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) | Creates a network security group (NSG), which is a security boundary between the internet and the virtual machine. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) | Creates an NSG rule to allow inbound traffic. In this sample, port 22 is opened for SSH traffic. |
| [az network nic create](/cli/azure/network/nic#az-network-nic-create) | Creates a virtual network card and attaches it to the virtual network, subnet, and NSG. |
| [az vm availability-set create](/cli/azure/network/lb/rule#az-network-lb-rule-create) | Creates an availability set. Availability sets ensure application uptime by spreading the virtual machines across physical resources such that if failure occurs, the entire set isn't affected. |
| [az vm create](/cli/azure/vm#az-vm-create) | Creates the virtual machine and connects it to the network card, virtual network, subnet, and NSG. This command also specifies the virtual machine image to be used and administrative credentials.  |
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Deletes a resource group including all nested resources. |

## Next steps

For more information on the Azure CLI, see [Azure CLI documentation](/cli/azure).

Additional Azure Networking CLI script samples can be found in the [Azure Networking documentation](../cli-samples.md).
