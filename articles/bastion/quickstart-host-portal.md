---
title: 'Quickstart: Deploy Bastion with default settings'
titleSuffix: Azure Bastion
description: Learn how to deploy Bastion with default settings from the Azure portal.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 06/05/2022
ms.author: cherylmc
ms.custom: ignite-fall-2021, mode-other
#Customer intent: As someone with a networking background, I want to connect to a virtual machine securely via RDP/SSH using a private IP address through my browser.
---

# Quickstart: Deploy Azure Bastion with default settings

In this quickstart, you'll learn how to deploy Azure Bastion with default settings to your virtual network using the Azure portal. After Bastion is deployed, you can connect (SSH/RDP) to virtual machines in the virtual network via Bastion using the private IP address of the VM. When you connect to a VM, it doesn't need a public IP address, client software, agent, or a special configuration.

In this quickstart, you deploy Bastion from your VM resource using the Azure portal. Bastion is deployed using default settings that are based on the virtual network in which your VM is located. You then connect to your VM using RDP/SSH connectivity and the VM's private IP address. If your VM has a public IP address that you don't need for anything else, you can remove it.

Azure Bastion is a PaaS service that's maintained for you, not a bastion host that you install on one of your VMs and maintain yourself. For more information about Azure Bastion, see [What is Azure Bastion?](bastion-overview.md)

## <a name="prereq"></a>Prerequisites

* **An Azure account with an active subscription**. If you don't have one, [create one for free](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* **A VM in a VNet**.

    When you deploy Bastion using default values, the values are pulled from the VNet in which your VM resides. This VM doesn't become a part of the Bastion deployment itself, but you do connect to it later in the exercise.

  * If you don't already have a VM in a VNet, create one using [Quickstart: Create a Windows VM](../virtual-machines/windows/quick-create-portal.md), or [Quickstart: Create a Linux VM](../virtual-machines/linux/quick-create-portal.md).
  * If you need example values, see the [Example values](#values) section.
  * If you already have a virtual network, make sure it's selected on the Networking tab when you create your VM.
  * If you don't have a virtual network, you can create one at the same time you create your VM.

* **Required VM roles:**

  * Reader role on the virtual machine.
  * Reader role on the NIC with private IP of the virtual machine.
  
* **Required VM ports inbound ports:**

  * 3389 for Windows VMs
  * 22 for Linux VMs

> [!NOTE]
> The use of Azure Bastion with Azure Private DNS Zones is not supported at this time. Before you begin, please make sure that the virtual network where you plan to deploy your Bastion resource is not linked to a private DNS zone.
>

### <a name="values"></a>Example values

You can use the following example values when creating this configuration, or you can substitute your own.

**Basic VNet and VM values:**

|**Name** | **Value** |
| --- | --- |
| Virtual machine| TestVM |
| Resource group | TestRG1 |
| Region | East US |
| Virtual network | VNet1 |
| Address space | 10.1.0.0/16 |
| Subnets | FrontEnd: 10.1.0.0/24 |

**Bastion values:**

When you deploy from VM settings, Bastion is automatically configured with default values.

 You can't modify or specify additional values for a default deployment. However, once Bastion deploys, you can later modify [settings](configuration-settings.md). For example, the default SKU is the Basic SKU. You can later upgrade to the Standard SKU to support more features.

|**Name** | **Default value** |
|---|---|
|AzureBastionSubnet | This subnet is created within the VNet as a /26 |
|SKU | Basic |
| Name | Based on  the virtual network name |
| Public IP address name | Based on the virtual network name |

## <a name="createvmset"></a>Deploy Bastion

In this quickstart, you deploy Bastion from your virtual machine settings in the Azure portal. You don't connect and sign in to your virtual machine or deploy Bastion from your VM directly.

1. Sign in to the [Azure portal](https://portal.azure.com).
1. In the portal, go to the VM to which you want to connect. The values from the virtual network in which this VM resides will be used to create the Bastion deployment. 
1. Select **Bastion** in the left menu. You can view some of the values that will be used when creating the bastion host for your virtual network. Select **Create Azure Bastion using defaults**.

   :::image type="content" source="./media/quickstart-host-portal/deploy-bastion.png" alt-text="Screenshot of Deploy Bastion." lightbox="./media/quickstart-host-portal/deploy-bastion.png":::
1. Bastion begins deploying. This can take around 10 minutes to complete.

   :::image type="content" source="./media/quickstart-host-portal/creating-bastion.png" alt-text="Screenshot of Bastion resources being created." lightbox="./media/quickstart-host-portal/creating-bastion.png":::

## <a name="connect"></a>Connect to a VM

When the Bastion deployment is complete, the screen changes to the **Connect** page.

1. Type the username and password for your virtual machine. Then, select **Connect**.

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="Screenshot shows the Connect using Azure Bastion dialog." lightbox="./media/quickstart-host-portal/connect-vm.png":::
1. The connection to this virtual machine via Bastion will open directly in the Azure portal (over HTML5) using port 443 and the Bastion service. Select **Allow** when asked for permissions to the clipboard. This lets you use the remote clipboard arrows on the left of the screen.

   * When you connect, the desktop of the VM may look different than the example screenshot.
   * Using keyboard shortcut keys while connected to a VM may not result in the same behavior as shortcut keys on a local computer. For example, when connected to a Windows VM from a Windows client, CTRL+ALT+END is the keyboard shortcut for CTRL+ALT+Delete on a local computer. To do this from a Mac while connected to a Windows VM, the keyboard shortcut is Fn+CTRL+ALT+Backspace.

     :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="Screenshot of RDP connection." lightbox="./media/quickstart-host-portal/connected.png":::

### <a name="audio"></a>To enable audio output

[!INCLUDE [Enable VM audio output](../../includes/bastion-vm-audio.md)]

## <a name="remove"></a>Remove VM public IP address

[!INCLUDE [Remove a public IP address from a VM](../../includes/bastion-remove-ip.md)]

## Clean up resources

When you're done using the virtual network and the virtual machines, delete the resource group and all of the resources it contains:

1. Enter the name of your resource group in the **Search** box at the top of the portal and select it from the search results.

1. Select **Delete resource group**.

1. Enter your resource group for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## Next steps

In this quickstart, you deployed Bastion to your virtual network, and then connected to a virtual machine securely via Bastion. Next, you can continue with the following steps if you want to copy and paste to your VM.

> [!div class="nextstepaction"]
> [Copy and paste to a Windows VM](bastion-vm-copy-paste.md)