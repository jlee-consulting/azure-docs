---
title: Tutorial - Monitor network communication using the Azure portal using VM scale set 
description: In this tutorial, learn how to monitor network communication between two virtual machine scale sets with Azure Network Watcher's connection monitor capability.
services: network-watcher
documentationcenter: na
author: mjha
editor: ''
tags: azure-resource-manager
# Customer intent: I need to monitor communication between a VM scale set and another VM scale set. If the communication fails, I need to know why, so that I can resolve the problem. 

ms.service: network-watcher
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload:  infrastructure-services
ms.date: 05/24/2022
ms.author: mjha
ms.custom: mvc
---

# Tutorial: Monitor network communication between two virtual machine scale sets using the Azure portal

> [!NOTE]
> This tutorial cover Connection Monitor (classic). Try the new and improved [Connection Monitor](connection-monitor-overview.md) to experience enhanced connectivity monitoring

> [!IMPORTANT]
> Starting 1 July 2021, you will not be able to add new connection monitors in Connection Monitor (classic) but you can continue to use existing connection monitors created prior to 1 July 2021. To minimize service disruption to your current workloads, [migrate from Connection Monitor (classic) to the new Connection Monitor](migrate-to-connection-monitor-from-connection-monitor-classic.md) in Azure Network Watcher before 29 February 2024.

Successful communication between a virtual machine scale set (VMSS) and an endpoint such as another VM, can be critical for your organization. Sometimes, configuration changes are introduced which can break communication. In this tutorial, you learn how to:

> [!div class="checklist"]
> * Create a VM scale set and a VM
> * Monitor communication between VMs with the connection monitor capability of Network Watcher
> * Generate alerts on Connection Monitor metrics
> * Diagnose a communication problem between two VM scale sets, and learn how you can resolve it

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Sign in to Azure

Sign in to the [Azure portal](https://portal.azure.com).

## Create a VM scale set

Create a VM scale set

## Create a load balancer

Azure [load balancer](../load-balancer/load-balancer-overview.md) distributes incoming traffic among healthy virtual machine instances. 

First, create a public Standard Load Balancer by using the portal. The name and public IP address you create are automatically configured as the load balancer's front end.

1. In the search box, type **load balancer**. Under **Marketplace** in the search results, pick **Load balancer**.
1. In the **Basics** tab of the **Create load balancer** page, enter or select the following information:

    | Setting                 | Value   |
    | ---| ---|
    | Subscription  | Select your subscription.    |    
    | Resource group | Select **Create new** and type *myVMSSResourceGroup* in the text box.|
    | Name           | *myLoadBalancer*         |
    | Region         | Select **East US**.       |
    | Type          | Select **Public**.       |
    | SKU           | Select **Standard**.       |
    | Public IP address | Select **Create new**. |
    | Public IP address name  | *myPip*   |
    | Assignment| Static |
    | Availability zone | Select **Zone-redundant**. |

1. When you are done, select **Review + create** 
1. After it passes validation, select **Create**. 


## Create virtual machine scale set

You can deploy a scale set with a Windows Server image or Linux image such as RHEL, CentOS, Ubuntu, or SLES.

1. Type **Scale set** in the search box. In the results, under **Marketplace**, select **Virtual machine scale sets**. Select **Create** on the **Virtual machine scale sets** page, which will open the **Create a virtual machine scale set** page. 
1. In the **Basics** tab, under **Project details**, make sure the correct subscription is selected and select *myVMSSResourceGroup* from resource group list. 
1. Type *myScaleSet* as the name for your scale set.
1. In **Region**, select a region that is close to your area.
1. Under **Orchestration**, ensure the *Uniform* option is selected for **Orchestration mode**. 
1. Select a marketplace image for **Image**. In this example, we have chosen *Ubuntu Server 18.04 LTS*.
1. Enter your desired username, and select which authentication type you prefer.
   - A **Password** must be at least 12 characters long and meet three out of the four following complexity requirements: one lower case character, one upper case character, one number, and one special character. For more information, see [username and password requirements](../virtual-machines/windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-).
   - If you select a Linux OS disk image, you can instead choose **SSH public key**. Only provide your public key, such as *~/.ssh/id_rsa.pub*. You can use the Azure Cloud Shell from the portal to [create and use SSH keys](../virtual-machines/linux/mac-create-ssh-keys.md).
   
 
1. Select **Next** to move the other pages. 
1. Leave the defaults for the **Instance** and **Disks** pages.
1. On the **Networking** page, under **Load balancing**, select **Yes** to put the scale set instances behind a load balancer. 
1. In **Load balancing options**, select **Azure load balancer**.
1. In **Select a load balancer**, select *myLoadBalancer* that you created earlier.
1. For **Select a backend pool**, select **Create new**, type *myBackendPool*, then select **Create**.
1. When you are done, select **Review + create**. 
1. After it passes validation, select **Create** to deploy the scale set.


Once the scale set is created, follow the steps below to enable the Network Watcher extension in the scale set.

1. Under **Settings**, select **Extensions**. Select **Add extension**, and select **Network Watcher Agent for Windows**, as shown in the following picture:

:::image type="content" source="./media/connection-monitor/nw-agent-extension.png" alt-text="Screenshot that shows Network Watcher extension addition.":::

  
1. Under **Network Watcher Agent for Windows**, select **Create**, under **Install extension** select **OK**, and then under **Extensions**, select **OK**.

 
### Create the VM

Complete the steps in [create a VM](./connection-monitor.md#create-the-first-vm) again, with the following changes:

|Step|Setting|Value|
|---|---|---|
| 1 | Select a version of **Ubuntu Server** |                                                                         |
| 3 | Name                                  | myVm2                                                                   |
| 3 | Authentication type                   | Paste your SSH public key or select **Password**, and enter a password. |
| 3 | Resource group                        | Select **Use existing** and select **myResourceGroup**.                 |
| 6 | Extensions                            | **Network Watcher Agent for Linux**                                             |

The VM takes a few minutes to deploy. Wait for the VM to finish deploying before continuing with the remaining steps.


## Create a connection monitor

Create a connection monitor to monitor communication over TCP port 22 from *myVmss1* to *myVm2*.

1. On the left side of the portal, select **All services**.
2. Start typing *network watcher* in the **Filter** box. When **Network Watcher** appears in the search results, select it.
3. Under **MONITORING**, select **Connection monitor**.
4. Select **+ Add**.
5. Enter or select the information for the connection you want to monitor, and then select **Add**. In the example shown in the following picture, the connection monitored is from the *myVmss1* VM scale set to the *myVm2* VM over port 22:

    | Setting                  | Value               |
    | ---------                | ---------           |
    | Name                     | myVmss1-myVm2(22)     |
    | Source                   |                     |
    | Virtual machine          | myVmss1               |
    | Destination              |                     |
    | Select a virtual machine |                     |
    | Virtual machine          | myVm2               |
    | Port                     | 22                  |

    :::image type="content" source="./media/connection-monitor/add-connection-monitor.png" alt-text="Screenshot that shows addition of Connection Monitor.":::
	
## View a connection monitor

1. Complete steps 1-3 in [Create a connection monitor](#create-a-connection-monitor) to view connection monitoring. You see a list of existing connection monitors, as shown in the following picture:

    :::image type="content" source="./media/connection-monitor/connection-monitors.png" alt-text="Screenshot that shows Connection Monitor.":::
	
2. Select the monitor with the name **myVmss1-myVm2(22)**, as shown in the previous picture, to see details for the monitor, as shown in the following picture:

    :::image type="content" source="./media/connection-monitor/vm-monitor.png" alt-text="Screenshot that shows virtual machine monitor.":::
	
    Note the following information:

    | Item                     | Value                      | Details                                                     |
    | ---------                | ---------                  |--------                                                     |
    | Status                   | Reachable                  | Lets you know whether the endpoint is reachable or not.|
    | AVG. ROUND-TRIP          | Lets you know the round-trip time to make the connection, in milliseconds. Connection monitor probes the connection every 60 seconds, so you can monitor latency over time.                                         |
    | Hops                     | Connection monitor lets you know the hops between the two endpoints. In this example, the connection is between two VMs in the same virtual network, so there is only one hop, to the 10.0.0.5 IP address. If any existing system or custom routes, route traffic between the VMs through a VPN gateway, or network virtual appliance, for example, additional hops are listed.                                                                                                                         |
    | STATUS                   | The green check marks for each endpoint let you know that each endpoint is healthy.    ||

## Generate alerts

Alerts are created by alert rules in Azure Monitor and can automatically run saved queries or custom log searches at regular intervals. A generated alert can automatically run one or more actions, such as to notify someone or start another process. When setting an alert rule, the resource that you target determines the list of available metrics that you can use to generate alerts.

1. In Azure portal, select the **Monitor** service, and then select **Alerts** > **New alert rule**.
2. Click **Select target**, and then select the resources that you want to target. Select the **Subscription**, and set **Resource type** to filter down to the Connection Monitor that you want to use.

    :::image type="content" source="./media/connection-monitor/set-alert-rule.png" alt-text="Screenshot of alert rule.":::
	
1. Once you have selected a resource to target, select **Add criteria**.The Network Watcher has [metrics on which you can create alerts](../azure-monitor/alerts/alerts-metric-near-real-time.md#metrics-and-dimensions-supported). Set **Available signals** to the metrics ProbesFailedPercent and AverageRoundtripMs:

    :::image type="content" source="./media/connection-monitor/set-alert-signals.png" alt-text="Screenshot of alert signals.":::
	
1. Fill out the alert details like alert rule name, description and severity. You can also add an action group to the alert to automate and customize the alert response.

## View a problem

By default, Azure allows communication over all ports between VMs in the same virtual network. Over time, you, or someone in your organization, might override Azure's default rules, inadvertently causing a communication failure. Complete the following steps to create a communication problem and then view the connection monitor again:

1. In the search box at the top of the portal, enter *myResourceGroup*. When the **myResourceGroup** resource group appears in the search results, select it.
2. Select the **myVm2-nsg** network security group.
3. Select **Inbound security rules**, and then select **Add**, as shown in the following picture:

	:::image type="content" source="./media/connection-monitor/inbound-security-rules.png" alt-text="Screenshot of network security rules.":::

4. The default rule that allows communication between all VMs in a virtual network is the rule named **AllowVnetInBound**. Create a rule with a higher priority (lower number) than the **AllowVnetInBound** rule that denies inbound communication over port 22. Select, or enter, the following information, accept the remaining defaults, and then select **Add**:

    | Setting                 | Value          |
    | ---                     | ---            |
    | Destination port ranges | 22             |
    | Action                  | Deny           |
    | Priority                | 100            |
    | Name                    | DenySshInbound |

5. Since connection monitor probes at 60-second intervals, wait a few minutes and then on the left side of the portal, select **Network Watcher**, then **Connection monitor**, and then select the **myVm1-myVm2(22)** monitor again. The results are different now, as shown in the following picture:

	:::image type="content" source="./media/connection-monitor/vm-monitor-fault.png" alt-text="Screenshot of virtual  machine at fault.":::

    You can see that there's a red exclamation icon in the status column for the **myvm2529** network interface.

6. To learn why the status has changed, select 10.0.0.5, in the previous picture. Connection monitor informs you that the reason for the communication failure is: *Traffic blocked due to the following network security group rule: UserRule_DenySshInbound*.

    If you didn't know that someone had implemented the security rule you created in step 4, you'd learn from connection monitor that the rule is causing the communication problem. You could then change, override, or remove the rule, to restore communication between the VMs.

## Clean up resources

When no longer needed, delete the resource group and all of the resources it contains:

1. Enter *myResourceGroup* in the **Search** box at the top of the portal. When you see **myResourceGroup** in the search results, select it.
2. Select **Delete resource group**.
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME:** and select **Delete**.

## Next steps

In this tutorial, you learned how to monitor a connection between two VMs. You learned that a network security group rule prevented communication to a VM. To learn about all of the different responses connection monitor can return, see [response types](network-watcher-connectivity-overview.md#response). You can also monitor a connection between a VM, a fully qualified domain name, a uniform resource identifier, or an IP address.

At some point, you may find that resources in a virtual network are unable to communicate with resources in other networks connected by an Azure virtual network gateway. Advance to the next tutorial to learn how to diagnose a problem with a virtual network gateway.

> [!div class="nextstepaction"]
> [Diagnose communication problems between networks](diagnose-communication-problem-between-networks.md)
