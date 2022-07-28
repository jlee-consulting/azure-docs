---
title: Connect a classic Azure Video Indexer account to ARM
description: This topic explains how to connect an existing classic paid Azure Video Indexer account to an ARM-based account
ms.topic: how-to
ms.author: itnorman
ms.date: 05/04/2022
ms.custom: ignite-fall-2021
---

# Connect an existing classic paid Azure Video Indexer account to ARM-based account  

This article details how to connect an existing classic paid Azure Video Indexer account to an Azure Resource Manager (ARM) based account.
Today, Azure Video Indexer, is a GA(general availability) product that is not an ARM resource on Azure.
In this article, we will go through options on connecting your **existing** Azure Video Indexer account to [ARM][docs-arm-overview].

## Prerequisites

Before creating a new account, review [Account types](accounts-overview.md).

1. Unlimited paid Azure Video Indexer account (classic account).

   1. To perform the connect to the ARM (Azure Resource Manager) action, you should have owner's permissions on the Azure Video Indexer classic account.
1. Azure Subscription with Owner permissions or Contributor with Administrator Role assignment.

   1. Same level of permission for the Azure Media Service associated with the existing Azure Video Indexer Classic account.
1. User assigned managed identity (can be created along the flow).

## Transition state

Connecting a classic account to be ARM-based triggers a 30 days of a transition state. In the transition state, an existing account can be accessed by generating an access token using both:

* Access token [generated through API Management](https://aka.ms/avam-dev-portal)(classic way) 
* Access token [generated through ARM](/rest/api/videoindexer/generate/access-token) 

The transition state moves all account management functionality to be managed by ARM and will be handled by [Azure RBAC][docs-rbac-overview]. 

The [invite users](invite-users.md) feature in the Azure Video Indexer portal gets disabled. The invited users on this account lose their access to the Azure Video Indexer account Media in the portal.  
However, this can be resolved by assigning the right role-assignment to these users through Azure RBAC, see [How to assign RBAC][docs-rbac-assignment]. 

Only the account owner, who performed the connect action, is automatically assigned as the owner on the connected account. When [Azure policies][docs-governance-policy] are enforced, they override the settings on the account.

If users are not added through Azure RBAC to the account after 30 days, they will lose access through API as well as Azure Video Indexer portal.  
After the transition state ends, users will only be able to generate a valid access token through ARM, making Azure RBAC the exclusive way to manage role-based access control on the account.

> [!NOTE]
> If there are invited users you wish to remove access from, do it before connecting the account to ARM. 

Before the end of the 30 days of transition state, you can remove access from users through the Azure Video Indexer portal on the account settings page.

## Get started

### Browse to [Azure Video Indexer portal](https://aka.ms/vi-portal-link)

1. Sign in using your Azure AD account.
1. On the top right bar press *User account* to open the side pane account list.
1. Select the Azure Video Indexer classic account you wish to connect to ARM (classic accounts will be tagged with a *classic tag*).
1. Click **Settings**.

    :::image type="content" alt-text="Screenshot that shows the Azure Video Indexer portal settings." source="./media/connect-classic-account-to-arm/classic-account-settings.png":::
1. Click **Connect to an ARM-based account**.

    :::image type="content" alt-text="Screenshot that shows the connect to an ARM-based account dialog." source="./media/connect-classic-account-to-arm/connect-classic-to-arm.png":::
1. Sign to Azure portal.
1. The Azure Video Indexer create blade will open.
1. In the **Create Azure Video Indexer account** section enter required values.

    If you followed the steps the fields should be auto-populated, make sure to validate the eligible values.

    :::image type="content" alt-text="Screenshot that shows the create Azure Video Indexer account dialog." source="./media/connect-classic-account-to-arm/connect-blade.png":::
    
    Here are the descriptions for the resource fields: 

    | Name | Description |
    | ---|---|
    |**Subscription**| The subscription currently contains the classic account and other related resources such as the Media Services.|
    |**Resource Group**|Select an existing resource or create a new one. The resource group must be the same location as the classic account being connected|
    |**Azure Video Indexer account** (radio button)| Select the *"Connecting an existing classic account"*.|
    |**Existing account ID**| Enter the ID of existing Azure Video Indexer classic account.|
    |**Resource name**|Enter the name of the new Azure Video Indexer account. Default value would be the same name the account had as classic.|
    |**Location**|The geographic region can't be changed in the connect process, the connected account must stay in the same region. |
    |**Media Services account name**|The original Media Services account name that was associated with classic account.|
    |**User-assigned managed identity**|Select a user-assigned managed identity, or create a new one. Azure Video Indexer account will use it to access the Media services. The user-assignment managed identity will be assigned the roles of Contributor for the Media Service account.|
1. Click **Review + create** at the bottom of the form.

## After connecting to ARM is complete 

After successfully connecting your account to ARM, it is recommended to make sure your account management APIs are replaced with [Azure Video Indexer REST API](/rest/api/videoindexer/accounts?branch=videoindex).
As mentioned in the beginning of this article, during the 30 days of the transition state, “[Get-access-token](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account-Access-Token)” will be supported side by side the ARM-based “[Generate-Access token](/rest/api/videoindexer/generate/access-token)”.
Make sure to change to the new "Generate-Access token" by updating all your solutions that use the API.
 
APIs to be changed:

- Get Access token for each scope: Account, Project & Video.
- Get account – the account’s details.
- Get accounts – List of all account in a region.
- Create paid account – would create a classic account.
 
For a full description of [Azure Video Indexer REST API](/rest/api/videoindexer/accounts?branch=videoindex) calls and documentation, follow the link.

For code sample generating an access token through ARM see [C# code sample](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/ApiUsage/ArmBased/Program.cs).

### Next steps

Learn how to [Upload a video using C#](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/ApiUsage/ArmBased).
  
<!-- links -->
[docs-arm-overview]: ../azure-resource-manager/management/overview.md
[docs-rbac-overview]: ../role-based-access-control/overview.md
[docs-rbac-assignment]: ../role-based-access-control/role-assignments-portal.md
[docs-governance-policy]: ../governance/policy/overview.md
