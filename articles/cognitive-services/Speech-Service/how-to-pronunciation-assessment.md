---
title: Jak używać zestawu Speech SDK do oceny wymowy
titleSuffix: Azure Cognitive Services
description: Zestaw Speech SDK obsługuje ocenę wymowy, która ocenia jakość wymowy danych wejściowych mowy, ze wskaźnikami dokładności, Fluency, kompletności itp.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 2d1b5e490b7c8212e6103e3d169c1b5491d01dde
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167434"
---
# <a name="pronunciation-assessment"></a>Ocena wymowy

Ocena wymowy ocenia wymowę mowy i zapewnia nam opinię na temat dokładności i Fluency głosu.
Dzięki ocenie wymowy, informacje o języku mogą być praktyczne, otrzymywać błyskawiczne informacje zwrotne i ulepszać ich wymowę, aby mogły mówić i być niebezpieczne.
Osoby wykorzystujące wykładowcy mogą oszacować wymowę wielu głośników w czasie rzeczywistym.

W tym artykule dowiesz się, jak skonfigurować `PronunciationAssessmentConfig` i pobrać `PronunciationAssessmentResult` przy użyciu zestawu Speech SDK.

> [!NOTE]
> Funkcja oceny wymowy obsługuje obecnie `en-US` język, który jest dostępny we wszystkich [regionach zamiany mowy na tekst](regions.md#speech-to-text-text-to-speech-and-translation). Pomoc techniczna dla `en-GB` `zh-CN` języków i jest w wersji zapoznawczej, która jest dostępna w `westus` `eastasia` `centralindia` regionach i.

## <a name="pronunciation-assessment-with-the-speech-sdk"></a>Ocena wymowy przy użyciu zestawu Speech SDK

W poniższych przykładach utworzysz `PronunciationAssessmentConfig` , a następnie zastosujesz go do `SpeechRecognizer` .

Poniższe fragmenty kodu ilustrują sposób korzystania z automatycznego wykrywania języka w aplikacjach:

::: zone pivot="programming-language-csharp"

```csharp
var pronunciationAssessmentConfig = new PronunciationAssessmentConfig(
    "reference text", GradingSystem.HundredMark, Granularity.Phoneme);

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig))
{
    // apply the pronunciation assessment configuration to the speech recognizer
    pronunciationAssessmentConfig.ApplyTo(recognizer);
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var pronunciationAssessmentResult =
        PronunciationAssessmentResult.FromResult(speechRecognitionResult);
    var pronunciationScore = pronunciationAssessmentResult.PronunciationScore;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig =
    PronunciationAssessmentConfig::Create("reference text",
        PronunciationAssessmentGradingSystem::HundredMark,
        PronunciationAssessmentGranularity::Phoneme);

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig->ApplyTo(recognizer);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto pronunciationAssessmentResult =
    PronunciationAssessmentResult::FromResult(speechRecognitionResult);
auto pronunciationScore = pronunciationAssessmentResult->PronunciationScore;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
PronunciationAssessmentConfig pronunciationAssessmentConfig =
    new PronunciationAssessmentConfig("reference text", 
        PronunciationAssessmentGradingSystem.HundredMark,
        PronunciationAssessmentGranularity.Phoneme);

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(recognizer);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
PronunciationAssessmentResult pronunciationAssessmentResult =
    PronunciationAssessmentResult.fromResult(result);
Double pronunciationScore = pronunciationAssessmentResult.getPronunciationScore();

recognizer.close();
speechConfig.close();
audioConfig.close();
pronunciationAssessmentConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = \
        speechsdk.PronunciationAssessmentConfig(reference_text='reference text',
                grading_system=msspeech.PronunciationAssessmentGradingSystem.HundredMark,
                granularity=msspeech.PronunciationAssessmentGranularity.Phoneme)
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, \
        audio_config=audio_config)

# apply the pronunciation assessment configuration to the speech recognizer
pronunciation_assessment_config.apply_to(speech_recognizer)
result = speech_recognizer.recognize_once()
pronunciation_assessment_result = speechsdk.PronunciationAssessmentResult(result)
pronunciation_score = pronunciation_assessment_result.pronunciation_score
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var pronunciationAssessmentConfig = new SpeechSDK.PronunciationAssessmentConfig("reference text",
    PronunciationAssessmentGradingSystem.HundredMark,
    PronunciationAssessmentGranularity.Word, true);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, audioConfig);
// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(speechRecognizer);

speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var pronunciationAssessmentResult = SpeechSDK.PronunciationAssessmentResult.fromResult(result);
        var pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
        var wordLevelResult = pronunciationAssessmentResult.detailResult.Words;
},
{});
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXPronunciationAssessmentConfiguration* pronunciationAssessmentConfig =
    [[SPXPronunciationAssessmentConfiguration alloc]init:@"reference text"
                                           gradingSystem:SPXPronunciationAssessmentGradingSystem_HundredMark
                                             granularity:SPXPronunciationAssessmentGranularity_Phoneme];

SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                              audioConfiguration:audioConfig];

// apply the pronunciation assessment configuration to the speech recognizer
[pronunciationAssessmentConfig applyToRecognizer:speechRecognizer];

SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXPronunciationAssessmentResult* pronunciationAssessmentResult = [[SPXPronunciationAssessmentResult alloc] init:result];
double pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
```

::: zone-end

### <a name="pronunciation-assessment-configuration-parameters"></a>Parametry konfiguracji oceny wymowy

Ta tabela zawiera listę parametrów konfiguracji dla oceny wymowy.

| Parametr | Opis | Wymagane? |
|-----------|-------------|---------------------|
| ReferenceText | Tekst, dla którego będzie oceniana wymowa. | Wymagane |
| GradingSystem | System punktu do kalibracji oceny. `FivePoint`System daje 0-5 wynik zmiennoprzecinkowy i `HundredMark` daje wynik o wartości 0-100 zmiennoprzecinkowej. Wartość domyślna: `FivePoint`. | Opcjonalne |
| Poziom szczegółowości | Stopień szczegółowości oceny. Akceptowane wartości to `Phoneme` , które pokazują wynik na poziomie pełnego tekstu, wyrazów i fonem, `Word` który pokazuje wynik na poziomie pełnego tekstu i słowa, `FullText` który pokazuje wynik tylko na poziomie pełnego tekstu. Wartość domyślna: `Phoneme`. | Opcjonalne |
| EnableMiscue | Włącza Obliczanie miscue. Po włączeniu tej możliwości wymawiane wyrazy będą porównywane z tekstem odwołania i będą oznaczone jako pomijania/wstawiania na podstawie porównania. Akceptowane wartości to `False` i `True` . Wartość domyślna: `False`. | Opcjonalne |
| ScenarioId | Identyfikator GUID wskazujący dostosowany system punktów. | Opcjonalne |

### <a name="pronunciation-assessment-result-parameters"></a>Parametry wyniku oceny wymowy

W tej tabeli wymieniono parametry wynikowe oceny wymowy.

| Parametr | Opis |
|-----------|-------------|
| `AccuracyScore` | Dokładność wymowy mowy. Dokładność wskazuje, jak blisko fonemów jest zgodny z wymowyeniem prezentera natywnego. Wynik dokładności wyrazu i pełnego tekstu jest agregowany z oceny dokładności poziomu fonem. |
| `FluencyScore` | Fluency danego mowy. Fluency wskazuje, jak ściśle funkcja mowy dopasowuje ciche przerwy między wyrazami. |
| `CompletenessScore` | Kompletność mowy, ustalona przez obliczenie współczynnika wymawianych wyrazów w odniesieniu do danych wejściowych tekstu. |
| `PronunciationScore` | Ogólny wynik wskazujący jakość wymowy danego mowy. Ta wartość jest agregowana `AccuracyScore` z `FluencyScore` i `CompletenessScore` wagi. |
| `ErrorType` | Ta wartość wskazuje, czy słowo zostanie pominięte, wstawione lub nieprawidłowo wypowiadane, w porównaniu z `ReferenceText` . Możliwe wartości to `None` (brak błędu w tym wyrazie), `Omission` `Insertion` i `Mispronunciation` . |

### <a name="sample-responses"></a>Przykładowe odpowiedzi

Typowy wynik oceny wymowy w formacie JSON:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronunciationAssessment":
        {
            "PronScore" : 84.4,
            "AccuracyScore" : 100.0,
            "FluencyScore" : 74.0,
            "CompletenessScore" : 100.0,
        },
        "Words": [
            {
              "Word" : "Good",
              "Offset" : 500000,
              "Duration" : 2700000,
              "PronunciationAssessment":
              {
                "AccuracyScore" : 100.0,
                "ErrorType" : "None"
              }
            },
            {
              "Word" : "morning",
              "Offset" : 5300000,
              "Duration" : 900000,
              "PronunciationAssessment":
              {
                "AccuracyScore" : 100.0,
                "ErrorType" : "None"
              }
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

<!-- TODO: update JavaScript sample links after release -->

* Obejrzyj samouczek [wprowadzenie do filmów wideo](https://www.youtube.com/watch?v=cBE8CUHOFHQ) i [wideo](https://www.youtube.com/watch?v=zFlwm7N4Awc) z oceny wymowy

* Wypróbuj [Pokaz oceny wymowy](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment/BrowserJS)

::: zone pivot="programming-language-csharp"
* Zapoznaj się z [przykładowym kodem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L949) w serwisie GitHub, aby uzyskać ocenę wymowy.
::: zone-end

::: zone pivot="programming-language-cpp"
* Zapoznaj się z [przykładowym kodem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L633) w serwisie GitHub, aby uzyskać ocenę wymowy.
::: zone-end

::: zone pivot="programming-language-java"
* Zapoznaj się z [przykładowym kodem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L697) w serwisie GitHub, aby uzyskać ocenę wymowy.
::: zone-end

::: zone pivot="programming-language-python"
* Zapoznaj się z [przykładowym kodem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py#L576) w serwisie GitHub, aby uzyskać ocenę wymowy.
::: zone-end

::: zone pivot="programming-language-objectivec"
* Zapoznaj się z [przykładowym kodem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L642) w serwisie GitHub, aby uzyskać ocenę wymowy.
::: zone-end

* [Dokumentacja referencyjna zestawu Speech SDK](speech-sdk.md)

* [Tworzenie bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/cognitive-services/)
