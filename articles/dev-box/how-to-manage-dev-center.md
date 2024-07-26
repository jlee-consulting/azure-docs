---
title: Manage a Microsoft Dev Box dev center
titleSuffix: Microsoft Dev Box
description: Microsoft Dev Box dev centers help you manage dev box resources, grouping projects with similar settings. Learn how to create, delete, and manage dev centers.
services: dev-box
ms.service: dev-box
author: RoseHJM
ms.author: rosemalcolm
ms.date: 01/12/2024
ms.topic: how-to
#Customer intent: As a platform engineer, I want to be able to manage dev centers so that I can manage my Microsoft Dev Box implementation.
---

# Manage a Microsoft Dev Box dev center

In this article, you learn how to manage a dev center in Microsoft Dev Box by using the Azure portal.

Development teams vary in the way they function and can have different needs. A dev center helps you manage these scenarios by enabling you to group similar sets of projects together and apply similar settings.

## Permissions

To manage a dev center, you need the following permissions:

| Action | Permissions required |
|---|---|
| _Create or delete a dev center_ | Owner or Contributor permissions on an Azure subscription or a specific resource group. |
| _Manage a dev center_ | Owner or Contributor role, or specific Write permission to the dev center. |
| _Attach or remove a network connection_ | Network Contributor permissions on an existing network connection (Owner or Contributor). |

## Create a dev center

Your development teams' requirements change over time. You can create a new dev center in Microsoft Dev Box to support organizational changes like a new business requirement or a new regional center.

You can create as many or as few dev centers as you need, depending on how you organize and manage your development teams.

To create a dev center in the Azure portal: 

1. Sign in to the [Azure portal](https://portal.azure.com).

1. In the search box, enter **dev centers**. In the search results, select **Dev centers** from the **Services** list.

   :::image type="content" source="./media/how-to-manage-dev-center/search-dev-center.png" alt-text="Screenshot that shows the Azure portal with the search box and the result for dev centers." lightbox="./media/how-to-manage-dev-center/search-dev-center.png":::

1. On the **Dev centers** page, select **Create**.

   :::image type="content" source="./media/how-to-manage-dev-center/create-dev-center.png" alt-text="Screenshot that shows the Azure portal with the Create button on the page for dev centers." lightbox="./media/how-to-manage-dev-center/create-dev-center.png":::

1. On the **Create a dev center** pane, on the **Basics** tab, enter the following values:

   | Setting | Value |
   |---|---|
   | **Subscription** | Select the subscription in which you want to create the dev center. |
   | **ResourceGroup** | Select an existing resource group, or select **Create new** and then enter a name for the new resource group. |
   | **Name** | Enter a name for your dev center. |
   | **Location** | Select the location or region where you want the dev center to be created. |
   | **Attach a quick start catalog** | Clear both checkboxes. |

   :::image type="content" source="./media/how-to-manage-dev-center/create-dev-center-basics-not-selected.png" alt-text="Screenshot that shows the Basics tab on the pane for creating a dev center." lightbox="./media/how-to-manage-dev-center/create-dev-center-basics-not-selected.png":::

   For a list of the currently supported Azure locations with capacity, see [Frequently asked questions about Microsoft Dev Box](https://aka.ms/devbox_acom).

   The Dev Box quick start catalog contains tasks and scripts that you can use to configure your dev box during the final stage of the creation process. You can attach a quick start catalog to a dev center later. For more information, see [Create reusable dev box customizations](./how-to-customize-dev-box-setup-tasks.md).

1. (Optional) On the **Tags** tab, enter a name/value pair that you want to assign.

   :::image type="content" source="./media/how-to-manage-dev-center/create-dev-center-tags.png" alt-text="Screenshot that shows the Tags tab on the page for creating a dev center." lightbox="./media/how-to-manage-dev-center/create-dev-center-tags.png":::

1. Select **Review + Create**.

1. On the **Review** tab, select **Create**.

1. Monitor the progress of the dev center creation from any page in the Azure portal by opening the **Notifications** pane.

   :::image type="content" source="./media/how-to-manage-dev-center/azure-notifications.png" alt-text="Screenshot that shows the Notifications pane in the Azure portal." lightbox="./media/how-to-manage-dev-center/azure-notifications.png":::

1. When the deployment completes, select **Go to resource**. Confirm that the dev center page appears.

## Delete a dev center

You might choose to delete a dev center to reflect organizational or workload changes. Deleting a dev center in Microsoft Dev Box is irreversible, and you must prepare for the deletion carefully.

A dev center can't be deleted while any projects are associated with it. You must delete the projects before you can delete the dev center.

Attached network connections and their associated virtual networks aren't deleted when you delete a dev center.

When you're ready to delete your dev center, follow these steps:

1. Sign in to the [Azure portal](https://portal.azure.com).

1. In the search box, enter **dev centers**. In the search results, select **Dev centers** from the **Services** list.

1. On the **Dev centers** page, open the dev center that you want to delete.

1. Select **Delete**.

   :::image type="content" source="./media/how-to-manage-dev-center/delete-dev-center.png" alt-text="Screenshot of the Delete button on the page for a dev center." lightbox="./media/how-to-manage-dev-center/delete-dev-center.png":::

1. In the confirmation message, select **OK**.

[!INCLUDE [attach or remove a network connection](./includes/attach-remove-network-connections.md)]

## Assign permissions for users

You can assign multiple users permissions to a dev center to help with administrative tasks. You can assign users or groups to the following built-in roles:

- **Owner**: Grants full access to manage all resources, including the ability to assign roles in Azure role-based access control (RBAC).
- **Contributor**: Grants full access to manage all resources, but doesn't allow the user to assign roles in Azure RBAC, manage assignments in Azure Blueprints, or share image galleries.
- **Reader**: Grants the ability to view all resources, but doesn't allow the user to make any changes.

To make role assignments:

1. Sign in to the [Azure portal](https://portal.azure.com).

1. In the search box, enter **dev centers**. In the list of results, select **Dev centers**.

1. Select the dev center that you want to give access to.

1. On the left menu, select **Access Control (IAM)**.

1. Select **Add** > **Add role assignment**.

1. Assign a role by configuring the following settings. For detailed steps, see [Assign Azure roles using the Azure portal](../role-based-access-control/role-assignments-portal.yml).

    | Setting | Value |
    |---|---|
    | **Role** | Select **Owner**, **Contributor**, or **Reader**. |
    | **Assign access to** | Select **User, group, or service principal**. |
    | **Members** | Select the users or groups that you want to be able to access the dev center. |

## Related content

- [Provide access to projects for project admins](./how-to-project-admin.md)
- [Create a dev box definition](quickstart-configure-dev-box-service.md#create-a-dev-box-definition)
- [Configure Azure Compute Gallery](./how-to-configure-azure-compute-gallery.md)
