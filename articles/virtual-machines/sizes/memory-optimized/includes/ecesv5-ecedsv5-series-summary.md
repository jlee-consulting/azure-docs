---
title: ECesv5 and ECedsv5-series summary include
description: Include file containing a summary of the ECesv5 and ECedsv5-series size family.
services: virtual-machines
author: mattmcinnes
ms.topic: include
ms.service: virtual-machines
ms.subservice: sizes
ms.date: 04/18/2024
ms.author: mattmcinnes
ms.custom: include file
---
The ECesv5-series and ECedsv5-series are Azure confidential VMs that can be used to protect the confidentiality and integrity of your code and data while it's being processed in the public cloud. Organizations can use these VMs to seamlessly bring confidential workloads to the cloud without any code changes to the application. These machines are powered by Intel® 4th Generation Xeon® Scalable processors with Base Frequency of 2.1 GHz, All Core Turbo Frequency of reach 2.9 GHz and Intel® Advanced Matrix Extensions (AMX) for AI acceleration. Featuring Intel® Trust Domain Extensions (TDX), these VMs are hardened from the cloud virtualized environment by denying the hypervisor, other host management code and administrators access to the VM memory and state. It helps to protect VMs against a broad range of sophisticated hardware and software attacks. These VMs have native support for confidential disk encryption meaning organizations can encrypt their VM disks at boot with either a customer-managed key (CMK), or platform-managed key (PMK). This feature is fully integrated with Azure KeyVault or Azure Managed HSM with validation for FIPS 140-2 Level 3.