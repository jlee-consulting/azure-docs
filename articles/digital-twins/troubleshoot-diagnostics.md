---
# Mandatory fields.
title: Set up diagnostics
titleSuffix: Azure Digital Twins
description: See how to enable logging with diagnostics settings.
author: baanders
ms.author: baanders # Microsoft employees only
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
---

# Troubleshooting Azure Digital Twins: Diagnostics logging

Azure Digital Twins collects [metrics](troubleshoot-metrics.md) for your service instance that give information about the state of your resources. You can use these metrics to assess the overall health of Azure Digital Twins service and the resources connected to it. These user-facing statistics help you see what is going on with your Azure Digital Twins and help perform root-cause analysis on issues without needing to contact Azure support.

This article shows you how to turn on **diagnostics logging** for your metrics data from your Azure Digital Twins instance. You can use these logs to help you troubleshoot service issues, and configure diagnostic settings to send Azure Digital Twins metrics to different destinations. You can read more about these settings in [*Create diagnostic settings to send platform logs and metrics to different destinations*](../azure-monitor/platform/diagnostic-settings.md).

## Turn on diagnostic settings with the Azure portal

Here is how to enable diagnostic settings for your Azure Digital Twins instance:

1. Sign in to the [Azure portal](https://portal.azure.com) and navigate to your Azure Digital Twins instance. You can find it by typing its name into the portal search bar. 

2. Select **Diagnostic settings** from the menu, then **Add diagnostic setting**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Screenshot showing the diagnostic settings page and button to add":::

3. On the page that follows, fill in the following values:
     * **Diagnostic setting name**: Give the diagnostic settings a name.
     * **Category details**: Choose which operations you want to monitor, and check the boxes to enable diagnostics for those operations. The operations that diagnostic settings can report on are:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        For more details about these options, see the [*Category details*](#category-details) section below.
     * **Destination details**: Choose where you want to send the logs. You can select any combination of the three options:
        - Send to Log Analytics
        - Archive to a storage account
        - Stream to an event hub

        You may be asked to fill in additional details if they are necessary for your destination selection.  
    
4. Save the new settings. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Screenshot showing the diagnostic settings page and button to add":::

New settings take effect in about 10 minutes. After that, logs appear in the configured target back on the **Diagnostic settings** page for your instance. 

## Category details

Here are more details about the log categories that can be selected under **Category details** when setting up diagnostic settings.

| Log category | Description |
| --- | --- |
| ADTModelsOperation | Log all API calls pertaining to Models |
| ADTQueryOperation | Log all API calls pertaining to Queries |
| ADTEventRoutesOperation | Log all API calls pertaining to Event Routes as well as egress of events from Azure Digital Twins to an endpoint service like Event Grid, Event Hubs and Service Bus |
| ADTDigitalTwinsOperation | Log all API calls pertaining to Azure Digital Twins |

Each log category consists of operations of write, read, delete and action.  These map to REST API calls as follows:

| Event type | REST API operations |
| --- | --- |
| Write | PUT and PATCH |
| Read | GET |
| Delete | DELETE |
| Action | POST |

Here is a comprehensive list of the operations and corresponding [Azure Digital Twins REST API calls](https://docs.microsoft.com/rest/api/azure-digitaltwins/) that are logged in each category. 

>[!NOTE]
> Each log category contains several operations/REST API calls. In the table below, each log category maps to all operations/REST API calls underneath it until the next log category is listed. 

| Log category | Operation | REST API calls and other events |
| --- | --- | --- |
| ADTModelsOperation | Microsoft.DigitalTwins/models/write | Digital Twin Models Update API |
|  | Microsoft.DigitalTwins/models/read | Digital Twin Models Get By Id and List APIs |
|  | Microsoft.DigitalTwins/models/delete | Digital Twin Models Delete API |
|  | Microsoft.DigitalTwins/models/action | Digital Twin Models Add API |
| ADTQueryOperation | Microsoft.DigitalTwins/query/action | Query Twins API |
| ADTEventRoutesOperation | Microsoft.DigitalTwins/eventroutes/write | Event Routes Add API |
|  | Microsoft.DigitalTwins/eventroutes/read | Event Routes Get By Id and List APIs |
|  | Microsoft.DigitalTwins/eventroutes/delete | Event Routes Delete API |
|  | Microsoft.DigitalTwins/eventroutes/action | Egress of an event to an End Point service (not an API call) |
| ADTDigitalTwinsOperation | Microsoft.DigitalTwins/digitaltwins/write | Digital Twins Add, Add Relationship, Update, Update Component |
|  | Microsoft.DigitalTwins/digitaltwins/read | Digital Twins Get By Id, Get Component, Get Relationship by Id, List Incoming Relationships, List Relationships |
|  | Microsoft.DigitalTwins/digitaltwins/delete | Digital Twins Delete, Delete Relationship |
|  | Microsoft.DigitalTwins/digitaltwins/action | Digital Twins Send Component Telemetry, Send Telemetry |

## Next steps

* For more information about configuring diagnostics, see [*Collect and consume log data from your Azure resources*](../azure-monitor/platform/platform-logs-overview.md).
* For information about the Azure Digital Twins metrics, see [*Troubleshooting: View metrics with Azure Monitor*](troubleshoot-metrics.md).
* To see how to enable alerts for your metrics, see [*Troubleshooting: Set up alerts*](troubleshoot-alerts.md).
