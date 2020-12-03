---
title: Tagi zawartości — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Zapoznaj się z pojęciami dotyczącymi funkcji tagowania obrazów w interfejs API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a75e55cd51890173b6733ab5f8c0f1bcbfeef0f5
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532979"
---
# <a name="applying-content-tags-to-images"></a>Stosowanie tagów zawartości do obrazów

Przetwarzanie obrazów zwraca Tagi na podstawie tysięcy rozpoznawalnych obiektów, żywych, scenerii i akcji. Gdy tagi są niejednoznaczne lub wykraczają poza popularną wiedzę, odpowiedź interfejsu API zawiera „wskazówki”, aby wyjaśnić ich znaczenie w kontekście znanego otoczenia. Tagi nie są zorganizowane w postaci taksonomii i nie mają hierarchii dziedziczenia. Kolekcja tagów zawartości stanowi podstawę „opisu” obrazu wyświetlanego jako język zrozumiały dla użytkownika w formie pełnych zdań. Należy pamiętać, że obecnie jedynym obsługiwanym językiem opisu obrazu jest język angielski.

Po przekazaniu obrazu lub określeniu adresu URL obrazu algorytmy przetwarzania obrazów tworzą tagi wyjściowe na podstawie obiektów, żywych istot i akcji zidentyfikowanych na obrazie. Tagowanie nie jest ograniczone do głównego tematu, na przykład do osoby na pierwszym planie, ale uwzględnia także otoczenie (wewnątrz lub na zewnątrz), meble, narzędzia, rośliny, zwierzęta, akcesoria, gadżety itd.

## <a name="image-tagging-example"></a>Przykład tagowania obrazu

Następująca odpowiedź JSON ilustruje, co przetwarzanie obrazów zwracać podczas tagowania funkcji wizualnych wykrytych w przykładowym obrazie.

![Niebieska i czołowa Jard](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Używanie interfejsu API

Funkcja tagowania jest częścią interfejsu API [analizowania obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Ten interfejs API można wywołać za pomocą natywnego zestawu SDK lub wywołań REST. Uwzględnij `Tags` w parametrze zapytania **visualFeatures** . Po otrzymaniu pełnej odpowiedzi JSON należy po prostu przeanalizować ciąg dla zawartości `"tags"` sekcji.

* [Szybki Start: przetwarzanie obrazów interfejsów API REST lub bibliotek klienckich](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Następne kroki

Poznaj powiązane koncepcje [kategoryzacji obrazów](concept-categorizing-images.md) i [opisywania obrazów](concept-describing-images.md).
