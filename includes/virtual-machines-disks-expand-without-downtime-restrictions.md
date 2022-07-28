---
 title: include file
 description: include file
 services: virtual-machines
 author: roygara
 ms.service: virtual-machines
 ms.topic: include
 ms.date: 07/27/2022
 ms.author: rogarana
 ms.custom: include file
---    
- Only supported for data disks.
- If a disk is 4 TiB or less, you can't expand it beyond 4 TiB without deallocating the VM. If a disk is already greater than 4 TiB, you can expand it without deallocating the VM.
- Not supported for Ultra disks 
- Not supported for shared disks
- Install and use either:
    - The [latest Azure CLI](/cli/azure/install-azure-cli)
    - The [latest Azure PowerShell module](/powershell/azure/install-az-ps)
    - The Azure portal if accessed through [https://aka.ms/iaasexp/DiskLiveResize](https://aka.ms/iaasexp/DiskLiveResize)
    - Or an Azure Resource Manager template with an API version that's 2021-04-01 or newer.
