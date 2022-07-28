---
title: Azure Blob storage trigger for Azure Functions
description: Learn how to run an Azure Function as Azure Blob storage data changes.
ms.topic: reference
ms.date: 03/04/2022
ms.devlang: csharp, java, javascript, powershell, python
ms.custom: "devx-track-csharp, devx-track-python"
zone_pivot_groups: programming-languages-set-functions-lang-workers
---

# Azure Blob storage trigger for Azure Functions

The Blob storage trigger starts a function when a new or updated blob is detected. The blob contents are provided as [input to the function](./functions-bindings-storage-blob-input.md).

The Azure Blob storage trigger requires a general-purpose storage account. Storage V2 accounts with [hierarchical namespaces](../storage/blobs/data-lake-storage-namespace.md) are also supported. To use a blob-only account, or if your application has specialized needs, review the alternatives to using this trigger.

For information on setup and configuration details, see the [overview](./functions-bindings-storage-blob.md).

## Example

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-bindings-csharp-intro](../../includes/functions-bindings-csharp-intro.md)]

# [In-process](#tab/in-process)

The following example shows a [C# function](functions-dotnet-class-library.md) that writes a log when a blob is added or updated in the `samples-workitems` container.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

The string `{name}` in the blob trigger path `samples-workitems/{name}` creates a [binding expression](./functions-bindings-expressions-patterns.md) that you can use in function code to access the file name of the triggering blob. For more information, see [Blob name patterns](#blob-name-patterns) later in this article.

For more information about the `BlobTrigger` attribute, see [Attributes](#attributes).

# [Isolated process](#tab/isolated-process)

The following example is a [C# function](dotnet-isolated-process-guide.md) that runs in an isolated process and uses a blob trigger with both blob input and blob output blob bindings. The function is triggered by the creation of a blob in the *test-samples-trigger* container. It reads a text file from the *test-samples-input* container and creates a new text file in an output container based on the name of the triggered file.

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Blob/BlobFunction.cs" range="9-25":::

# [C# Script](#tab/csharp-script)

The following example shows a blob trigger binding in a *function.json* file and code that uses the binding. The function writes a log when a blob is added or updated in the `samples-workitems` [container](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Here's the binding data in the *function.json* file:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

The string `{name}` in the blob trigger path `samples-workitems/{name}` creates a [binding expression](./functions-bindings-expressions-patterns.md) that you can use in function code to access the file name of the triggering blob. For more information, see [Blob name patterns](#blob-name-patterns) later in this article.

For more information about *function.json* file properties, see the [Configuration](#configuration) section explains these properties.

Here's C# script code that binds to a `Stream`:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Here's C# script code that binds to a `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

---

::: zone-end
::: zone pivot="programming-language-java"

This function writes a log when a blob is added or updated in the `myblob` container.

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```

::: zone-end  
::: zone pivot="programming-language-javascript"  

The following example shows a blob trigger binding in a *function.json* file and [JavaScript code](functions-reference-node.md) that uses the binding. The function writes a log when a blob is added or updated in the `samples-workitems` container.

Here's the *function.json* file:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

The string `{name}` in the blob trigger path `samples-workitems/{name}` creates a [binding expression](./functions-bindings-expressions-patterns.md) that you can use in function code to access the file name of the triggering blob. For more information, see [Blob name patterns](#blob-name-patterns) later in this article.

For more information about *function.json* file properties, see the [Configuration](#configuration) section explains these properties.

Here's the JavaScript code:

```javascript
module.exports = async function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
};
```

::: zone-end  
::: zone pivot="programming-language-powershell"  

The following example demonstrates how to create a function that runs when a file is added to `source` blob storage container.

The function configuration file (_function.json_) includes a binding with the `type` of `blobTrigger` and `direction` set to `in`.

```json
{
  "bindings": [
    {
      "name": "InputBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "source/{name}",
      "connection": "MyStorageAccountConnectionString"
    }
  ]
}
```

Here's the associated code for the _run.ps1_ file.

```powershell
param([byte[]] $InputBlob, $TriggerMetadata)

Write-Host "PowerShell Blob trigger: Name: $($TriggerMetadata.Name) Size: $($InputBlob.Length) bytes"
```

::: zone-end  
::: zone pivot="programming-language-python"  

The following example shows a blob trigger binding in a *function.json* file and [Python code](functions-reference-python.md) that uses the binding. The function writes a log when a blob is added or updated in the `samples-workitems` [container](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Here's the *function.json* file:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

The string `{name}` in the blob trigger path `samples-workitems/{name}` creates a [binding expression](./functions-bindings-expressions-patterns.md) that you can use in function code to access the file name of the triggering blob. For more information, see [Blob name patterns](#blob-name-patterns) later in this article.

For more information about *function.json* file properties, see the [Configuration](#configuration) section explains these properties.

Here's the Python code:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

::: zone-end  
::: zone pivot="programming-language-csharp"
## Attributes

Both [in-process](functions-dotnet-class-library.md) and [isolated process](dotnet-isolated-process-guide.md) C# libraries use the [BlobAttribute](/dotnet/api/microsoft.azure.webjobs.blobattribute) attribute to define the function. C# script instead uses a function.json configuration file.

The attribute's constructor takes the following parameters:

|Parameter | Description|
|---------|----------------------|
|**BlobPath** | The path to the blob.|
|**Connection** | The name of an app setting or setting collection that specifies how to connect to Azure Blobs. See [Connections](#connections).|
|**Access** | Indicates whether you will be reading or writing.|

# [In-process](#tab/in-process)

In [C# class libraries](functions-dotnet-class-library.md), the attribute's constructor takes a path string that indicates the container to watch and optionally a [blob name pattern](#blob-name-patterns). Here's an example:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
  [BlobTrigger("sample-images/{name}")] Stream image,
  [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
  ....
}
```

[!INCLUDE [functions-bindings-storage-attribute](../../includes/functions-bindings-storage-attribute.md)]

# [Isolated process](#tab/isolated-process)

Here's an `BlobTrigger` attribute in a method signature:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Blob/BlobFunction.cs" range="11-16":::


# [C# script](#tab/csharp-script)

C# script uses a *function.json* file for configuration instead of attributes.

|function.json property | Description|
|---------|----------------------|
|**type** | Must be set to `blobTrigger`. This property is set automatically when you create the trigger in the Azure portal.|
|**direction** | Must be set to `in`. This property is set automatically when you create the trigger in the Azure portal. Exceptions are noted in the [usage](#usage) section. |
|**name** | The name of the variable that represents the blob in function code. |
|**path** | The [container](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) to monitor.  May be a [blob name pattern](#blob-name-patterns). |
|**connection** | The name of an app setting or setting collection that specifies how to connect to Azure Blobs. See [Connections](#connections).|

---

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

::: zone-end  
::: zone pivot="programming-language-java"  
## Annotations

The `@BlobTrigger` attribute is used to give you access to the blob that triggered the function. Refer to the [trigger example](#example) for details.
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"  
## Configuration

The following table explains the binding configuration properties that you set in the *function.json* file.

|function.json property |Description|
|---------|----------------------|
|**type** | Must be set to `blobTrigger`. This property is set automatically when you create the trigger in the Azure portal.|
|**direction** | Must be set to `in`. This property is set automatically when you create the trigger in the Azure portal. Exceptions are noted in the [usage](#usage) section. |
|**name** |  The name of the variable that represents the blob in function code. |
|**path** | The [container](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) to monitor.  May be a [blob name pattern](#blob-name-patterns). |
|**connection** | The name of an app setting or setting collection that specifies how to connect to Azure Blobs. See [Connections](#connections).|

::: zone-end  

See the [Example section](#example) for complete examples.

::: zone pivot="programming-language-csharp"
## Metadata

The blob trigger provides several metadata properties. These properties can be used as part of binding expressions in other bindings or as parameters in your code. These values have the same semantics as the [Cloud​Blob](/dotnet/api/microsoft.azure.storage.blob.cloudblob) type.

|Property  |Type  |Description  |
|----------|---------|---------|
|`BlobTrigger`|`string`|The path to the triggering blob.|
|`Uri`|`System.Uri`|The blob's URI for the primary location.|
|`Properties` |[BlobProperties](/dotnet/api/microsoft.azure.storage.blob.blobproperties)|The blob's system properties. |
|`Metadata` |`IDictionary<string,string>`|The user-defined metadata for the blob.|

The following example logs the path to the triggering blob, including the container:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
::: zone-end
::: zone pivot="programming-language-javascript"  
## Metadata


The blob trigger provides several metadata properties. These properties can be used as part of binding expressions in other bindings or as parameters in your code. 

|Property     |Description  |
|-------------|--------|
|`blobTrigger`|The path to the triggering blob.|
|`uri`        |The blob's URI for the primary location.|
|`properties` |The blob's system properties. |
|`metadata`   |The user-defined metadata for the blob.|

Metadata can be obtained from the `bindingData` property of the supplied `context` object, as shown in the following example, which logs the path to the triggering blob (`blobTrigger`), including the container:

```javascript
module.exports = async function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
};
```

::: zone-end  
::: zone pivot="programming-language-powershell"  
## Metadata

Metadata is available through the `$TriggerMetadata` parameter.
::: zone-end  

## Usage

::: zone pivot="programming-language-csharp"  

The usage of the Blob trigger depends on the extension package version, and the C# modality used in your function app, which can be one of the following:

[!INCLUDE [functions-bindings-blob-storage-usage-csharp](../../includes/functions-bindings-blob-storage-usage-csharp.md)]

::: zone-end  

::: zone pivot="programming-language-java"
The `@BlobTrigger` attribute is used to give you access to the blob that triggered the function. Refer to the [trigger example](#example) for details.
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell"  
Access blob data using `context.bindings.<NAME>` where `<NAME>` matches the value defined in *function.json*.
::: zone-end  
::: zone pivot="programming-language-powershell"  
Access the blob data via a parameter that matches the name designated by binding's name parameter in the _function.json_ file.
::: zone-end  
::: zone pivot="programming-language-python"  
Access blob data via the parameter typed as [InputStream](/python/api/azure-functions/azure.functions.inputstream). Refer to the [trigger example](#example) for details.
::: zone-end  

[!INCLUDE [functions-storage-blob-connections](../../includes/functions-storage-blob-connections.md)]

## Blob name patterns

You can specify a blob name pattern in the `path` property in *function.json* or in the `BlobTrigger` attribute constructor. The name pattern can be a [filter or binding expression](./functions-bindings-expressions-patterns.md). The following sections provide examples.

> [!TIP]
> A container name can't contain a resolver in the name pattern.

### Get file name and extension

The following example shows how to bind to the blob file name and extension separately:

```json
"path": "input/{blobname}.{blobextension}",
```

If the blob is named *original-Blob1.txt*, the values of the `blobname` and `blobextension` variables in function code are *original-Blob1* and *txt*.

### Filter on blob name

The following example triggers only on blobs in the `input` container that start with the string "original-":

```json
"path": "input/original-{name}",
```

If the blob name is *original-Blob1.txt*, the value of the `name` variable in function code is `Blob1.txt`.

### Filter on file type

The following example triggers only on *.png* files:

```json
"path": "samples/{name}.png",
```

### Filter on curly braces in file names

To look for curly braces in file names, escape the braces by using two braces. The following example filters for blobs that have curly braces in the name:

```json
"path": "images/{{20140101}}-{name}",
```

If the blob is named *{20140101}-soundfile.mp3*, the `name` variable value in the function code is *soundfile.mp3*.



## Polling

Polling works as a hybrid between inspecting logs and running periodic container scans. Blobs are scanned in groups of 10,000 at a time with a continuation token used between intervals.

> [!WARNING]
> In addition, [storage logs are created on a "best effort"](/rest/api/storageservices/About-Storage-Analytics-Logging) basis. There's no guarantee that all events are captured. Under some conditions, logs may be missed.
> 
> If you require faster or more reliable blob processing, consider creating a [queue message](../storage/queues/storage-dotnet-how-to-use-queues.md) when you create the blob. Then use a [queue trigger](functions-bindings-storage-queue.md) instead of a blob trigger to process the blob. Another option is to use Event Grid; see the tutorial [Automate resizing uploaded images using Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## Alternatives

### Event Grid trigger

> [!NOTE]
> When using Storage Extensions 5.x and higher, the Blob trigger has built-in support for an Event Grid based Blob trigger. For more information, see the [Storage extension 5.x and higher](#storage-extension-5x-and-higher) section below.

The [Event Grid trigger](functions-bindings-event-grid.md) also has built-in support for [blob events](../storage/blobs/storage-blob-event-overview.md). Use Event Grid instead of the Blob storage trigger for the following scenarios:

- **Blob-only storage accounts**: [Blob-only storage accounts](../storage/common/storage-account-overview.md#types-of-storage-accounts) are supported for blob input and output bindings but not for blob triggers.

- **High-scale**: High scale can be loosely defined as containers that have more than 100,000 blobs in them or storage accounts that have more than 100 blob updates per second.

- **Existing Blobs**: The blob trigger will process all existing blobs in the container when you set up the trigger. If you have a container with many existing blobs and only want to trigger for new blobs, use the Event Grid trigger.

- **Minimizing latency**: If your function app is on the Consumption plan, there can be up to a 10-minute delay in processing new blobs if a function app has gone idle. To avoid this latency, you can switch to an App Service plan with Always On enabled. You can also use an [Event Grid trigger](functions-bindings-event-grid.md) with your Blob storage account. For an example, see the [Event Grid tutorial](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

See the [Image resize with Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md) tutorial of an Event Grid example.

#### Storage Extension 5.x and higher

When using the storage extension, there is built-in support for Event Grid in the Blob trigger, which requires setting the `source` parameter to Event Grid in your existing Blob trigger. 

For more information on how to use the Blob Trigger based on Event Grid, refer to the [Event Grid Blob Trigger guide](./functions-event-grid-blob-trigger.md).

### Queue storage trigger

Another approach to processing blobs is to write queue messages that correspond to blobs being created or modified and then use a [Queue storage trigger](./functions-bindings-storage-queue.md) to begin processing.

## Blob receipts

The Azure Functions runtime ensures that no blob trigger function gets called more than once for the same new or updated blob. To determine if a given blob version has been processed, it maintains *blob receipts*.

Azure Functions stores blob receipts in a container named *azure-webjobs-hosts* in the Azure storage account for your function app (defined by the app setting `AzureWebJobsStorage`). A blob receipt has the following information:

- The triggered function (`<FUNCTION_APP_NAME>.Functions.<FUNCTION_NAME>`, for example: `MyFunctionApp.Functions.CopyBlob`)
- The container name
- The blob type (`BlockBlob` or `PageBlob`)
- The blob name
- The ETag (a blob version identifier, for example: `0x8D1DC6E70A277EF`)

To force reprocessing of a blob, delete the blob receipt for that blob from the *azure-webjobs-hosts* container manually. While reprocessing might not occur immediately, it's guaranteed to occur at a later point in time. To reprocess immediately, the *scaninfo* blob in *azure-webjobs-hosts/blobscaninfo* can be updated. Any blobs with a last modified timestamp after the `LatestScan` property will be scanned again.

## Poison blobs

When a blob trigger function fails for a given blob, Azure Functions retries that function a total of five times by default.

If all 5 tries fail, Azure Functions adds a message to a Storage queue named *webjobs-blobtrigger-poison*. The maximum number of retries is configurable. The same MaxDequeueCount setting is used for poison blob handling and poison queue message handling. The queue message for poison blobs is a JSON object that contains the following properties:

- FunctionId (in the format `<FUNCTION_APP_NAME>.Functions.<FUNCTION_NAME>`)
- BlobType (`BlockBlob` or `PageBlob`)
- ContainerName
- BlobName
- ETag (a blob version identifier, for example: `0x8D1DC6E70A277EF`)

## Concurrency and memory usage

The blob trigger uses a queue internally, so the maximum number of concurrent function invocations is controlled by the [queues configuration in host.json](functions-host-json.md#queues). The default settings limit concurrency to 24 invocations. This limit applies separately to each function that uses a blob trigger.

> [!NOTE]
> For apps using the 5.0.0 or higher version of the Storage extension, the queues configuration in host.json only applies to queue triggers. The blob trigger concurrency is instead controlled by [blobs configuration in host.json](functions-host-json.md#blobs).

[The Consumption plan](event-driven-scaling.md) limits a function app on one virtual machine (VM) to 1.5 GB of memory. Memory is used by each concurrently executing function instance and by the Functions runtime itself. If a blob-triggered function loads the entire blob into memory, the maximum memory used by that function just for blobs is 24 * maximum blob size. For example, a function app with three blob-triggered functions and the default settings would have a maximum per-VM concurrency of 3*24 = 72 function invocations.

JavaScript and Java functions load the entire blob into memory, and C# functions do that if you bind to `string`, or `Byte[]`.

## host.json properties

The [host.json](functions-host-json.md#blobs) file contains settings that control blob trigger behavior. See the [host.json settings](functions-bindings-storage-blob.md#hostjson-settings) section for details regarding available settings.

## Next steps

- [Read blob storage data when a function runs](./functions-bindings-storage-blob-input.md)
- [Write blob storage data from a function](./functions-bindings-storage-blob-output.md)
