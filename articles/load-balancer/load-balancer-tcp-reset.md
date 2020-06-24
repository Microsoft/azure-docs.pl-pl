---
title: Load Balancer Resetowanie protokołu TCP przy bezczynności na platformie Azure
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
ms.date: 05/03/2019
ms.author: allensu
ms.openlocfilehash: 68714053ac92faf8550a3e5f83a526afa1222971
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/16/2020
ms.locfileid: "84808475"
---
# <a name="load-balancer-with-tcp-reset-on-idle"></a>Usługa Load Balancer z resetowaniem protokołu TCP przy bezczynności

Za pomocą [Usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md) można utworzyć bardziej przewidywalne zachowanie aplikacji dla Twoich scenariuszy przez włączenie resetowania protokołu TCP dla danej reguły. Domyślne zachowanie Load Balancer polega na dyskretnym porzucaniu przepływów, gdy zostanie osiągnięty limit czasu bezczynności przepływu.  Włączenie tej funkcji spowoduje, że Load Balancer wysyłać dwukierunkowe Resetowanie protokołu TCP (pakiety TCP RST) przy limicie czasu bezczynności.  Spowoduje to wyświetlenie punktów końcowych aplikacji, dla których upłynął limit czasu połączenia i nie będzie już można go używać.  Punkty końcowe mogą natychmiast ustanowić nowe połączenie, jeśli jest to możliwe.

![Load Balancer Resetowanie protokołu TCP](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
Należy zmienić to zachowanie domyślne i włączyć opcję wysyłania resetowania protokołu TCP przy bezczynności, aby włączyć przychodzące reguły NAT, reguły równoważenia obciążenia i [reguły ruchu wychodzącego](https://aka.ms/lboutboundrules).  Po włączeniu dla każdej reguły, Load Balancer wyśle dwukierunkowe Resetowanie TCP (pakiety TCP RST) do punktów końcowych klienta i serwera w czasie bezczynności dla wszystkich zgodnych przepływów.

Punkty końcowe odbierające pakiety TCP RST zamykają odpowiednie gniazda natychmiast. Zapewnia to natychmiastowe powiadomienie do punktów końcowych, w których wystąpiło wydanie połączenia, i wszelka przyszła komunikacja z tym samym połączeniem TCP zakończy się niepowodzeniem.  Aplikacje mogą przeczyścić połączenia, gdy gniazdo zamknie i ponownie nawiąże połączenia w razie potrzeby bez oczekiwania na zakończenie limitu czasu połączenia TCP.

W przypadku wielu scenariuszy może to zmniejszyć konieczność wysłania aktywności protokołu TCP (lub warstwy aplikacji) w celu odświeżenia czasu bezczynności przepływu. 

Jeśli czasy trwania bezczynności przekraczają liczbę dozwolonych przez konfigurację lub aplikacja pokazuje niepożądane zachowanie z włączonym resetowaniem protokołu TCP, może być nadal konieczne użycie obsługi protokołu TCP (lub utrzymywania warstwy aplikacji) w celu monitorowania aktywności połączeń TCP.  Ponadto utrzymywanie aktywności może być również przydatne w przypadku, gdy połączenie jest nawiązywane z serwerem proxy w ścieżce, szczególnie w przypadku warstwy aplikacji.  

Uważnie Przeanalizuj cały kompleksowy scenariusz, aby zdecydować, czy można włączyć Resetowanie protokołu TCP, dostosować limit czasu bezczynności, a jeśli wymagane są dodatkowe kroki, aby zapewnić odpowiednie zachowanie aplikacji.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Włączanie resetowania protokołu TCP po upływie limitu czasu bezczynności

Korzystając z interfejsu API w wersji 2018-07-01, można włączyć wysyłanie dwukierunkowych resetów TCP przy bezczynności dla każdej reguły:

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="region-availability"></a><a name="regions"></a>Dostępność regionów

Dostępne we wszystkich regionach.

## <a name="limitations"></a>Ograniczenia

- Parametr RST jest wysyłany tylko podczas połączenia TCP w stanie USTANOWIONym.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [Usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).
- Poznaj [reguły ruchu wychodzącego](load-balancer-outbound-rules-overview.md).
- [Skonfiguruj RST o limicie czasu bezczynności protokołu TCP](load-balancer-tcp-idle-timeout.md)
