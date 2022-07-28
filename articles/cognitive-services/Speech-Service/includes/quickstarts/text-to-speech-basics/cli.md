---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/15/2022
ms.author: eur
---

[!INCLUDE [Introduction](intro.md)]

## Prerequisites

[!INCLUDE [Prerequisites](../../common/azure-prerequisites.md)]

## Set up the environment

[!INCLUDE [SPX Setup](../../spx-setup-quick.md)]

## Synthesize to speaker output

Run the following command for speech synthesis to the default speaker output. You can modify the text to be synthesized and the voice.

```console
 spx synthesize --text "I'm excited to try text to speech" --voice "en-US-JennyNeural"
```

If you don't set a voice name, the default voice for `en-US` will speak. All neural voices are multilingual and fluent in their own language and English. For example, if the input text in English is "I'm excited to try text to speech" and you set `--voice "es-ES-ElviraNeural"`, the text is spoken in English with a Spanish accent. If the voice does not speak the language of the input text, the Speech service won't output synthesized audio.
            
Run this command for information about additional speech synthesis options such as file input and output:
```console
spx help synthesize
```

## Clean up resources

[!INCLUDE [Delete resource](../../common/delete-resource.md)]
