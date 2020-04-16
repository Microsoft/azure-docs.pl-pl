---
title: Jak używać automatycznego wykrywania języka mowy na tekst
titleSuffix: Azure Cognitive Services
description: Zestaw SDK mowy obsługuje automatyczne wykrywanie języka mowy na tekst. Podczas korzystania z tej funkcji, audio pod warunkiem, jest porównywany z podanej listy języków i najbardziej prawdopodobne dopasowanie jest określana. Zwrócona wartość może następnie służyć do wybierania modelu języka używanego do mowy na tekst.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: fefbe793fa4a6b90ba9bf8d468d42dcbd315759c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402212"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Automatyczne wykrywanie języka mowy na tekst

Automatyczne wykrywanie języka służy do określenia najbardziej prawdopodobne dopasowanie audio przekazywane do SDK mowy w porównaniu z listą podanych języków. Wartość zwracana przez automatyczne wykrywanie języka jest następnie używana do wybierania modelu języka mowy na tekst, zapewniając dokładniejszą transkrypcję. Aby zobaczyć, które języki są dostępne, zobacz [Obsługa języków](language-support.md).

W tym artykule dowiesz się, jak użyć `AutoDetectSourceLanguageConfig` do skonstruowania `SpeechRecognizer` obiektu i pobrać wykryty język.

> [!IMPORTANT]
> Ta funkcja jest dostępna tylko dla SDK mowy dla języka C#, C++, Java i Python.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Automatyczne wykrywanie języka za pomocą sdk mowy

Automatyczne wykrywanie języka ma obecnie limit po stronie usług dwóch języków na wykrycie. Należy pamiętać o tym `AudoDetectSourceLanguageConfig` ograniczeniu podczas budowy obiektu. W poniższych przykładach utworzysz `AutoDetectSourceLanguageConfig`program , a `SpeechRecognizer`następnie użyje go do skonstruowania pliku .

> [!TIP]
> Można również określić model niestandardowy, który ma być używany podczas wykonywania mowy na tekst. Aby uzyskać więcej informacji, zobacz [Automatyczne wykrywanie języka za pomocą modelu niestandardowego.](#use-a-custom-model-for-automatic-language-detection)

Poniższe fragmenty kodu ilustrują sposób używania automatycznego wykrywania języka w aplikacjach:

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
    audioConfig);

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

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Automatyczne wykrywanie języka za pomocą modelu niestandardowego

Oprócz wykrywania języka przy użyciu modeli usługi mowy, można określić model niestandardowy dla rozszerzonego rozpoznawania. Jeśli model niestandardowy nie jest dostarczany, usługa użyje domyślnego modelu języka.

Poniższe fragmenty kodu ilustrują sposób określania modelu niestandardowego w wywołaniu usługi Mowy. Jeśli wykryty język `en-US`jest , to używany jest model domyślny. Jeśli wykryty język `fr-FR`jest , a następnie punkt końcowy dla modelu niestandardowego jest używany:

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

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja referencyjna SDK mowy](speech-sdk.md)
