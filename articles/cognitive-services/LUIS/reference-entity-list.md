---
title: List — typ jednostki — LUIS
description: Jednostki listy reprezentują stały, zamknięty zestaw powiązanych słów wraz z ich synonimami. LUIS nie odnajduje dodatkowych wartości dla jednostek listy. Użyj opcji zalecamy, aby zobaczyć sugestie dotyczące nowych słów na podstawie bieżącej listy.
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 273fabae38f6682cfaaffcdcc19e62adc41b7a47
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097578"
---
# <a name="list-entity"></a>Jednostka listy

Jednostki listy reprezentują stały, zamknięty zestaw powiązanych słów wraz z ich synonimami. LUIS nie odnajduje dodatkowych wartości dla jednostek listy. Użyj opcji **zalecamy** , aby zobaczyć sugestie dotyczące nowych słów na podstawie bieżącej listy. Jeśli istnieje więcej niż jedna jednostka listy o tej samej wartości, każda jednostka zostanie zwrócona w zapytaniu punktu końcowego.

Jednostka listy nie jest zauczenia maszynowego. Jest to dokładne dopasowanie tekstu. LUIS oznacza dowolne dopasowanie do elementu na dowolnej liście jako jednostki w odpowiedzi.

**Jednostka jest dobrym dopasowaniem, gdy dane tekstowe:**

* Są znanym zestawem.
* Nie zmienia się często. Jeśli musisz zmienić listę często lub chcesz, aby lista została powiększana, to lepszy wybór jest prostą jednostką z listą fraz.
* Zestaw nie przekracza maksymalnych [granic](luis-limits.md) usługi LUIS dla tego typu jednostki.
* Tekst w wypowiedź jest dopasowaniem bez uwzględniania wielkości liter z synonimem lub nazwą kanoniczną. LUIS nie używa listy poza dopasowaniem. Dopasowywanie rozmyte, Rdzeniowanie, plural i inne odmiany nie są rozpoznawane za pomocą jednostki listy. Aby zarządzać wariacjami, rozważ użycie [wzorca](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) z opcjonalną składnią tekstu.

![Jednostka listy](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>Przykład. JSON do zaimportowania do jednostki listy

  Możesz zaimportować wartości do istniejącej jednostki listy przy użyciu następującego formatu JSON:

  ```JSON
  [
      {
          "canonicalForm": "Blue",
          "list": [
              "navy",
              "royal",
              "baby"
          ]
      },
      {
          "canonicalForm": "Green",
          "list": [
              "kelly",
              "forest",
              "avacado"
          ]
      }
  ]
  ```

## <a name="example-json-response"></a>Przykładowa odpowiedź JSON

Załóżmy, że aplikacja ma listę o nazwie `Cities`, co pozwala na odmianę nazw miast, w tym miasto lotniska (Sea-Tac), kod lotniska (Sea), kod pocztowy (98101) i numer kierunkowy telefonu (206).

|Element listy|Synonimy elementów|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

W poprzednim wypowiedź słowo `paris` jest zamapowane na element paryski jako część jednostki `Cities` listy. Jednostka listy dopasowuje zarówno znormalizowaną nazwę elementu, jak i synonimy elementów.

#### <a name="v2-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)


Jest to kod JSON, `verbose=false` jeśli jest ustawiony w ciągu zapytania:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Jest to kod JSON, `verbose=true` jeśli jest ustawiony w ciągu zapytania:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
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

* * *

|Obiekt danych|Nazwa jednostki|Wartość|
|--|--|--|
|Jednostka listy|`Cities`|`paris`|


## <a name="next-steps"></a>Następne kroki

W tym [samouczku](tutorial-list-entity.md)dowiesz się, jak za pomocą **jednostki listy** wyodrębnić dokładne dopasowania tekstu z listy znanych elementów.
