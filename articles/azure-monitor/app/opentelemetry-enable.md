---
title: Enable Azure Monitor OpenTelemetry for .NET, Java, Node.js, and Python applications
description: This article provides guidance on how to enable Azure Monitor on applications by using OpenTelemetry.
ms.topic: conceptual
ms.date: 06/04/2024
ms.devlang: csharp
# ms.devlang: csharp, javascript, typescript, python
ms.custom: devx-track-dotnet, devx-track-extended-java, devx-track-python
ms.reviewer: mmcc
---

# Enable Azure Monitor OpenTelemetry for .NET, Node.js, Python, and Java applications

This article describes how to enable and configure OpenTelemetry-based data collection to power the experiences within [Azure Monitor Application Insights](app-insights-overview.md#application-insights-overview). We walk through how to install the *Azure Monitor OpenTelemetry Distro*. The Azure Monitor OpenTelemetry Distro provides an [OpenTelemetry distribution](https://opentelemetry.io/docs/concepts/distributions/#what-is-a-distribution) that includes support for features specific to Azure Monitor. The Distro enables [automatic](opentelemetry-add-modify.md#automatic-data-collection) telemetry by including OpenTelemetry instrumentation libraries for collecting traces, metrics, logs, and exceptions, and allows collecting [custom](opentelemetry-add-modify.md#collect-custom-telemetry) telemetry. You can also use the [Live Metrics](live-stream.md) feature included in the Distro to monitor and collect more telemetry from live, in-production web applications. For more information about the advantages of using the Azure Monitor OpenTelemetry Distro, see [Why should I use the "Azure Monitor OpenTelemetry Distro"?](#why-should-i-use-the-azure-monitor-opentelemetry-distro).
 
To learn more about collecting data using OpenTelemetry, see [Data Collection Basics](opentelemetry-overview.md) or [OpenTelemetry FAQ](#frequently-asked-questions).

## OpenTelemetry Release Status

OpenTelemetry offerings are available for .NET, Node.js, Python, and Java applications.

> [!NOTE]
> - For a feature-by-feature release status, see the [FAQ](#whats-the-current-release-state-of-features-within-the-azure-monitor-opentelemetry-distro).
> - The second tab of this article covers all .NET scenarios, including classic ASP.NET, console apps, Windows Forms (WinForms), etc.

## Get started

Follow the steps in this section to instrument your application with OpenTelemetry.

### Prerequisites

- An Azure subscription: [Create an Azure subscription for free](https://azure.microsoft.com/free/)
- An Application Insights resource: [Create an Application Insights resource](create-workspace-resource.md#create-a-workspace-based-resource)

<!---NOTE TO CONTRIBUTORS: PLEASE DO NOT SEPARATE OUT JAVASCRIPT AND TYPESCRIPT INTO DIFFERENT TABS.--->

#### [ASP.NET Core](#tab/aspnetcore)

- [ASP.NET Core Application](/aspnet/core/introduction-to-aspnet-core) using an officially supported version of [.NET](https://dotnet.microsoft.com/download/dotnet)

> [!Tip]
> If you're migrating from the Application Insights Classic API, see our [migration documentation](./opentelemetry-dotnet-migrate.md).

### [.NET](#tab/net)

- Application using an officially supported version of [.NET Core](https://dotnet.microsoft.com/download/dotnet) or [.NET Framework](https://dotnet.microsoft.com/download/dotnet-framework) that's at least .NET Framework 4.6.2

> [!Tip]
> If you're migrating from the Application Insights Classic API, see our [migration documentation](./opentelemetry-dotnet-migrate.md).

### [Java](#tab/java)

- A Java application using Java 8+

#### [Java native](#tab/java-native)

- A Java application using GraalVM 17+

#### [Node.js](#tab/nodejs)

> [!NOTE]
> If you rely on any properties in the [not-supported table](https://github.com/microsoft/ApplicationInsights-node.js/blob/beta/README.md#ApplicationInsights-Shim-Unsupported-Properties), use the distro, and we'll provide a migration guide soon. If not, the App Insights shim is your easiest path forward when it's out of beta. 

- Application using an officially [supported version](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter#currently-supported-environments) of Node.js runtime:
  - [OpenTelemetry supported runtimes](https://github.com/open-telemetry/opentelemetry-js#supported-runtimes)
  - [Azure Monitor OpenTelemetry Exporter supported runtimes](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter#currently-supported-environments)

> [!Tip]
> If you're migrating from the Application Insights Classic API, see our [migration documentation](./opentelemetry-nodejs-migrate.md).

### [Python](#tab/python)

- Python Application using Python 3.8+

> [!Tip]
> If you're migrating from OpenCensus, see our [migration documentation](./opentelemetry-python-opencensus-migrate.md).

---

### Install the client library

#### [ASP.NET Core](#tab/aspnetcore)

Install the latest `Azure.Monitor.OpenTelemetry.AspNetCore` [NuGet package](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.AspNetCore):

```dotnetcli
dotnet add package Azure.Monitor.OpenTelemetry.AspNetCore 
```

#### [.NET](#tab/net)

Install the latest `Azure.Monitor.OpenTelemetry.Exporter` [NuGet package](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter):

```dotnetcli
dotnet add package Azure.Monitor.OpenTelemetry.Exporter 
```

#### [Java](#tab/java)

Download the [applicationinsights-agent-3.5.3.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.5.3/applicationinsights-agent-3.5.3.jar) file.

> [!WARNING]
>
> If you are upgrading from an earlier 3.x version, you may be impacted by changing defaults or slight differences in the data we collect. For more information, see the migration section in the release notes.
> [3.5.0](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.5.0),
> [3.4.0](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.4.0),
> [3.3.0](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.3.0),
> [3.2.0](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.2.0), and
> [3.1.0](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.1.0)

#### [Java native](#tab/java-native)

For Spring Boot native applications:
* [Import the OpenTelemetry Bills of Materials (BOM)](https://opentelemetry.io/docs/zero-code/java/spring-boot-starter/getting-started/).
* Add the [Spring Cloud Azure Starter Monitor](https://central.sonatype.com/artifact/com.azure.spring/spring-cloud-azure-starter-monitor) dependency.
* Follow [these instructions](/azure//developer/java/spring-framework/developer-guide-overview#configuring-spring-boot-3) for the Azure SDK JAR (Java Archive) files.

For Quarkus native applications:
* Add the [Quarkus OpenTelemetry Exporter for Azure](https://mvnrepository.com/artifact/io.quarkiverse.opentelemetry.exporter/quarkus-opentelemetry-exporter-azure) dependency.

#### [Node.js](#tab/nodejs)

Install these packages:

- [@azure/monitor-opentelemetry](https://www.npmjs.com/package/@azure/monitor-opentelemetry)

```sh
npm install @azure/monitor-opentelemetry
```

The following packages are also used for some specific scenarios described later in this article:

- [@opentelemetry/api](https://www.npmjs.com/package/@opentelemetry/api)
- [@opentelemetry/sdk-metrics](https://www.npmjs.com/package/@opentelemetry/sdk-metrics)
- [@opentelemetry/resources](https://www.npmjs.com/package/@opentelemetry/resources)
- [@opentelemetry/semantic-conventions](https://www.npmjs.com/package/@opentelemetry/semantic-conventions)
- [@opentelemetry/sdk-trace-base](https://www.npmjs.com/package/@opentelemetry/sdk-trace-base)

```sh
npm install @opentelemetry/api
npm install @opentelemetry/sdk-metrics
npm install @opentelemetry/resources
npm install @opentelemetry/semantic-conventions
npm install @opentelemetry/sdk-trace-base
```

#### [Python](#tab/python)

Install the latest [azure-monitor-opentelemetry](https://pypi.org/project/azure-monitor-opentelemetry/) PyPI package:

```sh
pip install azure-monitor-opentelemetry
```

---

### Enable Azure Monitor Application Insights

To enable Azure Monitor Application Insights, you make a minor modification to your application and set your "Connection String." The Connection String tells your application where to send the telemetry the Distro collects, and it's unique to you.

#### Modify your Application

##### [ASP.NET Core](#tab/aspnetcore)

Add `UseAzureMonitor()` to your application startup, located in your `program.cs` class.

```csharp
// Import the Azure.Monitor.OpenTelemetry.AspNetCore namespace.
using Azure.Monitor.OpenTelemetry.AspNetCore;

// Create a new WebApplicationBuilder instance.
var builder = WebApplication.CreateBuilder(args);

// Add OpenTelemetry and configure it to use Azure Monitor.
builder.Services.AddOpenTelemetry().UseAzureMonitor();

// Build the application.
var app = builder.Build();

// Run the application.
app.Run();
```

##### [.NET](#tab/net)

Add the Azure Monitor Exporter to each OpenTelemetry signal in application startup. Depending on your version of .NET, it is in either your `startup.cs` or `program.cs` class.

```csharp
// Create a new tracer provider builder and add an Azure Monitor trace exporter to the tracer provider builder.
// It is important to keep the TracerProvider instance active throughout the process lifetime.
// See https://github.com/open-telemetry/opentelemetry-dotnet/tree/main/docs/trace#tracerprovider-management
var tracerProvider = Sdk.CreateTracerProviderBuilder()
    .AddAzureMonitorTraceExporter();

// Add an Azure Monitor metric exporter to the metrics provider builder.
// It is important to keep the MetricsProvider instance active throughout the process lifetime.
// See https://github.com/open-telemetry/opentelemetry-dotnet/tree/main/docs/metrics#meterprovider-management
var metricsProvider = Sdk.CreateMeterProviderBuilder()
    .AddAzureMonitorMetricExporter();

// Create a new logger factory.
// It is important to keep the LoggerFactory instance active throughout the process lifetime.
// See https://github.com/open-telemetry/opentelemetry-dotnet/tree/main/docs/logs#logger-management
var loggerFactory = LoggerFactory.Create(builder =>
{
    builder.AddOpenTelemetry(options =>
    {
        options.AddAzureMonitorLogExporter();
    });
});
```

> [!NOTE]
> For more information, see the [getting-started tutorial for OpenTelemetry .NET](https://github.com/open-telemetry/opentelemetry-dotnet/tree/main#getting-started)

##### [Java](#tab/java)

Java autoinstrumentation is enabled through configuration changes; no code changes are required.

Point the Java virtual machine (JVM) to the jar file by adding `-javaagent:"path/to/applicationinsights-agent-3.5.3.jar"` to your application's JVM args.

> [!TIP]
> Sampling is enabled by default at a rate of 5 requests per second, aiding in cost management. Telemetry data may be missing in scenarios exceeding this rate. For more information on modifying sampling configuration, see [sampling overrides](./java-standalone-sampling-overrides.md).

> [!TIP]
> For scenario-specific guidance, see [Get Started (Supplemental)](./java-get-started-supplemental.md).

> [!TIP]
> If you develop a Spring Boot application, you can optionally replace the JVM argument by a programmatic configuration. For more information, see [Using Azure Monitor Application Insights with Spring Boot](./java-spring-boot.md).

##### [Java native](#tab/java-native)

Several automatic instrumentations are enabled through configuration changes; no code changes are required

##### [Node.js](#tab/nodejs)

```typescript
// Import the `useAzureMonitor()` function from the `@azure/monitor-opentelemetry` package.
const { useAzureMonitor } = require("@azure/monitor-opentelemetry");

// Call the `useAzureMonitor()` function to configure OpenTelemetry to use Azure Monitor.
useAzureMonitor();
```

##### [Python](#tab/python)

```python
# Import the `configure_azure_monitor()` function from the
# `azure.monitor.opentelemetry` package.
from azure.monitor.opentelemetry import configure_azure_monitor

# Import the tracing api from the `opentelemetry` package.
from opentelemetry import trace

# Configure OpenTelemetry to use Azure Monitor with the 
# APPLICATIONINSIGHTS_CONNECTION_STRING environment variable.
configure_azure_monitor()

```

---

#### Copy the Connection String from your Application Insights Resource

> [!TIP]
> If you don't already have one, now is a great time to [Create an Application Insights Resource](create-workspace-resource.md#create-a-workspace-based-resource). Here's when we recommend you [create a new Application Insights Resource versus use an existing one](create-workspace-resource.md#when-to-use-a-single-application-insights-resource).

To copy your unique Connection String:

:::image type="content" source="media/migrate-from-instrumentation-keys-to-connection-strings/migrate-from-instrumentation-keys-to-connection-strings.png" alt-text="Screenshot that shows Application Insights overview and connection string." lightbox="media/migrate-from-instrumentation-keys-to-connection-strings/migrate-from-instrumentation-keys-to-connection-strings.png":::

1. Go to the **Overview** pane of your Application Insights resource.
2. Find your **Connection String**.
3. Hover over the connection string and select the **Copy to clipboard** icon.

#### Paste the Connection String in your environment

To paste your Connection String, select from the following options:

  A. Set via Environment Variable (Recommended)
  
  Replace `<Your Connection String>` in the following command with *your* unique connection string.

   ```console
   APPLICATIONINSIGHTS_CONNECTION_STRING=<Your Connection String>
   ```

  B. Set via Configuration File - Java Only (Recommended)
  
  Create a configuration file named `applicationinsights.json`, and place it in the same directory as `applicationinsights-agent-3.5.3.jar` with the following content:

   ```json
   {
     "connectionString": "<Your Connection String>"
   }
   ```

  Replace `<Your Connection String>` in the preceding JSON with *your* unique connection string.

  C. Set via Code - ASP.NET Core, Node.js, and Python Only (Not recommended)
  
  See [Connection String Configuration](opentelemetry-configuration.md#connection-string) for an example of setting Connection String via code.

  > [!NOTE]
  > If you set the connection string in more than one place, we adhere to the following precedence:
  >
  > 1. Code
  > 2. Environment Variable
  > 3. Configuration File

#### Confirm data is flowing

Run your application and open your **Application Insights Resource** tab in the Azure portal. It might take a few minutes for data to show up in the portal.

:::image type="content" source="media/opentelemetry/server-requests.png" alt-text="Screenshot of the Application Insights Overview tab with server requests and server response time highlighted.":::

Application Insights is now enabled for your application. All the following steps are optional and allow for further customization.

> [!IMPORTANT]
> If you have two or more services that emit telemetry to the same Application Insights resource, you're required to [set Cloud Role Names](opentelemetry-configuration.md#set-the-cloud-role-name-and-the-cloud-role-instance) to represent them properly on the Application Map.

As part of using Application Insights instrumentation, we collect and send diagnostic data to Microsoft. This data helps us run and improve Application Insights. To learn more, see [Statsbeat in Azure Application Insights](./statsbeat.md).

## Samples

Azure Monitor OpenTelemetry sample applications are available for all supported languages.

### [ASP.NET Core](#tab/aspnetcore)

- [ASP.NET Core sample app](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/monitor/Azure.Monitor.OpenTelemetry.AspNetCore/tests/Azure.Monitor.OpenTelemetry.AspNetCore.Demo)

### [.NET](#tab/net)

- [NET sample app](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/monitor/Azure.Monitor.OpenTelemetry.Exporter/tests/Azure.Monitor.OpenTelemetry.Exporter.Demo)

### [Java](#tab/java)

- [Java sample apps](https://github.com/Azure-Samples/ApplicationInsights-Java-Samples)

### [Java native](#tab/java-native)

- [Java GraalVM native sample apps](https://github.com/Azure-Samples/java-native-telemetry)

### [Node.js](#tab/nodejs)

- [Node.js sample app](https://github.com/Azure-Samples/azure-monitor-opentelemetry-node.js)

### [Python](#tab/python)

- [Python sample apps](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/monitor/azure-monitor-opentelemetry/samples)

---

## Next steps

### [ASP.NET Core](#tab/aspnetcore)

- For details on adding and modifying Azure Monitor OpenTelemetry, see [Add and modify Azure Monitor OpenTelemetry](opentelemetry-add-modify.md).
- To further configure the OpenTelemetry distro, see [Azure Monitor OpenTelemetry configuration](opentelemetry-configuration.md).
- To review the source code, see the [Azure Monitor AspNetCore GitHub repository](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/monitor/Azure.Monitor.OpenTelemetry.AspNetCore).
- To install the NuGet package, check for updates, or view release notes, see the [Azure Monitor AspNetCore NuGet Package](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.AspNetCore) page.
- To become more familiar with Azure Monitor and OpenTelemetry, see the [Azure Monitor Example Application](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/monitor/Azure.Monitor.OpenTelemetry.AspNetCore/tests/Azure.Monitor.OpenTelemetry.AspNetCore.Demo).
- To learn more about OpenTelemetry and its community, see the [OpenTelemetry .NET GitHub repository](https://github.com/open-telemetry/opentelemetry-dotnet).
- To enable usage experiences, [enable web or browser user monitoring](javascript.md).

### [.NET](#tab/net)

- For details on adding and modifying Azure Monitor OpenTelemetry, see [Add and modify Azure Monitor OpenTelemetry](opentelemetry-add-modify.md).
- To further configure the OpenTelemetry distro, see [Azure Monitor OpenTelemetry configuration](opentelemetry-configuration.md).
- To review the source code, see the [Azure Monitor Exporter GitHub repository](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/monitor/Azure.Monitor.OpenTelemetry.Exporter).
- To install the NuGet package, check for updates, or view release notes, see the [Azure Monitor Exporter NuGet Package](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter) page.
- To become more familiar with Azure Monitor and OpenTelemetry, see the [Azure Monitor Example Application](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/monitor/Azure.Monitor.OpenTelemetry.Exporter/tests/Azure.Monitor.OpenTelemetry.Exporter.Demo).
- To learn more about OpenTelemetry and its community, see the [OpenTelemetry .NET GitHub repository](https://github.com/open-telemetry/opentelemetry-dotnet).
- To enable usage experiences, [enable web or browser user monitoring](javascript.md).

### [Java](#tab/java)

- See [Add and modify Azure Monitor OpenTelemetry](opentelemetry-add-modify.md) for details on adding and modifying Azure Monitor OpenTelemetry.
- Review [Java autoinstrumentation configuration options](java-standalone-config.md).
- Review the source code in the [Azure Monitor Java autoinstrumentation GitHub repository](https://github.com/Microsoft/ApplicationInsights-Java).
- Learn more about OpenTelemetry and its community in the [OpenTelemetry Java GitHub repository](https://github.com/open-telemetry/opentelemetry-java-instrumentation).
- Enable usage experiences by seeing [Enable web or browser user monitoring](javascript.md).
- Review the [release notes](https://github.com/microsoft/ApplicationInsights-Java/releases) on GitHub.

### [Java native](#tab/java-native)
- See [Add and modify Azure Monitor OpenTelemetry](opentelemetry-add-modify.md) for details on adding and modifying Azure Monitor OpenTelemetry.
- Review the source code in the [Azure Monitor OpenTelemetry Distro in Spring Boot native image Java application](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/spring/spring-cloud-azure-starter-monitor) and [Quarkus OpenTelemetry Exporter for Azure](https://github.com/quarkiverse/quarkus-opentelemetry-exporter/tree/main/quarkus-opentelemetry-exporter-azure).
- Learn more about OpenTelemetry and its community in the [OpenTelemetry Java GitHub repository](https://github.com/open-telemetry/opentelemetry-java-instrumentation).
- Learn more features for Spring Boot native image applications in [OpenTelemetry SpringBoot starter](https://opentelemetry.io/docs/zero-code/java/spring-boot-starter/.)
- Learn more features for Quarkus native applications in [Quarkus OpenTelemetry Exporter for Azure](https://quarkus.io/guides/opentelemetry).
- Review the [release notes](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/spring/spring-cloud-azure-starter-monitor/CHANGELOG.md) on GitHub.

### [Node.js](#tab/nodejs)

- For details on adding and modifying Azure Monitor OpenTelemetry, see [Add and modify Azure Monitor OpenTelemetry](opentelemetry-add-modify.md).
- To review the source code, see the [Azure Monitor OpenTelemetry GitHub repository](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry).
- To install the npm package and check for updates, see the [`@azure/monitor-opentelemetry` npm Package](https://www.npmjs.com/package/@azure/monitor-opentelemetry) page.
- To become more familiar with Azure Monitor Application Insights and OpenTelemetry, see the [Azure Monitor Example Application](https://github.com/Azure-Samples/azure-monitor-opentelemetry-node.js).
- To learn more about OpenTelemetry and its community, see the [OpenTelemetry JavaScript GitHub repository](https://github.com/open-telemetry/opentelemetry-js).
- To enable usage experiences, [enable web or browser user monitoring](javascript.md).

### [Python](#tab/python)

- See [Add and modify Azure Monitor OpenTelemetry](opentelemetry-add-modify.md) for details on adding and modifying Azure Monitor OpenTelemetry.
- Review the source code and extra documentation in the [Azure Monitor Distro GitHub repository](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/monitor/azure-monitor-opentelemetry/README.md).
- See extra samples and use cases in [Azure Monitor Distro samples](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/monitor/azure-monitor-opentelemetry/samples).
- Review the [changelog](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/monitor/azure-monitor-opentelemetry/CHANGELOG.md) on GitHub.
- Install the PyPI package, check for updates, or view release notes on the [Azure Monitor Distro PyPI Package](https://pypi.org/project/azure-monitor-opentelemetry/) page.
- Become more familiar with Azure Monitor Application Insights and OpenTelemetry in the [Azure Monitor Example Application](https://github.com/Azure-Samples/azure-monitor-opentelemetry-python).
- Learn more about OpenTelemetry and its community in the [OpenTelemetry Python GitHub repository](https://github.com/open-telemetry/opentelemetry-python).
- See available OpenTelemetry instrumentations and components in the [OpenTelemetry Contributor Python GitHub repository](https://github.com/open-telemetry/opentelemetry-python-contrib).
- Enable usage experiences by [enabling web or browser user monitoring](javascript.md).

---

[!INCLUDE [azure-monitor-app-insights-opentelemetry-faqs](../includes/azure-monitor-app-insights-opentelemetry-faqs.md)]

[!INCLUDE [azure-monitor-app-insights-opentelemetry-support](../includes/azure-monitor-app-insights-opentelemetry-support.md)]
