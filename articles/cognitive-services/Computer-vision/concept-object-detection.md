---
title: Wykrywanie obiektów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Poznaj koncepcje związane z funkcją wykrywania obiektów dotyczącą interfejs API przetwarzania obrazów i limitów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3957e15a09bd7e7ecd814d169451af3241108b64
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80131669"
---
# <a name="detect-common-objects-in-images"></a>Wykrywanie typowych obiektów w obrazach

Wykrywanie obiektów jest podobne do [tagowania](concept-tagging-images.md), ale interfejs API zwraca współrzędne pola ograniczenia (w pikselach) dla każdego znalezionego obiektu. Na przykład jeśli na obrazie znajduje się pies, kot i osoba, operacja wykrywania utworzy listę tych obiektów wraz z ich współrzędnymi na obrazie. Korzystając z tej funkcji, można przetwarzać relacje między obiektami w obrazie. Umożliwia również określenie, czy istnieje wiele wystąpień tego samego tagu w obrazie.

Interfejs API wykrywania stosuje Tagi na podstawie obiektów lub elementów życia zidentyfikowanych w obrazie. Obecnie nie ma żadnej formalnej relacji między taksonomią tagowania a taksonomią wykrywania obiektów. W przypadku poziomu koncepcyjnego interfejs API wykrywania odnajduje tylko obiekty i rzeczy, w których interfejs API tagów może również zawierać warunki kontekstowe, takie jak "wewnętrzne", które nie mogą być lokalizowane przy użyciu pól ograniczenia.

## <a name="object-detection-example"></a>Przykład wykrywania obiektów

Następująca odpowiedź JSON ilustruje, co przetwarzanie obrazów zwraca podczas wykrywania obiektów w przykładowym obrazie.

![Kobieta przy użyciu urządzenia Surface firmy Microsoft w kuchni](./Images/windows-kitchen.jpg)

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

Ważne jest, aby zauważyć ograniczenia wykrywania obiektów, aby uniknąć lub wyeliminować efekty fałszywych negatywnych (nieodebranych obiektów) i szczegółowych informacji.

* Obiekty zwykle nie są wykrywane, jeśli są małe (mniej niż 5% obrazu).
* Obiekty zwykle nie są wykrywane, jeśli są ułożone blisko siebie (na przykład stos płyt).
* Obiekty nie są rozróżniane przez nazwy marki lub produktów (na przykład różne rodzaje wyniesień Można jednak uzyskać informacje o marce z obrazu za pomocą funkcji [wykrywania marki](concept-brand-detection.md) .

## <a name="use-the-api"></a>Używanie interfejsu API

Funkcja wykrywania obiektów jest częścią usługi [Analizowanie obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) interfejsu API. Ten interfejs API można wywołać za pomocą natywnego zestawu SDK lub wywołań REST. Uwzględnij `Objects` w parametrze zapytania **visualFeatures** . Po otrzymaniu pełnej odpowiedzi JSON należy po prostu przeanalizować ciąg dla zawartości `"objects"` sekcji.

* [Szybki Start: przetwarzanie obrazów zestawu .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Szybki Start: analizowanie obrazu (interfejs API REST)](./quickstarts/csharp-analyze.md)