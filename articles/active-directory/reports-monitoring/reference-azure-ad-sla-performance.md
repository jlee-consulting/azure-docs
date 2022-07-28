---
title: Azure Active Directory SLA performance | Microsoft Docs
description: Learn about the Azure AD SLA performance
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
ms.date: 03/15/2022
ms.author: markvi
ms.reviewer: besiler

ms.collection: M365-identity-device-management
---

# Azure Active Directory SLA performance 

As an identity admin, you may need to track Azure AD's service-level agreement (SLA) performance to make sure Azure AD can support your vital apps. This article shows how the Azure AD service has performed according to the [SLA for Azure Active Directory (Azure AD)](https://azure.microsoft.com/support/legal/sla/active-directory/v1_1/). 

You can use this article in discussions with app or business owners to help them understand the performance they can expect from Azure AD. 


## Service availability commitment 

Microsoft offers Premium Azure AD customers the opportunity to get a service credit if Azure AD fails to meet the documented SLA. When you request a service credit, Microsoft evaluates the SLA for your specific tenant; however, this global SLA can give you an indication of the general health of Azure AD over time. 

The SLA covers the following scenarios that are vital to businesses:

- **User authentication:** Users are able to login to the Azure Active Directory service.

- **App access:** Azure Active Directory successfully emits the authentication and authorization tokens required for users to log into applications connected to the service.

For full details on SLA coverage and instructions on requesting a service credit, see the [SLA for Azure Active Directory (Azure AD)](https://azure.microsoft.com/support/legal/sla/active-directory/v1_1/).


## No planned downtime 

You rely on Azure AD to provide identity and access management for your vital systems. To ensure Azure AD is available when business operations require it, Microsoft does not plan downtime for Azure AD system maintenance. Instead, maintenance is performed as the service runs, without customer impact. 

## Recent worldwide SLA performance 

To help you plan for moving workloads to Azure AD, we publish past SLA performance. These numbers show the level at which Azure AD met the requirements in the [SLA for Azure Active Directory (Azure AD)](https://azure.microsoft.com/support/legal/sla/active-directory/v1_1/), for all tenants. 

For each month, we truncate the SLA attainment at three places after the decimal. Numbers are not rounded up, so actual SLA attainment is higher than indicated. 


| Month     | 2021    | 2022    |
| ---       | ---     | ---     |
| January   |         | 99.999% |
| February  | 99.999% | 99.999% |
| March     | 99.568% | 99.999% |
| April     | 99.999% | 99.999% |
| May       | 99.999% |         |
| June      | 99.999% |         |
| July      | 99.999% |         |
| August    | 99.999% |         |
| September | 99.999% |         |
| October   | 99.999% |         |
| November  | 99.998% |         |
| December  | 99.978% |         |



### How is Azure AD SLA measured? 

The Azure AD SLA is measured in a way that reflects customer authentication experience, rather than simply reporting on whether the system is available to outside connections. This means that the calculation is based on whether:

- Users can authenticate 
- Azure AD successfully issues tokens for target apps after authentication
  
The numbers above are a global total of Azure AD authentications across all customers and geographies. 
  

## Incident history 

All incidents that seriously impact Azure AD performance are documented in the [Azure status history](https://azure.status.microsoft/status/history/). Not all events documented in Azure status history are serious enough to cause Azure AD to go below its SLA. You can view information about the impact of incidents, as well as a root cause analysis of what caused the incident and what steps Microsoft took to prevent future incidents. 

 


## Next steps

* [Azure AD reports overview](overview-reports.md)
* [Programmatic access to Azure AD reports](concept-reporting-api.md)
* [Azure Active Directory risk detections](../identity-protection/overview-identity-protection.md)
