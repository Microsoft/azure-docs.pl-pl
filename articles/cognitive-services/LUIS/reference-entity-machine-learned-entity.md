---
title: Typ jednostki uczenia maszynowego — LUIS
titleSuffix: Azure Cognitive Services
description: Jednostka uczenia maszynowego jest preferowaną jednostką do tworzenia aplikacji usługi LUIS.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/13/2021
ms.openlocfilehash: 12724175ebb9e23efa431169c01a804cd193cdd5
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502593"
---
# <a name="machine-learning-entity"></a>Jednostka uczenia maszynowego

Jednostka uczenia maszynowego jest preferowaną jednostką do tworzenia aplikacji usługi LUIS.


## <a name="example-json"></a>Przykładowy JSON

Załóżmy, że aplikacja przyjmuje zamówienia na pizzę, na przykład samouczek dotyczący [dekompozyowalnej jednostki](tutorial-machine-learned-entity.md). Każde zamówienie może zawierać kilka różnych pizz, w tym różne rozmiary.

Przykładowe wypowiedzi mogą być następujące:

|Przykładowe wypowiedzi dla aplikacji do pizzy|
|--|
|`Can I get a pepperoni pizza and a can of coke please`|
|`can I get a small pizza with onions peppers and olives`|
|`pickup an extra large meat lovers pizza`|



#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania w wersji 3](#tab/V3)

Ponieważ jednostka uczenia maszynowego może mieć wiele podjęć z wymaganymi funkcjami, jest to tylko przykład. Powinien on być traktowany jako przewodnik po tym, co zostanie zwrócone przez jednostkę.

Rozważ zapytanie:

`deliver 1 large cheese pizza on thin crust and 2 medium pepperoni pizzas on deep dish crust`

Jest to JSON, `verbose=false` jeśli jest ustawiony w ciągu zapytania:

```json
"entities": {
    "Order": [
        {
            "FullPizzaWithModifiers": [
                {
                    "PizzaType": [
                        "cheese pizza"
                    ],
                    "Size": [
                        [
                            "Large"
                        ]
                    ],
                    "Quantity": [
                        1
                    ]
                },
                {
                    "PizzaType": [
                        "pepperoni pizzas"
                    ],
                    "Size": [
                        [
                            "Medium"
                        ]
                    ],
                    "Quantity": [
                        2
                    ],
                    "Crust": [
                        [
                            "Deep Dish"
                        ]
                    ]
                }
            ]
        }
    ],
    "ToppingList": [
        [
            "Cheese"
        ],
        [
            "Pepperoni"
        ]
    ],
    "CrustList": [
        [
            "Thin"
        ]
    ]
}

```

Jest to JSON, `verbose=true` jeśli jest ustawiony w ciągu zapytania:

```json
"entities": {
    "Order": [
        {
            "FullPizzaWithModifiers": [
                {
                    "PizzaType": [
                        "cheese pizza"
                    ],
                    "Size": [
                        [
                            "Large"
                        ]
                    ],
                    "Quantity": [
                        1
                    ],
                    "$instance": {
                        "PizzaType": [
                            {
                                "type": "PizzaType",
                                "text": "cheese pizza",
                                "startIndex": 16,
                                "length": 12,
                                "score": 0.999998868,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ],
                        "Size": [
                            {
                                "type": "SizeList",
                                "text": "large",
                                "startIndex": 10,
                                "length": 5,
                                "score": 0.998720646,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ],
                        "Quantity": [
                            {
                                "type": "builtin.number",
                                "text": "1",
                                "startIndex": 8,
                                "length": 1,
                                "score": 0.999878645,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ]
                    }
                },
                {
                    "PizzaType": [
                        "pepperoni pizzas"
                    ],
                    "Size": [
                        [
                            "Medium"
                        ]
                    ],
                    "Quantity": [
                        2
                    ],
                    "Crust": [
                        [
                            "Deep Dish"
                        ]
                    ],
                    "$instance": {
                        "PizzaType": [
                            {
                                "type": "PizzaType",
                                "text": "pepperoni pizzas",
                                "startIndex": 56,
                                "length": 16,
                                "score": 0.999987066,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ],
                        "Size": [
                            {
                                "type": "SizeList",
                                "text": "medium",
                                "startIndex": 49,
                                "length": 6,
                                "score": 0.999841452,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ],
                        "Quantity": [
                            {
                                "type": "builtin.number",
                                "text": "2",
                                "startIndex": 47,
                                "length": 1,
                                "score": 0.9996054,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ],
                        "Crust": [
                            {
                                "type": "CrustList",
                                "text": "deep dish crust",
                                "startIndex": 76,
                                "length": 15,
                                "score": 0.761551,
                                "modelTypeId": 1,
                                "modelType": "Entity Extractor",
                                "recognitionSources": [
                                    "model"
                                ]
                            }
                        ]
                    }
                }
            ],
            "$instance": {
                "FullPizzaWithModifiers": [
                    {
                        "type": "FullPizzaWithModifiers",
                        "text": "1 large cheese pizza on thin crust",
                        "startIndex": 8,
                        "length": 34,
                        "score": 0.616001546,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    },
                    {
                        "type": "FullPizzaWithModifiers",
                        "text": "2 medium pepperoni pizzas on deep dish crust",
                        "startIndex": 47,
                        "length": 44,
                        "score": 0.7395033,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "ToppingList": [
        [
            "Cheese"
        ],
        [
            "Pepperoni"
        ]
    ],
    "CrustList": [
        [
            "Thin"
        ]
    ],
    "$instance": {
        "Order": [
            {
                "type": "Order",
                "text": "1 large cheese pizza on thin crust and 2 medium pepperoni pizzas on deep dish crust",
                "startIndex": 8,
                "length": 83,
                "score": 0.6881274,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ToppingList": [
            {
                "type": "ToppingList",
                "text": "cheese",
                "startIndex": 16,
                "length": 6,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "ToppingList",
                "text": "pepperoni",
                "startIndex": 56,
                "length": 9,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "CrustList": [
            {
                "type": "CrustList",
                "text": "thin crust",
                "startIndex": 32,
                "length": 10,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania w wersji 2](#tab/V2)

Ta jednostka nie jest dostępna w środowisku uruchomieniowym przewidywania w wersji 2.
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o jednostce uczenia maszynowego, w tym [samouczku,](tutorial-machine-learned-entity.md) [pojęciach](luis-concept-entity-types.md#machine-learned-ml-entity)i [przewodniku.](luis-how-to-add-entities.md#create-a-machine-learned-entity)

Dowiedz się więcej o [jednostce listy](reference-entity-list.md) [i jednostce wyrażenia regularnego.](reference-entity-regular-expression.md)