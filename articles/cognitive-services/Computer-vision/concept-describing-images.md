---
title: Opisy obrazów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z funkcją opisu obrazu interfejs API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7bf95a2b49608ef1f031a3b443db92b42cdae624
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80244719"
---
# <a name="describe-images-with-human-readable-language"></a>Opisywanie obrazów w języku czytelnym dla ludzi

Przetwarzanie obrazów może analizować obraz i generować zdanie odczytane przez człowieka, które opisuje jego zawartość. Algorytm faktycznie zwraca kilka opisów opartych na różnych funkcjach wizualnych, a każdy opis otrzymuje wynik zaufania. Ostateczne dane wyjściowe to lista opisów uporządkowanych od najwyższego do najniższego zaufania.

## <a name="image-description-example"></a>Przykład opisu obrazu

Poniższa odpowiedź JSON ilustruje, co przetwarzanie obrazów zwraca podczas opisywania przykładowego obrazu na podstawie jego funkcji wizualizacji.

![Czarno-białe zdjęcie budynków w Manhattan](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Używanie interfejsu API

Funkcja opisu obrazu jest częścią usługi [Analizowanie obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) interfejsu API. Ten interfejs API można wywołać za pomocą natywnego zestawu SDK lub wywołań REST. Uwzględnij `Description` w parametrze zapytania **visualFeatures** . Po otrzymaniu pełnej odpowiedzi JSON należy po prostu przeanalizować ciąg dla zawartości `"description"` sekcji.

* [Szybki Start: przetwarzanie obrazów zestawu .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Szybki Start: analizowanie obrazu (interfejs API REST)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Następne kroki

Poznaj powiązane koncepcje [tagowania obrazów](concept-tagging-images.md) i [kategoryzacji obrazów](concept-categorizing-images.md).
