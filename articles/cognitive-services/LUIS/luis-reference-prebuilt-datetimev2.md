---
title: Wstępnie skompilowane jednostki DatetimeV2 — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie skompilowane informacje o jednostkach datetimeV2 w Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/13/2020
ms.openlocfilehash: 83522de9c00056a3808b002b3103f45c72553399
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013075"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>DatetimeV2 prekompilowaną jednostkę dla aplikacji LUIS

Prebudowana jednostka **datetimeV2** wyodrębnia wartości daty i godziny. Te wartości są rozwiązywane w standardowym formacie dla programów klienckich do użycia. Gdy wypowiedź ma datę lub godzinę, która nie została ukończona, LUIS zawiera _wartości przeszłe i przyszłe_ w odpowiedzi punktu końcowego. Ponieważ ta jednostka jest już przeszkolone, nie trzeba dodawać przykładowej wyrażenia długości zawierającej datetimeV2 do intencji aplikacji.

## <a name="types-of-datetimev2"></a>Typy datetimeV2
DatetimeV2 jest zarządzany przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) repozytorium GitHub.

## <a name="example-json"></a>Przykładowy kod JSON

Poniżej przedstawiono wypowiedź i jego częściową odpowiedź JSON.

`8am on may 2nd 2019`

#### <a name="v3-response"></a>[Odpowiedź v3](#tab/1-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[Pełna odpowiedź w wersji 3](#tab/1-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.datetime",
                "text": "8am on may 2nd 2019",
                "startIndex": 0,
                "length": 19,
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

#### <a name="v2-response"></a>[Odpowiedź w wersji 2](#tab/1-3)

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
 ```

|Nazwa właściwości |Typ i opis właściwości|
|---|---|
|Jednostka|**ciąg** tekstowy wyodrębniony z wypowiedź z typem daty, godziny, zakresu dat lub zakresem czasu.|
|typ|**ciąg** — jeden z [podtypów datetimeV2](#subtypes-of-datetimev2)
|Indeks|**int** — indeks w wypowiedź, w którym rozpoczyna się jednostka.|
|endIndex|**int** — indeks w wypowiedź, w którym następuje zakończenie obiektu.|
|rozwiązanie|Ma `values` tablicę, która ma jedną, dwie lub cztery [wartości rozdzielczości](#values-of-resolution).|
|end|Wartość końcowa godziny lub zakresu dat w tym samym formacie co `value` . Używane tylko wtedy `type` , gdy jest `daterange` , `timerange` lub `datetimerange`|

* * *

## <a name="subtypes-of-datetimev2"></a>Podtypy datetimeV2

Wstępnie utworzona jednostka **datetimeV2** ma następujące podtypy, a przykłady z nich są podane w poniższej tabeli:
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>Wartości rozwiązania
* Tablica ma jeden element, jeśli data lub godzina w wypowiedź jest w pełni określona i niejednoznaczna.
* Tablica ma dwa elementy, jeśli wartość datetimeV2 jest niejednoznaczna. Niejednoznaczność obejmuje brak określonego roku, godziny lub zakresu czasu. Zobacz [niejednoznaczne daty](#ambiguous-dates) dla przykładów. Kiedy czas jest niejednoznaczny dla A.M. lub P.M. są uwzględniane obie wartości.
* Tablica ma cztery elementy, jeśli wypowiedź ma dwa elementy z niejednoznaczności. Ta niejednoznaczność zawiera elementy, które mają:
  * Data lub zakres dat, który jest niejednoznaczny dla roku.
  * Czas lub zakres czasu, który jest niejednoznaczny od do rano lub po południu. Na przykład 3:00 kwietnia.

Każdy element `values` tablicy może mieć następujące pola:

|Nazwa właściwości|Opis właściwości|
|--|--|
|Timex|godzina, Data lub zakres dat wyrażony w formacie TIMEX, który jest zgodny ze [standardem ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i atrybutami TIMEX3 dla adnotacji przy użyciu języka TimeML.|
|Funkcja|termin używany do opisywania sposobu używania wartości takich jak `before` , `after` .|
|typ|Podtyp, który może być jednym z następujących elementów:,,,,,, `datetime` `date` `time` `daterange` `timerange` `datetimerange` `duration` , `set` .|
|wartość|**Obowiązkowe.** Obiekt DateTime w formacie RRRR-MM-DD (Date), gg: mm: SS (Time) RRRR-MM-DD GG: mm: SS (DateTime). Jeśli `type` jest `duration` , wartość jest liczbą sekund (czas trwania) <br/> Używane tylko wtedy `type` , gdy jest `datetime` lub `date` , `time` lub "Duration".|

## <a name="valid-date-values"></a>Prawidłowe wartości daty

**DatetimeV2** obsługuje daty między następującymi zakresami:

| Min. | Maks. |
|----------|-------------|
| 1 stycznia 1900   | 31 grudnia 2099 |

## <a name="ambiguous-dates"></a>Niejednoznaczne daty

Jeśli data może przypadać w przeszłości lub w przyszłości, LUIS zawiera obie wartości. Przykładem jest wypowiedź, który obejmuje miesiąc i datę bez roku.

Na przykład, uwzględniając następujące wypowiedź:

`May 2nd`

* Jeśli dzisiejszą datą jest trzecia 2017, LUIS ma zarówno "2017-05-02", jak i "2018-05-02" jako wartości.
* Gdy dzisiejsza data wynosi 1 maja 2017, LUIS określa zarówno "2016-05-02", jak i "2017-05-02" jako wartości.

W poniższym przykładzie pokazano rozdzielczość jednostki "może być druga". W tym rozwiązaniu przyjęto założenie, że dzisiejsza data jest datą z przedziału od 2 maja 2017 do 1 maja 2018.
Pola z `X` w `timex` polu są częściami daty, które nie są jawnie określone w wypowiedź.

## <a name="date-resolution-example"></a>Przykład rozpoznawania dat


Poniżej przedstawiono wypowiedź i jego częściową odpowiedź JSON.

`May 2nd`

#### <a name="v3-response"></a>[Odpowiedź v3](#tab/2-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[Pełna odpowiedź w wersji 3](#tab/2-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "May 2nd",
                "startIndex": 0,
                "length": 7,
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

#### <a name="v2-response"></a>[Odpowiedź w wersji 2](#tab/2-3)

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-numeric-date"></a>Przykłady rozpoznawania zakresu dat dla daty numerycznej

`datetimeV2`Jednostka wyodrębnia zakresy dat i godzin. `start`Pola i `end` określają początek i koniec zakresu. W przypadku wypowiedź `May 2nd to May 5th` Luis zawiera wartości **DateRange** dla bieżącego roku i następnego roku. W `timex` polu `XXXX` wartości wskazują niejednoznaczność roku. `P3D` wskazuje, że okres jest dłuższy niż trzy dni.

Poniżej przedstawiono wypowiedź i jego częściową odpowiedź JSON.

`May 2nd to May 5th`

#### <a name="v3-response"></a>[Odpowiedź v3](#tab/3-1)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ]
}
```


#### <a name="v3-verbose-response"></a>[Pełna odpowiedź w wersji 3](#tab/3-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "May 2nd to May 5th",
                "startIndex": 0,
                "length": 18,
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

#### <a name="v2-response"></a>[Odpowiedź w wersji 2](#tab/3-3)

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-day-of-week"></a>Przykłady rozpoznawania zakresu dat dla dnia tygodnia

Poniższy przykład pokazuje, jak LUIS używa **datetimeV2** do rozwiązywania wypowiedź `Tuesday to Thursday` . W tym przykładzie bieżąca data to 19 czerwca. LUIS zawiera wartości **DateRange** dla obu zakresów dat, które poprzedzają bieżącą datę i obserwują ją.

Poniżej przedstawiono wypowiedź i jego częściową odpowiedź JSON.

`Tuesday to Thursday`

#### <a name="v3-response"></a>[Odpowiedź v3](#tab/4-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[Pełna odpowiedź w wersji 3](#tab/4-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "Tuesday to Thursday",
                "startIndex": 0,
                "length": 19,
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

#### <a name="v2-response"></a>[Odpowiedź w wersji 2](#tab/4-3)

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="ambiguous-time"></a>Niejednoznaczny czas
Tablica wartości ma dwa elementy czasu, jeśli czas lub zakres czasu jest niejednoznaczny. Gdy istnieje niejednoznaczny czas, wartości mają zarówno wartość A.M. i P.M. trzykrotn.

## <a name="time-range-resolution-example"></a>Przykład rozdzielczości zakresu czasu

Odpowiedź JSON DatetimeV2 została zmieniona w interfejsie API v3. Poniższy przykład pokazuje, jak LUIS używa **datetimeV2** do rozwiązywania wypowiedź, który ma zakres czasu.

Zmiany z interfejsu API v2:
* `datetimeV2.timex.type` Właściwość nie jest już zwracana, ponieważ jest zwracana na poziomie nadrzędnym `datetimev2.type` .
* `datetimeV2.value`Nazwa właściwości została zmieniona na `datetimeV2.timex` .

Poniżej przedstawiono wypowiedź i jego częściową odpowiedź JSON.

`from 6pm to 7pm`

#### <a name="v3-response"></a>[Odpowiedź v3](#tab/5-1)

Poniższy kod JSON jest z `verbose` parametrem ustawionym na `false` :

```JSON

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Pełna odpowiedź w wersji 3](#tab/5-2)

Poniższy kod JSON jest z `verbose` parametrem ustawionym na `true` :

```json

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.timerange",
                "text": "from 6pm to 7pm",
                "startIndex": 0,
                "length": 15,
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
#### <a name="v2-response"></a>[Odpowiedź w wersji 2](#tab/5-3)

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

* * *

## <a name="time-resolution-example"></a>Przykład czasu rozwiązania

Poniżej przedstawiono wypowiedź i jego częściową odpowiedź JSON.

`8am`

#### <a name="v3-response"></a>[Odpowiedź v3](#tab/6-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[Pełna odpowiedź w wersji 3](#tab/6-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.time",
                "text": "8am",
                "startIndex": 0,
                "length": 3,
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
#### <a name="v2-response"></a>[Odpowiedź w wersji 2](#tab/6-3)

```json
"entities": [
  {
    "entity": "8am",
    "type": "builtin.datetimeV2.time",
    "startIndex": 0,
    "endIndex": 2,
    "resolution": {
      "values": [
        {
          "timex": "T08",
          "type": "time",
          "value": "08:00:00"
        }
      ]
    }
  }
]
```

* * *

## <a name="deprecated-prebuilt-datetime"></a>Przestarzała prebudowana Data i godzina

`datetime`Wstępnie utworzona jednostka jest przestarzała i zastąpiona przez **datetimeV2**.

Aby zastąpić `datetime` `datetimeV2` w aplikacji Luis, wykonaj następujące czynności:

1. Otwórz okienko **jednostki** w interfejsie sieci Web Luis.
2. Usuń wstępnie utworzoną jednostkę **DateTime** .
3. Kliknij pozycję **Dodaj prekompilowaną jednostkę**
4. Wybierz pozycję **datetimeV2** , a następnie kliknij pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej na temat [wymiaru](luis-reference-prebuilt-dimension.md), jednostek [poczty e-mail](luis-reference-prebuilt-email.md) i [liczby](luis-reference-prebuilt-number.md).

