---
title: Remove a virtual machine scale set association from a Capacity Reservation group (preview)
description: Learn how to remove a virtual machine scale set from a Capacity Reservation group.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines #Required
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
---

# Remove a virtual machine scale set association from a Capacity Reservation group 

This article walks you through the steps of removing a virtual machine scale set association from a Capacity Reservation Group. To learn more about capacity reservations, see the [overview article](capacity-reservation-overview.md). 

Because both the VM and the underlying Capacity Reservation logically occupy capacity, Azure imposes some constraints on this process to avoid ambiguous allocation states and unexpected errors.  

There are two ways to change an association: 
- Option 1: Deallocate the Virtual machine scale set, change the Capacity Reservation Group property at the scale set level, and then update the underlying VMs
- Option 2: Update the reserved quantity to zero and then change the Capacity Reservation Group property

> [!IMPORTANT]
> Capacity Reservation is currently in public preview.
> This preview version is provided without a service-level agreement, and we don't recommend it for production workloads. Certain features might not be supported or might have constrained capabilities. 
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Register for Capacity Reservation 

Before you can use the Capacity Reservation feature, you must [register your subscription for the preview](capacity-reservation-overview.md#register-for-capacity-reservation). The registration may take several minutes to complete. You can use either Azure CLI or PowerShell to complete the feature registration.

> [!NOTE]
> On-demand Capacity Reservation is available for virtual machine scale sets in Uniform orchestration mode only in select regions. To check if your region is supported, go to [Uniform virtual machine scale set deployment tracker](https://aka.ms/vmssuniformdeploymenttracker).


## Deallocate the Virtual machine scale set

The first option is to deallocate the virtual machine scale set, change the Capacity Reservation Group property at the scale set level, and then update the underlying VMs. 

Go to [upgrade policies](#upgrade-policies) for more information about automatic, rolling, and manual upgrades. 

### [API](#tab/api1)

1. Deallocate the virtual machine scale set

    ```rest
    POST  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/deallocate?api-version=2021-04-01
    ```

1. Update the virtual machine scale set to remove association with the Capacity Reservation Group
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/update?api-version=2021-04-01
    ```
    In the request body, set the `capacityReservationGroup` property to empty to remove the virtual machine scale set association to the group:

    ```json
    {
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
            "capacityReservation": {
                "capacityReservationGroup":{
                    "id":""    
                }
            }
        }
    }
    }
    ```

### [PowerShell](#tab/powershell1)

1. Deallocate the virtual machine scale set. The following will deallocate all virtual machines within the scale set: 

    ```powershell-interactive
    Stop-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    ```

1. Update the scale set to remove association with the Capacity Reservation Group. Setting the `CapacityReservationGroupId` property to empty removes the association of scale set to the Capacity Reservation Group: 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myvmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId ""
    ```

To learn more, go to Azure PowerShell commands [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss), [Get-AzVmss](/powershell/module/az.compute/get-azvmss), and [Update-AzVmss](/powershell/module/az.compute/update-azvmss).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## Update the reserved quantity to zero 

The second option involves updating the reserved quantity to zero and then changing the Capacity Reservation Group property.

This option works well when the virtual machine scale set can’t be deallocated and when a reservation is no longer needed. For example, you may create a capacity reservation to temporarily assure capacity during a large-scale deployment. Once completed, the reservation is no longer needed. 

Go to [upgrade policies](#upgrade-policies) for more information about automatic, rolling, and manual upgrades. 

### [API](#tab/api2)

1. Update the reserved quantity to zero 

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/CapacityReservations/{CapacityReservationName}?api-version=2021-04-01
    ```

    In the request body, include the following:
    
    ```json
    {
    "sku": 
        {
        "capacity": 0
        }
    } 
    ```
    
    Note that `capacity` property is set to 0 above.

1. Update the virtual machine scale set to remove the association with the Capacity Reservation Group

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/update?api-version=2021-04-01
    ```

    In the request body, set the `capacityReservationGroup` property to empty to remove the association:
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
            "capacityReservation": {
                "capacityReservationGroup":{
                    "id":""
                }
            }
        }
    }
    }
    ```

### [PowerShell](#tab/powershell2)

>[!NOTE]
> The `Update-AzCapacityReservation` command is not available during the Preview. Use `New-AzCapacityReservation` to modify an existing capacity reservation.

1. Update reserved quantity to zero:

    ```powershell-interactive
    New-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    -Zone "1"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    -Sku "Standard_D2s_v3"
    -CapacityToReserve 0
    ```

2. Update the scale set to remove association with Capacity Reservation Group by setting the `CapacityReservationGroupId` property to empty: 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myvmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId ""
    ```

To learn more, go to Azure PowerShell commands [New-AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation), [Get-AzVmss](/powershell/module/az.compute/get-azvmss), and [Update-AzVmss](/powershell/module/az.compute/update-azvmss).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## Upgrade policies

- **Automatic Upgrade** – In this mode, the scale set VM instances are automatically dissociated from the Capacity Reservation Group without any further action from you.
- **Rolling Upgrade** – In this mode, the scale set VM instances are dissociated from the Capacity Reservation Group without any further action from you. However, they're updated in batches with an optional pause time between them.
- **Manual Upgrade** – In this mode, nothing happens to the scale set VM instances when the virtual machine scale set is updated. You'll need to individually remove each scale set VM by [upgrading it with the latest Scale Set model](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

## Next steps

> [!div class="nextstepaction"]
> [Learn about overallocating a Capacity Reservation](capacity-reservation-overallocate.md)