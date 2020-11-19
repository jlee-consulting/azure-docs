---
title: Accelerated lab account setup guide for Azure Lab Services
description: This guide helps administrators quickly set up a lab account for use within their school.
ms.topic: article
ms.date: 06/26/2020
---

# Lab account setup guide
To set up your Azure Lab Services environment, administrators need to first set up a **lab account** within your Azure subscription. A lab account is a container for your labs and only takes a few minutes to set up.

This guide includes three sections:
-  The first section focuses on the prerequisites that need to be completed *before* you set up your lab account.
-  The second section provides guidance on planning your lab account settings.
-  The third section provides step-by-step instructions for setting up a lab account.

## Prerequisites for setting up your lab account
This section outlines the prerequisites that you need to complete before you can set up a lab account.

### Obtain an Azure subscription
To create a lab account, you need access to an Azure subscription that is set up for your school. Your school may have one or more subscriptions. You use a subscription to manage billing and security for all your Azure resources and services, including lab accounts.  Azure subscriptions are typically managed by your IT department.  For more information, read the following topic:
 - [Administrator Guide - Subscription](./administrator-guide.md#subscription)

### Estimate the number of VMs and VM sizes that you need
You'll need to estimate the number of virtual machines (VMs) and the [VM sizes](./administrator-guide.md#vm-sizing) that your school needs.  Read the following blog post for guidance on how to structure your labs\images.  This blog post will also help you decide the number of VMs and the VM sizes that you'll need:
- [Moving from a Physical Lab to Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

Also, see this article that explains additional guidance on how to structure labs:
- [Administrator Guide - Lab](./administrator-guide.md)

### Understand subscription VM limits and regional VM capacity
Once you have an estimate of the number of VMs and the VM sizes for your labs, you need to:

- Ensure that your Azure subscription's capacity limit allows for the number of VMs and the VM size that you plan to use in your labs.

- Create your lab account within a region that has sufficient VM capacity available.

Read the following blog post to learn more: [VM Subscription Limits and Regional Capacity](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553).

### Decide how many lab accounts to create

To get started quickly, create a single lab account within its own resource group.  Later, you can create additional lab accounts (and resource groups) as needed. For example, you might eventually have one lab account and resource group per department as a way to clearly separate costs.  Read the following articles to learn more about lab accounts, resource groups, and separating costs:
- [Administrator Guide - Resource Group](./administrator-guide.md#resource-group)
- [Administrator Guide - Lab Account](./administrator-guide.md#lab-account) 
- [Cost management for Azure Lab Services](./cost-management-guide.md)

## Planning your lab account's settings

To plan your lab account's settings, you should consider the below questions.

### Who should be owners and contributors of the lab account?

   Your school's IT administrators are typically the Owners and Contributors for a lab account. They're responsible for managing the policies that apply to all the labs contained within the lab account. The person that creates the lab account is automatically an Owner. You can add additional Owners and Contributors from the Azure Active Directory (AD) tenant that is associated with your subscription. For more information about the lab account Owner and Contributor roles, read:
   -  [Administrator Guide - Manage Identity](./administrator-guide.md#manage-identity).

   [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

   Lab users only see a single list of the virtual machines that they have access to across tenants inside Azure Lab Services.

### Who will be allowed to create labs?

   You may choose to have your IT and faculty members create labs. When a user creates a lab, they automatically are assigned as the Owner of the lab.  To create labs, users (typically from the Azure AD tenant associated with your subscription) must be assigned to the Lab Creator role within the lab account.  For more information about the Lab Creator role, read:
   -  [Administrator Guide - Manage identity](./administrator-guide.md#manage-identity)

### Who will be allowed to own and manage labs?

   You can also choose to have IT and faculty members own\manage labs *without* giving them the ability to create labs.  In this case, users from your subscription's Azure AD tenant are assigned either the Owner or Contributor for existing labs.  For more information about a lab's Owner and Contributor roles, read:
   - [Administrator Guide - Manage identity](./administrator-guide.md#manage-identity)

### Do you want to save images that can be shared across labs?
A Shared Image Gallery is a repository that you can use for saving and sharing images. For classes that need the same image, lab creators can create the image and then export it to Shared Image Gallery.  Once an images is exported to Shared Image Gallery, it can be used to create new labs.

Also, you may want to create your images in your physical environment and then import them into Shared Image Gallery.  For more details on this process, read the following blog post: 
- [Import Custom Image to Shared Image Gallery](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)

If you decide you need to use a Shared Image Gallery, you'll need to create or attach a Shared Image Gallery to your lab account. Also, you can postpone this decision until later since it can be attached to a lab account at any time.  For more information about Shared Image Gallery, read:
- [Administrator Guide - Shared image gallery](./administrator-guide.md#shared-image-gallery)
- [Administrator Guide - Shared image gallery pricing](./administrator-guide.md#shared-image-gallery-2)

### Which images in Azure Marketplace will your labs use?
Azure Marketplace provides hundreds of images that you can enable so that lab creators can use the image for creating their lab. Some images might include everything that a lab already needs. In other cases, you might use an image as a starting point, and then the lab creator can customize it by installing additional applications or tools.

If you don't know which images you need, you can come back later to enable them. Also, the best way to see which images are available is to first create a lab account. This gives you access so that you can review the list of available images and their contents.  For more information about marketplace images, read:
- [Specify Marketplace images available to lab creators](./specify-marketplace-images.md)
  
### Do the lab's VMs need to have access to other Azure or on-premises resources?
When you set up a lab account, you also can peer your lab account with a virtual network (VNet).  Keep in mind that both your VNet and lab account must be located in the same region.  To decide whether you need to peer with a VNet, consider the following scenarios:

- **Access to a licensing server**
  
   When you use Azure Marketplace images, the cost of the operating system license is bundled into the pricing for lab services. However, you don't need to provide licenses for the operating system itself. However, for additional software and applications that are installed, you do need to provide a license as appropriate.  To access a licensing server:
   - You may choose to connect to an on-premise licensing server.  Connecting to an on-premise licensing server requires additional setup.
   - Another option, which is faster to set up, is to create a licensing server that you host on an Azure VM.  The Azure VM is located within a virtual network that you peer to your lab account.

- **Access to other on-premise resources such as a file share or database**

   You create a VNet to provide access to on-premises resources, typically by using a site-to-site virtual network gateway. Setting up this type of environment will take additional time.

- **Access to other Azure resources that are located outside a VNet**

   If you need access to Azure resources that are *not* secured within a VNet, then you can access these resources through the public internet, without doing any peering.

For more information on virtual networks, read:
- [Architecture fundamentals - Virtual Network](./classroom-labs-fundamentals.md#virtual-network)
- [How to connect to a virtual network](./how-to-connect-peer-virtual-network.md)
- [How to create a lab with a shared resource in Azure Lab Services](./how-to-create-a-lab-with-shared-resource.md)

## Set up your lab account

After you've finished planning, you're ready to set up your lab account.  These same steps are applicable for setting up a lab [Azure Lab Services with Teams](./lab-services-within-teams-overview.md).

1. **Create your lab account.** Refer to the tutorial on [creating a lab account](./tutorial-setup-lab-account.md#create-a-lab-account) for instructions.
   
    For more guidance on naming, refer to the following article:

   - [Naming guidance for resources](./administrator-guide.md#naming)

2. **Add users to the lab creator role.** For instructions, see [adding users to the lab creator role](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


3. **Connect to a peer virtual network.** For instructions, see [connecting your lab's network with a peer virtual network](./how-to-connect-peer-virtual-network.md).

   You might also need to refer to instructions on [configuring the lab VMs address range](./how-to-configure-lab-accounts.md).

4. **Enable and review images.** For instructions, see [enabling Azure Marketplace images for lab creators](./specify-marketplace-images.md).

   To review the contents of each Azure Marketplace image, select the image name. For example, the following screenshot shows the details for the Ubuntu Data Science VM image:

   ![Screenshot of Review Azure Marketplace images](./media/setup-guide/review-marketplace-images.png)

   If a shared image gallery is attached to your lab account, and you want to enable custom images to be shared by lab creators, complete similar steps as shown in the following screenshot:

   ![Screenshot of Enabling custom images in a shared image gallery](./media/setup-guide/enable-sig-custom-images.png)

## Next steps

Next steps that are common to setting up a lab environment:

- [Manage lab accounts](how-to-manage-lab-accounts.md)
- [Lab setup guide](setup-guide.md)