---
title: Real-time meeting transcription quickstart - Speech service
titleSuffix: Azure AI services
description: In this quickstart, learn how to transcribe meetings. You can add, remove, and identify multiple participants by streaming audio to the Speech service.
author: eric-urban
manager: nitinme
ms.service: azure-ai-speech
ms.topic: quickstart
ms.date: 1/21/2024
ms.author: eur
zone_pivot_groups: acs-js-csharp-python
ms.custom: cogserv-non-critical-speech, references_regions, devx-track-extended-java, devx-track-js, devx-track-python
---

# Quickstart: Real-time meeting transcription

You can transcribe meetings with the ability to add, remove, and identify multiple participants by streaming audio to the Speech service. You first create voice signatures for each participant using the REST API, and then use the voice signatures with the Speech SDK to transcribe meetings. See the meeting transcription [overview](meeting-transcription.md) for more information.

## Limitations

* Only available in the following subscription regions: `centralus`, `eastasia`, `eastus`, `westeurope`
* Requires a 7-mic circular multi-microphone array. The microphone array should meet [our specification](./speech-sdk-microphone.md).

> [!NOTE]
> The Speech SDK for C++, Java, Objective-C, and Swift support meeting transcription, but we haven't yet included a guide here. 

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/meeting-transcription/real-time-javascript.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/meeting-transcription/real-time-csharp.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/meeting-transcription/real-time-python.md)]
::: zone-end

## Next steps

> [!div class="nextstepaction"]
> [Asynchronous meeting transcription](how-to-async-meeting-transcription.md)
