---
title: Manage the Azure Monitor agent
description: Options for managing the Azure Monitor agent on Azure virtual machines and Azure Arc-enabled servers.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/22/2022
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.reviewer: shseth

---

# Manage the Azure Monitor agent

This article provides the different options currently available to install, uninstall, and update the [Azure Monitor agent](azure-monitor-agent-overview.md). This agent extension can be installed on Azure virtual machines, scale sets, and Azure Arc-enabled servers. It also lists the options to create [associations with data collection rules](data-collection-rule-azure-monitor-agent.md) that define which data the agent should collect. Installing, upgrading, or uninstalling the Azure Monitor agent won't require you to restart your server.

## Virtual machine extension details

The Azure Monitor agent is implemented as an [Azure VM extension](../../virtual-machines/extensions/overview.md) with the details in the following table. You can install it by using any of the methods to install virtual machine extensions including the methods described in this article.

| Property | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft.Azure.Monitor  | Microsoft.Azure.Monitor |
| Type      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | See [Azure Monitor agent extension versions](./azure-monitor-agent-extension-versions.md) | [Azure Monitor agent extension versions](./azure-monitor-agent-extension-versions.md) |

## Extension versions

View [Azure Monitor agent extension versions](./azure-monitor-agent-extension-versions.md).

## Prerequisites

The following prerequisites must be met prior to installing the Azure Monitor agent.

- **Permissions**: For methods other than using the Azure portal, you must have the following role assignments to install the agent:  

   | Built-in role | Scopes | Reason |  
   |:---|:---|:---|  
   | <ul><li>[Virtual Machine Contributor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)</li><li>[Azure Connected Machine Resource Administrator](../../role-based-access-control/built-in-roles.md#azure-connected-machine-resource-administrator)</li></ul> | <ul><li>Virtual machines, scale sets,</li><li>Azure Arc-enabled servers</li></ul> | To deploy the agent |  
   | Any role that includes the action *Microsoft.Resources/deployments/** | <ul><li>Subscription and/or</li><li>Resource group and/or </li></ul> | To deploy Azure Resource Manager templates |  
- **Non-Azure**: To install the agent on physical servers and virtual machines hosted *outside* of Azure (that is, on-premises) or in other clouds, you must [install the Azure Arc Connected Machine agent](../../azure-arc/servers/agent-overview.md) first, at no added cost.
- **Authentication**: [Managed identity](../../active-directory/managed-identities-azure-resources/overview.md) must be enabled on Azure virtual machines. Both user-assigned and system-assigned managed identities are supported.
  - **User-assigned**: This managed identity is recommended for large-scale deployments, configurable via [built-in Azure policies](#use-azure-policy). You can create a user-assigned managed identity once and share it across multiple VMs, which means it's more scalable than a system-assigned managed identity. If you use a user-assigned managed identity, you must pass the managed identity details to the Azure Monitor agent via extension settings:
  
    ```json
    {
      "authentication": {
        "managedIdentity": {
          "identifier-name": "mi_res_id" or "object_id" or "client_id",
          "identifier-value": "<resource-id-of-uai>" or "<guid-object-or-client-id>"
        }
      }
    }
    ```
    We recommend that you use `mi_res_id` as the `identifier-name`. The following sample commands only show usage with `mi_res_id` for the sake of brevity. For more information on `mi_res_id`, `object_id`, and `client_id`, see the [Managed identity documentation](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http).
  - **System-assigned**: This managed identity is suited for initial testing or small deployments. When used at scale, for example, for all VMs in a subscription, it results in a substantial number of identities created (and deleted) in Azure Active Directory. To avoid this churn of identities, use user-assigned managed identities instead. *For Azure Arc-enabled servers, system-assigned managed identity is enabled automatically* as soon as you install the Azure Arc agent. It's the only supported type for Azure Arc-enabled servers.
  - **Not required for Azure Arc-enabled servers**: The system identity is enabled automatically if the agent is installed via [creating and assigning a data collection rule by using the Azure portal](data-collection-rule-azure-monitor-agent.md#create-data-collection-rule-and-association).
- **Networking**: If you use network firewalls, the [AzureResourceManager service tag](../../virtual-network/service-tags-overview.md) must be enabled on the virtual network for the virtual machine. The virtual machine must also have access to the following HTTPS endpoints:

  -	global.handler.control.monitor.azure.com
  -	`<virtual-machine-region-name>`.handler.control.monitor.azure.com (example: westus.handler.control.azure.com)
  -	`<log-analytics-workspace-id>`.ods.opinsights.azure.com (example: 12345a01-b1cd-1234-e1f2-1234567g8h99.ods.opsinsights.azure.com)  
    (If you use private links on the agent, you must also add the [dce endpoints](../essentials/data-collection-endpoint-overview.md#components-of-a-data-collection-endpoint)).

> [!NOTE]
> This article only pertains to agent installation or management. After you install the agent, you must review the next article to [configure data collection rules and associate them with the machines](./data-collection-rule-azure-monitor-agent.md) with agents installed. *The Azure Monitor agents can't function without being associated with data collection rules.*

## Use the Azure portal

Follow these instructions to use the Azure portal.

### Install

To install the Azure Monitor agent by using the Azure portal, follow the process to [create a data collection rule](data-collection-rule-azure-monitor-agent.md#create-data-collection-rule-and-association) in the Azure portal. This process creates the rule, associates it to the selected resources, and installs the Azure Monitor agent on them if it's not already installed.

### Uninstall

To uninstall the Azure Monitor agent by using the Azure portal, go to your virtual machine, scale set, or Azure Arc-enabled server. Select the **Extensions** tab and select **AzureMonitorWindowsAgent** or **AzureMonitorLinuxAgent**. In the dialog that opens, select **Uninstall**.

### Update

To perform a one-time update of the agent, you must first uninstall the existing agent version. Then install the new version as described.

We recommend that you enable automatic update of the agent by enabling the [Automatic Extension Upgrade](../../virtual-machines/automatic-extension-upgrade.md) feature. Go to your virtual machine or scale set, select the **Extensions** tab and select **AzureMonitorWindowsAgent** or **AzureMonitorLinuxAgent**. In the dialog that opens, select **Enable automatic upgrade**.

## Use Resource Manager templates

Follow these instructions to use Azure Resource Manager templates.

### Install

You can use Resource Manager templates to install the Azure Monitor agent on Azure virtual machines and on Azure Arc-enabled servers and to create an association with data collection rules. You must create any data collection rule prior to creating the association.

Get sample templates for installing the agent and creating the association from the following resources:

- [Template to install Azure Monitor agent (Azure and Azure Arc)](../agents/resource-manager-agent.md#azure-monitor-agent)
- [Template to create association with data collection rule](./resource-manager-data-collection-rules.md)

Install the templates by using [any deployment method for Resource Manager templates](../../azure-resource-manager/templates/deploy-powershell.md), such as the following commands.

# [PowerShell](#tab/ARMAgentPowerShell)

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```

# [CLI](#tab/ARMAgentCLI)

```azurecli
az deployment group create --resource-group "<resource-group-name>" --template-file "<path-to-template>" --parameters "@<parameter-filename.json>"
```
---

## Use PowerShell

You can install the Azure Monitor agent on Azure virtual machines and on Azure Arc-enabled servers by using the PowerShell command for adding a virtual machine extension.

### Install on Azure virtual machines

Use the following PowerShell commands to install the Azure Monitor agent on Azure virtual machines. Choose the appropriate command based on your chosen authentication method.

#### User-assigned managed identity

# [Windows](#tab/PowerShellWindows)

```powershell
Set-AzVMExtension -Name AzureMonitorWindowsAgent -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion <version-number> -EnableAutomaticUpgrade $true -SettingString '{"authentication":{"managedIdentity":{"identifier-name":"mi_res_id","identifier-value":/subscriptions/<my-subscription-id>/resourceGroups/<my-resource-group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<my-user-assigned-identity>"}}}'
```
# [Linux](#tab/PowerShellLinux)

```powershell
Set-AzVMExtension -Name AzureMonitorLinuxAgent -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion <version-number> -EnableAutomaticUpgrade $true -SettingString '{"authentication":{"managedIdentity":{"identifier-name":"mi_res_id","identifier-value":/subscriptions/<my-subscription-id>/resourceGroups/<my-resource-group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<my-user-assigned-identity>"}}}'
```
---

#### System-assigned managed identity

# [Windows](#tab/PowerShellWindows)

```powershell
Set-AzVMExtension -Name AzureMonitorWindowsAgent -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion <version-number> -EnableAutomaticUpgrade $true
```
# [Linux](#tab/PowerShellLinux)

```powershell
Set-AzVMExtension -Name AzureMonitorLinuxAgent -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion <version-number> -EnableAutomaticUpgrade $true
```
---

### Uninstall on Azure virtual machines

Use the following PowerShell commands to uninstall the Azure Monitor agent on Azure virtual machines.

# [Windows](#tab/PowerShellWindows)

```powershell
Remove-AzVMExtension -Name AzureMonitorWindowsAgent -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> 
```
# [Linux](#tab/PowerShellLinux)

```powershell
Remove-AzVMExtension -Name AzureMonitorLinuxAgent -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> 
```
---

### Update on Azure virtual machines

To perform a one-time update of the agent, you must first uninstall the existing agent version, then install the new version as described.

We recommend that you enable automatic update of the agent by enabling the [Automatic Extension Upgrade](../../virtual-machines/automatic-extension-upgrade.md) feature by using the following PowerShell commands.

# [Windows](#tab/PowerShellWindows)

```powershell
Set-AzVMExtension -ExtensionName AzureMonitorWindowsAgent -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Publisher Microsoft.Azure.Monitor -ExtensionType AzureMonitorWindowsAgent -TypeHandlerVersion <version-number> -Location <location> -EnableAutomaticUpgrade $true
```

# [Linux](#tab/PowerShellLinux)

```powershell
Set-AzVMExtension -ExtensionName AzureMonitorLinuxAgent -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Publisher Microsoft.Azure.Monitor -ExtensionType AzureMonitorLinuxAgent -TypeHandlerVersion <version-number> -Location <location> -EnableAutomaticUpgrade $true
```
--- 
   

### Install on Azure Arc-enabled servers

Use the following PowerShell commands to install the Azure Monitor agent on Azure Arc-enabled servers.

# [Windows](#tab/PowerShellWindowsArc)

```powershell
New-AzConnectedMachineExtension -Name AzureMonitorWindowsAgent -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location> -EnableAutomaticUpgrade
```
# [Linux](#tab/PowerShellLinuxArc)

```powershell
New-AzConnectedMachineExtension -Name AzureMonitorLinuxAgent -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location> -EnableAutomaticUpgrade
```
---

### Uninstall on Azure Arc-enabled servers

Use the following PowerShell commands to uninstall the Azure Monitor agent on Azure Arc-enabled servers.

# [Windows](#tab/PowerShellWindowsArc)

```powershell
Remove-AzConnectedMachineExtension -MachineName <arc-server-name> -ResourceGroupName <resource-group-name> -Name AzureMonitorWindowsAgent
```

# [Linux](#tab/PowerShellLinuxArc)

```powershell
Remove-AzConnectedMachineExtension -MachineName <arc-server-name> -ResourceGroupName <resource-group-name> -Name AzureMonitorLinuxAgent
```
---

### Upgrade on Azure Arc-enabled servers

To perform a one-time upgrade of the agent, use the following PowerShell commands.

# [Windows](#tab/PowerShellWindowsArc)

```powershell
$target = @{"Microsoft.Azure.Monitor.AzureMonitorWindowsAgent" = @{"targetVersion"=<target-version-number>}}
Update-AzConnectedExtension -ResourceGroupName $env.ResourceGroupName -MachineName <arc-server-name> -ExtensionTarget $target
```

# [Linux](#tab/PowerShellLinuxArc)

```powershell
$target = @{"Microsoft.Azure.Monitor.AzureMonitorLinuxAgent" = @{"targetVersion"=<target-version-number>}}
Update-AzConnectedExtension -ResourceGroupName $env.ResourceGroupName -MachineName <arc-server-name> -ExtensionTarget $target
```
---

We recommend that you enable automatic update of the agent by enabling the [Automatic Extension Upgrade](../../azure-arc/servers/manage-automatic-vm-extension-upgrade.md#manage-automatic-extension-upgrade) feature by using the following PowerShell commands.

# [Windows](#tab/PowerShellWindowsArc)

```powershell
Update-AzConnectedMachineExtension -ResourceGroup <resource-group-name> -MachineName <arc-server-name> -Name AzureMonitorWindowsAgent -EnableAutomaticUpgrade
```

# [Linux](#tab/PowerShellLinuxArc)

```powershell
Update-AzConnectedMachineExtension -ResourceGroup <resource-group-name> -MachineName <arc-server-name> -Name AzureMonitorLinuxAgent -EnableAutomaticUpgrade
```
---

## Use the Azure CLI

You can install the Azure Monitor agent on Azure virtual machines and on Azure Arc-enabled servers by using the Azure CLI command for adding a virtual machine extension.

### Install on Azure virtual machines

Use the following CLI commands to install the Azure Monitor agent on Azure virtual machines. Choose the appropriate command based on your chosen authentication method.

#### User-assigned managed identity

# [Windows](#tab/CLIWindows)

```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id> --enable-auto-upgrade true --settings '{"authentication":{"managedIdentity":{"identifier-name":"mi_res_id","identifier-value":"/subscriptions/<my-subscription-id>/resourceGroups/<my-resource-group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<my-user-assigned-identity>"}}}'
```

# [Linux](#tab/CLILinux)

```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id> --enable-auto-upgrade true --settings '{"authentication":{"managedIdentity":{"identifier-name":"mi_res_id","identifier-value":"/subscriptions/<my-subscription-id>/resourceGroups/<my-resource-group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<my-user-assigned-identity>"}}}'
```
---

#### System-assigned managed identity

# [Windows](#tab/CLIWindows)

```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id> --enable-auto-upgrade true
```

# [Linux](#tab/CLILinux)

```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id> --enable-auto-upgrade true
```
---

### Uninstall on Azure virtual machines

Use the following CLI commands to uninstall the Azure Monitor agent on Azure virtual machines.

# [Windows](#tab/CLIWindows)

```azurecli
az vm extension delete --resource-group <resource-group-name> --vm-name <virtual-machine-name> -name AzureMonitorWindowsAgent
```

# [Linux](#tab/CLILinux)


```azurecli
az vm extension delete --resource-group <resource-group-name> --vm-name <virtual-machine-name> -name AzureMonitorLinuxAgent
```
---

### Update on Azure virtual machines

To perform a one-time update of the agent, you must first uninstall the existing agent version, then install the new version as described.
  
We recommend that you enable automatic update of the agent by enabling the [Automatic Extension Upgrade](../../virtual-machines/automatic-extension-upgrade.md) feature by using the following CLI commands.

# [Windows](#tab/CLIWindows)

```azurecli
az vm extension set -name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --vm-name <virtual-machine-name> --resource-group <resource-group-name> --enable-auto-upgrade true
```
# [Linux](#tab/CLILinux)

```azurecli
az vm extension set -name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --vm-name <virtual-machine-name> --resource-group <resource-group-name> --enable-auto-upgrade true
```
---

### Install on Azure Arc-enabled servers

Use the following CLI commands to install the Azure Monitor agent on Azure Arc-enabled servers.

# [Windows](#tab/CLIWindowsArc)

```azurecli
az connectedmachine extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --type AzureMonitorWindowsAgent --machine-name <arc-server-name> --resource-group <resource-group-name> --location <arc-server-location> --enable-auto-upgrade true
```

# [Linux](#tab/CLILinuxArc)

```azurecli
az connectedmachine extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --type AzureMonitorLinuxAgent --machine-name <arc-server-name> --resource-group <resource-group-name> --location <arc-server-location> --enable-auto-upgrade true
```
---

### Uninstall on Azure Arc-enabled servers

Use the following CLI commands to uninstall the Azure Monitor agent on Azure Arc-enabled servers.

# [Windows](#tab/CLIWindowsArc)

```azurecli
az connectedmachine extension delete --name AzureMonitorWindowsAgent --machine-name <arc-server-name> --resource-group <resource-group-name>
```
# [Linux](#tab/CLILinuxArc)

```azurecli
az connectedmachine extension delete --name AzureMonitorLinuxAgent --machine-name <arc-server-name> --resource-group <resource-group-name>
```
---

### Upgrade on Azure Arc-enabled servers

To perform a one-time upgrade of the agent, use the following CLI commands.

# [Windows](#tab/CLIWindowsArc)

```azurecli
az connectedmachine upgrade-extension --extension-targets "{\"Microsoft.Azure.Monitor.AzureMonitorWindowsAgent\":{\"targetVersion\":\"<target-version-number>\"}}" --machine-name <arc-server-name> --resource-group <resource-group-name>
```

# [Linux](#tab/CLILinuxArc)

```azurecli
az connectedmachine upgrade-extension --extension-targets "{\"Microsoft.Azure.Monitor.AzureMonitorWindowsAgent\":{\"targetVersion\":\"<target-version-number>\"}}" --machine-name <arc-server-name> --resource-group <resource-group-name>
```
---

 We recommend that you enable automatic update of the agent by enabling the [Automatic Extension Upgrade](../../azure-arc/servers/manage-automatic-vm-extension-upgrade.md#manage-automatic-extension-upgrade) feature by using the following PowerShell commands.

# [Windows](#tab/CLIWindowsArc)

```azurecli
az connectedmachine extension update --name AzureMonitorWindowsAgent --machine-name <arc-server-name> --resource-group <resource-group-name> --enable-auto-upgrade true
```

# [Linux](#tab/CLILinuxArc)

```azurecli
az connectedmachine extension update --name AzureMonitorLinuxAgent --machine-name <arc-server-name> --resource-group <resource-group-name> --enable-auto-upgrade true
```
---

## Use Azure Policy

Use the following policies and policy initiatives to automatically install the agent and associate it with a data collection rule every time you create a virtual machine, scale set, or Azure Arc-enabled server.

> [!NOTE]
> As per Microsoft Identity best practices, policies for installing the Azure Monitor agent on virtual machines and scale sets rely on user-assigned managed identity. This option is the more scalable and resilient managed identity for these resources.
> For Azure Arc-enabled servers, policies rely on system-assigned managed identity as the only supported option today.

### Built-in policy initiatives

Before you proceed, review [prerequisites for agent installation](azure-monitor-agent-manage.md#prerequisites).

Policy initiatives for Windows and Linux virtual machines, scale sets consist of individual policies that:

- (Optional) Create and assign built-in user-assigned managed identity, per subscription, per region. [Learn more](../../active-directory/managed-identities-azure-resources/how-to-assign-managed-identity-via-azure-policy.md#policy-definition-and-details).
   - `Bring Your Own User-Assigned Identity`: If set to `true`, it creates the built-in user-assigned managed identity in the predefined resource group and assigns it to all machines that the policy is applied to. If set to `false`, you can instead use existing user-assigned identity that *you must assign* to the machines beforehand.
- Install the Azure Monitor agent extension on the machine, and configure it to use user-assigned identity as specified by the following parameters.
  - `Bring Your Own User-Assigned Managed Identity`: If set to `false`, it configures the agent to use the built-in user-assigned managed identity created by the preceding policy. If set to `true`, it configures the agent to use an existing user-assigned identity that *you must assign* to the machines in scope beforehand.
  - `User-Assigned Managed Identity Name`: If you use your own identity (selected `true`), specify the name of the identity that's assigned to the machines.
  - `User-Assigned Managed Identity Resource Group`: If you use your own identity (selected `true`), specify the resource group where the identity exists.
  - `Additional Virtual Machine Images`: Pass additional VM image names that you want to apply the policy to, if not already included.
- Create and deploy the association to link the machine to specified data collection rule.
   - `Data Collection Rule Resource Id`: The Azure Resource Manager resourceId of the rule you want to associate via this policy to all machines the policy is applied to.

   ![Partial screenshot from the Azure Policy Definitions page that shows two built-in policy initiatives for configuring the Azure Monitor agent.](media/azure-monitor-agent-install/built-in-ama-dcr-initiatives.png)

#### Known issues

- Managed Identity default behavior. [Learn more](../../active-directory/managed-identities-azure-resources/managed-identities-faq.md#what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request).
- Possible rare condition with using built-in user-assigned identity creation policy. [Learn more](../../active-directory/managed-identities-azure-resources/how-to-assign-managed-identity-via-azure-policy.md#known-issues).
- Assigning policy to resource groups. If the assignment scope of the policy is a resource group and not a subscription, the identity used by policy assignment (different from the user-assigned identity used by agent) must be manually granted [these roles](../../active-directory/managed-identities-azure-resources/how-to-assign-managed-identity-via-azure-policy.md#required-authorization) prior to assignment/remediation. Failing to do this step will result in *deployment failures*.
- Other [Managed Identity limitations](../../active-directory/managed-identities-azure-resources/managed-identities-faq.md#limitations).

### Built-in policies

You can choose to use the individual policies from the preceding policy initiative to perform a single action at scale. For example, if you *only* want to automatically install the agent, use the second agent installation policy from the initiative, as shown.

![Partial screenshot from the Azure Policy Definitions page that shows policies contained within the initiative for configuring the Azure Monitor agent.](media/azure-monitor-agent-install/built-in-ama-dcr-policy.png)

### Remediation

The initiatives or policies will apply to each virtual machine as it's created. A [remediation task](../../governance/policy/how-to/remediate-resources.md) deploys the policy definitions in the initiative to existing resources, so you can configure the Azure Monitor agent for any resources that were already created.

When you create the assignment by using the Azure portal, you have the option of creating a remediation task at the same time. For information on the remediation, see [Remediate non-compliant resources with Azure Policy](../../governance/policy/how-to/remediate-resources.md).

![Screenshot that shows initiative remediation for the Azure Monitor agent.](media/azure-monitor-agent-install/built-in-ama-dcr-remediation.png)

## Next steps

[Create a data collection rule](data-collection-rule-azure-monitor-agent.md) to collect data from the agent and send it to Azure Monitor.
