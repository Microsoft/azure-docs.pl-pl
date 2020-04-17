---
title: Rozszerzanie aplikacji w czasie wykonywania — usługa LUIS
description: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: c0f9d71f5d89d73d9cdce2a2f646859d8eba3adc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538579"
---
# <a name="extend-app-at-prediction-runtime"></a>Rozszerzanie aplikacji w czasie wykonywania prognozowania

Schemat aplikacji (modele i funkcje) jest przeszkolony i opublikowany w punkcie końcowym przewidywania. Ten opublikowany model jest używany w czasie wykonywania prognozowania. Można przekazać nowe informacje, wraz z wypowiedź użytkownika, do środowiska uruchomieniowego przewidywania, aby rozszerzyć przewidywanie.

Dwie zmiany schematu środowiska uruchomieniowego prognozowania obejmują:
* [Podmioty zewnętrzne](#external-entities)
* [Listy dynamiczne](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>Podmioty zewnętrzne

Jednostki zewnętrzne dają aplikacji usługi LUIS możliwość identyfikowania i etykietowania jednostek w czasie wykonywania, które mogą być używane jako funkcje do istniejących jednostek. Dzięki temu można użyć własnych wyodrębniaczy jednostek oddzielne i niestandardowe przed wysłaniem zapytań do punktu końcowego przewidywania. Ponieważ odbywa się to w punkcie końcowym przewidywania kwerend, nie trzeba ponownie trenować i publikować modelu.

Aplikacja klienta dostarcza własny ekstraktor jednostek, zarządzając dopasowywaniem jednostek i określaniem lokalizacji w ramach wypowiedź tej dopasowanej jednostki, a następnie wysyłając te informacje z żądaniem.

Jednostki zewnętrzne są mechanizmem rozszerzania dowolnego typu jednostki, podczas gdy nadal są używane jako sygnały do innych modeli.

Jest to przydatne dla jednostki, która ma dane dostępne tylko w czasie wykonywania przewidywanie kwerend. Przykładami tego typu danych są stale zmieniające się dane lub konkretne dane na użytkownika. Encję kontaktu usługi LUIS można rozszerzyć o informacje zewnętrzne z listy kontaktów użytkownika.

Jednostki zewnętrzne są częścią interfejsu API tworzenia wersji 3. Dowiedz się więcej o [migracji](luis-migration-api-v3.md) do tej wersji.

### <a name="entity-already-exists-in-app"></a>Jednostka już istnieje w aplikacji

Wartość dla `entityName` jednostki zewnętrznej, przekazywane w treści żądania punktu końcowego POST, musi już istnieć w aplikacji przeszkolonych i opublikowanych w momencie żądania. Typ jednostki nie ma znaczenia, wszystkie typy są obsługiwane.

### <a name="first-turn-in-conversation"></a>Pierwszy zwrot w rozmowie

Należy wziąć pod uwagę pierwszą wypowiedź w konwersacji bota czatu, gdzie użytkownik wprowadza następujące niekompletne informacje:

`Send Hazem a new message`

Żądanie z bota czatu do usługi LUIS może `Hazem` przekazać informacje w treści POST o więc jest bezpośrednio dopasowywana jako jeden z kontaktów użytkownika.

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

Odpowiedź przewidywania obejmuje tej jednostki zewnętrznej, ze wszystkimi innymi przewidywanych jednostek, ponieważ jest zdefiniowany w żądaniu.

### <a name="second-turn-in-conversation"></a>Druga tura w rozmowie

Następna wypowiedź użytkownika do czatu bot używa bardziej niejasne określenie:

`Send him a calendar reminder for the party.`

Na tej turze konwersacji `him` wypowiedź `Hazem`używa jako odwołania do . Konwersacyjny czat bot, w `him` treści POST, można mapować do `Hazem`wartości jednostki wyodrębnione z pierwszej wypowiedź, .

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

Odpowiedź przewidywania obejmuje tej jednostki zewnętrznej, ze wszystkimi innymi przewidywanych jednostek, ponieważ jest zdefiniowany w żądaniu.

### <a name="override-existing-model-predictions"></a>Zastępowanie istniejących prognoz modelu

Właściwość `preferExternalEntities` options określa, że jeśli użytkownik wysyła jednostkę zewnętrzną, która nakłada się na przewidywaną jednostkę o tej samej nazwie, usługa LUIS wybiera jednostkę przekazaną lub jednostkę istniejącą w modelu.

Rozważmy na przykład `today I'm free`kwerendę . Usługa LUIS `today` wykrywa jako datetimeV2 z następującą odpowiedzią:

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

Jeśli użytkownik wysyła jednostkę zewnętrzną:

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

Jeśli `preferExternalEntities` jest ustawiona na `false`, usługa LUIS zwraca odpowiedź tak, jakby jednostka zewnętrzna nie została wysłana.

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

Jeśli `preferExternalEntities` jest ustawiona na `true`, usługa LUIS zwraca odpowiedź, w tym:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Rozwiązanie

Właściwość _opcjonalna_ `resolution` zwraca w odpowiedzi przewidywania, co pozwala przekazać w metadanych skojarzonych z jednostką zewnętrzną, a następnie odebrać go z powrotem w odpowiedzi.

Głównym celem jest rozszerzenie wstępnie utworzonych jednostek, ale nie jest ograniczona do tego typu jednostki.

Właściwość `resolution` może być liczbą, ciągiem, obiektem lub tablicą:

* "Dallas"
* {"text": "value"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>Listy dynamiczne

Listy dynamiczne umożliwiają rozszerzenie istniejącej jednostki listy przeszkolonych i opublikowanych, już w aplikacji usługi LUIS.

Użyj tej funkcji, gdy wartości encji listy muszą być okresowo zmieniane. Ta funkcja umożliwia rozszerzenie już przeszkolonej i opublikowanej jednostki listy:

* W czasie żądania punktu końcowego przewidywania kwerendy.
* Dla pojedynczego żądania.

Jednostka listy może być pusta w aplikacji usługi LUIS, ale musi istnieć. Jednostka listy w aplikacji usługi LUIS nie zostanie zmieniona, ale możliwość przewidywania w punkcie końcowym jest rozszerzana o maksymalnie 2 listy z około 1000 elementów.

### <a name="dynamic-list-json-request-body"></a>Treść żądania JSON listy dynamicznej

Wyślij w następującej treści JSON, aby dodać nową podlistę z synonimami do `LUIS`listy i `POST` przewidzieć jednostkę listy dla tekstu, z żądaniem przewidywania kwerendy:

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

Odpowiedź przewidywania zawiera tej jednostki listy, ze wszystkimi innymi przewidywanych jednostek, ponieważ jest zdefiniowany w żądaniu.

## <a name="next-steps"></a>Następne kroki

* [Współczynnik przewidywania](luis-concept-prediction-score.md)
* [Tworzenie zmian interfejsu API w wersji 3](luis-migration-api-v3.md)
