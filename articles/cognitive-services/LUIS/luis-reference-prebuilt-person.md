---
title: Osoba — wstępnie utworzona jednostka — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie skompilowane informacje o jednostce PersonName w Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/07/2019
ms.openlocfilehash: 7b0153d79aaf7b88fea958ab36183e57b41af204
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91535427"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Osoba z wbudowaną jednostką dla aplikacji LUIS
Przedbudowana jednostka PersonName wykrywa nazwiska osób. Ponieważ ta jednostka jest już przeszkolony, nie trzeba dodawać przykładowej wyrażenia długości zawierającej nazwisko osoby do zamiaru aplikacji. Jednostka PersonName jest obsługiwana w [kulturze](luis-reference-prebuilt-entities.md)angielskiej i chińskiej.

## <a name="resolution-for-personname-entity"></a>Rozwiązanie dla jednostki PersonName

Następujące obiekty jednostki są zwracane dla zapytania:

`Is Jill Jones in Cairo?`


#### <a name="v3-response"></a>[Odpowiedź v3](#tab/V3)


Poniższy kod JSON jest z `verbose` parametrem ustawionym na `false` :

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Pełna odpowiedź w wersji 3](#tab/V3-verbose)
Poniższy kod JSON jest z `verbose` parametrem ustawionym na `true` :

```json
"entities": {
    "personName": [
        "Jill Jones"
    ],
    "$instance": {
        "personName": [
            {
                "type": "builtin.personName",
                "text": "Jill Jones",
                "startIndex": 3,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-response"></a>[Odpowiedź w wersji 2](#tab/V2)

Poniższy przykład pokazuje rozdzielczość jednostki **wbudowanej. PersonName** .

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej o jednostkach [poczty e-mail](luis-reference-prebuilt-email.md), [liczb](luis-reference-prebuilt-number.md)i [porządkowej](luis-reference-prebuilt-ordinal.md) .
