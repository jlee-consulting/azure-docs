---
title: Use pronunciation assessment
titleSuffix: Azure Cognitive Services
description: Learn about pronunciation assessment features that are currently publicly available.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 06/13/2022
ms.author: eur
zone_pivot_groups: programming-languages-speech-sdk
---

# Use pronunciation assessment

In this article, you'll learn how to use pronunciation assessment through the Speech SDK.

::: zone pivot="programming-language-go"
> [!NOTE]
> Pronunciation assessment is not available with the Speech SDK for Go. You can read about the concepts below, but you must select another programming language for implementation details. 
::: zone-end

You can get pronunciation assessment scores for:

- Full text
- Words
- Syllable groups
- Phonemes in SAPI or IPA format

> [!NOTE]
> For information about availability of pronunciation assessment, see [supported languages](language-support.md#pronunciation-assessment) and [available regions](regions.md#speech-to-text-pronunciation-assessment-text-to-speech-and-translation).
>
> The syllable groups, IPA phonemes, and spoken phoneme features of pronunciation assessment are currently only available for the en-US locale.

## Configuration parameters

This table lists some of the key configuration parameters for pronunciation assessment.

| Parameter | Description | 
|-----------|-------------|
| `ReferenceText` | The text that the pronunciation will be evaluated against. | 
| `GradingSystem` | The point system for score calibration. The `FivePoint` system gives a 0-5 floating point score, and `HundredMark` gives a 0-100 floating point score. | 
| `Granularity` | Determines the lowest level of evaluation granularity. Scores for levels above or equal to the minimal value are returned.  Accepted values are `Phoneme`, which shows the score on the full text, word, syllable, and phoneme level, `Syllable`, which shows the score on the full text, word, and syllable level, `Word`, which shows the score on the full text and word level, or `FullText`, which shows the score on the full text level only. The provided full reference text can be a word, sentence, or paragraph, and it depends on your input reference text.| 
| `EnableMiscue` | Enables miscue calculation when the pronounced words are compared to the reference text. If this value is `True`, the `ErrorType` result value can be set to `Omission` or `Insertion` based on the comparison. Accepted values are `False` and `True`. Default: `False`. | 

You must create a `PronunciationAssessmentConfig` object with the reference text, grading system, and granularity. Enabling miscue and other configuration settings are optional. 

::: zone pivot="programming-language-csharp"

```csharp
var pronunciationAssessmentConfig = new PronunciationAssessmentConfig(
    referenceText: "good morning",
    gradingSystem: GradingSystem.HundredMark, 
    granularity: Granularity.Phoneme, 
    enableMiscue: true);
```
   
::: zone-end  

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig = PronunciationAssessmentConfig::CreateFromJson("{\"referenceText\":\"good morning\",\"gradingSystem\":\"HundredMark\",\"granularity\":\"Phoneme\"}");
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
PronunciationAssessmentConfig pronunciationAssessmentConfig = PronunciationAssessmentConfig.fromJson("{\"referenceText\":\"good morning\",\"gradingSystem\":\"HundredMark\",\"granularity\":\"Phoneme\"}");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = speechsdk.PronunciationAssessmentConfig(json_string="{\"referenceText\":\"good morning\",\"gradingSystem\":\"HundredMark\",\"granularity\":\"Phoneme\"}")
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
var pronunciationAssessmentConfig = SpeechSDK.PronunciationAssessmentConfig.fromJSON("{\"referenceText\":\"good morning\",\"gradingSystem\":\"HundredMark\",\"granularity\":\"Phoneme\"}");
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```ObjectiveC
SPXPronunciationAssessmentConfiguration *pronunciationAssessmentConfig =
[[SPXPronunciationAssessmentConfiguration alloc] init:@"good morning"
                            gradingSystem:SPXPronunciationAssessmentGradingSystem_HundredMark
                            granularity:SPXPronunciationAssessmentGranularity_Phoneme
                            enableMiscue:true];
```

::: zone-end


::: zone pivot="programming-language-swift"

```swift
var pronunciationAssessmentConfig: SPXPronunciationAssessmentConfiguration?
do {
    try pronunciationAssessmentConfig = SPXPronunciationAssessmentConfiguration.init(referenceText, gradingSystem: SPXPronunciationAssessmentGradingSystem.hundredMark, granularity: SPXPronunciationAssessmentGranularity.phoneme, enableMiscue: true)
} catch {
    print("error \(error) happened")
    pronunciationAssessmentConfig = nil
    return
}
```

::: zone-end

::: zone pivot="programming-language-go"

::: zone-end


## Syllable groups

Pronunciation assessment can provide syllable-level assessment results. Grouping in syllables is more legible and aligned with speaking habits, as a word is typically pronounced syllable by syllable rather than phoneme by phoneme.

The following table compares example phonemes with the corresponding syllables.

| Sample word | Phonemes | Syllables |
|-----------|-------------|-------------|
|technological|teknələdʒɪkl|tek·nə·lɑ·dʒɪkl|
|hello|hɛloʊ|hɛ·loʊ|
|luck|lʌk|lʌk|
|photosynthesis|foʊtəsɪnθəsɪs|foʊ·tə·sɪn·θə·sɪs|

To request syllable-level results along with phonemes, set the granularity [configuration parameter](#configuration-parameters) to `Phoneme`.

## Phoneme alphabet format

The phoneme name is provided together with the score, to help identity which phonemes were pronounced accurately or inaccurately. For the [supported languages](language-support.md#pronunciation-assessment), you can get the phoneme name in [SAPI](/previous-versions/windows/desktop/ee431828(v=vs.85)#american-english-phoneme-table) format, and for the `en-US` locale, you can also get the phoneme name in [IPA](https://en.wikipedia.org/wiki/IPA) format. 

The following table compares example SAPI phonemes with the corresponding IPA phonemes.

| Sample word | SAPI Phonemes | IPA phonemes |
|-----------|-------------|-------------|
|hello|h eh l ow|h ɛ l oʊ|
|luck|l ah k|l ʌ k|
|photosynthesis|f ow t ax s ih n th ax s ih s|f oʊ t ə s ɪ n θ ə s ɪ s|

To request IPA phonemes, set the phoneme alphabet to `"IPA"`. If you don't specify the alphabet, the phonemes will be in SAPI format by default.

::: zone pivot="programming-language-csharp"

```csharp
pronunciationAssessmentConfig.PhonemeAlphabet = "IPA";
```
   
::: zone-end  

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig = PronunciationAssessmentConfig::CreateFromJson("{\"referenceText\":\"good morning\",\"gradingSystem\":\"HundredMark\",\"granularity\":\"Phoneme\",\"phonemeAlphabet\":\"IPA\"}");
```
   
::: zone-end 

::: zone pivot="programming-language-java"

```Java
PronunciationAssessmentConfig pronunciationAssessmentConfig = PronunciationAssessmentConfig.fromJson("{\"referenceText\":\"good morning\",\"gradingSystem\":\"HundredMark\",\"granularity\":\"Phoneme\",\"phonemeAlphabet\":\"IPA\"}");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = speechsdk.PronunciationAssessmentConfig(json_string="{\"referenceText\":\"good morning\",\"gradingSystem\":\"HundredMark\",\"granularity\":\"Phoneme\",\"phonemeAlphabet\":\"IPA\"}")
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
var pronunciationAssessmentConfig = SpeechSDK.PronunciationAssessmentConfig.fromJSON("{\"referenceText\":\"good morning\",\"gradingSystem\":\"HundredMark\",\"granularity\":\"Phoneme\",\"phonemeAlphabet\":\"IPA\"}");
```

::: zone-end

::: zone pivot="programming-language-objectivec"
   
```ObjectiveC
pronunciationAssessmentConfig.phonemeAlphabet = @"IPA";
```

::: zone-end


::: zone pivot="programming-language-swift"

```swift
pronunciationAssessmentConfig?.phonemeAlphabet = "IPA"
```

::: zone-end

::: zone pivot="programming-language-go"

::: zone-end


## Spoken phoneme

With spoken phonemes, you can get confidence scores indicating how likely the spoken phonemes matched the expected phonemes. 

For example, when you speak the word "hello", the expected IPA phonemes are "h ɛ l oʊ". The actual spoken phonemes could be "h ə l oʊ". In the following assessment result, the most likely spoken phoneme was `"ə"` instead of the expected phoneme `"ɛ"`. The expected phoneme `"ɛ"` only received a confidence score of 47. Other potential matches received confidence scores of 52, 17, and 2. 

```json
{
    "Phoneme": "ɛ",
    "PronunciationAssessment": {
        "AccuracyScore": 47.0,
        "NBestPhonemes": [
            {
                "Phoneme": "ə",
                "Score": 100.0
            },
            {
                "Phoneme": "l",
                "Score": 52.0
            },
            {
                "Phoneme": "ɛ",
                "Score": 47.0
            },
            {
                "Phoneme": "h",
                "Score": 17.0
            },
            {
                "Phoneme": "æ",
                "Score": 2.0
            }
        ]
    },
    "Offset": 11100000,
    "Duration": 500000
},
```

To indicate whether, and how many potential spoken phonemes to get confidence scores for, set the `NBestPhonemeCount` parameter to an integer value such as `5`. 
   
::: zone pivot="programming-language-csharp"

```csharp
pronunciationAssessmentConfig.NBestPhonemeCount = 5;
```
   
::: zone-end      

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig = PronunciationAssessmentConfig::CreateFromJson("{\"referenceText\":\"good morning\",\"gradingSystem\":\"HundredMark\",\"granularity\":\"Phoneme\",\"phonemeAlphabet\":\"IPA\",\"nBestPhonemeCount\":5}");
```
   
::: zone-end

::: zone pivot="programming-language-java"

```Java
PronunciationAssessmentConfig pronunciationAssessmentConfig = PronunciationAssessmentConfig.fromJson("{\"referenceText\":\"good morning\",\"gradingSystem\":\"HundredMark\",\"granularity\":\"Phoneme\",\"phonemeAlphabet\":\"IPA\",\"nBestPhonemeCount\":5}");
```
   
::: zone-end   

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = speechsdk.PronunciationAssessmentConfig(json_string="{\"referenceText\":\"good morning\",\"gradingSystem\":\"HundredMark\",\"granularity\":\"Phoneme\",\"phonemeAlphabet\":\"IPA\",\"nBestPhonemeCount\":5}")
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
var pronunciationAssessmentConfig = SpeechSDK.PronunciationAssessmentConfig.fromJSON("{\"referenceText\":\"good morning\",\"gradingSystem\":\"HundredMark\",\"granularity\":\"Phoneme\",\"phonemeAlphabet\":\"IPA\",\"nBestPhonemeCount\":5}");
```

::: zone-end

::: zone pivot="programming-language-objectivec"
   
   
```ObjectiveC
pronunciationAssessmentConfig.nbestPhonemeCount = 5;
```

::: zone-end


::: zone pivot="programming-language-swift"

```swift
pronunciationAssessmentConfig?.nbestPhonemeCount = 5
```

::: zone-end

::: zone pivot="programming-language-go"

::: zone-end

## Get pronunciation assessment results 

When speech is recognized, you can request the pronunciation assessment results as SDK objects or a JSON string. 

::: zone pivot="programming-language-csharp"

```csharp
using (var speechRecognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig))
{
    pronunciationAssessmentConfig.ApplyTo(speechRecognizer);
    var speechRecognitionResult = await speechRecognizer.RecognizeOnceAsync();

    // The pronunciation assessment result as a Speech SDK object
    var pronunciationAssessmentResult =
        PronunciationAssessmentResult.FromResult(speechRecognitionResult);

    // The pronunciation assessment result as a JSON string
    var pronunciationAssessmentResultJson = speechRecognitionResult.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult);
}
```
   
::: zone-end   

::: zone pivot="programming-language-cpp"

Word, syllable, and phoneme results aren't available via SDK objects with the Speech SDK foc C++. Word, syllable, and phoneme results are only available in the JSON string.

```cpp
auto speechRecognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    audioConfig);

pronunciationAssessmentConfig->ApplyTo(speechRecognizer);
speechRecognitionResult = speechRecognizer->RecognizeOnceAsync().get();

// The pronunciation assessment result as a Speech SDK object
auto pronunciationAssessmentResult =
    PronunciationAssessmentResult::FromResult(speechRecognitionResult);

// The pronunciation assessment result as a JSON string
auto pronunciationAssessmentResultJson = speechRecognitionResult->Properties.GetProperty(PropertyId::SpeechServiceResponse_JsonResult);
```
   
::: zone-end 

::: zone pivot="programming-language-java"
For Android application development, the word, syllable, and phoneme results are available via SDK objects with the Speech SDK foc Java. The results are also available in the JSON string. For Java Runtime (JRE) application development, the word, syllable, and phoneme results are only available in the JSON string.

```Java
SpeechRecognizer speechRecognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig);

pronunciationAssessmentConfig.applyTo(speechRecognizer);
Future<SpeechRecognitionResult> future = speechRecognizer.recognizeOnceAsync();
SpeechRecognitionResult speechRecognitionResult = future.get(30, TimeUnit.SECONDS);

// The pronunciation assessment result as a Speech SDK object
PronunciationAssessmentResult pronunciationAssessmentResult =
    PronunciationAssessmentResult.fromResult(speechRecognitionResult);

// The pronunciation assessment result as a JSON string
String pronunciationAssessmentResultJson = speechRecognitionResult.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult);

recognizer.close();
speechConfig.close();
audioConfig.close();
pronunciationAssessmentConfig.close();
speechRecognitionResult.close();
```

::: zone-end 


::: zone pivot="programming-language-javascript"

```JavaScript
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, audioConfig);

pronunciationAssessmentConfig.applyTo(speechRecognizer);

speechRecognizer.recognizeOnceAsync((speechRecognitionResult: SpeechSDK.SpeechRecognitionResult) => {
    // The pronunciation assessment result as a Speech SDK object
	var pronunciationAssessmentResult = SpeechSDK.PronunciationAssessmentResult.fromResult(speechRecognitionResult);

	// The pronunciation assessment result as a JSON string
	var pronunciationAssessmentResultJson = speechRecognitionResult.properties.getProperty(SpeechSDK.PropertyId.SpeechServiceResponse_JsonResult);
},
{});


```
   
::: zone-end  

::: zone pivot="programming-language-python"

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, \
        audio_config=audio_config)

pronunciation_assessment_config.apply_to(speech_recognizer)
speech_recognition_result = speech_recognizer.recognize_once()

# The pronunciation assessment result as a Speech SDK object
pronunciation_assessment_result = speechsdk.PronunciationAssessmentResult(speech_recognition_result)

# The pronunciation assessment result as a JSON string
pronunciation_assessment_result_json = speech_recognition_result.properties.get(speechsdk.PropertyId.SpeechServiceResponse_JsonResult)
```

::: zone-end  


::: zone pivot="programming-language-objectivec"
   
```ObjectiveC
SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                              audioConfiguration:audioConfig];

[pronunciationAssessmentConfig applyToRecognizer:speechRecognizer];

SPXSpeechRecognitionResult *speechRecognitionResult = [speechRecognizer recognizeOnce];

// The pronunciation assessment result as a Speech SDK object
SPXPronunciationAssessmentResult* pronunciationAssessmentResult = [[SPXPronunciationAssessmentResult alloc] init:speechRecognitionResult];

// The pronunciation assessment result as a JSON string
NSString* pronunciationAssessmentResultJson = [speechRecognitionResult.properties getPropertyByName:SPXSpeechServiceResponseJsonResult];
```

::: zone-end 

::: zone pivot="programming-language-swift"

```swift
let speechRecognizer = try! SPXSpeechRecognizer(speechConfiguration: speechConfig, audioConfiguration: audioConfig)

try! pronConfig.apply(to: speechRecognizer)

let speechRecognitionResult = try? speechRecognizer.recognizeOnce()

// The pronunciation assessment result as a Speech SDK object
let pronunciationAssessmentResult = SPXPronunciationAssessmentResult(speechRecognitionResult!)

// The pronunciation assessment result as a JSON string
let pronunciationAssessmentResultJson = speechRecognitionResult!.properties?.getPropertyBy(SPXPropertyId.speechServiceResponseJsonResult)
```

::: zone-end

::: zone pivot="programming-language-go"

::: zone-end

### Result parameters

This table lists some of the key pronunciation assessment results.

| Parameter | Description |
|-----------|-------------|
| `AccuracyScore` | Pronunciation accuracy of the speech. Accuracy indicates how closely the phonemes match a native speaker's pronunciation. Syllable, word, and full text accuracy scores are aggregated from phoneme-level accuracy score. |
| `FluencyScore` | Fluency of the given speech. Fluency indicates how closely the speech matches a native speaker's use of silent breaks between words. |
| `CompletenessScore` | Completeness of the speech, calculated by the ratio of pronounced words to the input reference text. |
| `PronScore` | Overall score indicating the pronunciation quality of the given speech. `PronScore` is aggregated from `AccuracyScore`, `FluencyScore`, and `CompletenessScore` with weight. |
| `ErrorType` | This value indicates whether a word is omitted, inserted, or mispronounced, compared to the `ReferenceText`. Possible values are `None`, `Omission`, `Insertion`, and `Mispronunciation`. |

### JSON result example

Pronunciation assessment results for the spoken word "hello" are shown as a JSON string in the following example. Here's what you should know:
- The phoneme [alphabet](#phoneme-alphabet-format) is IPA.
- The [syllables](#syllable-groups) are returned alongside phonemes for the same word. 
- You can use the `Offset` and `Duration` values to align syllables with their corresponding phonemes. For example, the starting offset (11700000) of the second syllable ("loʊ") aligns with the third phoneme ("l").
- There are five `NBestPhonemes` corresponding to the number of [spoken phonemes](#spoken-phoneme) requested.
- Within `Phonemes`, the most likely [spoken phonemes](#spoken-phoneme) was `"ə"` instead of the expected phoneme `"ɛ"`. The expected phoneme `"ɛ"` only received a confidence score of 47. Other potential matches received confidence scores of 52, 17, and 2. 

```json
{
	"Id": "bbb42ea51bdb46d19a1d685e635fe173",
	"RecognitionStatus": 0,
	"Offset": 7500000,
	"Duration": 13800000,
	"DisplayText": "Hello.",
	"SNR": 34.879055,
	"NBest": [
		{
			"Confidence": 0.975003,
			"Lexical": "hello",
			"ITN": "hello",
			"MaskedITN": "hello",
			"Display": "Hello.",
			"PronunciationAssessment": {
				"AccuracyScore": 100,
				"FluencyScore": 100,
				"CompletenessScore": 100,
				"PronScore": 100
			},
			"Words": [
				{
					"Word": "hello",
					"Offset": 7500000,
					"Duration": 13800000,
					"PronunciationAssessment": {
						"AccuracyScore": 99.0,
						"ErrorType": "None"
					},
					"Syllables": [
						{
							"Syllable": "hɛ",
							"PronunciationAssessment": {
								"AccuracyScore": 91.0
							},
							"Offset": 7500000,
                            "Duration": 4100000
						},
						{
							"Syllable": "loʊ",
							"PronunciationAssessment": {
								"AccuracyScore": 100.0
							},
							"Offset": 11700000,
                            "Duration": 9600000
						}
					],
					"Phonemes": [
						{
							"Phoneme": "h",
							"PronunciationAssessment": {
								"AccuracyScore": 98.0,
								"NBestPhonemes": [
									{
										"Phoneme": "h",
										"Score": 100.0
									},
									{
										"Phoneme": "oʊ",
										"Score": 52.0
									},
									{
										"Phoneme": "ə",
										"Score": 35.0
									},
									{
										"Phoneme": "k",
										"Score": 23.0
									},
									{
										"Phoneme": "æ",
										"Score": 20.0
									}
								]
							},
							"Offset": 7500000,
                            "Duration": 3500000
						},
						{
							"Phoneme": "ɛ",
							"PronunciationAssessment": {
								"AccuracyScore": 47.0,
								"NBestPhonemes": [
									{
										"Phoneme": "ə",
										"Score": 100.0
									},
									{
										"Phoneme": "l",
										"Score": 52.0
									},
									{
										"Phoneme": "ɛ",
										"Score": 47.0
									},
									{
										"Phoneme": "h",
										"Score": 17.0
									},
									{
										"Phoneme": "æ",
										"Score": 2.0
									}
								]
							},
							"Offset": 11100000,
                            "Duration": 500000
						},
						{
							"Phoneme": "l",
							"PronunciationAssessment": {
								"AccuracyScore": 100.0,
								"NBestPhonemes": [
									{
										"Phoneme": "l",
										"Score": 100.0
									},
									{
										"Phoneme": "oʊ",
										"Score": 46.0
									},
									{
										"Phoneme": "ə",
										"Score": 5.0
									},
									{
										"Phoneme": "ɛ",
										"Score": 3.0
									},
									{
										"Phoneme": "u",
										"Score": 1.0
									}
								]
							},
							"Offset": 11700000,
                            "Duration": 1100000
						},
						{
							"Phoneme": "oʊ",
							"PronunciationAssessment": {
								"AccuracyScore": 100.0,
								"NBestPhonemes": [
									{
										"Phoneme": "oʊ",
										"Score": 100.0
									},
									{
										"Phoneme": "d",
										"Score": 29.0
									},
									{
										"Phoneme": "t",
										"Score": 24.0
									},
									{
										"Phoneme": "n",
										"Score": 22.0
									},
									{
										"Phoneme": "l",
										"Score": 18.0
									}
								]
							},
							"Offset": 12900000,
                            "Duration": 8400000
						}
					]
				}
			]
		}
	]
}
```

## Next steps

- Try out [pronunciation assessment in Speech Studio](pronunciation-assessment-tool.md)
- Try out the [pronunciation assessment demo](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment/BrowserJS) and watch the [video tutorial](https://www.youtube.com/watch?v=zFlwm7N4Awc) of pronunciation assessment.
