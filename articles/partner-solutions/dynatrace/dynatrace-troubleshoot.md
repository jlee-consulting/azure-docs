---
title: Troubleshooting Dynatrace for Azure (preview) - Azure partner solutions
description: This article provides information about troubleshooting Dynatrace for Azure 
ms.topic: conceptual
ms.collection: na
author: flang-msft
ms.author: franlanglois
ms.date: 06/07/2022

---

# Troubleshoot Dynatrace for Azure

This article describes how to contact support when working with a Dynatrace for Azure (preview) resource. Before contacting support, see [Fix common errors](#fix-common-errors).

## Contact support

To contact support about the Azure Dynatrace integration, select **New Support request** in the left pane. Select the link to the Dynatrace support website.

:::image type="content" source="media/dynatrace-troubleshoot/dynatrace-support.png" alt-text="Screenshot showing new support request selected in resource menu.":::

## Fix common errors

This document contains information about troubleshooting your solutions that use Dynatrace.

### Purchase error

- Purchase fails because a valid credit card isn't connected to the Azure subscription or a payment method isn't associated with the subscription.

  - Use a different Azure subscription. Or, add or update the credit card or payment method for the subscription. For more information, see [updating the credit and payment method](../../cost-management-billing/manage/change-credit-card.md).

- The EA subscription doesn't allow _Marketplace_ purchases.
  - Use a different subscription. Or, check if your EA subscription is enabled for Marketplace purchase. For more information, see [Enable Marketplace purchases](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases). If those options don't solve the problem, contact [Dynatrace support](https://support.dynatrace.com/).

### Unable to create Dynatrace resource

- To set up the Azure Dynatrace integration, you must have **Owner** or **Contributor** access on the Azure subscription. Ensure you have the appropriate access before starting the setup.

- Create fails because Last Name is empty. This happens when the user info in Azure AD is incomplete and doesn't contain Last Name. Contact your Azure tenant's global administrator to rectify this and try again.

### Single sign-on errors

- **Single sign-on configuration indicates lack of permissions** - This happens when the user that is trying to configure single sign-on doesn't have tenant
- **Unable to save single sign-on settings** - This error happens when there's another Enterprise app that is using the Dynatrace SAML identifier. To find which app is using it, select **Edit** on the Basic **SAML** configuration section.
    To resolve this issue, either disable the other app or use the other app as the Enterprise app to set up SAML SSO.

- **App not showing in Single sign-on settings page** - First, search for application ID. If no result is shown, check the SAML settings of the app. The grid only shows apps with correct SAML settings.
    The following image shows the correct values.

## Next steps

- Learn about [managing your instance](dynatrace-how-to-manage.md) of Dynatrace.
