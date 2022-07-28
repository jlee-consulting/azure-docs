---
title: How to deploy and use voice model - Speech service
titleSuffix: Azure Cognitive Services
description: Learn about how to deploy and use a custom neural voice model.
services: cognitive-services
author: Ling-Cao
manager: qiliao123
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 02/18/2022
ms.author: caoling
ms.custom: references_regions
zone_pivot_groups: programming-languages-set-nineteen
---

# Deploy and use your voice model

After you've successfully created and trained your voice model, you deploy it to a custom neural voice endpoint. Use the custom neural voice endpoint instead of the usual text-to-speech endpoint for requests with the REST API. Use the speech studio to create a custom neural voice endpoint. Use the REST API to suspend or resume a custom neural voice endpoint. 

> [!NOTE]
> See [Custom Neural Voice project types](custom-neural-voice.md#custom-neural-voice-project-types) for information about capabilities, requirements, and differences between Custom Neural Voice Pro and Custom Neural Voice Lite projects. This article focuses on the creation of a professional Custom Neural Voice using the Pro project.

## Create a custom neural voice endpoint

To create a custom neural voice endpoint:

1. On the **Deploy model** tab, select **Deploy model**. 
1. Select a voice model that you want to associate with this endpoint.  
1. Enter a **Name** and **Description** for your custom endpoint.
1. Select **Deploy** to create your endpoint.

In the endpoint table, you now see an entry for your new endpoint. It might take a few minutes to instantiate a new endpoint. When the status of the deployment is **Succeeded**, the endpoint is ready for use.

You can suspend and resume your endpoint if you don't use it all the time. When an endpoint is reactivated after suspension, the endpoint URL is retained, so you don't need to change your code in your apps. 

You can also update the endpoint to a new model. To change the model, make sure the new model is named the same as the one you want to update. 

> [!NOTE]
>- Standard subscription (S0) users can create up to 50 endpoints, each with its own custom neural voice.
>- To use your custom neural voice, you must specify the voice model name, use the custom URI directly in an HTTP request, and use the same subscription to pass through the authentication of the text-to-speech service.

After your endpoint is deployed, the endpoint name appears as a link. Select the link to display information specific to your endpoint, such as the endpoint key, endpoint URL, and sample code.

The custom endpoint is functionally identical to the standard endpoint that's used for text-to-speech requests. For more information, see the [Speech SDK](./get-started-text-to-speech.md) or [REST API](rest-text-to-speech.md).

[Audio Content Creation](https://speech.microsoft.com/audiocontentcreation) is a tool that allows you to fine-tune audio output by using a friendly UI.

## Copy your voice model to another project

You can copy your voice model to another project for the same region or another region. For example, you can copy a neural voice model that was trained in one region, to a project for another region.

> [!NOTE]
> Custom neural voice training is only available in the these regions: East US, Southeast Asia, and UK South. But you can copy a neural voice model from those regions to other regions. For more information, see the [regions for custom neural voice](regions.md#text-to-speech).

To copy your custom neural voice model to another project:

1. On the **Train model** tab, select a voice model that you want to copy, and then select **Copy to project**.

   :::image type="content" source="media/custom-voice/cnv-model-copy.png" alt-text="Copy to project":::

1. Select the **Region**, **Speech resource**, and **Project** where you want to copy the model. You must have a speech resource and project in the target region, otherwise you need to create them first. 

    :::image type="content" source="media/custom-voice/cnv-model-copy-dialog.png" alt-text="Copy voice model":::

1. Select **Submit** to copy the model.
1. Select **View model** under the notification message for copy success. 
1. On the **Train model** tab, select the newly copied model and then select **Deploy model**.

## Switch to a new voice model in your product

Once you've updated your voice model to the latest engine version, or if you want to switch to a new voice in your product, you need to redeploy the new voice model to a new endpoint. Redeploying new voice model on your existing endpoint is not supported. After deployment, switch the traffic to the newly created endpoint. We recommend that you transfer the traffic to the new endpoint in a test environment first to ensure that the traffic works well, and then transfer to the new endpoint in the production environment. During the transition, you need to keep the old endpoint. If there are some problems with the new endpoint during transition, you can switch back to your old endpoint. If the traffic has been running well on the new endpoint for about 24 hours (recommended value), you can delete your old endpoint. 

> [!NOTE]
> If your voice name is changed and you are using Speech Synthesis Markup Language (SSML), be sure to use the new voice name in SSML.

## Suspend and resume an endpoint

You can suspend or resume an endpoint, to limit spend and conserve resources that aren't in use. You won't be charged while the endpoint is suspended. When you resume an endpoint, you can use the same endpoint URL in your application to synthesize speech. 

You can suspend and resume an endpoint in Speech Studio or via the REST API.

> [!NOTE]
> The suspend operation will complete almost immediately. The resume operation completes in about the same amount of time as a new deployment. 

### Suspend and resume an endpoint in Speech Studio

This section describes how to suspend or resume a custom neural voice endpoint in the Speech Studio portal.

#### Suspend endpoint

1. To suspend and deactivate your endpoint, select **Suspend** from the **Deploy model** tab in [Speech Studio](https://aka.ms/custom-voice-portal).

   :::image type="content" source="media/custom-voice/cnv-endpoint-suspend.png" alt-text="Screenshot of the select suspend endpoint option":::

1. In the dialog box that appears, select **Submit**. After the endpoint is suspended, Speech Studio will show the **Successfully suspended endpoint** notification.

#### Resume endpoint

1. To resume and activate your endpoint, select **Resume** from the **Deploy model** tab in [Speech Studio](https://aka.ms/custom-voice-portal).

   :::image type="content" source="media/custom-voice/cnv-endpoint-resume.png" alt-text="Screenshot of the select resume endpoint option":::

1. In the dialog box that appears, select **Submit**. After you successfully reactivate the endpoint, the status will change from **Suspended** to **Succeeded**.

### Suspend and resume endpoint via REST API

This section will show you how to [get](#get-endpoint), [suspend](#suspend-endpoint), or [resume](#resume-endpoint) a custom neural voice endpoint via REST API.

#### Application settings

The application settings that you use as REST API [request parameters](#request-parameters) are available on the **Deploy model** tab in [Speech Studio](https://aka.ms/custom-voice-portal).

:::image type="content" source="./media/custom-voice/cnv-endpoint-app-settings-zoom.png" alt-text="Screenshot of custom endpoint app settings in Speech Studio." lightbox="./media/custom-voice/cnv-endpoint-app-settings-full.png":::

* The **Endpoint key** shows the subscription key the endpoint is associated with. Use the endpoint key as the value of your `Ocp-Apim-Subscription-Key` request header. 
* The **Endpoint URL** shows your service region. Use the value that precedes `voice.speech.microsoft.com` as your service region request parameter. For example, use `eastus` if the endpoint URL is `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1`.
* The **Endpoint URL** shows your endpoint ID. Use the value appended to the `?deploymentId=` query parameter as the value of your endpoint ID request parameter.

#### Get endpoint

Get the endpoint by endpoint ID. The operation returns details about an endpoint such as model ID, project ID, and status.  

For example, you might want to track the status progression for [suspend](#suspend-endpoint) or [resume](#resume-endpoint) operations. Use the `status` property in the response payload to determine the status of the endpoint.

The possible `status` property values are:

| Status | Description |
| ------------- | ------------------------------------------------------------ |
| `NotStarted` | The endpoint hasn't yet been deployed, and it's not available for speech synthesis. |
| `Running` | The endpoint is in the process of being deployed or resumed, and it's not available for speech synthesis. |
| `Succeeded` | The endpoint is active and available for speech synthesis. The endpoint has been deployed or the resume operation succeeded. |
| `Failed` | The endpoint deploy or suspend operation failed. The endpoint can only be viewed or deleted in [Speech Studio](https://aka.ms/custom-voice-portal).|
| `Disabling` | The endpoint is in the process of being suspended, and it's not available for speech synthesis. |
| `Disabled` | The endpoint is inactive, and it's not available for speech synthesis. The suspend operation succeeded or the resume operation failed. |

> [!Tip]
> If the status is `Failed` or `Disabled`, check `properties.error` for a detailed error message. However, there won't be error details if the status is `Disabled` due to a successful suspend operation. 

##### Get endpoint example

For information about endpoint ID, region, and subscription key parameters, see [request parameters](#request-parameters).

HTTP example:

```HTTP
GET api/texttospeech/v3.0/endpoints/<YourEndpointId> HTTP/1.1
Ocp-Apim-Subscription-Key: YourSubscriptionKey
Host: <YourServiceRegion>.customvoice.api.speech.microsoft.com
```

cURL example:

```Console
curl -v -X GET "https://<YourServiceRegion>.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/endpoints/<YourEndpointId>" -H "Ocp-Apim-Subscription-Key: <YourSubscriptionKey >"
```

Response header example:

```
Status code: 200 OK
```

Response body example:

```json
{
  "model": {
    "id": "a92aa4b5-30f5-40db-820c-d2d57353de44"
  },
  "project": {
    "id": "ffc87aba-9f5f-4bfa-9923-b98186591a79"
  },
  "properties": {},
  "status": "Succeeded",
  "lastActionDateTime": "2019-01-07T11:36:07Z",
  "id": "e7ffdf12-17c7-4421-9428-a7235931a653",
  "createdDateTime": "2019-01-07T11:34:12Z",
  "locale": "en-US",
  "name": "Voice endpoint",
  "description": "Example for voice endpoint"
}
```

#### Suspend endpoint

You can suspend an endpoint to limit spend and conserve resources that aren't in use. You won't be charged while the endpoint is suspended. When you resume an endpoint, you can use the same endpoint URL in your application to synthesize speech. 

You suspend an endpoint with its unique deployment ID. The endpoint status must be `Succeeded` before you can suspend it.

Use the [get endpoint](#get-endpoint) operation to poll and track the status progression from `Succeeded`, to `Disabling`, and finally to `Disabled`. 

##### Suspend endpoint example

For information about endpoint ID, region, and subscription key parameters, see [request parameters](#request-parameters).

HTTP example:

```HTTP
POST api/texttospeech/v3.0/endpoints/<YourEndpointId>/suspend HTTP/1.1
Ocp-Apim-Subscription-Key: YourSubscriptionKey
Host: <YourServiceRegion>.customvoice.api.speech.microsoft.com
Content-Type: application/json
Content-Length: 0
```

cURL example:

```Console
curl -v -X POST "https://<YourServiceRegion>.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/endpoints/<YourEndpointId>/suspend" -H "Ocp-Apim-Subscription-Key: <YourSubscriptionKey >" -H "content-type: application/json" -H "content-length: 0"
```

Response header example:

```
Status code: 202 Accepted
```

For more information, see [response headers](#response-headers).

#### Resume endpoint

When you resume an endpoint, you can use the same endpoint URL that you used before it was suspended. 

You resume an endpoint with its unique deployment ID. The endpoint status must be `Disabled` before you can resume it.

Use the [get endpoint](#get-endpoint) operation to poll and track the status progression from `Disabled`, to `Running`, and finally to `Succeeded`. If the resume operation failed, the endpoint status will be `Disabled`. 

##### Resume endpoint example

For information about endpoint ID, region, and subscription key parameters, see [request parameters](#request-parameters).

HTTP example:

```HTTP
POST api/texttospeech/v3.0/endpoints/<YourEndpointId>/resume HTTP/1.1
Ocp-Apim-Subscription-Key: YourSubscriptionKey
Host: <YourServiceRegion>.customvoice.api.speech.microsoft.com
Content-Type: application/json
Content-Length: 0
```

cURL example:

```Console
curl -v -X POST "https://<YourServiceRegion>.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/endpoints/<YourEndpointId>/resume" -H "Ocp-Apim-Subscription-Key: <YourSubscriptionKey >" -H "content-type: application/json" -H "content-length: 0"
```

Response header example:
```
Status code: 202 Accepted
```

For more information, see [response headers](#response-headers).

#### Parameters and response codes

##### Request parameters

You use these request parameters with calls to the REST API. See [application settings](#application-settings) for information about where to get your region, endpoint ID, and subscription key in Speech Studio.

| Name                        | Location     | Required | Type   | Description                                                                    |
| --------------------------- | ------ | -------- | ------ | ------------------------------------------------------------------------------ |
| `YourServiceRegion` | Path   | `True` | string | The Azure region the endpoint is associated with. |
| `YourEndpointId` | Path   | `True` | string | The identifier of the endpoint. |
| `Ocp-Apim-Subscription-Key` | Header | `True` | string | The subscription key the endpoint is associated with. |

##### Response headers

Status code: 202 Accepted

| Name          | Type   | Description                                                                      |
| ------------- | ------ | -------------------------------------------------------------------------------- |
| `Location` | string | The location of the endpoint that can be used as the full URL to get endpoint. |
| `Retry-After` | string | The total seconds of recommended interval to retry to get endpoint status.       |

##### HTTP status codes

The HTTP status code for each response indicates success or common errors.

| HTTP status code | Description       | Possible reason                                                                                                                                                           |
| ---------------- | ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 200              | OK                | The request was successful.                                                                                                                                               |
| 202              | Accepted          | The request has been accepted and is being processed.                                                                                  |
| 400              | Bad Request       | The value of a parameter is invalid, or a required parameter is missing, empty, or null. One common issue is a header that is too long. |
| 401              | Unauthorized      | The request isn't authorized. Check to make sure your subscription key or [token](rest-speech-to-text-short.md#authentication) is valid and in the correct region.                                                      |
| 429              | Too Many Requests | You've exceeded the quota or rate of requests allowed for your subscription.                                                                                            |
| 502              | Bad Gateway       | Network or server-side issue. May also indicate invalid headers.                                                                                                          |

## Use your custom voice

The difference between Custom voice sample codes and [Text-to-speech quickstart codes](get-started-speech-to-text.md) is that `EndpointId` must be filled in Custom Voice. So you should first build and run demo quickly by quickstart codes and then check following Custom voice sample codes to see how to set `EndpointId`.

::: zone pivot="programming-language-csharp"
```csharp
var speechConfig = SpeechConfig.FromSubscription(YourSubscriptionKey, YourServiceRegion);      
speechConfig.SpeechSynthesisVoiceName = "YourCustomVoiceName";
speechConfig.EndpointId = "YourEndpointId";
```
::: zone-end

::: zone pivot="programming-language-cpp"
```cpp
auto speechConfig = SpeechConfig::FromSubscription(YourSubscriptionKey, YourServiceRegion);
speechConfig->SetSpeechSynthesisVoiceName("YourCustomVoiceName");
speechConfig->SetEndpointId("YourEndpointId");
```
::: zone-end

::: zone pivot="programming-language-java"
```java
SpeechConfig speechConfig = SpeechConfig.fromSubscription(YourSubscriptionKey, YourServiceRegion);
speechConfig.setSpeechSynthesisVoiceName("YourCustomVoiceName");
speechConfig.setEndpointId("YourEndpointId");
```
::: zone-end

::: zone pivot="programming-language-objectivec"
```ObjectiveC
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:speechKey region:serviceRegion];
speechConfig.speechSynthesisVoiceName = @"YourCustomVoiceName";
speechConfig.EndpointId = @"YourEndpointId";
```
::: zone-end

::: zone pivot="programming-language-python"
```Python
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
speech_config.endpoint_id = "YourEndpointId"
speech_config.speech_synthesis_voice_name = "YourCustomVoiceName"
```
::: zone-end

## Next steps

- [How to record voice samples](record-custom-voice-samples.md)
- [Text-to-Speech API reference](rest-text-to-speech.md)
- [Long Audio API](long-audio-api.md)
