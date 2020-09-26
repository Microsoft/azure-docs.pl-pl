---
title: Jak określić język źródłowy dla zamiany mowy na tekst
titleSuffix: Azure Cognitive Services
description: Zestaw Speech SDK umożliwia określenie języka źródłowego podczas konwertowania mowy na tekst. W tym artykule opisano sposób korzystania z metod FromConfig i SourceLanguageConfig w celu umożliwienia usłudze mowy znajomości języka źródłowego i dostarczania niestandardowego obiektu docelowego modelu.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1b134fd3d09eeda340e7323638a36b68336242c2
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362031"
---
# <a name="specify-source-language-for-speech-to-text"></a>Określ język źródłowy dla zamiany mowy na tekst

W tym artykule dowiesz się, jak określić język źródłowy wejścia audio przesłanego do zestawu Speech SDK na potrzeby rozpoznawania mowy. Dodatkowo przykładowy kod jest dostarczany, aby określić niestandardowy model mowy na potrzeby ulepszonego rozpoznawania.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Jak określić język źródłowy w języku C #

W poniższym przykładzie język źródłowy jest dostarczany jawnie jako parametr przy użyciu `SpeechRecognizer` konstrukcji.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

W poniższym przykładzie język źródłowy jest dostarczany przy użyciu `SourceLanguageConfig` . Następnie `sourceLanguageConfig` jest przenoszona jako parametr do `SpeechRecognizer` konstruowania.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

W poniższym przykładzie język źródłowy i niestandardowy punkt końcowy są udostępniane przy użyciu polecenia `SourceLanguageConfig` . Następnie `sourceLanguageConfig` jest przenoszona jako parametr do `SpeechRecognizer` konstruowania.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage` i `EndpointId` metody Set są przestarzałe z `SpeechConfig` klasy w języku C#. Korzystanie z tych metod nie jest odradzane i nie powinno być używane podczas konstruowania `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Jak określić język źródłowy w języku C++

W poniższym przykładzie język źródłowy jest dostarczany jawnie jako parametr przy użyciu `FromConfig` metody.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

W poniższym przykładzie język źródłowy jest dostarczany przy użyciu `SourceLanguageConfig` . Następnie `sourceLanguageConfig` jest przenoszona jako parametr do `FromConfig` podczas tworzenia `recognizer` .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

W poniższym przykładzie język źródłowy i niestandardowy punkt końcowy są udostępniane przy użyciu polecenia `SourceLanguageConfig` . `sourceLanguageConfig`Jest przenoszona jako parametr do `FromConfig` podczas tworzenia `recognizer` .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` i `SetEndpointId` są przestarzałymi metodami z `SpeechConfig` klasy w językach C++ i Java. Korzystanie z tych metod nie jest odradzane i nie powinno być używane podczas konstruowania `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Jak określić język źródłowy w języku Java

W poniższym przykładzie język źródłowy jest dostarczany jawnie podczas tworzenia nowego `SpeechRecognizer` .

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

W poniższym przykładzie język źródłowy jest dostarczany przy użyciu `SourceLanguageConfig` . Następnie `sourceLanguageConfig` jest przenoszona jako parametr podczas tworzenia nowego `SpeechRecognizer` .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

W poniższym przykładzie język źródłowy i niestandardowy punkt końcowy są udostępniane przy użyciu polecenia `SourceLanguageConfig` . Następnie `sourceLanguageConfig` jest przenoszona jako parametr podczas tworzenia nowego `SpeechRecognizer` .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` i `setEndpointId` są przestarzałymi metodami z `SpeechConfig` klasy w językach C++ i Java. Korzystanie z tych metod nie jest odradzane i nie powinno być używane podczas konstruowania `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Jak określić język źródłowy w języku Python

W poniższym przykładzie język źródłowy jest dostarczany jawnie jako parametr przy użyciu `SpeechRecognizer` konstrukcji.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

W poniższym przykładzie język źródłowy jest dostarczany przy użyciu `SourceLanguageConfig` . Następnie `SourceLanguageConfig` jest przenoszona jako parametr do `SpeechRecognizer` konstruowania.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

W poniższym przykładzie język źródłowy i niestandardowy punkt końcowy są udostępniane przy użyciu polecenia `SourceLanguageConfig` . Następnie `SourceLanguageConfig` jest przenoszona jako parametr do `SpeechRecognizer` konstruowania.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language` i `endpoint_id` właściwości są przestarzałe z `SpeechConfig` klasy w języku Python. Nie zaleca się używania tych właściwości i nie należy ich używać podczas konstruowania `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Jak określić język źródłowy w języku JavaScript

Pierwszym krokiem jest utworzenie `SpeechConfig` :

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Następnie określ język źródłowy dźwięku przy użyciu `speechRecognitionLanguage` :

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Jeśli używasz modelu niestandardowego do rozpoznawania, możesz określić punkt końcowy z `endpointId` :

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Jak określić język źródłowy w zamierzeniu-C

W poniższym przykładzie język źródłowy jest dostarczany jawnie jako parametr przy użyciu `SPXSpeechRecognizer` konstrukcji.

```Objective-C
SPXSpeechRecognizer* speechRecognizer = \
    [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig language:@"de-DE" audioConfiguration:audioConfig];
```

W poniższym przykładzie język źródłowy jest dostarczany przy użyciu `SPXSourceLanguageConfiguration` . Następnie `SPXSourceLanguageConfiguration` jest przenoszona jako parametr do `SPXSpeechRecognizer` konstruowania.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"de-DE"];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

W poniższym przykładzie język źródłowy i niestandardowy punkt końcowy są udostępniane przy użyciu polecenia `SPXSourceLanguageConfiguration` . Następnie `SPXSourceLanguageConfiguration` jest przenoszona jako parametr do `SPXSpeechRecognizer` konstruowania.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"de-DE"
                                                     endpointId:@"The Endpoint ID for your custom model."];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

>[!Note]
> `speechRecognitionLanguage` i `endpointId` właściwości są przestarzałe z `SPXSpeechConfiguration` klasy w zamierzeniu-C. Nie zaleca się używania tych właściwości i nie należy ich używać podczas konstruowania `SPXSpeechRecognizer` .

::: zone-end

## <a name="see-also"></a>Zobacz też

* Aby uzyskać listę obsługiwanych języków i ustawień regionalnych dla zamiany mowy na tekst, zobacz temat [Obsługa języków](language-support.md).

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja referencyjna zestawu Speech SDK](speech-sdk.md)