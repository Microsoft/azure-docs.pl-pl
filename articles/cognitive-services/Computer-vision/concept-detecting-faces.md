---
title: Wykrywanie czołowe — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Zapoznaj się z pojęciami dotyczącymi funkcji wykrywania kroju interfejs API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d10e4c98d3e2f00e7139063c8263e65ffd015d7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80244705"
---
# <a name="face-detection-with-computer-vision"></a>Wykrywanie kroju przy użyciu przetwarzanie obrazów

Przetwarzanie obrazów mogą wykrywać ludzkie twarze na obrazie i generować wiek, płeć oraz prostokąt dla każdej wykrytej twarzy. 

> [!NOTE]
> Ta funkcja jest również oferowana [przez usługę Azure](/azure/cognitive-services/face/) site. Zapoznaj się z tą alternatywą w celu uzyskania bardziej szczegółowej analizy, w tym identyfikacji i wykrywania ułożenia. 

## <a name="face-detection-examples"></a>Przykłady wykrywania kroju

Poniższy przykład demonstruje odpowiedź JSON zwracaną przez przetwarzanie obrazów obrazu zawierającego pojedynczy człowiek.

![Analiza obrazu — kobieta, dach, twarz](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

W następnym przykładzie pokazano odpowiedź JSON zwracaną dla obrazu zawierającego wiele twarzy.

![Wzrokowa analiza kroju zdjęcia rodziny](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="use-the-api"></a>Używanie interfejsu API

Funkcja wykrywania czołowa jest częścią usługi [Analizowanie obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) interfejsu API. Ten interfejs API można wywołać za pomocą natywnego zestawu SDK lub wywołań REST. Uwzględnij `Faces` w parametrze zapytania **visualFeatures** . Po otrzymaniu pełnej odpowiedzi JSON należy po prostu przeanalizować ciąg dla zawartości `"faces"` sekcji.

* [Szybki Start: przetwarzanie obrazów zestawu .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Szybki Start: analizowanie obrazu (interfejs API REST)](./quickstarts/csharp-analyze.md)
