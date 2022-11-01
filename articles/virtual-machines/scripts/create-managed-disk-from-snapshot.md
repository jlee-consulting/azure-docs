---
title: Create managed disk from snapshot (Linux) - CLI sample
description: Azure CLI Script Sample - Create a managed disk from a snapshot
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag

tags: azure-service-management

ms.assetid:
ms.service: storage
ms.subservice: disks
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/23/2022
ms.author: ramankum
ms.custom: mvc
---

# Create a managed disk from a snapshot with CLI (Linux)

This script creates a managed disk from a snapshot. Use it to restore a virtual machine from snapshots of OS and data disks. Create OS and data managed disks from respective snapshots and then create a new virtual machine by attaching managed disks. You can also restore data disks of an existing VM by attaching data disks created from snapshots.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## Sample script

[!INCLUDE [cli-launch-cloud-shell-sign-in.md](../../../includes/cli-launch-cloud-shell-sign-in.md)]

### Run the script

:::code language="azurecli" source="~/azure_cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh" id="FullScript":::

## Clean up resources

Run the following command to remove the resource group, VM, and all related resources.

```azurecli-interactive
az group delete --name myResourceGroupName
```

## Sample reference

This script uses following commands to create a managed disk from a snapshot. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [az snapshot show](/cli/azure/snapshot) | Gets all the properties of a snapshot using the name and resource group properties of the snapshot. Id property is used to create managed disk.  |
| [az disk create](/cli/azure/disk) | Creates a managed disk using snapshot Id of a managed snapshot |

## Next steps

[Create a virtual machine by attaching a managed disk as OS disk](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

For more information on the Azure CLI, see [Azure CLI documentation](/cli/azure).

Additional virtual machine and managed disks CLI script samples can be found in the [Azure Linux VM documentation](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
