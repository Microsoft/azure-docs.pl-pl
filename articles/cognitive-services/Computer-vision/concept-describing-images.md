---
title: Opisy obrazów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z funkcją opisu obrazu w interfejsie API przetwarzanie obrazów API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: c517fa98bfc17d4702a51d4990e860b2ed7aaefd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778935"
---
# <a name="describe-images-with-human-readable-language"></a>Opisywanie obrazów w języku czytelnym dla człowieka

przetwarzanie obrazów może analizować obraz i generować czytelne dla człowieka zdanie opisujące jego zawartość. Algorytm faktycznie zwraca kilka opisów na podstawie różnych cech wizualnych, a każdy opis ma ocenę ufności. Końcowe dane wyjściowe to lista opisów uporządkowanych od najwyższego do najniższego poziomu ufności.

## <a name="image-description-example"></a>Przykładowy opis obrazu

Następująca odpowiedź JSON ilustruje, przetwarzanie obrazów zwraca podczas opisywania przykładowego obrazu na podstawie jego cech wizualnych.

![Czarno-biały obraz budynków w Centrum](./Images/bw_buildings.png)

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

Funkcja opisu obrazu jest częścią interfejsu API [analizowanie obrazów](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. Ten interfejs API można wywołać za pośrednictwem natywnego zestawu SDK lub wywołań REST. Uwzględnij `Description` w **parametrze zapytania visualFeatures.** Następnie, gdy otrzymasz pełną odpowiedź JSON, po prostu prze analizuj ciąg zawartości `"description"` sekcji.

* [Szybki start: przetwarzanie obrazów API REST lub bibliotek klienckich](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Następne kroki

Poznaj powiązane pojęcia dotyczące [tagowania obrazów](concept-tagging-images.md) i [kategoryzacji obrazów.](concept-categorizing-images.md)
