---
title: Zostań Lokalnym partnerem kodera
description: W tym artykule omówiono sposób weryfikowania lokalnych koderów strumieniowych na żywo.
services: media-services
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 08/31/2020
ms.topic: how-to
ms.service: media-services
ms.openlocfilehash: e07831ce4a016d49e0343da66efc0345effafd94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954365"
---
# <a name="how-to-verify-your-on-premises-live-streaming-encoder"></a>Weryfikowanie lokalnego kodera przesyłania strumieniowego na żywo

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Jako Azure Media Services lokalnego partnera kodera Media Services promować produkt, zalecając koder do klientów korporacyjnych. Aby móc pełnić rolę lokalnego partnera kodera, należy sprawdzić zgodność kodera lokalnego z Media Services. Aby to zrobić, wykonaj następujące weryfikacje.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="pass-through-live-event-verification"></a>Weryfikacja zdarzenia przekazującego na żywo

1. Na koncie Media Services upewnij się, że **punkt końcowy przesyłania strumieniowego** jest uruchomiony. 
2. Utwórz i uruchom wydarzenie **Pass-through** Live. <br/> Aby uzyskać więcej informacji, zobacz [Live Event states and billing](live-event-states-billing.md) (Stany i rozliczenia dotyczące wydarzenia na żywo).
3. Pobierz adresy URL pozyskiwania i skonfiguruj koder lokalny, aby używać adresu URL do wysyłania strumienia na żywo o wysokiej szybkości transmisji bitów do Media Services.
4. Uzyskaj adres URL wersji zapoznawczej i użyj go do sprawdzenia, czy dane wejściowe kodera są faktycznie odbierane.
5. Utwórz nowy obiekt **zasobów** .
6. Utwórz na **żywo wyjście** i użyj utworzonej nazwy zasobu.
7. Utwórz **lokalizator przesyłania strumieniowego** z wbudowanymi typami **zasad przesyłania strumieniowego** .
8. Wyświetl listę ścieżek w **lokalizatorze przesyłania strumieniowego** , aby odzyskać adresy URL do użycia.
9. Pobierz nazwę hosta dla **punktu końcowego przesyłania strumieniowego** , z którego chcesz przesyłać strumieniowo.
10. Połącz adres URL z kroku 8 z nazwą hosta w kroku 9, aby uzyskać pełny adres URL.
11. Uruchom koder na żywo przez około 10 minut.
12. Zatrzymaj wydarzenie na żywo. 
13. Użyj odtwarzacza, takiego jak [Azure Media Player](https://aka.ms/azuremediaplayer) , aby obejrzeć zarchiwizowany element zawartości, aby upewnić się, że odtwarzanie nie będzie widoczne na wszystkich poziomach jakości. Lub Obejrzyj i sprawdź poprawność przy użyciu adresu URL wersji zapoznawczej w trakcie sesji na żywo.
14. Zapisz identyfikator zasobu, opublikowany adres URL przesyłania strumieniowego dla archiwum na żywo oraz ustawienia i wersję używane z kodera na żywo.
15. Zresetuj stan zdarzenia na żywo po utworzeniu każdego przykładu.
16. Powtórz kroki od 5 do 15 dla wszystkich konfiguracji obsługiwanych przez koder (z niemniemi, napisami lub innymi szybkościami kodowania, a także bez nich).

## <a name="live-encoding-live-event-verification"></a>Weryfikacja zdarzeń na żywo w kodowaniu na żywo

1. Na koncie Media Services upewnij się, że **punkt końcowy przesyłania strumieniowego** jest uruchomiony. 
2. Utwórz i uruchom wydarzenie Live **Encoding** na żywo. <br/> Aby uzyskać więcej informacji, zobacz [Live Event states and billing](live-event-states-billing.md) (Stany i rozliczenia dotyczące wydarzenia na żywo).
3. Pobierz adresy URL pozyskiwania i skonfiguruj koder, aby wypchnąć strumień na żywo o pojedynczej szybkości transmisji bitów do Media Services.
4. Uzyskaj adres URL wersji zapoznawczej i użyj go do sprawdzenia, czy dane wejściowe kodera są faktycznie odbierane.
5. Utwórz nowy obiekt **zasobów** .
6. Utwórz na **żywo wyjście** i użyj utworzonej nazwy zasobu.
7. Utwórz **lokalizator przesyłania strumieniowego** z wbudowanymi typami **zasad przesyłania strumieniowego** .
8. Wyświetl listę ścieżek w **lokalizatorze przesyłania strumieniowego** , aby odzyskać adresy URL do użycia.
9. Pobierz nazwę hosta dla **punktu końcowego przesyłania strumieniowego** , z którego chcesz przesyłać strumieniowo.
10. Połącz adres URL z kroku 8 z nazwą hosta w kroku 9, aby uzyskać pełny adres URL.
11. Uruchom koder na żywo przez około 10 minut.
12. Zatrzymaj wydarzenie na żywo.
13. Użyj odtwarzacza, takiego jak [Azure Media Player](https://aka.ms/azuremediaplayer) , aby obejrzeć zarchiwizowany element zawartości, aby upewnić się, że odtwarzanie nie ma widocznych żadnych błędów dla wszystkich poziomów jakości. Lub Obejrzyj i sprawdź poprawność przy użyciu adresu URL wersji zapoznawczej w trakcie sesji na żywo.
14. Zapisz identyfikator zasobu, opublikowany adres URL przesyłania strumieniowego dla archiwum na żywo oraz ustawienia i wersję używane z kodera na żywo.
15. Zresetuj stan zdarzenia na żywo po utworzeniu każdego przykładu.
16. Powtórz kroki od 5 do 15 dla wszystkich konfiguracji obsługiwanych przez koder (z niemniemi, napisami lub innymi szybkościami kodowania, a także bez nich).

## <a name="longevity-verification"></a>Weryfikacja eksploatacji

Wykonaj te same czynności co w przypadku [weryfikacji zdarzenia](#pass-through-live-event-verification) dotyczącego przekazywania na żywo z wyjątkiem kroku 11. <br/>Zamiast 10 minut Uruchom koder na żywo przez jeden tydzień lub dłużej. Użyj odtwarzacza, takiego jak [Azure Media Player](https://aka.ms/azuremediaplayer) , aby obserwować przesyłanie strumieniowe na żywo od czasu do czasu (lub zarchiwizowanego zasobu), aby upewnić się, że odtwarzanie nie ma widocznych błędów.

## <a name="email-your-recorded-settings"></a>Wyślij zapisane ustawienia pocztą e-mail

Na koniec Wyślij zapisane ustawienia i parametry Archiwum na żywo, aby Azure Media Services amshelp@microsoft.com powiadomienia, że wszystkie testy samoweryfikacji zostały zakończone pomyślnie. Podaj również informacje kontaktowe dla każdej z nich. Możesz skontaktować się z zespołem Azure Media Services, podając wszelkie pytania dotyczące tego procesu.

## <a name="see-also"></a>Zobacz też

[Przetestowane kodery lokalne](recommended-on-premises-live-encoders.md)

## <a name="next-steps"></a>Następne kroki

[Przesyłanie strumieniowe na żywo z Media Services v3](live-streaming-overview.md)
