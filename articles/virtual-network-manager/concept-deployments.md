---
title: 'Configuration deployments in Azure Virtual Network Manager (Preview)'
description: Learn about how configuration deployments work in Azure Virtual Network Manager.
author: mbender-ms    
ms.author: mbender
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 09/22/2022
ms.custom: template-concept, ignite-fall-2022
---

# Configuration deployments in Azure Virtual Network Manager (Preview)

In this article, you'll learn about how configurations are applied to your network resources. You'll also explore how updating a configuration deployment is different for each membership type. Then we'll go into details about *Deployment status* and *Goal state model*.

> [!IMPORTANT]
> Azure Virtual Network Manager is currently in public preview.
> This preview version is provided without a service level agreement, and it's not recommended for production workloads. Certain features might not be supported or might have constrained capabilities.
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Deployment

*Deployment* is the method Azure Virtual Network Manager uses to apply configurations to your virtual networks in network groups. Configurations won't take effect until they're deployed. When a deployment request is sent to Azure Virtual Network Manager, it will calculate the [goal state](#goalstate) of all resources under your network manager in that region. Goal state is a combination of deployed configurations and network group membership. Network manager will then apply the necessary changes to your infrastructure.

When committing a deployment, you select the region(s) to which the configuration will be applied. The time this takes depends on how large the configuration is.  Once the VNets are members of a network group, deploying a configuration onto that network group takes  a few minutes. This includes adding or removing group members directly, or configuring an Azure Policy resource. Safe deployment practices recommend gradually rolling out changes on a per-region basis. 
## Deployment latency

Deployment latency is the time it takes for a deployment configuration to be applied and take effect. There are two factors in how quickly the configurations are applied: 

- The base time of applying a configuration is a few minutes. 

- The time to receive a notification of network group membership can vary. 

For manually added members, notification is immediate. For dynamic members where the scope is less than 1000 subscriptions, notification takes a few minutes. In environments with more than 1000 subscriptions, the notification mechanism works in a 24-hour window. Changes to network groups will take effect without the need for configuration redeployment.   

AVNM will apply the configuration to the VNets in the network group. So even if your network group consists of dynamic members from more than 1000 subscriptions, if AVNM also is notified who is in the network group, the configuration will be applied in a few minutes. 
## Deployment status

When you commit a configuration deployment, the API does a POST operation. Once the deployment request has been made, Azure Virtual Network Manager will calculate the goal state of your networks in the deployed regions and request the underlying infrastructure to make the changes. You can see the deployment status on the *Deployment* page of the Virtual Network Manager.

:::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deployment-in-progress.png" alt-text="Screenshot of deployment in progress in deployment list.":::

## <a name = "goalstate"></a> Goal state model

When you commit a deployment of configuration(s), you're describing the goal state of your network manager in that region. This goal state is enforced during the next deployment. For example, when you commit configurations named *Config1* and *Config2* into a region, these two configurations get applied and become the region's goal state. If you decided to commit configuration named *Config1* and *Config3* into the same region, *Config2* would then be removed, and *Config3* would be added. To remove all configurations, you would deploy a **None** configuration against the region(s) you no longer wish to have any configurations applied.

## Next steps

Learn how to [create an Azure Virtual Network Manager instance](create-virtual-network-manager-portal.md).
