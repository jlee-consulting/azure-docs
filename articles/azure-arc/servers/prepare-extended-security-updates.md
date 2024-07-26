---
title: How to prepare to deliver Extended Security Updates for Windows Server 2012 through Azure Arc
description: Learn how to prepare to deliver Extended Security Updates for Windows Server 2012 through Azure Arc.
ms.date: 07/03/2024
ms.topic: how-to
---

# Prepare to deliver Extended Security Updates for Windows Server 2012

With Windows Server 2012 and Windows Server 2012 R2 having reached end of support on October 10, 2023, Azure Arc-enabled servers lets you enroll your existing Windows Server 2012/2012 R2 machines in [Extended Security Updates (ESUs)](/windows-server/get-started/extended-security-updates-overview). Affording both cost flexibility and an enhanced delivery experience, Azure Arc better positions you to migrate to Azure.

The purpose of this article is to help you understand the benefits and how to prepare to use Arc-enabled servers to enable delivery of ESUs.

> [!NOTE]
> Azure VMware Solutions (AVS) machines are eligible for free ESUs and should not enroll in ESUs enabled through Azure Arc.
> 
## Key benefits

Delivering ESUs to your Windows Server 2012/2012 R2 machines provides the following key benefits:

- **Pay-as-you-go:** Flexibility to sign up for a monthly subscription service with the ability to migrate mid-year.

- **Azure billed:** You can draw down from your existing [Microsoft Azure Consumption Commitment (MACC)](/marketplace/azure-consumption-commitment-benefit) and analyze your costs using [Microsoft Cost Management and Billing](../../cost-management-billing/cost-management-billing-overview.md).

- **Built-in inventory:** The coverage and enrollment status of Windows Server 2012/2012 R2 ESUs on eligible Arc-enabled servers are identified in the Azure portal, highlighting gaps and status changes.

- **Keyless delivery:** The enrollment of ESUs on Azure Arc-enabled Windows Server 2012/2012 R2 machines won't require the acquisition or activation of keys.

## Access to Azure services

For Azure Arc-enabled servers enrolled in WS2012 ESUs enabled by Azure Arc, free access is provided to these Azure services from October 10, 2023:

* [Azure Update Manager](../../update-center/overview.md) - Unified management and governance of update compliance that includes not only Azure and hybrid machines, but also ESU update compliance for all your Windows Server 2012/2012 R2 machines.
    Enrollment in ESUs does not impact Azure Update Manager. After enrollment in ESUs through Azure Arc, the server becomes eligible for ESU patches. These patches can be delivered through Azure Update Manager or any other patching solution. You'll still need to configure updates from Microsoft Updates or Windows Server Update Services.
* [Azure Automation Change Tracking and Inventory](/azure/automation/change-tracking/overview?tabs=python-2) - Track changes in virtual machines hosted in Azure, on-premises, and other cloud environments.
* [Azure Policy Guest Configuration](/azure/cloud-adoption-framework/manage/azure-server-management/guest-configuration-policy) - Audit the configuration settings in a virtual machine. Guest configuration supports Azure VMs natively and non-Azure physical and virtual servers through Azure Arc-enabled servers.

Other Azure services through Azure Arc-enabled servers are available as well, with offerings such as:

* [Microsoft Defender for Cloud](../../defender-for-cloud/defender-for-cloud-introduction.md) - As part of the cloud security posture management (CSPM) pillar, it provides server protections through [Microsoft Defender for Servers](../../defender-for-cloud/plan-defender-for-servers.md) to help protect you from various cyber threats and vulnerabilities.
* [Microsoft Sentinel](scenario-onboard-azure-sentinel.md) - Collect security-related events and correlate them with other data sources.
   
## Prepare delivery of ESUs

Plan and prepare to onboard your machines to Azure Arc-enabled servers through the installation of the [Azure Connected Machine agent](agent-overview.md) (version 1.34 or higher) to establish a connection to Azure. Windows Server 2012 Extended Security Updates supports Windows Server 2012 and R2 Standard and Datacenter editions. Windows Server 2012 Storage is not supported.

We recommend you deploy your machines to Azure Arc in preparation for when the related Azure services deliver supported functionality to manage ESU. Once these machines are onboarded to Azure Arc-enabled servers, you'll have visibility into their ESU coverage and enroll through the Azure portal or using Azure Policy. Billing for this service starts from October 2023 (i.e., after Windows Server 2012 end of support).

> [!NOTE]
> In order to purchase ESUs, you must have Software Assurance through Volume Licensing Programs such as an Enterprise Agreement (EA), Enterprise Agreement Subscription (EAS), Enrollment for Education Solutions (EES), Server and Cloud Enrollment (SCE), or through Microsoft Open Value Programs. Alternatively, if your Windows Server 2012/2012 R2 machines are licensed through SPLA or with a Server Subscription, Software Assurance is not required to purchase ESUs.

You must also download both the licensing package and servicing stack update (SSU) for the Azure Arc-enabled server as documented at [KB5031043: Procedure to continue receiving security updates after extended support has ended on October 10, 2023](https://support.microsoft.com/topic/kb5031043-procedure-to-continue-receiving-security-updates-after-extended-support-has-ended-on-october-10-2023-c1a20132-e34c-402d-96ca-1e785ed51d45).

### Deployment options

There are several at-scale onboarding options for Azure Arc-enabled servers, including running a [Custom Task Sequence](onboard-configuration-manager-custom-task.md) through Configuration Manager and deploying a [Scheduled Task through Group Policy](onboard-group-policy-powershell.md). There are also at-scale ESU delivery options for [VMware vCenter managed VMs](../vmware-vsphere/deliver-extended-security-updates-for-vmware-vms-through-arc.md) and [SCVMM managed VMs](../system-center-virtual-machine-manager/deliver-esus-for-system-center-virtual-machine-manager-vms.md) through Azure Arc.

> [!NOTE]
> Delivery of ESUs through Azure Arc to virtual machines running on Virtual Desktop Infrastructure (VDI) is not recommended. VDI systems should use Multiple Activation Keys (MAK) to apply ESUs. See [Access your Multiple Activation Key from the Microsoft 365 Admin Center](/windows-server/get-started/extended-security-updates-deploy) to learn more.
> 

### Networking

Connectivity options include public endpoint, proxy server, and private link or Azure Express Route. Review the [networking prerequisites](network-requirements.md) to prepare non-Azure environments for deployment to Azure Arc.

[!INCLUDE [esu-network-requirements](./includes/esu-network-requirements.md)]

> [!TIP]
> To take advantage of the full range of offerings for Arc-enabled servers, such as extensions and remote connectivity, ensure that you allow the additional URLs that apply to your scenario. For more information, see [Connected machine agent networking requirements](network-requirements.md).

## Required Certificate Authorities

The following [Certificate Authorities](/azure/security/fundamentals/azure-ca-details?tabs=root-and-subordinate-cas-list) are required for Extended Security Updates for Windows Server 2012:

- [Microsoft Azure RSA TLS Issuing CA 03](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20RSA%20TLS%20Issuing%20CA%2003%20-%20xsign.crt)
- [Microsoft Azure RSA TLS Issuing CA 04](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20RSA%20TLS%20Issuing%20CA%2004%20-%20xsign.crt)
- [Microsoft Azure RSA TLS Issuing CA 07](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20RSA%20TLS%20Issuing%20CA%2007%20-%20xsign.crt)
- [Microsoft Azure RSA TLS Issuing CA 08](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20RSA%20TLS%20Issuing%20CA%2008%20-%20xsign.crt)

If necessary, these Certificate Authorities can be [manually download and installed](troubleshoot-extended-security-updates.md#option-2-manually-download-and-install-the-intermediate-ca-certificates).

## Next steps

* Find out more about [planning for Windows Server and SQL Server end of support](https://www.microsoft.com/en-us/windows-server/extended-security-updates) and [getting Extended Security Updates](/windows-server/get-started/extended-security-updates-deploy).

* Learn about best practices and design patterns through the [Azure Arc landing zone accelerator for hybrid and multicloud](/azure/cloud-adoption-framework/scenarios/hybrid/arc-enabled-servers/eslz-identity-and-access-management).
* Learn more about [Arc-enabled servers](overview.md) and how they work with Azure through the Azure Connected Machine agent.
* Explore options for [onboarding your machines](plan-at-scale-deployment.md) to Azure Arc-enabled servers.
