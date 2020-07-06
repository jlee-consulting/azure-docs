---
title: 'Quickstart: Configure properties for an application in your Azure Active Directory (Azure AD) tenant'
description: This quickstart uses the Azure portal to configure an application that has been registered with your Azure Active Directory (Azure AD) tenant.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
---

# Quickstart: Configure properties for an application in your Azure Active Directory (Azure AD) tenant

In the previous quickstart, you added an application to your Azure AD tenant. When you add an application, you are letting your Azure AD tenant know it is the identity provider for the app. Now you'll configure some of the properties for the app.
 
## Prerequisites

To configure the properties of an application in your Azure AD tenant, you need:

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- One of the following roles: Global Administrator, Cloud Application Administrator, Application Administrator, or owner of the service principal.
- (Optional: Completion of [View your apps](view-applications-portal.md)).
- (Optional: Completion of [Add an app](add-application-portal.md)).

>[!IMPORTANT]
>We recommend using a non-production environment to test the steps in this quickstart.

## Configure app properties

When you finish adding an application to your Azure AD tenant, you are immediately presented with the overview page for it. If you are configuring an application that has already been added, then look at the first quickstart, it walks you through viewing the applications added to your tenant. 

To edit the application properties:

1. In the Azure AD portal, select **Enterprise applications** and then find and select the application you want to configure.
2. In the Manage section, select **Properties** to open the properties pane for editing.
    ![Shows the Properties screen and editable app properties](media/add-application-portal/edit-properties.png)
3. Take a moment to understand the options available to configure.
    - **Enabled for users to sign in?** determines whether users assigned to the application can sign in.
    - **User assignment required?** determines whether users who aren't assigned to the application can sign in.
    - **Visible to users?** determines whether users assigned to an app can see it in the access panel (https://myapps.microsoft.com) and O365 app launcher (the waffle menu in the top left of an Office 365 or Microsoft 365 website).
4. Use the following tables to help you choose the best options for your needs.

   - Behavior for **assigned** users:

       | Application property | Application property | Application property | Assigned-user experience | Assigned-user experience |
       |---|---|---|---|---|
       | Enabled for users to sign in? | User assignment required? | Visible to users? | Can assigned users sign in? | Can assigned users see the application?* |
       | yes | yes | yes | yes | yes  |
       | yes | yes | no  | yes | no   |
       | yes | no  | yes | yes | yes  |
       | yes | no  | no  | yes | no   |
       | no  | yes | yes | no  | no   |
       | no  | yes | no  | no  | no   |
       | no  | no  | yes | no  | no   |
       | no  | no  | no  | no  | no   |

   - Behavior for **unassigned** users:

       | Application property | Application property | Application property | Unassigned-user experience | Unassigned-user experience |
       |---|---|---|---|---|
       | Enabled for users to sign in? | User assignment required? | Visible to users? | Can unassigned users sign in? | Can unassigned users see the application?* |
       | yes | yes | yes | no  | no   |
       | yes | yes | no  | no  | no   |
       | yes | no  | yes | yes | no   |
       | yes | no  | no  | yes | no   |
       | no  | yes | yes | no  | no   |
       | no  | yes | no  | no  | no   |
       | no  | no  | yes | no  | no   |
       | no  | no  | no  | no  | no   |

     *Can the user see the application in the access panel and the Office 365 app launcher?

## Use a custom logo

To use a custom logo:

1. Create a logo that is 215 by 215 pixels, and save it in PNG format.
2. In the Azure AD portal, select **Enterprise applications** and then find and select the application you want to configure.
3. In the Manage section, select **Properties** to open the properties pane for editing. 
4. Select the icon to upload the logo.
5. When you're finished, select **Save**. 
    ![Shows how to change the logo from the app's Properties page](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > The thumbnail displayed on this **Properties** pane doesn't update right away. You can close and reopen the properties to see the updated icon.

## Next steps

Now that you've configured the properties of an application you can continue on to set up single sign-on.

- [Set up single sign-on](add-application-portal-setup-sso.md)
- [Delete an app](delete-application-portal.md)