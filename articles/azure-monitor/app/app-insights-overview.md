---
title: Application Insights overview
description: Learn how Application Insights in Azure Monitor provides performance management and usage tracking of your live web application.
ms.topic: overview
ms.date: 09/20/2022
---

# Application Insights overview

Application Insights is an extension of [Azure Monitor](../overview.md) and provides Application Performance Monitoring (also known as “APM”) features. APM tools are useful to monitor applications from development, through test, and into production in the following ways:

1. *Proactively* understand how an application is performing.
1. *Reactively* review application execution data to determine the cause of an incident.

In addition to collecting [Metrics](standard-metrics.md) and application [Telemetry](data-model.md) data, which describe application activities and health, Application Insights can also be used to collect and store application [trace logging data](asp-net-trace-logs.md).

The [log trace](asp-net-trace-logs.md) is associated with other telemetry to give a detailed view of the activity. Adding trace logging to existing apps only requires providing a destination for the logs; the logging framework rarely needs to be changed.

Application Insights provides other features including, but not limited to:

- [Live Metrics](live-stream.md) – observe activity from your deployed application in real time with no effect on the host environment
- [Availability](availability-overview.md)  – also known as “Synthetic Transaction Monitoring”, probe your applications external endpoint(s) to test the overall availability and responsiveness over time
- [GitHub or Azure DevOps integration](work-item-integration.md) – create [GitHub](https://learn.microsoft.com/training/paths/github-administration-products/) or [Azure DevOps](https://learn.microsoft.com/azure/devops/?view=azure-devops) work items in context of Application Insights data
- [Usage](usage-overview.md) – understand which features are popular with users and how users interact and use your application
- [Smart Detection](proactive-diagnostics.md) – automatic failure and anomaly detection through proactive telemetry analysis

In addition, Application Insights supports [Distributed Tracing](distributed-tracing.md), also known as “distributed component correlation”. This feature allows [searching for](diagnostic-search.md) and [visualizing](transaction-diagnostics.md) an end-to-end flow of a given execution or transaction. The ability to trace activity end-to-end is increasingly important for applications that have been built as distributed components or [microservices](https://learn.microsoft.com/azure/architecture/guide/architecture-styles/microservices).

The [Application Map](app-map.md) allows a high level top-down view of the application architecture and at-a-glance visual references to component health and responsiveness. 

To understand the number of Application Insights resources required to cover your Application or components across environments, see the [Application Insights deployment planning guide](separate-resources.md). 

## How do I use Application Insights?

Application Insights is enabled through either [Auto-Instrumentation](codeless-overview.md) (agent) or by adding the [Application Insights SDK](sdk-support-guidance.md) to your application code. [Many languages](platforms.md) are supported and the applications could be on Azure, on-premises, or hosted by another cloud. To figure out which type of instrumentation is best for you, reference [How do I instrument an application?](#how-do-i-instrument-an-application).

The Application Insights agent or SDK pre-processes telemetry and metrics before sending the data to Azure where it's ingested and processed further before being stored in Azure Monitor Logs (Log Analytics). For this reason, an Azure account is required to use Application Insights.

The easiest way to get started consuming Application insights is through the Azure portal and the built-in visual experiences. Advanced users can [query the underlying data](../logs/log-query-overview.md) directly to [build custom visualizations](tutorial-app-dashboards.md) through Azure Monitor [Dashboards](overview-dashboard.md) and [Workbooks](../visualize/workbooks-overview.md).

Consider starting with the [Application Map](app-map.md) for a high level view. Use the [Search](diagnostic-search.md) experience to quickly narrow down telemetry and data by type and date-time, or search within data (for example Log Traces) and filter to a given correlated operation of interest.

Jump into analytics with [Performance view](tutorial-performance.md) – get deep insights into how your Application or API and downstream dependencies are performing and find for a representative sample to [explore end to end](transaction-diagnostics.md). And, be proactive with the [Failure view](tutorial-runtime-exceptions.md) – understand which components or actions are generating failures and triage errors and exceptions. The built-in views are helpful to track application health proactively and for reactive root-cause-analysis.

[Create Azure Monitor Alerts](tutorial-alert.md) to signal potential issues should your Application or components parts deviate from the established baseline. 

Application Insights pricing is consumption-based; you pay for only what you use. For more information on pricing, see the [Azure Monitor Pricing page](https://azure.microsoft.com/pricing/details/monitor/) and [how to optimize costs](../best-practices-cost.md).

## How do I instrument an application?

[Auto-Instrumentation](codeless-overview.md) is the preferred instrumentation method. It requires no developer investment and eliminates future overhead related to [updating the SDK](sdk-support-guidance.md). It's also the only way to instrument an application in which you don't have access to the source code.

You only need to install the Application Insights SDK in the following circumstances:

- You require [custom events and metrics](api-custom-events-metrics.md)
- You require control over the flow of telemetry
- [Auto-Instrumentation](codeless-overview.md) isn't available (typically due to language or platform limitations)

To use the SDK, you install a small instrumentation package in your app and then instrument the web app, any background components, and JavaScript within the web pages. The app and its components don't have to be hosted in Azure. The instrumentation monitors your app and directs the telemetry data to an Application Insights resource by using a unique token. The effect on your app's performance is small; tracking calls are non-blocking and batched to be sent in a separate thread.

### [.NET](#tab/net)

Integrated Auto-instrumentation is available for [Azure App Service .NET](azure-web-apps-net.md), [Azure App Service .NET Core](azure-web-apps-net-core.md), [Azure Functions](../../azure-functions/functions-monitoring.md), and [Azure Virtual Machines](azure-vm-vmss-apps.md).

[Azure Monitor Application Insights Agent](status-monitor-v2-overview.md) is available for workloads running in on-premises virtual machines.

A detailed view of all Auto-instrumentation supported environments, languages, and resource providers are available [here](codeless-overview.md#supported-environments-languages-and-resource-providers).

For other scenarios, the [Application Insights SDK](/dotnet/api/overview/azure/insights) is required.

A preview [Open Telemetry](opentelemetry-enable.md?tabs=net) offering is also available.

### [Java](#tab/java)

Integrated Auto-Instrumentation is available for Java Apps hosted on [Azure App Service](azure-web-apps-java.md) and [Azure Functions](monitor-functions.md).

Auto-instrumentation is available for any environment using [Azure Monitor OpenTelemetry-based auto-instrumentation for Java applications](java-in-process-agent.md).

### [Node.js](#tab/nodejs)

Auto-instrumentation is available for [Azure App Service](azure-web-apps-nodejs.md).

The [Application Insights SDK](nodejs.md) is an alternative and we also have a preview [Open Telemetry](opentelemetry-enable.md?tabs=nodejs) offering available.

### [JavaScript](#tab/javascript)

JavaScript requires the [Application Insights SDK](javascript.md).

### [Python](#tab/python)

Python applications can be monitored using [OpenCensus Python SDK via the Azure Monitor exporters](opencensus-python.md).

An extension is available for monitoring [Azure Functions](opencensus-python.md#integrate-with-azure-functions).

A preview [Open Telemetry](opentelemetry-enable.md?tabs=python) offering is also available.

---

## Next steps

- [Create a resource](create-workspace-resource.md)
- [Application Map](app-map.md)
- [Transaction search](diagnostic-search.md)

## Troubleshooting

### FAQ

Review [frequently asked questions](../faq.yml).
### Microsoft Q&A questions forum

Post questions to the Microsoft Q&A [answers forum](/answers/topics/24223/azure-monitor.html).

### Stack Overflow

Post coding questions to [Stack Overflow]() using an Application Insights tag.

### User Voice

Leave product feedback for the engineering team on [UserVoice](https://feedback.azure.com/d365community/forum/3887dc70-2025-ec11-b6e6-000d3a4f09d0).
