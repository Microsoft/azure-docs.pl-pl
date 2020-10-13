---
title: Jak śledzić użycie pamięci zestawu SDK mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Zestaw SDK usługi mowy obsługuje wiele języków programowania do konwersji mowy na tekst i zamiany tekstu na mowę oraz Tłumaczenie mowy. W tym artykule opisano narzędzia do zarządzania pamięcią wbudowane w zestaw SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: c7e74ce8f4d0b7889d2e5bcd117eaa43e2b6991b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934144"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Jak śledzić użycie pamięci zestawu SDK mowy

Zestaw Speech SDK jest oparty na bazie kodu natywnego, która jest rzutowana na wiele języków programowania za pośrednictwem szeregu warstw współdziałania. Każdy rzut charakterystyczny dla języka ma idiomatically poprawne funkcje do zarządzania cyklem życia obiektu. Ponadto zestaw SDK mowy zawiera narzędzia do zarządzania pamięcią do śledzenia użycia zasobów przy użyciu rejestrowania obiektów i limitów obiektów. 

## <a name="how-to-read-object-logs"></a>Jak odczytać dzienniki obiektów

Jeśli [rejestrowanie zestawu SDK mowy jest włączone](how-to-use-logging.md), śledzenie tagów jest emitowane, aby umożliwić obserwację obiektu historycznego. Tagi te obejmują: 

* `TrackHandle` lub `StopTracking` 
* Typ obiektu
* Bieżąca liczba obiektów, które są śledzone w typie obiektu i aktualnie śledzone liczby.

Oto przykładowy dziennik: 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Ustaw próg ostrzeżenia

Istnieje możliwość utworzenia progu ostrzegawczego. w przypadku przekroczenia progu (przy założeniu, że rejestrowanie jest włączone) jest rejestrowany komunikat ostrzegawczy. Komunikat ostrzegawczy zawiera zrzut wszystkich obiektów w istnieniu wraz z ich liczbą. Te informacje mogą służyć do lepszego zrozumienia problemów. 

Aby włączyć próg ostrzeżenia, musi on być określony dla `SpeechConfig` obiektu. Ten obiekt jest sprawdzany po utworzeniu nowego aparatu rozpoznawania. W poniższych przykładach Przypuśćmy, że utworzono wystąpienie o `SpeechConfig` nazwie `config` :

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> Wartość domyślna tej właściwości to 10 000.

## <a name="set-an-error-threshold"></a>Ustaw próg błędu 

Korzystając z zestawu Speech SDK, można ustawić maksymalną dozwoloną liczbę obiektów w danym momencie. Jeśli to ustawienie jest włączone, gdy zostanie osiągnięta maksymalna liczba, próby utworzenia nowych obiektów aparatu rozpoznawania zakończą się niepowodzeniem. Istniejące obiekty będą nadal działały.

Oto przykład błędu:

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

Aby włączyć próg błędu, musi on być określony dla `SpeechConfig` obiektu. Ten obiekt jest sprawdzany po utworzeniu nowego aparatu rozpoznawania. W poniższych przykładach Przypuśćmy, że utworzono wystąpienie o `SpeechConfig` nazwie `config` :

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> Wartością domyślną dla tej właściwości jest wartość maksymalna dla typu danych specyficzna dla platformy `size_t` . Typowym rozpoznawaniem będzie zużywać się od 7 do 10 obiektów wewnętrznych.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej na temat zestawu Speech SDK](speech-sdk.md)