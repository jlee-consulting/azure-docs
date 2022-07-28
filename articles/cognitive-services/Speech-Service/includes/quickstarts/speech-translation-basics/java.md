---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 06/08/2022
ms.author: eur
---

[!INCLUDE [Header](../../common/java.md)]

[!INCLUDE [Introduction](intro.md)]

## Prerequisites

[!INCLUDE [Prerequisites](../../common/azure-prerequisites.md)]

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=JAVA&Pillar=Speech&Product=Speech-translation&Page=quickstart&Section=Prerequisites" target="_target">I ran into an issue</a>

## Set up the environment

Before you can do anything, you need to install the Speech SDK. The sample in this quickstart works with the [Java Runtime](~/articles/cognitive-services/speech-service/quickstarts/setup-platform.md?pivots=programming-language-java&tabs=jre).

To complete the steps in this quickstart, you must also install [Apache Maven](https://maven.apache.org/install.html).

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=JAVA&Pillar=Speech&Product=Speech-translation&Page=quickstart&Section=Set-up-the-environment" target="_target">I ran into an issue</a>

## Translate speech from a microphone
To complete the steps in this quickstart, you must also install [Apache Maven](https://maven.apache.org/install.html).
Follow these steps to create a new console application for speech recognition.

1. Open a command prompt where you want the new project, and create a new `pom.xml` file. 
1. Copy the following XML content into `pom.xml`:
    ```xml
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <groupId>com.microsoft.cognitiveservices.speech.samples</groupId>
        <artifactId>quickstart-eclipse</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <build>
            <sourceDirectory>src</sourceDirectory>
            <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.7.0</version>
                <configuration>
                <source>1.8</source>
                <target>1.8</target>
                </configuration>
            </plugin>
            </plugins>
        </build>
        <repositories>
            <repository>
            <id>maven-cognitiveservices-speech</id>
            <name>Microsoft Cognitive Services Speech Maven Repository</name>
            <url>https://azureai.azureedge.net/maven/</url>
            </repository>
        </repositories>
        <dependencies>
            <dependency>
            <groupId>com.microsoft.cognitiveservices.speech</groupId>
            <artifactId>client-sdk</artifactId>
            <version>1.22.0</version>
            </dependency>
        </dependencies>
    </project>
    ```
1. Run the following Maven command to install the Speech SDK and dependencies.
    ```console
    mvn clean dependency:copy-dependencies
    ```
1. Create a new file named `SpeechTranslation.java` in the same project root directory.
1. Copy the following code into `SpeechTranslation.java`:

    ```java
    import com.microsoft.cognitiveservices.speech.*;
    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.translation.*;
    
    import java.util.concurrent.ExecutionException;
    import java.util.concurrent.Future;
    import java.util.Map;
    
    public class SpeechTranslation {
        private static String YourSubscriptionKey = "YourSubscriptionKey";
        private static String YourServiceRegion = "YourServiceRegion";
    
        public static void main(String[] args) throws InterruptedException, ExecutionException {
            SpeechTranslationConfig speechTranslationConfig = SpeechTranslationConfig.fromSubscription(YourSubscriptionKey, YourServiceRegion);
            speechTranslationConfig.setSpeechRecognitionLanguage("en-US");
    
            String[] toLanguages = { "it" };
            for (String language : toLanguages) {
                speechTranslationConfig.addTargetLanguage(language);
            }
    
            recognizeFromMicrophone(speechTranslationConfig);
        }
    
        public static void recognizeFromMicrophone(SpeechTranslationConfig speechTranslationConfig) throws InterruptedException, ExecutionException {
            AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
            TranslationRecognizer translationRecognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
    
            System.out.println("Speak into your microphone.");
            Future<TranslationRecognitionResult> task = translationRecognizer.recognizeOnceAsync();
            TranslationRecognitionResult translationRecognitionResult = task.get();
    
            if (translationRecognitionResult.getReason() == ResultReason.TranslatedSpeech) {
                System.out.println("RECOGNIZED: Text=" + translationRecognitionResult.getText());
                for (Map.Entry<String, String> pair : translationRecognitionResult.getTranslations().entrySet()) {
                    System.out.printf("Translated into '%s': %s\n", pair.getKey(), pair.getValue());
                }
            }
            else if (translationRecognitionResult.getReason() == ResultReason.NoMatch) {
                System.out.println("NOMATCH: Speech could not be recognized.");
            }
            else if (translationRecognitionResult.getReason() == ResultReason.Canceled) {
                CancellationDetails cancellation = CancellationDetails.fromResult(translationRecognitionResult);
                System.out.println("CANCELED: Reason=" + cancellation.getReason());
    
                if (cancellation.getReason() == CancellationReason.Error) {
                    System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                    System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                    System.out.println("CANCELED: Did you set the speech resource key and region values?");
                }
            }
    
            System.exit(0);
        }
    }
    ```

1. In `SpeechTranslation.java`, replace `YourSubscriptionKey` with your Speech resource key, and replace `YourServiceRegion` with your Speech resource region.
    > [!IMPORTANT]
    > Remember to remove the key from your code when you're done, and never post it publicly. For production, use a secure way of storing and accessing your credentials like [Azure Key Vault](../../../../../key-vault/general/overview.md). See the Cognitive Services [security](../../../../cognitive-services-security.md) article for more information.
1. To change the speech recognition language, replace `en-US` with another [supported language](~/articles/cognitive-services/speech-service/supported-languages.md#speech-to-text). Specify the full locale with a dash (`-`) separator. For example, `es-ES` for Spanish (Spain). The default language is `en-US` if you don't specify a language. For details about how to identify one of multiple languages that might be spoken, see [language identification](~/articles/cognitive-services/speech-service/language-identification.md). 
1. To change the translation target language, replace `it` with another [supported language](~/articles/cognitive-services/speech-service/supported-languages.md#speech-translation). With few exceptions you only specify the language code that precedes the locale dash (`-`) separator. For example, use `es` for Spanish (Spain) instead of `es-ES`. The default language is `en` if you don't specify a language.

Run your new console application to start speech recognition from a microphone:

```console
javac SpeechTranslation.java -cp ".;target\dependency\*"
java -cp ".;target\dependency\*" SpeechTranslation
```

Speak into your microphone when prompted. What you speak should be output as translated text in the target language:

```console
Speak into your microphone.
RECOGNIZED: Text=I'm excited to try speech translation.
Translated into 'it': Sono entusiasta di provare la traduzione vocale.
```

> [!div class="nextstepaction"]
> <a href="https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=JAVA&Pillar=Speech&Product=Speech-translation&Page=quickstart&Section=Translate-speech-from-a-microphone" target="_target">I ran into an issue</a>

## Remarks
Now that you've completed the quickstart, here are some additional considerations:

- This example uses the `RecognizeOnceAsync` operation to transcribe utterances of up to 30 seconds, or until silence is detected. For information about continuous recognition for longer audio, including multi-lingual conversations, see [How to translate speech](~/articles/cognitive-services/speech-service/how-to-translate-speech.md).
- To recognize speech from an audio file, use `fromWavFileInput` instead of `fromDefaultMicrophoneInput`:
    ```java
    AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
    ```
- For compressed audio files such as MP4, install GStreamer and use `PullAudioInputStream` or `PushAudioInputStream`. For more information, see [How to use compressed input audio](~/articles/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams.md).

## Clean up resources

[!INCLUDE [Delete resource](../../common/delete-resource.md)]

