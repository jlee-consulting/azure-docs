---
title: Azure Virtual Desktop Sandbox - Azure
description: How to set up Windows Sandbox for Azure Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 07/29/2022
ms.author: helohr
manager: femila
---

# Set up Windows Sandbox in Azure Virtual Desktop

This topic will walk you through how to publish Windows Sandbox for your users in an Azure Virtual Desktop environment.

## Prerequisites

Before you get started, here's what you need to configureWindows Sandbox in Azure Virtual Desktop:

- A working Azure profile that can access the Azure portal.
- A functioning Azure Virtual Desktop deployment. To learn how to deploy Azure Virtual Desktop (classic), see [Create a tenant in Azure Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). To learn how to deploy Azure Virtual Desktop with Azure Resource Manager integration, see [Create a host pool with the Azure portal](create-host-pools-azure-marketplace.md).
- Azure Virtual Desktop session hosts that supported the nested virtualization capability. To check if a specific VM size supports nested virtualization, navigate to the description page matching your VM size from [Sizes for virtual machines in Azure](../virtual-machines/sizes-general.md).

## Prepare the VHD image for Azure

First, you'll need to create a master VHD image. If you haven't created your master VHD image yet, go to [Prepare and customize a master VHD image](set-up-customize-master-image.md) and follow the instructions there. When you're given the option to select an operating system (OS) for your master image, select either Windows 10 or Windows 11.

When customizing your master image, you'll need to enable the **Containers-DisposableClientVM** feature by running the following command:

```powershell
Enable-WindowsOptionalFeature -FeatureName "Containers-DisposableClientVM" -All -Online
```

>[!NOTE]
>This change will require that you restart the virtual machine.

Once you've uploaded the VHD to Azure, create a host pool that's based on this new image by following the instructions in the [Create a host pool by using the Azure Marketplace](create-host-pools-azure-marketplace.md) tutorial.

## Publish Windows Sandbox on your host pool

### [Azure portal](#tab/azure)

To publish Windows Sandbox to your host pool:

1. Sign in to the Azure portal.

2. In the search bar, enter **Azure Virtual Desktop** and select the matching service entry.

3. Select **Application groups**, then select the name of the application group in the host pool you want to publish Windows Sandbox to.

4. Once you're in the application group, select the **Applications** tab. The Applications grid will display all existing apps within the app group.

5. Select **+ Add** to open the **Add application** tab.

6. For **Application source**, select **File Path**.

7. For **Application path**, enter **C:\windows\system32\WindowsSandbox.exe**.

8. Enter **Windows Sandbox** into the **Application Name** field.

9.  When you're done, select **Save**.

### [PowerShell](#tab/powershell)

To publish Windows Sandbox to your host pool using PowerShell:

1. Connect to Azure using one of the following methods:
  
   - Open a PowerShell prompt on your local device. Run the `Connect-AzAccount` cmdlet to sign in to your Azure account. For more information, see [Sign in with Azure PowerShell](/powershell/azure/authenticate-azureps).
   - Sign in to [the Azure portal](https://portal.azure.com/) and open [Azure Cloud Shell](../cloud-shell/overview.md) with PowerShell as the shell type.

2. Run the following cmdlet to get a list of all the Azure tenants your account has access to:
  
   ```powershell
   Get-AzTenant
   ```

   When you see the tenant you want to sign in to, make a note of its name.

3. Run the following command to store the ID of the Azure tenant you want to connect to, replacing `"Fabrikam"` with your tenant name:

   ```powershell
   $tenantId = (Get-AzTenant | ? Name -eq "Fabrikam").Id
   ```

4. Run the following command to list all subscriptions containing a host pool that are currently available to you:
   
   ```powershell
   Get-AzSubscription -TenantId $tenantId
   ```
   
   Find the name of the subscription that contains a host pool you want to assign a managed identity to in that list. Once you do, make a note of its name and ID.

5. Change your current Azure session to use the subscription you identified in the previous step, replacing the placeholder value `"<subscription name or id>"` with the name or ID of the subscription you want to use:
   
   ```powershell
   Set-AzContext -Tenant $tenantId -Subscription "<subscription name or id>"
   ```

6. Run the following command to create a Sandbox remote app:

   ```powershell
   New-AzWvdApplication -ResourceGroupName <Resource Group Name> -GroupName <Application Group Name> -FilePath 'C:\windows\system32\WindowsSandbox.exe' -IconIndex 0 -IconPath 'C:\windows\system32\WindowsSandbox.exe' -CommandLineSetting 'Allow' -ShowInPortal:$true -SubscriptionId <Workspace Subscription ID>
   ```

   >[!NOTE]
   >After running this command, you'll be given a prompt to name the app. Fill out the prompt to continue.

---

That's it! Leave the rest of the options default. You should now have Windows Sandbox Remote App published for your users.

## Next steps

Learn more about sandboxes and how to use them to test Windows environments at [Windows Sandbox](/windows/security/threat-protection/windows-sandbox/windows-sandbox-overview).