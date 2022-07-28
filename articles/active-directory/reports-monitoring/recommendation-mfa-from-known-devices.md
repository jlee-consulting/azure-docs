---
title: Azure Active Directory recommendation - Minimize MFA prompts from known devices in Azure AD | Microsoft Docs
description: Learn why you should minimize MFA prompts from known devices in Azure AD.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''

ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/31/2022
ms.author: markvi
ms.reviewer: hafowler

ms.collection: M365-identity-device-management
---

# Azure AD recommendation: Minimize MFA prompts from known devices 

[Azure AD recommendations](overview-recommendations.md) is a feature that provides you with personalized insights and actionable guidance to align your tenant with recommended best practices.


This article covers the recommendation to convert minimize multi-factor authentication (MFA) prompts from known devices. 


## Description

As an admin, you want to maintain security for my company’s resources, but you also want your employees to easily access resources as needed.

MFA enables you to enhance the security posture of your tenant. While enabling MFA is a good practice, you should try to keep the number of MFA prompts your users have to go through at a minimum. One option you have to accomplish this goal is to **allow users to remember multi-factor authentication on devices they trust**.

The remember multi-factor authentication feature sets a persistent cookie on the browser when a user selects the Don't ask again for X days option at sign-in. The user isn't prompted again for MFA from that browser until the cookie expires. If the user opens a different browser on the same device or clears the cookies, they're prompted again to verify.

![Remember MFA on trusted devices](./media/recommendation-mfa-from-known-devices\remember-mfa-on-trusted-devices.png)



For more information, see [Configure Azure AD Multi-Factor Authentication settings](../authentication/howto-mfa-mfasettings.md).


## Logic 

This recommendation shows up, if you have set the remember multi-factor authentication feature to less than 30 days.


## Value 

This recommendation improves your user's productivity and minimizes the sign-in time with fewer MFA prompts. Ensure that your most sensitive resources can have the tightest controls, while your least sensitive resources can be more freely accessible.

## Action plan

1. Review [configure Azure AD Multi-Factor Authentication settings](../authentication/howto-mfa-mfasettings.md).  

2. Set the remember multi-factor authentication feature to 90 days.
 

## Next steps

- [What is Azure Active Directory recommendations](overview-recommendations.md)

- [Azure AD reports overview](overview-reports.md)