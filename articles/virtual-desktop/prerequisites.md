---
title: Prerequisites for Azure Virtual Desktop
description: Find what prerequisites you need to complete to successfully connect your users to their Windows desktops and applications.
author: dknappettmsft
ms.topic: overview
ms.date: 03/09/2022
ms.author: daknappe
manager: femila
---
# Prerequisites for Azure Virtual Desktop

There are a few things you need to start using Azure Virtual Desktop. Here you can find what prerequisites you need to complete to successfully provide your users with virtual desktops and remote apps.

At a high level, you'll need:

> [!div class="checklist"]
> - An Azure account with an active subscription
> - An identity provider
> - A supported operating system
> - Appropriate licenses
> - Network connectivity
> - A Remote Desktop client

## Azure account with an active subscription

You'll need an Azure account with an active subscription to deploy Azure Virtual Desktop. If you don't have one already, you can [create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Your account must be assigned the [contributor or owner role](../role-based-access-control/built-in-roles.md) on your subscription.

You also need to make sure you've registered the *Microsoft.DesktopVirtualization* resource provider for your subscription. To check the status of the resource provider and register if needed:

> [!IMPORTANT]
> You must have permission to register a resource provider, which requires the `*/register/action` operation. This is included if your account is assigned the [contributor or owner role](../role-based-access-control/built-in-roles.md) on your subscription.

1. Sign in to the [Azure portal](https://portal.azure.com).
1. Select **Subscriptions**.
1. Select the name of your subscription.
1. Select **Resource providers**.
1. Search for **Microsoft.DesktopVirtualization**.
1. If the status is *NotRegistered*, select **Microsoft.DesktopVirtualization**, and then select **Register**.
1. Verify that the status of Microsoft.DesktopVirtualization is **Registered**.

## Identity

To access virtual desktops and remote apps from your session hosts, your users need to be able to authenticate. [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) is Microsoft's centralized cloud identity service that enables this capability. Azure AD is always used to authenticate users for Azure Virtual Desktop. Session hosts can be joined to the same Azure AD tenant, or to an Active Directory domain using [Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (AD DS) or [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md) (Azure AD DS), providing you with a choice of flexible configuration options.

### Session hosts

You need to join session hosts that provide virtual desktops and remote apps to an AD DS domain, Azure AD DS domain, or the same Azure AD tenant as your users.

- If you're joining session hosts to an AD DS domain and you want to manage them using [Intune](/mem/intune/fundamentals/what-is-intune), you'll need to configure [Azure AD Connect](../active-directory/hybrid/whatis-azure-ad-connect.md) to enable [hybrid Azure AD join](../active-directory/devices/hybrid-azuread-join-plan.md).
- If you're joining session hosts to an Azure AD DS domain, you can't manage them using [Intune](/mem/intune/fundamentals/what-is-intune).

### Users

Your users need accounts that are in Azure AD. If you're also using AD DS or Azure AD DS in your deployment of Azure Virtual Desktop, these accounts will need to be [hybrid identities](../active-directory/hybrid/whatis-hybrid-identity.md), which means the user account is synchronized. You'll need to keep the following things in mind based on which account you use:

- If you're using Azure AD with AD DS, you'll need to configure [Azure AD Connect](../active-directory/hybrid/whatis-azure-ad-connect.md) to synchronize user identity data between AD DS and Azure AD.
- If you're using Azure AD with Azure AD DS, user accounts are synchronized one way from Azure AD to Azure AD DS. This synchronization process is automatic.

### Supported identity scenarios

The following table summarizes identity scenarios that Azure Virtual Desktop currently supports:

| Identity scenario | Session hosts | User accounts |
|--|--|--|
| Azure AD + AD DS | Joined to AD DS | In AD DS and Azure AD, synchronized |
| Azure AD + Azure AD DS | Joined to Azure AD DS | In Azure AD and Azure AD DS, synchronized |
| Azure AD + Azure AD DS + AD DS | Joined to Azure AD DS | In Azure AD and AD DS, synchronized |
| Azure AD only | Joined to Azure AD | In Azure AD |

> [!NOTE]
> If you're planning on using Azure AD only with [FSLogix Profile Container](/fslogix/configure-profile-container-tutorial), you will need to [store profiles on Azure Files](create-profile-container-azure-ad.md), which is currently in public preview. In this scenario, user accounts must be [hybrid identities](../active-directory/hybrid/whatis-hybrid-identity.md), which means you'll also need AD DS and [Azure AD Connect](../active-directory/hybrid/whatis-azure-ad-connect.md). You must create these accounts in AD DS and synchronize them to Azure AD. The service doesn't currently support environments where users are managed with Azure AD and synchronized to Azure AD DS. 

> [!IMPORTANT]
> The user account must exist in the Azure AD tenant you use for Azure Virtual Desktop. Azure Virtual Desktop doesn't support [B2B](../active-directory/external-identities/what-is-b2b.md), [B2C](../active-directory-b2c/overview.md), or personal Microsoft accounts.
>
> When using hybrid identities, either the UserPrincipalName (UPN) or the Security Identifier (SID) must match across Active Directory Domain Services and Azure Active Directory. For more information, see [Supported identities and authentication methods](authentication.md#hybrid-identity).

### Deployment parameters

You'll need to enter the following identity parameters when deploying session hosts:

- Domain name, if using AD DS or Azure AD DS.
- Credentials to join session hosts to the domain.
- Organizational Unit (OU), which is an optional parameter that lets you place session hosts in the desired OU at deployment time.

> [!IMPORTANT]
> The account you use for joining a domain can't have multi-factor authentication (MFA) enabled.
> 
> When joining an Azure AD DS domain, the account you use must be part of the Azure AD DC administrators group.

## Operating systems and licenses

You have a choice of operating systems that you can use for session hosts to provide virtual desktops and remote apps. You can use different operating systems with different host pools to provide flexibility to your users. Supported dates are inline with the [Microsoft Lifecycle Policy](/lifecycle/). We support the following 64-bit versions of these operating systems: 

|Operating system |User access rights|
|---|---|
|<ul><li>Windows 11 Enterprise multi-session</li><li>Windows 11 Enterprise</li><li>Windows 10 Enterprise multi-session, version 1909 and later</li><li>Windows 10 Enterprise, version 1909 and later</li><li>Windows 7 Enterprise</li></ul>|License entitlement:<ul><li>Microsoft 365 E3, E5, A3, A5, F3, Business Premium, Student Use Benefit</li><li>Windows Enterprise E3, E5</li><li>Windows VDA E3, E5</li><li>Windows Education A3, A5</li></ul>External users can use [per-user access pricing](https://azure.microsoft.com/pricing/details/virtual-desktop/) instead of license entitlement.</li></ul>|
|<ul><li>Windows Server 2022</li><li>Windows Server 2019</li><li>Windows Server 2016</li><li>Windows Server 2012 R2</li></ul>|License entitlement:<ul><li>Remote Desktop Services (RDS) Client Access License (CAL) with Software Assurance (per-user or per-device), or RDS User Subscription Licenses.</li></ul>Per-user access pricing is not available for Windows Server operating systems.|

> [!NOTE]
> Azure Virtual Desktop doesn't support 32-bit operating systems or SKUs not listed in the previous table. In addition, Windows 7 doesn't support any VHD or VHDX-based profile solutions hosted on managed Azure Storage due to a sector size limitation.

You can use operating system images provided by Microsoft in the [Azure Marketplace](https://azuremarketplace.microsoft.com), or your own custom images stored in an Azure Compute Gallery, as a managed image, or storage blob. To learn more about how to create custom images, see:

- [Store and share images in an Azure Compute Gallery](../virtual-machines/shared-image-galleries.md).
- [Create a managed image of a generalized VM in Azure](../virtual-machines/windows/capture-image-resource.md).
- [Prepare a Windows VHD or VHDX to upload to Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

You can deploy virtual machines (VMs) to be used as session hosts from these images with any of the following methods:

- Automatically, as part of the [host pool setup process](create-host-pools-azure-marketplace.md).
- Manually, in the Azure portal and [adding to a host pool after you've created it](expand-existing-host-pool.md).
- Programmatically, with [Azure CLI, PowerShell](create-host-pools-powershell.md), or [REST API](/rest/api/desktopvirtualization/).

There are different automation and deployment options available depending on which operating system and version you choose, as shown in the following table:

|Operating system|Azure Image Gallery|Manual VM deployment|Azure Resource Manager template integration|Deploy host pools from Azure Marketplace|
|--------------------------------------|:------:|:------:|:------:|:------:|
|Windows 11 Enterprise multi-session|Yes|Yes|Yes|Yes|
|Windows 11 Enterprise|Yes|Yes|No|No|
|Windows 10 Enterprise multi-session, version 1909 and later|Yes|Yes|Yes|Yes|
|Windows 10 Enterprise, version 1909 and later|Yes|Yes|No|No|
|Windows 7 Enterprise|Yes|Yes|No|No|
|Windows Server 2022|Yes|Yes|No|No|
|Windows Server 2019|Yes|Yes|Yes|Yes|
|Windows Server 2016|Yes|Yes|No|No|
|Windows Server 2012 R2|Yes|Yes|No|No|

## Network

There are several network requirements you'll need to meet to successfully deploy Azure Virtual Desktop. This lets users connect to their virtual desktops and remote apps while also giving them the best possible user experience.

Users connecting to Azure Virtual Desktop use Transmission Control Protocol (TCP) or User Datagram Protocol (UDP) on port 443, which securely establishes a reverse connection to the service. This means you don't need to open any inbound ports.

To successfully deploy Azure Virtual Desktop, you'll need to meet the following network requirements:

- You'll need a virtual network for your session hosts. If you create your session hosts at the same time as a host pool, you must create this virtual network in advance for it to appear in the drop-down list. Your virtual network must be in the same Azure region as the session host.

- Make sure this virtual network can connect to your domain controllers and relevant DNS servers if you're using AD DS or Azure AD DS, since you'll need to join session hosts to the domain.

- Your session hosts and users need to be able to connect to the Azure Virtual Desktop service. This connection also uses TCP on port 443 to a specific list of URLs. For more information, see [Required URL list](safe-url-list.md). You must make sure these URLs aren't blocked by network filtering or a firewall in order for your deployment to work properly and be supported. If your users need to access Microsoft 365, make sure your session hosts can connect to [Microsoft 365 endpoints](/microsoft-365/enterprise/microsoft-365-endpoints).

Also consider the following:

- Your users may need access to applications and data that is hosted on different networks, so make sure your session hosts can connect to them.

- Round-trip time (RTT) latency from the client's network to the Azure region that contains the host pools should be less than 150 ms. Use the [Experience Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/) to view your connection health and recommended Azure region. To optimize for network performance, we recommend you create session hosts in the Azure region closest to your users.

- Use [Azure Firewall for Azure Virtual Desktop deployments](../firewall/protect-azure-virtual-desktop.md) to help you lock down your environment and filter outbound traffic.

> [!NOTE]
> To keep Azure Virtual Desktop reliable and scalable, we aggregate traffic patterns and usage to check the health and performance of the infrastructure control plane. We aggregate this information from all locations where the service infrastructure is, then send it to the US region. The data sent to the US region includes scrubbed data, but not customer data. For more information, see [Data locations for Azure Virtual Desktop](data-locations.md).

To learn more, see [Understanding Azure Virtual Desktop network connectivity](network-connectivity.md).

## Remote Desktop clients

Your users will need a [Remote Desktop client](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) to connect to virtual desktops and remote apps. The following clients support Azure Virtual Desktop:

- [Windows Desktop client](./user-documentation/connect-windows-7-10.md)
- [Web client](./user-documentation/connect-web.md)
- [macOS client](./user-documentation/connect-macos.md)
- [iOS client](./user-documentation/connect-ios.md)
- [Android client](./user-documentation/connect-android.md)
- [Microsoft Store client](./user-documentation/connect-microsoft-store.md)

> [!IMPORTANT]
> Azure Virtual Desktop doesn't support connections from the RemoteApp and Desktop Connections (RADC) client or the Remote Desktop Connection (MSTSC) client.

To learn which URLs clients use to connect and that you must allow through firewalls and internet filters, see the [Required URL list](safe-url-list.md).

## Next steps

Get started with Azure Virtual Desktop by creating a host pool. Head to the following tutorial to find out more.

> [!div class="nextstepaction"]
> [Create a host pool with the Azure portal](create-host-pools-azure-marketplace.md)
