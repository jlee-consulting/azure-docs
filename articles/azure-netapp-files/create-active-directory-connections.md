---
title: Create and manage Active Directory connections for Azure NetApp Files | Microsoft Docs
description: This article shows you how to create and manage Active Directory connections for Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''

ms.assetid:
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.topic: how-to
ms.date: 07/26/2022
ms.author: anfdocs
---
# Create and manage Active Directory connections for Azure NetApp Files

Several features of Azure NetApp Files require that you have an Active Directory connection.  For example, you need to have an Active Directory connection before you can create an [SMB volume](azure-netapp-files-create-volumes-smb.md), a [NFSv4.1 Kerberos volume](configure-kerberos-encryption.md), or a [dual-protocol volume](create-volumes-dual-protocol.md). This article shows you how to create and manage Active Directory connections for Azure NetApp Files.

## <a name="requirements-for-active-directory-connections"></a>Requirements and considerations for Active Directory connections

> [!IMPORTANT]
> You must follow guidelines described in [Understand guidelines for Active Directory Domain Services site design and planning for Azure NetApp Files](understand-guidelines-active-directory-domain-service-site.md) for Active Directory Domain Services (AD DS) or Azure Active Directory Domain Services (AAD DS) used with Azure NetApp Files. 
> In addition, before creating the AD connection, review [Modify Active Directory connections for Azure NetApp Files](modify-active-directory-connections.md) to understand the impact of making changes to the AD connection configuration options after the AD connection has been created. Changes to the AD connection configuration options are disruptive to client access and some options cannot be changed at all.

* An Azure NetApp Files account must be created in the region where the Azure NetApp Files volumes are deployed.

* You can configure only one Active Directory (AD) connection per subscription per region. 

    Azure NetApp Files doesn’t support multiple AD connections in a single region, even if the AD connections are created in different NetApp accounts. However, you can have multiple AD connections in a single subscription if the AD connections are in different regions. If you need multiple AD connections in a single region, you can use separate subscriptions to do so. 

    The AD connection is visible only through the NetApp account it's created in. However, you can enable the Shared AD feature to allow NetApp accounts that are under the same subscription and same region to use the same AD connection. See [Map multiple NetApp accounts in the same subscription and region to an AD connection](#shared_ad). 

* The Azure NetApp Files AD connection admin account must have the following properties: 
    * It must be an AD DS domain user account in the same domain where the Azure NetApp Files machine accounts are created. 
    * It must have the permission to create machine accounts (for example, AD domain join) in the AD DS organizational unit path specified in the **Organizational unit path option** of the AD connection. 
    * It cannot be a [Group Managed Service Account](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview.md).

* The AD connection admin account supports DES, Kerberos AES-128, and Kerberos AES-256 encryption types for authentication with AD DS for Azure NetApp Files machine account creation (for example, AD domain join operations).

* To enable the AES encryption on the Azure NetApp Files AD connection admin account, you must use an AD domain user account that is a member of one of the following AD DS groups: 

    * Domain Admins 
    * Enterprise Admins 
    * Administrators 
    * Account Operators 
    * Azure AD DC Administrators _(Azure AD DS Only)_

    Alternatively, an AD domain user account with `msDS-SupportedEncryptionTypes` write permission on the AD connection admin account can also be used to set the Kerberos encryption type property on the AD connection admin account. 

    >[!NOTE]
    >It's not recommended or required to add the Azure NetApp Files AD admin account to the AD domain groups listed above. Nor is it recommended or required to grant `msDS-SupportedEncryptionTypes` write permission to the AD admin account.  

    If you set both AES-128 and AES-256 Kerberos encryption on the admin account of the AD connection, the highest level of encryption supported by your AD DS will be used. If AES encryption is not set, DES encryption will be used by default.   

* To enable AES encryption support for the admin account in the AD connection, run the following Active Directory PowerShell commands:

    ```powershell
    Get-ADUser -Identity <ANF AD connection account username>
    Set-ADUser -KerberosEncryptionType <encryption_type>
    ```

    `KerberosEncryptionType` is a multivalued parameter that supports AES-128 and AES-256 values. 

* For more information, see the [Set-ADUser documentation](/powershell/module/activedirectory/set-aduser).


## Create an Active Directory connection

1. From your NetApp account, select **Active Directory connections**, then select **Join**.  

    ![Screenshot showing the Active Directory connections menu. The join button is highlighted.](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

    >[!NOTE]
    >Azure NetApp Files supports only one Active Directory connection within the same region and the same subscription. 

2. In the Join Active Directory window, provide the following information, based on the Domain Services you want to use:  

    * **Primary DNS (required)**  
        This is the IP address of the primary DNS server that is required for Active Directory domain join operations, SMB authentication, Kerberos, and LDAP operations.

    * **Secondary DNS**   
        This is the IP address of the secondary DNS server that is required for Active Directory domain join operations, SMB authentication, Kerberos, and LDAP operations.
        
        >[!NOTE]
        >It is recommended that you configure a Secondary DNS server. See [Understand guidelines for Active Directory Domain Services site design and planning for Azure NetApp Files](understand-guidelines-active-directory-domain-service-site.md). Ensure that your DNS server configuration meets the requirements for Azure NetApp Files. Otherwise, Azure NetApp Files service operations, SMB authentication, Kerberos, or LDAP operations might fail.

        If you use Azure AD DS (AAD DS), you should use the IP addresses of the AAD DS domain controllers for Primary DNS and Secondary DNS respectively. 
    * **AD DNS Domain Name (required)**  
       This is the fully qualified domain name of the AD DS that will be used with Azure NetApp Files (for example, `contoso.com`).
    * **AD Site Name**  
        This is the AD DS site name that will be used by Azure NetApp Files for domain controller discovery.  
        
         >[!NOTE]
         > See [Understand guidelines for Active Directory Domain Services site design and planning for Azure NetApp Files](understand-guidelines-active-directory-domain-service-site.md). Ensure that your AD DS site design and configuration meets the requirements for Azure NetApp Files. Otherwise, Azure NetApp Files service operations, SMB authentication, Kerberos, or LDAP operations might fail.

    * **SMB server (computer account) prefix (required)**  
        This is the naming prefix for new machine accounts created in AD DS for Azure NetApp Files SMB, dual protocol, and NFSv4.1 Kerberos volumes. 

        For example, if the naming standard that your organization uses for file services is `NAS-01`, `NAS-02`, and so on, then you would use `NAS` for the prefix. 
    
        Azure NetApp Files will create additional machine accounts in AD DS as needed. 
    
        >[!IMPORTANT]
        >Renaming the SMB server prefix after you create the Active Directory connection is disruptive. You will need to re-mount existing SMB shares after renaming the SMB server prefix. 

    * **Organizational unit path**  
        This is the LDAP path for the organizational unit (OU) where SMB server machine accounts will be created. That is, `OU=second level, OU=first level`. For example, if you want to use an OU called `ANF` created at the root of the domain, the value would be `OU=ANF`.

        If no value is provided, Azure NetApp Files will use the `CN=Computers` container. 

        If you're using Azure NetApp Files with Azure Active Directory Domain Services (AAD DS), the organizational unit path is `OU=AADDC Computers`

        ![Screenshot of the Join Active Directory input fields.](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * <a name="aes-encryption"></a>**AES Encryption**    
        This option enables AES encryption authentication support for the admin account of the AD connection. 

        ![Screenshot of the AES description field which is a checkbox.](../media/azure-netapp-files/active-directory-aes-encryption.png) 
        
        See [Requirements for Active Directory connections](#requirements-for-active-directory-connections) for requirements.  
  
    * <a name="ldap-signing"></a>**LDAP Signing**   

        This option enables LDAP signing. This functionality enables integrity verification for Simple Authentication and Security Layer (SASL) LDAP binds from Azure NetApp Files and the user-specified [Active Directory Domain Services domain controllers](/windows/win32/ad/active-directory-domain-services). 
        
        Azure NetApp Files supports LDAP Channel Binding if both LDAP Signing and LDAP over TLS settings options are enabled in the Active Directory Connection. For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).  

        ![Screenshot of the LDAP signing checkbox.](../media/azure-netapp-files/active-directory-ldap-signing.png) 

    * **Allow local NFS users with LDAP**
        This option enables local NFS client users to access to NFS volumes. Setting this option disables extended groups for NFS volumes. It also limits the number of groups to 16. For more information, see [Allow local NFS users with LDAP to access a dual-protocol volume](create-volumes-dual-protocol.md#allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume). 

    * **LDAP over TLS**   

        This option enables LDAP over TLS for secure communication between an Azure NetApp Files volume and the Active Directory LDAP server. You can enable LDAP over TLS for NFS, SMB, and dual-protocol volumes of Azure NetApp Files. 
        
        >[!NOTE]
        >LDAP over TLS must not be enabled if you're using Azure Active Directory Domain Services (AAD DS). AAD DS uses LDAPS (port 636) to secure LDAP traffic instead of LDAP over TLS (port 389). 
        
        For more information, see [Enable Active Directory Domain Services (AD DS) LDAP authentication for NFS volumes](configure-ldap-over-tls.md).

    * **Server root CA Certificate** 
        
        This option uploads the CA certificate used with LDAP over TLS. 
        
        For more information, see [Enable Active Directory Domain Services (AD DS) LDAP authentication for NFS volumes](configure-ldap-over-tls.md).  

    * **LDAP Search Scope**, **User DN**, **Group DN**, and **Group Membership Filter**  

        The **LDAP search scope** option optimizes Azure NetApp Files storage LDAP queries for use with large AD DS topologies and LDAP with extended groups or Unix security style with an Azure NetApp Files dual-protocol volume. 
        
        The **User DN** and **Group DN** options allow you to set the search base in AD DS LDAP.  
         
        The **Group Membership Filter** option allows you to create a custom search filter for users who are members of specific AD DS groups. 

        ![Screenshot of the LDAP search scope field, showing a checked box.](../media/azure-netapp-files/ldap-search-scope-checked.png)

        See [Configure AD DS LDAP with extended groups for NFS volume access](configure-ldap-extended-groups.md#ldap-search-scope) for information about these options.

    * <a name="backup-policy-users"></a> **Backup policy users**
        This option grants addition security privileges to AD DS domain users or groups that require elevated backup privileges to support backup, restore, and migration workflows in Azure NetApp Files. The specified AD DS user accounts or groups will have elevated NTFS permissions at the file or folder level.

        ![Screenshot of the Backup policy users field showing an empty text input field.](../media/azure-netapp-files/active-directory-backup-policy-users.png) 

        The following privileges apply when you use the **Backup policy users**  setting:

        | Privilege | Description |
        |---|---|
        | `SeBackupPrivilege` | Back up files and directories, overriding any ACLs.  |
        | `SeRestorePrivilege` |  Restore files and directories, overriding any ACLs. <br> Set any valid user or group SID as the file owner. |
        | `SeChangeNotifyPrivilege` |  Bypass traverse checking. <br> Users with this privilege aren't required to have traversed (`x`) permissions to traverse folders or symlinks.  |

     * **Security privilege users**   <!-- SMB CA share feature -->   
        This option grants security privilege (`SeSecurityPrivilege`) to AD DS domain users or groups that require elevated privileges to access Azure NetApp Files volumes. The specified AD DS users or groups will be allowed to perform certain actions on SMB shares that require security privilege not assigned by default to domain users.

        ![Screenshot showing the Security privilege users box of Active Directory connections window.](../media/azure-netapp-files/security-privilege-users.png) 

        The following privilege applies when you use the **Security privilege users** setting:

        |  Privilege  |  Description  |
        |---|---|
        |  `SeSecurityPrivilege`  |  Manage log operations.  |

        This feature is used for installing SQL Server in certain scenarios where a non-administrator AD DS domain account must temporarily be granted elevated security privilege.

        >[!NOTE]
        > Using the Security privilege users feature requires that you submit a waitlist request through the Azure NetApp Files SMB Continuous Availability Shares Public Preview waitlist submission page. Wait for an official confirmation email from the Azure NetApp Files team before using this feature.

        > [!IMPORTANT]
        > Using the **Security privilege users** feature requires that you submit a waitlist request through the **[Azure NetApp Files SMB Continuous Availability Shares Public Preview waitlist submission page](https://aka.ms/anfsmbcasharespreviewsignup)**. Wait for an official confirmation email from the Azure NetApp Files team before using this feature.  
        >This feature is optional and supported only with SQL server. The AD DS domain account used for installing SQL server must already exist before you add it to the **Security privilege users** option. When you add the SQL Server installer account to **Security privilege users** option, the Azure NetApp Files service might validate the account by contacting an AD DS domain controller. This action might fail if Azure NetApp Files cannot contact the AD DS domain controller.
        
        For more information about `SeSecurityPrivilege` and SQL Server, see [SQL Server installation fails if the Setup account doesn't have certain user rights](/troubleshoot/sql/install/installation-fails-if-remove-user-right).

    * <a name="administrators-privilege-users"></a>**Administrators privilege users** 

        This option grants additional security privileges to AD DS domain users or groups that require elevated privileges to access the Azure NetApp Files volumes. The specified accounts will have elevated permissions at the file or folder level.

        ![Screenshot that shows the Administrators box of Active Directory connections window.](../media/azure-netapp-files/active-directory-administrators.png) 

        The following privileges apply when you use the **Administrators privilege users** setting:

        |  Privilege  |  Description  |
        |---|---|
        |  `SeBackupPrivilege`  |  Back up files and directories, overriding any ACLs. |  
        |  `SeRestorePrivilege`  |  Restore files and directories, overriding any ACLs. <br> Set any valid user or group SID as the file owner.  |  
        |  `SeChangeNotifyPrivilege`  |  Bypass traverse checking. <br> Users with this privilege aren't required to have traverse (`x`) permissions to traverse folders or symlinks.  |  
        |  `SeTakeOwnershipPrivilege`  |  Take ownership of files or other objects. |  
        |  `SeSecurityPrivilege`  |  Manage log operations. |  
        |  `SeChangeNotifyPrivilege`  |  Bypass traverse checking. <br> Users with this privilege aren't required to have traverse (`x`) permissions to traverse folders or symlinks.  |  <!-- tHIS option IS REMOVED -->

    * Credentials, including your **username** and **password**

        ![Screenshot that shows Active Directory credentials fields showing username, password and confirm password fields.](../media/azure-netapp-files/active-directory-credentials.png)

3. Select **Join**.  

    The Active Directory connection you created appears.

    ![Screenshot of the Active Directory connections menu showing a successfully created connection.](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="shared_ad"></a>Map multiple NetApp accounts in the same subscription and region to an AD connection  

The Shared AD feature enables all NetApp accounts to share an Active Directory (AD) connection created by one of the NetApp accounts that belong to the same subscription and the same region. For example, using this feature, all NetApp accounts in the same subscription and region can use the common AD configuration to create an [SMB volume](azure-netapp-files-create-volumes-smb.md), a [NFSv4.1 Kerberos volume](configure-kerberos-encryption.md), or a [dual-protocol volume](create-volumes-dual-protocol.md). When you use this feature, the AD connection will be visible in all NetApp accounts that are under the same subscription and same region.   

This feature is currently in preview. You need to register the feature before using it for the first time. After registration, the feature is enabled and works in the background. No UI control is required. 

1. Register the feature: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSharedAD
    ```

2. Check the status of the feature registration: 

    > [!NOTE]
    > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is **Registered** before continuing.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSharedAD
    ```
You can also use [Azure CLI commands](/cli/azure/feature) `az feature register` and `az feature show` to register the feature and display the registration status. 
 
## Next steps  

* [Understand guidelines for Active Directory Domain Services site design and planning for Azure NetApp Files](understand-guidelines-active-directory-domain-service-site.md)
* [Modify Active Directory connections](modify-active-directory-connections.md)
* [Create an SMB volume](azure-netapp-files-create-volumes-smb.md)
* [Create a dual-protocol volume](create-volumes-dual-protocol.md)
* [Configure NFSv4.1 Kerberos encryption](configure-kerberos-encryption.md)
* [Install a new Active Directory forest using Azure CLI](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm) 
* [Enable Active Directory Domain Services (AD DS) LDAP authentication for NFS volumes](configure-ldap-over-tls.md)
* [AD DS LDAP with extended groups for NFS volume access](configure-ldap-extended-groups.md)
