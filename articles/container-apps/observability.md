---
title: Observability in Azure Container Apps
description: Monitor your running app in Azure Container Apps
services: container-apps
author: cebundy
ms.service: container-apps
ms.custom: event-tier1-build-2022
ms.topic: conceptual
ms.date: 07/05/2022
ms.author: v-bcatherine
---

# Observability in Azure Container Apps

Azure Container Apps provides several built-in observability features that give you a holistic view of your container app’s health throughout its application lifecycle.  These features help you monitor and diagnose the state of your app to improve performance and respond to critical problems.

These features include:

- [Log streaming](#log-streaming)
- [Container console](#container-console)
- [Azure Monitor metrics](#azure-monitor-metrics)
- [Azure Monitor Log Analytics](#azure-monitor-log-analytics)
- [Azure Monitor alerts](#azure-monitor-alerts)

>[!NOTE]
> While not a built-in feature, [Azure Monitor's Application Insights](../azure-monitor/app/app-insights-overview.md) is a powerful tool to monitor your web and background applications.
> Although Container Apps doesn't support the Application Insights auto-instrumentation agent, you can instrument your application code using Application Insights SDKs.  

## Log streaming

While developing and troubleshooting your container app, you often want to see a container's logs in real-time.  Container Apps lets you view a stream of your container's `stdout` and `stderr` log messages using the Azure portal or the Azure CLI.

### View log streams from the Azure portal

Go to your container app page in the Azure portal.   Select **Log stream** under the **Monitoring** section on the sidebar menu.  For apps with more than one container, choose a container from the drop-down lists. When there are multiple revisions and replicas, first choose from the **Revision**, **Replica**, and then the **Container** drop-down lists.

After you select the container, you can view the log stream in the viewing pane.  You can stop the log stream and clear the log messages from the viewing pane. To save the log messages, you can copy and paste them into the editor of your choice.

:::image type="content" source="media/observability/log-stream.png" alt-text="Screenshot of Azure Container Apps Log stream page.":::

### Show log streams from Azure CLI

Show a container's application logs from the Azure CLI with the `az containerapp logs show` command.  You can view previous log entries using the `--tail` argument.  To view a live stream, use the `--follow` argument.  Select Ctrl-C to stop the live stream.

For example, you can list the last 50 container log entries in a container app with a single revision, replica, and container using the following command.

# [Bash](#tab/bash)

```azurecli
az containerapp logs show \
  --name album-api \
  --resource-group album-api-rg \
  --tail 50
```

# [PowerShell](#tab/powershell)

```azurecli
az containerapp logs show `
  --name album-api `
  --resource-group album-api-rg `
  --tail 50
```

---

You can view a log stream from a container in a container app with multiple revisions, replicas, and containers by adding the `--revision`, `--replica`, `--container` arguments to the `az containerapp show` command.  

Use the `az containerapp revision list` command to get the revision, replica, and container names to use in the `az containerapp logs show` command.

# [Bash](#tab/bash)

```azurecli
az containerapp revision list \
  --name album-api \
  --resource-group album-api-rg
```

# [PowerShell](#tab/powershell)

```azurecli
az containerapp revision list `
  --name album-api `
  --resource-group album-api-rg 
```

---

Show the streaming container logs: 

# [Bash](#tab/bash)

```azurecli
az containerapp logs show \
  --name album-api \
  --resource-group album-api-rg \
  --revision album-api--v2 \
  --replica album-api--v2-5fdd5b4ff5-6mblw \
  --container album-api-container \
  --follow
```

# [PowerShell](#tab/powershell)

```azurecli
az containerapp logs show  `
  --name album-api `
  --resource-group album-api-rg `
  --revision album-api--v2 `
  --replica album-api--v2-5fdd5b4ff5-6mblw `
  --container album-api-container `
  --follow
```

---

## Container console

Connecting to a container's console is useful when you want to troubleshoot and modify something inside a container.  Azure Container Apps lets you connect to a container's console using the Azure portal or the Azure CLI.

### Connect to a container console via the Azure portal

Select **Console** in the **Monitoring** menu group from your container app page in the Azure portal. When your app has more than one container, choose a container from the drop-down list. When there are multiple revisions and replicas, first choose from the **Revision**, **Replica**, and then the **Container** drop-down lists.

You can choose to access your console via bash, sh, or a custom executable.  If you choose a custom executable, it must be available in the container.

:::image type="content" source="media/observability/console-ss.png" alt-text="Screenshot of Azure Container Apps Console page.":::

### Connect to a container console via the Azure CLI

Use the `az containerapp exec` command to connect to a container console.  Select Ctrl-D to exit the console.

For example, you can connect to a container console in a container app with a single revision, replica, and container using the following command.

# [Bash](#tab/bash)

```azurecli
az containerapp exec \
  --name album-api \
  --resource-group album-api-rg
```

# [PowerShell](#tab/powershell)

```azurecli
az containerapp exec `
  --name album-api `
  --resource-group album-api-rg
```

---

To connect to a container console in a container app with multiple revisions, replicas, and containers include the `--revision`, `--replica`, and `--container` arguments in the `az containerapp exec` command. 

Use the `az containerapp revision list` command to get the revision, replica and container names to use in the `az containerapp exec` command.

# [Bash](#tab/bash)

```azurecli
az containerapp revision list \
  --name album-api \
  --resource-group album-api-rg
```

# [PowerShell](#tab/powershell)

```azurecli
az containerapp revision list `
  --name album-api `
  --resource-group album-api-rg
```

---

Connect to the container console.

# [Bash](#tab/bash)

```azurecli
az containerapp exec \
  --name album-api \
  --resource-group album-api-rg \
  --revision album-api--v2 \
  --replica album-api--v2-5fdd5b4ff5-6mblw \
  --container album-api-container 
```

# [PowerShell](#tab/powershell)

```azurecli
az containerapp exec `
  --name album-api `
  --resource-group album-api-rg `
  --revision album-api--v2 `
  --replica album-api--v2-5fdd5b4ff5-6mblw `
  --container album-api-container
```

---

## Azure Monitor metrics

Azure Monitor collects metric data from your container app at regular intervals. These metrics help you gain insights into the performance and health of your container app. You can use metrics explorer in the Azure portal to monitor and analyze the metric data. You can also retrieve metric data through the [Azure CLI](/cli/azure/monitor/metrics) and Azure [PowerShell cmdlets](/powershell/module/az.monitor/get-azmetric).

### Available metrics for Container Apps

Container Apps provides these metrics.

|Title  | Description | Metric ID |Unit  |
|---------|---------|---------|---------|
|CPU usage nanocores | CPU usage in nanocores (1,000,000,000 nanocores = 1 core) | UsageNanoCores| nanocores|
|Memory working set bytes |Working set memory used in bytes |WorkingSetBytes |bytes|
|Network in bytes|Network received bytes|RxBytes|bytes|
|Network out bytes|Network transmitted bytes|TxBytes|bytes|
|Requests|Requests processed|Requests|n/a|
|Replica count| Number of active replicas| Replicas | n/a |
|Replica Restart Count| Number of replica restarts | RestartCount | n/a |

The metrics namespace is `microsoft.app/containerapps`.

### View a current snapshot of your app's metrics

On your container app **Overview** page in the Azure portal, select the **Monitoring** tab to display charts showing your container app's current CPU, memory, and network utilization.

:::image type="content" source="media/observability/metrics-in-overview-page.png" alt-text="Screenshot of the Monitoring section in the container app overview page.":::

From this view, you can pin one or more charts to your dashboard or select a chart to open it in the metrics explorer.

### View metrics with metrics explorer

The Azure Monitor metrics explorer lets you create charts from metric data to help you analyze your container app's resource and network usage over time. You can pin charts to a dashboard or in a shared workbook.

Open the metrics explorer in the Azure portal by selecting **Metrics** from the sidebar menu on your container app page.  To learn more about metrics explorer, go to [Getting started with metrics explorer](../azure-monitor/essentials/metrics-getting-started.md).

Create a chart by selecting a **Metric**.  You can modify the chart by changing aggregation, adding more metrics, changing time ranges and intervals, adding filters, and applying splitting.

:::image type="content" source="media/observability/metrics-main-page.png" alt-text="Screenshot of the metrics explorer from the container app resource page.":::

You can filter your metrics by revision or replica.  For example, to filter by a replica, select **Add filter** and select a replica from the **Value** drop-down list.

:::image type="content" source="media/observability/metrics-add-filter.png" alt-text="Screenshot of the metrics explorer showing the chart filter options.":::

When applying splitting, you can split the metric information in your chart by revision or replica (except for Replica count, which you can only split by revision). The requests metric can also be split by status code and status code category. For example, to split by revision, select **Apply splitting** and select **Revision** from the **Values** drop-down list. Splitting is only available when the chart contains a single metric.

:::image type="content" source="media/observability/metrics-apply-splitting.png" alt-text="Screenshot of the metrics explorer that shows a chart with metrics split by revision.":::

You can add more scopes to view metrics across multiple container apps.

:::image type="content" source="media/observability/metrics-across-apps.png" alt-text="Screenshot of the metrics explorer that shows a chart with metrics for multiple container apps.":::

## Azure Monitor Log Analytics

Azure Container Apps is integrated with Azure Monitor Log Analytics to monitor and analyze your container app's logs.  Each Container Apps environment includes a Log Analytics workspace that provides a common place to store the system and application log data from all container apps running in the environment.  

Log entries are accessible by querying Log Analytics tables through the Azure portal or a command shell using the [Azure CLI](/cli/azure/monitor/log-analytics).

<!--
Azure Monitor collects application logs and stores them in a Log Analytics workspace.  Each Container Apps environment includes a Log Analytics workspace that provides a common place to store the application log data from all containers running in the environment.  
-->

There are two types of logs for Container Apps.  

1. Console logs, which are emitted by your app.
1. System logs, which are emitted by the Container Apps service.


### Container Apps System Logs

The Container Apps service provides system log messages at the container app level.  System logs emits the following messages:

| Source | Type | Message |
|---------|------|---------|
| Dapr | info | Successfully created dapr component \<component-name\> with scope \<dapr-component-scope\> |
| Dapr | info | Successfully updated dapr component \<component-name\> with scope \<component-type\> |
| Dapr | error | Error creating dapr component \<component-name\> |
| Volume Mounts | info | Successfully mounted volume \<volume-name\> for revision \<revision-scope\> |
| Volume Mounts | error | Error mounting volume \<volume-name\> |
| Domain Binding | info | Successfully bound Domain \<domain\> to the container app \<container app name\> |
| Authentication | info | Auth enabled on app. Creating authentication config |
| Authentication | info | Auth config created successfully |
| Traffic weight | info | Setting a traffic weight of \<percentage>% for revision \<revision-name\\> |
| Revision Provisioning | info | Creating a new revision: \<revision-name\> |
| Revision Provisioning | info | Successfully provisioned revision \<name\> |
| Revision Provisioning | info| Deactivating Old revisions since 'ActiveRevisionsMode=Single' |
| Revision Provisioning | error | Error provisioning revision \<revision-name>. ErrorCode: \<[ErrImagePull]\|[Timeout]\|[ContainerCrashing]\> |

The system log data is accessible by querying the `ContainerAppSystemlogs_CL` table. The most used Container Apps specific columns in the table are:

| Column  | Description |
|---|---|
| `ContainerAppName_s` | Container app name |
| `EnvironmentName_s` | Container Apps environment name |
| `Log_s` | Log message |
| `RevisionName_s` | Revision name |

### Container Apps Console Logs

Console logs originate from the `stderr` and `stdout` messages from the containers in your container app and Dapr sidecars.  You can view console logs by querying the `ContainerAppConsolelogs_CL` table.

> [!TIP]
> Instrumenting your code with well-defined log messages can help you to understand how your code is performing and to debug issues.  To learn more about best practices refer to [Design for operations](/azure/architecture/guide/design-principles/design-for-operations).

The most commonly used Container Apps specific columns in ContainerAppConsoleLogs_CL include:

|Column  |Description |
|---------|---------|
| `ContainerAppName_s` | Container app name |
| `ContainerGroupName_g` | Replica name |
| `ContainerId_s` | Container identifier |
| `ContainerImage_s` | Container image name |
| `EnvironmentName_s` | Container Apps environment name |
| `Log_s` | Log message |
| `RevisionName_s` | Revision name |

### Use Log Analytics to query logs

Log Analytics is a tool in the Azure portal that you can use to view and analyze log data. Using Log Analytics, you can write Kusto queries and then sort, filter, and visualize the results in charts to spot trends and identify issues. You can work interactively with the query results or use them with other features such as alerts, dashboards, and workbooks.

Start Log Analytics from **Logs** in the sidebar menu on your container app page.  You can also start Log Analytics from **Monitor>Logs**.  

You can query the logs using the tables listed in the **CustomLogs** category **Tables** tab.  The tables in this category are the `ContainerAppSystemlogs_CL` and `ContainerAppConsolelogs_CL` tables.

:::image type="content" source="media/observability/log-analytics-query-page.png" alt-text="Screenshot of the Log Analytics custom log tables.":::

Below is a Kusto query that displays console log entries for the container app named *album-api*. 

```kusto
ContainerAppConsoleLogs_CL
| where ContainerAppName_s == 'album-api'
| project Time=TimeGenerated, AppName=ContainerAppName_s, Revision=RevisionName_s, Container=ContainerName_s, Message=Log_s
| take 100
```

Below is a Kusto query that displays system log entries for the container app named *album-api*. 

```kusto
ContainerAppSystemLogs_CL
| where ContainerAppName_s == 'album-api'
| project Time=TimeGenerated, EnvName=EnvironmentName_s, AppName=ContainerAppName_s, Revision=RevisionName_s, Message=Log_s
| take 100
```

For more information regarding Log Analytics and log queries, see the [Log Analytics tutorial](../azure-monitor/logs/log-analytics-tutorial.md).

### Query logs via the Azure CLI

Container Apps logs can be queried using the [Azure CLI](/cli/azure/monitor/log-analytics).  

These example Azure CLI queries output a table containing log records for the container app name **album-api**.  The table columns are specified by the parameters after the `project` operator.  The `$WORKSPACE_CUSTOMER_ID` variable contains the GUID of the Log Analytics workspace.


This example queries the `ContainerAppConsoleLogs_CL` table:

```azurecli
az monitor log-analytics query --workspace $WORKSPACE_CUSTOMER_ID --analytics-query "ContainerAppConsoleLogs_CL | where ContainerAppName_s == 'album-api' | project Time=TimeGenerated, AppName=ContainerAppName_s, Revision=RevisionName_s, Container=ContainerName_s, Message=Log_s, LogLevel_s | take 5" --out table
```

This example queries the `ContainerAppSystemLogs_CL` table:

```azurecli
az monitor log-analytics query --workspace $WORKSPACE_CUSTOMER_ID --analytics-query "ContainerAppSystemLogs_CL | where ContainerAppName_s == 'album-api' | project Time=TimeGenerated, AppName=ContainerAppName_s, Revision=RevisionName_s, Message=Log_s, LogLevel_s | take 5" --out table
```

For more information about using Azure CLI to view container app logs, see [Viewing Logs](monitor.md#viewing-logs).

## Azure Monitor alerts

Azure Monitor alerts notify you so that you can respond quickly to critical issues.  There are two types of alerts that you can define:

- [metric alerts](../azure-monitor/alerts/alerts-metric-overview.md) based on metric data
- [log alerts](../azure-monitor/alerts/alerts-unified-log.md) based on log data

You can create alert rules from metric charts in the metric explorer and from queries in Log Analytics.  You can also define and manage alerts from the **Monitor>Alerts** page. 
 
To learn more about alerts, refer to [Overview of alerts in Microsoft Azure](../azure-monitor/alerts/alerts-overview.md).

### Create metric alerts in metrics explorer

When you add alert rules to a metric chart in the metrics explorer, alerts are triggered when the collected metric data matches alert rule conditions.  

After creating a [metric chart](#view-metrics-with-metrics-explorer), select **New alert rule** to create an alert rule based on the chart's settings.  The new alert rule will include your chart's target resource, metric, splitting and filter dimensions.

:::image type="content" source="media/observability/metrics-alert-new-alert-rule.png" alt-text="Screenshot of the metrics explorer highlighting the new rule button.":::

When you select **New alert rule**, the rule creation pane opens to the **Condition** tab.  Metrics explorer automatically creates an alert condition containing the chart's metric settings.  Select the alert condition to add the threshold criteria to complete the condition.

:::image type="content" source="media/observability/metrics-alert-create-condition.png" alt-text="Screenshot of the metric explorer alert rule editor.  A condition is automatically created based on the chart settings.":::

Add more conditions to your alert rule by selecting the **Add condition** option in the **Create an alert rule** pane.

:::image type="content" source="media/observability/metrics-alert-add-condition.png" alt-text="Screenshot of the metric explorer alert rule editor highlighting the Add condition button.":::

When adding a new alert condition, select from the metrics listed in the **Select a signal** pane.

:::image type="content" source="media/observability/metrics-alert-select-a-signal.png" alt-text="Screenshot of the metric explorer alert rule editor showing the Select a signal pane.":::

After selecting the metric, you can configure the settings for your alert condition.  For more information about configuring alerts, see [Manage metric alerts](../azure-monitor/alerts/alerts-metric.md).

You can receive individual alerts for specific revisions or replicas by enabling alert splitting and selecting **Revision** or **Replica** from the **Dimension name** list.

Example of selecting a dimension to split an alert.

:::image type="content" source="media/observability/metrics-alert-split-by-dimension.png" alt-text="Screenshot of the metrics explorer alert rule editor.  This example shows the Split by dimensions options in the Configure signal logic pane.":::

 To learn more about configuring alerts, visit [Create a metric alert for an Azure resource](../azure-monitor/alerts/tutorial-metric-alert.md)

### Create log alert rules in Log Analytics

Use Log Analytics to create alert rules from a log query.  When you create an alert rule from a query, the query is run at set intervals triggering alerts when the log data matches the alert rule conditions.  To learn more about creating log alert rules, see [Manage log alerts](../azure-monitor/alerts/alerts-log.md).

To create an alert rule, you first create and run the query to validate it.  Then, select **New alert rule**.  

:::image type="content" source="media/observability/log-alert-new-alert-rule.png" alt-text="Screenshot of the Log Analytics interface highlighting the new alert rule button.":::

The **Create an alert rule** editor is opened to the **Condition** tab, which is populated with your log query.  Configure the settings in the **Measurement** and **Alert logic** sections to complete the alert rule.

:::image type="content" source="media/observability/log-alerts-rule-editor.png" alt-text="Screenshot of the Log Analytics alert rule editor.":::

Optionally, you can enable alert splitting in the alert rule to send individual alerts for each dimension you select in the **Split by dimensions** section of the editor.  The dimensions for Container Apps are:

- app name
- revision
- container
- log message

:::image type="content" source="media/observability/log-alerts-splitting.png" alt-text="Screenshot of the Log Analytics alert rule editor showing the Split by dimensions options":::

## Observability throughout the application lifecycle

With the Container Apps observability features, you can monitor your app throughout the development-to-production lifecycle. The following sections describe the most useful monitoring features for each phase. 

### Development and test phase

During the development and test phase, real-time access to your containers' application logs and console is critical for debugging issues.  Container Apps provides: 

- [log streaming](#log-streaming) for real-time monitoring
- [container console](#container-console) access to debug your application

### Deployment phase

Once you deploy your container app, it's essential to monitor your app. Continuous monitoring helps you quickly identify problems that may occur around error rates, performance, or metrics.

Azure Monitor features give you the ability to track your app with the following features:

- [Azure Monitor Metrics](#azure-monitor-metrics): monitor and analyze key metrics
- [Azure Monitor Alerts](#azure-monitor-alerts): send alerts for critical conditions
- [Azure Monitor Log Analytics](#azure-monitor-log-analytics): view and analyze application logs

### Maintenance phase

Container Apps manages updates to your container app by creating [revisions](revisions.md).  You can run multiple revisions concurrently to perform A/B testing or for blue green deployments.  These observability features will help you monitor your app across revisions:

- [Azure Monitor Metrics](#azure-monitor-metrics): monitor and compare key metrics for multiple revisions
- [Azure Monitor Alerts](#azure-monitor-alerts): send alerts individual alerts per revision
- [Azure Monitor Log Analytics](#azure-monitor-log-analytics): view, analyze and compare log data for multiple revisions

## Next steps

- [Monitor an app in Azure Container Apps](monitor.md)
- [Health probes in Azure Container Apps](health-probes.md)
