---
title: Release Notes for Azure Application Consistent Snapshot tool for Azure NetApp Files | Microsoft Docs
description: Provides release notes for the Azure Application Consistent Snapshot tool that you can use with Azure NetApp Files. 
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''

ms.assetid:
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: phjensen
---

# Release Notes for Azure Application Consistent Snapshot tool

This page lists major changes made to AzAcSnap to provide new functionality or resolve defects.

## Aug-2021

### AzAcSnap v5.0.2 (Build_20210827.19086) - Patch update to v5.0.1

AzAcSnap v5.0.2 (Build_20210827.19086) is provided as a patch update to the v5.0 branch with the following fixes and improvements:

- Ignore `ssh` 255 exit codes.  In some cases the `ssh` command, which is used to communicate with storage on Azure Large Instance, would emit an exit code of 255 when there were no errors or execution failures  (refer `man ssh` "EXIT STATUS") - subsequently AzAcSnap would trap this as a failure and abort.  With this update additional verification is done to validate correct execution, this includes parsing `ssh` STDOUT and STDERR for errors in addition to traditional Exit Code checks.
- Fix installer hdbuserstore source path check.  The installer would check for the existence of an incorrect source directory for the hdbuserstore for the user running the install - this is fixed to check for `~/.hdb`.  This is applicable to systems (e.g. Azure Large Instance) where the hdbuserstore was pre-configured for the `root` user before installing `azacsnap`.
- Installer now shows the version it will install/extract (if the installer is run without any arguments).

Download the [latest release](https://aka.ms/azacsnapinstaller) of the installer and review how to [get started](azacsnap-get-started.md).

## May-2021

### AzAcSnap v5.0.1 (Build: 20210524.14837) - Patch update to v5.0

AzAcSnap v5.0.1 (Build: 20210524.14837) is provided as a patch update to the v5.0 branch with the following fixes and improvements:

- Improved exit code handling.  In some cases an exit code of 0 (zero) was emitted even when there was an execution failure where it should have been non-zero.  Exit codes should now only be zero on successfully running `azacsnap` to completion and non-zero in case of any failure.  Additionally, AzAcSnap's internal error handling has been extended to capture and emit the exit code of the external commands (e.g. hdbsql, ssh) run by AzAcSnap, if they are the cause of the failure.

## April-2021

### AzAcSnap v5.0 (Build: 20210421.6349) - GA Released (21-April-2021)

AzAcSnap v5.0 (Build: 20210421.6349) has been made Generally Available and for this build had the following fixes and improvements:

- The hdbsql retry timeout (to wait for a response from SAP HANA) is automatically set to half of the "savePointAbortWaitSeconds" to avoid race conditions.  The setting for "savePointAbortWaitSeconds" can be modified directly in the JSON configuration file and must be a minimum of 600 seconds.

## March-2021

### AzAcSnap v5.0 Preview (Build:20210318.30771)

AzAcSnap v5.0 Preview (Build:20210318.30771) has been released with the following fixes and improvements:

- Removed the need to add the AZACSNAP user into the SAP HANA Tenant DBs, see the [Enable communication with SAP HANA](azacsnap-installation.md#enable-communication-with-sap-hana) section.
- Fix to allow a [restore](azacsnap-cmd-ref-restore.md) with volumes configured with Manual QOS.
- Added mutex control to throttle SSH connections for Azure Large Instance.
- Fix installer for handling path names with spaces and other related issues.
- In preparation for supporting other database servers, changed the optional parameter '--hanasid' to '--dbsid'.

## Next steps

- [Get started with Azure Application Consistent Snapshot tool](azacsnap-get-started.md)
