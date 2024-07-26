---
title: Convert a disk from LRS to ZRS
description: Learn how to convert a managed disk from locally redundant storage to zone-redundant storage.
author: roygara
ms.service: azure-disk-storage
ms.topic: conceptual
ms.date: 10/19/2023
ms.author: rogarana
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
---

# Convert a disk from LRS to ZRS

This article explains how to convert an existing disk from locally redundant storage (LRS) to zone-redundant storage (ZRS).

For conceptual information on ZRS, see [Zone-redundant storage for managed disks](disks-redundancy.md#zone-redundant-storage-for-managed-disks)

## Limitations

[!INCLUDE [disk-storage-zrs-limitations](../../includes/disk-storage-zrs-limitations.md)]

## Regional availability

[!INCLUDE [disk-storage-zrs-regions](../../includes/disk-storage-zrs-regions.md)]

## Determine infrastructure redundancy

Before you convert a disk from LRS to ZRS, you must first identify whether the disk is zonal or regional. How you migrate your disk changes based on whether it's zonal or regional.

# [Portal](#tab/azure-portal)

1. Sign in to the Azure portal
1. Navigate to your disk and look under **Availability zone** on the **Overview** tab.
    1. If the value is **No infrastructure redundancy required** you have a regional disk. Use [Regional migration](#regional-migration) to migrate your disk.
    1. If the value is anything else, you have a zonal disk. Use [Zonal migration](#zonal-migration) to migrate your disk.

# [Azure PowerShell](#tab/azure-powershell)

Run the following command and observe the output. If there's no value in **Zones**, you have a regional disk. Use [Regional migration](#regional-migration) to migrate your disk. Otherwise, you have a zonal disk. Use [Zonal migration](#zonal-migration) to migrate your disk.

```azurepowershell
Get-AzDisk -DiskName your_disk_name_here -ResourceGroupName your_RG_name_here
```

# [Azure CLI](#tab/azure-cli)

Run the following command and observe the output. If there's no value in **zones** you have a regional disk. Use [Regional migration](#regional-migration) to migrate your disk. Otherwise, you have a zonal disk. Use [Zonal migration](#zonal-migration) to migrate your disk.

```azurecli
az disk show --name your_disk_name_here --resource-group your_RG_name_here
```

---

## Regional migration

You can directly change the disk type of regional disks with the following options.

# [Portal](#tab/azure-portal)

Follow these steps:

1. Sign in to the [Azure portal](https://portal.azure.com).
1. Select the VM from the list of **Virtual machines**.
1. If the VM isn't stopped, select **Stop** at the top of the VM **Overview** pane, and wait for the VM to stop.
1. In the pane for the VM, select **Disks** from the menu.
1. Select the disk that you want to convert.
1. Select **Size + performance** from the menu.
1. Change the **Account type** from the original disk type to the desired disk type.
1. Select **Save**, and close the disk pane.

The disk type conversion is instantaneous. You can start your VM after the conversion.

# [Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardSSD_ZRS or Premium_ZRS based on your scenario
$storageType = 'Premium_ZRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

# [Azure CLI](#tab/azure-cli)


 ```azurecli

#resource group that contains the managed disk
$rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between StandardSSD_ZRS or Premium_ZRS based on your scenario
sku='Premium_ZRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

---

## Zonal migration

In this section, you migrate the data from your current managed disks to ZRS managed disks.

If you have a zonal disk, you can't directly change its type. You must take a snapshot and use that snapshot to create a new ZRS disk.

#### Step 1: Create your snapshot

The easiest and cleanest way to create a snapshot is to take one while the VM is offline. See [Snapshots](../virtual-machines/backup-and-disaster-recovery-for-azure-iaas-disks.md#snapshots). If you choose this approach, some downtime should be expected. To create a snapshot of your VM using the Azure portal, PowerShell, or Azure CLI, see [Create a snapshot of a virtual hard disk](../virtual-machines/snapshot-copy-managed-disk.md)

If you're taking a snapshot of a disk that's attached to a running VM, read the guidance in [Snapshots](backup-and-disaster-recovery-for-azure-iaas-disks.md#snapshots) before continuing.

>[!NOTE]
> The source managed disks remain intact with their current configurations and you'll continue to be billed for them. To avoid this, you must manually delete the disks once you've finished your migration and confirmed the new disks are working. For more information, see [Find and delete unattached Azure managed and unmanaged disks](windows/find-unattached-disks.md).


#### Step 2: Migrate the data on your managed disks

Now that you have snapshots of your original disks, you can use them to create ZRS managed disks.

##### Migrate your data to ZRS managed disks

1. Create a ZRS managed disk from the source disk snapshot by using the following Azure CLI snippet: 

    ```azurecli
    # Create a new ZRS Managed Disks using the snapshot Id and the SKU supported   
    storageType=Premium_ZRS 
    location=westus2 

    az disk create --resource-group $resourceGroupName --name $diskName --sku $storageType --size-gb $diskSize --source $snapshotId 
    
    ```

#### Step 3: Create a new VM with your new disks

Now that you have migrated your data to ZRS managed disks or zonal managed disks, create a new VM with these new disks set as the OS and data disks:

```azurecli

    az vm create -g MyResourceGroup -n MyVm --attach-os-disk newZonalOSDiskCopy --attach-data-disks newZonalDataDiskCopy --os-type linux

```
