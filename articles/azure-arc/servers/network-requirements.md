---
title: Connected Machine agent network requirements
description: Learn about the networking requirements for using the Connected Machine agent for Azure Arc-enabled servers.
ms.date: 07/26/2022
ms.topic: conceptual 
---

# Connected Machine agent network requirements

This topic describes the networking requirements for using the Connected Machine agent to onboard a physical server or virtual machine to Azure Arc-enabled servers.

## Networking configuration

The Azure Connected Machine agent for Linux and Windows communicates outbound securely to Azure Arc over TCP port 443. By default, the agent uses the default route to the internet to reach Azure services. You can optionally [configure the agent to use a proxy server](manage-agent.md#update-or-remove-proxy-settings) if your network requires it. Proxy servers don't make the Connected Machine agent more secure because the traffic is already encrypted.

To further secure your network connectivity to Azure Arc, instead of using public networks and proxy servers, you can implement an [Azure Arc Private Link Scope](private-link-security.md) .

> [!NOTE]
> Azure Arc-enabled servers does not support using a [Log Analytics gateway](../../azure-monitor/agents/gateway.md) as a proxy for the Connected Machine agent.

If outbound connectivity is restricted by your firewall or proxy server, make sure the URLs and Service Tags listed below are not blocked.

## Service tags

Be sure to allow access to the following Service Tags:

* AzureActiveDirectory
* AzureTrafficManager
* AzureResourceManager
* AzureArcInfrastructure
* Storage
* WindowsAdminCenter (if [using Windows Admin Center to manage Arc-enabled servers](/windows-server/manage/windows-admin-center/azure/manage-arc-hybrid-machines))

For a list of IP addresses for each service tag/region, see the JSON file [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519). Microsoft publishes weekly updates containing each Azure Service and the IP ranges it uses. This information in the JSON file is the current point-in-time list of the IP ranges that correspond to each service tag. The IP addresses are subject to change. If IP address ranges are required for your firewall configuration, then the **AzureCloud** Service Tag should be used to allow access to all Azure services. Do not disable security monitoring or inspection of these URLs, allow them as you would other Internet traffic.

For more information, see [Virtual network service tags](../../virtual-network/service-tags-overview.md).

## URLs

The table below lists the URLs that must be available in order to install and use the Connected Machine agent.

### [Azure Cloud](#tab/azure-cloud)

| Agent resource | Description | When required| Endpoint used with private link |
|---------|---------|--------|---------|
|`aka.ms`|Used to resolve the download script during installation|At installation time, only| Public |
|`download.microsoft.com`|Used to download the Windows installation package|At installation time, only| Public |
|`packages.microsoft.com`|Used to download the Linux installation package|At installation time, only| Public |
|`login.windows.net`|Azure Active Directory|Always| Public |
|`login.microsoftonline.com`|Azure Active Directory|Always| Public |
|`pas.windows.net`|Azure Active Directory|Always| Public |
|`management.azure.com`|Azure Resource Manager - to create or delete the Arc server resource|When connecting or disconnecting a server, only| Public, unless a [resource management private link](../../azure-resource-manager/management/create-private-link-access-portal.md) is also configured |
|`*.his.arc.azure.com`|Metadata and hybrid identity services|Always| Private |
|`*.guestconfiguration.azure.com`| Extension management and guest configuration services |Always| Private |
|`guestnotificationservice.azure.com`, `*.guestnotificationservice.azure.com`|Notification service for extension and connectivity scenarios|Always| Private |
|`azgn*.servicebus.windows.net`|Notification service for extension and connectivity scenarios|Always| Public |
|`*.servicebus.windows.net`|For Windows Admin Center and SSH scenarios|If using SSH or Windows Admin Center from Azure|Public|
|`*.blob.core.windows.net`|Download source for Azure Arc-enabled servers extensions|Always, except when using private endpoints| Not used when private link is configured |
|`dc.services.visualstudio.com`|Agent telemetry|Optional| Public |

### [Azure Government](#tab/azure-government)

| Agent resource | Description | When required| Endpoint used with private link |
|---------|---------|--------|---------|
|`aka.ms`|Used to resolve the download script during installation|At installation time, only| Public |
|`download.microsoft.com`|Used to download the Windows installation package|At installation time, only| Public |
|`packages.microsoft.com`|Used to download the Linux installation package|At installation time, only| Public |
|`login.microsoftonline.us`|Azure Active Directory|Always| Public |
|`pasff.usgovcloudapi.net`|Azure Active Directory|Always| Public |
|`management.usgovcloudapi.net`|Azure Resource Manager - to create or delete the Arc server resource|When connecting or disconnecting a server, only| Public, unless a [resource management private link](../../azure-resource-manager/management/create-private-link-access-portal.md) is also configured |
|`*.his.arc.azure.us`|Metadata and hybrid identity services|Always| Private |
|`*.guestconfiguration.azure.us`| Extension management and guest configuration services |Always| Private |
|`*.blob.core.usgovcloudapi.net`|Download source for Azure Arc-enabled servers extensions|Always, except when using private endpoints| Not used when private link is configured |
|`dc.applicationinsights.us`|Agent telemetry|Optional| Public |

### [Azure China](#tab/azure-china)

> [!NOTE]
> Private link is not available for Azure Arc-enabled servers in Azure China regions.

| Agent resource | Description | When required|
|---------|---------|--------|
|`aka.ms`|Used to resolve the download script during installation|At installation time, only|
|`download.microsoft.com`|Used to download the Windows installation package|At installation time, only|
|`packages.microsoft.com`|Used to download the Linux installation package|At installation time, only|
|`login.chinacloudapi.cn`|Azure Active Directory|Always|
|`login.partner.chinacloudapi.cn`|Azure Active Directory|Always|
|`pas.chinacloudapi.cn`|Azure Active Directory|Always|
|`management.chinacloudapi.cn`|Azure Resource Manager - to create or delete the Arc server resource|When connecting or disconnecting a server, only|
|`*.his.arc.azure.cn`|Metadata and hybrid identity services|Always|
|`*.guestconfiguration.azure.cn`| Extension management and guest configuration services |Always|
|`guestnotificationservice.azure.cn`, `*.guestnotificationservice.azure.cn`|Notification service for extension and connectivity scenarios|Always|
|`azgn*.servicebus.chinacloudapi.cn`|Notification service for extension and connectivity scenarios|Always|
|`*.servicebus.chinacloudapi.cn`|For Windows Admin Center and SSH scenarios|If using SSH or Windows Admin Center from Azure|
|`*.blob.core.chinacloudapi.cn`|Download source for Azure Arc-enabled servers extensions|Always, except when using private endpoints|
|`dc.applicationinsights.azure.cn`|Agent telemetry|Optional|

---

## Transport Layer Security 1.2 protocol

To ensure the security of data in transit to Azure, we strongly encourage you to configure machine to use Transport Layer Security (TLS) 1.2. Older versions of TLS/Secure Sockets Layer (SSL) have been found to be vulnerable and while they still currently work to allow backwards compatibility, they are **not recommended**.

|Platform/Language | Support | More Information |
| --- | --- | --- |
|Linux | Linux distributions tend to rely on [OpenSSL](https://www.openssl.org) for TLS 1.2 support. | Check the [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) to confirm your version of OpenSSL is supported.|
| Windows Server 2012 R2 and higher | Supported, and enabled by default. | To confirm that you are still using the [default settings](/windows-server/security/tls/tls-registry-settings).|

## Next steps

* Review additional [prerequisites for deploying the Connected Machine agent](prerequisites.md).
* Before you deploy the Azure Arc-enabled servers agent and integrate with other Azure management and monitoring services, review the [Planning and deployment guide](plan-at-scale-deployment.md).
* To resolve problems, review the [agent connection issues troubleshooting guide](troubleshoot-agent-onboard.md).
