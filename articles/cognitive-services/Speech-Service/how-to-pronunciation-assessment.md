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
ms.date: 09/29/2020
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-set-nineteen
ms.openlocfilehash: 1cc313daf0e76ddd14865959410b07d9bdc189d7
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94984925"
---
# <a name="pronunciation-assessment"></a>Ocena wymowy

Ocena wymowy ocenia wymowę mowy i zapewnia nam opinię na temat dokładności i Fluency głosu.
Dzięki ocenie wymowy, informacje o języku mogą być praktyczne, otrzymywać błyskawiczne informacje zwrotne i ulepszać ich wymowę, aby mogły mówić i być niebezpieczne.
Osoby wykorzystujące wykładowcy mogą oszacować wymowę wielu głośników w czasie rzeczywistym.

W tym artykule dowiesz się, jak skonfigurować `PronunciationAssessmentConfig` i pobrać `PronunciationAssessmentResult` przy użyciu zestawu Speech SDK.

> [!NOTE]
> Funkcja oceny wymowy jest obecnie dostępna tylko w regionach `westus` `eastasia` i i `centralindia` obsługuje tylko język `en-US` .

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

## <a name="next-steps"></a>Następne kroki

<!-- TODO: update the sample links after release -->

<!-- ::: zone pivot="programming-language-csharp"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L741) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-cpp"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L507) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-java"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L521) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-python"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_synthesis_sample.py#L434) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-objectivec"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L494) on GitHub for automatic language detection
::: zone-end -->

* [Dokumentacja referencyjna zestawu Speech SDK](speech-sdk.md)
