---
title: Azure Arc resource bridge maintenance operations
description: Learn how to manage Azure Arc resource bridge so that it remains online and operational.
ms.topic: conceptual
ms.date: 11/03/2023
---

# Azure Arc resource bridge maintenance operations

To keep your Azure Arc resource bridge deployment online and operational, you need to perform maintenance operations such as updating credentials,  monitoring upgrades and ensuring the appliance VM is online.

## Prerequisites

To maintain the on-premises appliance VM, the [appliance configuration files generated during deployment](deploy-cli.md#az-arcappliance-createconfig) need to be saved in a secure location and made available on the management machine. 

The management machine used to perform maintenance operations must meet all of [the Arc resource bridge requirements](system-requirements.md).  

The following sections describe the maintenance tasks for Arc resource bridge.

## Update credentials in the appliance VM

Arc resource bridge consists of an on-premises appliance VM. The appliance VM [stores credentials](system-requirements.md#user-account-and-credentials) (for example, a user account for VMware vCenter) used to access the control center of the on-premises infrastructure to view and manage on-premises resources. The credentials used by Arc resource bridge are the same ones provided during deployment of the resource bridge. This allows the resource bridge visibility to on-premises resources for guest management in Azure.

If the credentials change, the credentials stored in the Arc resource bridge need to be updated with the [`update-infracredentials` command](/cli/azure/arcappliance/update-infracredentials). This command must be run from the management machine, and it requires a [kubeconfig file](system-requirements.md#kubeconfig). 

Reference: [Arc-enabled VMware - Update the credentials stored in Arc resource bridge](../vmware-vsphere/administer-arc-vmware.md#updating-the-vsphere-account-credentials-using-a-new-password-or-a-new-vsphere-account-after-onboarding)

## Troubleshoot Arc resource bridge

If you experience problems with the appliance VM, the appliance configuration files can help with troubleshooting. You can include these files when you [open an Azure support request](../../azure-portal/supportability/how-to-create-azure-support-request.md).

You might want to [collect logs](/cli/azure/arcappliance/logs#az-arcappliance-logs-vmware), which requires you to pass credentials to the on-premises control center:

- For VMWare vSphere, use the username and password provided to Arc resource bridge at deployment.
- For Azure Stack HCI, use the cloud service IP and HCI login configuration file path.

## Delete Arc resource bridge

You might need to delete Arc resource bridge due to deployment failures or when no longer needed. To do so, you need the appliance configuration files. The [delete command](deploy-cli.md#az-arcappliance-delete) is the recommended way to delete the bridge. This command deletes the on-premises appliance VM along with the Azure resource and underlying components across the two environments.

## Next steps

- Learn about [upgrading Arc resource bridge](upgrade.md).
- Review the [Azure Arc resource bridge overview](overview.md) to understand more about requirements and technical details.
- Learn about [system requirements for Azure Arc resource bridge](system-requirements.md).
