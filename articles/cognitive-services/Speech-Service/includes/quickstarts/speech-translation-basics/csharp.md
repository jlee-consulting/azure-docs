---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 06/08/2022
ms.author: eur
---

[!INCLUDE [Header](../../common/csharp.md)]

[!INCLUDE [Introduction](intro.md)]

## Prerequisites

[!INCLUDE [Prerequisites](../../common/azure-prerequisites.md)]

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=CSHARP&Pillar=Speech&Product=Speech-translation&Page=quickstart&Section=Prerequisites" target="_target">I ran into an issue</a>

## Set up the environment
The Speech SDK is available as a [NuGet package](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) and implements .NET Standard 2.0. You install the Speech SDK in the next section of this article, but first check the [SDK installation guide](../../../quickstarts/setup-platform.md?pivots=programming-language-csharp) for any more requirements. 

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=CSHARP&Pillar=Speech&Product=Speech-translation&Page=quickstart&Section=Set-up-the-environment" target="_target">I ran into an issue</a>

## Translate speech from a microphone

Follow these steps to create a new console application and install the Speech SDK.

1. Open a command prompt where you want the new project, and create a console application with the .NET CLI.
    ```dotnetcli
    dotnet new console
    ```
1. Install the Speech SDK in your new project with the .NET CLI.
    ```dotnetcli
    dotnet add package Microsoft.CognitiveServices.Speech
    ```
1. Replace the contents of `Program.cs` with the following code. 

    ```csharp
    using System;
    using System.IO;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Translation;
    
    class Program 
    {
        static string YourSubscriptionKey = "YourSubscriptionKey";
        static string YourServiceRegion = "YourServiceRegion";
    
        static void OutputSpeechRecognitionResult(TranslationRecognitionResult translationRecognitionResult)
        {
            switch (translationRecognitionResult.Reason)
            {
                case ResultReason.TranslatedSpeech:
                    Console.WriteLine($"RECOGNIZED: Text={translationRecognitionResult.Text}");
                    foreach (var element in translationRecognitionResult.Translations)
                    {
                        Console.WriteLine($"TRANSLATED into '{element.Key}': {element.Value}");
                    }
                    break;
                case ResultReason.NoMatch:
                    Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    break;
                case ResultReason.Canceled:
                    var cancellation = CancellationDetails.FromResult(translationRecognitionResult);
                    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");
    
                    if (cancellation.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
                        Console.WriteLine($"CANCELED: Did you set the speech resource key and region values?");
                    }
                    break;
            }
        }
    
        async static Task Main(string[] args)
        {
            var speechTranslationConfig = SpeechTranslationConfig.FromSubscription(YourSubscriptionKey, YourServiceRegion);        
            speechTranslationConfig.SpeechRecognitionLanguage = "en-US";
            speechTranslationConfig.AddTargetLanguage("it");
    
            using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
            using var translationRecognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
    
            Console.WriteLine("Speak into your microphone.");
            var translationRecognitionResult = await translationRecognizer.RecognizeOnceAsync();
            OutputSpeechRecognitionResult(translationRecognitionResult);
        }
    }
    ```

1. In `Program.cs`, replace `YourSubscriptionKey` with your Speech resource key, and replace `YourServiceRegion` with your Speech resource region.
    > [!IMPORTANT]
    > Remember to remove the key from your code when you're done, and never post it publicly. For production, use a secure way of storing and accessing your credentials like [Azure Key Vault](../../../../../key-vault/general/overview.md). See the Cognitive Services [security](../../../../cognitive-services-security.md) article for more information.
1. To change the speech recognition language, replace `en-US` with another [supported language](~/articles/cognitive-services/speech-service/supported-languages.md#speech-to-text). Specify the full locale with a dash (`-`) separator. For example, `es-ES` for Spanish (Spain). The default language is `en-US` if you don't specify a language. For details about how to identify one of multiple languages that might be spoken, see [language identification](~/articles/cognitive-services/speech-service/language-identification.md).
1. To change the translation target language, replace `it` with another [supported language](~/articles/cognitive-services/speech-service/supported-languages.md#speech-translation). With few exceptions you only specify the language code that precedes the locale dash (`-`) separator. For example, use `es` for Spanish (Spain) instead of `es-ES`. The default language is `en` if you don't specify a language.

Run your new console application to start speech recognition from a microphone:

```console
dotnet run
```

Speak into your microphone when prompted. What you speak should be output as translated text in the target language:

```console
Speak into your microphone.
RECOGNIZED: Text=I'm excited to try speech translation.
TRANSLATED into 'it': Sono entusiasta di provare la traduzione vocale.
```

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=CSHARP&Pillar=Speech&Product=Speech-translation&Page=quickstart&Section=Translate-speech-from-a-microphone" target="_target">I ran into an issue</a>

## Remarks
Now that you've completed the quickstart, here are some additional considerations:

- This example uses the `RecognizeOnceAsync` operation to transcribe utterances of up to 30 seconds, or until silence is detected. For information about continuous recognition for longer audio, including multi-lingual conversations, see [How to translate speech](~/articles/cognitive-services/speech-service/how-to-translate-speech.md).
- To recognize speech from an audio file, use `FromWavFileInput` instead of `FromDefaultMicrophoneInput`:
    ```csharp
    using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
    ```
- For compressed audio files such as MP4, install GStreamer and use `PullAudioInputStream` or `PushAudioInputStream`. For more information, see [How to use compressed input audio](~/articles/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams.md).

## Clean up resources

[!INCLUDE [Delete resource](../../common/delete-resource.md)]

