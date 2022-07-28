---
title: Release notes for the Azure File Sync agent | Microsoft Docs
description: Read the release notes for the Azure File Sync agent, which lets you centralize your organization's file shares in Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 6/06/2022
ms.author: wgries
ms.subservice: files
---

# Release notes for the Azure File Sync agent
Azure File Sync allows you to centralize your organization's file shares in Azure Files without giving up the flexibility, performance, and compatibility of an on-premises file server. Your Windows Server installations are transformed into a quick cache of your Azure file share. You can use any protocol that's available on Windows Server to access your data locally (including SMB, NFS, and FTPS). You can have as many caches as you need around the world.

This article provides the release notes for the supported versions of the Azure File Sync agent.

## Supported versions
The following Azure File Sync agent versions are supported:

| Milestone | Agent version number | Release date | Status |
|----|----------------------|--------------|------------------|
| V15 Release - [KB5003882](https://support.microsoft.com/topic/2f93053f-869b-4782-a832-e3c772a64a2d)| 15.0.0.0 | March 30, 2022 | Supported |
| V14.1 Release - [KB5001873](https://support.microsoft.com/topic/d06b8723-c4cf-4c64-b7ec-3f6635e044c5)| 14.1.0.0 | December 1, 2021 | Supported |
| V14 Release - [KB5001872](https://support.microsoft.com/topic/92290aa1-75de-400f-9442-499c44c92a81)| 14.0.0.0 | October 29, 2021 | Supported |
| V13 Release - [KB4588753](https://support.microsoft.com/topic/632fb833-42ed-4e4d-8abd-746bd01c1064)| 13.0.0.0 | July 12, 2021 | Supported - Agent version expires on August 8, 2022 |

## Unsupported versions
The following Azure File Sync agent versions have expired and are no longer supported:

| Milestone | Agent version number | Release date | Status |
|----|----------------------|--------------|------------------|
| V12 Release | 12.0.0.0 - 12.1.0.0 | N/A | Not Supported - Agent versions expired on May 23, 2022 |
| V11 Release | 11.1.0.0 - 11.3.0.0 | N/A | Not Supported - Agent versions expired on March 28, 2022 |
| V10 Release | 10.0.0.0 - 10.1.0.0 | N/A | Not Supported - Agent versions expired on June 28, 2021 |
| V9 Release | 9.0.0.0 - 9.1.0.0 | N/A | Not Supported - Agent versions expired on February 16, 2021 |
| V8 Release | 8.0.0.0 | N/A | Not Supported - Agent versions expired on January 12, 2021 |
| V7 Release | 7.0.0.0 - 7.2.0.0 | N/A | Not Supported - Agent versions expired on September 1, 2020 |
| V6 Release | 6.0.0.0 - 6.3.0.0 | N/A | Not Supported - Agent versions expired on April 21, 2020 |
| V5 Release | 5.0.2.0 - 5.2.0.0 | N/A | Not Supported - Agent versions expired on March 18, 2020 |
| V4 Release | 4.0.1.0 - 4.3.0.0 | N/A | Not Supported - Agent versions expired on November 6, 2019 |
| V3 Release | 3.1.0.0 - 3.4.0.0 | N/A | Not Supported - Agent versions expired on August 19, 2019 |
| Pre-GA agents | 1.1.0.0 - 3.0.13.0 | N/A | Not Supported - Agent versions expired on October 1, 2018 |

### Azure File Sync agent update policy
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## Agent version 15.0.0.0
The following release notes are for version 15.0.0.0 of the Azure File Sync agent (released March 30, 2022).

### Improvements and issues that are fixed
- Reduced transactions when cloud change enumeration job runs 
	- Azure File Sync has a cloud change enumeration job that runs every 24 hours to detect changes made directly in the Azure file share and sync those changes to servers in your sync groups. In the v14 release, we made improvements to reduce the number of transactions when this job runs and in the v15 release we made further improvements. The transaction cost is also more predictable, each job will now produce 1 List transaction per directory, per day.
 
- View Cloud Tiering status for a server endpoint or volume
	- The Get-StorageSyncCloudTieringStatus cmdlet will show cloud tiering status for a specific server endpoint or for a specific volume (depending on path specified). The cmdlet will show current policies, current distribution of tiered vs. fully downloaded data, and last tiering session statistics if the server endpoint path is specified. If the volume path is specified, it will show the effective volume free space policy, the server endpoints located on that volume, and whether these server endpoints have cloud tiering enabled.
 
		To get the cloud tiering status for a server endpoint or volume, run the following PowerShell commands:
 		```powershell
		Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
		Get-StorageSyncCloudTieringStatus -Path <server endpoint path or volume>
 		```
- New diagnostic and troubleshooting tool 
	- The Debug-StorageSyncServer cmdlet will diagnose common issues like certificate misconfiguration and incorrect server time. Also, we have simplified Azure Files Sync troubleshooting by merging the functionality of some of existing scripts and cmdlets (AFSDiag.ps1, FileSyncErrorsReport.ps1, Test-StorageSyncNetworkConnectivity) into the Debug-StorageSyncServer cmdlet.
 
		To run diagnostics on the server, run the following PowerShell commands:
 		```powershell
		Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
		Debug-StorageSyncServer -Diagnose
 		```
		To test network connectivity on the server, run the following PowerShell commands:
		```powershell
		Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
		Debug-StorageSyncServer -TestNetworkConnectivity
		```
		To identify files that are failing to sync on the server, run the following PowerShell commands:
		```powershell
		Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
		Debug-StorageSyncServer -FileSyncErrorsReport
		```
		To collect logs and traces on the server, run the following PowerShell commands:
		```powershell
		Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
		Debug-StorageSyncServer -AFSDiag -OutputDirectory C:\output -KernelModeTraceLevel Verbose -UserModeTraceLevel Verbose
 		```
- Miscellaneous improvements
	- Reliability and telemetry improvements for cloud tiering and sync.

### Evaluation Tool
Before deploying Azure File Sync, you should evaluate whether it is compatible with your system using the Azure File Sync evaluation tool. This tool is an Azure PowerShell cmdlet that checks for potential issues with your file system and dataset, such as unsupported characters or an unsupported OS version. For installation and usage instructions, see [Evaluation Tool](file-sync-planning.md#evaluation-cmdlet) section in the planning guide. 

### Agent installation and server configuration
For more information on how to install and configure the Azure File Sync agent with Windows Server, see [Planning for an Azure File Sync deployment](file-sync-planning.md) and [How to deploy Azure File Sync](file-sync-deployment-guide.md).

- A restart is required for servers that have an existing Azure File Sync agent installation if the agent version is less than version 12.0.
- The agent installation package must be installed with elevated (admin) permissions.
- The agent is not supported on Nano Server deployment option.
- The agent is supported only on Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, and Windows Server 2022.
- The agent requires at least 2 GiB of memory. If the server is running in a virtual machine with dynamic memory enabled, the VM should be configured with a minimum 2048 MiB of memory. See [Recommended system resources](file-sync-planning.md#recommended-system-resources) for more information.
- The Storage Sync Agent (FileSyncSvc) service does not support server endpoints located on a volume that has the system volume information (SVI) directory compressed. This configuration will lead to unexpected results.

### Interoperability
- Antivirus, backup, and other applications that access tiered files can cause undesirable recall unless they respect the offline attribute and skip reading the content of those files. For more information, see [Troubleshoot Azure File Sync](file-sync-troubleshoot.md).
- File Server Resource Manager (FSRM) file screens can cause endless sync failures when files are blocked because of the file screen.
- Running sysprep on a server that has the Azure File Sync agent installed is not supported and can lead to unexpected results. The Azure File Sync agent should be installed after deploying the server image and completing sysprep mini-setup.

### Sync limitations
The following items don't sync, but the rest of the system continues to operate normally:
- Files with unsupported characters. See [Troubleshooting guide](file-sync-troubleshoot.md#handling-unsupported-characters) for list of unsupported characters.
- Files or directories that end with a period.
- Paths that are longer than 2,048 characters.
- The system access control list (SACL) portion of a security descriptor that's used for auditing.
- Extended attributes.
- Alternate data streams.
- Reparse points.
- Hard links.
- Compression (if it's set on a server file) isn't preserved when changes sync to that file from other endpoints.
- Any file that's encrypted with EFS (or other user mode encryption) that prevents the service from reading the data.

    > [!Note]  
    > Azure File Sync always encrypts data in transit. Data is always encrypted at rest in Azure.
 
### Server endpoint
- A server endpoint can be created only on an NTFS volume. ReFS, FAT, FAT32, and other file systems aren't currently supported by Azure File Sync.
- Cloud tiering is not supported on the system volume. To create a server endpoint on the system volume, disable cloud tiering when creating the server endpoint.
- Failover Clustering is supported only with clustered disks, but not with Cluster Shared Volumes (CSVs).
- A server endpoint can't be nested. It can coexist on the same volume in parallel with another endpoint.
- Do not store an OS or application paging file within a server endpoint location.

### Cloud endpoint
- Azure File Sync supports making changes to the Azure file share directly. However, any changes made on the Azure file share first need to be discovered by an Azure File Sync change detection job. A change detection job is initiated for a cloud endpoint once every 24 hours. To immediately sync files that are changed in the Azure file share, the [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet can be used to manually initiate the detection of changes in the Azure file share. In addition, changes made to an Azure file share over the REST protocol will not update the SMB last modified time and will not be seen as a change by sync.
- The storage sync service and/or storage account can be moved to a different resource group, subscription, or Azure AD tenant. After the  storage sync service or storage account is moved, you need to give the Microsoft.StorageSync application access to the storage account (see [Ensure Azure File Sync has access to the storage account](file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > When creating the cloud endpoint, the storage sync service and storage account must be in the same Azure AD tenant. Once the cloud endpoint is created, the storage sync service and storage account can be moved to different Azure AD tenants.

### Cloud tiering
- If a tiered file is copied to another location by using Robocopy, the resulting file isn't tiered. The offline attribute might be set because Robocopy incorrectly includes that attribute in copy operations.
- When copying files using robocopy, use the /MIR option to preserve file timestamps. This will ensure older files are tiered sooner than recently accessed files.

## Agent version 14.1.0.0
The following release notes are for version 14.1.0.0 of the Azure File Sync agent released December 1, 2021. These notes are in addition to the release notes listed for version 14.0.0.0.

### Improvements and issues that are fixed 
- Tiered files deleted on Windows Server 2022 are not detected by cloud tiering filter driver
	- This issue occurs because the DeleteFile API on Windows Server 2022 uses the FILE_DISPOSITION_INFORMATION_EX class to delete files. The v14.1 release adds support for detecting tiered files deleted using the FILE_DISPOSITION_INFORMATION_EX class.
    
    > [!Note]  
    > This issue can also impact Windows 2016 and Windows Server 2019 if a tiered file is deleted using the FILE_DISPOSITION_INFORMATION_EX class.

## Agent version 14.0.0.0
The following release notes are for version 14.0.0.0 of the Azure File Sync agent (released October 29, 2021).

### Improvements and issues that are fixed
- Reduced transactions when cloud change enumeration job runs 
	- Azure File Sync has a cloud change enumeration job that runs every 24 hours to detect changes made directly in the Azure file share and sync those changes to servers in your sync groups. We have made improvements to reduce the number of transactions when this job runs.

- Improved server endpoint deprovisioning guidance in the portal
	- When removing a server endpoint via the portal, we now provide step by step guidance based on the reason behind deleting the server endpoint, so that you can avoid data loss and ensure your data is where it needs to be (server or Azure file share). This feature also includes new PowerShell cmdlets (Get-StorageSyncStatus & New-StorageSyncUploadSession) that you can use on your local server to aid you through the deprovisioning process.

- Invoke-AzStorageSyncChangeDetection cmdlet improvements
	- Prior to the v14 release, if you made changes directly in the Azure file share, you could use the Invoke-AzStorageSyncChangeDetection cmdlet to detect the changes and sync them to the servers in your sync group. However, the cmdlet would fail to run if the path specified contained more than 10,000 items. We have improved the Invoke-AzStorageSyncChangeDetection cmdlet and the 10,000 item limit no longer applies when scanning the entire share. To learn more, see the [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) documentation.

- Miscellaneous improvements
	- Azure File Sync is now supported in West US 3 region.
	- Fixed a bug that caused the FileSyncErrorsReport.ps1 script to not provide the list of all per-item errors.
	- Reduced transactions when a file consistently fails to upload due to a per-item sync error.
	- Reliability and telemetry improvements for cloud tiering and sync. 

### Evaluation Tool
Before deploying Azure File Sync, you should evaluate whether it is compatible with your system using the Azure File Sync evaluation tool. This tool is an Azure PowerShell cmdlet that checks for potential issues with your file system and dataset, such as unsupported characters or an unsupported OS version. For installation and usage instructions, see [Evaluation Tool](file-sync-planning.md#evaluation-cmdlet) section in the planning guide. 

### Agent installation and server configuration
For more information on how to install and configure the Azure File Sync agent with Windows Server, see [Planning for an Azure File Sync deployment](file-sync-planning.md) and [How to deploy Azure File Sync](file-sync-deployment-guide.md).

- A restart is required for servers that have an existing Azure File Sync agent installation if the agent version is less than version 12.0.
- The agent installation package must be installed with elevated (admin) permissions.
- The agent is not supported on Nano Server deployment option.
- The agent is supported only on Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, and Windows Server 2022.
- The agent requires at least 2 GiB of memory. If the server is running in a virtual machine with dynamic memory enabled, the VM should be configured with a minimum 2048 MiB of memory. See [Recommended system resources](file-sync-planning.md#recommended-system-resources) for more information.
- The Storage Sync Agent (FileSyncSvc) service does not support server endpoints located on a volume that has the system volume information (SVI) directory compressed. This configuration will lead to unexpected results.

### Interoperability
- Antivirus, backup, and other applications that access tiered files can cause undesirable recall unless they respect the offline attribute and skip reading the content of those files. For more information, see [Troubleshoot Azure File Sync](file-sync-troubleshoot.md).
- File Server Resource Manager (FSRM) file screens can cause endless sync failures when files are blocked because of the file screen.
- Running sysprep on a server that has the Azure File Sync agent installed is not supported and can lead to unexpected results. The Azure File Sync agent should be installed after deploying the server image and completing sysprep mini-setup.

### Sync limitations
The following items don't sync, but the rest of the system continues to operate normally:
- Files with unsupported characters. See [Troubleshooting guide](file-sync-troubleshoot.md#handling-unsupported-characters) for list of unsupported characters.
- Files or directories that end with a period.
- Paths that are longer than 2,048 characters.
- The system access control list (SACL) portion of a security descriptor that's used for auditing.
- Extended attributes.
- Alternate data streams.
- Reparse points.
- Hard links.
- Compression (if it's set on a server file) isn't preserved when changes sync to that file from other endpoints.
- Any file that's encrypted with EFS (or other user mode encryption) that prevents the service from reading the data.

    > [!Note]  
    > Azure File Sync always encrypts data in transit. Data is always encrypted at rest in Azure.
 
### Server endpoint
- A server endpoint can be created only on an NTFS volume. ReFS, FAT, FAT32, and other file systems aren't currently supported by Azure File Sync.
- Cloud tiering is not supported on the system volume. To create a server endpoint on the system volume, disable cloud tiering when creating the server endpoint.
- Failover Clustering is supported only with clustered disks, but not with Cluster Shared Volumes (CSVs).
- A server endpoint can't be nested. It can coexist on the same volume in parallel with another endpoint.
- Do not store an OS or application paging file within a server endpoint location.

### Cloud endpoint
- Azure File Sync supports making changes to the Azure file share directly. However, any changes made on the Azure file share first need to be discovered by an Azure File Sync change detection job. A change detection job is initiated for a cloud endpoint once every 24 hours. To immediately sync files that are changed in the Azure file share, the [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet can be used to manually initiate the detection of changes in the Azure file share. In addition, changes made to an Azure file share over the REST protocol will not update the SMB last modified time and will not be seen as a change by sync.
- The storage sync service and/or storage account can be moved to a different resource group, subscription, or Azure AD tenant. After the  storage sync service or storage account is moved, you need to give the Microsoft.StorageSync application access to the storage account (see [Ensure Azure File Sync has access to the storage account](file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > When creating the cloud endpoint, the storage sync service and storage account must be in the same Azure AD tenant. Once the cloud endpoint is created, the storage sync service and storage account can be moved to different Azure AD tenants.

### Cloud tiering
- If a tiered file is copied to another location by using Robocopy, the resulting file isn't tiered. The offline attribute might be set because Robocopy incorrectly includes that attribute in copy operations.
- When copying files using robocopy, use the /MIR option to preserve file timestamps. This will ensure older files are tiered sooner than recently accessed files.

## Agent version 13.0.0.0
The following release notes are for version 13.0.0.0 of the Azure File Sync agent (released July 12, 2021).

### Improvements and issues that are fixed
- Authoritative upload  
	- Authoritative upload is a new mode available when creating the first server endpoint in a sync group. It is useful for the scenario where the cloud (Azure file share) has some/most of the data but is outdated and needs to be caught up with the more recent data on the new server endpoint. This is the case in offline migration scenarios like DataBox, for instance. When a DataBox is filled and sent to Azure, the users of the local server will keep changing / adding / deleting files on the local server. That makes the data in the DataBox and thus the Azure file share, slightly outdated. With Authoritative Upload, you can now tell the server and cloud, how to resolve this case and get the cloud seamlessly updated with the latest changes on the server. 

		No matter how the data got to the cloud, this mode can update the Azure file share if the data stems from the matching location on the server. Be sure to avoid large directory restructures between the initial copy to the cloud and catching up with Authoritative Upload. This will ensure you are only transporting updates. Changes to directory names will cause all files in these renamed directories to be uploaded again. This functionality is comparable to semantics of RoboCopy /MIR = mirror source to target, including removing files on the target that no longer exist on the source. 
		
		Authoritative Upload replaces the "Offline Data Transfer" feature for DataBox integration with Azure File Sync via a staging share. A staging share is no longer required to use DataBox. New Offline Data Transfer jobs can no longer be started with the AFS V13 agent. Existing jobs on a server will continue even with the upgrade to agent version 13.

- Portal improvements to view cloud change enumeration and sync progress  
	- When a new sync group is created, any connected server endpoint can only begin sync, when cloud change enumeration is complete.  In case files already exist in the cloud endpoint (Azure file share) of this sync group, change enumeration of content in the cloud can take some time. The more items (files and folders) exist in the namespace, the longer this process can take. Admins will now be able to obtain cloud change enumeration progress in the Azure portal to estimate an eta for completion / sync to start with servers.

- Support for server rename  
	- If a registered server is renamed, Azure File Sync will now show the new server name in the portal. If the server was renamed prior to the v13 release, the server name in the portal will now be updated to show the correct server name.

- Support for Windows Server 2022  
	- The Azure File Sync agent is now supported on Windows Server 2022.

	> [!Note]  
	> Windows Server 2022 adds support for TLS 1.3 which is not currently supported by Azure File Sync.  If the [TLS settings](/windows-server/security/tls/tls-ssl-schannel-ssp-overview) are managed via group policy, the server must be configured to support TLS 1.2. 

- Miscellaneous improvements
	- Reliability improvements for sync, cloud tiering and cloud change enumeration.
	- If a large number of files is changed on the server, sync upload is now performed from a VSS snapshot which reduces per-item errors and sync session failures. 
	- The Invoke-StorageSyncFileRecall cmdlet will now recall all tiered files associated with a server endpoint, even if the file has moved outside the server endpoint location. 
	- Explorer.exe is now excluded from cloud tiering last access time tracking.
	- New telemetry (Event ID 6664) to monitor the orphaned tiered files cleanup progress after removing a server endpoint with cloud tiering enabled.


### Evaluation Tool
Before deploying Azure File Sync, you should evaluate whether it is compatible with your system using the Azure File Sync evaluation tool. This tool is an Azure PowerShell cmdlet that checks for potential issues with your file system and dataset, such as unsupported characters or an unsupported OS version. For installation and usage instructions, see [Evaluation Tool](file-sync-planning.md#evaluation-cmdlet) section in the planning guide. 

### Agent installation and server configuration
For more information on how to install and configure the Azure File Sync agent with Windows Server, see [Planning for an Azure File Sync deployment](file-sync-planning.md) and [How to deploy Azure File Sync](file-sync-deployment-guide.md).

- A restart is required for servers that have an existing Azure File Sync agent installation if the agent version is less than version 12.0.
- The agent installation package must be installed with elevated (admin) permissions.
- The agent is not supported on Nano Server deployment option.
- The agent is supported only on Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, and Windows Server 2022.
- The agent requires at least 2 GiB of memory. If the server is running in a virtual machine with dynamic memory enabled, the VM should be configured with a minimum 2048 MiB of memory. See [Recommended system resources](file-sync-planning.md#recommended-system-resources) for more information.
- The Storage Sync Agent (FileSyncSvc) service does not support server endpoints located on a volume that has the system volume information (SVI) directory compressed. This configuration will lead to unexpected results.

### Interoperability
- Antivirus, backup, and other applications that access tiered files can cause undesirable recall unless they respect the offline attribute and skip reading the content of those files. For more information, see [Troubleshoot Azure File Sync](file-sync-troubleshoot.md).
- File Server Resource Manager (FSRM) file screens can cause endless sync failures when files are blocked because of the file screen.
- Running sysprep on a server that has the Azure File Sync agent installed is not supported and can lead to unexpected results. The Azure File Sync agent should be installed after deploying the server image and completing sysprep mini-setup.

### Sync limitations
The following items don't sync, but the rest of the system continues to operate normally:
- Files with unsupported characters. See [Troubleshooting guide](file-sync-troubleshoot.md#handling-unsupported-characters) for list of unsupported characters.
- Files or directories that end with a period.
- Paths that are longer than 2,048 characters.
- The system access control list (SACL) portion of a security descriptor that's used for auditing.
- Extended attributes.
- Alternate data streams.
- Reparse points.
- Hard links.
- Compression (if it's set on a server file) isn't preserved when changes sync to that file from other endpoints.
- Any file that's encrypted with EFS (or other user mode encryption) that prevents the service from reading the data.

    > [!Note]  
    > Azure File Sync always encrypts data in transit. Data is always encrypted at rest in Azure.
 
### Server endpoint
- A server endpoint can be created only on an NTFS volume. ReFS, FAT, FAT32, and other file systems aren't currently supported by Azure File Sync.
- Cloud tiering is not supported on the system volume. To create a server endpoint on the system volume, disable cloud tiering when creating the server endpoint.
- Failover Clustering is supported only with clustered disks, but not with Cluster Shared Volumes (CSVs).
- A server endpoint can't be nested. It can coexist on the same volume in parallel with another endpoint.
- Do not store an OS or application paging file within a server endpoint location.

### Cloud endpoint
- Azure File Sync supports making changes to the Azure file share directly. However, any changes made on the Azure file share first need to be discovered by an Azure File Sync change detection job. A change detection job is initiated for a cloud endpoint once every 24 hours. To immediately sync files that are changed in the Azure file share, the [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet can be used to manually initiate the detection of changes in the Azure file share. In addition, changes made to an Azure file share over the REST protocol will not update the SMB last modified time and will not be seen as a change by sync.
- The storage sync service and/or storage account can be moved to a different resource group, subscription, or Azure AD tenant. After the  storage sync service or storage account is moved, you need to give the Microsoft.StorageSync application access to the storage account (see [Ensure Azure File Sync has access to the storage account](file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > When creating the cloud endpoint, the storage sync service and storage account must be in the same Azure AD tenant. Once the cloud endpoint is created, the storage sync service and storage account can be moved to different Azure AD tenants.

### Cloud tiering
- If a tiered file is copied to another location by using Robocopy, the resulting file isn't tiered. The offline attribute might be set because Robocopy incorrectly includes that attribute in copy operations.
- When copying files using robocopy, use the /MIR option to preserve file timestamps. This will ensure older files are tiered sooner than recently accessed files.
