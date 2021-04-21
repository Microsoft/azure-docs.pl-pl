---
title: Zawartość specyficzna dla domeny — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak określić domenę kategoryzacji obrazów, aby zwrócić bardziej szczegółowe informacje o obrazie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 530ca81cedad06c949323889cc02d2a233dd0c02
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778827"
---
# <a name="detect-domain-specific-content"></a>Wykrywanie zawartości specyficznej dla domeny

Oprócz tagowania i kategoryzacji wysokiego poziomu program przetwarzanie obrazów także dalszą analizę specyficzną dla domeny przy użyciu modeli, które zostały wytrenowane na podstawie wyspecjalizowanych danych.

Istnieją dwa sposoby korzystania z modeli specyficznych dla domeny: samodzielnie (analiza w zakresie) lub jako rozszerzenie funkcji kategoryzacji.

### <a name="scoped-analysis"></a>Analiza o zakresie

Obraz można analizować tylko przy użyciu wybranego modelu specyficznego dla domeny, wywołując interfejs API [Models/ \<model\> /Analyze.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b)

Poniżej przedstawiono przykładową odpowiedź JSON zwróconą przez interfejs API **models/celebrities/analyze** dla danego obrazu:

![Satya Nadella na stałe, uśmiechnięta](./images/satya.jpeg)

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

Możesz również użyć modeli specyficznych dla domeny, aby uzupełnić ogólną analizę obrazów. Można to zrobić w ramach kategoryzacji wysokiego poziomu,  określając modele specyficzne dla domeny w parametrze szczegółów wywołania [interfejsu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API analizy. [](concept-categorizing-images.md)

W tym przypadku klasyfikator taksonomii 86 kategorii jest wywoływany jako pierwszy. Jeśli dowolna z wykrytych kategorii ma zgodny model specyficzny dla domeny, obraz również jest przekazywany przez ten model, a wyniki są dodawane.

W poniższej odpowiedzi JSON pokazano, jak analiza specyficzna dla domeny może zostać uwzględniona jako `detail` węzeł w szerszej analizie kategoryzacji.

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

## <a name="list-the-domain-specific-models"></a>Lista modeli specyficznych dla domeny

Obecnie przetwarzanie obrazów obsługuje następujące modele specyficzne dla domeny:

| Nazwa | Opis |
|------|-------------|
| Gwiazdy | Rozpoznawanie osobistości obsługiwane w przypadku obrazów sklasyfikowanych w `people_` kategorii |
| Zabytki | Rozpoznawanie punktów orientacyjnych obsługiwane w przypadku obrazów sklasyfikowanych `outdoor_` w `building_` kategoriach lub |

Wywołanie [interfejsu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f20e) API modeli zwróci te informacje wraz z kategoriami, do których można zastosować poszczególne modele:

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

Poznaj pojęcia dotyczące [kategoryzowania obrazów.](concept-categorizing-images.md)
