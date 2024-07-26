---
title: Diagnose a VM network routing problem - Azure PowerShell
titleSuffix: Azure Network Watcher
description: In this article, you learn how to diagnose a virtual machine network routing problem using the next hop capability of Azure Network Watcher.
services: network-watcher
author: halkazwini
ms.service: network-watcher
ms.topic: how-to
ms.tgt_pltfrm: network-watcher
ms.date: 01/07/2021
ms.author: halkazwini
ms.custom: devx-track-azurepowershell
# Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
---

# Diagnose a virtual machine network routing problem - Azure PowerShell

In this article, you deploy a virtual machine (VM), and then check communications to an IP address and URL. You determine the cause of a communication failure and how you can resolve it.

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

[!INCLUDE [cloud-shell-try-it.md](~/reusable-content/ce-skilling/azure/includes/cloud-shell-try-it.md)]

If you choose to install and use PowerShell locally, this article requires the Az PowerShell module. For more information, see [How to install Azure PowerShell](/powershell/azure/install-azure-powershell). To find the installed version, run `Get-InstalledModule -Name Az`. If you run PowerShell locally, sign in to Azure using the [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet.

## Create a VM

Before you can create a VM, you must create a resource group to contain the VM. Create a resource group with [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). The following example creates a resource group named *myResourceGroup* in the *eastus* location.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Create the VM with [New-AzVM](/powershell/module/az.compute/new-azvm). When running this step, you are prompted for credentials. The values that you enter are configured as the user name and password for the VM.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

The VM takes a few minutes to create. Don't continue with remaining steps until the VM is created and PowerShell returns output.

## Test network communication

To test network communication with Network Watcher, you must first enable a network watcher in the region the VM that you want to test is in, and then use Network Watcher's next hop capability to test communication.

## Enable network watcher

If you already have a network watcher enabled in the East US region, use [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher) to retrieve the network watcher. The following example retrieves an existing network watcher named *NetworkWatcher_eastus* that is in the *NetworkWatcherRG* resource group:

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

If you don't already have a network watcher enabled in the East US region, use [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher) to create a network watcher in the East US region:

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### Use next hop

Azure automatically creates routes to default destinations. You may create custom routes that override the default routes. Sometimes, custom routes can cause communication to fail. To test routing from a VM, use the [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) command to determine the next routing hop when traffic is destined for a specific address.

Test outbound communication from the VM to one of the IP addresses for www.bing.com:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

After a few seconds, the output informs you that the **NextHopType** is **Internet**, and that the **RouteTableId** is **System Route**. This result lets you know that there is a valid route to the destination.

Test outbound communication from the VM to 172.31.0.100:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

The output returned informs you that **None** is the **NextHopType**, and that the **RouteTableId** is also **System Route**. This result lets you know that, while there is a valid system route to the destination, there is no next hop to route the traffic to the destination.

## View details of a route

To analyze routing further, review the effective routes for the network interface with the [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) command:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

Output that includes the following text is returned:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

As you can see in the previous output, the route with the **AddressPrefix** of **0.0.0.0/0** routes all traffic not destined for addresses within other route's address prefixes with a next hop of **Internet**. As you can also see in the output, though there is a default route to the 172.16.0.0/12 prefix, which includes the 172.31.0.100 address, the **nextHopType** is **None**. Azure creates a default route to 172.16.0.0/12, but doesn't specify a next hop type until there is a reason to. If, for example, you added the 172.16.0.0/12 address range to the address space of the virtual network, Azure changes the **nextHopType** to **Virtual network** for the route. A check would then show **Virtual network** as the **nextHopType**.

## Clean up resources

When no longer needed, you can use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) to remove the resource group and all of the resources it contains:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## Next steps

In this article, you created a VM and diagnosed network routing from the VM. You learned that Azure creates several default routes and tested routing to two different destinations. Learn more about [routing in Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) and how to [create custom routes](../virtual-network/manage-route-table.yml?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

For outbound VM connections, you can also determine the latency and allowed and denied network traffic between the VM and an endpoint using Network Watcher's [connection troubleshoot](network-watcher-connectivity-powershell.md) capability. You can monitor communication between a VM and an endpoint, such as an IP address or URL over time using the Network Watcher connection monitor capability. For more information, see [Monitor a network connection](monitor-vm-communication.md).
