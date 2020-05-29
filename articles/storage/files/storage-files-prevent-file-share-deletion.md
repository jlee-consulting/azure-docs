---
title: Prevent accidental deletion - Azure file shares
description: Learn about soft delete for Azure file shares and how you can use it to for data recovery and preventing accidental deletion.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: rogarana
ms.subservice: files
services: storage
---

# Prevent accidental deletion of Azure file shares

Azure Storage now offers soft delete for file shares (preview). Soft delete allows you to recover your data when it is mistakenly deleted by an application or other storage account user.

## How soft delete (preview) works

When enabled, soft delete enables you to save and recover your file shares when they're deleted. When data is deleted, it transitions to a soft deleted state instead of being permanently erased. You can configure the amount of time soft deleted data is recoverable before it's permanently deleted.

Soft delete can be enabled on new or existing file shares. Soft delete is also backwards compatible, so you don't have to make any changes to your applications to take advantage of the protections of soft delete. 

For soft-deleted premium file shares, the file share quota (the provisioned size of a file share) is used in the total storage account quota calculation until the soft-deleted share expiry date, when the share is fully deleted.

### Availability

Soft delete for Azure file shares (preview) is available on all storage tiers, all storage account types, and in every region that Azure Files is available in.

## Configuration settings

Soft delete for file shares is enabled at the storage account level, the soft delete settings apply to all file shares within a storage account. When you create a new storage account, soft delete is off by default. Soft delete is also off by default for existing storage accounts. You can toggle soft delete on and off at any time.

If you enable soft delete for file shares, delete some file shares, and then disable soft delete, if the shares were saved in that period you can still access and recover those file shares. When you enable soft delete, you also need to configure the retention period.

The retention period indicates the amount of time that soft deleted file shares are stored and available for recovery. For file shares that are explicitly deleted, the retention period clock starts when the data is deleted. Currently you can keep soft deleted shares for between 1 and 365 days.

You can change the soft delete retention period at any time. An updated retention period will only apply to shares deleted after the retention period has been updated. Shares deleted before the retention period update will expire based on the retention period that was configured when that data was deleted.

To permanently delete a file share in a soft delete state prior to its expiry time, you must undelete the share, disable soft delete, and then delete the share again. Then you should reenable soft delete, since any other file shares in that storage account will be vulnerable to accidental deletion while soft delete is off.

## Pricing and billing

Both standard and premium file shares are billed on the used capacity when soft deleted, rather than provisioned capacity. Additionally, premium file shares are billed at the snapshot rate while in the soft delete state. Standard file shares are billed at the regular rate while in the soft delete state. You won't be charged for data that is permanently deleted after the configured retention period.

For more information on prices for Azure File Storage in general, see the [Azure File Storage Pricing Page](https://azure.microsoft.com/pricing/details/storage/files/).

When you initially enable soft delete, we recommend using a small retention period to better understand how the feature affects your bill.

## Next steps

To learn how to enable and use soft delete, continue to [Enable soft delete](storage-files-enable-soft-delete.md)
