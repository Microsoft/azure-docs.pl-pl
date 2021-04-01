---
title: Wbudowane jednostki adresów URL — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje o wstępnie utworzonym obiekcie URL w Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.openlocfilehash: bc33fd1b90306a016c419f227fb9e73e83e8ea83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91535257"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>Obiekt prekompilowanego adresu URL dla aplikacji LUIS
Jednostka adresu URL wyodrębnia adresy URL z nazwami domen lub adresami IP. Ponieważ ta jednostka jest już przeszkolone, nie trzeba dodawać przykładowych wyrażenia długości zawierających adresy URL do aplikacji. Jednostka adresu URL jest obsługiwana `en-us` tylko w kulturze.

## <a name="types-of-urls"></a>Typy adresów URL
Adres URL jest zarządzany przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) repozytorium GitHub

## <a name="resolution-for-prebuilt-url-entity"></a>Rozwiązanie dla wstępnie skompilowanej jednostki adresu URL

Następujące obiekty jednostki są zwracane dla zapytania:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[Odpowiedź v3](#tab/V3)

Poniższy kod JSON jest z `verbose` parametrem ustawionym na `false` :

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Pełna odpowiedź w wersji 3](#tab/V3-verbose)

Poniższy kod JSON jest z `verbose` parametrem ustawionym na `true` :

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ],
    "$instance": {
        "url": [
            {
                "type": "builtin.url",
                "text": "https://www.luis.ai",
                "startIndex": 0,
                "length": 17,
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

W poniższym przykładzie pokazano, https://www.luis.ai jak to jest doskonałe rozwiązanie usługi poznawczej analizy

```json
"entities": [
    {
        "entity": "https://www.luis.ai",
        "type": "builtin.url",
        "startIndex": 0,
        "endIndex": 17
    }
]
```

* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej o liczbie [porządkowej](luis-reference-prebuilt-ordinal.md), [liczbie](luis-reference-prebuilt-number.md)i jednostkach [temperatury](luis-reference-prebuilt-temperature.md) .
