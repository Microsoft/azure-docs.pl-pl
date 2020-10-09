---
title: Często zadawane pytania dotyczące interfejs API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Znajdź odpowiedzi na często zadawane pytania dotyczące pojęć, kodu i scenariuszy związanych z interfejs API analizy tekstu Cognitive Services platformy Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: aca3b0bea65923cee1bae25020058666c973922c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "73837119"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-cognitive-service"></a>Często zadawane pytania dotyczące usługi analiza tekstu poznawczej

 Znajdź odpowiedzi na często zadawane pytania dotyczące pojęć, kodu i scenariuszy związanych z interfejs API analizy tekstu Microsoft Cognitive Services na platformie Azure.

## <a name="can-text-analytics-identify-sarcasm"></a>Czy analiza tekstu identyfikować sarkazmu?

Analiza jest dla tonacji z ujemnym wynikiem, a nie od wykrywania nastrojenia.

W analizie tonacji zawsze występuje pewien stopień niedokładności, ale model jest najbardziej przydatny, gdy nie ma żadnego ukrytego znaczenia ani podtekstu zawartości. Sarkazmu, humor i podobne złożonych zawartość jest zależna od kontekstu kulturowego i norm do przekazywania zamiaru. Ten typ zawartości należy do najtrudniejszych do przeanalizowania. Zazwyczaj największą rozbieżność między danym wynikiem uzyskanym przez analizatora a oceną subiektywną przez człowieka jest za zawartość z złożonych znaczeniem.

## <a name="can-i-add-my-own-training-data-or-models"></a>Czy mogę dodać własne dane szkoleniowe lub modele?

Nie, modele są przedszkolene. Jedyne operacje dostępne dla przekazanych danych to Punktacja, wyodrębnianie kluczowych fraz i wykrywanie języka. Nie obsługujemy modeli niestandardowych. Jeśli chcesz utworzyć i hostować niestandardowe modele uczenia maszynowego, weź pod uwagę [możliwości uczenia maszynowego w Microsoft R Server](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Czy mogę zażądać dodatkowych języków?

Analiza tonacji i wyodrębnianie kluczowych fraz są dostępne dla [wybranej liczby języków](text-analytics-supported-languages.md). Przetwarzanie języka naturalnego jest skomplikowane i wymaga poważnej testów przed wydaniem nowych funkcji. Z tego powodu unikamy wstępnego ogłaszania pomocy technicznej, aby nikt nie przeprowadził zależności od funkcjonalności wymagającej większego czasu. 

Aby pomóc nam określić priorytety języków, które mają być wykonywane po następnym, zagłosuj na określone języki na [głos użytkownika](https://cognitive.uservoice.com/forums/555922-text-analytics). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Dlaczego wyodrębnianie kluczowych fraz zwraca niektóre słowa, ale nie inne?

Wyodrębnianie kluczowych fraz eliminuje nieistotne słowa i autonomiczne przymiotniki. Kombinacje przymiotnik-rzeczownik, takie jak "spektakularnych views" lub "Foggy Pogoda", są zwracane razem.

Ogólnie rzecz biorąc, dane wyjściowe składają się z rzeczowników i obiektów zdania. Dane wyjściowe są wyświetlane w kolejności ważności, przy czym pierwsza fraza jest najważniejszym elementem. Znaczenie jest mierzone przez liczbę przypadków, w których wymieniono konkretne koncepcje, lub relacje tego elementu z innymi elementami w tekście.

## <a name="why-does-output-vary-given-identical-inputs"></a>Dlaczego dane wyjściowe różnią się w zależności od tego, czy są one identyczne?

Udoskonalenia modeli i algorytmów są ogłaszane w przypadku, gdy zmiana jest główna lub jest w trybie cichym włączana do usługi, jeśli aktualizacja jest niewielka. W miarę upływu czasu można stwierdzić, że te same dane wejściowe są wynikiem innym wynikami tonacji lub zwrotów kluczy. Jest to normalna i zacelowa konsekwencja użycia zarządzanych zasobów uczenia maszynowego w chmurze.

## <a name="next-steps"></a>Następne kroki

Czy masz pytanie dotyczące brakującej funkcji lub funkcji? Rozważ zażądanie lub oddanie głosu w naszej [witrynie sieci Web](https://cognitive.uservoice.com/forums/555922-text-analytics)w usłudze UserVoice.

## <a name="see-also"></a>Zobacz też

 [StackOverflow: interfejs API analizy tekstu](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow: Cognitive Services](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
