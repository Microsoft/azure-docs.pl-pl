---
title: Wykrywanie typu obrazu — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z funkcją wykrywania typu obrazu w interfejsie API przetwarzanie obrazów API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: dc24788ddd21ca2b7df1f9f92238c776dee33016
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778881"
---
# <a name="detecting-image-types-with-computer-vision"></a>Wykrywanie typów obrazów za pomocą przetwarzanie obrazów

Za pomocą [analizowanie obrazów](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API przetwarzanie obrazów analizować typ zawartości obrazów, wskazując, czy obraz jest obiektem clipart, czy rysunkiem liniowym.

## <a name="detecting-clip-art"></a>Wykrywanie obiektu clipart

przetwarzanie obrazów analizuje obraz i określa prawdopodobieństwo, że obraz jest obiektem clipart w skali od 0 do 3, zgodnie z opisem w poniższej tabeli.

| Wartość | Znaczenie |
|-------|---------|
| 0 | Obiekt inny niż clipart |
| 1 | Niejednoznaczne |
| 2 | Normalny obiekt clipart |
| 3 | Dobry obiekt clipart |

### <a name="clip-art-detection-examples"></a>Przykłady wykrywania obiektu clipart

Poniższe odpowiedzi JSON ilustrują, przetwarzanie obrazów zwraca podczas oceny prawdopodobieństwa, że przykładowe obrazy są obiektami clipart.

![Obraz clipart wycinka sera](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Niebieski dom i front](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>Wykrywanie rysunki linii

przetwarzanie obrazów analizuje obraz i zwraca wartość logiczną wskazującą, czy obraz jest rysunkiem liniowym.

### <a name="line-drawing-detection-examples"></a>Przykłady wykrywania rysowania linii

Poniższe odpowiedzi JSON ilustrują, przetwarzanie obrazów zwraca, wskazując, czy przykładowe obrazy są rysunkiem liniowym.

![Linia rysująca obraz łasucha](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Biały kwiat z zielonym tłem](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Używanie interfejsu API

Funkcja wykrywania typów obrazów jest częścią interfejsu API [analizowanie obrazów](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. Ten interfejs API można wywołać za pośrednictwem natywnego zestawu SDK lub wywołań REST. Uwzględnij `ImageType` w **parametrze zapytania visualFeatures.** Następnie, gdy otrzymasz pełną odpowiedź JSON, po prostu prze analizuj ciąg zawartości `"imageType"` sekcji.

* [Szybki start: przetwarzanie obrazów API REST lub bibliotek klienckich](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
