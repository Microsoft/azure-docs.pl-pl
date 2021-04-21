---
title: Wykrywanie twarzy — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Poznaj pojęcia związane z funkcją wykrywania twarzy w interfejsie API przetwarzanie obrazów API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: bfb352c68b910a114e13041da4e8e86529e52040
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778845"
---
# <a name="face-detection-with-computer-vision"></a>Wykrywanie twarzy za pomocą przetwarzanie obrazów

przetwarzanie obrazów wykrywać ludzkie twarze na obrazie i generować wiek, płeć i prostokąt dla każdej wykrytej twarzy. 

> [!NOTE]
> Ta funkcja jest również oferowana przez usługę rozpoznawania [twarzy platformy](../face/index.yml) Azure. Zapoznaj się z tą alternatywą, aby uzyskać bardziej szczegółową analizę twarzy, w tym identyfikację twarzy i wykrywanie pozy. 

## <a name="face-detection-examples"></a>Przykłady wykrywania twarzy

W poniższym przykładzie pokazano odpowiedź JSON zwróconą przez przetwarzanie obrazów dla obrazu zawierającego jedną ludzką twarz.

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

W następnym przykładzie pokazano odpowiedź JSON zwróconą dla obrazu zawierającego wiele ludzkich twarzy.

![Analiza obrazów twarzy na zdjęciach rodziny](./Images/family_photo_face.png)

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

Funkcja wykrywania twarzy jest częścią interfejsu API [analizowanie obrazów](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. Ten interfejs API można wywołać za pomocą natywnego zestawu SDK lub wywołań REST. Uwzględnij `Faces` w **parametrze zapytania visualFeatures.** Następnie, gdy otrzymasz pełną odpowiedź JSON, po prostu prze analizuj ciąg pod celu uzyskania zawartości `"faces"` sekcji.

* [Szybki start: przetwarzanie obrazów API REST lub bibliotek klienckich](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
