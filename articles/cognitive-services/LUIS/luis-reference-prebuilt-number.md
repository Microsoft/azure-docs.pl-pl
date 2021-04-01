---
title: Liczba wstępnie utworzonych jednostek — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera numery wstępnie skompilowanych informacji o jednostkach w Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 13594886b83d4474ee2531185db5868a5198ca64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91541966"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Liczba wstępnie utworzonych jednostek dla aplikacji LUIS
Istnieje wiele sposobów, w których wartości numeryczne są używane do określania ilości informacji. W tym artykule omówiono tylko niektóre z możliwych przykładów. LUIS interpretuje Wariacje w wyrażenia długości użytkownika i zwraca spójne wartości liczbowe. Ponieważ ta jednostka jest już przeszkolone, nie trzeba dodawać przykładowej wyrażenia długości zawierającej liczbę do intencji aplikacji.

## <a name="types-of-number"></a>Typy liczb
Numer jest zarządzany przez [Aparaty rozpoznawania tekstu —](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) repozytorium GitHub

## <a name="examples-of-number-resolution"></a>Przykłady rozpoznawania liczb

| Wypowiedź        | Jednostka   | Rozwiązanie |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      |
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


LUIS zawiera rozpoznaną wartość **`builtin.number`** jednostki w `resolution` polu odpowiedzi JSON, która zwraca.

## <a name="resolution-for-prebuilt-number"></a>Rozwiązanie dla wstępnie skompilowanego numeru

Następujące obiekty jednostki są zwracane dla zapytania:

`order two dozen eggs`

#### <a name="v3-response"></a>[Odpowiedź v3](#tab/V3)

Poniższy kod JSON jest z `verbose` parametrem ustawionym na `false` :

```json
"entities": {
    "number": [
        24
    ]
}
```
#### <a name="v3-verbose-response"></a>[Pełna odpowiedź w wersji 3](#tab/V3-verbose)

Poniższy kod JSON jest z `verbose` parametrem ustawionym na `true` :

```json
"entities": {
    "number": [
        24
    ],
    "$instance": {
        "number": [
            {
                "type": "builtin.number",
                "text": "two dozen",
                "startIndex": 6,
                "length": 9,
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

Poniższy przykład przedstawia odpowiedź JSON z LUIS, która zawiera rozdzielczość wartość 24 dla wypowiedź "dwa dziesiątki".

```json
"entities": [
  {
    "entity": "two dozen",
    "type": "builtin.number",
    "startIndex": 6,
    "endIndex": 14,
    "resolution": {
      "subtype": "integer",
      "value": "24"
    }
  }
]
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej o [walucie](luis-reference-prebuilt-currency.md), [liczbie porządkowej](luis-reference-prebuilt-ordinal.md)i [wartości procentowej](luis-reference-prebuilt-percentage.md).
