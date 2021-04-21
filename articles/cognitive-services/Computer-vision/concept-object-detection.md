---
title: Wykrywanie obiektów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Poznaj pojęcia związane z funkcją wykrywania obiektów interfejsu API przetwarzanie obrazów — użycie i limity.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a705a4134ec22d1cb14406cab4491f2af9177b48
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768001"
---
# <a name="detect-common-objects-in-images"></a>Wykrywanie typowych obiektów na obrazach

Wykrywanie obiektów jest podobne do [tagowania](concept-tagging-images.md), ale interfejs API zwraca współrzędne pola granicy (w pikselach) dla każdego znalezionego obiektu. Na przykład jeśli na obrazie znajduje się pies, kot i osoba, operacja wykrywania utworzy listę tych obiektów wraz z ich współrzędnymi na obrazie. Ta funkcja umożliwia przetwarzanie relacji między obiektami na obrazie. Pozwala również określić, czy istnieje wiele wystąpień tego samego tagu na obrazie.

Interfejs API wykrywania stosuje tagi na podstawie obiektów lub istot żywych zidentyfikowanych na obrazie. Obecnie nie ma formalnej relacji między taksonomią tagowania a taksonomią wykrywania obiektów. Na poziomie koncepcyjnym interfejs API wykrywania znajduje tylko obiekty i istoty żywych, a interfejs API tagów może również zawierać kontekstowe terminy, takie jak "pomieszczeniu", których nie można zlokalizowane za pomocą pól ograniczonych.

## <a name="object-detection-example"></a>Przykład wykrywania obiektów

Następująca odpowiedź JSON ilustruje, przetwarzanie obrazów zwraca podczas wykrywania obiektów na przykładzie obrazu.

![Kobieta korzystająca z urządzenia Microsoft Surface w kuchni](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="limitations"></a>Ograniczenia

Ważne jest, aby zwrócić uwagę na ograniczenia wykrywania obiektów, aby uniknąć lub ograniczyć skutki wyników fałszywie ujemnych (pominiętych obiektów) i ograniczonych szczegółów.

* Obiekty zwykle nie są wykrywane, jeśli są małe (mniej niż 5% obrazu).
* Obiekty zwykle nie są wykrywane, jeśli są ze sobą ściśle ułożone (np. stos na talerze).
* Obiekty nie różnią się nazwami marki ani produktów (na przykład różnymi typami sod na półce sklepowej). Informacje o marce można jednak uzyskać z obrazu przy użyciu funkcji [wykrywania marki.](concept-brand-detection.md)

## <a name="use-the-api"></a>Używanie interfejsu API

Funkcja wykrywania obiektów jest częścią interfejsu API [analizowanie obrazów](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. Ten interfejs API można wywołać za pomocą natywnego zestawu SDK lub wywołań REST. Uwzględnij `Objects` w **parametrze zapytania visualFeatures.** Następnie, gdy otrzymasz pełną odpowiedź JSON, po prostu prze analizuj ciąg zawartości `"objects"` sekcji.

* [Szybki start: przetwarzanie obrazów API REST lub bibliotek klienckich](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
