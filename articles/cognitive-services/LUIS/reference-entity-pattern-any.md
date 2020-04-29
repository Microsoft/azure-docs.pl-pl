---
title: Wzorzec. dowolny typ jednostki — LUIS
titleSuffix: Azure Cognitive Services
description: Wzorzec. any jest symbolem zastępczym o zmiennej długości używanym tylko w szablonie wzorca wypowiedź, aby oznaczyć, gdzie rozpoczyna się i kończą.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 5164bf55ef8233cf34a470524da3bc852678d79a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75979176"
---
# <a name="patternany-entity"></a>Jednostka Pattern.any

Wzorzec. any jest symbolem zastępczym o zmiennej długości używanym tylko w szablonie wzorca wypowiedź, aby oznaczyć, gdzie rozpoczyna się i kończą.  

Wzorzec. wszystkie jednostki muszą być oznaczone jako przykłady szablonu [wzorca](luis-how-to-model-intent-pattern.md) , a nie przykłady użytkownika.

**Jednostka jest dobrym dopasowaniem w przypadku:**

* Koniec jednostki można pomylić z pozostałym tekstem wypowiedź.

## <a name="usage"></a>Sposób użycia

Dana aplikacja kliencka, która wyszukuje książki na podstawie tytułu, wzorca. wszystkie wyodrębnia pełny tytuł. Szablon wypowiedź przy użyciu wzorca. wszystkie te wyszukiwania są `Was {BookTitle} written by an American this year[?]`następujące.

W poniższej tabeli każdy wiersz ma dwie wersje wypowiedź. Górna wypowiedź to sposób, w jaki LUIS początkowo widzi wypowiedź. Nie jest jasne, gdzie rozpoczyna się i kończą tytuł książki. Dolna wypowiedź używa wzorca. Każda jednostka do oznaczenia początku i końca jednostki.

|Wypowiedź z jednostką pogrubioną|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>**Czy człowiek, który wykonał niezawodną swoją żona dla Hat i innych klinicznych wskaźnikami** , w tym roku?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Czy **w ramach żabichu Pajamas** wypisano połowę w tym roku?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Czy była to **szczególna smuteka z cytryn cytrynowy**|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>**Czy w moim urządzeniu Pocket został Wocket** Czy ten rok został zapisany przez amerykańskie?|
||



## <a name="example-json"></a>Przykładowy kod JSON

Rozpatrzmy następujące zapytanie:

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

Z osadzoną nazwą formularza do wyodrębnienia jako wzorzec. any:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "understand your responsibilities as a member of the community",
    "type": "FormName",
    "startIndex": 18,
    "endIndex": 78,
    "role": ""
  }
```


#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

Jest to kod JSON, `verbose=false` jeśli jest ustawiony w ciągu zapytania:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Jest to kod JSON, `verbose=true` jeśli jest ustawiony w ciągu zapytania:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ],
    "$instance": {
        "FormName": [
            {
                "type": "FormName",
                "text": "Understand your responsibilities as a member of the community",
                "startIndex": 18,
                "length": 61,
                "modelTypeId": 7,
                "modelType": "Pattern.Any Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Następne kroki

W tym [samouczku](luis-tutorial-pattern.md)Użyj **wzorca. Każda** jednostka do wyodrębnienia danych z wyrażenia długości, gdzie wyrażenia długości są dobrze sformatowane, a koniec danych można łatwo pomylić z innymi słowami wypowiedź.
