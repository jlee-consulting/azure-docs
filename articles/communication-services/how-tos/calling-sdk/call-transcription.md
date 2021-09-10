---
title: Display call transcription state on the client
titleSuffix: An Azure Communication Services how-to guide
description: Use Azure Communication Services SDKs to display the call transcription state
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.topic: how-to 
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-ios-android

#Customer intent: As a developer, I want to display the call transcription state on the client.
---

# Display call transcription state on the client

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

When using call transcription you may want to let your users know that a call is being transcribe. Here's how.

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- A deployed Communication Services resource. [Create a Communication Services resource](../../quickstarts/create-communication-resource.md).
- A user access token to enable the calling client. For more information, see [Create and manage access tokens](../../quickstarts/access-tokens.md).
- Optional: Complete the quickstart to [add voice calling to your application](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

::: zone pivot="platform-android"
[!INCLUDE [Call transcription client-side Android](./includes/call-transcription/call-transcription-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Call transcription client-side iOS](./includes/call-transcription/call-transcription-ios.md)]
::: zone-end

## Next steps
- [Learn how to manage video](./manage-video.md)
- [Learn how to manage calls](./manage-calls.md)
- [Learn how to record calls](./record-calls.md)