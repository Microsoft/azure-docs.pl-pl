---
title: Kategoryzacja obrazów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Poznaj pojęcia związane z funkcją kategoryzacji obrazów w interfejsie API przetwarzanie obrazów API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 97a6a08730ae0c87df3b65185d48297e2338e69b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768111"
---
# <a name="categorize-images-by-subject-matter"></a>Kategoryzowanie obrazów według tematu

Oprócz tagów i opisu przetwarzanie obrazów kategorie oparte na taksonomii wykryte na obrazie. W przeciwieństwie do tagów kategorie są zorganizowane w hierarchii dziedziczenia nadrzędnego/podrzędnego i jest ich mniej (86, w przeciwieństwie do tysięcy tagów). Wszystkie nazwy kategorii są w języku angielskim. Kategoryzacja może być wykonywana samodzielnie lub obok nowszego modelu tagów.

## <a name="the-86-category-concept"></a>Koncepcja 86 kategorii

Za pomocą listy 86 kategorii na poniższym diagramie obraz może kategoryzować obraz w szerokim lub specyficznym znaczeniu. Aby uzyskać informacje dotyczące pełnej taksonomii w formacie tekstowym, zobacz temat [Taksonomia kategorii](category-taxonomy.md).

![Pogrupowane listy wszystkich kategorii w taksonomii kategorii](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Przykłady kategoryzacji obrazów

Następująca odpowiedź JSON ilustruje, przetwarzanie obrazów zwraca podczas kategoryzowania przykładowego obrazu na podstawie jego cech wizualnych.

![Kobieta na daszce w budynku z domu](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

W poniższej tabeli przedstawiono typowy zestaw obrazów i kategorię zwróconą przez przetwarzanie obrazów dla każdego obrazu.

| Obraz | Kategoria |
|-------|----------|
| ![Cztery osoby pozytywowane razem jako rodzina](./Images/family_photo.png) | people_group |
| ![A przesieć w nieschętnych polach](./Images/cute_dog.png) | animal_dog |
| ![Osoba stojąca na skałach górskich o wycopszanych skałach](./Images/mountain_vista.png) | outdoor_mountain |
| ![Stos ról narzuty na tabelę](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>Używanie interfejsu API

Funkcja kategoryzacji jest częścią interfejsu API [analizowanie obrazów](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. Ten interfejs API można wywołać za pomocą natywnego zestawu SDK lub wywołań REST. Uwzględnij `Categories` w **parametrze zapytania visualFeatures.** Następnie, gdy otrzymasz pełną odpowiedź JSON, po prostu prze analizuj ciąg zawartości `"categories"` sekcji.

* [Szybki start: przetwarzanie obrazów API REST lub bibliotek klienckich](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Następne kroki

Poznaj powiązane pojęcia dotyczące [tagowania obrazów](concept-tagging-images.md) i [opisywania obrazów.](concept-describing-images.md)
