---
title: Informacje o projekcie VPN Gateway platformy Azure
description: Dowiedz się więcej na temat sposobów projektowania topologii bramy sieci VPN w celu nawiązania połączenia z sieciami wirtualnymi platformy Azure.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 61732f66aef58f5a9edcb9e095782e19e8aaffdd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397219"
---
# <a name="vpn-gateway-design"></a>Projekt VPN Gateway

Dostępne są różne konfiguracje połączeń bramy sieci VPN. Należy określić, która konfiguracja najlepiej odpowiada Twoim wymaganiom. W poniższych sekcjach można wyświetlić informacje o projekcie i diagramy topologii dotyczące następujących połączeń bramy sieci VPN. Przedstawione diagramy i opisy mogą ułatwić wybór topologii połączenia dostosowanej do potrzeb użytkownika. Na diagramach są wyświetlane podstawowe topologie linii bazowej, ale istnieje możliwość tworzenia bardziej złożonych konfiguracji przy użyciu diagramów jako wskazówek.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a><a name="s2smulti"></a>Połączenia typu lokacja-lokacja i połączenia obejmujące wiele lokacji (tunel VPN protokołu IPsec/IKE)

### <a name="site-to-site"></a><a name="S2S"></a>Lokacja-lokacja

Połączenie bramy sieci VPN typu lokacja-lokacja to połączenie nawiązywane za pośrednictwem tunelu sieci VPN wykorzystującego protokół IPsec/IKE (IKEv1 lub IKEv2). Z połączeń typu lokacja-lokacja (S2S) można korzystać w ramach konfiguracji hybrydowych i obejmujących wiele lokalizacji. Połączenie S2S wymaga, aby urządzenie sieci VPN znajdowało się w środowisku lokalnym, które ma przypisany publiczny adres IP. Aby uzyskać informacje o wybieraniu urządzenia VPN, zobacz [Brama VPN Gateway — często zadawane pytania dotyczące urządzeń VPN](vpn-gateway-vpn-faq.md#s2s).

![Przykład połączenia typu lokacja-lokacja w usłudze Azure VPN Gateway](./media/design/vpngateway-site-to-site-connection-diagram.png)

VPN Gateway można skonfigurować w trybie aktywny-gotowość przy użyciu jednego publicznego adresu IP lub w trybie aktywny-aktywny przy użyciu dwóch publicznych adresów IP. W trybie aktywny-Standby jeden tunel IPsec jest aktywny, a drugi tunel jest w stanie wstrzymania. W tej konfiguracji ruch przechodzi przez aktywny tunel, a jeśli jakiś problem występuje z tym tunelem, ruch jest przełączany do tunelu w stanie wstrzymania. Ustawienie VPN Gateway w trybie aktywny-aktywny jest *zalecane* , gdy tunele IPSec są jednocześnie aktywne, z danymi przepływanymi jednocześnie przez oba tunele. Dodatkową zaletą trybu Active-Active jest to, że klienci mogą uzyskać większą przepływność.

### <a name="multi-site"></a><a name="Multi"></a>Wiele lokacji

Ten typ połączenia jest odmianą połączenia typu lokacja-lokacja. W tym przypadku tworzysz więcej niż jedno połączenie VPN z bramy sieci wirtualnej — zwykle do nawiązywania połączenia z wieloma lokacjami lokalnymi. Podczas pracy z wieloma połączeniami musisz użyć sieci VPN typu RouteBased (nazywanego dynamiczną bramą w przypadku pracy z klasycznymi sieciami wirtualnymi). Ze względu na to, że każda sieć wirtualna może mieć tylko jedną bramę sieci VPN, wszystkie połączenia za pośrednictwem bramy współużytkują dostępną przepustowość. Ten typ połączenia jest często określany mianem połączenia „obejmującego wiele lokacji”.

![Przykład połączenia obejmującego wiele lokacji w usłudze Azure VPN Gateway](./media/design/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Modele wdrażania i metody nawiązywania połączeń typu lokacja-lokacja i połączeń obejmujących wiele lokacji

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="point-to-site-vpn"></a><a name="P2S"></a>Sieć VPN typu punkt-lokacja

Połączenie bramy VPN Gateway typu punkt-lokacja pozwala utworzyć bezpieczne połączenie z siecią wirtualną z indywidualnego komputera klienckiego. Połączenie typu punkt-lokacja jest ustanawiane przez uruchomienie z komputera klienckiego. To rozwiązanie jest przydatne dla osób pracujących zdalnie, które chcą łączyć się z sieciami wirtualnymi platformy Azure z lokalizacji zdalnej, na przykład z domu lub sali konferencyjnej. Połączenie sieci VPN typu punkt-lokacja jest również przydatne zamiast połączenia sieci VPN typu lokacja-lokacja w przypadku niewielkiej liczby klientów, którzy muszą się łączyć z siecią wirtualną.

W przeciwieństwie do połączeń S2S połączenia P2S nie wymagają lokalnego, publicznego adresu IP ani urządzenia sieci VPN. Połączenia typu punkt-lokacja mogą być używane z połączeniami typu lokacja-lokacja z użyciem tej samej bramy sieci VPN, pod warunkiem że wszystkie wymagania dotyczące konfiguracji dla obu połączeń są zgodne. Aby uzyskać więcej informacji na temat połączeń punkt-lokacja, zobacz [About Point-to-Site VPN](point-to-site-about.md) (Informacje o sieci VPN typu punkt-lokacja).

![Przykład połączenia typu punkt-lokacja w usłudze Azure VPN Gateway](./media/design/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>Modele wdrażania i metody nawiązywania połączeń typu punkt-lokacja

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="vnet-to-vnet-connections-ipsecike-vpn-tunnel"></a><a name="V2V"></a>Połączenia między sieciami wirtualnymi (tunel VPN protokołu IPsec/IKE)

Proces nawiązywania połączenia między dwiema sieciami wirtualnymi przebiega podobnie do procesu łączenia sieci wirtualnej z lokacją lokalną. Oba typy połączeń wykorzystują bramę sieci VPN, aby zapewnić bezpieczny tunel z użyciem protokołu IPsec/IKE. Można także łączyć połączenia między sieciami wirtualnymi z konfiguracjami połączeń obejmujących wiele lokacji. Pozwala to tworzyć topologie sieci, które łączą wdrożenia obejmujące wiele lokalizacji z połączeniami między sieciami wirtualnymi.

Sieci wirtualne, między którymi tworzone jest połączenie, mogą:

* znajdować się w tym samym lub różnych regionach,
* należeć do tej samej lub różnych subskrypcji, 
* korzystać z tego samego lub różnych modeli wdrażania.

![Przykład połączenia między sieciami wirtualnymi w usłudze Azure VPN Gateway](./media/design/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>Połączenia między modelami wdrażania

Platforma Azure ma obecnie dwa modele wdrażania: klasyczny model wdrażania oraz model wdrażania przy użyciu usługi Resource Manager. Jeśli korzystasz z platformy Azure od pewnego czasu, prawdopodobnie masz maszyny wirtualne i wystąpienia roli platformy Azure działające w klasycznej sieci wirtualnej. Nowsze maszyny wirtualne i wystąpienia roli mogą działać w sieci wirtualnej utworzonej w usłudze Resource Manager. Możesz utworzyć połączenie między tymi sieciami wirtualnymi, aby umożliwić zasobom w jednej sieci wirtualnej bezpośrednie komunikowanie się z zasobami w innej sieci.

### <a name="vnet-peering"></a>Komunikacja równorzędna sieci wirtualnych

Można utworzyć połączenie przy użyciu komunikacji równorzędnej sieci wirtualnych pod warunkiem, że sieć wirtualna spełnia określone wymagania. W przypadku komunikacji równorzędnej sieci wirtualnych nie jest używana brama sieci wirtualnej. Aby uzyskać więcej informacji, zobacz temat [Komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Modele wdrażania i metody nawiązywania połączeń między sieciami wirtualnymi

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="expressroute-private-connection"></a><a name="ExpressRoute"></a>ExpressRoute (połączenie prywatne)

Usługa ExpressRoute umożliwia rozszerzenie sieci lokalnych na chmurę firmy Microsoft za pośrednictwem połączenia prywatnego obsługiwanego przez dostawcę połączenia. Dzięki usłudze ExpressRoute można nawiązywać połączenia z usługami w chmurze firmy Microsoft, takimi jak Microsoft Azure, Microsoft 365 i CRM Online. Łączność może być z sieci typu dowolna-dowolna (IP VPN), sieci Ethernet typu punkt-punkt lub wirtualnego połączenia krzyżowego za pośrednictwem dostawcy łączności w ramach funkcji wspólnej lokalizacji.

Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Dzięki temu oferują one większą niezawodność i szybkość oraz mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet.

Połączenie usługi ExpressRoute używa bramy sieci wirtualnej w ramach wymaganej konfiguracji. W przypadku połączenia usługi ExpressRoute brama sieci wirtualnej jest konfigurowana z typem bramy „ExpressRoute” zamiast „Vpn”. Mimo iż ruch sieciowy przesyłany za pośrednictwem obwodu usługi ExpressRoute domyślnie nie jest szyfrowany, możliwe jest utworzenie rozwiązania umożliwiającego przesyłanie zaszyfrowanego ruchu sieciowego za pośrednictwem obwodu usługi ExpressRoute. Więcej informacji na temat usługi ExpressRoute zawiera artykuł [ExpressRoute technical overview](../expressroute/expressroute-introduction.md) (Opis techniczny usługi ExpressRoute).

## <a name="site-to-site-and-expressroute-coexisting-connections"></a><a name="coexisting"></a>Współistniejące połączenia typu lokacja-lokacja i ExpressRoute

ExpressRoute to bezpośrednie, prywatne połączenie z usługami firmy Microsoft, w tym z platformą Azure, nawiązane z poziomu sieci WAN użytkownika, a nie z publicznego Internetu. Zaszyfrowany ruch połączenia sieci VPN typu lokacja-lokacja jest przesyłany za pośrednictwem publicznej sieci Internet. Możliwość konfiguracji połączenia sieci VPN typu lokacja-lokacja oraz połączenia ExpressRoute dla tej samej sieci wirtualnej niesie ze sobą pewne korzyści.

Sieć VPN typu lokacja-lokacja może zostać skonfigurowana jako bezpieczna ścieżka trybu failover dla połączenia ExpressRoute. Połączenia sieci VPN typu lokacja-lokacja mogą także zostać użyte w celu połączenia się z lokacjami, które nie są częścią sieci, ale które są połączone metodą ExpressRoute. Ta konfiguracja wymaga dwóch bram sieci wirtualnej dla tej samej sieci wirtualnej, z których jedna używa bramy typu „Vpn”, a druga — bramy typu „ExpressRoute”.

![Przykład współistniejących połączeń usług ExpressRoute i VPN Gateway](./media/design/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>Modele wdrażania i metody nawiązywania połączeń typu lokacja-lokacja i połączeń usługi ExpressRoute współistnieją

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="highly-available-connections"></a><a name="highly-available"></a>Połączenia o wysokiej dostępności

Aby zaplanować i zaprojektować połączenia o wysokiej dostępności, zobacz [połączenia o wysokiej](vpn-gateway-highlyavailable.md)dostępności.

## <a name="next-steps"></a>Następne kroki

* Więcej informacji można znaleźć w temacie [Brama VPN Gateway — często zadawane pytania](vpn-gateway-vpn-faq.md).

* Dowiedz się więcej o [ustawieniach konfiguracji VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).

* Aby uzyskać VPN Gateway zagadnień dotyczących protokołu BGP, zobacz [Informacje o](vpn-gateway-bgp-overview.md)protokole BGP.

* Wyświetl [limity usług i subskrypcji](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

* Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.
