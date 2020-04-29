---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: e879afdbd4c34e9d74405644de86421fb2cbab46
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77279537"
---
Przykład wyrażenia długości jest zgodny z określonym formatem.

Pole `text` zawiera tekst przykładowej wypowiedzi. Pole `intentName` musi odpowiadać nazwie istniejącej wypowiedzi w aplikacji LUIS. Pole `entityLabels` jest wymagane. Jeśli nie chcesz dodawać etykiet do żadnych jednostek, podaj pustą tablicę.

Jeśli tablica entityLabels nie jest pusta, elementy `startCharIndex` i `endCharIndex` muszą oznaczać jednostkę określoną w polu `entityName`. Indeks jest liczony od zera, co oznacza, że wartość 6 w pierwszym przykładzie odwołuje się do litery „S” wyrazu Seattle, a nie do spacji przed wielką literą S. Jeśli etykieta w tekście będzie rozpoczynać się lub kończyć spacją, wywołanie interfejsu API w celu dodania wypowiedzi nie powiedzie się.

```JSON
[
  {
    "text": "go to Seattle today",
    "intentName": "BookFlight",
    "entityLabels": [
      {
        "entityName": "Location::LocationTo",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "purple dogs are difficult to work with",
    "intentName": "BookFlight",
    "entityLabels": []
  }
]
```
