---
title: Device Update for Azure RTOS | Microsoft Docs
description: Get started with Device Update for Azure RTOS.
author: ValOlson
ms.author: valls
ms.date: 3/18/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
---

# Tutorial: Device Update for Azure IoT Hub using Azure RTOS

This tutorial shows you how to create the Device Update for Azure IoT Hub agent in Azure RTOS NetX Duo. It also provides simple APIs for developers to integrate the Device Update capability in their application. Explore [samples](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) of key semiconductors evaluation boards that include the get-started guides to learn how to configure, build, and deploy over-the-air updates to the devices.

In this tutorial, you'll learn how to:
> [!div class="checklist"]
> * Get started.
> * Tag your device.
> * Create a device group.
> * Deploy an image update.
> * Monitor the update deployment.

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Prerequisites

* Access to an IoT Hub instance. We recommend that you use an S1 (Standard) tier or higher.
* A Device Update instance and account linked to your IoT hub. Follow the guide to [create and link](./create-device-update-account.md) a device update account if you haven't done so previously.

## Get started

Each board-specific sample Azure real-time operating system (RTOS) project contains code and documentation on how to use Device Update for IoT Hub on it. You will:

1. Download the board-specific sample files from [Azure RTOS and Device Update samples](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU).
1. Find the docs folder from the downloaded sample.
1. From the docs, follow the steps for how to prepare Azure resources and an account and register IoT devices to it.
1. Follow the docs to build a new firmware image and import manifest for your board.
1. Publish the firmware image and manifest to Device Update for IoT Hub.
1. Download and run the project on your device.

Learn more about [Azure RTOS](/azure/rtos/).

## Tag your device

1. Keep the device application running from the previous step.
1. Sign in to the [Azure portal](https://portal.azure.com) and go to the IoT hub.
1. On the left pane, under **IoT Devices**, find your IoT device and go to the device twin.
1. In the device twin, delete any existing Device Update tag values by setting them to null.
1. Add a new Device Update tag value to the root JSON object, as shown:
    
    ```JSON
        "tags": {
                "ADUGroup": "<CustomTagValue>"
                }
    ```

## Create an update group

1. Go to the **Groups and Deployments** tab at the top of the page.

   :::image type="content" source="media/create-update-group/ungrouped-devices.png" alt-text="Screenshot that shows ungrouped devices." lightbox="media/create-update-group/ungrouped-devices.png":::

1. Select **Add group** to create a new group.

   :::image type="content" source="media/create-update-group/add-group.png" alt-text="Screenshot that shows a device group addition." lightbox="media/create-update-group/add-group.png":::

1. Select an **IoT Hub** tag and **Device class** from the list. Then select **Create group**.

   :::image type="content" source="media/create-update-group/select-tag.png" alt-text="Screenshot that shows tag selection." lightbox="media/create-update-group/select-tag.png":::

1. After the group is created, you see that the update compliance chart and groups list are updated. The update compliance chart shows the count of devices in various states of compliance: **On latest update**, **New updates available**, and **Updates in progress**. [Learn about update compliance](device-update-compliance.md).

   :::image type="content" source="media/create-update-group/updated-view.png" alt-text="Screenshot that shows the update compliance view." lightbox="media/create-update-group/updated-view.png":::

1. You should see your newly created group and any available updates for the devices in the new group. If there are devices that don't meet the device class requirements of the group, they show up in a corresponding invalid group. To deploy the best available update to the new user-defined group from this view, select **Deploy** next to the group.

[Learn more](create-update-group.md) about how to add tags and create update groups.

## Deploy new firmware

1. After the group is created, you should see a new update available for your device group with a link to the update under **Best update**. You might need to refresh once. [Learn more about update compliance](device-update-compliance.md).

1. Select the target group by selecting the group name. You're directed to the group details under **Group basics**.

   :::image type="content" source="media/deploy-update/group-basics.png" alt-text="Screenshot that shows Group details." lightbox="media/deploy-update/group-basics.png":::

1. To start the deployment, go to the **Current deployment** tab. Select the deploy link next to the desired update from the **Available updates** section. The best available update for a given group is denoted with a **Best** highlight.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Screenshot that shows selecting an update." lightbox="media/deploy-update/select-update.png":::

1. Schedule your deployment to start immediately or in the future. Then select **Create**.

   > [!TIP]
   > By default, the **Start** date and time is 24 hours from your current time. Be sure to select a different date and time if you want the deployment to begin earlier.

   :::image type="content" source="media/deploy-update/create-deployment.png" alt-text="Screenshot that shows the Create deployment screen." lightbox="media/deploy-update/create-deployment.png":::

1. Under **Deployment details**, **Status** turns to **Active**. The deployed update is marked with **(deploying)**.

    :::image type="content" source="media/deploy-update/deployment-active.png" alt-text="Screenshot that shows deployment as Active." lightbox="media/deploy-update/deployment-active.png":::

1. View the compliance chart to see that the update is now in progress.

1. After your device is successfully updated, you see that your compliance chart and deployment details updated to reflect the same.

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Screenshot that shows the update succeeded." lightbox="media/deploy-update/update-succeeded.png":::

## Monitor the update deployment

1. Select the **Deployment history** tab at the top of the page.

   :::image type="content" source="media/deploy-update/deployments-history.png" alt-text="Screenshot that shows the deployment history." lightbox="media/deploy-update/deployments-history.png":::

1. Select **Details** next to the deployment you created.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Screenshot that shows deployment details." lightbox="media/deploy-update/deployment-details.png":::

1. Select **Refresh** to view the latest status details.

You've now completed a successful end-to-end image update by using Device Update for IoT Hub on an Azure RTOS embedded device.

## Clean up resources

When no longer needed, clean up your device update account, instance, IoT hub, and IoT device.

## Next steps

To learn more about Azure RTOS and how it works with IoT Hub, see the [Azure RTOS webpage](https://azure.com/rtos).
