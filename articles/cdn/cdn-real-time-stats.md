---
title: Statystyka w czasie rzeczywistym w Azure CDN | Microsoft Docs
description: Statystyki w czasie rzeczywistym zawierają dane czasu rzeczywistego dotyczące wydajności Azure CDN podczas dostarczania zawartości do klientów.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 3af2e849aa6658e539b0b5bdbda4428cc28e5ce5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84887230"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Statystyki w czasie rzeczywistym w usłudze Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Omówienie
W tym dokumencie wyjaśniono statystyki w czasie rzeczywistym w Microsoft Azure sieci CDN.  Ta funkcja udostępnia dane w czasie rzeczywistym, takie jak przepustowość, Stany pamięci podręcznej i współbieżne połączenia z profilem CDN podczas dostarczania zawartości do klientów. Pozwala to na ciągłe monitorowanie kondycji usługi w dowolnym momencie, w tym wydarzeń na żywo.

Dostępne są następujące wykresy:

* [Przepustowość](#bandwidth)
* [Kody stanu](#status-codes)
* [Stany pamięci podręcznej](#cache-statuses)
* [Połączenia](#connections)

## <a name="accessing-real-time-stats"></a>Uzyskiwanie dostępu do statystyk w czasie rzeczywistym
1. W [witrynie Azure Portal](https://portal.azure.com)przejdź do swojego profilu CDN.
   
    ![Blok profilu CDN](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. W bloku profil usługi CDN kliknij przycisk **Zarządzaj** .
   
    ![Przycisk zarządzania bloku profilu usługi CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    Zostanie otwarty portal zarządzania sieci CDN.
3. Umieść kursor na karcie **Analiza** , a następnie umieść wskaźnik myszy na wyskakującym menu **statystyk w czasie rzeczywistym** .  Kliknij opcję **duży obiekt http**.
   
    ![Portal zarządzania CDN](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Wyświetlane są wykresy statystyk w czasie rzeczywistym.

Na każdym z wykresów są wyświetlane statystyki w czasie rzeczywistym dla wybranego przedziału czasu, rozpoczynając od ładowania strony.  Wykresy są aktualizowane automatycznie co kilka sekund.  Przycisk **Odśwież wykres** , jeśli jest obecny, spowoduje wyczyszczenie grafu, po którym będą wyświetlane tylko wybrane dane.

## <a name="bandwidth"></a>Przepustowość
![Wykres przepustowości](./media/cdn-real-time-stats/cdn-bandwidth.png)

Wykres **przepustowości** przedstawia ilość przepustowości używanej przez bieżącą platformę w wybranym przedziale czasu. Zacieniona część wykresu wskazuje użycie przepustowości. Dokładna ilość aktualnie używanej przepustowości jest wyświetlana bezpośrednio pod wykresem liniowym.

## <a name="status-codes"></a>Kody stanu
![Wykres kodów stanu](./media/cdn-real-time-stats/cdn-status-codes.png)

Wykres **kodów stanu** wskazuje, jak często pewne kody odpowiedzi HTTP występują w wybranym przedziale czasu.

> [!TIP]
> Aby uzyskać opis każdej opcji kodu stanu HTTP, zobacz [Azure CDN kodów stanu HTTP](/previous-versions/azure/mt759238(v=azure.100)).
> 
> 

Lista kodów stanu HTTP jest wyświetlana bezpośrednio powyżej grafu. Ta lista wskazuje każdy kod stanu, który może być uwzględniony w grafie liniowym oraz bieżącą liczbę wystąpień na sekundę dla tego kodu stanu. Domyślnie dla każdego z tych kodów stanu w grafie jest wyświetlany wiersz. Można jednak wybrać opcję monitorowania kodów stanu o specjalnym znaczeniu dla konfiguracji sieci CDN. W tym celu sprawdź żądane kody stanu i wyczyść wszystkie inne opcje, a następnie kliknij przycisk **Odśwież Graf**. 

Można tymczasowo ukryć zarejestrowane dane dla określonego kodu stanu.  W legendzie bezpośrednio poniżej wykresu kliknij kod stanu, który chcesz ukryć. Kod stanu zostanie natychmiast ukryty z grafu. Kliknięcie tego kodu stanu spowoduje ponowne wyświetlenie tej opcji.

## <a name="cache-statuses"></a>Stany pamięci podręcznej
![Wykres Stanów pamięci podręcznej](./media/cdn-real-time-stats/cdn-cache-status.png)

Wykres **Stanów pamięci podręcznej** wskazuje, jak często niektóre typy stanów pamięci podręcznej są wykonywane w wybranym przedziale czasu. 

> [!TIP]
> Aby uzyskać opis każdej opcji kodu stanu pamięci podręcznej, zobacz [Azure CDN kodów stanu pamięci podręcznej](/previous-versions/azure/mt759237(v=azure.100)).
> 
> 

Lista kodów stanu pamięci podręcznej jest wyświetlana bezpośrednio powyżej grafu. Ta lista wskazuje każdy kod stanu, który może być uwzględniony w grafie liniowym oraz bieżącą liczbę wystąpień na sekundę dla tego kodu stanu. Domyślnie dla każdego z tych kodów stanu w grafie jest wyświetlany wiersz. Można jednak wybrać opcję monitorowania kodów stanu o specjalnym znaczeniu dla konfiguracji sieci CDN. W tym celu sprawdź żądane kody stanu i wyczyść wszystkie inne opcje, a następnie kliknij przycisk **Odśwież Graf**. 

Można tymczasowo ukryć zarejestrowane dane dla określonego kodu stanu.  W legendzie bezpośrednio poniżej wykresu kliknij kod stanu, który chcesz ukryć. Kod stanu zostanie natychmiast ukryty z grafu. Kliknięcie tego kodu stanu spowoduje ponowne wyświetlenie tej opcji.

## <a name="connections"></a>Połączenia
![Wykres połączeń](./media/cdn-real-time-stats/cdn-connections.png)

Ten wykres wskazuje liczbę połączeń ustanowionych na serwerach brzegowych. Każde żądanie dotyczące zasobu, który przechodzi przez naszą sieć CDN, spowoduje połączenie.

## <a name="next-steps"></a>Następne kroki
* Otrzymuj powiadomienia dotyczące [alertów w czasie rzeczywistym w Azure CDN](cdn-real-time-alerts.md)
* Dig bardziej [Zaawansowane raporty http](cdn-advanced-http-reports.md)
* Analizowanie [wzorców użycia](cdn-analyze-usage-patterns.md)

