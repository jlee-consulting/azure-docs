---
title: Media streaming quickstart
titleSuffix: An Azure Communication Services quickstart document
description: Provides a quick start for developers to get audio streams through media streaming APIs from ACS calls.
author: kunaal
ms.service: azure-communication-services
ms.topic: include
ms.date: 10/25/2022
ms.author: kpunjabi
ms.custom: private_preview
services: azure-communication-services
zone_pivot_groups: acs-csharp-java
---

# Quickstart: Subscribing to audio streams from an ongoing call

> [!IMPORTANT]
> Functionality described on this document is currently in private preview. Private preview includes access to SDKs and documentation for testing purposes that are not yet available publicly.
> Apply to become an early adopter by filling out the form for [preview access to Azure Communication Services](https://aka.ms/ACS-EarlyAdopter).

Get started with using audio streams through Azure Communication Services Media Streaming API. This quickstart assumes you're already familiar with Call Automation APIs to build an automated call routing solution. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [Media Streaming with .NET](./includes/call-automation-media/media-streaming-quickstart-csharp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Media Streaming with Java](./includes/call-automation-media/media-streaming-quickstart-java.md)]
::: zone-end


## Message schema
When ACS has received the URL for your WebSocket server, it will create a connection to it. Once ACS has successfully connected to your WebSocket server, it will send through the first data packet which contains metadata regarding the incoming media packets.

``` code
/**
 * The first message upon WebSocket connection will be the metadata packet
 * which contains the subscriptionId and audio format
 */
public class AudioMetadataSample {
    public string kind; // What kind of data this is, e.g. AudioMetadata, AudioData.
    public AudioMetadata audioMetadata;
}

public class AudioMetadata {
    public string subscriptionId // unique identifier for a subscription request
    public string encoding; // PCM only supported
    public int sampleRate; // 16000 default
    public int channels; // 1 default
    public int length; // 640 default
}
```

## Audio streaming schema
After sending through the metadata packet, ACS will start streaming audio media to your WebSocket server. Below is an example of what the media object your server will receive looks like. 

``` code
/**
 * The audio buffer object which is then serialized to JSON format
 */
public class AudioDataSample {
    public string kind; // What kind of data this is, e.g. AudioMetadata, AudioData.
    public AudioData audioData;
}

public class AudioData {
    public string data; // Base64 Encoded audio buffer data
    public string timestamp; // In ISO 8601 format (yyyy-mm-ddThh:mm:ssZ) 
    public string participantRawID;
    public boolean silent; // Indicates if the received audio buffer contains only silence.
} 
```

Example of audio data being streamed 

``` json
{
  "kind": "AudioData",
  "audioData": {
    "timestamp": "2022-10-03T19:16:12.925Z",
    "participantRawID": "8:acs:3d20e1de-0f28-41c5-84a0-4960fde5f411_0000000b-faeb-c708-99bf-a43a0d0036b0",
    "data": "5ADwAOMA6AD0AOIA4ADkAN8AzwDUANEAywC+ALQArgC0AKYAnACJAIoAlACWAJ8ApwCiAKkAqgCqALUA0wDWANAA3QDVAN0A8wDzAPAA7wDkANkA1QDPAPIA6QDmAOcA0wDYAPMA8QD8AP0AAwH+AAAB/QAAAREBEQEDAQoB9wD3APsA7gDxAPMA7wDpAN0A6gD5APsAAgEHAQ4BEAETARsBMAFHAUABPgE2AS8BKAErATEBLwE7ASYBGQEAAQcBBQH5AAIBBwEMAQ4BAAH+APYA6gDzAPgA7gDkAOUA3wDcANQA2gDWAN8A3wDcAMcAxwDIAMsA1wDfAO4A3wDUANQA3wDvAOUA4QDpAOAA4ADhAOYA5wDkAOUA1gDxAOcA4wDpAOEA4gD0APoA7wD9APkA6ADwAPIA7ADrAPEA6ADfANQAzQDLANIAzwDaANcA3QDZAOQA4wDXANwA1ADbAOsA7ADyAPkA7wDiAOIA6gDtAOsA7gDeAOIA4ADeANUA6gD1APAA8ADgAOQA5wDgAPgA8ADnAN8A5gDgAOoA6wDcAOgA2gDZANUAyQDPANwA3gDgAO4A8QDyAAQBEwEDAewA+gDpAN4A6wDeAO8A8QDwAO8ABAEKAQUB/gD5AAMBAwEIARoBFAEeARkBDgH8AP0A+gD8APcA+gDrAO0A5wDcANEA0QDHAM4A0wDUAM4A0wDZANQAxgDSAM4A1ADVAOMA4QDhANUA2gDjAOYA5wDrANQA5wDrAMsAxQDWANsA5wDpAOEA4QDFAMoA0QDKAMgAwgDNAMsAwgCwAKkAtwCrAKoAsACgAJ4AlQCeAKAAoQCmAKwApwCsAK0AnQCVAA==",
    "silent": false
  }
}
```

## Stop audio streaming
Audio streaming will automatically stop when the call ends or is canceled. 

## Clean up resources

If you want to clean up and remove a Communication Services subscription, you can delete the resource or resource group. Deleting the resource group also deletes any other resources associated with it. Learn more about [cleaning up resources](../create-communication-resource.md#clean-up-resources).

## Next steps
- Learn more about [Media Streaming](../../concepts/voice-video-calling/media-streaming.md).
- Learn more about [Call Automation](../../concepts/voice-video-calling/call-automation.md) and its features. 
- Learn more about [Play action](../../concepts/voice-video-calling/play-action.md).
- Learn more about [Recognize action](../../concepts/voice-video-calling/recognize-action.md).
