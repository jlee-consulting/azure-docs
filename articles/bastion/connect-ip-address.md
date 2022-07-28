---
title: 'Connect to a VM - specified private IP address: Azure portal'
titleSuffix: Azure Bastion
description: Learn how to connect to your virtual machines using a specified private IP address via Azure Bastion.
author: cherylmc

ms.service: bastion
ms.topic: how-to
ms.date: 04/26/2022
ms.author: cherylmc

---

# Connect to a VM via specified private IP address through the portal

IP-based connection lets you connect to your on-premises, non-Azure, and Azure virtual machines via Azure Bastion over ExpressRoute or a VPN site-to-site connection using a specified private IP address. The steps in this article show you how to configure your Bastion deployment, and then connect to an on-premises resource using IP-based connection. For more information about Azure Bastion, see the [Overview](bastion-overview.md).

:::image type="content" source="./media/connect-ip-address/architecture.png" alt-text="Diagram that shows the Azure Bastion architecture." lightbox="./media/connect-ip-address/architecture.png":::

> [!NOTE]
> This configuration requires the Standard SKU tier for Azure Bastion. To upgrade, see [Upgrade a SKU](upgrade-sku.md).
>

**Limitations**

IP-based connection won’t work with force tunneling over VPN, or when a default route is advertised over an ExpressRoute circuit. Azure Bastion requires access to the Internet and force tunneling, or the default route advertisement will result in traffic blackholing.

## Prerequisites

Before you begin these steps, verify that you have the following environment set up:

* A VNet with Bastion already deployed.

  * Make sure that you have deployed Bastion to the virtual network. Once the Bastion service is provisioned and deployed in your virtual network, you can use it to connect to any VM deployed in any of the virtual networks that is reachable from Bastion.
  * To deploy Bastion, see [Quickstart: Deploy Bastion with default settings](quickstart-host-portal.md).

* A virtual machine in any reachable virtual network. This is the virtual machine to which you'll connect.

## Configure Bastion

1. Sign in to the [Azure portal](https://portal.azure.com/).

1. In the Azure portal, go to your Bastion deployment.

1. IP based connection requires the Standard SKU tier. On the **Configuration** page, for **Tier**, verify the tier is set to the **Standard** SKU. If the tier is set to the Basic SKU, select **Standard** from the dropdown.
1. To enable **IP based connection**, select **IP based connection**.

    :::image type="content" source="./media/connect-ip-address/ip-connection.png" alt-text="Screenshot that shows the Configuration page." lightbox="./media/connect-ip-address/ip-connection.png":::

1. Select **Apply** to apply the changes. It takes a few minutes for the Bastion configuration to complete.

## Connect to VM

1. To connect to a VM using a specified private IP address, you make the connection from Bastion to the VM, not directly from the VM page. On your Bastion page, select **Connect** to open the Connect page.

1. On the Bastion **Connect** page, for **IP address**, enter the private IP address of the target VM.

    :::image type="content" source="./media/connect-ip-address/ip-address.png" alt-text="Screenshot of the Connect using Azure Bastion page." lightbox="./media/connect-ip-address/ip-address.png":::

1. Adjust your connection settings to the desired **Protocol** and **Port**.

1. Enter your credentials in **Username** and **Password**.

1. Select **Connect** to connect to your virtual machine.  

## Next steps

Read the [Bastion FAQ](bastion-faq.md) for additional information.