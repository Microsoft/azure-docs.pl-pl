---
title: Wzorzec. dowolny typ jednostki — LUIS
titleSuffix: Azure Cognitive Services
description: Wzorzec. any jest symbolem zastępczym o zmiennej długości używanym tylko w szablonie wzorca wypowiedź, aby oznaczyć, gdzie rozpoczyna się i kończą.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.openlocfilehash: ec23be3709cebc534c059a21c52452abff683b18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542210"
---
# <a name="patternany-entity"></a>Jednostka Pattern.any

Wzorzec. any jest symbolem zastępczym o zmiennej długości używanym tylko w szablonie wzorca wypowiedź, aby oznaczyć, gdzie rozpoczyna się i kończą.  

Wzorzec. wszystkie jednostki muszą być oznaczone jako przykłady szablonu [wzorca](luis-how-to-model-intent-pattern.md) , a nie przykłady użytkownika.

**Jednostka jest dobrym dopasowaniem w przypadku:**

* Koniec jednostki można pomylić z pozostałym tekstem wypowiedź.

## <a name="usage"></a>Użycie

Dana aplikacja kliencka, która wyszukuje książki na podstawie tytułu, wzorca. wszystkie wyodrębnia pełny tytuł. Szablon wypowiedź przy użyciu wzorca. wszystkie te wyszukiwania są następujące `Was {BookTitle} written by an American this year[?]` .

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

Jest to kod JSON, jeśli `verbose=false` jest ustawiony w ciągu zapytania:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Jest to kod JSON, jeśli `verbose=true` jest ustawiony w ciągu zapytania:

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
