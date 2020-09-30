---
title: Wstępnie skompilowany obiekt Entity-LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera porządkowe wstępnie skompilowane informacje o jednostkach w Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 1fa86c8960ea9f32163ebd7991260a19ef7a5d79
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535461"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Wstępnie utworzona jednostka dla aplikacji LUIS
Numer porządkowy jest reprezentacją liczbową obiektu wewnątrz zestawu: `first` , `second` , `third` . Ponieważ ta jednostka jest już przeszkolone, nie trzeba dodawać przykładowej wyrażenia długości zawierającej numer porządkowy do intencji aplikacji. Jednostka porządkowa jest obsługiwana w [wielu kulturach](luis-reference-prebuilt-entities.md).

## <a name="types-of-ordinal"></a>Typy porządkowe
Numer porządkowy jest zarządzany przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) repozytorium GitHub

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Rozwiązanie dla wstępnie skompilowanej jednostki porządkowej

Następujące obiekty jednostki są zwracane dla zapytania:

`Order the second option`

#### <a name="v3-response"></a>[Odpowiedź v3](#tab/V3)

Poniższy kod JSON jest z `verbose` parametrem ustawionym na `false` :

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[Pełna odpowiedź w wersji 3](#tab/V3-verbose)
Poniższy kod JSON jest z `verbose` parametrem ustawionym na `true` :

```json
"entities": {
    "ordinal": [
        2
    ],
    "$instance": {
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "second",
                "startIndex": 10,
                "length": 6,
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

Poniższy przykład pokazuje rozdzielczość jednostki **wbudowanej. porządkowej** .

```json
"entities": [
  {
    "entity": "second",
    "type": "builtin.ordinal",
    "startIndex": 10,
    "endIndex": 15,
    "resolution": {
      "value": "2"
    }
  }
]
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej na temat [OrdinalV2](luis-reference-prebuilt-ordinal-v2.md), [numeru telefonu](luis-reference-prebuilt-phonenumber.md)i jednostek [temperatury](luis-reference-prebuilt-temperature.md) .
