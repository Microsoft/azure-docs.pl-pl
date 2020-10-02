---
title: Load Balancer Resetowanie i limit czasu bezczynności protokołu TCP na platformie Azure
titleSuffix: Azure Load Balancer
description: W tym artykule dowiesz się więcej o Azure Load Balancer z dwukierunkowym pakietem TCP RST przy przekroczeniu limitu czasu bezczynności.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/09/2019
ms.author: allensu
ms.openlocfilehash: f77dd21a2c017ee41f955fdf5e0848df190dec2a
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651279"
---
# <a name="load-balancer-tcp-reset-and-idle-timeout"></a>Load Balancer Resetowanie i limit czasu bezczynności protokołu TCP

Za pomocą [Usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md) można utworzyć bardziej przewidywalne zachowanie aplikacji dla Twoich scenariuszy przez włączenie resetowania protokołu TCP dla danej reguły. Domyślne zachowanie Load Balancer polega na dyskretnym porzucaniu przepływów, gdy zostanie osiągnięty limit czasu bezczynności przepływu.  Włączenie tej funkcji spowoduje, że Load Balancer wysyłać dwukierunkowe Resetowanie protokołu TCP (pakiety TCP RST) przy limicie czasu bezczynności.  Spowoduje to wyświetlenie punktów końcowych aplikacji, dla których upłynął limit czasu połączenia i nie będzie już można go używać.  Punkty końcowe mogą natychmiast ustanowić nowe połączenie, jeśli jest to możliwe.

![Load Balancer Resetowanie protokołu TCP](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
## <a name="tcp-reset"></a>Resetowanie protokołu TCP

Należy zmienić to zachowanie domyślne i włączyć opcję wysyłania resetowania protokołu TCP przy bezczynności, aby włączyć przychodzące reguły NAT, reguły równoważenia obciążenia i [reguły ruchu wychodzącego](https://aka.ms/lboutboundrules).  Po włączeniu dla każdej reguły, Load Balancer wyśle dwukierunkowe Resetowanie TCP (pakiety TCP RST) do punktów końcowych klienta i serwera w czasie bezczynności dla wszystkich zgodnych przepływów.

Punkty końcowe odbierające pakiety TCP RST zamykają odpowiednie gniazda natychmiast. Zapewnia to natychmiastowe powiadomienie do punktów końcowych, w których wystąpiło wydanie połączenia, i wszelka przyszła komunikacja z tym samym połączeniem TCP zakończy się niepowodzeniem.  Aplikacje mogą przeczyścić połączenia, gdy gniazdo zamknie i ponownie nawiąże połączenia w razie potrzeby bez oczekiwania na zakończenie limitu czasu połączenia TCP.

W przypadku wielu scenariuszy może to zmniejszyć konieczność wysłania aktywności protokołu TCP (lub warstwy aplikacji) w celu odświeżenia czasu bezczynności przepływu. 

Jeśli czasy trwania bezczynności przekraczają liczbę dozwolonych przez konfigurację lub aplikacja pokazuje niepożądane zachowanie z włączonym resetowaniem protokołu TCP, może być nadal konieczne użycie obsługi protokołu TCP (lub utrzymywania warstwy aplikacji) w celu monitorowania aktywności połączeń TCP.  Ponadto utrzymywanie aktywności może być również przydatne w przypadku, gdy połączenie jest nawiązywane z serwerem proxy w ścieżce, szczególnie w przypadku warstwy aplikacji.  

Uważnie Przeanalizuj cały kompleksowy scenariusz, aby zdecydować, czy można włączyć Resetowanie protokołu TCP, dostosować limit czasu bezczynności, a jeśli wymagane są dodatkowe kroki, aby zapewnić odpowiednie zachowanie aplikacji.

## <a name="configurable-tcp-idle-timeout"></a>Konfigurowalny limit czasu bezczynności TCP

Azure Load Balancer ma ustawienie limitu czasu bezczynności wynoszące 4 minuty do 120 minut. Wartość domyślna to 4 minuty. Jeśli okres braku aktywności jest dłuższy niż wartość limitu czasu, nie ma gwarancji, że sesja TCP lub HTTP jest utrzymywana między klientem a usługą w chmurze.

Gdy połączenie zostanie zamknięte, aplikacja kliencka może otrzymać następujący komunikat o błędzie: "Połączenie podstawowe zostało zamknięte: połączenie, które było oczekiwać aktywności, zostało zamknięte przez serwer".

Typowym zastosowaniem jest utrzymywanie aktywności protokołu TCP. Ta metoda utrzymuje, że połączenie jest aktywne przez dłuższy czas. Aby uzyskać więcej informacji, zobacz te [przykłady dla platformy .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Gdy włączona jest funkcja Keep-Alive, pakiety są wysyłane w trakcie okresów braku aktywności w ramach połączenia. Pakiety Keep-Alive zapewniają, że wartość limitu czasu bezczynności nie zostanie osiągnięta, a połączenie jest utrzymywane przez długi czas.

Ustawienie działa tylko dla połączeń przychodzących. Aby uniknąć utraty połączenia, skonfiguruj wartość "Keep-Alive TCP" z interwałem mniejszym niż ustawienie limitu czasu bezczynności lub Zwiększ limit czasu bezczynności. Aby można było obsługiwać te scenariusze, dodano obsługę limitu czasu bezczynności, który można skonfigurować.

Utrzymywanie aktywności TCP działa w scenariuszach, w których czas pracy baterii nie jest ograniczeniem. Nie jest to zalecane w przypadku aplikacji mobilnych. Korzystanie z funkcji utrzymywania połączenia TCP w aplikacji mobilnej umożliwia szybsze opróżnianie baterii urządzenia.


## <a name="limitations"></a>Ograniczenia

- Resetowanie protokołu TCP zostało wysłane tylko podczas połączenia TCP w stanie nawiązane.
- Resetowanie protokołu TCP nie jest wysyłane dla wewnętrznych modułów równoważenia obciążenia z skonfigurowanymi portami HA.
- Limit czasu bezczynności protokołu TCP nie ma wpływu na reguły równoważenia obciążenia w protokole UDP.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [Usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).
- Poznaj [reguły ruchu wychodzącego](load-balancer-outbound-rules-overview.md).
- [Skonfiguruj RST o limicie czasu bezczynności protokołu TCP](load-balancer-tcp-idle-timeout.md)
