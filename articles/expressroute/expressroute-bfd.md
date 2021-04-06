---
title: 'Azure ExpressRoute: Konfigurowanie BFD'
description: Ten artykuł zawiera instrukcje dotyczące konfigurowania BFD (wykrywanie ruchu dwukierunkowego) za pośrednictwem prywatnej komunikacji równorzędnej obwodu ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 254f5909e7ed8db4dc18ade2677a3213b268cf41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97511267"
---
# <a name="configure-bfd-over-expressroute"></a>Konfigurowanie BFD ponad ExpressRoute

ExpressRoute obsługuje wykrywanie dwukierunkowego przekazywania (BFD) zarówno w komunikacji równorzędnej, jak i za pośrednictwem firmy Microsoft. Po włączeniu BFD za pośrednictwem ExpressRoute można przyspieszyć wykrywanie awarii łącza między urządzeniami Microsoft Enterprise Edge (MSEE) i routerami, które są skonfigurowane dla obwodu ExpressRoute (CE/PE). Można skonfigurować ExpressRoute za pośrednictwem urządzeń routingu brzegowego lub urządzeń routingu brzegowego partnera (jeśli zaszło do zarządzanej usługi połączenia warstwy 3). W tym dokumencie przedstawiono potrzeby BFD oraz sposób włączania BFD przez ExpressRoute.

## <a name="need-for-bfd"></a>Potrzeba do BFD

Na poniższym diagramie przedstawiono korzyści płynące z włączenia BFD over ExpressRoute obwód: [![1]][1]

Obwód ExpressRoute można włączyć przez połączenia warstwy 2 lub zarządzane połączenia warstwy 3. W obu przypadkach, jeśli w ścieżce połączenia ExpressRoute istnieje więcej niż jedno urządzenie warstwy 2, odpowiedzialność za wykrywanie wszelkich błędów łączy w ścieżce leży w przypadku sesji BGP.

Na urządzeniach MSEE protokół BGP Keep-Alive i czas wstrzymania są zwykle konfigurowane odpowiednio do 60 i 180 sekund. Z tego powodu, gdy wystąpi awaria łącza, może upłynąć do trzech minut, aby wykryć awarię łącza i przełączyć ruch do alternatywnego połączenia.

Można kontrolować czasomierze protokołu BGP przez skonfigurowanie mniejszej aktywności protokołu BGP Keep-Alive i czasu wstrzymania na urządzeniu sieci równorzędnej brzegowej. Jeśli czasomierze protokołu BGP nie są takie same między dwoma urządzeniami równorzędnymi, sesja protokołu BGP zostanie ustanowiona przy użyciu wartości niższego czasu. Wartość utrzymywania protokołu BGP może być ustawiona jako niska jako trzy sekundy, a czas wstrzymania wynosi 10 sekund. Jednak ustawienie bardzo agresywnego czasomierza BGP nie jest zalecane, ponieważ protokół jest intensywnie przetwarzany.

W tym scenariuszu BFD może pomóc. BFD zapewnia słabo rozrzutne wykrywanie awarii linków w drugim przedziale czasu. 


## <a name="enabling-bfd"></a>Włączanie BFD

BFD jest domyślnie konfigurowana w ramach wszystkich nowo utworzonych interfejsów prywatnych komunikacji równorzędnej ExpressRoute na MSEE. W związku z tym, aby włączyć BFD, należy tylko skonfigurować BFD na urządzeniach podstawowych i dodatkowych. Konfigurowanie BFD jest procesem dwuetapowym. Należy skonfigurować BFD w interfejsie, a następnie połączyć go z sesją BGP.

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

Między elementami równorzędnymi BFD wolniejsze dwa elementy równorzędne określają szybkość transmisji. Interwały przesyłania/odbierania MSEE BFD są ustawione na 300 milisekund. W niektórych scenariuszach interwał może być ustawiony na wyższą wartość 750 milisekund. Konfigurując wyższą wartość, można wymusić, aby te interwały były dłuższe, ale nie można ich skrócić.

>[!NOTE]
>Jeśli skonfigurowano nadmiarowe obwody usługi ExpressRoute lub używasz połączenia sieci VPN typu lokacja-lokacja jako kopii zapasowej. Włączenie BFD może pomóc w szybszym przejściu w tryb failover po błędzie łączności ExpressRoute. 
>

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji i uzyskać pomoc, Skorzystaj z następujących linków:

- [Tworzenie i modyfikowanie obwodu usługi ExpressRoute][CreateCircuit]
- [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD przyspiesza czas odejmowania niepowodzeń konsolidacji"

<!--Link References-->
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ResetPeering]: ./expressroute-howto-reset-peering.md