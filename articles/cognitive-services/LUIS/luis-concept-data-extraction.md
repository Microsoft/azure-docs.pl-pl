---
title: Wyodrębnianie danych — LUIS
description: Wyodrębnij dane z wypowiedź tekstu z intencjami i jednostkami. Dowiedz się, jakiego rodzaju dane mogą zostać wyodrębnione z Language Understanding (LUIS).
author: diberry
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 3cea4a46564210ad8c37fdeda68e24337091d0bb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82100298"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Wyodrębnij dane z wypowiedź tekstu z intencjami i jednostkami
LUIS daje możliwość uzyskiwania informacji z wyrażenia długości języka naturalnego użytkownika. Informacje są wyodrębniane w taki sposób, aby mogły być używane przez program, aplikację lub czat bot. W poniższych sekcjach dowiesz się, jakie dane są zwracane z intencji i jednostek z przykładami JSON.

Najtrudniejsze dane do wyodrębnienia to dane zdobyte na maszynie, ponieważ nie jest to dokładne dopasowanie tekstu. Wyodrębnianie danych [jednostek](luis-concept-entity-types.md) , które są poznanie maszynowe, musi być częścią [cyklu tworzenia](luis-concept-app-iteration.md) , dopóki nie uzyskasz pewności, że otrzymasz oczekiwane dane.

## <a name="data-location-and-key-usage"></a>Lokalizacja danych i użycie klucza
LUIS udostępnia dane z opublikowanego [punktu końcowego](luis-glossary.md#endpoint). **Żądanie https** (post lub Get) zawiera wypowiedź, a także niektóre opcjonalne konfiguracje, takie jak środowiska przejściowe lub produkcyjne.

#### <a name="v2-prediction-endpoint-request"></a>[Żądanie punktu końcowego przewidywania wersji 2](#tab/V2)

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

#### <a name="v3-prediction-endpoint-request"></a>[Żądanie punktu końcowego przewidywania v3](#tab/V3)

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

* * *

Jest ona dostępna na stronie **Ustawienia** aplikacji Luis, a także jako część adresu URL (po `/apps/`) podczas edytowania tej aplikacji Luis. `appID` `subscription-key` Jest to klucz punktu końcowego używany do wykonywania zapytań dotyczących aplikacji. Chociaż możesz użyć bezpłatnego klucza autorstwa/początkowego podczas uczenia się LUIS, ważne jest, aby zmienić klucz punktu końcowego na klucz, który obsługuje [oczekiwane użycie Luis](luis-limits.md#key-limits). `timezoneOffset` Jednostka jest minut.

**Odpowiedź https** zawiera wszystkie zamierzenia i informacje o jednostce, które Luis mogą ustalić w oparciu o aktualnie opublikowany model punktu końcowego lub produkcyjnego. Adres URL punktu końcowego znajduje się w witrynie sieci Web [Luis](luis-reference-regions.md) w sekcji **Zarządzanie** na stronie **klucze i punkty końcowe** .

## <a name="data-from-intents"></a>Dane z intencji
Dane podstawowe są największą **nazwą przeznaczenie**oceniania. Odpowiedź punktu końcowego to:

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

* * *

|Obiekt danych|Typ danych|Lokalizacja danych|Wartość|
|--|--|--|--|
|Intencja|String|topScoringIntent. intencja|"GetStoreInfo"|

Jeśli aplikacja chatbot lub LUIS-wywołująca podejmuje decyzję na podstawie więcej niż jednego wyniku zamiaru, zwracają wszystkie wyniki założeń.


#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

Ustaw parametr QueryString, `verbose=true`. Odpowiedź punktu końcowego to:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

Ustaw parametr QueryString, `show-all-intents=true`. Odpowiedź punktu końcowego to:

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

* * *

Intencje są uporządkowane od najwyższego do najniższego wyniku.

|Obiekt danych|Typ danych|Lokalizacja danych|Wartość|Wynik|
|--|--|--|--|:--|
|Intencja|String|intencje [0]. cel|"GetStoreInfo"|0,984749258|
|Intencja|String|intencje [1]. cel|Dawaj|0,0168218873|

W przypadku dodania wstępnie skompilowanych domen nazwa zamierzenia wskazuje domenę, taką jak `Utilties` lub `Communication` , a także zamiar:

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

* * *

|Domain|Obiekt danych|Typ danych|Lokalizacja danych|Wartość|
|--|--|--|--|--|
|Narzędzia|Intencja|String|intencje [0]. cel|"<b>Narzędzia</b>. ShowNext"|
|Komunikacja|Intencja|String|intencje [1]. cel|<b>Komunikacja</b>. StartOver"|
||Intencja|String|intencje [2]. cel|Dawaj|


## <a name="data-from-entities"></a>Dane z jednostek
Większość rozszerzenie czatbotów i aplikacji potrzebują więcej niż nazwy zamierzeń. Te dodatkowe dane opcjonalne pochodzą z jednostek odnalezionych w wypowiedź. Każdy typ jednostki zwraca różne informacje o dopasowaniu.

Pojedyncze słowo lub fraza w wypowiedź może być zgodna z więcej niż jedną jednostką. W takim przypadku każda pasująca jednostka jest zwracana z wynikiem.

Wszystkie jednostki są zwracane w tablicy **jednostek** odpowiedzi z punktu końcowego:

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

```JSON
"entities": {
    "name":["bob jones"],
    "number": [3]
}
```
Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

* * *

## <a name="tokenized-entity-returned"></a>Zwrócono token jednostki

Zapoznaj się z [pomocą techniczną tokenu](luis-language-support.md#tokenization) w Luis.

## <a name="simple-entity-data"></a>Proste dane jednostki

[Prosta jednostka](reference-entity-simple.md) to wartość nadana przez maszynę. Może to być słowo lub fraza.

## <a name="composite-entity-data"></a>Dane jednostki złożonej

[Jednostka złożona](reference-entity-composite.md) składa się z innych jednostek, takich jak wstępnie zbudowane jednostki, proste, wyrażenie regularne i jednostki listy. Oddzielne jednostki tworzą całość jednostki.

## <a name="list-entity-data"></a>Wyświetl dane jednostki

[Jednostki listy](reference-entity-list.md) reprezentują stały, zamknięty zestaw powiązanych słów wraz z ich synonimami. LUIS nie odnajduje dodatkowych wartości dla jednostek listy. Użyj opcji **zalecamy** , aby zobaczyć sugestie dotyczące nowych słów na podstawie bieżącej listy. Jeśli istnieje więcej niż jedna jednostka listy o tej samej wartości, każda jednostka zostanie zwrócona w zapytaniu punktu końcowego.

## <a name="prebuilt-entity-data"></a>Wstępnie utworzone dane jednostki
Wstępnie [skompilowane](luis-concept-entity-types.md) jednostki są odnajdywane na podstawie wyrażenia regularnego zgodnego z rozpoznawaniem typu "Open Source [" — projektem tekstu](https://github.com/Microsoft/Recognizers-Text) . Wstępnie skompilowane jednostki są zwracane w tablicy jednostek i używają nazwy typu poprzedzonej prefiksem `builtin::`. Następujący tekst to przykład wypowiedź z zwróconymi wstępnie skompilowanymi jednostkami:

`Dec 5th send to +1 360-555-1212`

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

Bez parametru QueryString `verbose=true`:

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ]
}
```

Za pomocą QueryString parametru `verbose=true`:

```json

"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "Dec 5th",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "number": [
            {
                "type": "builtin.number",
                "text": "1 360",
                "startIndex": 17,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "555",
                "startIndex": 23,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "1212",
                "startIndex": 27,
                "length": 4,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 360-555-1212",
                "startIndex": 17,
                "length": 14,
                "score": 1.0,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

* * *
## <a name="regular-expression-entity-data"></a>Dane jednostki wyrażenia regularnego

[Jednostka wyrażenia regularnego](reference-entity-regular-expression.md) wyodrębnia jednostkę na podstawie podania wzorca wyrażenia regularnego.

## <a name="extracting-names"></a>Wyodrębnianie nazw
Pobieranie nazw z wypowiedź jest trudne, ponieważ nazwa może być niemal dowolną kombinacją liter i wyrazów. W zależności od typu wyodrębnianych nazw można korzystać z kilku opcji. Poniższe sugestie nie są regułami, ale są bardziej szczegółowe.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Dodawanie wstępnie utworzonych jednostek PersonName i GeographyV2

Jednostki [PersonName](luis-reference-prebuilt-person.md) i [GeographyV2](luis-reference-prebuilt-geographyV2.md) są dostępne w niektórych [kulturach językowych](luis-reference-prebuilt-entities.md).

### <a name="names-of-people"></a>Nazwy osób

Nazwa osoby może mieć nieco niewielki format w zależności od języka i kultury. Użyj wstępnie utworzonej jednostki **[PersonName](luis-reference-prebuilt-person.md)** lub **[prostej jednostki](luis-concept-entity-types.md#simple-entity)** z [rolami](luis-concept-roles.md) imię i nazwisko.

Jeśli używasz prostej jednostki, upewnij się, że są podane przykłady, które używają pierwszej i ostatniej nazwy w różnych częściach wypowiedź, w wyrażenia długości o różnej długości i wyrażenia długości we wszystkich intencjach, w tym dla opcji Brak. Regularnie [Przeglądaj](luis-how-to-review-endoint-utt.md) wyrażenia długości punktu końcowego, aby oznaczyć wszystkie nazwy, które nie zostały prawidłowo przewidywalne.

### <a name="names-of-places"></a>Nazwy miejsc

Nazwy lokalizacji są ustawiane i znane, takie jak miasta, powiaty, Stany, prowincje i kraje/regiony. Aby wyodrębnić informacje o lokalizacji, Użyj wstępnie skompilowanej jednostki **[geographyV2](luis-reference-prebuilt-geographyv2.md)** .

### <a name="new-and-emerging-names"></a>Nowe i pojawiające się nazwy

Niektóre aplikacje muszą być w stanie znaleźć nowe i pojawiające się nazwy, takie jak produkty lub firmy. Te typy nazw są najbardziej trudnym typem wyodrębniania danych. Zacznij od **[prostej jednostki](luis-concept-entity-types.md#simple-entity)** i Dodaj [listę fraz](luis-concept-feature.md). Regularnie [Przeglądaj](luis-how-to-review-endoint-utt.md) wyrażenia długości punktu końcowego, aby oznaczyć wszystkie nazwy, które nie zostały prawidłowo przewidywalne.

## <a name="pattern-roles-data"></a>Dane ról wzorca
Role są kontekstowymi różnicami jednostek.


#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

Nazwa jednostki to `Location`, z dwiema rolami `Origin` i. `Destination`

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Employee",
    "startIndex": 5,
    "endIndex": 13,
    "score": 0.922820568,
    "role": ""
  },
  {
    "entity": "seattle",
    "type": "Location",
    "startIndex": 20,
    "endIndex": 26,
    "score": 0.948008537,
    "role": "Origin"
  },
  {
    "entity": "redmond",
    "type": "Location",
    "startIndex": 31,
    "endIndex": 37,
    "score": 0.7047979,
    "role": "Destination"
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

W wersji 3 **Nazwa roli** jest nazwą podstawową obiektu.

Nazwa jednostki to `Location`, z dwiema rolami `Origin` i. `Destination`

Bez parametru QueryString `verbose=true`:

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "Origin": [
        "seattle"
    ],
    "Destination": [
        "redmond"
    ]
}
```

Za pomocą QueryString parametru `verbose=true`:

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "LocationOrigin": [
        "seattle"
    ],
    "LocationDestination": [
        "redmond"
    ],
    "$instance": {
        "Employee": [
            {
                "type": "Employee",
                "text": "bob jones",
                "startIndex": 5,
                "length": 9,
                "score": 0.982873261,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Origin": [
            {
                "role": "Origin",
                "type": "Location",
                "text": "seattle",
                "startIndex": 20,
                "length": 7,
                "score": 0.9913306,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "redmond",
                "startIndex": 31,
                "length": 7,
                "score": 0.898179531,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]

}
```

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

* * *

## <a name="patternany-entity-data"></a>Wzorzec. wszystkie dane jednostki

[Wzorzec. any](reference-entity-pattern-any.md) jest symbolem zastępczym o zmiennej długości używanym tylko w szablonie wzorca wypowiedź, aby oznaczyć, gdzie rozpoczyna się i kończą.

## <a name="sentiment-analysis"></a>Analiza tonacji
W przypadku skonfigurowania analizy tonacji odpowiedź JSON LUIS obejmuje analizę tonacji. Dowiedz się więcej o analizie tonacji w dokumentacji [Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) .

### <a name="sentiment-data"></a>Tonacji dane
Dane tonacji to wynik z zakresu od 1 do 0 wskazujący dodatnie (bliżej 1) lub ujemne (bliżej 0) tonacji danych.

Gdy kultura jest `en-us`, odpowiedź jest:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

W przypadku wszystkich innych kultur odpowiedź jest:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Dane jednostki wyodrębniania klucza
Jednostka wyodrębniania klucza zwraca kluczowe frazy w wypowiedź, dostarczone przez [Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).


#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

```JSON
{
  "query": "Is there a map of places with beautiful views on a favorite trail?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.764368951
  },
  "intents": [
    ...
  ],
  "entities": [
    {
      "entity": "beautiful views",
      "type": "builtin.keyPhrase",
      "startIndex": 30,
      "endIndex": 44
    },
    {
      "entity": "map of places",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 23
    },
    {
      "entity": "favorite trail",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 64
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Bez parametru QueryString `verbose=true`:

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ]
}
```

Za pomocą QueryString parametru `verbose=true`:

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "map of places",
                "startIndex": 11,
                "length": 13,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "beautiful views",
                "startIndex": 30,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "favorite trail",
                "startIndex": 51,
                "length": 14,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

* * *


## <a name="data-matching-multiple-entities"></a>Dane pasujące do wielu jednostek

LUIS zwraca wszystkie jednostki odnalezione w wypowiedź. W związku z tym chatbot może wymagać podjęcia decyzji na podstawie wyników. Element wypowiedź może mieć wiele jednostek w wypowiedź:

`book me 2 adult business tickets to paris tomorrow on air france`

Punkt końcowy LUIS może odnaleźć te same dane w różnych jednostkach.

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

Bez `verbose=true` jako parametr QueryString.

```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ]
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ]
}
```

Za `verbose=true` pomocą jako parametru QueryString.


```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 8,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "PassengerCategory": [
                    {
                        "type": "PassengerCategory",
                        "text": "adult",
                        "startIndex": 10,
                        "length": 5,
                        "score": 0.9503733,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "TravelClass": [
                    {
                        "type": "TravelClass",
                        "text": "business",
                        "startIndex": 16,
                        "length": 8,
                        "score": 0.950095,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ],
    "$instance": {
        "TicketsOrder": [
            {
                "type": "TicketsOrder",
                "text": "2 adult business",
                "startIndex": 8,
                "length": 16,
                "score": 0.942183256,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Location::LocationTo": [
            {
                "type": "Location::LocationTo",
                "text": "paris",
                "startIndex": 36,
                "length": 5,
                "score": 0.9905354,
                "modelTypeId": 3,
                "modelType": "Hierarchical Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "tomorrow",
                "startIndex": 42,
                "length": 8,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Airline": [
            {
                "type": "Airline",
                "text": "air france",
                "startIndex": 54,
                "length": 10,
                "score": 0.9455415,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

* * *

## <a name="data-matching-multiple-list-entities"></a>Dane zgodne z wieloma jednostkami listy

Jeśli słowo lub fraza pasuje do więcej niż jednej jednostki listy, zapytanie punktu końcowego zwróci każdą jednostkę listy.

Dla zapytania `when is the best time to go to red rock?`, a aplikacja zawiera słowo `red` w więcej niż jednej liście, Luis rozpoznaje wszystkie jednostki i zwraca tablicę jednostek jako część odpowiedzi punktu końcowego JSON:

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```



#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

Bez `verbose=true` ciągu zapytania:

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ]
        }
    }
}
```


Za `verbose=true` pomocą ciągu zapytania:

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ],
            "$instance": {
                "Colors": [
                    {
                        "type": "Colors",
                        "text": "red",
                        "startIndex": 31,
                        "length": 3,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "Cities": [
                    {
                        "type": "Cities",
                        "text": "red rock",
                        "startIndex": 31,
                        "length": 8,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

* * *

## <a name="next-steps"></a>Następne kroki

Zobacz [Dodawanie jednostek](luis-how-to-add-entities.md) , aby dowiedzieć się więcej na temat dodawania jednostek do aplikacji Luis.
