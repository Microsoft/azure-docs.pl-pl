---
title: Rozszerzona aplikacja w czasie wykonywania — LUIS
description: Dowiedz się, jak rozłożyć już opublikowany punkt końcowy przewidywania, aby przekazać nowe informacje.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 1d94e9f59062e4d730b8f3b71022442e81e6eeda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98953025"
---
# <a name="extend-app-at-prediction-runtime"></a>Rozszerzona aplikacja w środowisku uruchomieniowym przewidywania

Schemat aplikacji (modele i funkcje) jest szkolony i publikowany w punkcie końcowym przewidywania. Ten opublikowany model jest używany w środowisku uruchomieniowym przewidywania. Można przekazać nowe informacje wraz z wypowiedź użytkownika do środowiska uruchomieniowego przewidywania, aby rozszerzyć prognozę.

Dwie zmiany schematu przewidywania w środowisku uruchomieniowym obejmują:
* [Jednostki zewnętrzne](#external-entities)
* [Listy dynamiczne](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>Jednostki zewnętrzne

Jednostki zewnętrzne zapewniają aplikacji LUIS możliwość identyfikowania i etykietowania jednostek podczas środowiska uruchomieniowego, które mogą być używane jako funkcje istniejących jednostek. Dzięki temu można używać oddzielnych i niestandardowych wyodrębniania jednostek przed wysłaniem zapytań do punktu końcowego przewidywania. Ponieważ odbywa się to w punkcie końcowym przewidywania zapytań, nie trzeba ponownie przeszkolić ani opublikować modelu.

Klient-aplikacja dostarcza własne ekstraktory jednostek przez zarządzanie dopasowaniem jednostek i Określanie lokalizacji w wypowiedź tej dopasowanej jednostki, a następnie wysłanie tych informacji przy użyciu żądania.

Jednostki zewnętrzne są mechanizmem rozszerzania dowolnego typu jednostki, gdy nadal są używane jako sygnały do innych modeli.

Jest to przydatne w przypadku jednostki, która ma dane dostępne tylko w środowisku uruchomieniowym przewidywania zapytań. Przykładami danych tego typu są stale zmieniające się dane lub specyficzne dla użytkownika. Jednostkę Contact LUIS można rozciągnąć z informacjami zewnętrznymi z listy kontaktów użytkownika.

Jednostki zewnętrzne są częścią interfejsu API tworzenia v3. Dowiedz się więcej na temat [migracji](luis-migration-api-v3.md) do tej wersji.

### <a name="entity-already-exists-in-app"></a>Jednostka już istnieje w aplikacji

Wartość `entityName` dla jednostki zewnętrznej, która została przeniesiona do treści żądania punktu końcowego, musi już istnieć w nauczonej i opublikowanej aplikacji w momencie zgłoszenia żądania. Typ jednostki nie ma znaczenia, obsługiwane są wszystkie typy.

### <a name="first-turn-in-conversation"></a>Najpierw włącz konwersację

Weź pod uwagę pierwszy wypowiedź w rozmowie bot rozmowy, w której użytkownik wprowadza następujące niepełne informacje:

`Send Hazem a new message`

Żądanie od bot rozmowy do LUIS może przekazać informacje w treści wpisu, `Hazem` Aby było ono bezpośrednio dopasowane jako jeden z kontaktów użytkownika.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Odpowiedź przewidywania obejmuje tę jednostkę zewnętrzną, z innymi przewidzianymi jednostkami, ponieważ jest zdefiniowana w żądaniu.

### <a name="second-turn-in-conversation"></a>Druga włączona konwersacja

Następny użytkownik wypowiedź do rozmowy bot używa bardziej niejasnego terminu:

`Send him a calendar reminder for the party.`

W tej części konwersacji wypowiedź używa `him` jako odwołania do `Hazem` . Konwersacja rozmówcy bot w treści wpisu można mapować `him` na wartość jednostki wyodrębnioną z pierwszego wypowiedź, `Hazem` .

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Odpowiedź przewidywania obejmuje tę jednostkę zewnętrzną, z innymi przewidzianymi jednostkami, ponieważ jest zdefiniowana w żądaniu.

### <a name="override-existing-model-predictions"></a>Zastąp istniejące przewidywania modelu

`preferExternalEntities`Właściwość Options określa, że jeśli użytkownik wysyła jednostkę zewnętrzną, która pokrywa się z przewidywaną jednostką o tej samej nazwie, Luis wybiera jednostkę przekazaną lub jednostkę istniejącą w modelu.

Rozważmy na przykład zapytanie `today I'm free` . LUIS wykrywa `today` jako datetimeV2 z następującą odpowiedzią:

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Jeśli użytkownik wyśle jednostkę zewnętrzną:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

Jeśli `preferExternalEntities` jest ustawiona na `false` , Luis zwraca odpowiedź, tak jakby zewnętrzna jednostka nie została wysłana.

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Jeśli `preferExternalEntities` jest ustawiona na `true` , Luis zwraca odpowiedź, w tym:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Rozwiązanie

_Opcjonalna_ `resolution` Właściwość zwraca w odpowiedzi predykcyjnej, co pozwala na przekazywanie metadanych skojarzonych z jednostką zewnętrzną, a następnie otrzymywanie jej z powrotem w odpowiedzi.

Podstawowym celem jest rozszerzanie wstępnie utworzonych jednostek, ale nie jest to ograniczone do tego typu jednostki.

`resolution`Właściwość może być liczbą, ciągiem, obiektem lub tablicą:

* Dallas
* {"text": "value"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>Listy dynamiczne

Listy dynamiczne umożliwiają rozbudowa istniejącej, przeszkolonej i opublikowanej jednostki listy, już w aplikacji LUIS.

Użyj tej funkcji, gdy wartości jednostki listy muszą być okresowo zmieniane. Ta funkcja umożliwia przeszerzenie już przeszkolonej i opublikowanej jednostki listy:

* W czasie żądania punktu końcowego przewidywania zapytań.
* Dla pojedynczego żądania.

Jednostka listy może być pusta w aplikacji LUIS, ale musi istnieć. Jednostka listy w aplikacji LUIS nie została zmieniona, ale zdolność przewidywania w punkcie końcowym zostanie rozszerzona, aby dołączyć do 2 list z około 1 000 elementami.

### <a name="dynamic-list-json-request-body"></a>Treść żądania JSON listy dynamicznej

Wyślij w następującej treści JSON, aby dodać nową podlistę z synonimami do listy i przewidzieć jednostkę listy dla tekstu, `LUIS` przy użyciu `POST` żądania przewidywania zapytania:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

Odpowiedź przewidywania obejmuje tę jednostkę listy wraz ze wszystkimi innymi przewidywanymi jednostkami, ponieważ jest zdefiniowana w żądaniu.

## <a name="next-steps"></a>Następne kroki

* [Współczynnik przewidywania](luis-concept-prediction-score.md)
* [Tworzenie zmian interfejsu API v3](luis-migration-api-v3.md)
