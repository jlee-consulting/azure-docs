---
title: Automatic Extension Upgrade for VMs and Scale Sets in Azure
description: Learn how to enable the Automatic Extension Upgrade for your virtual machines and virtual machine scale sets in Azure.
ms.service: virtual-machines
ms.subservice: extensions
ms.topic: how-to
ms.reviewer: jushiman
ms.date: 11/7/2023
ms.custom: devx-track-azurepowershell
---

# Automatic Extension Upgrade for VMs and Scale Sets in Azure

Automatic Extension Upgrade is available for Azure VMs and Azure Virtual Machine Scale Sets. When Automatic Extension Upgrade is enabled on a VM or scale set, the extension is upgraded automatically whenever the extension publisher releases a new version for that extension.

 Automatic Extension Upgrade has the following features:
- Supported for Azure VMs and Azure Virtual Machine Scale Sets.
- Upgrades are applied in an availability-first deployment model.
- For a Virtual Machine Scale Set, no more than 20% of the scale set virtual machines upgrades in a single batch. The minimum batch size is one virtual machine.
- Works for all VM sizes, and for both Windows and Linux extensions.
- You can opt out of automatic upgrades at any time.
- Automatic extension upgrade can be enabled on a Virtual Machine Scale Sets of any size.
- Each supported extension is enrolled individually, and you can choose which extensions to upgrade automatically.
- Supported in all public cloud regions.

## How does Automatic Extension Upgrade work?
The extension upgrade process replaces the existing extension version on a VM with a new version of the same extension when published by the extension publisher. The health of the VM is monitored after the new extension is installed. If the VM isn't in a healthy state within 5 minutes of the upgrade completion, the extension version is rolled back to the previous version.

A failed extension upgrade is automatically retried. A retry is attempted every few days automatically without user intervention.

### Availability-first Updates
The availability-first model for platform orchestrated upgrades ensures that availability configurations in Azure are respected across multiple availability levels.

For a group of virtual machines undergoing an upgrade, the Azure platform orchestrates upgrades:

**Across regions:**
- An upgrade moves across Azure globally in a phased manner to prevent Azure-wide deployment failures.
- A 'phase' can have one or more regions, and an upgrade moves across phases only if eligible VMs in the previous phase upgrade successfully.
- Geo-paired regions won't be upgraded concurrently and can't be in the same regional phase.
- The success of an upgrade is measured by tracking the health of a VM post upgrade. VM health is tracked through platform health indicators for the VM. For Virtual Machine Scale Sets, the VM health is tracked through application health probes or the Application Health extension, if applied to the scale set.

**Within a region:**
- VMs in different Availability Zones aren't upgraded concurrently with the same upgrade.
- Single VMs that aren't part of an availability set are batched on a best effort basis to avoid concurrent upgrades for all VMs in a subscription.  

**Within a 'set':**
- All VMs in a common availability set or scale set aren't upgraded concurrently.  
- VMs in a common availability set are upgraded within Update Domain boundaries and VMs across multiple Update Domains aren't upgraded concurrently.  
- VMs in a common virtual machine scale set are grouped in batches and upgrded within Update Domain boundaries. [Upgrade policies](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-policy.md) defined on the scale set are honored during the upgrade. If upgrade policy is set to Manual, VMs won't get upgraded even if automatic extension upgrade is enabled. 

### Upgrade process for Virtual Machine Scale Sets
1. Before the upgrade process starts, the orchestrator ensures that no more than 20% of VMs in the entire scale set are unhealthy (for any reason).

2. The upgrade orchestrator identifies the batch of VM instances to upgrade. An upgrade batch can have a maximum of 20% of the total VM count, subject to a minimum batch size of one virtual machine. Definition of Upgrade Policy and Availability Zones is considered while identifying the batch.  

3. After the upgrade, the VM health is always monitored before moving to the next batch. For scale sets with configured application health probes or Application Health extension, application health is also monitored. The upgrade waits up to 5 minutes (or the defined health probe configuration) for the VM to become healthy before upgrading the next batch. If a VM doesn't recover its health after an upgrade, then by default the previous extension version on the VM is reinstalled.

4. The upgrade orchestrator also tracks the percentage of VMs that become unhealthy after an upgrade. The upgrade stops if more than 20% of upgraded instances become unhealthy during the upgrade process.

The above process continues until all instances in the scale set have been upgraded.

The scale set upgrade orchestrator checks for the overall scale set health before upgrading every batch. During a batch upgrade, there could be other concurrent planned or unplanned maintenance activities that could impact the health of your scale set virtual machines. In such cases, if more than 20% of the scale set's instances become unhealthy, then the scale set upgrade stops at the end of current batch.

## Supported extensions
Automatic Extension Upgrade supports the following extensions (and more are added periodically):
- [Azure Automation Hybrid Worker extension](../automation/extension-based-hybrid-runbook-worker-install.md) - Linux and Windows
- Dependency Agent – [Linux](./extensions/agent-dependency-linux.md) and [Windows](./extensions/agent-dependency-windows.md)
- [Application Health Extension](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) – Linux and Windows
- [Guest Attestation Extension](../virtual-machines/boot-integrity-monitoring-overview.md) - Linux and Windows
- [Guest Configuration Extension](./extensions/guest-configuration.md) – Linux and Windows
- Key Vault – [Linux](./extensions/key-vault-linux.md) and [Windows](./extensions/key-vault-windows.md)
- [Azure Monitor Agent](../azure-monitor/agents/azure-monitor-agent-overview.md)
- [Log Analytics Agent for Linux](../azure-monitor/agents/log-analytics-agent.md)
- [Azure Diagnostics extension for Linux](../azure-monitor/agents/diagnostics-extension-overview.md)
- Service Fabric – [Linux](../service-fabric/service-fabric-tutorial-create-vnet-and-linux-cluster.md#service-fabric-extension)

## Enabling Automatic Extension Upgrade

To enable Automatic Extension Upgrade for an extension, you must ensure the property `enableAutomaticUpgrade` is set to `true` and added to every extension definition individually.

### REST API for Virtual Machines
To enable automatic extension upgrade for an extension (in this example the Dependency Agent extension) on an Azure VM, use the following call:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/extensions/<extensionName>?api-version=2019-12-01`
```

```json
{    
    "name": "extensionName",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "location": "<location>",
    "properties": {
        "autoUpgradeMinorVersion": true,
        "enableAutomaticUpgrade": true, 
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5"
        }
}
```

### REST API for Virtual Machine Scale Sets
Use the following call to add the extension to the scale set model:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<vmssName>?api-version=2019-12-01`
```

```json
{
   "location": "<location>",
   "properties": {
   	    "virtualMachineProfile": {
            "extensionProfile": {
       	        "extensions": [
            	{
                "name": "<extensionName>",
            	  "properties": {
             		    "autoUpgradeMinorVersion": true,
             		    "enableAutomaticUpgrade": true,
              	    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
              	    "type": "DependencyAgentWindows",
              	    "typeHandlerVersion": "9.5"
            		}
          	    }
        	    ]
    	    }
    	}
    }
}
```

### Azure PowerShell for Virtual Machines
Use the [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) cmdlet:

```azurepowershell-interactive
Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS `
    -EnableAutomaticUpgrade $true
```


### Azure PowerShell for Virtual Machine Scale Sets
Use the [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet to add the extension to the scale set model:

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

Update the scale set using [Update-AzVmss](/powershell/module/az.compute/update-azvmss) after adding the extension.


### Azure CLI for Virtual Machines
Use the [az vm extension set](/cli/azure/vm/extension#az-vm-extension-set) cmdlet:

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### Azure CLI for Virtual Machine Scale Sets
Use the [az vmss extension set](/cli/azure/vmss/extension#az-vmss-extension-set) cmdlet to add the extension to the scale set model:

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### ARM template for Virtual Machines
The following example describes how to set automatic extension upgrades for an extension (Dependency Agent Extension in this example) on a Virtual Machine using Azure Resource Manager

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "location": "[resourceGroup().location]",
    "name": "<extensionName>",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true,
        "enableAutomaticUpgrade": true,
        "settings": {
            "enableAMA": "true"
        }
    }
}
```

### ARM template for Virtual Machine Scale Sets
Use the following example to set automatic extension upgrade on the extension within the scale set model:

```json
{
   "type": "Microsoft.Compute/virtualMachineScaleSets",
   "apiVersion": "2023-09-01",
   "name": "[variables('vmScaleSetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
   	    "virtualMachineProfile": {
            "extensionProfile": {
       	        "extensions": [{
                     "name": "<extensionName>",
                     "properties": {
                          "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                          "type": "DependencyAgentWindows",
                          "typeHandlerVersion": "9.5",
                          "autoUpgradeMinorVersion": true,
                          "enableAutomaticUpgrade": true,
                     }
                }]
    	    }
    	}
    }
}
```

### Using Azure portal
You can use Azure portal - Extension blade to enable automatic upgrade of extensions on existing Virtual Machines and Virtual Machine Scale Sets. 
1. Navigate to [Virtual Machines](https://portal.azure.com/#view/HubsExtension/BrowseResource/resourceType/Microsoft.Compute%2FVirtualMachines) or [Virtual Machines Scale Sets](https://ms.portal.azure.com/#view/HubsExtension/BrowseResource/resourceType/Microsoft.Compute%2FvirtualMachineScaleSets) blade and select the resource by clicking on its name.
2. Navigate to "Extenisons + applications" blade under Settings to view all extensions installed on the resource. The "Automatic Upgrade Status" column tells if Automatic upgrade of the extension is enabled, disabled or not-supported.
3. Navigate to Extension details blade by clicking on the extension name.
:::image type="content" source="media/auto-extension.png" alt-text="Screenshot of Azure portal - Extension blade." lightbox="media/auto-extension.png":::
4. Click "Enable automatic upgrade" to enable automatic upgrade of the extension. This button can also be used to disable automatic upgrade when required.   
:::image type="content" source="media/auto-extension-upgrade.png" alt-text="Screenshot of Azure portal to enable automatic upgrade of the extension.":::

## Extension upgrades with multiple extensions

A VM or Virtual Machine Scale Set can have multiple extensions with automatic extension upgrade enabled. The same VM or scale set can also have other extensions without automatic extension upgrade enabled.  

If multiple extension upgrades are available for a virtual machine, the upgrades may be batched together, but each extension upgrade is applied individually on a virtual machine. A failure on one extension doesn't impact the other extension(s) that may be upgrading. For example, if two extensions are scheduled for an upgrade, and the first extension upgrade fails, the second extension will still be upgraded.

Automatic Extension Upgrades can also be applied when a VM or virtual machine scale set has multiple extensions configured with [extension sequencing](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). Extension sequencing is applicable for the first-time deployment of the VM, and any future extension upgrades on an extension are applied independently.

## Difference between enableAutomaticUpgrade and autoUpgradeMinorVersion
1. AutoUpgradeMinorVersion:
   - This property is used during VM creation and while upgrading the VM with a new configuration.  
   - When set to “true,” it ensures that the latest minor version of the extension is automatically installed on the virtual machine.
   - It overrides the TypeHandlerVersion with the latest stable minor version available.
   - While upgrading the VM configuration, if a new minor version is available, then its considered a configuration change and the extension is reinstalled with latest minor version. 
   - This helps keep newly created VMs up-to-date with the latest stable minor extension version.
   - If you want to manually set the extension to a specific version, set this property to “false.”
     
2. EnableAutomaticUpgrade:
   - This property affects existing virtual machines.
   - It does not impact the version installed during VM creation.
   - After VM creation, if the VM is not running the latest minor version of the extension, enabling this property triggers an automatic upgrade.
   - Upgrades do not cause VM reboot, are rolled out in a safe rolling manner and failed upgrades are rolled back immediately to provide high service availability and reliablity. 
   - It ensures that existing VMs stay secure and up-to-date by automatically updating them to the latest minor version.

It is recommemded to enable both properties to keep all VMs secure and up-to-date. 

Upgrades to major extension versions are never performed automatically by either property since they could cause breaking change. You must manually set the TypeHandlerVersion to a major version and manually upgrade each existing VM to the latest major version.

## Next steps
> [!div class="nextstepaction"]
> [Learn about the Application Health Extension](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
