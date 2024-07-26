---
title: In-place upgrade of Red Hat Enterprise Linux images on Azure
description: Learn how to do an in-place upgrade from Red Hat Enterprise 7.x images to the latest 8.x version.
author: ju-shim
ms.service: virtual-machines
ms.subservice: redhat
ms.custom: linux-related-content
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: jushiman
---

# Red Hat Enterprise Linux in-place upgrades

**Applies to:** :heavy_check_mark: Linux VMs


> [!CAUTION]
> Following the process in this article will cause a disconnection between the data plane and the **[control plane](/azure/architecture/guide/multitenant/considerations/control-planes)** of the virtual machine (VM). Azure capabilities such as **[Auto guest patching](/azure/virtual-machines/automatic-vm-guest-patching)**, **[Auto OS image upgrades](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)**, **[Hotpatching](/windows-server/get-started/hotpatch?toc=%2Fazure%2Fvirtual-machines%2Ftoc.json)**, and **[Azure Update Manager](/azure/update-manager/overview)** won't be available. To utilize these features, it's recommended to create a new VM using your preferred operating system instead of performing an in-place upgrade.
>[!Note]
> Offerings of SQL Server on Red Hat Enterprise Linux don't support in-place upgrades on Azure.

>[!Important]
> Take a snapshot of the image before you start the upgrade as a precaution.

## What is RHEL in-place upgrade?
During an in-place upgrade, the earlier RHEL OS major version will be replaced with the new RHEL OS major version without removing the earlier version first. The installed applications and utilities, along with the configurations and preferences, are incorporated into the new version.


## Upgrade from RHEL 7 VMs to RHEL 8 VMs
Instructions for an in-place upgrade from Red Hat Enterprise Linux 7 VMs to Red Hat Enterprise Linux 8 VMs on Azure is provided at the [Red Hat upgrading from RHEL 7 to RHEL 8 documentation here.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)

## Upgrade from RHEL 8 VMs to RHEL 9 VMs
Instructions for an in-place upgrade from Red Hat Enterprise Linux 8 VMs to Red Hat Enterprise Linux 9 VMs on Azure is provided at the [Red Hat upgrading from RHEL 8 to RHEL 9 documentation here.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/html/upgrading_from_rhel_8_to_rhel_9/index)

## Upgrade SAP environments from RHEL 7 VMs to RHEL 8 VMs
Instructions for an in-place upgrade from Red Hat Enterprise Linux 7 SAP VMs to Red Hat Enterprise Linux 8 SAP VMs on Azure is provided at the [Red Hat upgrading from RHEL 7 SAP to RHEL 8 SAP documentation here.](https://access.redhat.com/solutions/5154031)

## Upgrade SAP environments from RHEL 8 VMs to RHEL 9 VMs
Instructions for an in-place upgrade from Red Hat Enterprise Linux 8 SAP VMs to Red Hat Enterprise Linux 9 SAP VMs on Azure is provided at the [Red Hat upgrading from RHEL 8 SAP to RHEL 9 SAP documentation here.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux_for_sap_solutions/9/html/upgrading_sap_environments_from_rhel_8_to_rhel_9/index)




## Next steps
* Learn more about [Red Hat images in Azure](./redhat-images.md).
* Learn more about [Red Hat update infrastructure](./redhat-rhui.md).
* Learn more about the [RHEL BYOS offer](./byos.md).
* To learn more about the Red Hat in-place upgrade processes, see [Upgrading from RHEL 7 TO RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index) in the Red Hat documentation.
* To learn more about Red Hat support policies for all versions of RHEL, see [Red Hat Enterprise Linux life cycle](https://access.redhat.com/support/policy/updates/errata) in the Red Hat documentation.
