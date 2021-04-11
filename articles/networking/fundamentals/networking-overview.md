---
title: Omówienie usług sieciowych platformy Azure
description: Dowiedz się więcej na temat usług sieciowych na platformie Azure, w tym łączności, ochrony aplikacji, dostarczania aplikacji i usług monitorowania sieci.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: kumud
ms.openlocfilehash: 3145a52a6050272c01cd7ccb64afef0b89812753
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106170101"
---
# <a name="azure-networking-services-overview"></a>Omówienie usług sieciowych platformy Azure

Usługi sieciowe na platformie Azure zapewniają różne możliwości sieci, które mogą być używane razem lub oddzielnie. Kliknij dowolną z następujących funkcji, aby dowiedzieć się więcej na ich temat:
- [**Usługi łączności**](#connect): łączenie zasobów platformy Azure i zasobów lokalnych przy użyciu dowolnej lub kombinacji tych usług sieciowych na platformie azure — Virtual Network (VNET), wirtualnej sieci WAN, ExpressRoute, VPN Gateway, wirtualnej sieci NAT Gateway, Azure DNS, usługa komunikacji równorzędnej i Azure bastionu.
- [**Usługi ochrony aplikacji**](#protect): Chroń aplikacje przy użyciu dowolnej lub kombinacji tych usług sieciowych na platformie Azure — łącz prywatny, DDoS ochronę, zaporę, sieciowe grupy zabezpieczeń, zaporę aplikacji sieci Web i Virtual Network punkty końcowe.
- [**Usługi dostarczania aplikacji**](#deliver): dostarczanie aplikacji w sieci platformy Azure przy użyciu dowolnej lub kombinacji tych usług sieciowych na platformie azure — Content Delivery Network (CDN), usługi Azure Front-drzwiczk, Traffic Manager, Application Gateway, Analizator internetu i Load Balancer.
- [**Monitorowanie sieci**](#monitor): Monitoruj zasoby sieciowe przy użyciu dowolnej lub kombinacji tych usług sieciowych na platformie Azure — Network Watcher, monitor ExpressRoute, Azure monitor lub punkt dostępu do terminalu sieci wirtualnej (TAP).

## <a name="connectivity-services"></a><a name="connect"></a>Usługi łączności
 
W tej sekcji opisano usługi, które zapewniają łączność między zasobami platformy Azure, łącznością z siecią lokalną i zasobami platformy Azure oraz rozgałęzieniem do połączenia z gałęzią w ramach platformy Azure — Virtual Network (VNet), ExpressRoute, VPN Gateway, wirtualnej sieci WAN, bramy translatora adresów sieciowych, Azure DNS, usługi komunikacji równorzędnej platformy Azure i platformy Azure bastionu.


### <a name="virtual-network"></a><a name="vnet"></a>Sieć wirtualna

Usługa Azure Virtual Network (VNet) to podstawowy blok konstrukcyjny dla sieci prywatnej na platformie Azure. Możesz użyć sieci wirtualnych, aby:
- **Komunikacja między zasobami platformy Azure**: możesz wdrażać maszyny wirtualne oraz kilka innych typów zasobów platformy Azure w sieci wirtualnej, na przykład w środowiskach Azure App Service, usłudze Azure Kubernetes Service (AKS) i platformie Azure Virtual Machine Scale Sets. Aby wyświetlić kompletną listę zasobów platformy Azure, które można wdrożyć w sieci wirtualnej, zobacz [Integracja sieci wirtualnej z usługą](../../virtual-network/virtual-network-for-azure-services.md).
- **Komunikacja między** sobą: można połączyć sieci wirtualne ze sobą, włączając zasoby w dowolnej sieci wirtualnej, aby komunikować się ze sobą za pomocą komunikacji równorzędnej sieci wirtualnych. Łączone sieci wirtualne mogą znajdować się w tym samym regionie lub w różnych regionach świadczenia usługi Azure. Aby uzyskać więcej informacji, zobacz [wirtualne sieci równorzędne](../../virtual-network/virtual-network-peering-overview.md).
- **Komunikacja z Internetem**: Domyślnie wszystkie zasoby w sieci wirtualnej mogą komunikować się z Internetem. Z zasobem w ruchu przychodzącym możesz komunikować się przez przypisanie publicznego adresu IP lub publicznego modułu równoważenia obciążenia. Możesz również użyć [publicznych adresów IP](../../virtual-network/virtual-network-public-ip-address.md) lub [Load Balancer](../../load-balancer/load-balancer-overview.md) publicznych do zarządzania połączeniami wychodzącymi.
- **Komunikacja z sieciami lokalnymi**: można połączyć lokalne komputery i sieci z siecią wirtualną przy użyciu [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [ExpressRoute](../../expressroute/expressroute-introduction.md).

Aby uzyskać więcej informacji, zobacz [co to jest platforma Azure Virtual Network?](../../virtual-network/virtual-networks-overview.md).

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
Usługa ExpressRoute umożliwia rozbudowa sieci lokalnych do chmury firmy Microsoft przez połączenie prywatne przez dostawcę połączenia. To połączenie jest prywatne. Ruch nie przechodzi przez Internet. Dzięki usłudze ExpressRoute możesz ustanowić połączenia z usługami firmy Microsoft w chmurze, np. Microsoft Azure, Microsoft 365 i Dynamics 365.  Aby uzyskać więcej informacji, zobacz [co to jest ExpressRoute?](../../expressroute/expressroute-introduction.md).

:::image type="content" source="./media/networking-overview/expressroute-connection-overview.png" alt-text="Azure ExpressRoute" border="false":::

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
VPN Gateway ułatwia tworzenie szyfrowanych połączeń między różnymi lokalizacjami do sieci wirtualnej z lokalizacji lokalnych lub tworzenie szyfrowanych połączeń między sieci wirtualnych. Dostępne są różne konfiguracje połączeń VPN Gateway, takich jak lokacja, lokacja, punkt-lokacja lub Sieć wirtualna-sieć wirtualna.
Na poniższym diagramie przedstawiono wiele połączeń sieci VPN typu lokacja-lokacja z tą samą siecią wirtualną.

:::image type="content" source="./media/networking-overview/vpngateway-multisite-connection-diagram.png" alt-text="Połączenia VPN Gateway platformy Azure między lokacjami":::

Aby uzyskać więcej informacji na temat różnych typów połączeń sieci VPN, zobacz [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtual-wan"></a><a name="virtualwan"></a>Wirtualna sieć WAN
Wirtualna sieć WAN platformy Azure to usługa sieciowa, która zapewnia zoptymalizowaną i zautomatyzowaną łączność z gałęzią z usługami i i za pośrednictwem platformy Azure. Regiony platformy Azure służą jako centra, do których można połączyć gałęzie. Korzystając ze szkieletu platformy Azure, można również łączyć gałęzie i korzystać z połączeń między sieciami wirtualnymi. Wirtualna sieć WAN platformy Azure oferuje wiele usług łączności w chmurze platformy Azure, takich jak sieci VPN typu lokacja-lokacja, Sieć VPN typu punkt-lokacja w jednym interfejsie operacyjnym. Połączenie z usługą Azure sieci wirtualnych jest nawiązywane przy użyciu połączeń sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [co to jest Azure Virtual WAN?](../../virtual-wan/virtual-wan-about.md).

:::image type="content" source="./media/networking-overview/virtualwan1.png" alt-text="Diagram usługi Virtual WAN":::

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS to usługa hostingu dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury platformy Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure. Aby uzyskać więcej informacji, zobacz [co to jest Azure DNS?](../../dns/dns-overview.md).

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Usługa Azure bastionu to nowa w pełni oparta na platformie usługa PaaS, która jest dostarczana w ramach sieci wirtualnej. Zapewnia bezpieczną i bezproblemową łączność protokołu RDP/SSH z maszynami wirtualnymi bezpośrednio w Azure Portal za pośrednictwem protokołu TLS. Gdy łączysz się za pomocą usługi Azure Bastion, maszyny wirtualne nie muszą mieć publicznego adresu IP. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure bastionu?](../../bastion/bastion-overview.md).

:::image type="content" source="./media/networking-overview/architecture.png" alt-text="Architektura usługi Azure bastionu":::

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Brama translatora adresów sieciowych sieci wirtualnej
Virtual Network NAT (translator adresów sieciowych) upraszcza połączenia z Internetem tylko w ruchu wychodzącym dla sieci wirtualnych. W przypadku skonfigurowania w podsieci wszystkie połączenia wychodzące korzystają z określonych statycznych publicznych adresów IP. Łączność wychodząca jest możliwa bez usługi równoważenia obciążenia lub publicznych adresów IP podłączonych bezpośrednio do maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [co to jest brama translatora adresów sieciowych sieci wirtualnej?](../../virtual-network/nat-overview.md)

:::image type="content" source="./media/networking-overview/flow-map.png" alt-text="Brama translatora adresów sieciowych sieci wirtualnej":::

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a> Usługa komunikacji równorzędnej Azure
Usługa komunikacji równorzędnej platformy Azure ulepsza łączność klienta z usługami w chmurze firmy Microsoft, takimi jak Microsoft 365, Dynamics 365, oprogramowanie jako usługa (SaaS), Azure lub wszystkie usługi firmy Microsoft dostępne za pośrednictwem publicznej sieci Internet. Aby uzyskać więcej informacji, zobacz [co to jest usługa komunikacji równorzędnej Azure?](../../peering-service/about.md).

### <a name="azure-edge-zones"></a><a name="edge-zones"></a>Strefy usługi Azure Edge Zone

Strefa brzegowa platformy Azure to rodzina ofert od Microsoft Azure, która umożliwia użytkownikom przetwarzanie danych blisko użytkownika. Maszyny wirtualne, kontenery i inne wybrane usługi platformy Azure można wdrażać w strefach brzegowych, aby spełnić wymagania dotyczące małych opóźnień i wysokiej przepływności aplikacji.

### <a name="azure-orbital"></a><a name="orbital"></a>Orbita platformy Azure

Orbita platformy Azure to w pełni zarządzana stacja naziemna oparta na chmurze jako usługa, która umożliwia komunikowanie się ze swoimi kosmicznymi lub Constellations satelitami, danymi zbieranymi i pasmem, przetwarzaniem danych w chmurze, usług łańcucha z usługami platformy Azure w ramach unikatowych scenariuszy oraz generowaniem produktów dla klientów. Ten system jest oparty na globalnej infrastrukturze platformy Azure i globalnej sieci Fiber o małym opóźnieniu.

## <a name="application-protection-services"></a><a name="protect"></a>Usługi ochrony aplikacji

W tej sekcji opisano usługi sieciowe na platformie Azure, które ułatwiają ochronę zasobów sieciowych — ochrona aplikacji przy użyciu dowolnej lub kombinacji tych usług sieciowych w ramach platformy Azure-DDoS Protection, prywatnego linku, zapory, zapory aplikacji sieci Web, sieciowych grup zabezpieczeń i punktów końcowych usługi Virtual Network.

### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS Protection 
[Azure DDoS Protection](../../ddos-protection/manage-ddos-protection.md) zapewnia środki zaradcze względem najbardziej zaawansowanych zagrożeń DDoS. Usługa zapewnia ulepszone możliwości ograniczania DDoS dla aplikacji i zasobów wdrożonych w sieciach wirtualnych. Ponadto klienci korzystający z usługi Azure DDoS Protection mają dostęp do DDoSej szybkiej odpowiedzi, aby skontaktować się z ekspertami DDoS podczas aktywnego ataku.

:::image type="content" source="./media/networking-overview/ddos-protection.png" alt-text="DDoS Protection":::

### <a name="azure-private-link"></a><a name="privatelink"></a>Link prywatny platformy Azure
[Link prywatny platformy Azure](../../private-link/private-link-overview.md) umożliwia dostęp do usług Azure PaaS Services (na przykład Azure Storage i SQL Database) oraz hostowanych usług partnerów/partnerskich platformy Azure w ramach prywatnego punktu końcowego w sieci wirtualnej.
Ruch między siecią wirtualną a usługą porusza się w sieci szkieletowej firmy Microsoft. Ujawnienie usługi do publicznej sieci Internet nie jest już konieczne. Możesz utworzyć własną prywatną usługę linku w sieci wirtualnej i dostarczyć jej klientom.

:::image type="content" source="./media/networking-overview/private-endpoint.png" alt-text="Omówienie prywatnego punktu końcowego":::

### <a name="azure-firewall"></a><a name="firewall"></a>Azure Firewall
Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Korzystając z zapory platformy Azure, można centralnie tworzyć, wymuszać i rejestrować zasady łączności aplikacji i sieci w ramach subskrypcji i sieci wirtualnych. Usługa Azure Firewall korzysta ze statycznego publicznego adresu IP dla zasobów sieci wirtualnej, co umożliwia zewnętrznym zaporom identyfikowanie ruchu pochodzącego z sieci wirtualnej. 

Aby uzyskać więcej informacji na temat zapory platformy Azure, zobacz [dokumentację dotyczącą zapory platformy Azure](../../firewall/overview.md).

:::image type="content" source="./media/networking-overview/firewall-threat.png" alt-text="Omówienie zapory":::

### <a name="web-application-firewall"></a><a name="waf"></a>Web Application Firewall
[Zapora aplikacji sieci Web](../../web-application-firewall/overview.md) (WAF) platformy Azure zapewnia ochronę aplikacji sieci Web przed typowymi atakami sieci Web i lukami w zabezpieczeniach, takimi jak iniekcja SQL i wykonywanie skryptów między lokacjami. Usługa Azure WAF zapewnia ochronę poza Box od OWASP 10 najważniejszych luk w zabezpieczeniach za pośrednictwem reguł zarządzanych. Dodatkowo klienci mogą również konfigurować reguły niestandardowe, które są regułami zarządzanymi przez klienta, aby zapewnić dodatkową ochronę na podstawie źródłowego zakresu adresów IP, a także atrybuty żądania, takie jak nagłówki, pliki cookie, pola danych formularza lub parametry ciągu zapytania.

Klienci mogą wybrać wdrożenie [usługi Azure WAF z Application Gateway](../../web-application-firewall/ag/ag-overview.md) , która zapewnia ochronę regionalną jednostek w publicznej i prywatnej przestrzeni adresowej. Klienci mogą również wybrać wdrożenie [usługi Azure WAF z przednimi drzwiami](../../web-application-firewall/afds/afds-overview.md) , które zapewniają ochronę w granicach sieci z publicznymi punktami końcowymi.

:::image type="content" source="./media/networking-overview/waf-overview.png" alt-text="Web Application Firewall":::

### <a name="network-security-groups"></a><a name="nsg"></a>Sieciowe grupy zabezpieczeń
Ruch sieciowy przychodzący do zasobów platformy Azure i wychodzący z nich w sieci wirtualnej platformy Azure można filtrować za pomocą grupy zabezpieczeń sieci. Aby uzyskać więcej informacji, zobacz [sieciowe grupy zabezpieczeń](../../virtual-network/network-security-groups-overview.md).

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Punkty końcowe usługi
Punkty końcowe usługi sieci wirtualnej rozszerzają prywatną przestrzeń adresową i tożsamość sieci wirtualnej do usług platformy Azure za pośrednictwem bezpośredniego połączenia. Punkty końcowe umożliwiają zabezpieczanie krytycznych zasobów usługi platformy Azure tylko do sieci wirtualnych. Ruch z sieci wirtualnej do usługi platformy Azure zawsze pozostaje w sieci szkieletowej platformy Microsoft Azure. Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi sieci wirtualnej](../../virtual-network/virtual-network-service-endpoints-overview.md).

:::image type="content" source="./media/networking-overview/vnet-service-endpoints-overview.png" alt-text="Punkty końcowe usługi dla sieci wirtualnej":::

## <a name="application-delivery-services"></a><a name="deliver"></a>Usługi dostarczania aplikacji

W tej sekcji opisano usługi sieciowe na platformie Azure, które pomagają dostarczać aplikacje — Content Delivery Network, usługa Azure front-drzwi, Traffic Manager, Load Balancer i Application Gateway.

### <a name="content-delivery-network"></a><a name="cdn"></a>Content Delivery Network
Usługa Azure Content Delivery Network (CDN) oferuje deweloperom globalne rozwiązanie umożliwiające szybkie dostarczanie użytkownikom zawartości wymagającej wysokiej przepustowości przez zapisywanie zawartości w pamięci podręcznej w węzłach fizycznych strategicznie rozmieszczonych na całym świecie. Aby uzyskać więcej informacji na temat Azure CDN, zobacz [Azure Content Delivery Network](../../cdn/cdn-overview.md).

:::image type="content" source="./media/networking-overview/cdn-overview.png" alt-text="Azure CDN":::

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure Front Door Service
Usługa Azure Front Door Service umożliwia definiowanie i monitorowanie globalnego routingu ruchu internetowego oraz zarządzanie nim przez optymalizowanie pod kątem uzyskiwania najlepszej wydajności i natychmiastowego przechodzenia w tryb failover w celu zapewnienia wysokiej dostępności. Dzięki usłudze Front Door można przekształcić globalne usługi dla konsumentów (wiele regionów) i aplikacji w niezawodne, spersonalizowane, nowoczesne, wysokowydajne aplikacje, interfejsy API i zawartość, które dotrą do odbiorców globalnych za pomocą platformy Azure. Aby uzyskać więcej informacji, zobacz [zewnętrzne drzwi platformy Azure](../../frontdoor/front-door-overview.md).

:::image type="content" source="./media/networking-overview/front-door-visual-diagram.png" alt-text="Przegląd usługi front-drzwi":::

### <a name="traffic-manager"></a><a name="trafficmanager"></a>Traffic Manager

Usługa Azure Traffic Manager to oparty na systemie DNS moduł równoważenia obciążenia ruchu, który umożliwia optymalną dystrybucję ruchu do usług w wielu regionach platformy Azure na świecie, przy jednoczesnym zapewnieniu wysokiej dostępności i krótkiego czasu odpowiedzi. Traffic Manager zapewnia szereg metod routingu ruchu w celu dystrybucji ruchu, takiego jak priorytet, waga, wydajność, geograficzna, wiele wartości lub podsieć. Aby uzyskać więcej informacji o metodach routingu ruchu, zobacz [Traffic Manager metod routingu](../../traffic-manager/traffic-manager-routing-methods.md).

Na poniższym diagramie przedstawiono routing oparty na priorytetach punktów końcowych z Traffic Manager:

:::image type="content" source="./media/networking-overview/priority.png" alt-text="Usługa Azure Traffic Manager &quot;Priority&quot; — Metoda routingu":::

Aby uzyskać więcej informacji na temat Traffic Manager, zobacz [co to jest usługa Azure Traffic Manager?](../../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load Balancer
Azure Load Balancer w przypadku wszystkich protokołów UDP i TCP zapewnia wysoką wydajność i niskie opóźnienie warstwy 4. Służy do zarządzania połączeniami przychodzącymi i wychodzącymi. Można skonfigurować publiczne i wewnętrzne punkty końcowe ze zrównoważonym obciążeniem. Można zdefiniować reguły mapowania połączeń przychodzących do miejsc docelowych puli zaplecza przy użyciu opcji badania kondycji protokołów TCP i HTTP w celu zarządzania dostępnością usługi. Aby dowiedzieć się więcej na temat Load Balancer, przeczytaj artykuł [omówienie Load Balancer](../../load-balancer/load-balancer-overview.md) .

Na poniższej ilustracji przedstawiono wielowarstwową aplikację internetową, która wykorzystuje zarówno zewnętrzne, jak i wewnętrzne moduły równoważenia obciążenia:

:::image type="content" source="./media/networking-overview/load-balancer.png" alt-text="Przykład Azure Load Balancer":::

### <a name="application-gateway"></a><a name="applicationgateway"></a>Application Gateway
Azure Application Gateway to moduł równoważenia obciążenia ruchu internetowego, który umożliwia zarządzanie ruchem kierowanym do aplikacji internetowych. Jest to kontroler dostarczania aplikacji (ADC) jako usługa, oferując różne możliwości równoważenia obciążenia warstwy 7 dla aplikacji. Aby uzyskać więcej informacji, zobacz [co to jest platforma Azure Application Gateway?](../../application-gateway/overview.md).

Na poniższym diagramie przedstawiono routing oparty na ścieżce URL przy użyciu Application Gateway.

:::image type="content" source="./media/networking-overview/figure1-720.png" alt-text="Przykład Application Gateway":::

## <a name="network-monitoring-services"></a><a name="monitor"></a>Usługi monitorowania sieci
W tej sekcji opisano usługi sieciowe na platformie Azure, które ułatwiają monitorowanie zasobów sieciowych — Network Watcher, Azure Monitor sieci, monitora ExpressRoute, Azure Monitor i Virtual Network NACIŚNIĘCIe.

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
Azure Network Watcher to usługa, która udostępnia narzędzia umożliwiające monitorowanie, diagnozowanie, wyświetlanie metryk i włączanie lub wyłączanie dzienników zasobów w sieci wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [co to jest Network Watcher?](../../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

### <a name="azure-monitor-for-networks-preview"></a>Azure Monitor w wersji zapoznawczej sieci
Azure Monitor dla sieci zapewnia kompleksowy wgląd w kondycję i metryki dla wszystkich wdrożonych zasobów sieciowych, bez konieczności konfigurowania. Zapewnia również dostęp do funkcji monitorowania sieci, takich jak [monitor połączeń](../../network-watcher/connection-monitor-overview.md), [Rejestrowanie przepływów dla sieciowych grup zabezpieczeń](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)i [Analiza ruchu](../../network-watcher/traffic-analytics.md). Aby uzyskać więcej informacji, zobacz [Azure monitor dla sieci w wersji zapoznawczej](../../azure-monitor/insights/network-insights-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>Monitor ExpressRoute
Aby dowiedzieć się więcej na temat wyświetlania metryk obwodu ExpressRoute, dzienników zasobów i alertów, zobacz [ExpressRoute monitoring, Metrics and Alerts](../../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Usługa Azure Monitor maksymalizuje dostępność i wydajność aplikacji zapewniając kompleksowe rozwiązanie do zbierania i analizowania danych telemetrycznych ze środowisk chmurowych i lokalnych oraz podejmowania działań w oparciu o nie. Pomaga interpretować działanie aplikacji i proaktywnie identyfikuje problemy dotyczące aplikacji i zasobów, od których zależą. Aby uzyskać więcej informacji, zobacz [Azure monitor przegląd](../../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="virtual-network-tap"></a><a name="vnettap"></a>Virtual Network naciśnij pozycję
Usługa Azure Virtual Network TAP (punkt dostępu terminalu) umożliwia ciągłą przesyłanie strumieniowego ruchu sieciowego maszyny wirtualnej do narzędzia do zbierania pakietów sieciowych lub narzędzi analitycznych. Moduł zbierający lub narzędzie analityczne jest dostarczany przez partnera [wirtualnego urządzenia sieciowego](https://azure.microsoft.com/solutions/network-appliances/) .

Na poniższej ilustracji przedstawiono sposób działania narzędzia Virtual Network TAP:

:::image type="content" source="./media/networking-overview/virtual-network-tap-architecture.png" alt-text="Jak działa NACIŚNIĘCIe sieci wirtualnej":::

Aby uzyskać więcej informacji, zobacz [co to jest Virtual Network TAP](../../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Następne kroki

- Utwórz pierwszą sieć wirtualną i Połącz z nim kilka maszyn wirtualnych, wykonując kroki opisane w artykule [Tworzenie pierwszej sieci wirtualnej](../../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) .
- Połącz komputer z siecią wirtualną, wykonując kroki opisane w [artykule Konfigurowanie połączenia punkt-lokacja](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Równoważenie obciążenia ruchu internetowego do serwerów publicznych przez wykonanie kroków opisanych w artykule [Tworzenie modułu równoważenia obciążenia](../../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) dostępnego z Internetu.