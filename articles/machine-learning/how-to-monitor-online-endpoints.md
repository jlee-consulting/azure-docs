---
title: Monitor online endpoints
titleSuffix: Azure Machine Learning
description: Monitor online endpoints and create alerts with Application Insights.
services: machine-learning
ms.service: machine-learning
ms.reviewer: None
author: msakande
ms.author: mopeakande
ms.subservice: mlops
ms.date: 10/24/2023
ms.topic: conceptual
ms.custom: how-to, devplatv2
---

# Monitor online endpoints

Azure Machine Learning uses integration with Azure Monitor to track and monitor metrics and logs for [online endpoints](concept-endpoints.md). You can view metrics in charts, compare between endpoints and deployments, pin to Azure portal dashboards, configure alerts, query from log tables, and push logs to supported targets. You can also use Application Insights to analyze events from user containers.

* **Metrics**: For endpoint-level metrics such as request latency, requests per minute, new connections per second, and network bytes, you can drill down to see details at the deployment level or status level. Deployment-level metrics such as CPU/GPU utilization and memory or disk utilization can also be drilled down to instance level. Azure Monitor allows tracking these metrics in charts and setting up dashboards and alerts for further analysis.

* **Logs**: You can send metrics to the Log Analytics workspace where you can query the logs using Kusto query syntax. You can also send metrics to Azure Storage accounts and/or Event Hubs for further processing. In addition, you can use dedicated log tables for online endpoint related events, traffic, and console (container) logs. Kusto query allows complex analysis and joining of multiple tables.

* **Application insights**: Curated environments include integration with Application Insights, and you can enable or disable this integration when you create an online deployment. Built-in metrics and logs are sent to Application Insights, and you can use the built-in features of Application Insights (such as Live metrics, Transaction search, Failures, and Performance) for further analysis. 

In this article you learn how to:

> [!div class="checklist"]
> * Choose the right method to view and track metrics and logs
> * View metrics for your online endpoint
> * Create a dashboard for your metrics
> * Create a metric alert
> * View logs for your online endpoint
> * Use Application Insights to track metrics and logs 

## Prerequisites

- Deploy an Azure Machine Learning online endpoint.
- You must have at least [Reader access](../role-based-access-control/role-assignments-portal.yml) on the endpoint.

## Metrics

You can view metrics pages for online endpoints or deployments in the Azure portal. An easy way to access these metrics pages is through links available in the Azure Machine Learning studio user interface—specifically in the **Details** tab of an endpoint's page. Following these links will take you to the exact metrics page in the Azure portal for the endpoint or deployment. Alternatively, you can also go into the Azure portal to search for the metrics page for the endpoint or deployment.

To access the metrics pages through links available in the studio:

1. Go to the [Azure Machine Learning studio](https://ml.azure.com).
1. In the left navigation bar, select the **Endpoints** page.
1. Select an endpoint by clicking its name.
1. Select **View metrics** in the **Attributes** section of the endpoint to open up the endpoint's metrics page in the Azure portal.
1. Select **View metrics** in the section for each available deployment to open up the deployment's metrics page in the Azure portal.

    :::image type="content" source="media/how-to-monitor-online-endpoints/online-endpoints-access-metrics-from-studio.png" alt-text="A screenshot showing how to access the metrics of an endpoint and deployment from the studio UI." lightbox="media/how-to-monitor-online-endpoints/online-endpoints-access-metrics-from-studio.png":::

To access metrics directly from the Azure portal:

1. Sign in to the [Azure portal](https://portal.azure.com).
1. Navigate to the online endpoint or deployment resource.

    Online endpoints and deployments are Azure Resource Manager (ARM) resources that can be found by going to their owning resource group. Look for the resource types **Machine Learning online endpoint** and **Machine Learning online deployment**.

1. In the left-hand column, select **Metrics**.

### Available metrics

Depending on the resource that you select, the metrics that you see will be different. Metrics are scoped differently for online endpoints and online deployments.

#### Metrics at endpoint scope

[!INCLUDE [Microsoft.MachineLearningServices/workspaces](~/reusable-content/ce-skilling/azure/includes/azure-monitor/reference/metrics/microsoft-machinelearningservices-workspaces-onlineendpoints-metrics-include.md)]

**Bandwidth throttling**

Bandwidth will be throttled if the quota limits are exceeded for _managed_ online endpoints. For more information on limits, see the article on [limits for online endpoints](how-to-manage-quotas.md#azure-machine-learning-online-endpoints-and-batch-endpoints). To determine if requests are throttled:
- Monitor the "Network bytes" metric
- The response trailers will have the fields: `ms-azureml-bandwidth-request-delay-ms` and `ms-azureml-bandwidth-response-delay-ms`. The values of the fields are the delays, in milliseconds, of the bandwidth throttling.

For more information, see [Bandwidth limit issues](how-to-troubleshoot-online-endpoints.md#bandwidth-limit-issues).

#### Metrics at deployment scope

[!INCLUDE [Microsoft.MachineLearningServices/workspaces/onlineEndpoints/deployments](~/reusable-content/ce-skilling/azure/includes/azure-monitor/reference/metrics/microsoft-machinelearningservices-workspaces-onlineendpoints-deployments-metrics-include.md)]

### Create dashboards and alerts

Azure Monitor allows you to create dashboards and alerts, based on metrics.

#### Create dashboards and visualize queries

You can create custom dashboards and visualize metrics from multiple sources in the Azure portal, including the metrics for your online endpoint. For more information on creating dashboards and visualizing queries, see [Dashboards using log data](../azure-monitor/visualize/tutorial-logs-dashboards.md) and [Dashboards using application data](../azure-monitor/app/overview-dashboard.md#create-custom-kpi-dashboards-using-application-insights).
    
#### Create alerts

You can also create custom alerts to notify you of important status updates to your online endpoint:

1. At the top right of the metrics page, select **New alert rule**.

    :::image type="content" source="./media/how-to-monitor-online-endpoints/online-endpoints-new-alert-rule.png" alt-text="Screenshot showing 'New alert rule' button surrounded by a red box."  lightbox="./media/how-to-monitor-online-endpoints/online-endpoints-new-alert-rule.png" :::

1. Select a condition name to specify when your alert should be triggered.

    :::image type="content" source="./media/how-to-monitor-online-endpoints/online-endpoints-configure-signal-logic.png" alt-text="Screenshot showing 'Configure signal logic' button surrounded by a red box."  lightbox="./media/how-to-monitor-online-endpoints/online-endpoints-configure-signal-logic.png" :::

1. Select **Add action groups** > **Create action groups** to specify what should happen when your alert is triggered.

1. Choose **Create alert rule** to finish creating your alert.

For more information, see [Create Azure Monitor alert rules](../azure-monitor/alerts/alerts-create-new-alert-rule.md).

### Enable autoscale based on metrics

You can enable autoscale of deployments using metrics using UI or code. When you use code (either CLI or SDK), you can use Metrics IDs listed in the table of [available metrics](#available-metrics) in condition for triggering autoscaling. For more information, see [Autoscaling online endpoints](how-to-autoscale-endpoints.md).

## Logs

There are three logs that can be enabled for online endpoints:

* **AmlOnlineEndpointTrafficLog**: You could choose to enable traffic logs if you want to check the information of your request. Below are some cases: 

    * If the response isn't 200, check the value of the column "ResponseCodeReason" to see what happened. Also check the reason in the "HTTPS status codes" section of the [Troubleshoot online endpoints](how-to-troubleshoot-online-endpoints.md#http-status-codes) article.

    * You could check the response code and response reason of your model from the column "ModelStatusCode" and "ModelStatusReason". 

    * You want to check the duration of the request like total duration, the request/response duration, and the delay caused by the network throttling. You could check it from the logs to see the breakdown latency. 

    * If you want to check how many requests or failed requests recently. You could also enable the logs. 

* **AmlOnlineEndpointConsoleLog**: Contains logs that the containers output to the console. Below are some cases: 

    * If the container fails to start, the console log can be useful for debugging. 

    * Monitor container behavior and make sure that all requests are correctly handled. 

    * Write request IDs in the console log. Joining the request ID, the AmlOnlineEndpointConsoleLog, and AmlOnlineEndpointTrafficLog in the Log Analytics workspace, you can trace a request from the network entry point of an online endpoint to the container.  

    * You can also use this log for performance analysis in determining the time required by the model to process each request. 

* **AmlOnlineEndpointEventLog**: Contains event information regarding the container's life cycle. Currently, we provide information on the following types of events: 

    | Name | Message |
    | ----- | ----- | 
    | BackOff | Back-off restarting failed container 
    | Pulled | Container image "\<IMAGE\_NAME\>" already present on machine 
    | Killing | Container inference-server failed liveness probe, will be restarted 
    | Created | Created container image-fetcher 
    | Created | Created container inference-server 
    | Created | Created container model-mount 
    | LivenessProbeFailed | Liveness probe failed: \<FAILURE\_CONTENT\> 
    | ReadinessProbeFailed | Readiness probe failed: \<FAILURE\_CONTENT\> 
    | Started | Started container image-fetcher 
    | Started | Started container inference-server 
    | Started | Started container model-mount 
    | Killing | Stopping container inference-server 
    | Killing | Stopping container model-mount 

### How to enable/disable logs

> [!IMPORTANT]
> Logging uses Azure Log Analytics. If you do not currently have a Log Analytics workspace, you can create one using the steps in [Create a Log Analytics workspace in the Azure portal](../azure-monitor/logs/quick-create-workspace.md#create-a-workspace).

1. In the [Azure portal](https://portal.azure.com), go to the resource group that contains your endpoint and then select the endpoint.
1. From the **Monitoring** section on the left of the page, select **Diagnostic settings** and then **Add settings**.
1. Select the log categories to enable, select **Send to Log Analytics workspace**, and then select the Log Analytics workspace to use. Finally, enter a **Diagnostic setting name** and select **Save**.

    :::image type="content" source="./media/how-to-monitor-online-endpoints/diagnostic-settings.png" alt-text="Screenshot of the diagnostic settings dialog.":::

    > [!IMPORTANT]
    > It may take up to an hour for the connection to the Log Analytics workspace to be enabled. Wait an hour before continuing with the next steps.
    
1. Submit scoring requests to the endpoint. This activity should create entries in the logs.
1. From either the online endpoint properties or the Log Analytics workspace, select **Logs** from the left of the screen.
1. Close the **Queries** dialog that automatically opens, and then double-click the **AmlOnlineEndpointConsoleLog**. If you don't see it, use the **Search** field.

    :::image type="content" source="./media/how-to-monitor-online-endpoints/online-endpoints-log-queries.png" alt-text="Screenshot showing the log queries.":::

1. Select **Run**.

    :::image type="content" source="./media/how-to-monitor-online-endpoints/query-results.png" alt-text="Screenshots of the results after running a query.":::

### Example queries

You can find example queries on the __Queries__ tab while viewing logs. Search for __Online endpoint__ to find example queries.

:::image type="content" source="./media/how-to-monitor-online-endpoints/example-queries.png" alt-text="Screenshot of the example queries.":::

### Log column details 

The following tables provide details on the data stored in each log:

**AmlOnlineEndpointTrafficLog**

[!INCLUDE [endpoint-monitor-traffic-reference](includes/endpoint-monitor-traffic-reference.md)]

**AmlOnlineEndpointConsoleLog**

[!INCLUDE [endpoint-monitor-console-reference](includes/endpoint-monitor-console-reference.md)]

**AmlOnlineEndpointEventLog**

[!INCLUDE [endpoint-monitor-event-reference](includes/endpoint-monitor-event-reference.md)]


## Using Application Insights

Curated environments include integration with Application Insights, and you can enable or disable this integration when you create an online deployment. Built-in metrics and logs are sent to Application Insights, and you can use the built-in features of Application Insights (such as Live metrics, Transaction search, Failures, and Performance) for further analysis.

See [Application Insights overview](../azure-monitor/app/app-insights-overview.md) for more.

In the studio, you can use the **Monitoring** tab on an online endpoint's page to see high-level activity monitor graphs for the managed online endpoint. To use the monitoring tab, you must select **Enable Application Insight diagnostic and data collection** when you create your endpoint.

:::image type="content" source="media/how-to-monitor-online-endpoints/monitor-endpoint.png" lightbox="media/how-to-monitor-online-endpoints/monitor-endpoint.png" alt-text="A screenshot of monitoring endpoint-level metrics in the studio.":::


## Related content

* Learn how to [view costs for your deployed endpoint](./how-to-view-online-endpoints-costs.md).
* Read more about [metrics explorer](../azure-monitor/essentials/metrics-charts.md).
