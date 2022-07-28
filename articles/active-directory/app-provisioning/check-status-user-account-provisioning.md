---
title: Report automatic user account provisioning from Azure Active Directory to Software as a Service (SaaS) applications
description: 'Learn how to check the status of automatic user account provisioning jobs, and how to troubleshoot the provisioning of individual users.'
services: active-directory
author: kenwith
manager: rkarlin
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 05/30/2022
ms.author: kenwith
ms.reviewer: arvinh
---

# Tutorial: Reporting on automatic user account provisioning

Azure Active Directory (Azure AD) includes a [user account provisioning service](user-provisioning.md) that helps automate the provisioning de-provisioning of user accounts in SaaS apps and other systems, for the purpose of end-to-end identity lifecycle management. Azure AD supports pre-integrated user provisioning connectors for all of the applications and systems with user provisioning tutorials [here](../saas-apps/tutorial-list.md).

This article describes how to check the status of provisioning jobs after they have been set up, and how to troubleshoot the provisioning of individual users and groups.

## Overview

Provisioning connectors are set up and configured using the [Azure portal](https://portal.azure.com), by following the [provided documentation](../saas-apps/tutorial-list.md) for the supported application. Once configured and running, provisioning jobs can be reported on using the following methods:

- The [Azure portal](https://portal.azure.com)

- Streaming the provisioning logs into [Azure Monitor](../app-provisioning/application-provisioning-log-analytics.md). This method allows for extended data retention and building custom dashboards, alerts, and queries.

- Querying the [Microsoft Graph API](/graph/api/resources/provisioningobjectsummary) for the provisioning logs.

- Downloading the provisioning logs as a CSV or JSON file.

### Definitions

This article uses the following terms, defined below:

* **Source System** - The repository of users that the Azure AD provisioning service synchronizes from. Azure Active Directory is the source system for the majority of pre-integrated provisioning connectors, however there are some exceptions (example: Workday Inbound Synchronization).
* **Target System** - The repository of users that the Azure AD provisioning service synchronizes to. This is typically a SaaS application (examples: Salesforce, ServiceNow, G Suite, Dropbox for Business), but in some cases can be an on-premises system such as Active Directory (example: Workday Inbound Synchronization to Active Directory).

## Getting provisioning reports from the Azure portal

To get provisioning report information for a given application, start by launching the [Azure portal](https://portal.azure.com) and **Azure Active Directory** &gt; **Enterprise Apps** &gt; **Provisioning logs** in the **Activity** section. You can also browse to the Enterprise Application for which provisioning is configured. For example, if you are provisioning users to LinkedIn Elevate, the navigation path to the application details is:

**Azure Active Directory > Enterprise Applications > All applications > LinkedIn Elevate**

From here, you can access both the provisioning progress bar and the provisioning logs, described below.

## Provisioning progress bar

The [provisioning progress bar](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) is visible in the **Provisioning** tab for a given application. It is located in the **Current Status** section and shows the status of the current initial or incremental cycle. This section also shows:

* The total number of users and/groups that have been synchronized and are currently in scope for provisioning between the source system and the target system.
* The last time the synchronization was run. Synchronizations typically occur every 20-40 minutes, after an [initial cycle](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) has completed.
* Whether or not an [initial cycle](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) has been completed.
* Whether or not the provisioning process has been placed in quarantine, and what the reason for the quarantine status is (for example, failure to communicate with target system due to invalid admin credentials).

The **Current Status** should be the first place admins look to check on the operational health of the provisioning job.

 ![Summary report](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## Provisioning logs 

All activities performed by the provisioning service are recorded in the Azure AD [provisioning logs](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). You can access the provisioning logs in the Azure portal by selecting **Azure Active Directory** &gt; **Enterprise Apps** &gt; **Provisioning logs ** in the **Activity** section. You can search the provisioning data based on the name of the user or the identifier in either the source system or the target system. For details, see [Provisioning logs](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 


## Troubleshooting

The provisioning summary report and provisioning logs play a key role helping admins troubleshoot various user account provisioning issues.

For scenario-based guidance on how to troubleshoot automatic user provisioning, see [Problems configuring and provisioning users to an application](../app-provisioning/application-provisioning-config-problem.md).

## Additional Resources

* [Managing user account provisioning for Enterprise Apps](configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
