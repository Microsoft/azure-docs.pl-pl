---
title: 'Azure ExpressRoute: wymagania dotyczące jakości usług'
description: Ta strona zawiera szczegółowe wymagania dotyczące konfigurowania usług QoS i zarządzania nimi. Usługa Skype dla firm/usługi głosowe została omówiona.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: duau
ms.openlocfilehash: 871af5b767e8cfe10db3a0a1c15c47340e25581f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89395030"
---
# <a name="expressroute-qos-requirements"></a>Wymagania dotyczące technologii QoS w usłudze ExpressRoute
Program Skype dla firm obejmuje różne obciążenia, które wymagają zróżnicowanej obsługi w technologii QoS. Jeśli planujesz korzystać z usług głosowych za pośrednictwem usługi ExpressRoute, musisz spełnić opisane poniżej wymagania.

![Diagram przedstawiający usługi głosowe przechodzące przez ExpressRoute.](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> Wymagania dotyczące technologii QoS dotyczą tylko komunikacji równorzędnej firmy Microsoft. Wartości DSCP w ruchu sieciowym otrzymane w publicznej komunikacji równorzędnej Azure i prywatnej komunikacji równorzędnej Azure zostaną zresetowane do wartości 0. 
> 
> 

Poniższa tabela zawiera listę oznaczeń DSCP używanych przez usługi Microsoft Teams i Skype dla firm. Więcej informacji znajduje się w artykule [Managing QoS for Skype for Business](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/managing-quality-of-service-QoS) (Zarządzanie technologią QoS na potrzeby programu Skype dla firm).

| **Klasa ruchu** | **Obsługa (oznaczanie DSCP)** | **Obciążenia Microsoft Teams i Skype dla firm** |
| --- | --- | --- |
| **Połączenia głosowe** |EF (46) |Skype/Microsoft Teams/Lync Voice |
| **Interaktywne** |AF41 (34) |Wideo, VBSS |
| |AF21 (18) |Współdzielenie aplikacji | 
| **Wartooć** |AF11 (10) |Transfer plików |
| |CS0 (0) |Inne |

* Sklasyfikuj obciążenia i zaznacz odpowiednie wartości DSCP. Postępuj zgodnie ze wskazówkami podanymi [tutaj](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/configuring-port-ranges-for-your-skype-clients#configure-quality-of-service-policies-for-clients-running-on-windows-10) dotyczącymi ustawiania oznaczeń DSCP w sieci.
* Skonfiguruj i obsługuj wiele kolejek w technologii QoS w sieci. Głos musi być klasą autonomiczną i przyjmować traktowanie EF określone w [dokumencie RFC 3246](https://www.ietf.org/rfc/rfc3246.txt). 
* Możesz wybrać mechanizm kolejkowania, zasady wykrywania przeciążenia oraz alokację przepustowości na klasę ruchu. Oznaczanie DSCP dla obciążeń programu Skype dla firm musi jednak zostać zachowane. Jeśli używasz oznaczenia DSCP niewymienionego powyżej, np. AF31 (26), musisz zmodyfikować tę wartość DSCP do 0 przed wysłaniem pakietu do firmy Microsoft. Firma Microsoft wysyła tylko pakiety oznaczone wartościami DSCP pokazanymi w powyższej tabeli. 

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z wymaganiami dotyczącymi [routingu](expressroute-routing.md) i [translatora adresów sieciowych](expressroute-nat.md).
* Użyj następujących linków przydatnych podczas konfigurowania połączenia za pomocą usługi ExpressRoute.
  
  * [Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](expressroute-howto-circuit-classic.md)
  * [Configure routing (Konfigurowanie routingu)](expressroute-howto-routing-classic.md)
  * [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-classic.md)

