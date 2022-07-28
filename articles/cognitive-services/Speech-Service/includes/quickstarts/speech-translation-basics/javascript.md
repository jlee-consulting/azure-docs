---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 06/08/2022
ms.author: eur
---

[!INCLUDE [Header](../../common/javascript.md)]

[!INCLUDE [Introduction](intro.md)]

## Prerequisites

[!INCLUDE [Prerequisites](../../common/azure-prerequisites.md)]

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=JAVASCRIPT&Pillar=Speech&Product=Speech-translation&Page=quickstart&Section=Prerequisites" target="_target">I ran into an issue</a>

## Set up the environment

Before you can do anything, you need to install the Speech SDK for JavaScript. If you just want the package name to install, run `npm install microsoft-cognitiveservices-speech-sdk`. For guided installation instructions, see the [SDK installation guide](../../../quickstarts/setup-platform.md?pivots=programming-language-javascript).

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=JAVASCRIPT&Pillar=Speech&Product=Speech-translation&Page=quickstart&Section=Set-up-the-environment" target="_target">I ran into an issue</a>

## Translate speech from a file 

Follow these steps to create a Node.js console application for speech recognition.

1. Open a command prompt where you want the new project, and create a new file named `SpeechTranslation.js`.
1. Install the Speech SDK for JavaScript:
    ```console
    npm install microsoft-cognitiveservices-speech-sdk
    ```
1. Copy the following code into `SpeechTranslation.js`:

    ```javascript
    const fs = require("fs");
    const sdk = require("microsoft-cognitiveservices-speech-sdk");
    const speechTranslationConfig = sdk.SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    speechTranslationConfig.speechRecognitionLanguage = "en-US";
    
    var language = "it";
    speechTranslationConfig.addTargetLanguage(language);
    
    function fromFile() {
        let audioConfig = sdk.AudioConfig.fromWavFileInput(fs.readFileSync("YourAudioFile.wav"));
        let translationRecognizer = new sdk.TranslationRecognizer(speechTranslationConfig, audioConfig);
    
        translationRecognizer.recognizeOnceAsync(result => {
            switch (result.reason) {
                case sdk.ResultReason.TranslatedSpeech:
                    console.log(`RECOGNIZED: Text=${result.text}`);
                    console.log("Translated into [" + language + "]: " + result.translations.get(language));
    
                    break;
                case sdk.ResultReason.NoMatch:
                    console.log("NOMATCH: Speech could not be recognized.");
                    break;
                case sdk.ResultReason.Canceled:
                    const cancellation = sdk.CancellationDetails.fromResult(result);
                    console.log(`CANCELED: Reason=${cancellation.reason}`);
    
                    if (cancellation.reason == sdk.CancellationReason.Error) {
                        console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
                        console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
                        console.log("CANCELED: Did you set the speech resource key and region values?");
                    }
                    break;
            }
            translationRecognizer.close();
        });
    }
    fromFile();
    ```

1. In `SpeechTranslation.js`, replace `YourSubscriptionKey` with your Speech resource key, and replace `YourServiceRegion` with your Speech resource region.
    > [!IMPORTANT]
    > Remember to remove the key from your code when you're done, and never post it publicly. For production, use a secure way of storing and accessing your credentials like [Azure Key Vault](../../../../../key-vault/general/overview.md). See the Cognitive Services [security](../../../../cognitive-services-security.md) article for more information.
1. In `SpeechTranslation.js`, replace `YourAudioFile.wav` with your own WAV file. This example only recognizes speech from a WAV file. For information about other audio formats, see [How to use compressed input audio](~/articles/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams.md). This example supports up to 30 seconds audio.
1. To change the speech recognition language, replace `en-US` with another [supported language](~/articles/cognitive-services/speech-service/supported-languages.md#speech-to-text). Specify the full locale with a dash (`-`) separator. For example, `es-ES` for Spanish (Spain). The default language is `en-US` if you don't specify a language. For details about how to identify one of multiple languages that might be spoken, see [language identification](~/articles/cognitive-services/speech-service/language-identification.md).
1. To change the translation target language, replace `it` with another [supported language](~/articles/cognitive-services/speech-service/supported-languages.md#speech-translation). With few exceptions you only specify the language code that precedes the locale dash (`-`) separator. For example, use `es` for Spanish (Spain) instead of `es-ES`. The default language is `en` if you don't specify a language.

Run your new console application to start speech recognition from a file:

```console
node.exe SpeechTranslation.js
```

The speech from the audio file should be output as translated text in the target language:

```console
RECOGNIZED: Text=I'm excited to try speech translation.
Translated into [it]: Sono entusiasta di provare la traduzione vocale.
```

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=JAVASCRIPT&Pillar=Speech&Product=Speech-translation&Page=quickstart&Section=Translate-speech-from-a-file" target="_target">I ran into an issue</a>

## Remarks
Now that you've completed the quickstart, here are some additional considerations:

This example uses the `recognizeOnceAsync` operation to transcribe utterances of up to 30 seconds, or until silence is detected. For information about continuous recognition for longer audio, including multi-lingual conversations, see [How to translate speech](~/articles/cognitive-services/speech-service/how-to-translate-speech.md).

> [!NOTE]
> Recognizing speech from a microphone is not supported in Node.js. It's supported only in a browser-based JavaScript environment. 

## Clean up resources

[!INCLUDE [Delete resource](../../common/delete-resource.md)]

