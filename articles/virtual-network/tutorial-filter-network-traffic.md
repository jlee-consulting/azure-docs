---
title: 'Tutorial: Filter network traffic with a network security group (NSG) - Azure portal'
titlesuffix: Azure Virtual Network
description: In this tutorial, you learn how to filter network traffic to a subnet, with a network security group (NSG), using the Azure portal.
services: virtual-network
author: mbender-ms
ms.service: virtual-network
ms.topic: tutorial
ms.date: 06/28/2022
ms.author: mbender
ms.custom: template-tutorial #Required; leave this attribute/value as-is.
# Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
---

# Tutorial: Filter network traffic with a network security group using the Azure portal

You can use a network security group to filter inbound and outbound network traffic to and from Azure resources in an Azure virtual network.

Network security groups contain security rules that filter network traffic by IP address, port, and protocol. When a network security group is associated with a subnet, security rules are applied to resources deployed in that subnet. 

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Create a network security group and security rules
> * Create application security groups  
> * Create a virtual network and associate a network security group to a subnet
> * Deploy virtual machines and associate their network interfaces to the application security groups
> * Test traffic filters

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Prerequisites

- An Azure subscription

## Sign in to Azure

Sign in to the [Azure portal](https://portal.azure.com).

## Create a virtual network

1. From the Azure portal menu, select **+ Create a resource** > **Networking** > **Virtual network**, or search for *Virtual Network* in the portal search box.

1. Select **Create**.

1. On the **Basics** tab of **Create virtual network**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | **Project details** |   |
    | Subscription | Select your subscription. |
    | Resource group | Select **Create new**.  </br> Enter *myResourceGroup*. </br> Select **OK**. |
    | **Instance details** |   |
    | Name | Enter *myVNet*. |
    | Region | Select **East US**. |

1. Select the **Review + create** tab, or select the blue **Review + create** button at the bottom of the page.

1. Select **Create**.

## Create application security groups

An [application security group (ASGs)](application-security-groups.md) enables you to group together servers with similar functions, such as web servers.

1. From the Azure portal menu, select **+ Create a resource** > **Networking** > **Application security group**, or search for *Application security group* in the portal search box. 

2. Select **Create**.

3. On the **Basics** tab of **Create an application security group**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    |**Project details** |  |
    | Subscription | Select your subscription. |
    | Resource group | Select **myResourceGroup**. |
    | **Instance details** |  |
    | Name | Enter *myAsgWebServers*. |
    | Region | Select **(US) East US**. | 

4. Select the **Review + create** tab, or select the blue **Review + create** button at the bottom of the page.

5. Select **Create**.

6. Repeat the previous steps, specifying the following values:

    | Setting | Value |
    | ------- | ----- |
    |**Project details** |  |
    | Subscription | Select your subscription. |
    | Resource group | Select **myResourceGroup**. |
    | **Instance details** |  |
    | Name | Enter *myAsgMgmtServers*. |
    | Region | Select **(US) East US**. |

8. Select the **Review + create** tab, or select the blue **Review + create** button at the bottom of the page.

9. Select **Create**.

## Create a network security group

A [network security group (NSG)](network-security-groups-overview.md) secures network traffic in your virtual network. 

1. From the Azure portal menu, select **+ Create a resource** > **Networking** > **Network security group**, or search for *Network security group* in the portal search box.

1. Select **Create**.

1. On the **Basics** tab of **Create network security group**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | **Project details** |   |
    | Subscription | Select your subscription. |
    | Resource group | Select **myResourceGroup**. |
    | **Instance details** |   |
    | Name | Enter *myNSG*. |
    | Location | Select **(US) East US**. | 

5. Select the **Review + create** tab, or select the blue **Review + create** button at the bottom of the page.

6. Select **Create**.

## Associate network security group to subnet

In this section, you'll associate the network security group with the subnet of the virtual network you created earlier.

1. Search for *myNsg* in the portal search box.

2. Select **Subnets** from the **Settings** section of **myNSG**.

3. In the **Subnets** page, select **+ Associate**:

    :::image type="content" source="./media/tutorial-filter-network-traffic/associate-nsg-subnet.png" alt-text="Screenshot of Associate a network security group to a subnet." border="true":::

3. Under **Associate subnet**, select **myVNet** for **Virtual network**. 

4. Select **default** for **Subnet**, and then select **OK**.

## Create security rules


1. Select **Inbound security rules** from the **Settings** section of **myNSG**.

1. In **Inbound security rules** page, select **+ Add**:

    :::image type="content" source="./media/tutorial-filter-network-traffic/add-inbound-rule.png" alt-text="Screenshot of Inbound security rules in a network security group." border="true":::

1. Create a security rule that allows ports 80 and 443 to the **myAsgWebServers** application security group. In **Add inbound security rule** page, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Source | Leave the default of **Any**. |
    | Source port ranges | Leave the default of **(*)**. |
    | Destination | Select **Application security group**. |
    | Destination application security groups | Select **myAsgWebServers**. |
    | Service | Leave the default of **Custom**. |
    | Destination port ranges | Enter *80,443*. |
    | Protocol | Select **TCP**. |
    | Action | Leave the default of **Allow**. |
    | Priority | Leave the default of **100**. |
    | Name | Enter *Allow-Web-All*. |

    :::image type="content" source="./media/tutorial-filter-network-traffic/inbound-security-rule-inline.png" alt-text="Screenshot of Add inbound security rule in a network security group." lightbox="./media/tutorial-filter-network-traffic/inbound-security-rule-expanded.png":::

1. Select **Add**.

1. Complete steps 3-4 again using this information:

    | Setting | Value |
    | ------- | ----- |
    | Source | Leave the default of **Any**. |
    | Source port ranges | Leave the default of **(*)**. |
    | Destination | Select **Application security group**. |
    | Destination application security group | Select **myAsgMgmtServers**. |
    | Service | Leave the default of **Custom**. |
    | Destination port ranges | Enter *3389*. |
    | Protocol | Select **Any**. |
    | Action | Leave the default of **Allow**. |
    | Priority | Leave the default of **110**. |
    | Name | Enter *Allow-RDP-All*. |

1. Select **Add**.

    > [!CAUTION]
    > In this article, RDP (port 3389) is exposed to the internet for the VM that is assigned to the **myAsgMgmtServers** application security group. 
    >
    > For production environments, instead of exposing port 3389 to the internet, it's recommended that you connect to Azure resources that you want to manage using a VPN, private network connection, or Azure Bastion.
    >
    > For more information on Azure Bastion, see [What is Azure Bastion?](../bastion/bastion-overview.md).

Once you've completed steps 1-3, review the rules you created. Your list should look like the list in the following example:

:::image type="content" source="./media/tutorial-filter-network-traffic/security-rules.png" alt-text="Screenshot of Security rules of a network security group." border="true":::

## Create virtual machines

Create two virtual machines (VMs) in the virtual network.

### Create the first virtual machine

1. From the Azure portal menu, select **+ Create a resource** > **Compute** > **Virtual machine**, or search for *Virtual machine* in the portal search box.

2. In **Create a virtual machine**, enter or select this information in the **Basics** tab:

    | Setting | Value |
    | ------- | ----- |
    | **Project details** |  |
    | Subscription | Select your subscription. |
    | Resource group | Select **myResourceGroup**. |
    | **Instance details** |   |
    | Virtual machine name | Enter *myVMWeb*. |
    | Region | Select **(US) East US**. |
    | Availability options | Leave the default of **No infrastructure redundancy required**. |
    | Security type | Leave the default of **Standard**. |
    | Image | Select **Windows Server 2019 Datacenter - Gen2**. |
    | Azure Spot instance | Leave the default of unchecked. |
    | Size | Select **Standard_D2s_V3**. |
    | **Administrator account** |   |
    | Username | Enter a username. |
    | Password | Enter a password. |
    | Confirm password | Reenter password. |
    | **Inbound port rules** |   |
    | Select inbound ports | Select **None**. |

3. Select the **Networking** tab.

4. In the **Networking** tab, enter or select the following information:

    | Setting | Value |
    | ------- | ----- |
    | **Network interface** |   |
    | Virtual network | Select **myVNet**. |
    | Subnet | Select **default (10.0.0.0/24)**. |
    | Public IP | Leave the default of a new public IP. |
    | NIC network security group | Select **None**. | 

5. Select the **Review + create** tab, or select the blue **Review + create** button at the bottom of the page.

6. Select **Create**. The VM may take a few minutes to deploy.

### Create the second virtual machine

Complete steps 1-6 again, but in step 2, enter *myVMMgmt* for Virtual machine name.

Wait for the VMs to complete deployment before advancing to the next section.

## Associate network interfaces to an ASG

When you created the VMs, Azure created a network interface for each VM, and attached it to the VM. 

Add the network interface of each VM to one of the application security groups you created previously:

1. Search for *myVMWeb* in the portal search box.

2. Select **Networking** from the **Settings** section of **myVMWeb** VM.  

3. Select the **Application security groups** tab, then select **Configure the application security groups**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/configure-app-sec-groups.png" alt-text="Screenshot of Configure application security groups." border="true":::

4. In **Configure the application security groups**, select **myAsgWebServers**. Select **Save**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/select-application-security-groups-inline.png" alt-text="Screenshot showing how to associate application security groups to a network interface." border="true" lightbox="./media/tutorial-filter-network-traffic/select-application-security-groups-expanded.png":::

5. Complete steps 1 and 2 again, searching for the *myVMMgmt* virtual machine and selecting the **myAsgMgmtServers** ASG.

## Test traffic filters

1. Search for *myVMWeb* in the portal search box.

1. On the **Overview** page, select the **Connect** button and then select **RDP**.

1. Select **Download RDP file**.

1. Open the downloaded rdp file and select **Connect**. Enter the username and password you specified when creating the VM.

4. Select **OK**.

5. You may receive a certificate warning during the connection process. If you receive the warning, select **Yes** or **Continue**, to continue with the connection.

    The connection succeeds, because inbound traffic from the internet to the **myAsgMgmtServers** application security group is allowed through port 3389. 
    
    The network interface for **myVMMgmt** is associated with the **myAsgMgmtServers** application security group and allows the connection.

6. Open a PowerShell session on **myVMMgmt**. Connect to **myVMWeb** using the following: 

    ```powershell
    mstsc /v:myVmWeb
    ```

    The RDP connection from **myVMMgmt** to **myVMWeb** succeeds because virtual machines in the same network can communicate with each other over any port by default.
    
    You can't create an RDP connection to the **myVMWeb** virtual machine from the internet. The security rule for the **myAsgWebServers** prevents connections to port 3389 inbound from the internet. Inbound traffic from the Internet is denied to all resources by default.

7. To install Microsoft IIS on the **myVMWeb** virtual machine, enter the following command from a PowerShell session on the **myVMWeb** virtual machine:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. After the IIS installation is complete, disconnect from the **myVMWeb** virtual machine, which leaves you in the **myVMMgmt** virtual machine remote desktop connection.

9. Disconnect from the **myVMMgmt** VM.

10. Search for *myVMWeb* in the portal search box.

11. On the **Overview** page of **myVMWeb**, note the **Public IP address** for your VM. The address shown in the following example is 23.96.39.113, but your address is different:

    :::image type="content" source="./media/tutorial-filter-network-traffic/public-ip-address.png" alt-text="Screenshot of Public IP address of a virtual machine in the Overview page." border="true":::
    
11. To confirm that you can access the **myVMWeb** web server from the internet, open an internet browser on your computer and browse to `http://<public-ip-address-from-previous-step>`. 

You see the IIS default page, because inbound traffic from the internet to the **myAsgWebServers** application security group is allowed through port 80. 

The network interface attached for **myVMWeb** is associated with the **myAsgWebServers** application security group and allows the connection. 

## Clean up resources

When no longer needed, delete the resource group and all of the resources it contains:

1. Enter *myResourceGroup* in the **Search** box at the top of the portal. When you see **myResourceGroup** in the search results, select it.
2. Select **Delete resource group**.
3. Enter **myResourceGroup** for **TYPE THE RESOURCE GROUP NAME:** and select **Delete**.

## Next steps

In this tutorial, you:

* Created a network security group and associated it to a virtual network subnet. 
* Created application security groups for web and management.
* Created two virtual machines and associated their network interfaces with the application security groups.
* Tested the application security group network filtering.

To learn more about network security groups, see [Network security group overview](./network-security-groups-overview.md) and [Manage a network security group](manage-network-security-group.md).

Azure routes traffic between subnets by default. You may instead, choose to route traffic between subnets through a VM, serving as a firewall, for example. 

To learn how to create a route table, advance to the next tutorial.
> [!div class="nextstepaction"]
> [Create a route table](./tutorial-create-route-table-portal.md)
