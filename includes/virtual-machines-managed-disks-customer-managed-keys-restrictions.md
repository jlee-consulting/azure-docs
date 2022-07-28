---
 title: include file
 description: include file
 services: virtual-machines
 author: roygara
 ms.service: virtual-machines
 ms.topic: include
 ms.date: 03/16/2022
 ms.author: rogarana
 ms.custom: include file
---
- Only [software and HSM RSA keys](../articles/key-vault/keys/about-keys.md) of sizes 2,048-bit, 3,072-bit and 4,096-bit are supported, no other keys or sizes.
    - [HSM](../articles/key-vault/keys/hsm-protected-keys.md) keys require the **premium** tier of Azure Key vaults.
- Disks created from custom images that are encrypted using server-side encryption and customer-managed keys must be encrypted using the same customer-managed keys and must be in the same subscription.
- Snapshots created from disks that are encrypted with server-side encryption and customer-managed keys must be encrypted with the same customer-managed keys.
- Most resources related to your customer-managed keys (disk encryption sets, VMs, disks, and snapshots) must be in the same subscription and region.
    - Azure Key Vaults may be used from a different subscription but must be in the same region and tenant as your disk encryption set.
- Disks, snapshots, and images encrypted with customer-managed keys cannot move to another resource group and subscription.
- Managed disks currently or previously encrypted using Azure Disk Encryption cannot be encrypted using customer-managed keys.
- Can only create up to 1000 disk encryption sets per region per subscription.
- For information about using customer-managed keys with shared image galleries, see [Preview: Use customer-managed keys for encrypting images](../articles/virtual-machines/image-version-encryption.md).
