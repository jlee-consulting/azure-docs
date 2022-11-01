---
title: Stream Analytics job diagram (preview) in Azure portal
description: This article describes the Azure Stream Analytics job diagram with metrics in Azure portal.
titleSuffix: Azure Stream Analytics
author: xujxu
ms.author: xujiang1
ms.service: stream-analytics
ms.custom: ignite-2022
ms.topic: conceptual
ms.date: 10/12/2022
---

# Stream Analytics job diagram (preview) in Azure portal

The job diagram in the Azure portal can help you visualize your job's query steps (logical concept) or streaming node (physical concept) with its input source, output destination, and metrics. You can use the job diagram to examine the metrics for each step or streaming node and quickly identify the source of a problem when you troubleshoot issues.

There are two types of job diagrams: 

* **Physical diagram**: it visualizes the key metrics of Stream Analytics job with the physical computation concept: streaming node dimension. A streaming node represents a set of compute resources that's used to process job's input data. To learn more details about the streaming node dimension, see [Azure Stream Analytics node name dimension](./stream-analytics-job-metrics-dimensions.md#node-name-dimension).
* **Logical diagram**: it visualizes the key metrics of Stream Analytics job with the logical concept: query step based on job's queries. To learn more, see [Debugging with the logical job diagram (preview) in Azure portal](./stream-analytics-job-logical-diagram-with-metrics.md).

This article describes the two types of job diagrams to guide you. 

> [!IMPORTANT]
> This feature is currently in PREVIEW.
> See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

## Use the job diagram

In the Azure portal, locate and select a Stream Analytics job. Then select **Job diagram (preview)** under **Developer tools**: 

:::image type="content" source="./media/job-diagram-with-metrics/diagram-location-in-portal-tree.png" alt-text="Screenshot that shows job diagram location in portal tree." lightbox="./media/job-diagram-with-metrics/diagram-location-in-portal-tree.png":::

In the top left corner, you can switch the two types of job diagram by clicking **Logical**, **Physical**.

:::image type="content" source="./media/job-diagram-with-metrics/2-diagram-switcher.png" alt-text="Screenshot that shows job diagram switcher."  lightbox="./media/job-diagram-with-metrics/2-diagram-switcher.png":::

## Physical job diagram

The following screenshot shows a physical job diagram with a default time period (last 30 minutes).

:::image type="content" source="./media/job-diagram-with-metrics/3-physical-diagram-sections.png" alt-text="Screenshot that shows physical job diagram sections." lightbox="./media/job-diagram-with-metrics/3-physical-diagram-sections.png":::

1. **Command bar section**: it's the command area where you can configure the time range of the job metrics, switch/configure heatmap visualization, search a streaming node, and switch view between **Diagram** and **Table**. 
    * **Heatmap settings**: the heatmap setting enables you to sort the nodes in diagram based on your wanted metrics and sorting type. The metrics can be CPU/memory utilization, watermark delay, input event, and backlogged input events.
    * **Time range**: you can choose different time range and job run to view the diagram and metrics.
    * **Job run**: Job run is inside **Time range**. When a job is started, restarted or scaled-up/down (SU changes), a new job run will be generated. One job run maps on physical job diagram.
    * **Diagram/Table view switcher**: you can switch the view between diagram and table. The table view is shown as below:

        :::image type="content" source="./media/job-diagram-with-metrics/4-physical-diagram-table-view.png" alt-text="Screenshot that shows physical job diagram with table overview."  lightbox="./media/job-diagram-with-metrics/4-physical-diagram-table-view.png":::

1. **Diagram/Table section**: it's the place where you can view the metrics (aggregated within the selected time range) in streaming node level with diagram view or table view. Each box in this section represents a streaming node that is used to process the input data. The metrics on each node are:
    * **Input Events** (Aggregation type: SUM)
    * **CPU % Utilization** (Aggregation type: Avg)
    * **SU (Memory) % Utilization** (Aggregation type: Max)
    * **Partition IDs** (A list, no aggregation)
    * **Watermark Delay** (Aggregation type: Max)
    * **Backlogged Input Events** (Aggregation type: SUM)

    For more information about the metrics definition, see [Azure Stream Analytics node name dimension](./stream-analytics-job-metrics-dimensions.md#node-name-dimension).
1. **Chart section**: it's the place where you can view the historical metrics data within the selected time range. The default metrics shown in the default chart are **SU (Memory) % Utilization** and **CPU % Utilization**". You can also add more charts by clicking **Add chart**.

The **Diagram/Table section** and **Chart section** can be interactive with each other. You can select multiple nodes in **Diagram/Table section** to get the metrics in **Chart section** filtered by the selected nodes and vice versa.

:::image type="content" source="./media/job-diagram-with-metrics/5-job-physical-diagram-node-chart-interaction.png" alt-text="Screenshot that shows physical job diagram node chart interaction."  lightbox="./media/job-diagram-with-metrics/5-job-physical-diagram-node-chart-interaction.png":::


To learn more about how to debug with physical diagram, see [Debugging with the physical job diagram (preview) in Azure portal](./stream-analytics-job-physical-diagram-with-metrics.md).


## Logical job diagram

The logical job diagram has a similar layout to the physical diagram, with three sections, but it has different metrics and configuration settings.

:::image type="content" source="./media/job-diagram-with-metrics/3-logical-diagram-overview.png" alt-text="Screenshot that shows logical job diagram sections."  lightbox="./media/job-diagram-with-metrics/3-logical-diagram-overview.png":::

1. **Command bar section**: in logical diagram, you can operate the cloud job (Stop, Delete), and configure the time range of the job metrics. The diagram view is only available for logical diagrams.
2. **Diagram section**: the node box in this selection represents the job's input, output, and query steps. You can view the metrics in the node directly or in the chart section interactively by clicking certain node in this section. For more information about the metrics definition, see [Azure Stream Analytics node name dimension](./stream-analytics-job-metrics-dimensions.md#node-name-dimension).
3. **Chart section**: the chart section in a logical diagram has two tabs: **Metrics** and **Activity Logs**.
    * **Metrics**: job's metrics data is shown here when the corresponding metrics are selected in the right panel. 
    * **Activity Logs**: job's operations performed on jobs is shown here. When the job's diagnostic log is enabled, it's also shown here. To learn more about the job logs, see [Azure Stream Analytics job logs](./stream-analytics-job-diagnostic-logs.md).

   When a logical job diagram is loaded, this job's metrics: Watermark delay, Input events, Output Events, and Backlogged Input Events are shown in the chart section for the latest 30 minutes.

The interaction between **Diagram section** and **Chart section** is also available in logical diagram as well. The metrics data will be filtered by the node's properties.

:::image type="content" source="./media/job-diagram-with-metrics/5-job-logical-diagram-node-selection.png" alt-text="Screenshot that shows physical job diagram overview."  lightbox="./media/job-diagram-with-metrics/5-job-logical-diagram-node-selection.png":::

To learn more about how to debug with logical diagrams, see [Debugging with the logical job diagram (preview) in Azure portal](./stream-analytics-job-logical-diagram-with-metrics.md).


## Next steps
* [Introduction to Stream Analytics](stream-analytics-introduction.md)
* [Get started with Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics job metrics](./stream-analytics-job-metrics.md)
* [Scale Stream Analytics jobs](stream-analytics-scale-jobs.md)
* [Stream Analytics query language reference](/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics management REST API reference](/rest/api/streamanalytics/)
