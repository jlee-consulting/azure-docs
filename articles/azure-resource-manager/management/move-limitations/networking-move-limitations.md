---
title: Move Azure Networking resources to new subscription or resource group
description: Use Azure Resource Manager to move virtual networks and other networking resources to a new resource group or subscription.
ms.topic: conceptual
ms.date: 03/31/2022
---

# Move networking resources to new resource group or subscription

This article describes how to move virtual networks and other networking resources to a new resource group or Azure subscription.

During the move, your networking resources will operate without interruption.

If you want to move networking resources to a new region, see [Tutorial: Move Azure VMs across regions](../../../resource-mover/tutorial-move-region-virtual-machines.md).

## Dependent resources

> [!NOTE]
> Please note that VPN Gateways associated with Public IP Standard SKU addresses are not currently able to move between resource groups or subscriptions.

> [!NOTE]
> Please note that any resource associated with Public IP Standard SKU addresses are not currently able to move across subscriptions.

When moving a resource, you must also move its dependent resources (for example - public IP addresses, virtual network gateways, all associated connection resources). Local network gateways can be in a different resource group.

To move a virtual machine with a network interface card to a new subscription, you must move all dependent resources. Move the virtual network for the network interface card, all other network interface cards for the virtual network, and the VPN gateways.

For more information, see [Scenario for move across subscriptions](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions).

## Peered virtual network

To move a peered virtual network, you must first disable the virtual network peering. Once disabled, you can move the virtual network. After the move, reenable the virtual network peering.

## Subnet links

You can't move a virtual network to a different subscription if the virtual network contains a subnet with resource navigation links. For example, if an Azure Cache for Redis resource is deployed into a subnet, that subnet has a resource navigation link.

## Next steps

For commands to move resources, see [Move resources to new resource group or subscription](../move-resource-group-and-subscription.md).
