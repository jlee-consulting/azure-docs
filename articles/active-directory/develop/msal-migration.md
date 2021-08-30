---
title: Migrate to the Microsoft Authentication Library (MSAL)
titleSuffix: Microsoft identity platform
description: Learn about the differences between the Microsoft Authentication Library (MSAL) and Azure AD Authentication Library (ADAL) and how to migrate to MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/22/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, has-adal-ref
# Customer intent: As an application developer, I want to learn about MSAL library so I can migrate my ADAL applications to MSAL.
---

# Migrate applications to the Microsoft Authentication Library (MSAL)

If any of your applications use the Azure Active Directory Authentication Library (ADAL)  for authentication and authorization functionality, it's time to migrate them to the [Microsoft Authentication Library (MSAL)](msal-overview.md#languages-and-frameworks).

- All Microsoft support and development for ADAL, including security fixes, ends on June 30, 2022.
- No new features have been added to ADAL since June 30, 2020.

> [!WARNING]
> If you choose not to migrate to MSAL before ADAL support ends on June 30, 2022, you put your app's security at risk. Existing apps that use ADAL will continue to work after the end-of-support date, but Microsoft will no longer release security fixes on ADAL.

## Why switch to MSAL?

MSAL provides multiple benefits over ADAL, including the following features: 

|Features|MSAL|ADAL|
|---------|---------|---------|
|**Security**|||
|Security fixes beyond June 30, 2022|![Security fixes beyond June 30, 2022 - MSAL provides the feature][y]|![Security fixes beyond June 30, 2022 - ADAL doesn't provide the feature][n]|
| Proactively refresh and revoke tokens based on policy or critical events for Microsoft Graph and other APIs that support [Continuous Access Evaluation (CAE)](app-resilience-continuous-access-evaluation.md).|![Proactively refresh and revoke tokens based on policy or critical events for Microsoft Graph and other APIs that support Continuous Access Evaluation (CAE) - MSAL provides the feature][y]|![Proactively refresh and revoke tokens based on policy or critical events for Microsoft Graph and other APIs that support Continuous Access Evaluation (CAE) - ADAL doesn't provide the feature][n]|
| Standards compliant with OAuth v2.0 and OpenID Connect (OIDC) |![Standards compliant with OAuth v2.0 and OpenID Connect (OIDC) - MSAL provides the feature][y]|![Standards compliant with OAuth v2.0 and OpenID Connect (OIDC) - ADAL doesn't provide the feature][n]|
|**User accounts and experiences**|||
|Azure Active Directory (Azure AD) accounts|![Azure Active Directory (Azure AD) accounts - MSAL provides the feature][y]|![Azure Active Directory (Azure AD) accounts - ADAL provides the feature][y]|
| Microsoft account (MSA) |![Microsoft account (MSA) - MSAL provides the feature][y]|![Microsoft account (MSA) - ADAL doesn't provide the feature][n]|
| Azure AD B2C accounts |![Azure AD B2C accounts - MSAL provides the feature][y]|![Azure AD B2C accounts - ADAL doesn't provide the feature][n]|
| Best single sign-on experience |![Best single sign-on experience - MSAL provides the feature][y]|![Best single sign-on experience - ADAL doesn't provide the feature][n]|
|**Resilience**|||
| Proactive token renewal |![Proactive token renewal - MSAL provides the feature][y]|![Proactive token renewal - ADAL doesn't provide the feature][n]|
| Throttling |![Throttling - MSAL provides the feature][y]|![Throttling - ADAL doesn't provide the feature][n]|

## AD FS support in MSAL.NET

You can use MSAL.NET, MSAL Java, and MSAL Python to get tokens from Active Directory Federation Services (AD FS) 2019 or later. Earlier versions of AD FS, including AD FS 2016, are unsupported by MSAL.

If you need to continue using AD FS, you should upgrade to AD FS 2019 or later before you update your applications from ADAL to MSAL.

## How to migrate to MSAL

Before you start the migration, you need to identify which of your apps are using ADAL for authentication. Follow the steps in this article to get a list by using the Azure portal:
- [How to: Get a complete list of apps using ADAL in your tenant](howto-get-list-of-all-active-directory-auth-library-apps.md)

After identifying your apps that use ADAL, migrate them to MSAL depending on your application type as illustrated below.

[!INCLUDE [application type](includes/adal-msal-migration.md)]

## Migration help

If you have questions about migrating your app from ADAL to MSAL, here are some options:

- Post your question on [Microsoft Q&A](/answers/topics/azure-ad-adal-deprecation.html) and tag it with `[azure-ad-adal-deprecation]`.
- Open an issue in the library's GitHub repository. See the [Languages and frameworks](msal-overview.md#languages-and-frameworks) section of the MSAL overview article for links to each library's repo.

If you partnered with an Independent Software Vendor (ISV) in the development of your application, we recommend that you contact them directly to understand their migration journey to MSAL.

## Next steps

For more information about MSAL, including usage information and which libraries are available for different programming languages and application types, see:

- [Acquire and cache tokens using MSAL](msal-acquire-cache-tokens.md)
- [Application configuration options](msal-client-application-configuration.md)
- [MSAL authentication libraries](reference-v2-libraries.md)

<!--
 ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->
[y]: media/common/yes.png
[n]: media/common/no.png