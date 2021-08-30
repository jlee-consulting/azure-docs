---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/15/2021
ms.author: glenga
---

In a C# project, the bindings are defined as binding attributes on the function method. Specific definitions depend on whether your app runs in-process (C# class library) or in an isolated process.  

# [In-process](#tab/in-process)

Open the *HttpExample.cs* project file and add the following parameter to the `Run` method definition:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

The `msg` parameter is an `ICollector<T>` type, which represents a collection of messages that are written to an output binding when the function completes. In this case, the output is a storage queue named `outqueue`. The connection string for the Storage account is set by the `StorageAccountAttribute`. This attribute indicates the setting that contains the Storage account connection string and can be applied at the class, method, or parameter level. In this case, you could omit `StorageAccountAttribute` because you are already using the default storage account.

The Run method definition should now look like the following:  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::

# [Isolated process](#tab/isolated-process)

Open the *HttpExample.cs* project file and add the following `MultiResponse` class:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-isolated/HttpExample.cs" range="33-38":::

The `MultiResponse` class allows you to write to both a storage queue named `outqueue` and an HTTP success message. Because the `QueueOutput` attribute is applied to a string array, multiple messages could be sent to the queue. 

The connection string for the Storage account is set by the `Connection` property. In this case, you could omit `Connection` because you are already using the default storage account.

---