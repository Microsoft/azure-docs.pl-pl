---
title: Wykrywanie typu obrazu — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z funkcją wykrywania typu obrazu interfejs API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 6d2ed00f3fc6f5b52a9a13a96f1e1659e30f02d5
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532605"
---
# <a name="detecting-image-types-with-computer-vision"></a>Wykrywanie typów obrazów przy użyciu przetwarzanie obrazów

Korzystając z interfejsu API [analizy obrazów](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) , przetwarzanie obrazów może analizować typ zawartości obrazów, wskazując, czy obraz jest obiektem clipart czy rysunkiem.

## <a name="detecting-clip-art"></a>Wykrywanie grafiki clipart

Przetwarzanie obrazów analizuje obraz i ocenia prawdopodobieństwo, że obraz jest przycinania na skalę od 0 do 3, zgodnie z opisem w poniższej tabeli.

| Wartość | Znaczenie |
|-------|---------|
| 0 | Obiekt inny niż clipart |
| 1 | Udziel |
| 2 | Normalne — clipart |
| 3 | Dobry — clipart |

### <a name="clip-art-detection-examples"></a>Przykłady wykrywania obiektów clipart

Poniższe odpowiedzi JSON przedstawiają, co przetwarzanie obrazów zwraca, gdy ocenia prawdopodobieństwo, że przykładowe obrazy są przycinania.

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

![Niebieska i czołowa Jard](./Images/house_yard.png)

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

## <a name="detecting-line-drawings"></a>Wykrywanie rysunków liniowych

Przetwarzanie obrazów analizuje obraz i zwraca wartość logiczną wskazującą, czy obraz jest rysunkiem liniowym.

### <a name="line-drawing-detection-examples"></a>Przykłady wykrywania rysowania linii

Poniższe odpowiedzi JSON przedstawiają, co przetwarzanie obrazów zwraca, gdy wskazuje, czy przykładowe obrazy są ilustracjami linii.

![Obraz przedstawiający rysowanie linii elementu Lion](./Images/lion_drawing.png)

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

Funkcja wykrywania typu obrazu jest częścią usługi [Analizowanie obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) interfejsu API. Ten interfejs API można wywołać za pomocą natywnego zestawu SDK lub wywołań REST. Uwzględnij `ImageType` w parametrze zapytania **visualFeatures** . Po otrzymaniu pełnej odpowiedzi JSON należy po prostu przeanalizować ciąg dla zawartości `"imageType"` sekcji.

* [Szybki Start: przetwarzanie obrazów interfejsów API REST lub bibliotek klienckich](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
