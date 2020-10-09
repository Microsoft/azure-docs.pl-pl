---
title: Moderowanie wideo dzięki przeglądowi przez człowieka Content Moderator
titleSuffix: Azure Cognitive Services
description: Używanie narzędzia do oceny wideo z obsługą maszyn oraz umiarkowanej nieodpowiedniej zawartości
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 0c031a890efc7fad7e5d9caefce3b0e66c515d90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81404249"
---
# <a name="video-moderation-with-human-review"></a>Moderowanie wideo przy użyciu recenzji przez człowieka

Za pomocą narzędzia do [łagodzenia filmów wideo](video-moderation-api.md) Content moderator i [narzędzi do przeglądu](Review-Tool-User-Guide/human-in-the-loop.md) można moderować wideo i transkrypcje dla dorosłych (jawnej) i erotycznej (z sugestią), aby uzyskać najlepsze wyniki dla Twojej firmy.

## <a name="video-trained-classifier-preview"></a>Klasyfikator przeszkolony przez wideo (wersja zapoznawcza)

Klasyfikacja wideo oparta na maszynach jest osiągana z użyciem modeli przeszkolonych przez obrazy lub modeli wideo. W przeciwieństwie do klasyfikatorów wideo szkolonych na obrazach, klasyfikator wideo firmy Microsoft dla dorosłych i erotycznej jest szkolony z filmami wideo. Ta metoda skutkuje lepszą zgodnością jakości.

## <a name="shot-detection"></a>Wykrywanie zrzutu

Podczas wprowadzania szczegółów klasyfikacji dodatkowe analizy wideo pomagają zapewnić większą elastyczność analizowania filmów wideo. Zamiast wyprowadzać tylko ramki, usługa moderowania wideo firmy Microsoft udostępnia również informacje na poziomie zrzutu. Masz teraz możliwość analizowania filmów wideo na poziomie zrzutu i poziomie ramki.

## <a name="key-frame-detection"></a>Wykrywanie klatek kluczowych

Zamiast umieszczania ramek w regularnych odstępach czasu usługa moderowania wideo identyfikuje i wyprowadza tylko potencjalnie kompletne (dobre) ramki. Funkcja umożliwia wydajne generowanie ramek na potrzeby analizy dorosłych i erotycznej na poziomie ramki.

Poniższy wyodrębnienie przedstawia część odpowiedzi z potencjalnymi zrzutami, kluczowymi klatkami i erotycznej:

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

## <a name="visualization-for-human-reviews"></a>Wizualizacja na potrzeby recenzji przez człowieka

Aby uzyskać więcej złożonychych przypadków, firmy potrzebują rozwiązania do oceny człowieka do renderowania wideo, jego ramek i znaczników przypisanych do komputera. W celu przeglądania filmów wideo i ramek można uzyskać pełny wgląd w szczegółowe dane, zmienić Tagi i przesłać decyzje.

![widok domyślny narzędzia do przeglądania wideo](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Widok odtwarzacza na potrzeby przeglądu na poziomie wideo

Decyzje binarne na poziomie wideo są dostępne z widokiem odtwarzacza wideo, który pokazuje potencjalną liczbę ramek dorosłych i erotycznej. Recenzenci ludzkich przechodźą wideo z różnymi opcjami szybkości, aby przeanalizować sceny. Potwierdzają oni decyzje, przełączając Tagi.

![widok odtwarzacza narzędzia do przeglądania wideo](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Widok ramek dla szczegółowych przeglądów

Szczegółowy przegląd wideo na potrzeby analizy klatek po klatce jest możliwy z widokiem opartym na ramce. Recenzenci przez pracowników przeglądają i wybierają co najmniej jedną ramkę i przełączają Tagi w celu potwierdzenia ich decyzji. Opcjonalny następny krok to Redakcja obraźliwych ramek lub zawartości.

![Widok ramek narzędzia do przeglądania wideo](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Moderowanie transkrypcji

Filmy wideo zwykle zawierają głos, który wymaga moderatora, a także w przypadku wulgarnej mowy. Usługa Azure Media Indexer służy do konwertowania zamiany mowy na tekst i używania interfejsu API przeglądu Content Moderator do przesyłania transkrypcji do moderowania tekstu w ramach narzędzia do przeglądu.

![Widok transkrypcji narzędzia do przeglądania wideo](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Następne kroki

- Wprowadzenie do [przewodnika Szybki Start dotyczącego moderowania wideo](video-moderation-api.md).
- Dowiedz się, jak generować [Recenzje wideo](video-reviews-quickstart-dotnet.md) dla recenzentów z moderowanych danych wyjściowych.
- Dodawaj [przeglądy transkrypcji wideo](video-transcript-reviews-quickstart-dotnet.md) do Twoich recenzji wideo.
- Zapoznaj się z szczegółowym samouczkiem dotyczącym tworzenia [kompletnego rozwiązania do moderowania wideo](video-transcript-moderation-review-tutorial-dotnet.md).