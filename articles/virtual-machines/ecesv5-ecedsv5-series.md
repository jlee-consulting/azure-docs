---
title: Azure ECesv5 and ECedsv5-series confidential virtual machines
description: Specifications for Azure Confidential Computing's ECesv5 and ECedsv5-series confidential virtual machines.
author: michamcr
ms.author: mmcrey
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.custom:
  - ignite-2023
ms.topic: conceptual
ms.date: 11/14/2023
---

# ECesv5 and ECedsv5-series confidential VMs

**Applies to:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs 

> [!IMPORTANT]
> These virtual machines are in public preview and not recommended for production usage.
> These VMs are available in West Europe, Central US, East US 2 and North Europe.

The ECesv5-series and ECedsv5-series are [Azure confidential VMs](../confidential-computing/confidential-vm-overview.md) that can be used to protect the confidentiality and integrity of your code and data while it's being processed in the public cloud. Organizations can use these VMs to seamlessly bring confidential workloads to the cloud without any code changes to the application. 

These machines are powered by Intel® 4th Generation Xeon® Scalable processors with Base Frequency of 2.1 GHz, All Core Turbo Frequency of reach 2.9 GHz and [Intel® Advanced Matrix Extensions (AMX)](https://www.intel.com/content/www/us/en/products/docs/accelerator-engines/advanced-matrix-extensions/overview.html) for AI acceleration. 

Featuring [Intel® Trust Domain Extensions (TDX)](https://www.intel.com/content/www/us/en/developer/tools/trust-domain-extensions/overview.html), these VMs are hardened from the cloud virtualized environment by denying the hypervisor, other host management code and administrators access to the VM memory and state. It helps to protect VMs against a broad range of sophisticated [hardware and software attacks](https://www.intel.com/content/www/us/en/developer/articles/technical/intel-trust-domain-extensions.html). 

These VMs have native support for [confidential disk encryption](disk-encryption-overview.md) meaning organizations can encrypt their VM disks at boot with either a customer-managed key (CMK), or platform-managed key (PMK). This feature is fully integrated with [Azure KeyVault](../key-vault/general/overview.md) or [Azure Managed HSM](../key-vault/managed-hsm/overview.md) with validation for FIPS 140-2 Level 3. 

> [!NOTE]
> There are some [pricing differences based on your encryption settings](../confidential-computing/confidential-vm-overview.md#encryption-pricing-differences) for confidential VMs.

> [!NOTE]
> Certain applications which are time sensitive may experience asynchronous time at VM boot. Whilst a long-term fix is in development, a [workaround is available](../confidential-computing/confidential-vm-faq.yml#what-can-i-do-if-the-time-on-my-dcesv5-ecesv5-series-vm-differs-from-utc-) for Linux and Windows customers today. If you need additional support, please create a support request.

### ECesv5 and ECedsv5-series feature support

*Supported* features in ECesv5-series VMs:

- [Premium Storage](premium-storage-performance.md)
- [Premium Storage caching](premium-storage-performance.md)
- [VM Generation 2](generation-2.md)
- [Ephemeral OS Disk](ephemeral-os-disks.md) - ECedsv5 only

*Unsupported* features in ECesv5-series VMs:

- [Live Migration](maintenance-and-updates.md)
- [Memory Preserving Updates](maintenance-and-updates.md)
- [Accelerated Networking](../virtual-network/create-vm-accelerated-networking-cli.md)
- [Ephemeral OS Disks](ephemeral-os-disks.md) - ECedsv5 only
- [Nested Virtualization](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)

## ECesv5-series

The ECesv5 VMs offer even higher memory to vCPU ratio and an all new VM size with up to 128 vCPUs and 768 GiB of RAM. If you require a local disk, please consider ECedsv5-series. These VMs are ideal for memory intensive applications, large relational database servers, business intelligence applications, and critical applications that process sensitive and regulated data. 

This series supports Standard SSD, Standard HDD, and Premium SSD disk types. Billing for disk storage and VMs is separate. To estimate your costs, use the [Pricing Calculator](https://azure.microsoft.com/pricing/calculator/). 

### ECesv5-series specifications

| Size | vCPU | RAM (GiB) | Temp storage (SSD) GiB | Max data disks | Max temp disk throughput IOPS/MBps | Max uncached disk throughput IOPS/MBps | Max burst uncached disk throughput: IOPS/MBps | Max NICs | Max Network Bandwidth (Mbps) |
|:------:|:----:|:---------:|:------------------------:|:--------------:|:-------------------------------------:|:--------------------------------------:|:-----------------------------------------------:|:--------:|:-------------------------------------:|
| Standard_EC2es_v5 | 2 | 16 | RS* | 4 | N/A | 3750/80 | 10000/1200 | 2 | 3000 |
| Standard_EC4es_v5 | 4 | 32 | RS* | 8 | N/A | 6400/140 | 20000/1200 | 2 | 5000 |
| Standard_EC8es_v5 | 8 | 64 | RS* | 16 | N/A | 12800/300 | 20000/1200 | 4 | 5000 |
| Standard_EC16es_v5 | 16 | 128 | RS* | 32 | N/A | 25600/600  	|40000/1200  	|8  	|10000  	|
| Standard_EC32es_v5  	|32  	|256  	|RS*  	|32  	| N/A  	|51200/860  	|80000/2000  	|8  	|12500  	|
| Standard_EC48es_v5  	|48  	|384  	|RS*  	|32  	| N/A  	|76800/1320  	|80000/3000  	|8  	|15000  	|
| Standard_EC64es_v5  	|64  	|512  	|RS*  	|32  	| N/A  	|80000/1740  	|80000/3000  	|8  	|20000    |
| Standard_EC128es_v5   	|128   	|768    |RS*   	    |32    | N/A   	    |80000/2600   	    |120000/4000   	    |8   	    |30000 |

*RS: These VMs have support for remote storage only

## ECedsv5-series

The ECedsv5 VMs offer even higher memory to vCPU ratio and an all new VM size with up to 128 vCPUs and 768 GiB of RAM, as well as up to 2.8 TB local disk storage. These VMs are ideal for memory intensive applications, large relational database servers, business intelligence applications, and critical applications that process sensitive and regulated data. 

This series supports Standard SSD, Standard HDD, and Premium SSD disk types. Billing for disk storage and VMs is separate. To estimate your costs, use the [Pricing Calculator](https://azure.microsoft.com/pricing/calculator/).

### ECedsv5-series specifications

| Size | vCPU | RAM (GiB) | Temp storage (SSD) GiB | Max data disks | Max temp disk throughput IOPS/MBps | Max uncached disk throughput IOPS/MBps | Max burst uncached disk throughput: IOPS/MBps | Max NICs | Max Network Bandwidth (Mbps) |
|:------:|:----:|:---------:|:------------------------:|:--------------:|:-------------------------------------:|:--------------------------------------:|:-----------------------------------------------:|:--------:|:-------------------------------------:|
| Standard_EC2eds_v5  	|2  	|16  	|47  	|4  	|9300/100  	|3750/80  	| 10000/1200 | 2 | 3000 |
| Standard_EC4eds_v5  	|4  	|32  	|105  	|8  	|19500/200  	|6400/140  	| 20000/1200 | 2 | 5000 |
| Standard_EC8eds_v5  	|8  	|64  	|227  	|16  	|38900/500  	|12800/300  	| 20000/1200 | 4 | 5000 |
| Standard_EC16eds_v5  |16  |128  |463  |32  |76700/1000  |25600/600  |40000/1200  	|8  	|10000  	|
| Standard_EC32eds_v5  |32  |256  |935  |32  |153200/2000  |51200/860  |80000/2000  	|8  	|12500  	|
| Standard_EC48eds_v5  |48  |384  |1407  |32  |229700/3000  |76800/1320  |80000/3000  	|8  	|15000  	|
| Standard_EC64eds_v5  |64  |512  |2823  |32  |306200/4000  |80000/1740  |80000/3000  	|8  	|20000    |
| Standard_EC128eds_v5   	|128 |768 |2832 |32 |459200/4000 |80000/2600 |120000/4000   	    |8   	    |30000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## Next steps

> [!div class="nextstepaction"]
> [Create a confidential VM in Azure Portal](../confidential-computing/quick-create-confidential-vm-portal.md)
> [Create a confidential VM in Azure CLI](../confidential-computing/quick-create-confidential-vm-azure-cli.md)
