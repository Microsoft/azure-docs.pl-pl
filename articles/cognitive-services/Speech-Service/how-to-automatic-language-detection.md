---
title: Jak używać automatycznego wykrywania języka dla zamiany mowy na tekst
titleSuffix: Azure Cognitive Services
description: Zestaw Speech SDK obsługuje automatyczne wykrywanie języka dla zamiany mowy na tekst. W przypadku korzystania z tej funkcji, podany dźwięk jest porównywany z podaną listą języków i jest określana najprawdopodobniej. Zwracana wartość może następnie zostać użyta do wybrania modelu języka używanego do zamiany mowy na tekst.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: trbye
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: e62333f241cf24620773314fb7aa4846af651eaf
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185873"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Automatyczne wykrywanie języka dla zamiany mowy na tekst

Funkcja automatycznego wykrywania języka służy do określania najbardziej pasującego dźwięku przekazanego do zestawu Speech SDK w porównaniu z listą podanych języków. Wartość zwracana przez funkcję automatycznego wykrywania języka służy do wybierania modelu języka dla zamiany mowy na tekst, co zapewnia dokładniejsze transkrypcje. Aby zobaczyć, które języki są dostępne, zobacz temat [Obsługa języków](language-support.md).

W tym artykule dowiesz się, jak `AutoDetectSourceLanguageConfig` utworzyć `SpeechRecognizer` obiekt i pobrać wykryty język przy użyciu programu.

> [!IMPORTANT]
> Ta funkcja jest dostępna tylko dla zestawu Speech SDK z językiem C#, C++, Java, Python, JavaScript i celu-C.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Automatyczne wykrywanie języka za pomocą zestawu Speech SDK

Funkcja automatycznego wykrywania języka ma obecnie limit czterech języków po stronie usług. To ograniczenie należy wziąć pod uwagę podczas konstruowania `AudoDetectSourceLanguageConfig` obiektu. W poniższych przykładach utworzysz `AutoDetectSourceLanguageConfig` , a następnie użyjesz go do utworzenia `SpeechRecognizer` .

> [!TIP]
> Możesz również określić model niestandardowy, który ma być używany podczas wykonywania zamiany mowy na tekst. Aby uzyskać więcej informacji, zobacz [Korzystanie z modelu niestandardowego na potrzeby automatycznego wykrywania języka](#use-a-custom-model-for-automatic-language-detection).

Poniższe fragmenty kodu ilustrują sposób korzystania z automatycznego wykrywania języka w aplikacjach:

::: zone pivot="programming-language-csharp"

```csharp
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE" });

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult =
        AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig
    );

speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
auto_detect_source_language_config = \
        speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["en-US", "de-DE"])
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, 
        auto_detect_source_language_config=auto_detect_source_language_config, 
        audio_config=audio_config)
result = speech_recognizer.recognize_once()
auto_detect_source_language_result = speechsdk.AutoDetectSourceLanguageResult(result)
detected_language = auto_detect_source_language_result.language
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
NSArray *languages = @[@"zh-CN", @"de-DE"];
SPXAutoDetectSourceLanguageConfiguration* autoDetectSourceLanguageConfig = \
        [[SPXAutoDetectSourceLanguageConfiguration alloc]init:languages];
SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                           autoDetectSourceLanguageConfiguration:autoDetectSourceLanguageConfig
                                              audioConfiguration:audioConfig];
SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXAutoDetectSourceLanguageResult *languageDetectionResult = [[SPXAutoDetectSourceLanguageResult alloc] init:result];
NSString *detectedLanguage = [languageDetectionResult language];
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var autoDetectConfig = SpeechSDK.AutoDetectSourceLanguageConfig.fromLanguages(["en-US", "de-DE"]);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, autoDetectConfig, audioConfig);
speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var languageDetectionResult = SpeechSDK.AutoDetectSourceLanguageResult.fromResult(result);
        var detectedLanguage = languageDetectionResult.language;
},
{});
```

::: zone-end

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Używanie modelu niestandardowego do automatycznego wykrywania języka

Oprócz wykrywania języka przy użyciu modeli usługi Speech Service można określić model niestandardowy do rozszerzonego rozpoznawania. Jeśli niestandardowy model nie zostanie podany, usługa użyje domyślnego modelu języka.

Poniższe fragmenty kodu ilustrują sposób określania niestandardowego modelu w wywołaniu usługi mowy. Jeśli wykryty język to `en-US` , używany jest domyślny model. Jeśli wykryty język to `fr-FR` , używany jest punkt końcowy dla modelu niestandardowego:

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
 en_language_config = speechsdk.languageconfig.SourceLanguageConfig("en-US")
 fr_language_config = speechsdk.languageconfig.SourceLanguageConfig("fr-FR", "The Endpoint Id for custom model of fr-FR")
 auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(
        sourceLanguageConfigs=[en_language_config, fr_language_config])
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSourceLanguageConfiguration* enLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"en-US"];
SPXSourceLanguageConfiguration* frLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"fr-FR"
                                                     endpointId:@"The Endpoint Id for custom model of fr-FR"];
NSArray *languageConfigs = @[enLanguageConfig, frLanguageConfig];
SPXAutoDetectSourceLanguageConfiguration* autoDetectSourceLanguageConfig = \
        [[SPXAutoDetectSourceLanguageConfiguration alloc]initWithSourceLanguageConfigurations:languageConfigs];
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var enLanguageConfig = SpeechSDK.SourceLanguageConfig.fromLanguage("en-US");
var frLanguageConfig = SpeechSDK.SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR");
var autoDetectConfig = SpeechSDK.AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs([enLanguageConfig, frLanguageConfig]);
```

::: zone-end

## <a name="next-steps"></a>Następne kroki

::: zone pivot="programming-language-csharp"
* Zobacz [przykładowy kod](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L741) w usłudze GitHub, aby automatycznie wykrywać język
::: zone-end

::: zone pivot="programming-language-cpp"
* Zobacz [przykładowy kod](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L507) w usłudze GitHub, aby automatycznie wykrywać język
::: zone-end

::: zone pivot="programming-language-java"
* Zobacz [przykładowy kod](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L521) w usłudze GitHub, aby automatycznie wykrywać język
::: zone-end

::: zone pivot="programming-language-python"
* Zobacz [przykładowy kod](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py#L458) w usłudze GitHub, aby automatycznie wykrywać język
::: zone-end

::: zone pivot="programming-language-objectivec"
* Zobacz [przykładowy kod](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L525) w usłudze GitHub, aby automatycznie wykrywać język
::: zone-end

* [Dokumentacja referencyjna zestawu Speech SDK](speech-sdk.md)
