---
title: What's new in Azure Automation
description: Significant updates to Azure Automation updated each month.
services: automation
ms.subservice: 
ms.topic: overview
ms.date: 11/02/2021
ms.custom: references_regions
---

# What's new in Azure Automation?

Azure Automation receives improvements on an ongoing basis. To stay up to date with the most recent developments, this article provides you with information about:

- The latest releases
- New features
- Improvements to existing features
- Known issues
- Bug fixes


This page is updated monthly, so revisit it regularly. If you're looking for items older than six months, you can find them in [Archive for What's new in Azure Automation](whats-new-archive.md).


## March 2022

###  Forward diagnostic audit data to Azure Monitor logs

**Type:** New feature

Azure Automation can send diagnostic audit logs in addition to runbook job status and job streams to your Log Analytics workspace. Read [here](automation-manage-send-joblogs-log-analytics.md) for more information.

## February 2022

### Permissions change in the built-in Reader role for the Automation Account.

**Type:** New change

To strengthen the overall Azure Automation security posture, the built-in RBAC Reader role would not have access to Automation account keys through the API call - `GET /automationAccounts/agentRegistrationInformation`. Read [here](./automation-role-based-access-control.md#reader) for more information.


### Restore deleted Automation Accounts 

**Type:** New change 

Users can now restore an Automation account deleted within 30 days. Read [here](./delete-account.md?tabs=azure-portal#restore-a-deleted-automation-account) for more information.


## December 2021

### New scripts added for Azure VM management based on Azure Monitor Alert

**Type:** New feature

New scripts are added to the Azure Automation [GitHub repository](https://github.com/azureautomation) to address one of Azure Automation's key scenarios of VM management based on Azure Monitor alert. For more information, see [Trigger runbook from Azure alert](./automation-create-alert-triggered-runbook.md#common-azure-vm-management-operations).

- Stop-Azure-VM-On-Alert
- Restart-Azure-VM-On-Alert
- Delete-Azure-VM-On-Alert
- ScaleDown-Azure-VM-On-Alert
- ScaleUp-Azure-VM-On-Alert

## November 2021 

### General Availability of Managed Identity for Azure Automation 

**Type:** New feature

Azure Automation now supports Managed Identities in Azure public, Azure Gov, and Azure China cloud. [System Assigned Managed Identities](./enable-managed-identity-for-automation.md) is supported for cloud as well as hybrid jobs, while  [User Assigned Managed Identities](./automation-security-overview.md) is supported only for cloud jobs. Read the [announcement](https://azure.microsoft.com/updates/azure-automation-managed-identities-ga/) for more information.

### Preview support for PowerShell 7.1 

**Type:** New feature

Azure Automation support for PowerShell 7.1 runbooks is available as public preview in Azure public, Azure Gov, and Azure China clouds. Read the [announcement](https://azure.microsoft.com/updates/azure-automation-powershell-7/) for more information.



## October 2021

### Preview support for Hybrid Runbook Worker extension for Azure VMs and Arc-enabled servers

**Type:** New feature

Azure Automation released native integration of User Hybrid Runbook Worker for Azure VMs, and for non-Azure machines through Arc-enabled servers. Read the [announcement](https://azure.microsoft.com/updates/automation-user-hybrid-extension-support) for more information.

### Preview support for Azure Active Directory authentication

**Type:** New feature

Azure Automation added a critical security feature with Azure AD authentication support for all Automation service public endpoints. The feature has been implemented through Hybrid Runbook Worker extension support for Azure VMs and Arc-enabled servers.

This removes the dependency on certificates and enables you to meet your stringent audit and compliance requirements by not using local authentication methods. Read the [announcement](https://azure.microsoft.com/updates/automation-user-hybrid-extension-support) for more information.

### Source control enabled to use managed identities

**Type:** New feature

Source control integration in Azure Automation can now use [managed identities](automation-security-overview.md#managed-identities) instead of a Run As account. For more information, see [source control integration prerequisites](source-control-integration.md#prerequisites).

## September

### Support for Az modules by default

**Type:** New Feature

Azure Automation now supports Az modules by default. New Automation accounts created include the latest version of Az modules - 6.4.0 by default. Automation also includes an option in the Azure portal - **Update Az Modules** enabling you to update Az modules in your existing Automation accounts. Read the [announcement](https://azure.microsoft.com/updates/azure-automation-az-module-support) for more information.

## August 2021

### Azure Policy Guest Configuration

**Type:** Plan for change

Customers should evaluate and plan for migration from Azure Automation State Configuration to Azure Policy guest configuration. For more information, see [Azure Policy guest configuration](../governance/policy/concepts/guest-configuration.md).

## July 2021

### Preview support for user-assigned managed identity

**Type:** New feature

Azure Automation now supports [user-assigned Managed Identities](automation-secure-asset-encryption.md) for cloud jobs in Azure global, Azure Government, and Azure China regions. Read the [announcement](https://azure.microsoft.com/updates/azure-automation-user-assigned-identities/) for more information.

### General Availability of customer-managed keys for Azure Automation

**Type:** New feature

Customers can manage and secure encryption of Azure Automation assets using their own managed keys. With the introduction of customer-managed keys, you can supplement default encryption with an extra encryption layer using keys that you create and manage in Azure Key Vault. This additional level of encryption should help you meet your organization’s regulatory or compliance needs.

For more information, see [Use of customer-managed keys](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account).

## June 2021

### Security update for Log Analytics Contributor role

**Type:** Plan for change

Microsoft intends to remove the Automation account rights from the Log Analytics Contributor role. Currently, the built-in [Log Analytics Contributor](./automation-role-based-access-control.md#log-analytics-contributor) role can escalate privileges to the subscription [Contributor](./../role-based-access-control/built-in-roles.md#contributor) role. Since Automation account Run As accounts are initially configured with Contributor rights on the subscription, it can be used by an attacker to create new runbooks and execute code as a Contributor on the subscription.

As a result of this security risk, we recommend you don't use the Log Analytics Contributor role to execute Automation jobs. Instead, create the Azure Automation Contributor custom role and use it for actions related to the Automation account.

### Support for Automation and State Configuration available in West US 3

**Type:** New feature

For more information, see [Data residency in Azure](https://azure.microsoft.com/global-infrastructure/data-residency/) and select your geography from the drop-down list.

## May 2021

### Start/Stop VMs during off-hours (v1)

**Type:** Plan for change

Start/Stop VMs during off-hours (v1) will deprecate on May 21, 2022. Customers should evaluate and plan for migration to the Start/Stop VMs v2 (preview). For more information, see [Start/Stop v2 overview](../azure-functions/start-stop-vms/overview.md) (preview).

## April 2021

### Support for Update Management and Change Tracking

**Type:** New feature

Region mapping has been updated to support Update Management and Change Tracking in Norway East, UAE North, North Central US, Brazil South, and Korea Central. For more information, see [Supported mappings](./how-to/region-mappings.md#supported-mappings).

### Support for system-assigned Managed Identities

**Type:** New feature

Azure Automation now supports [system-assigned managed identities](./automation-security-overview.md#managed-identities) for cloud and hybrid jobs in Azure global and Azure Government regions. Read the [announcement](https://azure.microsoft.com/updates/azure-automation-system-assigned-managed-identities/) for more information.

## Next steps

If you'd like to contribute to Azure Automation documentation, see the [Docs Contributor Guide](/contribute/).