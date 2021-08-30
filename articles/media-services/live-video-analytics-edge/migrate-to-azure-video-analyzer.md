---
title: Migrate from Live Video Analytics to Azure Video Analyzer
description: Learn how to migrate from Live Video Analytics to Azure Video Analyzer.
ms.topic: how-to
ms.date: 08/16/2021

---
# How to migrate from Live Video Analytics to Azure Video Analyzer

This article should be used if you have deployed Live Video Analytics Edge Module to an IoT Edge device and you are currently running media graphs to process feeds from Real Time Streaming Protocol (RTSP) cameras.

If you are not using RTSP cameras and are using one of the [quickstarts or tutorials on Live Video Analytics](../live-video-analytics-edge/overview.md), you should simply switch to the corresponding [quickstart or tutorial with Azure Video Analyzer](../../azure-video-analyzer/video-analyzer-docs/overview.md).

The following instructions apply to migrating Live Video Analytics to Azure Video Analyzer for a single IoT Edge device. You'll need to repeat the steps for each separate IoT Edge device.

## Prerequisites

* An active Azure subscription.

   > [!NOTE]
   > You will need an Azure subscription where you have access to both the [Contributor](../../role-based-access-control/built-in-roles.md) role and the [User Access Administrator](../../role-based-access-control/built-in-roles.md) role to the resource group under which you will create new resources (user-assigned managed identity, storage account, Video Analyzer account). If you do not have the right permissions, ask your account administrator to grant you those permissions. 

* You will need administrative privileges to the IoT Edge device(s) on which you have installed Live Video Analytics.

## Create a backup of current settings

Before beginning any migration efforts, it is strongly recommended to save your edge deployment manifest and Live Video Analytics media graph configurations.

> [!NOTE]
> The steps listed below your cameras RTSP credentials and stream URL details are not able to be retrieved.  You will need to need to retrieve that information directly from the camera's management application.

### Save the current deployment manifest

1. Sign in to the Azure portal.

1. Go to the **IoT Hub** and select **IoT Edge** under **Automatic Device Management**.

1. Under **IoT Edge**, select the IoT Edge device that you want to migrate to Azure Video Analyzer.

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/iot-edge.png" alt-text="Screenshot that shows IoT Edge devices.":::

1. Select **Set modules**, and then select **Review + create**

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/set-modules.png" alt-text="Screenshot that shows the Deployment Manifest.":::

1. Copy the **Deployment** section in the window and save in a safe location.

1. Select the **X** in the top-right corner of the portal.  

    > [!NOTE]
    > *Do not* select **Create**.  Review the deployment manifest and locate the `lvaEdge` node under `modules`.  If you have an `env` node under the `lvaEdge` node, note the values of LOCAL_EDGE_USER and lOCAL_GROUP_ID, you will need these later in the document.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/deployment-manifest-env-node.png" alt-text="Screenshot that shows the deployment manifest and the env section for the local edge user and group.":::

### Save the Live Video Analytics topologies

1. To save your Live Video Analytics media graph, select the Live Video Analytics module under the IoT Edge device.  In the following example, the container name is *lvaEdge*.

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/live-video-analytics-edge.png" alt-text="Screenshot that shows the Live Video Analytics IoT Edge module container name.":::

1. Select **Direct method**.

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/direct-method.png" alt-text="Screenshot that shows the direct method calls for Live Video Analytics.":::

1. Enter **GraphTopologyList** in the Method name field and enter the following code in the payload:

    ```JSON
    {
        "@apiVersion" : "2.0"
    }
    ```

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/method-name.png" alt-text="Screenshot that shows the IoT Edge direct method call.":::

1. Select **Invoke Method**.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/invoke-method.png" alt-text="Screenshot that shows the invoke method.":::

1. The return will be shown in the results section.  This is your Live Video Analytics graph topology.  Copy the JSON and save it in a safe location.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/topology-result.png" alt-text="Screenshot that shows  the result of the GraphTopologyList method.":::

1. Repeat the process, but replace `GraphTopologyList` with `GraphInstanceList` and save the result section.  This is a list of your graph instances. Note the names of all active instances.

1.  After you have created a backup copy of your Live Video Analytics settings, you can then *deactivate all active media graphs* by entering **GraphInstanceDeactivate** in **Method name** and then entering the following code:

    ```json
    {
        "@apiVersion" : "2.0",
        "name" : "<Graph_Name>"
    }
    ```

    > [!NOTE]
    > Replace Graph_Name with the name of active instances in the new results.  Repeat this process until all active graphs are deactivated. 

## Azure Video Analyzer: Device preparation 

To start the migration, the IoT Edge device needs to be configured to run the Azure Video Analyzer module. The Azure Video Analyzer Module should run on an IoT Edge device with a non-privileged local user account. The module needs certain local folders for storing application configuration data. The RTSP camera simulator module needs video files with which it can synthesize a live video feed. 

1. Sign in to the IoT Edge device via SSH.

    > [!Note]
    > Based on the review of your IoT Edge device manifest file (step 6 under the section "Create a Backup of the Current Settings") you will either need to create a new user/group on the IoT Edge device or reuse the ones that were created under Live Video Analytics.

    If the IoT Edge device has been configured with a local user and group ID for Live Video Analytics (found in the deployment manifest) you can skip step 1, but note the user and group name and value it will be used later in this document.  If you are also using a unique folder structure for your Live Video Analytics deployment (found in the deployment manifest), replace *videoanalyzer* with those values and omit the first two lines in step 2.  

    The following example shows a unique folder structure for a Live Video Analytics deployment.  Note that the *applicationDataDirectory* is /var/lib/videofiles. For this example, Live Video Analytics has *localedgeuser* and *localedgegroup* created on the IoT Edge device.

    ```
    "lvaEdge": {
                "properties.desired": {
                    "applicationDataDirectory": "/var/lib/videofiles",
                    "azureMediaServicesArmId": "/subscriptions/resourceGroups/lvamigration/providers/microsoft.media/mediaservices/lvasamplejbbvaomtycjas",
                    "aadTenantId": "<guid>",
                    "aadServicePrincipalAppId": "<guid>",
                    "aadServicePrincipalSecret": "<guid>",
                    "aadEndpoint": "https://login.microsoftonline.com",
                    "aadResourceId": "https://management.core.windows.net/",
                    "armEndpoint": "https://management.azure.com/",
                    "diagnosticsEventsOutputName": "AmsDiagnostics",
                    "operationalEventsOutputName": "AmsOperational",
                    "logLevel": "Information",
                    "logCategories": "Application,Events",
                    "allowUnsecuredEndpoints": true,
                    "telemetryOptOut": false
                }
            },
    ```

    Then run the following code:

     ```
       sudo mkdir -p /var/lib/videofiles/tmp/ 
       sudo chown -R localedgeuser:localedgegroup /var/lib/videofiles/tmp/
       sudo mkdir -p /var/lib/videofiles/logs
       sudo chown -R localedgeuser:localedgegroup /var/lib/videofiles/logs
     ```

1. Create a local user account on the IoT Edge device by running the following code:

   ```bash
   sudo groupadd -g 1010 localedgegroup
   sudo useradd --home-dir /home/localedgeuser --uid 1010 --gid 1010 localedgeuser
   sudo mkdir -p /home/localedgeuser
   sudo chown -R localedgeuser:localedgegroup /home/localedgeuser/
   ```

1. Create the *avaedge* folder structure with the following code:

   ```bash
   sudo mkdir -p /var/lib/videoanalyzer 
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/
   sudo mkdir -p /var/lib/videoanalyzer/tmp/ 
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/tmp/
   sudo mkdir -p /var/lib/videoanalyzer/logs
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/logs
   ```

## Create Azure resources

The next step is to create the required Azure resources. The following instructions are documented in the [Create Azure resources section](../../azure-video-analyzer/video-analyzer-docs/get-started-detect-motion-emit-events-portal.md) and repeated here for convenience. 

You will now create the required Azure resources (Video Analyzer account, storage account, and user-assigned managed identity). 

When you create an Azure Video Analyzer account, you have to associate an Azure storage account with it. If you use Video Analyzer to record the live video from a camera, that data is stored as blobs in a container in the storage account. You must use a managed identity to grant the Video Analyzer account the appropriate access to the storage account. 

### Create a Video Analyzer account in the Azure portal

1. Sign in to the [Azure portal](https://portal.azure.com/). 

1. In the search box, enter **Video Analyzer**. 

1. Select **Video Analyzers** under **Services**. 

1. Select **Add**. 

1. In the **Create Video Analyzer account** section, enter these required values: 

   - **Subscription**: Choose the subscription that you're using to create the Video Analyzer account. 

   - **Resource group**: Choose a resource group where you're creating the Video Analyzer account, or select **Create new** to create a resource group. 

   - **Video Analyzer account name**: Enter a name for your Video Analyzer account. The name must be all lowercase letters or numbers with no spaces, and 3 to 24 characters in length. 

   - **Location**: Choose a location to deploy your Video Analyzer account (for example, **West US 2**). 

   - **Storage account**: Create a storage account. We recommend that you select a [standard general-purpose v2](../../storage/common/storage-account-overview.md#types-of-storage-accounts) storage account. 

      > [!NOTE]
      > If you have a storage account that is in the region that you are deploying the Azure Video Analyzer account to you can choose to use that storage account instead of creating a new one. 

   - **User identity**: Create and name a new user-assigned managed identity. 

1. At the bottom of the form, **check** the box next to: 
   “I certify that I have obtained all necessary rights and consents under applicable law to process media content and agree to be bound by all applicable” 

1. Select **Review + create** at the bottom of the form. 

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/video-analyzer-account.png" alt-text="Screenshot that shows how to create the Video Analyzer account in the Azure portal.":::

   > [!NOTE]
   > You will get these Azure resources added to your Resource Group.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/resources.png" alt-text="Screenshot that shows the resources that will also be added to your Azure resource group.":::

## Deploy the Azure Video Analyzer Edge module

### Generate Azure Video Analyzer Edge module token

1. Go to your Video Analyzer account.

1. Select **Edge Modules** in the **Edge** pane.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge-module.png" alt-text="Screenshot that shows Azure Video Analyzer account Edge module creation.":::

1. Select **Add edge modules**, enter **avaedge** as the name of the new edge module and select **Add**.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/add-edge-modules.png" alt-text="Screenshot that shows how to add edge modules.":::

1. The **Copy the provisioning token** pane will appear on the right. Under **Recommended desired properties for IoT module deployment**, copy the snippet.  You will need this in a later step.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/provisioning-token.png" alt-text="Screenshot that shows the provisioning token pane.":::

### Deploy the Azure Video Analyzer Edge module

1.  Go to your Azure IoT Hub.

1. Under **Automatic Device Management**, choose **Select IoT Edge**.

1. Select the Device ID value for your IT Edge device.

1. Select Set modules.

1. Select **Add** and then select **IoT Edge Module Marketplace** from the dropdown menu.

1. Enter Azure Video Analyzer in the search field.

1. Select Azure Video Analyzer.

1. In the **Set modules pane**, select **AzureVideoAnalyzerEdge**.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge.png" alt-text="Screenshot that shows the IoT Edge add module Azure Video Analyzer.":::

1. In the IoT Edge Module Name field, enter **avaedge**.
1. Select **Environmental Variables**. 

   > [!NOTE]
   > If your Live Video Analytics deployment was using a local user and group found in step 6 in the section "Create a backup of current settings" then use those values instead of LOCAL_USER_ID, LOCAL_GROUP_ID in the following step.

1. In the name field, enter **LOCAL_USER_ID** and in the Value field enter **1010**.  In the next name field, enter **LOCAL_GROUP_ID** and in the Value field enter **1010**.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/environment-variables.png" alt-text="Screenshot that shows Azure Video Analyzer environmental variables.":::

1. Select the **Container Create Options** and paste the following:

      ```json
      {
         "HostConfig": {
            "LogConfig": {
                  "Type": "",
                  "Config": {
                     "max-size": "10m",
                     "max-file": "10"
                  }
            },
            "Binds": [
                  "/var/media/:/var/media/",
                  "/var/lib/videoanalyzer/:/var/lib/videoanalyzer"
            ],
            "IpcMode": "host",
            "ShmSize": 1536870912
         }
      }
      ```

1. Select **Module Twin Settings** and paste the snippet that you copied earlier from the **Copy the provisioning token** page in the Video Analyzer account. 

      ```json
      {
         "applicationDataDirectory": "/var/lib/videoanalyzer",
         "ProvisioningToken": "<Provisioning Token Value>",
         "diagnosticsEventsOutputName": "diagnostics",
         "operationalEventsOutputName": "operational",
         "logLevel": "information",
         "LogCategories": "Application,Events",
         "allowUnsecuredEndpoints": true,
         "telemetryOptOut": false
      }
      ```

      > [!NOTE]
      > `<Provisioning Token Value>` is a placeholder for your provisioning token captured earlier in Generate Azure Video Analyzer Edge module token section step 4.

1. Select **Update**.

1. Select **Routes**.
1. Under **NAME**, enter **AVAToHub**. Under **VALUE**, enter __FROM /messages/modules/avaedge/outputs/* INTO $upstream__. 
1. Remove the route **LVAToHub** by selecting the trash can icon to the right of the route.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/delete-route.png" alt-text="Screenshot that shows how to delete the Live Video Analytics I O T Edge route.":::

1. Under **Modules**, locate your *lvaEdge* module and remove it by selecting the trash can icon on the right.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/delete-live-video-analytics-edge.png" alt-text="Screenshot that shows how to delete the Live Video Analytics IoT Edge module.":::

1. Select **Review + create**, and then select **Create** to deploy your **avaedge** edge module. 

### Verify your deployment

On the device details page, verify that the **avaedge**  module is listed as both **Specified in Deployment** and **Reported by Device**. 

It might take a few moments for the module to be started on the device and then reported back to IoT Hub. Refresh the page to see an updated status. Status code **200 -- OK** means that [the IoT Edge runtime](../../iot-edge/iot-edge-runtime.md) is healthy and is operating fine. 

:::image type="content" source="./media/migrate-to-azure-video-analyzer/status-200.png" alt-text="Screenshot that shows the IoT Edge runtime is reports a status of 200.":::

:::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge-running.png" alt-text="Screenshot that shows IoT Edge runtime is reporting all modules are running.":::

## Convert Media Graph topologies to Azure Video Analyzer pipelines

There are some differences between the Media Graph topologies that Live Video Analytics uses and the Pipeline topologies that Azure Video Analyzer uses.  Use the following table to compare the Live Video Analytics and Azure Video Analyzer topologies.

| Scenario                                                     | Media Graph (Live Video Analytics)                                            | Pipeline (Azure Video Analyzer)                                               |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Record an object of interest using multiple AI models         | [ai-composition](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/ai-composition/2.0/topology.json) | [ai-composition](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/ai-composition/topology.json) |
| Video and Audio                                              | [audio-video]()                                               | [audio-video]()                                               |
| continuous video recording                                   | [cvr-asset](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset) | [cvr-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cvr-video-sink) |
| Continuous video recording and inferencing using gRPC Extension | [cvr-with-grpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json) | [cvr-with-grpcExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-grpcExtension/topology.json) |
| Continuous video recording and inferencing using HTTP Extension | [cvr-with-httpExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-httpExtension/2.0/topology.json) | [cvr-with-httpExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-httpExtension/topology.json) |
| Continuous video recording with Motion Detection             | [cvr-with-motion](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-motion/2.0/topology.json) | [cvr-with-motion](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-motion/topology.json) |
| Event-based video recording to [Azure Video Analyzer Video / Assets] based on events from external AI | [evr-grpcExtension-assets](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-grpcExtension-assets) | [evr-grpcExtension-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-grpcExtension-video-sinkhttps://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-grpcExtension-video-sink) |
| Event-based video recording to [Azure Video Analyzer Video / Assets]  based on events from external AI | [evr-httpExtension-assets](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-httpExtension-assets) | [evr-httpExtension-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-httpExtension-video-sink) |
| Event-based video recording to [Assets / Video Sink] based on specific objects being detected by external inference engine | [evr-hubMessages-assets](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/2.0/topology.json) | [evr-hubMessages-video-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json) |
| Event-based recording of video to files based on messages sent via IoT Edge Hub | [evr-hubMessage-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/2.0/topology.json) | [evr-hubMessage-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-hubMessage-file-sink/topology.json) |
| Event-based video recording to local files based on motion events | [evr-motion-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/2.0/topology.json) | [evr-motion-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-motion-file-sink/topology.json) |
| Event-based video recording to [assets / video sink] and local files based on motion events | [evr-motion-assets-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets-files/2.0/topology.json) | [evr-motion-video-sink-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-motion-video-sink-file-sink/topology.json) |
| Analyzing live video using gRPC Extension to send images to the OpenVINO(TM) DL Streamer - Edge AI Extension module from Intel | [grpcExtensionOpenVINO](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json) | [grpcExtensionOpenVINO](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json) |
| Analyzing live video using HTTP Extension to send images to an external inference engine. | [httpExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json) | [httpExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtension/topology.json) |
| Analyzing live video using HTTP Extension to send images to the OpenVINO™ Model Server – AI Extension module from Intel | [httpExtensionOpenVINO](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtensionOpenVINO/2.0/topology.json) | [httpExtensionOpenVINO](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtensionOpenVINO/topology.json) |
| Analyzing Live Video with Computer Vision for Spatial Analysis | [lva-spatial-analysis](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/lva-spatial-analysis/2.0/topology.json) | [spatial-analysis](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/spatial-analysis) |

> [!NOTE]
> You can find and compare both [Live Video Analytics  Media Graph topologies](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies) and [Azure Video Analyzer Pipeline Topologies](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies) in our samples section on GitHub. By reviewing these samples, you will be able to update your existing Media Graph topologies to Azure Video Analyzer Pipeline topologies.

### Set and activate the pipeline topology

After you convert the Media Graph topology (Live Video Analytics) over to the Pipeline Topology (Azure Video Analyzer), you will need to set the Pipeline on the *avaedge* module.

1. To set your topology, select the Azure Video Analyzer Edge module under the IoT Edge device.  In the example below the container name is *avaedge*.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-container.png" alt-text="Screenshot that shows the Live Video Analytics IoT Edge module.":::

1. Select **Direct method**.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/direct-method.png" alt-text="Screenshot that shows direct method calls for Live Video Analytics.":::

1. Enter **pipelineTopologySet** in **Method name** and enter the converted pipeline JSON in the payload.

1. Select **Invoke Method**.

      In the return field, you should see "status": 201 followed by your topology.  This status indicates that a new topology was created.

1. Next we need to create the live pipeline by using the topology.  Use steps 1-3, however, in the Method name field enter **livePipelineSet** and in the payload field enter:

   ```JSON
         {
   "@apiVersion": "1.0",
   "name": "<live pipeline name>",
   "properties": {
      "topologyName": "<Name of the set topology>",
      "description": "Sample pipeline description",
      "parameters": [
         {
         "name": "rtspUrl",
         "value": "<RTSP camera string>"
         },
         {
         "name": "rtspUserName",
         "value": "<Camera User>"
         },
         {
         "name": "rtspPassword",
         "value": "Camera User Password"
         }
      ]
   }
   }
   ```

   > [!NOTE]
   > Replace `<live pipeline name>`, `<Name of the set topology>`, `<RTSP camera string>`, `<Camera User Password>` values with the ones for your enviroment. 

1. Select **Invoke Method**.
   
      In the return field, you should see "status": 201 followed by your live pipeline.  This status indicates that a new pipeline was created.

2. Finally, activate your live pipeline by following steps 1-3, however, in **Method name**, enter **livePipelineActivate** and in the payload field enter:

      ```JSON
         {
            "@apiVersion": "1.0",
            "name": "<live pipeline name>"
         }
      ```

    > [!NOTE]
    > Replace `<live pipeline name>` with the live pipeline name set above.

4. Select **Invoke Method**.
   
   In the return field, you should see "status": 200.  This status indicates that a new pipeline was activated.

## Direct methods

Both Live Video Analytics and Azure Video Analyzer use direct method calls.  As part of the migration also note that the direct methods have been updated, and requires review for any workflows that might use them in Live Video Analytics solutions.  The following table shows the differences between [Live Video Analytics direct method calls](direct-methods.md) and [Azure Video Analyzer direct method calls](../../azure-video-analyzer/video-analyzer-docs/direct-methods.md).

| Live Video Analytics | Azure Video Analyzer |
| ------------------------ | ------------------------ |
| GraphTopologyList        | pipelineTopologyList     |
| GraphTopologySet         | pipelineTopologySet      |
| GraphTopologyDelete      | pipelineTopologyDelete   |
| GraphInstanceList        | livePipelineList         |
| GraphInstanceSet         | livePipelineSet          |
| GraphInstanceActivate    | livePipelineActivate     |
| GraphInstanceDeactivate  | livePipelineDeactivate   |
| GraphInstanceDelete      | livePipelineDelete       |

## Miscellaneous changes 

- Appsettings.json: Update the appsettings.json file so that moduleId refers to your module. That is, set it to *avaeEdge*. 

- There is a minor change to the [gRPC contract](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc). While your existing gRPC extension should still work, it's advisable to update the prototype files. The change is a new line in inferencing.proto : string sequence_id=14.

## Next steps

- Try the [quickstart for recording videos to the cloud when motion is detected](../../azure-video-analyzer/video-analyzer-docs/detect-motion-record-video-clips-cloud.md). 
- Try the [quickstart for analyzing live video](../../azure-video-analyzer/video-analyzer-docs/analyze-live-video-use-your-model-http.md). 
- Review the [Azure Video Analyzer terminology](../../azure-video-analyzer/video-analyzer-docs/terminology.md). 
- Review [the Azure Video Analyzer pipeline](../../azure-video-analyzer/video-analyzer-docs/pipeline.md). 