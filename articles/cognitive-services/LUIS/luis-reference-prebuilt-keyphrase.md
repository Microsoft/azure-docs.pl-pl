---
title: Keyphrase prekompilowana jednostka — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie skompilowane informacje o jednostkach keyphrase w Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 02a48c8d106795b970d573bb71f52ba92e0a90ac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91541989"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>keyPhrase prekompilowaną jednostkę dla aplikacji LUIS
Jednostka keyPhrase wyodrębnia różne kluczowe frazy z wypowiedź. Nie musisz dodawać przykładowej wyrażenia długości zawierającej keyPhrase do aplikacji. Jednostka keyPhrase jest obsługiwana w [wielu kulturach](luis-language-support.md#languages-supported) w ramach funkcji [analizy tekstu](../text-analytics/overview.md) .

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Rozwiązanie dla wstępnie skompilowanej jednostki keyPhrase

Następujące obiekty jednostki są zwracane dla zapytania:

`where is the educational requirements form for the development and engineering group`

#### <a name="v3-response"></a>[Odpowiedź v3](#tab/V3)

Poniższy kod JSON jest z `verbose` parametrem ustawionym na `false` :

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Pełna odpowiedź w wersji 3](#tab/V3-verbose)
Poniższy kod JSON jest z `verbose` parametrem ustawionym na `true` :

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "educational requirements",
                "startIndex": 13,
                "length": 24,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "development",
                "startIndex": 51,
                "length": 11,
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

Poniższy przykład pokazuje rozdzielczość jednostki **wbudowanej. keyPhrase** .

```json
"entities": [
    {
        "entity": "development",
        "type": "builtin.keyPhrase",
        "startIndex": 51,
        "endIndex": 61
    },
    {
        "entity": "educational requirements",
        "type": "builtin.keyPhrase",
        "startIndex": 13,
        "endIndex": 36
    }
]
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej o jednostkach [wartości procentowych](luis-reference-prebuilt-percentage.md), [liczbowych](luis-reference-prebuilt-number.md)i [wieku](luis-reference-prebuilt-age.md) .
