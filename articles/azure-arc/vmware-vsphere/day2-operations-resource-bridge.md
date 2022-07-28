---
title:  Perform ongoing administration for Arc-enabled VMware vSphere
description: Learn how to perform day 2 administrator operations related to Azure Arc-enabled VMware vSphere
ms.topic: how-to 
ms.date: 03/28/2022

---

# Perform ongoing administration for Arc-enabled VMware vSphere

In this article, you'll learn how to perform various administrative operations related to Azure Arc-enabled VMware vSphere (preview):

- Upgrading the Arc resource bridge
- Updating the credentials
- Collecting logs from the Arc resource bridge

Each of these operations requires either SSH key to the resource bridge VM or the kubeconfig that provides access to the Kubernetes cluster on the resource bridge VM.

## Upgrading the Arc resource bridge

Azure Arc-enabled VMware vSphere requires the Arc resource bridge to connect your VMware vSphere environment with Azure. Periodically, new images of Arc resource bridge will be released to include security and feature updates.

> [!NOTE]
> To upgrade the arc resource bridge VM to the latest version, you will need to perform the onboarding again with the **same resource IDs**. This will cause some downtime as operations performed through Arc during this time might fail.

To upgrade to the latest version of the resource bridge, perform the following steps:

1. Copy the Azure region and resource IDs of the Arc resource bridge, custom location and vCenter Azure resources

2. Find and delete the old Arc resource bridge **template** from your vCenter

3. Download the script from the portal and update the following section in the script

    ```powershell
    $location = <Azure region of the resources>
    
    $applianceSubscriptionId = <subscription-id>
    $applianceResourceGroupName = <resourcegroup-name>
    $applianceName = <resource-bridge-name>
    
    $customLocationSubscriptionId = <subscription-id>
    $customLocationResourceGroupName = <resourcegroup-name>
    $customLocationName = <custom-location-name>
    
    $vCenterSubscriptionId = <subscription-id>
    $vCenterResourceGroupName = <resourcegroup-name>
    $vCenterName = <vcenter-name-in-azure>
    ```

4. [Run the onboarding script](quick-start-connect-vcenter-to-arc-using-script.md#run-the-script) again with the `--force` parameter

    ``` powershell-interactive
    ./resource-bridge-onboarding-script.ps1 --force
    ```

5. [Provide the inputs](quick-start-connect-vcenter-to-arc-using-script.md#inputs-for-the-script) as prompted.

6. Once the onboarding is successfully completed, the resource bridge is upgraded to the latest version.

## Updating the vSphere account credentials

Azure Arc-enabled VMware vSphere uses the vSphere account credentials you provided during the onboarding to communicate with your vCenter server. These credentials are only persisted locally on the Arc resource bridge VM.

As part of your security practices, you might need to rotate credentials for your vCenter accounts. As credentials are rotated, you must also update the credentials provided to Azure Arc to ensure the functioning of Azure Arc-enabled VMware services.

There are two different sets of credentials stored on the Arc resource bridge. But you can use the same account credentials for both.

- **Account for Arc resource bridge**. This account is used for deploying the Arc resource bridge VM and will be used for upgrade.
- **Account for VMware cluster extension**. This account is used to discover inventory and perform all VM operations through Azure Arc-enabled VMware vSphere

To update the credentials of the account for Arc resource bridge, run the following command from a workstation that can access cluster configuration IP address of the Arc resource bridge locally:

```azurecli
az arcappliance setcredential vmware --kubeconfig <kubeconfig>
```

To update the credentials used by the VMware cluster extension on the resource bridge. This command can be run from anywhere with `connectedvmware` CLI extension installed.

```azurecli
az connectedvmware vcenter connect --custom-location <name of the custom location> --location <Azure region>  --name <name of the vCenter resource in Azure>       --resource-group <resource group for the vCenter resource>  --username   <username for the vSphere account>  --password  <password to the vSphere account>
```

## Collecting logs from the Arc resource bridge

For any issues encountered with the Azure Arc resource bridge, you can collect logs for further investigation. To collect the logs, use the Azure CLI [`Az arcappliance log`](/cli/azure/arcappliance/logs#az-arcappliance-logs-vmware) command.

The `az arcappliance log` command must be run from a workstation that can communicate with the Arc resource bridge either via the cluster configuration IP address or the IP address of the Arc resource bridge VM.

To save the logs to a destination folder, run the following command. This command requires connectivity to cluster configuration IP address.

```azurecli
az arcappliance logs <provider> --kubeconfig <path to kubeconfig> --out-dir <path to specified output directory>
```

If the Kubernetes cluster on the resource bridge isn't in functional state, you can use the following command. This command requires connectivity to IP address of the Azure Arc resource bridge VM via SSH

```azurecli
az arcappliance logs <provider> --out-dir <path to specified output directory> --ip XXX.XXX.XXX.XXX
```

During initial onboarding, SSH keys are saved to the workstation. If you're running this command from the workstation that was used for onboarding, no other steps are required.

If you're running this command from a different workstation,  you must make sure the following files are copied to the new workstation in the same location.

- For a Windows workstation, `C:\ProgramData\kva\.ssh\logkey` and `C:\ProgramData\kva\.ssh\logkey.pub`
  
- For a Linux workstation, `$HOME\.KVA\.ssh\logkey` and `$HOME\.KVA\.ssh\logkey.pub`

## Next steps

[Troubleshoot common issues related to resource bridge](../resource-bridge/troubleshoot-resource-bridge.md)