---
title: Typ jednostki złożonej — LUIS
titleSuffix: Azure Cognitive Services
description: Jednostka złożona składa się z innych jednostek, takich jak wstępnie zbudowane jednostki, proste, wyrażenie regularne i jednostki listy. Oddzielne jednostki tworzą całość jednostki.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 3d936555a4d9a9c80718a24ba892c762f5a8354a
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608396"
---
# <a name="composite-entity"></a>Jednostka złożona

Jednostka złożona składa się z innych jednostek, takich jak wstępnie zbudowane jednostki, proste, wyrażenie regularne i jednostki listy. Oddzielne jednostki tworzą całość jednostki.

> [!CAUTION]
> Ta jednostka jest **przestarzała**. Przeprowadź migrację do [jednostki uczenie maszynowego](reference-entity-machine-learned-entity.md).

**Ta jednostka jest przydatna, gdy dane:**

* Są ze sobą powiązane.
* Są ze sobą powiązane w kontekście wypowiedzi.
* Używaj różnych typów jednostek.
* Należy grupować i przetwarzać przez aplikację kliencką jako jednostkę informacji.
* Posiadanie różnych wyrażenia długości użytkowników, które wymagają uczenia maszynowego.

![Jednostka złożona](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Przykładowy kod JSON

Weź pod uwagę złożone jednostki prekompilowane `number` i `Location::ToLocation` z następującymi wypowiedź:

`book 2 tickets to cairo`

Zwróć uwagę, że `2` Liczba i `cairo` ToLocation zawierają słowa między nimi, które nie są częścią żadnej z tych jednostek. Zielone podkreślenie używane w wypowiedź z etykietą w witrynie sieci Web [Luis](luis-reference-regions.md) wskazuje jednostkę złożoną.

![Jednostka złożona](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

Jednostki złożone są zwracane w `compositeEntities` tablicy, a wszystkie jednostki w ramach złożone są również zwracane w `entities` tablicy:

```JSON
  "entities": [
    {
      "entity": "2 tickets to cairo",
      "type": "ticketinfo",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.9214487
    },
    {
      "entity": "cairo",
      "type": "builtin.geographyV2.city",
      "startIndex": 18,
      "endIndex": 22
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "subtype": "integer",
        "value": "2"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "ticketinfo",
      "value": "2 tickets to cairo",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "builtin.geographyV2.city",
          "value": "cairo"
        }
      ]
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

Jest to kod JSON, jeśli `verbose=false` jest ustawiony w ciągu zapytania:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ]
        }
    ]
}
```

Jest to kod JSON, jeśli `verbose=true` jest ustawiony w ciągu zapytania:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 5,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2.city",
                        "text": "cairo",
                        "startIndex": 18,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "$instance": {
        "ticketinfo": [
            {
                "type": "ticketinfo",
                "text": "2 tickets to cairo",
                "startIndex": 5,
                "length": 18,
                "score": 0.9214487,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

|Obiekt danych|Nazwa jednostki|Wartość|
|--|--|--|
|Wstępnie zbudowany numer jednostki|"wbudowane. Number"|dwóch|
|Wstępnie skonstruowany obiekt Entity-GeographyV2|"Lokalizacja:: ToLocation"|Kair|

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o jednostkach:

* [Pojęcia](luis-concept-entity-types.md)
* [Jak utworzyć](luis-how-to-add-entities.md)
