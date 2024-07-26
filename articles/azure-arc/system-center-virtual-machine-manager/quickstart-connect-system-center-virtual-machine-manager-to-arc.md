---
title: Quickstart for Azure Arc-enabled System Center Virtual Machine Manager (SCVMM)
description: In this Quickstart, you learn how to use the helper script to connect your System Center Virtual Machine Manager management server to Azure Arc.
author: PriskeyJeronika-MS
ms.author: v-gjeronika
manager: jsuri
ms.topic: quickstart
ms.services: azure-arc
ms.subservice: azure-arc-scvmm
ms.date: 07/11/2024
ms.custom: references_regions

# Customer intent: As a VI admin, I want to connect my VMM management server to Azure Arc.
---

# Quickstart: Connect your System Center Virtual Machine Manager management server to Azure Arc

Before you can start using the Azure Arc-enabled SCVMM features, you need to connect your VMM management server to Azure Arc.

This Quickstart shows you how to connect your SCVMM management server to Azure Arc using a helper script. The script deploys a lightweight Azure Arc appliance (called Azure Arc resource bridge) as a virtual machine running in your VMM environment and installs an SCVMM cluster extension on it to provide a continuous connection between your VMM management server and Azure Arc.

## Prerequisites

>[!Note]
> - If VMM server is running on Windows Server 2016 machine, ensure that [Open SSH package](https://github.com/PowerShell/Win32-OpenSSH/releases) and tar are installed. To install tar, you can copy tar.exe and archiveint.dll from any Windows 11 or Windows Server 2019/2022 machine to *C:\Windows\System32* path on your VMM server machine.
> - If you deploy an older version of appliance (version lesser than 0.2.25), Arc operation fails with the error *Appliance cluster is not deployed with Microsoft Entra ID authentication*. To fix this issue, download the latest version of the onboarding script and deploy the resource bridge again.
> - Azure Arc Resource Bridge deployment using private link is currently not supported.

| **Requirement** | **Details** |
| --- | --- |
| **Azure** | An Azure subscription  <br/><br/> A resource group in the above subscription where you have the *Owner/Contributor* role. |
| **SCVMM** | You need an SCVMM management server running version 2019 or later.<br/><br/> A private cloud or a host group with a minimum free capacity of 32 GB of RAM, 4 vCPUs with 100 GB of free disk space. <br/><br/> A VM network with internet access, directly or through proxy. Appliance VM will be deployed using this VM network.<br/><br/> Only Static IP allocation is supported and VMM Static IP Pool is required. Follow [these steps](/system-center/vmm/network-pool?view=sc-vmm-2022&preserve-view=true) to create a VMM Static IP Pool and ensure that the Static IP Pool has at least four IP addresses. If your SCVMM server is behind a firewall, all IPs in this IP Pool and the Control Plane IP should be allowed to communicate through WinRM ports. The default WinRM ports are 5985 and 5986. <br/><br/> Dynamic IP allocation using DHCP isn't supported. <br/><br/> A library share with write permission for the SCVMM admin account through which Resource Bridge deployment is going to be performed.  |
| **SCVMM accounts** | An SCVMM admin account that can perform all administrative actions on all objects that VMM manages. <br/><br/> The user should be part of local administrator account in the SCVMM server. If the SCVMM server is installed in a High Availability configuration, the user should be a part of the local administrator accounts in all the SCVMM cluster nodes. <br/><br/>This will be used for the ongoing operation of Azure Arc-enabled SCVMM and the deployment of the Arc Resource bridge VM. |
| **Workstation** | The workstation will be used to run the helper script. Ensure you have [64-bit Azure CLI installed](/cli/azure/install-azure-cli) on the workstation.<br/><br/> A Windows/Linux machine that can access both your SCVMM management server and internet, directly or through proxy.<br/><br/> The helper script can be run directly from the VMM server machine as well.<br/><br/> To avoid network latency issues, we recommend executing the helper script directly in the VMM server machine.<br/><br/> Note that when you execute the script from a Linux machine, the deployment takes a bit longer and you might experience performance issues. |

## Prepare SCVMM management server

-	Create an SCVMM private cloud if you don't have one. The private cloud should have a reservation of at least 32 GB of RAM and 4 vCPUs. It should also have at least 100 GB of disk space.
-	Ensure that SCVMM administrator account has the appropriate permissions.

## Download the onboarding script

1. Go to [Azure portal](https://aka.ms/SCVMM/MgmtServers).
1. Search and select **Azure Arc**.
1. In the **Overview** page, select **Add resources** under **Manage resources across environments**.

    :::image type="content" source="media/quick-start-connect-scvmm-to-azure/overview-add-infrastructure.png" alt-text="Screenshot of how to select Add your infrastructure for free." lightbox="media/quick-start-connect-scvmm-to-azure/overview-add-infrastructure.png":::

1. In the **Host environments** section, in **System Center VMM** select **Add**.

    :::image type="content" source="media/quick-start-connect-scvmm-to-azure/platform-add-system-center-vmm.png" alt-text="Screenshot of how to select System Center V M M platform." lightbox="media/quick-start-connect-scvmm-to-azure/platform-add-system-center-vmm.png":::

1. Select **Create a new resource bridge** and select **Next : Basics >**.
1. Provide a name for **Azure Arc resource bridge**. For example: *contoso-nyc-resourcebridge*.
1. Select a subscription and resource group where you want to create the resource bridge.
1. Under **Region**, select an Azure location where you want to store the resource metadata. The currently supported regions are **East US** and **West Europe**.
1. Provide a name for **Custom location**.
   This is the name that you'll see when you deploy virtual machines. Name it for the datacenter or the physical location of your datacenter. For example: *contoso-nyc-dc.*

1. Leave the option **Use the same subscription and resource group as your resource bridge** selected.
1. Provide a name for your **SCVMM management server instance** in Azure. For example: *contoso-nyc-scvmm.*
1. Select **Next: Tags >**.
1. Assign Azure tags to your resources in **Value** under **Physical location tags**. You can add additional tags to help you organize your resources to facilitate administrative tasks using custom tags.
1. Select **Next: Download and run script >**.
1. If your subscription isn't registered with all the required resource providers, select **Register** to proceed to next step.
1. Based on the operating system of your workstation, download the PowerShell or Bash script and copy it to the workstation.
1. To see the status of your onboarding after you run the script on your workstation, select **Next:Verification**. The onboarding isn't affected when you close this page.

### Windows

Follow these instructions to run the script on a Windows machine.

1. Open a new PowerShell window as Administrator and verify if Azure CLI is successfully installed in the workstation, and use the following command:
    ```azurepowershell-interactive
    az
    ```
1. Navigate to the folder where you've downloaded the PowerShell script:
   *cd C:\Users\ContosoUser\Downloads*

1. Run the following command to allow the script to run since it's an unsigned script (if you close the session before you complete all the steps, run this command again in the new PowerShell Administrator session):
    ```azurepowershell-interactive
    Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
    ```
1. Run the script:
    ```azurepowershell-interactive
    ./resource-bridge-onboarding-script.ps1
    ```
### Linux

Follow these instructions to run the script on a Linux machine:

1. Open the terminal and navigate to the folder where you've downloaded the Bash script.
2. Execute the script using the following command:

    ```sh
    bash resource-bridge-onboarding-script.sh
    ```

## Script runtime
The script execution will take up to half an hour and you'll be prompted for various details. See the following table for related information:

| **Parameter** | **Details** |
| --- | --- |
| **Azure login** | You would be asked to sign in to Azure by visiting [this site](https://www.microsoft.com/devicelogin) and pasting the prompted code. |
| **SCVMM management server FQDN/Address** | FQDN for the VMM server (or an IP address). </br> Provide role name if it’s a Highly Available VMM deployment. </br> For example: nyc-scvmm.contoso.com or 10.160.0.1 |
| **SCVMM Username**</br> (domain\username) | Username for the SCVMM administrator account. The required permissions for the account are listed in the prerequisites above.</br> Example: contoso\contosouser |
| **SCVMM password** | Password for the SCVMM admin account |
| **Deployment location selection** | Select if you want to deploy the Arc resource bridge VM in an SCVMM Cloud or an SCVMM Host Group. |
| **Private cloud/Host group selection** | Select the name of the private cloud or the host group where the Arc resource bridge VM should be deployed. |
| **Virtual Network selection** | Select the name of the virtual network to which *Arc resource bridge VM* needs to be connected. This network should allow the appliance to talk to the VMM management server and the Azure endpoints (or internet). |
| **Static IP pool** | Select the VMM static IP pool that will be used to allot the IP address. |
| **Control Plane IP** | Provide a reserved IP address in the same subnet as the static IP pool used for Resource Bridge deployment. This IP address should be outside of the range of static IP pool used for Resource Bridge deployment and shouldn't be assigned to any other machine on the network. |
| **Appliance proxy settings** | Enter *Y* if there's a proxy in your appliance network, else enter *N*.|
| **http** | Address of the HTTP proxy server. |
| **https** | Address of the HTTPS proxy server.|
| **NoProxy** | Addresses to be excluded from proxy.|
|**CertificateFilePath** | For SSL based proxies, provide the path to the certificate. |

Once the command execution is completed, your setup is complete, and you can try out the capabilities of Azure Arc-enabled SCVMM.

>[!IMPORTANT]
>After the successful installation of Azure Arc Resource Bridge, it's recommended to retain a copy of the resource bridge config (.yaml) files in a secure place that facilitates easy retrieval. These files are needed later to run commands to perform management operations (e.g. [az arcappliance upgrade](/cli/azure/arcappliance/upgrade#az-arcappliance-upgrade-vmware)) on the resource bridge. You can find the three config files (.yaml files) in the same folder where you ran the onboarding script. 


### Retry command - Windows

If for any reason, the appliance creation fails, you need to retry it. Run the command with ```-Force``` to clean up and onboard again.

```powershell-interactive
 ./resource-bridge-onboarding-script.ps1 -Force -Subscription <Subscription> -ResourceGroup <ResourceGroup> -AzLocation <AzLocation> -ApplianceName <ApplianceName> -CustomLocationName <CustomLocationName> -VMMservername <VMMservername>
```

>[!Note]
>You can find the values for *Subscription*, *ResourceGroup*, *Azlocation*, *ApplianceName*, *CustomLocationName*, and *VMMservername* parameters from the onboarding script.

 ### Retry command - Linux

If for any reason, the appliance creation fails, you need to retry it. Run the command with ```--force``` to clean up and onboard again.

  ```sh
    bash resource-bridge-onboarding-script.sh --force
  ```
>[!IMPORTANT]
> After the successful installation of Azure Arc Resource Bridge, it's recommended to retain a copy of the resource bridge config.yaml files in a place that facilitates easy retrieval. These files could be needed later to run commands to perform management operations (e.g. [az arcappliance upgrade](/cli/azure/arcappliance/upgrade#az-arcappliance-upgrade-vmware)) on the resource bridge. You can find the three .yaml files (config files) in the same folder where you ran the script.

>[!NOTE]
> - After successful deployment, we recommend maintaining the state of **Arc Resource Bridge VM** as *online*.
> - Intermittently appliance might become unreachable when you shut down and restart the VM. 
> - After the execution of command, your setup is complete, and you can try out the capabilities of Azure Arc-enabled SCVMM. 

## Next steps

- [Browse and enable SCVMM resources through Azure RBAC](enable-scvmm-inventory-resources.md).
- [Create a VM using Azure Arc-enabled SCVMM](create-virtual-machine.md).
