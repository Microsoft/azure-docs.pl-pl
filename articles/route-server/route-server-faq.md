---
title: Często zadawane pytania dotyczące usługi Azure Route Server
description: Znajdź odpowiedzi na często zadawane pytania dotyczące usługi Azure Route Server.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 04/16/2021
ms.author: duau
ms.openlocfilehash: 0bbe16fb63a4546b4b4745df16074f6a4b0cb26b
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599540"
---
# <a name="azure-route-server-preview-faq"></a>Azure Route Server (wersja zapoznawcza) — często zadawane pytania

> [!IMPORTANT]
> Usługa Azure Route Server (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-is-azure-route-server"></a>Co to jest serwer usługi Azure Route Server?

Azure Route Server to w pełni zarządzana usługa, która umożliwia łatwe zarządzanie routingiem między wirtualnym urządzeniem sieciowym a siecią wirtualną.

### <a name="is-azure-route-server-just-a-vm"></a>Czy usługa Azure Route Server jest po prostu maszyną wirtualną?

Nie. Azure Route Server to usługa zaprojektowana z myślą o wysokiej dostępności. Jeśli jest on wdrożony w regionie świadczenia usługi Azure, który [obsługuje](../availability-zones/az-overview.md)Strefy dostępności , będzie mieć nadmiarowość na poziomie strefy.

### <a name="how-many-route-servers-can-i-create-in-a-virtual-network"></a>Ile serwerów tras można utworzyć w sieci wirtualnej?

W sieci wirtualnej można utworzyć tylko jeden serwer tras. Należy ją wdrożyć w wyznaczonej podsieci o nazwie *RouteServerSubnet.*

### <a name="does-azure-route-server-support-vnet-peering"></a>Czy usługa Azure Route Server obsługuje wirtualne sieci równorzędne?

Tak. W przypadku połączenia równorzędnego sieci wirtualnej hostowania serwera usługi Azure Route Server z inną siecią wirtualną i włączenia funkcji Użyj bramy zdalnej w drugiej sieci wirtualnej serwer usługi Azure Route Server nauczy się przestrzeni adresowych tej sieci wirtualnej i wyśle je do wszystkich równorzędnych urządzeń WUS. Programuje również trasy z urządzeń WUS do tabeli routingu maszyn wirtualnych w równorzędnej sieci wirtualnej. 


### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Jakie protokoły routingu obsługuje usługa Azure Route Server?

Usługa Azure Route Server obsługuje Border Gateway Protocol (BGP). Urządzenie WUS musi obsługiwać zewnętrzny protokół BGP z wieloma przeskoków, ponieważ należy wdrożyć serwer usługi Azure Route Server w dedykowanej podsieci w sieci wirtualnej. Nazwa [ASN,](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) który wybierzesz, musi różnić się od tej używanej przez usługę Azure Route Server podczas konfigurowania protokołu BGP na węzłym WUS.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Czy usługa Azure Route Server przekieruje ruch danych między moim urządzeniem WUS i moimi maszynami wirtualnych?

Nie. Serwer usługi Azure Route Server wymienia tylko trasy protokołu BGP z urządzeniem WUS. Ruch danych przechodzi bezpośrednio z urządzenia WUS do docelowej maszyny wirtualnej i bezpośrednio z maszyny wirtualnej do urządzenia WUS.

### <a name="does-azure-route-server-store-customer-data"></a>Czy w usłudze Azure Route Server są przechowywane dane klientów?
Nie. Serwer usługi Azure Route Server wymienia tylko trasy protokołu BGP z urządzeniem WUS, a następnie propaguje je do sieci wirtualnej.

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-how-does-it-handle-them"></a>Jeśli serwer usługi Azure Route Server odbiera tę samą trasę z więcej niż jednego urządzenia WUS, jak je obsługuje?

Jeśli trasa ma taką samą długość ścieżki AS, serwer usługi Azure Route Server będzie programować wiele kopii trasy, z których każda ma inny następny przeskok, do maszyn wirtualnych w sieci wirtualnej. Gdy maszyny wirtualne wysyłają ruch do miejsca docelowego tej trasy, hosty maszyn wirtualnych będą Equal-Cost routingiem wielokanałowym (ECMP). Jeśli jednak jedno urządzenie WUS wysyła trasę z krótszą długością ścieżki AS niż inne urządzenia WUS, serwer usługi Azure Route Server zaprogramowa tylko trasę, która ma następny przeskok ustawiony na to urządzenie WUS, do maszyn wirtualnych w sieci wirtualnej.

### <a name="does-azure-route-server-preserve-the-bgp-communities-of-the-route-it-receives"></a>Czy serwer usługi Azure Route Server zachowuje społeczności BGP o odbieranych trasach?

Tak, serwer usługi Azure Route Server propaguje trasę za pomocą społeczności BGP bez zmian.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>Jakich numerów systemu autonomicznego (ASN) mogę użyć?

Możesz użyć własnych publicznych lub prywatnych sieci ASN w wirtualnym urządzeniu sieciowym. Nie można używać zakresów zarezerwowanych przez platformę Azure lub IANA.
Następujące sieci ASN są zarezerwowane przez platformę Azure lub IANA:

* Usługi ASN zarezerwowane przez platformę Azure:
    * Publiczne numery ASN: 8074, 8075, 12076
    * Prywatne numery ASN: 65515, 65517, 65518, 65519, 65520
* Sieci ASN [zarezerwowane przez IANA:](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>Czy mogę używać 32-bitowych (4-bajtowych) asnów?

Nie, serwer usługi Azure Route Server obsługuje tylko 16-bitowe (2 bajty) usługi ASN.

## <a name="route-server-limits"></a><a name = "limitations"></a>Limity serwera tras

Serwer usługi Azure Route Server ma następujące limity (na wdrożenie).

| Zasób | Limit |
|----------|-------|
| Liczba obsługiwanych elementów równorzędnych BGP | 8 |
| Liczba tras, które każdy elementu równorzędnego protokołu BGP może anonsować do usługi Azure Route Server | 200 |
| Liczba tras, które serwer usługi Azure Route Server może anonsować do usługi ExpressRoute lub bramy sieci VPN | 200 |

Jeśli urządzenie WUS anonsuje więcej tras niż wynosi limit, sesja protokołu BGP zostanie porzucona. W takim przypadku w przypadku bramy i serwera usługi Azure Route Server utracisz łączność z sieci lokalnej do platformy Azure. Aby uzyskać więcej informacji, zobacz [Diagnose an Azure virtual machine routing problem (Diagnozowanie problemu z routingiem maszyn wirtualnych platformy Azure).](../virtual-network/diagnose-network-routing-problem.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [skonfigurować usługę Azure Route Server.](quickstart-configure-route-server-powershell.md)
