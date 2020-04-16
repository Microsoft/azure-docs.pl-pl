---
title: Moderowanie wideo z ludzką recenzją - Content Moderator
titleSuffix: Azure Cognitive Services
description: Użyj moderowania wideo wspomaganego maszynowo i narzędzia Recenzja, aby moderować nieodpowiednie treści
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 0c031a890efc7fad7e5d9caefce3b0e66c515d90
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404249"
---
# <a name="video-moderation-with-human-review"></a>Umiar wideo z ludzką recenzją

Użyj narzędzia moderatora wideo wspomaganego [maszynowo](video-moderation-api.md) i [narzędzia do przeglądania,](Review-Tool-User-Guide/human-in-the-loop.md) aby moderować filmy i transkrypcje dla treści dla dorosłych (jawnych) i rasistowskich (sugestywnych), aby uzyskać najlepsze wyniki dla Twojej firmy.

## <a name="video-trained-classifier-preview"></a>Klasyfikator wyszkolony w trybie wideo (wersja zapoznawcza)

Klasyfikacja wideo wspomagana maszynowo jest osiągana za pomocą modeli wyszkolonych w zakresie obrazów lub modeli przeszkolonych w zakresie wideo. W przeciwieństwie do klasyfikatorów wideo wyszkolonych w obrazie, klasyfikator wideo dla dorosłych i rasowych filmów firmy Microsoft jest szkolony w filmach wideo. Ta metoda powoduje lepszą jakość dopasowania.

## <a name="shot-detection"></a>Wykrywanie strzałów

Podczas wyprowadzania szczegółów klasyfikacji dodatkowa analiza wideo pomaga w większej elastyczności w analizowaniu filmów. Zamiast wyprowadzać tylko klatki, usługa moderowania wideo firmy Microsoft zapewnia również informacje na poziomie ujęcia. Teraz masz możliwość analizowania filmów na poziomie ujęcia i na poziomie klatki.

## <a name="key-frame-detection"></a>Wykrywanie klatek kluczowych

Zamiast wyprowadzać klatki w regularnych odstępach czasu, usługa moderowania wideo identyfikuje i wyprowadza tylko potencjalnie kompletne (dobre) klatki. Funkcja ta umożliwia wydajne generowanie ramek do analizy dorosłych i racy na poziomie ramy.

Poniższy wyciąg pokazuje częściową odpowiedź z potencjalnych zdjęć, klatek kluczowych i dorosłych i racy wyniki:

```json
"fragments":[  
  {  
    "start":0,
    "duration":18000
  },
  {  
    "start":18000,
    "duration":3600,
    "interval":3600,
    "events":[  
      [  
        {  
          "reviewRecommended":false,
          "adultScore":0.00001,
          "racyScore":0.03077,
          "index":5,
          "timestamp":18000,
          "shotIndex":0
        }
      ]
    ]
  },
  {  
    "start":18386372,
    "duration":119149,
    "interval":119149,
    "events":[  
      [  
        {  
          "reviewRecommended":true,
          "adultScore":0.00000,
          "racyScore":0.91902,
          "index":5085,
          "timestamp":18386372,
          "shotIndex":62
        }
      ]
    ]
```

## <a name="visualization-for-human-reviews"></a>Wizualizacja dla opinii ludzi

W przypadku bardziej zniuansowanych przypadków firmy potrzebują rozwiązania do przeglądu ludzkiego do renderowania wideo, jego ramek i tagów przypisanych maszynowo. Moderatorzy z ludzkich moderatorów przeglądający filmy i klatki otrzymują pełny wgląd w statystyki, zmieniają tagi i przekonują swoje decyzje.

![Domyślny widok narzędzia do przeglądania wideo](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Widok odtwarzacza do recenzji na poziomie wideo

Decyzje binarne na poziomie wideo są możliwe dzięki widokowi odtwarzacza wideo, który pokazuje potencjalne klatki dla dorosłych i rasistowskie. Recenzenci nawigują po filmie z różnymi opcjami prędkości, aby zbadać sceny. Potwierdzają swoje decyzje, toggling tagów.

![widok odtwarzacza narzędzi do przeglądu wideo](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Widok ramek, aby uzyskać szczegółowe recenzje

Szczegółowy przegląd wideo do analizy klatka po klatce jest możliwy w widoku opartym na klatce. Recenzenci przeglądają i wybierają jedną lub więcej ramek oraz przełączają tagi, aby potwierdzić swoje decyzje. Opcjonalnym następnym krokiem jest redakcja obraźliwych ramek lub zawartości.

![Widok ramek narzędzi do przeglądania wideo](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Moderowanie transkrypcji

Filmy zazwyczaj mają głos, który wymaga umiaru, jak również do obraźliwej mowy. Za pomocą usługi Azure Media Indexer do konwersji mowy na tekst i za pomocą interfejsu API przeglądu moderatora zawartości do przesyłania transkrypcji dla moderacji tekstu w narzędziu do przeglądu.

![Widok transkrypcji narzędzia do przeglądu wideo](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Następne kroki

- Wprowadzenie do szybkiego [startu moderowania wideo](video-moderation-api.md).
- Dowiedz się, jak generować [recenzje wideo](video-reviews-quickstart-dotnet.md) dla recenzentów z moderowane dane wyjściowe.
- Dodaj [recenzje transkrypcji wideo](video-transcript-reviews-quickstart-dotnet.md) do recenzji wideo.
- Zapoznaj się ze szczegółowym samouczkiem, jak opracować [kompletne rozwiązanie do moderowania wideo](video-transcript-moderation-review-tutorial-dotnet.md).