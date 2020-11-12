---
title: Zawartość specyficzna dla domeny — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak określić domenę kategoryzacji obrazu w celu zwrócenia bardziej szczegółowych informacji o obrazie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5cd872d66088e165bfc8356ab6d96a0a6135a0e0
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94538312"
---
# <a name="detect-domain-specific-content"></a>Wykrywanie zawartości specyficznej dla domeny

Oprócz tagowania i kategoryzacji wysokiego poziomu, przetwarzanie obrazów również obsługuje dalsze analizy specyficzne dla domeny przy użyciu modeli, które zostały przeszkolone na wyspecjalizowanych danych.

Istnieją dwa sposoby użycia modeli specyficznych dla domeny: same (analiza z zakresem) lub rozszerzenie funkcji kategoryzacji.

### <a name="scoped-analysis"></a>Analiza w zakresie

Można analizować obraz przy użyciu tylko wybranego modelu specyficznego dla domeny, wywołując [modele/ \<model\> /analyze](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API.

Poniżej przedstawiono przykładową odpowiedź JSON zwracaną przez **modele/osobistości/analizowanie** interfejsu API dla danego obrazu:

![Satya Nadella, uśmiechnięty](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>Rozszerzona analiza kategoryzacji

Można również użyć modeli specyficznych dla domeny, aby uzupełnić ogólną analizę obrazu. Tę czynność należy wykonać w ramach [kategoryzacji wysokiego poziomu](concept-categorizing-images.md) , określając modele specyficzne dla domeny w parametrze *Details* wywołania interfejsu API [analizy](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) .

W tym przypadku klasyfikator 86-kategorii Taksonomia jest wywoływana jako pierwsza. Jeśli dowolna z wykrytych kategorii ma zgodny model specyficzny dla domeny, obraz jest również przesyłany przez ten model i są dodawane wyniki.

W poniższej odpowiedzi JSON przedstawiono sposób, w jaki analiza specyficzna dla domeny może być uwzględniona jako `detail` węzeł w szerszej analizie kategoryzacji.

```json
"categories":[
  {
    "name":"abstract_",
    "score":0.00390625
  },
  {
    "name":"people_",
    "score":0.83984375,
    "detail":{
      "celebrities":[
        {
          "name":"Satya Nadella",
          "faceRectangle":{
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[
        {
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>Wyświetlanie listy modeli specyficznych dla domeny

Obecnie przetwarzanie obrazów obsługuje następujące modele specyficzne dla domeny:

| Nazwa | Opis |
|------|-------------|
| osobistości | Rozpoznawanie osobistości, obsługiwane w przypadku obrazów sklasyfikowanych w `people_` kategorii |
| charakterystycznych elementów krajobrazu | Rozpoznawanie punktu orientacyjnego, obsługiwane w przypadku obrazów sklasyfikowanych w `outdoor_` `building_` kategorii lub |

Wywołanie interfejsu API [modeli](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20e) zwróci te informacje wraz z kategoriami, do których można zastosować każdy model:

```json
{
  "models":[
    {
      "name":"celebrities",
      "categories":[
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {
      "name":"landmarks",
      "categories":[
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

Poznaj koncepcje dotyczące [kategoryzacji obrazów](concept-categorizing-images.md).
