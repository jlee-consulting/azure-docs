---
title: Perform disaster recovery operations
description: Learn how to perform recovery operations for the Azure Arc resource bridge VM in Azure Arc-enabled VMware vSphere disaster scenarios.
ms.topic: how-to 
ms.custom:
ms.date: 11/06/2023
ms.service: azure-arc
ms.subservice: azure-arc-vmware-vsphere
author: PriskeyJeronika-MS
ms.author: v-gjeronika
manager: jsuri
---

# Recover from accidental deletion of resource bridge VM

In this article, you learn how to recover the Azure Arc resource bridge connection into a working state in disaster scenarios such as accidental deletion. In such cases, the connection between on-premises infrastructure and Azure is lost and any operations performed through Arc fail.

## Prerequisites

1. The disaster recovery script must be run from the same folder where the config (.yaml) files are present. The config files are present on the machine used to run the script to deploy Arc resource bridge. 

1. The machine being used to run the script must have bidirectional connectivity to the Arc resource bridge VM on port 6443 (Kubernetes API server) and 22 (SSH), and outbound connectivity to the Arc resource bridge VM on port 443 (HTTPS).

## Recovering the Arc resource bridge if there is VM deletion

To recover from Arc resource bridge VM deletion, you need to deploy a new resource bridge with the same resource ID as the current resource bridge using the following steps.

1. Copy the Azure region and resource IDs of the Arc resource bridge, custom location, and vCenter Azure resources.

2. Find and delete the old Arc resource bridge template from your vCenter.

3. Download the [onboarding script](../vmware-vsphere/quick-start-connect-vcenter-to-arc-using-script.md#download-the-onboarding-script) from the Azure portal and update the following section in the script, using the same information as the original resources in Azure.

    ```powershell
    $location = <Azure region of the resources>
    $applianceSubscriptionId = <subscription-id>
    $applianceResourceGroupName = <resource-group-name>
    $applianceName = <resource-bridge-name>
    
    $customLocationSubscriptionId = <subscription-id>
    $customLocationResourceGroupName = <resource-group-name>
    $customLocationName = <custom-location-name>
    
    $vCenterSubscriptionId = <subscription-id>
    $vCenterResourceGroupName = <resource-group-name>
    $vCenterName = <vcenter-name-in-azure>
    ```

4. [Run the onboarding script](../vmware-vsphere/quick-start-connect-vcenter-to-arc-using-script.md#run-the-script) again with the `--force` parameter.

    ``` powershell-interactive
    ./resource-bridge-onboarding-script.ps1 --force
    ```

5. [Provide the inputs](../vmware-vsphere/quick-start-connect-vcenter-to-arc-using-script.md#inputs-for-the-script) as prompted.

6. Once the script successfully finishes, the resource bridge should be recovered, and the previously disconnected Arc-enabled resources are manageable in Azure again.

## Next steps

[Troubleshoot Azure Arc resource bridge issues](../resource-bridge/troubleshoot-resource-bridge.md)

If the recovery steps mentioned above are unsuccessful in restoring Arc resource bridge to its original state, try one of the following channels for support:

- Get answers from Azure experts through [Microsoft Q&A](/answers/topics/azure-arc.html).
- Connect with [@AzureSupport](https://twitter.com/azuresupport), the official Microsoft Azure account for improving customer experience. Azure Support connects the Azure community to answers, support, and experts.
- [Open an Azure support request](../../azure-portal/supportability/how-to-create-azure-support-request.md).
