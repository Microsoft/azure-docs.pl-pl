---
title: Obsługa języków — LUIS
titleSuffix: Azure Cognitive Services
description: Usługa LUIS oferuje wiele funkcji. Nie wszystkie funkcje są równoważne w każdym języku. Upewnij się, że interesujące Cię funkcje są obsługiwane w Twojej docelowej kulturze językowej. Aplikacja LUIS jest specyficzna dla kultury i nie można jej zmienić po jej ustawieniu.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 1da43d0ef208d61ced6c8ca8dbc7603e0ef51155
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "96021575"
---
# <a name="language-and-region-support-for-luis"></a>Obsługa języka i regionu w programie LUIS

Usługa LUIS oferuje wiele funkcji. Nie wszystkie funkcje są równoważne w każdym języku. Upewnij się, że interesujące Cię funkcje są obsługiwane w Twojej docelowej kulturze językowej. Aplikacja LUIS jest specyficzna dla kultury i nie można jej zmienić po jej ustawieniu.

## <a name="multi-language-luis-apps"></a>Aplikacje LUIS z obsługą wielu języków

Jeśli potrzebujesz LUIS aplikacji klienckiej z obsługą wielu języków, takiej jak chatbot, masz kilka opcji. Jeśli LUIS obsługuje wszystkie języki, tworzysz aplikację LUIS dla każdego języka. Każda aplikacja LUIS ma unikatowy identyfikator aplikacji i dziennik punktów końcowych. Jeśli musisz zapewnić zrozumienie języka dla LUIS języka nie obsługuje, możesz użyć [usługi translatora](../Translator/translator-info-overview.md) , aby przetłumaczyć wypowiedź w obsługiwany język, przesłać wypowiedź do punktu końcowego Luis i otrzymać wynikowe wyniki.

## <a name="languages-supported"></a>Obsługiwane języki

LUIS rozumie wyrażenia długości w następujących językach:

| Język |Regionalne  |  Preskompilowana domena | Wstępnie utworzona jednostka | Zalecenia dotyczące listy fraz | **[Analiza tekstu](../text-analytics/language-support.md)<br>(Tonacji i<br>Służąc|
|--|--|:--:|:--:|:--:|:--:|
| Angielski (Stany Zjednoczone) |`en-US` | ✔ | ✔  |✔|✔|
| Arabski (wersja zapoznawcza — nowoczesny Standard arabski) |`ar-AR`|-|-|-|-|
| *[Chiński](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Niderlandzki |`nl-NL` |✔|-|-|✔|
| Francuski (Francja) |`fr-FR` |✔| ✔ |✔ |✔|
| francuski (Kanada) |`fr-CA` |-|-|-|✔|
| Niemiecki |`de-DE` |✔| ✔ |✔ |✔|
| Gudżarati | `gu-IN`|-|-|-|-|
| Hindi | `hi-IN`|-|✔|-|-|
| Włoski |`it-IT` |✔| ✔ |✔|✔|
| *[Japoński](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Tylko fraza klucza|
| Koreański |`ko-KR` |✔|-|-|Tylko fraza klucza|
| Marathi | `mr-IN`|-|-|-|-|
| Portugalski (Brazylia) |`pt-BR` |✔| ✔ |✔ |nie wszystkie hodowle podrzędne|
| hiszpański (Hiszpania) |`es-ES` |✔| ✔ |✔|✔|
| Hiszpański (Meksyk)|`es-MX` |-|-|✔|✔|
| Tamilski | `ta-IN`|-|-|-|-|
| Telugu | `te-IN`|-|-|-|-|
| Turecki | `tr-TR` |✔|✔|-|Tylko tonacji|




Obsługa języków jest różna w przypadku [wstępnie utworzonych jednostek](luis-reference-prebuilt-entities.md) i [prekompilowanych domen](luis-reference-prebuilt-domains.md).

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>* Uwagi dotyczące pomocy technicznej dla języka japońskiego

 - Ponieważ LUIS nie zapewnia analizy składni i nie będzie zrozumieć różnicy między Keigo i nieformalnym japońskim, należy dołączyć różne poziomy formalne, jak przykłady szkolenia dla aplikacji.
     - でございます nie jest taka sama jak です.
     - です nie jest taka sama jak だ.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Obsługiwane języki Speech API
Zobacz [języki obsługiwane](../speech-service/speech-to-text.md) przez mowę dla języków trybu dyktowania mowy.

### <a name="bing-spell-check-supported-languages"></a>Obsługiwane języki sprawdzanie pisowni Bing
Zapoznaj się z listą obsługiwanych języków i Stanów w sprawdzanie pisowni Bing [obsługiwanych językach](../bing-spell-check/language-support.md) .

## <a name="rare-or-foreign-words-in-an-application"></a>Rzadkie lub obce słowa w aplikacji
W `en-us` kulturze Luis się z rozróżnieniem większości wyrazów angielskich, w tym żargonu. W `zh-cn` kulturze Luis się z rozróżnieniem większości znaków w języku chińskim. Jeśli używasz rzadkich wyrazów w `en-us` znakach `zh-cn` i zobaczysz, że Luis nie można odróżnić tego wyrazu lub znaku, możesz dodać ten wyraz lub znak do [funkcji listy fraz](luis-how-to-add-features.md). Na przykład słowa poza kulturą aplikacji — czyli słowa obce — należy dodać do funkcji listy fraz.

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Języki hybrydowe
Języki hybrydowe łączą wyrazy z dwóch kultur, takich jak angielski i chiński. Te języki nie są obsługiwane w programie LUIS, ponieważ aplikacja jest oparta na pojedynczej kulturze.

## <a name="tokenization"></a>Tokenizacji
Aby wykonać Uczenie maszynowe, LUIS dzieli wypowiedź na [tokeny](luis-glossary.md#token) na podstawie kultury.

|Język|  wszystkie spacje lub znaki specjalne | poziom znaku|wyrazy złożone
|--|:--:|:--:|:--:|
|Arabski|✔|||
|Chiński||✔||
|Niderlandzki|✔||✔|
|Angielski (en-us)|✔ |||
|Francuski (fr-FR)|✔|||
|Francuski (fr-CA)|✔|||
|Niemiecki|✔||✔|
|Gudżarati|✔|||
|Hindi|✔|||
|Włoski|✔|||
|japoński|||✔
|Koreański||✔||
|Marathi|✔|||
|Portugalski (Brazylia)|✔|||
|Hiszpański (es-ES)|✔|||
|Hiszpański (es-MX)|✔|||
|Tamilski|✔|||
|Telugu|✔|||
|Turecki|✔|||


### <a name="custom-tokenizer-versions"></a>Niestandardowe wersje tokenizatora

Następujące kultury mają niestandardowe wersje tokenizatora:

|Kultura|Wersja|Przeznaczenie|
|--|--|--|
|Niemiecki<br>`de-de`|1.0.0|Tokenizes wyrazy, dzieląc je za pomocą tokenizatora opartych na uczeniu maszynowym, które próbują podzielić wyrazy złożone na ich pojedyncze składniki.<br>Jeśli użytkownik wprowadzi `Ich fahre einen krankenwagen` jako wypowiedź, jest on włączony `Ich fahre einen kranken wagen` . Zezwalanie na oznaczanie `kranken` i `wagen` niezależnie jako różne jednostki.|
|Niemiecki<br>`de-de`|1.0.2|Tokenizes wyrazy, dzieląc je na spacje.<br> Jeśli użytkownik wprowadzi `Ich fahre einen krankenwagen` jako wypowiedź, pozostaje pojedynczym tokenem. Oznacza `krankenwagen` to, że jest oznaczona jako pojedyncza jednostka. |
|Niderlandzki<br>`nl-nl`|1.0.0|Tokenizes wyrazy, dzieląc je za pomocą tokenizatora opartych na uczeniu maszynowym, które próbują podzielić wyrazy złożone na ich pojedyncze składniki.<br>Jeśli użytkownik wprowadzi `Ik ga naar de kleuterschool` jako wypowiedź, jest on włączony `Ik ga naar de kleuter school` . Zezwalanie na oznaczanie `kleuter` i `school` niezależnie jako różne jednostki.|
|Niderlandzki<br>`nl-nl`|1.0.1|Tokenizes wyrazy, dzieląc je na spacje.<br> Jeśli użytkownik wprowadzi `Ik ga naar de kleuterschool` jako wypowiedź, pozostaje pojedynczym tokenem. Oznacza `kleuterschool` to, że jest oznaczona jako pojedyncza jednostka. |


### <a name="migrating-between-tokenizer-versions"></a>Migrowanie między wersjami tokenizatora
<!--
Your first choice is to change the tokenizer version in the app file, then import the version. This action changes how the utterances are tokenized but allows you to keep the same app ID.

Tokenizer JSON for 1.0.0. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.0",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.0",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 23,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

Tokenizer JSON for version 1.0.1. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.1",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.1",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 16,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```
-->

Tokenizacji odbywa się na poziomie aplikacji. Nie ma obsługi tokenizacji poziomu wersji.

[Zaimportuj plik jako nową aplikację](luis-how-to-start-new-app.md)zamiast wersji. Ta akcja oznacza, że nowa aplikacja ma inny identyfikator aplikacji, ale używa wersji tokenizatora określonej w pliku.