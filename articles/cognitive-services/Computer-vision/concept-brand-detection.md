---
title: Wykrywanie marki — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: W tym artykule omówiono wyspecjalizowany tryb wykrywania obiektów; wykrywanie marki i/lub logo przy użyciu przetwarzanie obrazów API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 5892fc0bbbd07690ff010e8e1212a914733cbb18
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778989"
---
# <a name="detect-popular-brands-in-images"></a>Wykrywanie popularnych marek na obrazach

Wykrywanie marki to wyspecjalizowany tryb wykrywania obiektów, który używa bazy danych tysięcy logo globalnych do identyfikowania marek komercyjnych na obrazach lub wideo. [](concept-object-detection.md) Za pomocą tej funkcji można na przykład ustalać, które marki są najpopularniejsze w mediach społecznościowych lub najpowszechniej promowane za pomocą lokowania produktów w mediach.

Usługa przetwarzanie obrazów wykrywa, czy na danym obrazie znajdują się logo marki; Jeśli tak, zwraca nazwę marki, ocenę ufności i współrzędne pola graniczności wokół logo.

Wbudowana baza danych logo obejmuje popularne marki sprzętu elektronicznego, odzieży i nie tylko. Jeśli znajdziesz markę, którego szukasz, nie została wykryta przez usługę przetwarzanie obrazów, możesz lepiej tworzyć i trenować własny detektor logo przy użyciu usługi [Custom Vision](../custom-vision-service/index.yml) service.

## <a name="brand-detection-example"></a>Przykład wykrywania marki

Poniższe odpowiedzi JSON ilustrują, przetwarzanie obrazów zwracane podczas wykrywania marek na przykładowych obrazach.

![Czerwona shirt z etykietą i logo firmy Microsoft](./Images/red-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":20,
         "y":97,
         "w":62,
         "h":52
      }
   }
]
```

W niektórych przypadkach detektor marki wybierze zarówno obraz logo, jak i stylizowaną nazwę marki jako dwa oddzielne logo.

![Szarashirt z etykietą i logo firmy Microsoft](./Images/gray-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":106,
         "w":55,
         "h":46
      }
   },
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":86,
         "w":202,
         "h":63
      }
   }
]
```

## <a name="use-the-api"></a>Używanie interfejsu API

Funkcja wykrywania marki jest częścią interfejsu API [analizowanie obrazów](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. Ten interfejs API można wywołać za pośrednictwem natywnego zestawu SDK lub wywołań REST. Uwzględnij `Brands` w **parametrze zapytania visualFeatures.** Następnie, gdy otrzymasz pełną odpowiedź JSON, po prostu prze analizuj ciąg zawartości `"brands"` sekcji.

* [Szybki start: przetwarzanie obrazów API REST lub bibliotek klienckich](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)