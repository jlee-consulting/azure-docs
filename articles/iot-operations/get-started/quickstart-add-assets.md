---
title: "Quickstart: Add assets"
description: "Quickstart: Add OPC UA assets that publish messages to the Azure IoT MQ broker in your Azure IoT Operations cluster."
author: dominicbetts
ms.author: dobett
ms.topic: quickstart
ms.custom:
  - ignite-2023
ms.date: 02/19/2024

#CustomerIntent: As an OT user, I want to create assets in Azure IoT Operations so that I can subscribe to asset data points, and then process the data before I send it to the cloud.
---

# Quickstart: Add OPC UA assets to your Azure IoT Operations Preview cluster

[!INCLUDE [public-preview-note](../includes/public-preview-note.md)]

In this quickstart, you manually add OPC UA assets to your Azure IoT Operations Preview cluster. These assets publish messages to the Azure IoT MQ Preview broker in your Azure IoT Operations cluster. Typically, an OT user completes these steps.

An _asset_ is a physical device or logical entity that represents a device, a machine, a system, or a process. For example, a physical asset could be a pump, a motor, a tank, or a production line. A logical asset that you define can have properties, stream telemetry, or generate events.

_OPC UA servers_ are software applications that communicate with assets. _OPC UA tags_ are data points that OPC UA servers expose. OPC UA tags can provide real-time or historical data about the status, performance, quality, or condition of assets.

In this quickstart, you use the Azure IoT Operations (preview) portal to create your assets. You can also use the [Azure CLI to complete some of these tasks](/cli/azure/iot/ops/asset).

## Prerequisites

Complete [Quickstart: Deploy Azure IoT Operations Preview to an Arc-enabled Kubernetes cluster](quickstart-deploy.md) before you begin this quickstart.

To sign in to the Azure IoT Operations (preview) portal, you need a work or school account in the tenant where you deployed Azure IoT Operations. If you're currently using a Microsoft account (MSA), you need to create a Microsoft Entra ID with at least contributor permissions for the resource group that contains your **Kubernetes - Azure Arc** instance. To learn more, see [Known Issues > Create Entra account](../troubleshoot/known-issues.md#azure-iot-operations-preview-portal).

## What problem will we solve?

The data that OPC UA servers expose can have a complex structure and can be difficult to understand. Azure IoT Operations provides a way to model OPC UA assets as tags, events, and properties. This modeling makes it easier to understand the data and to use it in downstream processes such as the MQ broker and Azure IoT Data Processor Preview pipelines.

## Sign into the Azure IoT Operations (preview) portal

To create asset endpoints, assets and subscribe to OPC UA tags and events, use the Azure IoT Operations (preview) portal. Navigate to the [Azure IoT Operations (preview)](https://iotoperations.azure.com) portal in your browser and sign in with your Microsoft Entra ID credentials.

> [!IMPORTANT]
> You must use a work or school account to sign in to the Azure IoT Operations (preview) portal. To learn more, see [Known Issues > Create Entra account](../troubleshoot/known-issues.md#azure-iot-operations-preview-portal).

## Select your site

After you sign in, the portal displays a list of sites that you have access to. Each site is a collection of Azure IoT Operations instances where you can configure your assets. Your [IT administrator is responsible for organizing instances in to sites](../../azure-arc/site-manager/overview.md) and granting access to OT users in your organization. Because you're working with a new deployment, there are no sites yet. You can find the cluster you created in the previous quickstart by selecting **Unassigned instances**. In the portal, an instance represents a cluster where you deployed Azure IoT Operations.

:::image type="content" source="media/quickstart-add-assets/site-list.png" alt-text="Screenshot that shows the unassigned instances node in the Azure IoT Operations (preview) portal.":::

## Select your instance

Select the instance where you deployed Azure IoT Operations in the previous quickstart:

:::image type="content" source="media/quickstart-add-assets/cluster-list.png" alt-text="Screenshot of Azure IoT Operations instance list.":::

> [!TIP]
> If you don't see any instances, you might not be in the right Microsoft Entra ID tenant. You can change the tenant from the top right menu in the portal.

## Add an asset endpoint

When you deployed Azure IoT Operations, you chose to include a built-in OPC PLC simulator. In this step, you add an asset endpoint that enables you to connect to the OPC PLC simulator.

To add an asset endpoint:

1. Select **Asset endpoints** and then **Create asset endpoint**:

    :::image type="content" source="media/quickstart-add-assets/asset-endpoints.png" alt-text="Screenshot that shows the asset endpoints page in the Azure IoT Operations (preview) portal.":::

1. Enter the following endpoint information:

    | Field | Value |
    | --- | --- |
    | Asset endpoint name | `opc-ua-connector-0` |
    | OPC UA server URL | `opc.tcp://opcplc-000000:50000` |
    | User authentication mode | `Anonymous` |
    | Transport authentication | `Do not use transport authentication certificate` |

1. To save the definition, select **Create**.

    This configuration deploys a new asset endpoint called `opc-ua-connector-0` to the cluster. You can use `kubectl` to view the asset endpoints:

    ```console
    kubectl get assetendpointprofile -n azure-iot-operations
    ```

## Configure the simulator

These quickstarts use the **OPC PLC simulator** to generate sample data. To enable the quickstart scenario, you need to configure your asset endpoint to connect without mutual trust established. This configuration is not recommended for production or pre-production environments:

1. To configure the asset endpoint for the quickstart scenario, run the following command:

    ```console
    kubectl patch AssetEndpointProfile opc-ua-connector-0 -n azure-iot-operations --type=merge -p '{"spec":{"additionalConfiguration":"{\"applicationName\":\"opc-ua-connector-0\",\"security\":{\"autoAcceptUntrustedServerCertificates\":true}}"}}'
    ```

    > [!CAUTION]
    > Don't use this configuration in production or pre-production environments. Exposing your cluster to the internet without proper authentication might lead to unauthorized access and even DDOS attacks.

    To learn more, see [Deploy the OPC PLC simulator](../manage-devices-assets/howto-configure-opc-plc-simulator.md) section.

1. To enable the configuration changes to take effect immediately, first find the name of your `aio-opc-supervisor` pod by using the following command:

    ```console
    kubectl get pods -n azure-iot-operations
    ```

    The name of your pod looks like `aio-opc-supervisor-956fbb649-k9ppr`.

1. Restart the `aio-opc-supervisor` pod by using a command that looks like the following example. Use the `aio-opc-supervisor` pod name from the previous step:

    ```console
    kubectl delete pod aio-opc-supervisor-956fbb649-k9ppr -n azure-iot-operations
    ```

After you define an asset, an OPC UA connector pod discovers it. The pod uses the asset endpoint that you specify in the asset definition to connect to an OPC UA server. You can use `kubectl` to view the discovery pod that was created when you added the asset endpoint. The pod name looks like `aio-opc-opc.tcp-1-8f96f76-kvdbt`:

```console
kubectl get pods -n azure-iot-operations
```

When the OPC PLC simulator is running, data flows from the simulator, to the connector, to the OPC UA broker, and finally to the MQ broker.

## Manage your assets

After you select your instance in Azure IoT Operations (preview) portal, you see the available list of assets on the **Assets** page. If there are no assets yet, this list is empty:

:::image type="content" source="media/quickstart-add-assets/create-asset-empty.png" alt-text="Screenshot of Azure IoT Operations empty asset list.":::

### Create an asset

To create an asset, select **Create asset**. Then enter the following asset information:

| Field | Value |
| --- | --- |
| Asset name | `thermostat` |
| Asset Endpoint | `opc-ua-connector-0` |
| Description | `A simulated thermostat asset` |

Remove the existing **Custom properties** and add the following custom properties. Be careful to use the exact property names, as the Power BI template in a later quickstart queries for them:

| Property name | Property detail |
|---------------|-----------------|
| batch         | 102             |
| customer      | Contoso         |
| equipment     | Boiler          |
| isSpare       | true            |
| location      | Seattle         |

:::image type="content" source="media/quickstart-add-assets/create-asset-details.png" alt-text="Screenshot of Azure IoT Operations asset details page.":::

Select **Next** to go to the **Add tags** page.

### Create OPC UA tags

Add two OPC UA tags on the **Add tags** page. To add each tag, select **Add tag or CSV** and then select **Add tag**. Enter the tag details shown in the following table:

| Node ID            | Tag name    | Observability mode |
| ------------------ | ----------- | ------------------ |
| ns=3;s=FastUInt10  | temperature | none               |
| ns=3;s=FastUInt100 | Tag 10      | none               |

The **Observability mode** is one of the following values: `none`, `gauge`, `counter`, `histogram`, or `log`.

You can override the default sampling interval and queue size for each tag.

:::image type="content" source="media/quickstart-add-assets/add-tag.png" alt-text="Screenshot of Azure IoT Operations add tag page.":::

Select **Next** to go to the **Add events** page and then **Next** to go to the **Review** page.

### Review

Review your asset and tag details and make any adjustments you need before you select **Create**:

:::image type="content" source="media/quickstart-add-assets/review-asset.png" alt-text="Screenshot of Azure IoT Operations create asset review page.":::

## Verify data is flowing

[!INCLUDE [deploy-mqttui](../includes/deploy-mqttui.md)]

To verify that the thermostat asset you added is publishing data, view the telemetry in the `azure-iot-operations/data` topic:

:::image type="content" source="media/quickstart-add-assets/mqttui-output.png" alt-text="Screenshot of the mqttui topic display showing the temperature telemetry." lightbox="media/quickstart-add-assets/mqttui-output.png":::

> [!TIP]
> Data from an asset with a name that starts with _boiler-_ is from an asset that was automatically discovered. This is not the same asset as the thermostat asset you created.

If there's no data flowing, restart the `aio-opc-opc.tcp-1` pod:

1. Find the name of your `aio-opc-opc.tcp-1` pod by using the following command:

    ```console
    kubectl get pods -n azure-iot-operations
    ```

    The name of your pod looks like `aio-opc-opc.tcp-1-849dd78866-vhmz6`.

1. Restart the `aio-opc-opc.tcp-1` pod by using a command that looks like the following example. Use the `aio-opc-opc.tcp-1` pod name from the previous step:

    ```console
    kubectl delete pod aio-opc-opc.tcp-1-849dd78866-vhmz6 -n azure-iot-operations
    ```

The sample tags you added in the previous quickstart generate messages from your asset that look like the following examples:

```json
{
    "Timestamp": "2024-03-08T00:54:58.6572007Z",
    "MessageType": "ua-deltaframe",
    "payload": {
      "temperature": {
        "SourceTimestamp": "2023-08-10T00:54:58.2543129Z",
        "Value": 7109
      },
      "Tag 10": {
        "SourceTimestamp": "2023-08-10T00:54:58.2543482Z",
        "Value": 7109
      }
    },
    "DataSetWriterName": "thermostat",
    "SequenceNumber": 4660
}
```

## Discover OPC UA data sources by using Azure IoT Akri Preview

In the previous section, you saw how to add assets manually. You can also use Azure IoT Akri Preview to automatically discover OPC UA data sources and create Akri instance custom resources that represent the discovered devices. Currently, Akri can't detect and create assets that can be ingested into the Azure Device Registry Preview.

When you deploy Azure IoT Operations, the deployment includes the Akri discovery handler pods. To verify these pods are running, run the following command:

```console
kubectl get pods -n azure-iot-operations | grep akri
```

The output from the previous command looks like the following example:

```output
aio-akri-otel-collector-5c775f745b-g97qv       1/1     Running   3 (4h15m ago)    2d23h
aio-akri-agent-daemonset-mp6v7                 1/1     Running   3 (4h15m ago)    2d23h
```

Use the following command to verify that the discovery pod is running:

```console
kubectl get pods -n azure-iot-operations | grep discovery
```

The output from the previous command looks like the following example:

```output
aio-opc-asset-discovery-wzlnj                   1/1     Running     0              19m
```

> [!TIP]
> There's currently a known issue where the Akri discovery handler pod might not start. If you encounter this issue, see [Troubleshoot Azure IoT Akri Preview](../troubleshoot/known-issues.md#azure-iot-akri-preview).

To verify the configuration, run the following command to view the Akri instances that represent the OPC UA data sources discovered by Akri:

```console
kubectl get akrii -n azure-iot-operations
```

The output from the previous command looks like the following example.

```output
NAME                      CONFIG             SHARED   NODES                          AGE
akri-opcua-asset-dbdef0   akri-opcua-asset   true     ["k3d-k3s-default-server-0"]   45s
```

Now you can use these resources in the local cluster namespace.

To confirm that Akri connected to the OPC UA Broker, copy and paste the name of the Akri instance from the previous step into the following command:

```console
kubectl get akrii <AKRI_INSTANCE_NAME> -n azure-iot-operations -o json
```

The command output looks like the following example. This example excerpt from the output shows the Akri instance `brokerProperties` values and confirms that the OPC UA Broker is connected.

```json
"spec": {

        "brokerProperties": {
            "ApplicationUri": "Boiler #2",
            "AssetEndpointProfile": "{\"spec\":{\"uuid\":\"opc-ua-broker-opcplc-000000-azure-iot-operation\"……
```

## How did we solve the problem?

In this quickstart, you added an asset endpoint and then defined an asset and tags. The assets and tags model data from the OPC UA server to make the data easier to use in an MQTT broker and other downstream processes. You use the thermostat asset you defined in the next quickstart.

## Clean up resources

If you won't use this deployment further, delete the Kubernetes cluster where you deployed Azure IoT Operations and remove the Azure resource group that contains the cluster.

## Next step

[Quickstart: Send asset telemetry to the cloud using the data lake connector for Azure IoT MQ](quickstart-upload-telemetry-to-cloud.md).
