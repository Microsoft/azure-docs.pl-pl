---
title: Wstępnie utworzona jednostka porządkowa v2 — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie skompilowane informacje o jednostkach w wersji 2 w Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 3fd80389dfa54cb8386c13e0e05a1e71ac4bd956
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541938"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Wstępnie utworzona jednostka porządkowa v2 dla aplikacji LUIS
Numer porządkowy v2 rozszerza liczbę [porządkową](luis-reference-prebuilt-ordinal.md) , aby zapewnić odwołania względne, takie jak `next` , `last` , i `previous` . Nie są one wyodrębniane przy użyciu wstępnie skompilowanej jednostki.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Rozwiązanie dla wstępnie skompilowanej liczby porządkowej v2

Następujące obiekty jednostki są zwracane dla zapytania:

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[Odpowiedź v3](#tab/V3)

Poniższy kod JSON jest z `verbose` parametrem ustawionym na `false` :

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[Pełna odpowiedź w wersji 3](#tab/V3-verbose)

Poniższy kod JSON jest z `verbose` parametrem ustawionym na `true` :

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ],
    "$instance": {
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2.relative",
                "text": "the second to last",
                "startIndex": 8,
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
#### <a name="v2-response"></a>[Odpowiedź w wersji 2](#tab/V2)

Poniższy przykład pokazuje rozdzielczość jednostki **wbudowanej. ordinalV2** .

```json
"entities": [
    {
        "entity": "the second to last",
        "type": "builtin.ordinalV2.relative",
        "startIndex": 8,
        "endIndex": 25,
        "resolution": {
            "offset": "-1",
            "relativeTo": "end"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej na temat [wartości procentowej](luis-reference-prebuilt-percentage.md), [numeru telefonu](luis-reference-prebuilt-phonenumber.md)i jednostek [temperatury](luis-reference-prebuilt-temperature.md) .
