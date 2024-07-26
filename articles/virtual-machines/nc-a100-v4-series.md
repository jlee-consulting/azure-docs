---
title: NC A100 v4-series 
description: Specifications for the NC A100 v4-series Azure VMs. These VMs include Linux, Windows, Flexible scale sets, and uniform scale sets.```
author: sherrywangms
ms.author: sherrywang
ms.service: virtual-machines
ms.subservice: sizes
ms.custom: linux-related-content
ms.topic: conceptual
ms.date: 09/19/2023
---

#  NC A100 v4-series  

> [!CAUTION]
> This article references CentOS, a Linux distribution that is End Of Life (EOL) status. Please consider your use and plan accordingly. For more information, see the [CentOS End Of Life guidance](~/articles/virtual-machines/workloads/centos/centos-end-of-life.md).

**Applies to:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: Flexible scale sets :heavy_check_mark: Uniform scale sets

The NC A100 v4 series virtual machine (VM) is a new addition to the Azure GPU family. You can use this series for real-world Azure Applied AI training and batch inference workloads. 

The NC A100 v4 series is powered by NVIDIA A100 PCIe GPU and third generation AMD EPYC™ 7V13 (Milan) processors.  The VMs feature up to 4 NVIDIA A100 PCIe GPUs with 80 GB memory each, up to 96 non-multithreaded AMD EPYC Milan processor cores and 880 GiB of system memory. 
These VMs are ideal for real-world Applied AI workloads, such as: 

- GPU-accelerated analytics and databases
- Batch inferencing with heavy pre- and post-processing
- Autonomy model training
- Oil and gas reservoir simulation
- Machine learning (ML) development
- Video processing
- AI/ML web services



## Supported features

To get started with NC A100 v4 VMs, refer to [HPC Workload Configuration and Optimization](configure.md) for steps including driver and network configuration.

Due to increased GPU memory I/O footprint, the NC A100 v4 requires the use of [Generation 2 VMs](generation-2.md) and marketplace images. While the [Azure HPC images](configure.md) are  recommended, Azure HPC Ubuntu 20.04 and Azure HPC CentOS 7.9, RHEL 8.8, RHEL 9.2, Windows Server 2019, and Windows Server 2022 images are supported.

- [Premium Storage](premium-storage-performance.md): Supported
- [Premium Storage caching](premium-storage-performance.md): Supported
- [Ultra Disks](disks-types.md#ultra-disks): Not Supported
- [Live Migration](maintenance-and-updates.md): Not Supported
- [Memory Preserving Updates](maintenance-and-updates.md): Not Supported
- [VM Generation Support](generation-2.md): Generation 2
- [Accelerated Networking](../virtual-network/create-vm-accelerated-networking-cli.md): Supported
- [Ephemeral OS Disks](ephemeral-os-disks.md): Supported
- InfiniBand: Not Supported
- NVIDIA NVLink Interconnect: Supported
- [Nested Virtualization](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): Not Supported



| Size | vCPU | Memory (GiB) | Temp Disk<sup>1</sup> (GiB)  | NVMe Disks<sup>2</sup> | GPU<sup>3</sup> | GPU Memory (GiB) | Max data disks | Max uncached disk throughput (IOPS / MBps) | Max NICs/network bandwidth (MBps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NC24ads_A100_v4   | 24  | 220 |64 | 960 GB | 1 | 80  | 8 | 30000/1000 | 2/20,000  |
| Standard_NC48ads_A100_v4   | 48 | 440 | 128| 2x960 GB| 2 | 160 | 16 | 60000/2000 | 4/40,000  | 
| Standard_NC96ads_A100_v4   | 96 | 880 | 256| 4x960 GB | 4 | 320 | 32 | 120000/4000 | 8/80,000  |

<sup>1</sup>  NC A100 v4 series VMs have a standard SCSI based temp resource disk for OS paging/swap file use. This ensures the NVMe drives can be fully dedicated to application use. This disk is Ephemeral, and all data will be lost on stop/deallocate.

<sup>2</sup> Local NVMe disks are ephemeral, data will be lost on these disks if you stop/deallocate your VM. Local NVMe disk is coming as RAM and it needs to be manually formatted in newly deployed VM.

<sup>3</sup>1 GPU = one A100 80GB PCIe GPU card <br>

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## Other sizes and information

- [General purpose](sizes-general.md)
- [Memory optimized](sizes-memory.md)
- [Storage optimized](sizes-storage.md)
- [GPU optimized](sizes-gpu.md)
- [High performance compute](sizes-hpc.md)
- [Previous generations](sizes-previous-gen.md)

You can [use the pricing calculator](https://azure.microsoft.com/pricing/calculator/) to estimate your Azure VMs costs.

For more information on disk types, see [What disk types are available in Azure?](disks-types.md)

## Next step

- [Compare compute performance across Azure SKUs with Azure compute units (ACU)](acu.md)
