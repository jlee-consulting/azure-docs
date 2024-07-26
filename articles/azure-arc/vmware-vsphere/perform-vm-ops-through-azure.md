---
title: Perform VM operations on VMware VMs through Azure
description: Learn how to view the operations that you can do on VMware virtual machines and install the Log Analytics agent.
ms.topic: how-to 
ms.date: 03/12/2024
ms.service: azure-arc
ms.subservice: azure-arc-vmware-vsphere
author: PriskeyJeronika-MS
ms.author: v-gjeronika
manager: jsuri
---

# Manage VMware VMs in Azure through Arc-enabled VMware vSphere

In this article, you learn how to perform various operations on the Azure Arc-enabled VMware vSphere VMs such as:

- Start, stop, and restart a VM

- Control access and add Azure tags

- Add, remove, and update network interfaces

- Add, remove, and update disks and update VM size (CPU cores, memory)

- Enable guest management

- Install extensions (enabling guest management is required). All the [extensions](../servers/manage-vm-extensions.md#extensions) that are available with Arc-enabled Servers are supported.

:::image type="content" source="media/perform-vm-ops-through-azure/manage-virtual-machines.png" alt-text="Screenshot showing the VMware virtual machine operations." lightbox="media/perform-vm-ops-through-azure/manage-virtual-machines.png":::

To perform guest OS operations on Arc-enabled VMs, you must enable guest management on the VMs. When you enable guest management, the Arc Connected Machine Agent is installed on the VM.

## Enable guest management

Before you can install an extension, you must enable guest management on the VMware VM.  

1. Make sure your target machine:

   - is running a [supported operating system](../servers/prerequisites.md#supported-operating-systems).

   - is able to connect through the firewall to communicate over the internet and these [URLs](../servers/network-requirements.md#urls) aren't blocked.

   - has VMware tools installed and running.

   - is powered on and the resource bridge has network connectivity to the host running the VM.

   >[!NOTE]
   >If you're using a Linux VM, the account must not prompt for login on sudo commands. To override the prompt, from a terminal, run `sudo visudo` and add `<username> ALL=(ALL) NOPASSWD:ALL` to the end of the file.  Make sure to replace `<username>`.
   >
   >If your VM template has these changes incorporated, you won't need to do this for the VM created from that template.

1. From your browser, go to the [Azure portal](https://portal.azure.com).

2. Search for and select the VMware VM for which you want to enable guest management and select **Configuration**.

3. Select **Enable guest management** and provide the administrator username and password to enable guest management.  Then select **Apply**.

   For Linux, use the root account, and for Windows, use an account that is a member of the Local Administrators group.

## Delete a VM

If you no longer need the VM, you can delete it.

1. From your browser, go to the [Azure portal](https://portal.azure.com).

2. Search for and select the VM you want to delete.

3. In the selected VM's Overview page, select **Delete**.

4. When prompted, confirm that you want to delete it.

>[!NOTE]
>This also deletes the VM in your VMware vCenter.

## Next steps

[Tutorial - Create a VM using Azure Arc-enabled vSphere](quick-start-create-a-vm.md).
