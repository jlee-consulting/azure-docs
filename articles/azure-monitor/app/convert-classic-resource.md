---
title: Migrate an Application Insights classic resource to a workspace-based resource - Azure Monitor | Microsoft Docs
description: Learn about the steps required to upgrade your Application Insights classic resource to the new workspace-based model. 
ms.topic: conceptual
ms.date: 09/23/2020 
ms.custom: devx-track-azurepowershell
ms.reviewer: cawa
---

# Migrate to workspace-based Application Insights resources

This guide will walk you through migrating a classic Application Insights resource to a workspace-based resource. Workspace-based resources support full integration between Application Insights and Log Analytics. Workspace-based resources send Application Insights telemetry to a common Log Analytics workspace. This behavior allows you to access [the latest features of Azure Monitor](#new-capabilities) while keeping application, infrastructure, and platform logs in a consolidated location.

Workspace-based resources enable common Azure role-based access control (Azure RBAC) across your resources, and eliminate the need for cross-app/workspace queries.

**Workspace-based resources are currently available in all commercial regions and Azure US Government.**

## New capabilities

Workspace-based Application Insights allows you to take advantage of all the latest capabilities of Azure Monitor and Log Analytics, including:

* [Customer-Managed Keys (CMK)](../logs/customer-managed-keys.md) provides encryption at rest for your data with encryption keys that only you have access to.
* [Azure Private Link](../logs/private-link-security.md) allows you to securely link Azure PaaS services to your virtual network using private endpoints.
* [Bring Your Own Storage (BYOS) for Profiler and Snapshot Debugger](./profiler-bring-your-own-storage.md) gives you full control over:
    - Encryption-at-rest policy
    - Lifetime management policy
    - Network access for all data associated with Application Insights Profiler and Snapshot Debugger 
* [Commitment Tiers](../logs/cost-logs.md#commitment-tiers) enable you to save as much as 30% compared to the Pay-As-You-Go price. Otherwise, Pay-as-you-go data ingestion and data retention are billed similarly in Log Analytics as they are in Application Insights. 
* Faster data ingestion via Log Analytics streaming ingestion.

> [!NOTE]
> After migrating to a workspace-based Application Insights resource, telemetry from multiple Application Insights resources may be stored in a common Log Analytics workspace. You will still be able to pull data from a specific Application Insights resource, as described under [Understanding log queries](#understanding-log-queries).

## Migration process

When you migrate to a workspace-based resource, no data is transferred from your classic resource's storage to the new workspace-based storage. Choosing to migrate will change the location where new data is written to a Log Analytics workspace while preserving access to your classic resource data.

Your classic resource data will persist and be subject to the retention settings on your classic Application Insights resource. All new data ingested post migration will be subject to the [retention settings](../logs/data-retention-archive.md) of the associated Log Analytics workspace, which also supports [different retention settings by data type](../logs/data-retention-archive.md#set-retention-and-archive-policy-by-table).
The migration process is **permanent, and cannot be reversed**. Once you migrate a resource to workspace-based Application Insights, it will always be a workspace-based resource. However, once you migrate you're able to change the target workspace as often as needed. 

If you don't need to migrate an existing resource, and instead want to create a new workspace-based Application Insights resource, use the [workspace-based resource creation guide](create-workspace-resource.md).

## Pre-requisites

- A Log Analytics workspace with the access control mode set to the **`use resource or workspace permissions`** setting. 

    - Workspace-based Application Insights resources aren't compatible with workspaces set to the dedicated **`workspace based permissions`** setting. To learn more about Log Analytics workspace access control, consult the [access control mode guidance](../logs/manage-access.md#access-control-mode)

    - If you don't already have an existing Log Analytics Workspace, [consult the Log Analytics workspace creation documentation](../logs/quick-create-workspace.md).
    
- **Continuous export is not supported for workspace-based resources** and must be disabled.
Once the migration is complete, you can use [diagnostic settings](../essentials/diagnostic-settings.md) to configure data archiving to a storage account or streaming to Azure Event Hubs.  

    > [!CAUTION]
    > * Diagnostics settings uses a different export format/schema than continuous export, migrating will break any existing integrations with Stream Analytics.
    > * Diagnostic settings export may increase costs. ([more information](export-telemetry.md#diagnostic-settings-based-export))

- Check your current retention settings under **General** > **Usage and estimated costs** > **Data Retention** for your Log Analytics workspace. This setting will affect how long any new ingested data is stored once you migrate your Application Insights resource.

    > [!NOTE]
    > -  If you currently store Application Insights data for longer than the default 90 days and want to retain this larger retention period, you may need to adjust your workspace retention settings.
    > - If you’ve selected data retention greater than 90 days on data ingested into the Classic Application Insights resource prior to migration, data retention will continue to be billed to through that Application Insights resource until that data exceeds the retention period.
    > - If the retention setting for your Application Insights instance under **Configure** > **Usage and estimated costs** > **Data Retention** is enabled, then use that setting to control the retention days for the telemetry data still saved in your classic resource's storage.

- Understand [Workspace-based Application Insights](../logs/cost-logs.md#application-insights-billing) usage and costs.

## Migrate your resource

This section walks through migrating a classic Application Insights resource to a workspace-based resource.

1. From your Application Insights resource, select **Properties** under the **Configure** heading in the left-hand menu bar.

![Properties highlighted in red box](./media/convert-classic-resource/properties.png)

2. Select **`Migrate to Workspace-based`**.
    
![Migrate resource button](./media/convert-classic-resource/migrate.png)

3. Choose the Log Analytics workspace where you want all future ingested Application Insights telemetry to be stored. It can either be a Log Analytics workspace in the same subscription, or in a different subscription that shares the same Azure AD tenant. The Log Analytics workspace does not have to be in the same resource group as the Application Insights resource.

> [!NOTE]
> Migrating to a workspace-based resource can take up to 24 hours, but is usually faster than that. Please rely on accessing data through your Application Insights resource while waiting for the migration process to complete. Once completed, you will start seeing new data stored in the Log Analytics workspace tables.

![Migration wizard UI with option to select targe workspace](./media/convert-classic-resource/migration.png)
    
Once your resource is migrated, you'll see the corresponding workspace info in the **Overview** pane:

![Workspace Name](./media/create-workspace-resource/workspace-name.png)

Clicking the blue link text will take you to the associated Log Analytics workspace where you can take advantage of the new unified workspace query environment.

> [!TIP]
> After migrating to a workspace-based Application Insights resource, we recommend using the [workspace's daily cap](../logs/daily-cap.md) to limit ingestion and costs instead of the cap in Application Insights.

## Understanding log queries

We still provide full backwards compatibility for your Application Insights classic resource queries, workbooks, and log-based alerts within the Application Insights experience. 

To write queries against the [new workspace-based table structure/schema](#workspace-based-resource-changes), you must first navigate to your Log Analytics workspace. 

To ensure the queries successfully run, validate that the query's fields align with the [new schema fields](#appmetrics).

If you have multiple Application Insights resources store telemetry in one Log Analytics workspace, but you only want to query data from one specific Application Insights resource, you have two options:

- Option 1: Go to the desired Application Insights resource and open the **Logs** tab. All queries from this tab will automatically pull data from the selected Application Insights resource.
- Option 2: Go to the Log Analytics workspace that you configured as the destination for your Application Insights telemetry and open the **Logs** tab. To query data from a specific Application Insights resource, filter for the built-in ```_ResourceId``` property that is available in all application specific tables.

Notice that if you query directly from the Log Analytics workspace, you'll only see data that is ingested post migration. To see both your classic Application Insights data and the new data ingested after migration in a unified query experience, use the **Logs** tab from within your migrated Application Insights resource.

> [!NOTE]
> If you rename your Application Insights resource after migrating to workspace-based model, the Application Insights Logs tab will no longer show the telemetry collected before renaming. You will be able to see all data (old and new) on the Logs tab of the associated Log Analytics resource.

## Programmatic resource migration

### Azure CLI

To access the preview Application Insights Azure CLI commands, you first need to run:

```azurecli
 az extension add -n application-insights
```

If you don't run the `az extension add` command, you'll see an error message that states: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Now you can run the following to create your Application Insights resource:

```azurecli
az monitor app-insights component update --app
                                         --resource-group
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--query-access {Disabled, Enabled}]
                                         [--retention-time]
                                         [--workspace]
```

#### Example

```azurecli
az monitor app-insights component update --app your-app-insights-resource-name -g your_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

For the full Azure CLI documentation for this command,  consult the [Azure CLI documentation](/cli/azure/monitor/app-insights/component#az-monitor-app-insights-component-update).

### Azure PowerShell

The `Update-AzApplicationInsights` PowerShell command doesn't currently support migrating a classic Application Insights resource to workspace-based. To create a workspace-based resource with PowerShell, you can use the Azure Resource Manager templates below and deploy with PowerShell.

### Azure Resource Manager templates

#### Template file

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### Parameters file

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## Modifying the associated workspace

Once a workspace-based Application Insights resource has been created, you can modify the associated Log Analytics Workspace.

From within the Application Insights resource pane, select **Properties** > **Change Workspace** > **Log Analytics Workspaces**.

## Troubleshooting

### Access mode

**Error message:** *The selected workspace is configured with workspace-based access mode. Some APM features may be impacted. Select another workspace or allow resource-based access in the workspace settings. You can override this error by using CLI.* 

In order for your workspace-based Application Insights resource to operate properly you need to change the access control mode of your target Log Analytics workspace to the **resource or workspace permissions** setting. This setting is located in the Log Analytics workspace UI under **Properties** > **Access control mode**. For detailed instructions, consult the [Log Analytics configure access control mode guidance](../logs/manage-access.md#access-control-mode). If your access control mode is set to the exclusive **Require workspace permissions** setting, migration via the portal migration experience will remain blocked.

If you can’t change the access control mode for security reasons for your current target workspace, we recommend creating a new Log Analytics workspace to use for the migration. 

### Continuous export

**Error message:** *Continuous Export needs to be disabled before continuing. After migration, use Diagnostic Settings for export.* 

The legacy continuous export functionality isn't supported for workspace-based resources. Prior to migrating you need to disable continuous export.

1. From your Application Insights resource view, under the **Configure** heading select **Continuous Export**.

    ![Continuous export menu item](./media/convert-classic-resource/continuous-export.png)

2. Select **Disable**.

    ![Continuous export disable button](./media/convert-classic-resource/disable.png)

- Once you have selected disable, you can navigate back to the migration UI. If the edit continuous export page prompts you that your settings won't be saved, you can select ok for this prompt as it doesn't pertain to disabling/enabling continuous export.

- Once you've successfully migrated your Application Insights resource to workspace-based, you can use Diagnostic settings to replace the functionality that continuous export used to provide. Select **Diagnostic settings** > **add diagnostic setting** from within your Application Insights resource. You can select all tables, or a subset of tables to archive to a storage account, or to stream to Azure Event Hubs. For detailed guidance on diagnostic settings, refer to the [Azure Monitor diagnostic settings guidance](../essentials/diagnostic-settings.md).

### Retention settings

**Warning Message:** *Your customized Application Insights retention settings will not apply to data sent to the workspace. You'll need to reconfigure these separately.*

You don't have to make any changes prior to migrating. This message alerts you that your current Application Insights retention settings aren't set to the default 90-day retention period. This warning message means you may want to modify the retention settings for your Log Analytics workspace prior to migrating and starting to ingest new data. 

You can check your current retention settings for Log Analytics under **General** > **Usage and estimated costs** > **Data Retention** from within the Log Analytics UI. This setting will affect how long any new ingested data is stored once you migrate your Application Insights resource.

## Workspace-based resource changes

Prior to the introduction of [workspace-based Application Insights resources](create-workspace-resource.md), Application Insights data was stored separate from other log data in Azure Monitor. Both are based on Azure Data Explorer and use the same Kusto Query Language (KQL). Workspace-based Application Insights resources data is stored in a Log Analytics workspace, together with other monitoring data and application data. This simplifies your configuration by allowing you to analyze data across multiple solutions more easily, and to leverage the capabilities of workspaces.

### Classic data structure
The structure of a Log Analytics workspace is described in [Log Analytics workspace overview](../logs/log-analytics-workspace-overview.md). For a classic application, the data is not stored in a Log Analytics workspace. It uses the same query language, and you create and run queries by using the same Log Analytics tool in the Azure portal. Data items for classic applications are stored separately from each other. The general structure is the same as for workspace-based applications, although the table and column names are different. 

> [!NOTE]
> The classic Application Insights experience includes backward compatibility for your resource queries, workbooks, and log-based alerts. To query or view against the [new workspace-based table structure or schema](#table-structure), you must first go to your Log Analytics workspace. During the preview, selecting **Logs** from within the Application Insights panes will give you access to the classic Application Insights query experience. For more information, see [Query scope](../logs/scope.md).

[![Diagram that shows the Azure Monitor Logs structure for Application Insights.](../logs/media/data-platform-logs/logs-structure-ai.png)](../logs/media/data-platform-logs/logs-structure-ai.png#lightbox)

### Table structure

| Legacy table name | New table name | Description |
|:---|:---|:---|
| availabilityResults | AppAvailabilityResults |  Summary data from availability tests.|
| browserTimings | AppBrowserTimings | Data about client performance, such as the time taken to process the incoming data.|
| dependencies | AppDependencies | Calls from the application to other components (including external components) recorded via TrackDependency() – for example, calls to REST API, database or a file system.  |
| customEvents | AppEvents | Custom events created by your application. |
| customMetrics | AppMetrics | Custom metrics created by your application. |
| pageViews | AppPageViews| Data about each website view with browser information. |
| performanceCounters | AppPerformanceCounters | Performance measurements from the compute resources supporting the application, for example, Windows performance counters. |
| requests | AppRequests | Requests received by your application. For example, a separate request record is logged for each HTTP request that your web app receives.  |
| exceptions | AppExceptions | Exceptions thrown by the application runtime, captures both server side and client-side (browsers) exceptions. |
| traces | AppTraces | Detailed logs (traces) emitted through application code/logging frameworks recorded via TrackTrace(). |

> [!CAUTION]
> Do not take a production dependency on the Log Analytics tables, until you see new telemetry records show up directly in Log Analytics. This might take up to 24 hours after the migration process started.

### Table schemas

The following sections show the mapping between the classic property names and the new workspace-based Application Insights property names.  Use this information to convert any queries using legacy tables.

Most of the columns have the same name with different capitalization. Since KQL is case-sensitive, you will need to change each column name along with the table names in existing queries. Columns with changes in addition to capitalization are highlighted. You can still use your classic Application Insights queries within the **Logs** pane of your Application Insights resource, even if it is a workspace-based resource. The new property names are required for when querying from within the context of the Log Analytics workspace experience.

#### AppAvailabilityResults

Legacy table: availability

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dynamic|Properties|Dynamic|
|customMeasurements|dynamic|Measurements|Dynamic|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Type|String|
|location|string|Location|string|
|message|string|Message|string|
|name|string|Name|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|size|real|Size|real|
|success|string|Success|Bool|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

#### AppBrowserTimings

Legacy table: browserTimings

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dynamic|Properties|Dynamic|
|customMeasurements|dynamic|Measurements|Dynamic|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|string|Name|datetime|
|networkDuration|real|NetworkDurationMs|real|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|processingDuration|real|ProcessingDurationMs|real|
|receiveDuration|real|ReceiveDurationMs|real|
|sdkVersion|string|SdkVersion|string|
|sendDuration|real|SendDurationMs|real|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|totalDuration|real|TotalDurationMs|real|
|url|string|Url|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

#### AppDependencies

Legacy table: dependencies

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dynamic|Properties|Dynamic|
|customMeasurements|dynamic|Measurements|Dynamic|
|data|string|Data|string|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Type|String|
|name|string|Name|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|resultCode|string|ResultCode|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|success|string|Success|Bool|
|target|string|Target|string|
|timestamp|datetime|TimeGenerated|datetime|
|type|string|DependencyType|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

#### AppEvents

Legacy table: customEvents

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dynamic|Properties|Dynamic|
|customMeasurements|dynamic|Measurements|Dynamic|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|string|Name|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

#### AppMetrics

Legacy table: customMetrics

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dynamic|Properties|Dynamic|
|iKey|string|IKey|string|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|string|Name|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|
|value|real|(removed)||
|valueCount|int|ValueCount|int|
|valueMax|real|ValueMax|real|
|valueMin|real|ValueMin|real|
|valueSum|real|ValueSum|real|

> [!NOTE]
> Older versions of Application Insights SDKs used to report standard deviation (valueStdDev) in the metrics pre-aggregation. Due to little adoption in metrics analysis, the field was removed and is no longer aggregated by the SDKs. If the value is received by the Application Insights data collection end point, it gets dropped during ingestion and is not sent to the Log Analytics workspace. If you are interested in using standard deviation in your analysis, we recommend using queries against Application Insights raw events.

#### AppPageViews

Legacy table: pageViews

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dynamic|Properties|Dynamic|
|customMeasurements|dynamic|Measurements|Dynamic|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Type|String|
|name|string|Name|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|url|string|Url|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

#### AppPerformanceCounters

Legacy table: performanceCounters

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|category|string|Category|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|counter|string|(removed)||
|customDimensions|dynamic|Properties|Dynamic|
|iKey|string|IKey|string|
|instance|string|Instance|string|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|name|string|Name|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|
|value|real|Value|real|

#### AppRequests

Legacy table: requests

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dynamic|Properties|Dynamic|
|customMeasurements|dynamic|Measurements|Dynamic|
|duration|real|DurationMs|Real|
|`id`|string|`Id`|String|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Type|String|
|name|string|Name|String|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|String|
|resultCode|string|ResultCode|String|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|source|string|Source|String|
|success|string|Success|Bool|
|timestamp|datetime|TimeGenerated|datetime|
|url|string|Url|String|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

#### AppExceptions

Legacy table: exceptions

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|assembly|string|Assembly|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dynamic|Properties|dynamic|
|customMeasurements|dynamic|Measurements|dynamic|
|details|dynamic|Details|dynamic|
|handledAt|string|HandledAt|string|
|iKey|string|IKey|string|
|innermostAssembly|string|InnermostAssembly|string|
|innermostMessage|string|InnermostMessage|string|
|innermostMethod|string|InnermostMethod|string|
|innermostType|string|InnermostType|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|message|string|Message|string|
|method|string|Method|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|outerAssembly|string|OuterAssembly|string|
|outerMessage|string|OuterMessage|string|
|outerMethod|string|OuterMethod|string|
|outerType|string|OuterType|string|
|problemId|string|ProblemId|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|severityLevel|int|SeverityLevel|int|
|timestamp|datetime|TimeGenerated|datetime|
|type|string|ExceptionType|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

#### AppTraces

Legacy table: traces

|ApplicationInsights|Type|LogAnalytics|Type|
|:---|:---|:---|:---|
|appId|string|ResourceGUID|string|
|application_Version|string|AppVersion|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|ClientModel|string|
|client_OS|string|ClientOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|AppRoleName|string|
|customDimensions|dynamic|Properties|dynamic|
|customMeasurements|dynamic|Measurements|dynamic|
|iKey|string|IKey|string|
|itemCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Type|string|
|message|string|Message|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperationParentId|string|
|operation_SyntheticSource|string|OperationSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|severityLevel|int|SeverityLevel|int|
|timestamp|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|UserId|string|

## Next steps

* [Explore metrics](../essentials/metrics-charts.md)
* [Write Analytics queries](../logs/log-query-overview.md)
