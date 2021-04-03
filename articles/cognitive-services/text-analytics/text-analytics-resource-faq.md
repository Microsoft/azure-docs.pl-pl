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
ms.date: 01/05/2021
ms.author: aahi
ms.openlocfilehash: 9a4e179767cc38169cd794f4cd629604bdcdaab0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97955046"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-api"></a>Często zadawane pytania dotyczące interfejs API analizy tekstu

 Znajdź odpowiedzi na często zadawane pytania dotyczące pojęć, kodu i scenariuszy związanych z interfejs API analizy tekstu w usłudze Azure Cognitive Services.

## <a name="can-text-analytics-identify-sarcasm"></a>Czy analiza tekstu identyfikować sarkazmu?

Analiza jest dla tonacji z ujemnym wynikiem, a nie od wykrywania nastrojenia.

W analizie tonacji zawsze występuje pewien stopień niedokładności, ale model jest najbardziej przydatny, gdy nie ma żadnego ukrytego znaczenia ani podtekstu zawartości. Sarkazmu, humor i podobne złożonych zawartość jest zależna od kontekstu kulturowego i norm do przekazywania zamiaru. Ten typ zawartości należy do najtrudniejszych do przeanalizowania. Zazwyczaj największą rozbieżność między danym wynikiem uzyskanym przez analizatora a oceną subiektywną przez człowieka jest za zawartość z złożonych znaczeniem.

## <a name="can-i-add-my-own-training-data-or-models"></a>Czy mogę dodać własne dane szkoleniowe lub modele?

Nie, modele są przedszkolene. Jedyne operacje dostępne dla przekazanych danych to Punktacja, wyodrębnianie kluczowych fraz i wykrywanie języka. Nie obsługujemy modeli niestandardowych. Jeśli chcesz utworzyć i hostować niestandardowe modele uczenia maszynowego, weź pod uwagę [możliwości uczenia maszynowego w Microsoft R Server](/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Czy mogę zażądać dodatkowych języków?

Analiza tonacji i wyodrębnianie kluczowych fraz są dostępne dla [wybranej liczby języków](./language-support.md). Przetwarzanie języka naturalnego jest skomplikowane i wymaga poważnej testów przed wydaniem nowych funkcji. Z tego powodu unikamy wstępnego ogłaszania pomocy technicznej, aby nikt nie przeprowadził zależności od funkcjonalności wymagającej większego czasu. 

Aby pomóc nam określić priorytety języków, które mają być wykonywane po następnym, zagłosuj na określone języki na [głos użytkownika](https://cognitive.uservoice.com/forums/555922-text-analytics). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Dlaczego wyodrębnianie kluczowych fraz zwraca niektóre słowa, ale nie inne?

Wyodrębnianie kluczowych fraz eliminuje nieistotne słowa i autonomiczne przymiotniki. Kombinacje przymiotnik-rzeczownik, takie jak "spektakularnych views" lub "Foggy Pogoda", są zwracane razem.

Ogólnie rzecz biorąc, dane wyjściowe składają się z rzeczowników i obiektów zdania. Dane wyjściowe są wyświetlane w kolejności ważności, przy czym pierwsza fraza jest najważniejszym elementem. Znaczenie jest mierzone przez liczbę przypadków, w których wymieniono konkretne koncepcje, lub relacje tego elementu z innymi elementami w tekście.

## <a name="why-does-output-vary-given-identical-inputs"></a>Dlaczego dane wyjściowe różnią się w zależności od tego, czy są one identyczne?

Udoskonalenia modeli i algorytmów są ogłaszane w przypadku, gdy zmiana jest główna lub jest w trybie cichym włączana do usługi, jeśli aktualizacja jest niewielka. W miarę upływu czasu można stwierdzić, że te same dane wejściowe są wynikiem innym wynikami tonacji lub zwrotów kluczy. Jest to normalna i zacelowa konsekwencja użycia zarządzanych zasobów uczenia maszynowego w chmurze.

## <a name="service-availability-and-redundancy"></a>Dostępność i nadmiarowość usługi

### <a name="is-text-analytics-service-zone-resilient"></a>Czy analiza tekstu strefy usługi są odporne?

Tak. Usługa analiza tekstu jest domyślnie odporna na strefy.

### <a name="how-do-i-configure-the-text-analytics-service-to-be-zone-resilient"></a>Jak mogę skonfigurować usługę analiza tekstu, aby była odporna na strefy?

W celu włączenia odporności stref nie trzeba wykonywać żadnych czynności konfiguracyjnych. Odporność stref na zasoby analiza tekstu jest domyślnie dostępna i zarządzana przez samą usługę.

## <a name="next-steps"></a>Następne kroki

Czy masz pytanie dotyczące brakującej funkcji lub funkcji? Rozważ zażądanie lub oddanie głosu w naszej [witrynie sieci Web](https://cognitive.uservoice.com/forums/555922-text-analytics)w usłudze UserVoice.

## <a name="see-also"></a>Zobacz też

 * [StackOverflow: interfejs API analizy tekstu](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 * [StackOverflow: Cognitive Services](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
