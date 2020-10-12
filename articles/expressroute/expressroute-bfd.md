---
title: 'Azure ExpressRoute: Konfigurowanie BFD'
description: Ten artykuł zawiera instrukcje dotyczące konfigurowania BFD (wykrywanie ruchu dwukierunkowego) za pośrednictwem prywatnej komunikacji równorzędnej obwodu ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 11/1/2018
ms.author: duau
ms.openlocfilehash: db2f45da0193ac648d58c0be9773f36e542ed917
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89397614"
---
# <a name="configure-bfd-over-expressroute"></a>Konfigurowanie BFD ponad ExpressRoute

ExpressRoute obsługuje wykrywanie dwukierunkowego przekazywania (BFD) zarówno w komunikacji równorzędnej, jak i za pośrednictwem firmy Microsoft. Włączenie funkcji BFD over ExpressRoute umożliwia przyspieszenie wykrywania awarii łącza między urządzeniami Microsoft Enterprise Edge (MSEE) i routerami, na których kończy się obwód ExpressRoute (CE/PE). Możesz przerwać ExpressRoute za pośrednictwem urządzeń routingu brzegowego klienta lub urządzeń routingu brzegowego partnera (jeśli zaszło do zarządzanej usługi połączenia warstwy 3). W tym dokumencie przedstawiono potrzeby BFD oraz sposób włączania BFD przez ExpressRoute.

## <a name="need-for-bfd"></a>Potrzeba do BFD

Na poniższym diagramie przedstawiono korzyści płynące z włączenia BFD over ExpressRoute obwód: [![1]][1]

Obwód ExpressRoute można włączyć przez połączenia warstwy 2 lub zarządzane połączenia warstwy 3. W obu przypadkach, jeśli w ścieżce połączenia ExpressRoute istnieje co najmniej jedno urządzenie warstwy 2, odpowiedzialność za wykrycie błędów łącza w ścieżce leży w przypadku użycia protokołu BGP.

Na urządzeniach z systemem MSEE czas utrzymywania aktywności i czasu wstrzymania BGP są zwykle konfigurowane odpowiednio do 60 i 180 sekund. W związku z tym po niepowodzeniu łącza może upłynąć nawet trzy minuty na wykrycie błędu łącza i przełączenie ruchu do alternatywnego połączenia.

Można kontrolować czasomierze protokołu BGP przez skonfigurowanie mniejszej aktywności protokołu BGP i czasu wstrzymania na urządzeniu równorzędnym dla klienta. Jeśli czasomierze protokołu BGP są niezgodne między dwoma urządzeniami równorzędnymi, sesja protokołu BGP między elementami równorzędnymi będzie używać niższej wartości czasomierza. Wartość utrzymywania aktywności BGP może być ustawiona na maksymalnie trzy sekundy i czas przechowywania w kolejności dziesiątek sekund. Jednak ustawienie czasomierzy protokołu BGP agresywnie jest mniej zalecane, ponieważ protokół jest intensywnie przetwarzany.

W tym scenariuszu BFD może pomóc. BFD zapewnia słabo rozrzutne wykrywanie awarii linków w drugim przedziale czasu. 


## <a name="enabling-bfd"></a>Włączanie BFD

BFD jest domyślnie konfigurowana w ramach wszystkich nowo utworzonych interfejsów prywatnych komunikacji równorzędnej ExpressRoute na MSEE. W związku z tym, aby włączyć BFD, należy skonfigurować BFD na serwerze rejestracji certyfikatów (zarówno na urządzeniach podstawowych, jak i dodatkowych). Konfigurowanie BFD jest procesem dwuetapowym: należy skonfigurować BFD w interfejsie, a następnie połączyć go z sesją BGP.

Poniżej przedstawiono przykład CE/PE (z zastosowaniem konfiguracji Cisco IOS XE). 

```console
interface TenGigabitEthernet2/0/0.150
   description private peering to Azure
   encapsulation dot1Q 15 second-dot1q 150
   ip vrf forwarding 15
   ip address 192.168.15.17 255.255.255.252
   bfd interval 300 min_rx 300 multiplier 3


router bgp 65020
   address-family ipv4 vrf 15
      network 10.1.15.0 mask 255.255.255.128
      neighbor 192.168.15.18 remote-as 12076
      neighbor 192.168.15.18 fall-over bfd
      neighbor 192.168.15.18 activate
      neighbor 192.168.15.18 soft-reconfiguration inbound
   exit-address-family
```

>[!NOTE]
>Aby włączyć BFD w ramach istniejącej prywatnej komunikacji równorzędnej; należy zresetować komunikację równorzędną. Zobacz [Resetowanie komunikacji równorzędnej ExpressRoute][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>Negocjowanie czasomierza BFD

Między elementami równorzędnymi BFD wolniejsze dwa elementy równorzędne określają szybkość transmisji. Interwały przesyłania/odbierania MSEE BFD są ustawione na 300 milisekund. W niektórych scenariuszach interwał może być ustawiony na wyższą wartość 750 milisekund. Konfigurując wyższe wartości, można wymusić dłuższe interwały. ale nie krótszy.

>[!NOTE]
>Jeśli skonfigurowano nadmiarowe obwody usługi ExpressRoute lub używasz połączenia sieci VPN typu lokacja-lokacja jako kopii zapasowej; włączenie BFD może pomóc w szybszym przejściu w tryb failover po błędzie łączności ExpressRoute. 
>

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji i uzyskać pomoc, Skorzystaj z następujących linków:

- [Tworzenie i modyfikowanie obwodu usługi ExpressRoute][CreateCircuit]
- [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD przyspiesza czas odejmowania niepowodzeń konsolidacji"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






