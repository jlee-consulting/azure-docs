---
title: 'Tutorial: Deploy Bastion using manual settings: Azure portal'
description: Learn how to deploy Bastion using manual settings using the Azure portal.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 05/05/2022
ms.author: cherylmc
ms.custom: ignite-fall-2021
---

# Tutorial: Deploy Bastion using manual settings

This tutorial helps you deploy Azure Bastion from the Azure portal using manual settings. When you use manual settings, you can specify configuration values such as instance counts and the SKU at the time of deployment. After Bastion is deployed, you can connect (SSH/RDP) to virtual machines in the virtual network via Bastion using the private IP address of the VM. When you connect to a VM, it doesn't need a public IP address, client software, agent, or a special configuration.

In this tutorial, you deploy Bastion using the Standard SKU tier and adjust host scaling (instance count). After the deployment is complete, you connect to your VM via private IP address. If your VM has a public IP address that you don't need for anything else, you can remove it.

Azure Bastion is a PaaS service that's maintained for you, not a bastion host that you install on one of your VMs and maintain yourself. For more information about Azure Bastion, see [What is Azure Bastion?](bastion-overview.md)

In this tutorial, you'll learn how to:

> [!div class="checklist"]
> * Deploy Bastion to your VNet.
> * Connect to a virtual machine.
> * Remove the public IP address from a virtual machine.

If you don’t have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Prerequisites

* A [virtual network](../virtual-network/quick-create-portal.md). This will be the VNet to which you deploy Bastion.
* A virtual machine in the virtual network. This VM isn't a part of the Bastion configuration and doesn't become a bastion host. You connect to this VM later in this tutorial via Bastion. If you don't have a VM, create one using [Quickstart: Create a VM](../virtual-machines/windows/quick-create-portal.md).
* **Required VM roles:**
    * Reader role on the virtual machine.
    * Reader role on the NIC with private IP of the virtual machine.

* **Required inbound ports:**
    * For Windows VMs - RDP (3389)
    * For Linux VMs - SSH (22)

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

**Azure Bastion values:**

|**Name** | **Value** |
| --- | --- |
| Name | VNet1-bastion |
| + Subnet Name | AzureBastionSubnet |
| AzureBastionSubnet addresses | A subnet within your VNet address space with a subnet mask /26 or larger.<br> For example, 10.1.1.0/26.  |
| Tier/SKU | Standard |
| Instance count (host scaling)| 3 or greater |
| Public IP address |  Create new |
| Public IP address name | VNet1-ip  |
| Public IP address SKU |  Standard  |
| Assignment  | Static |

 > [!IMPORTANT]
 > For Azure Bastion resources deployed on or after November 2, 2021, the minimum AzureBastionSubnet size is /26 or larger (/25, /24, etc.). All Azure Bastion resources deployed in subnets of size /27 prior to this date are unaffected by this change and will continue to work, but we highly recommend increasing the size of any existing AzureBastionSubnet to /26 in case you choose to take advantage of [host scaling](./configure-host-scaling.md) in the future.
 >

## <a name="createhost"></a>Deploy Bastion

This section helps you deploy Bastion to your VNet. Once Bastion is deployed, you can connect securely to any VM in the VNet using its private IP address.

1. Sign in to the [Azure portal](https://portal.azure.com).

1. Go to your VNet.

1. Select **Bastion** in the left pane to open the **Bastion** page.

1. On the Bastion page, select **Configure manually**. This lets you configure specific additional settings before deploying Bastion to your VNet.
   :::image type="content" source="./media/tutorial-create-host-portal/configure-manually.png" alt-text="Screenshot of Bastion page showing configure manually button." lightbox="./media/tutorial-create-host-portal/configure-manually.png":::

1. On the **Create a Bastion** page, configure the settings for your bastion host. Project details are populated from your virtual network values. Configure the **Instance details** values.

   * **Name**: Type the name that you want to use for your bastion resource.

   * **Region**: The Azure public region in which the resource will be created. Choose the region in which your virtual network resides.

   * **Tier:** The tier is also known as the **SKU**. For this tutorial, select **Standard**. The Standard SKU lets you configure the instance count for host scaling and other features. For more information about features that require the Standard SKU, see [Configuration settings - SKU](configuration-settings.md#skus).

   * **Instance count:** This is the setting for **host scaling**. It's configured in scale unit increments. Use the slider or type a number to configure the instance count that you want. For this tutorial, you can select the instance count you'd prefer. For more information, see [Host scaling](configuration-settings.md#instance) and [Pricing](https://azure.microsoft.com/pricing/details/azure-bastion).

   :::image type="content" source="./media/tutorial-create-host-portal/instance-values.png" alt-text="Screenshot of Bastion page instance values." lightbox="./media/tutorial-create-host-portal/instance-values.png":::

1. Configure the **virtual networks** settings. Select the VNet from the dropdown. If you don't see your VNet in the dropdown list, make sure you selected the correct Region in the previous settings on this page.

1. To configure the AzureBastionSubnet, select **Manage subnet configuration**.

   :::image type="content" source="./media/tutorial-create-host-portal/select-vnet.png" alt-text="Screenshot of configure virtual networks section." lightbox="./media/tutorial-create-host-portal/select-vnet.png":::

1. On the **Subnets** page, select **+Subnet** to open the **Add subnet** page.

1. On the **Add subnet page**, create the 'AzureBastionSubnet' subnet using the following values. Leave the other values as default.

   * The subnet name must be **AzureBastionSubnet**.
   * The subnet must be at least **/26 or larger** (/26, /25, /24 etc.) to accommodate features available with the Standard SKU.

   Select **Save** at the bottom of the page to save your values.

1. At the top of the **Subnets** page, select **Create a Bastion** to return to the Bastion configuration page.

   :::image type="content" source="./media/tutorial-create-host-portal/create-a-bastion.png" alt-text="Screenshot of Create a Bastion."lightbox="./media/tutorial-create-host-portal/create-a-bastion.png":::

1. The public IP address section is where you configure the public IP address of the Bastion host resource on which RDP/SSH will be accessed (over port 443). The public IP address must be in the same region as the Bastion resource you're creating. This IP address doesn't have anything to do with any of the VMs that you want to connect to. Create a new IP address. You can leave the default naming suggestion.

1. When you finish specifying the settings, select **Review + Create**. This validates the values.

1. Once validation passes, you can deploy Bastion. Select **Create**.  You'll see a message letting you know that your deployment is in process. Status will display on this page as the resources are created. It takes about 10 minutes for the Bastion resource to be created and deployed.

## <a name="connect"></a>Connect to a VM

You can use the [Connection steps](#steps) in the section below to connect to your VM. You can also use any of the following articles to connect to a VM. Some connection types require the Bastion [Standard SKU](configuration-settings.md#skus).

[!INCLUDE [Links to Connect to VM articles](../../includes/bastion-vm-connect-article-list.md)]

### <a name="steps"></a>Connection steps

[!INCLUDE [Connect to a VM](../../includes/bastion-vm-connect.md)]

### <a name="audio"></a>To enable audio output

[!INCLUDE [Enable VM audio output](../../includes/bastion-vm-audio.md)]

## <a name="ip"></a>Remove VM public IP address

[!INCLUDE [Remove a public IP address from a VM](../../includes/bastion-remove-ip.md)]

## Clean up resources

If you're not going to continue to use this application, delete
your resources using the following steps:

1. Enter the name of your resource group in the **Search** box at the top of the portal. When you see your resource group in the search results, select it.
1. Select **Delete resource group**.
1. Enter the name of your resource group for **TYPE THE RESOURCE GROUP NAME:** and select **Delete**.

## Next steps

In this tutorial, you deployed Bastion to a virtual network and connected to a VM. You then removed the public IP address from the VM. Next, learn about and configure additional Bastion features.

> [!div class="nextstepaction"]
> [Bastion features and configuration settings](configuration-settings.md)
