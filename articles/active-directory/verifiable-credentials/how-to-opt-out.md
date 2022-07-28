---
title: Opt out of the Azure Active Directory Verifiable Credentials (preview)
description: Learn how to Opt Out of the Verifiable Credentials Preview
documentationCenter: ''
author: barclayn
manager: rkarlin
ms.service: decentralized-identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 06/16/2022
ms.author: barclayn

#Customer intent: As an administrator, I am looking for information to help me disable 
---

# Opt out of the verifiable credentials (preview)

[!INCLUDE [Verifiable Credentials announcement](../../../includes/verifiable-credentials-brand.md)]

In this article:

- The reason why you may need to opt out.
- The steps required.
- What happens to your data?
- Effect on existing verifiable credentials.

> [!IMPORTANT]
> Azure Active Directory Verifiable Credentials is currently in public preview.
> This preview version is provided without a service level agreement, and it's not recommended for production workloads. Certain features might not be supported or might have constrained capabilities.
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Prerequisites

- Complete verifiable credentials onboarding.

## When do you need to opt out?

Opting out is a one-way operation, after you opt out your Azure Active Directory Verifiable Credentials environment will be reset. During the public preview, opting out may be required to:

- Enable new service capabilities.
- Reset your service configuration.
- Switch between trust systems ION and Web

## What happens to your data when you opt out?

When you complete opting out of the Azure Active Directory Verifiable Credentials service, the following actions will take place:

- The DID keys in Key Vault will be [soft deleted](../../key-vault/general/soft-delete-overview.md).
- The issuer object will be deleted from our database.
- The tenant identifier will be deleted from our database.
- All of the verifiable credentials contracts will be deleted from our database.

Once an opt-out takes place, you won't be able to recover your DID or conduct any operations on your DID. This step is a one-way operation, and you need to opt in again, which results in a new environment being created.

## Effect on existing verifiable credentials

All verifiable credentials already issued will continue to exist. They won't be cryptographically invalidated as your DID will remain resolvable through ION.
However, when relying parties call the status API, they'll always receive back a failure message.  

## How to opt out from the Azure Active Directory Verifiable Credentials Public Preview?

1. From the Azure portal search for verifiable credentials.
2. Choose **Organization Settings** from the left side menu.
3. Under the section, **Reset your organization**, select **Delete all credentials, and opt out of preview**.

    :::image type="content" source="media/how-to-opt-out/settings-reset.png" alt-text="Section in settings that allows you to reset your organization":::


4. Read the warning message and to continue select **Delete and opt out**.

    :::image type="content" source="media/how-to-opt-out/delete-and-opt-out.png" alt-text="settings delete and opt out":::

## Next steps

- Set up verifiable credentials on your [Azure tenant](verifiable-credentials-configure-tenant.md)