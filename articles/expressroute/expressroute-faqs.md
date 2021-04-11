---
title: Często zadawane pytania — Azure ExpressRoute | Microsoft Docs
description: Często zadawane pytania ExpressRoute zawierają informacje o obsługiwanych usługach platformy Azure, kosztach, danych i połączeniach, umowie SLA, dostawcach i lokalizacjach, przepustowości i innych szczegółach technicznych.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: duau
ms.openlocfilehash: efa5c3192ca6f51c219cc308a776e6db7212103c
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552244"
---
# <a name="expressroute-faq"></a>Usługa ExpressRoute — często zadawane pytania

## <a name="what-is-expressroute"></a>Co to jest usługa ExpressRoute?

ExpressRoute to usługa platformy Azure, która umożliwia tworzenie prywatnych połączeń między centrami danych firmy Microsoft i infrastrukturą lokalną lub międzylokalizacyjną. Połączenia ExpressRoute nie przechodzą przez publiczny Internet i oferują wyższe opóźnienia niż typowe połączenia przez Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Jakie korzyści wynikają z używania ExpressRoute i prywatnych połączeń sieciowych?

Połączenia usługi ExpressRoute nie korzystają z publicznego Internetu. Oferują one wyższy poziom bezpieczeństwa, niezawodność i szybkość oraz krótsze i spójne opóźnienia niż typowe połączenia przez Internet. W niektórych przypadkach używanie połączeń ExpressRoute do przesyłania danych między urządzeniami lokalnymi i platformą Azure może przynieść znaczący koszt.

### <a name="where-is-the-service-available"></a>Gdzie jest dostępna usługa?

Zobacz Tę stronę, aby poznać lokalizację i dostępność usługi: [ExpressRoute partnerzy i lokalizacje](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Jak mogę używać ExpressRoute do łączenia się z firmą Microsoft, jeśli nie mam partnerstwa z jednym z partnerów ExpressRoute-przewoźnika?

Do jednej z obsługiwanych lokalizacji dostawcy programu Exchange można wybrać przewoźników regionalnych i sieci Ethernet. Następnie można nawiązać komunikację równorzędną z firmą Microsoft w lokalizacji dostawcy. Zapoznaj się z ostatnią sekcją [partnerów i lokalizacji ExpressRoute](expressroute-locations.md) , aby sprawdzić, czy dostawca usług znajduje się w dowolnej lokalizacji programu Exchange. Następnie można zamówić obwód usługi ExpressRoute za pomocą dostawcy usług w celu nawiązania połączenia z platformą Azure.

### <a name="how-much-does-expressroute-cost"></a>Ile kosztuje ExpressRoute?

Sprawdź [szczegóły cennika](https://azure.microsoft.com/pricing/details/expressroute/) , aby uzyskać informacje o cenach.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-this-bandwidth-allocated-for-ingress-and-egress-traffic-separately"></a>Czy jeśli płacisz za obwód usługi ExpressRoute danej przepustowości, czy ta przepustowość jest przypisana osobno dla ruchu przychodzącego i wychodzącego?

Tak, przepustowość obwodu ExpressRoute jest dwukierunkowa. Na przykład jeśli zakupiono obwód ExpressRoute z 200 MB/s, nastąpi zaplanowanie 200 MB/s dla ruchu przychodzącego i 200 MB/s dla ruchu wychodzącego.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-private-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Czy jeśli płacisz za obwód usługi ExpressRoute danej przepustowości, czy połączenie prywatne zostało zakupione od usługodawcy sieciowego, musi mieć taką samą szybkość?

Nie. Możesz zakupić prywatne połączenie z dowolną szybkością od usługodawcy. Jednak połączenie z platformą Azure jest ograniczone do zakupionej przepustowości obwodu usługi ExpressRoute.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-use-more-than-my-procured-bandwidth"></a>Czy jeśli płacisz za obwód usługi ExpressRoute o danej przepustowości, czy mogę korzystać z większej przepustowości?

Tak, można użyć maksymalnie dwa razy ograniczenia przepustowości, korzystając z przepustowości dostępnej na pomocniczym połączeniu obwodu usługi ExpressRoute. Wbudowana nadmiarowość obwodu jest konfigurowana przy użyciu podstawowych i pomocniczych połączeń, a każda z nich jest przystosowana do dwóch routerów brzegowych firmy Microsoft (MSEE). Przepustowość dostępna za pomocą połączenia pomocniczego może być używana w przypadku dodatkowego ruchu w razie potrzeby. Ponieważ połączenie pomocnicze jest przeznaczone do nadmiarowości, ale nie jest gwarantowane i nie powinno być używane dla dodatkowego ruchu przez dłuższy czas. Aby dowiedzieć się więcej o tym, jak używać obu połączeń do przesyłania ruchu, zobacz [Używanie as Path w oczekiwany](./expressroute-optimize-routing.md#solution-use-as-path-prepending)sposób.

Jeśli planujesz używać tylko połączenia podstawowego do przesyłania ruchu, przepustowość połączenia jest stała i próba jego anulowania spowoduje zwiększenie liczby porzucanych pakietów. Jeśli ruch przepływów przez bramę ExpressRoute, przepustowość dla jednostki SKU bramy jest stała i nie można jej przełączać. Aby uzyskać przepustowość poszczególnych jednostek SKU bramy, zobacz [Informacje o bramach sieci wirtualnej ExpressRoute](expressroute-about-virtual-network-gateways.md#aggthroughput).

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Czy można jednocześnie używać tego samego połączenia sieci prywatnej z siecią wirtualną i innymi usługami platformy Azure?

Tak. Obwód usługi ExpressRoute, po skonfigurowaniu, umożliwia dostęp do usług w sieci wirtualnej i w innych usługach platformy Azure jednocześnie. Nawiąż połączenie z sieciami wirtualnymi za pośrednictwem prywatnej ścieżki komunikacji równorzędnej i innych usług za pośrednictwem ścieżki komunikacji równorzędnej firmy Microsoft.

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>Jak sieci wirtualnych są anonsowane w prywatnej komunikacji równorzędnej ExpressRoute?

Brama ExpressRoute będzie ogłaszać *przestrzenie adresowe* sieci wirtualnej platformy Azure, nie można uwzględnić/wykluczyć na poziomie podsieci. Jest to zawsze anonsowana przestrzeń adresowa sieci wirtualnej. Ponadto jeśli używana jest Komunikacja równorzędna sieci wirtualnych, a w równorzędnej sieci wirtualnej jest włączona funkcja "Użyj bramy zdalnej", przestrzeń adresowa równorzędnej sieci wirtualnej również będzie anonsowana.

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>Ile prefiksów może być anonsowanych z sieci wirtualnej do lokalnej komunikacji równorzędnej ExpressRoute?

Istnieje maksymalnie 1000 prefiksów anonsowanych w pojedynczym połączeniu ExpressRoute lub za pośrednictwem komunikacji równorzędnej sieci wirtualnej przy użyciu funkcji tranzytu bramy. Na przykład jeśli w jednej sieci wirtualnej podłączonej do obwodu usługi ExpressRoute znajdują się przestrzenie adresowe 999, wszystkie 999 te prefiksy będą anonsowane w środowisku lokalnym. Alternatywnie, jeśli masz włączoną sieć wirtualną zezwalającą na tranzyt bramy z 1 przestrzenią adresową i 500 szprych sieci wirtualnych z włączoną opcją "Zezwalaj na bramę zdalną", Sieć wirtualna wdrożona z bramą będzie ogłaszać prefiksy 501 w środowisku lokalnym.

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>Co się stanie w przypadku przekroczenia limitu prefiksu dla połączenia ExpressRoute?

Połączenie między obwodem usługi ExpressRoute a bramą (i sieci wirtualnych komunikacji równorzędnej przy użyciu tranzytu bramy, jeśli ma zastosowanie) spowoduje przejście do trybu. Zostanie on ponownie ustanowiony, gdy limit prefiksu nie zostanie przekroczony.  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>Czy można filtrować trasy pochodzące z sieci lokalnej?

Jedynym sposobem filtrowania/uwzględniania tras jest lokalny router brzegowy. Trasy zdefiniowane przez użytkownika można dodać w sieci wirtualnej, aby mieć wpływ na określony Routing, ale będzie to statyczne i nie jest częścią anonsu protokołu BGP.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Czy ExpressRoute oferuje Umowa dotycząca poziomu usług (SLA)?

Aby uzyskać więcej informacji, zobacz stronę [umów SLA ExpressRoute](https://azure.microsoft.com/support/legal/sla/) .

## <a name="supported-services"></a>Obsługiwane usługi

ExpressRoute obsługuje [trzy domeny routingu](expressroute-circuit-peerings.md) dla różnych typów usług: prywatna Komunikacja równorzędna, Komunikacja równorzędna firmy Microsoft i publiczna Komunikacja równorzędna (przestarzałe).

### <a name="private-peering"></a>Prywatna komunikacja równorzędna

**Obsługiwał**

* Sieci wirtualne, w tym wszystkie maszyny wirtualne i usługi w chmurze

### <a name="microsoft-peering"></a>Komunikacja równorzędna firmy Microsoft

Jeśli obwód usługi ExpressRoute jest włączony dla komunikacji równorzędnej Azure Microsoft, możesz uzyskać dostęp do [zakresów publicznych adresów IP](../virtual-network/public-ip-addresses.md#public-ip-addresses) używanych na platformie Azure w ramach obwodu. Komunikacja równorzędna Azure firmy Microsoft zapewnia dostęp do usług hostowanych obecnie na platformie Azure (z ograniczeniami geograficznymi w zależności od jednostki SKU obwodu). Aby sprawdzić dostępność określonej usługi, możesz sprawdzić dokumentację tej usługi, aby sprawdzić, czy istnieje zastrzeżony zakres opublikowany dla tej usługi. Następnie należy odszukać zakresy adresów IP usługi docelowej i porównać z zakresami wymienionymi w obszarze [zakresy adresów IP platformy Azure i Tagi usług — plik XML chmury publicznej](https://www.microsoft.com/download/details.aspx?id=56519). Możesz również otworzyć bilet pomocy technicznej dla omawianej usługi, aby uzyskać wyjaśnienie.

**Obsługiwał**

* [Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* Power BI — dostępne za pośrednictwem społeczności regionalnej platformy Azure, zobacz [tutaj](/power-bi/service-admin-where-is-my-tenant-located) , jak sprawdzić region dzierżawy Power BI.
* Usługa Azure Active Directory
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (społeczność usług globalnych platformy Azure)
* Publiczne adresy IP platformy Azure dla IaaS (Virtual Machines, bram Virtual Network, modułów równoważenia obciążenia itp.)  
* Obsługiwane są również większość innych usług platformy Azure. Bezpośrednio Sprawdź usługę, która ma zostać użyta do zweryfikowania pomocy technicznej.

**Nieobsługiwane:**

* CDN
* Azure Front Door
* [Windows Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/)
* Serwer uwierzytelniania wieloskładnikowego (starsza wersja)
* Traffic Manager
* Logic Apps

### <a name="public-peering"></a>Publiczna komunikacja równorzędna

Publiczna Komunikacja równorzędna została wyłączona w nowych obwodach usługi ExpressRoute. Usługi platformy Azure są teraz dostępne w komunikacji równorzędnej firmy Microsoft. Jeśli obwód, który został utworzony przed przestarzałą publiczną komunikację równorzędną, można wybrać opcję użycia komunikacji równorzędnej firmy Microsoft lub publicznej komunikacji równorzędnej w zależności od wybranych usług.

Aby uzyskać więcej informacji i kroków konfiguracji dla publicznej komunikacji równorzędnej, zobacz [ExpressRoute publicznej komunikacji równorzędnej](about-public-peering.md).

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Dlaczego podczas konfigurowania komunikacji równorzędnej firmy Microsoft jest wyświetlany stan "anonsowane publiczne prefiksy"?

Firma Microsoft sprawdza, czy określone "anonsowane publiczne prefiksy" i "peer ASN" (lub "ASN klienta") są przypisane do Ciebie w rejestrze routingu internetowego. Jeśli otrzymujesz publiczne prefiksy z innej jednostki i jeśli przypisanie nie zostanie zarejestrowane w rejestrze routingu, automatyczne sprawdzanie poprawności nie zostanie ukończone i będzie wymagać ręcznej walidacji. Jeśli automatyczne sprawdzanie poprawności nie powiedzie się, zostanie wyświetlony komunikat "weryfikacja nie jest konieczna".

Jeśli zobaczysz komunikat "wymagana Walidacja", Zbierz dokumenty, które pokazują, że publiczne prefiksy są przypisane do organizacji przez jednostkę wymienioną jako właściciel prefiksów w rejestrze routingu i prześlij te dokumenty w celu ręcznej weryfikacji, otwierając bilet pomocy technicznej, jak pokazano poniżej.

![Zrzut ekranu przedstawiający nowe żądanie obsługi (bilet pomocy technicznej) dotyczące "dowodu własności dla publicznych prefiksów".](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>Czy Dynamics 365 jest obsługiwany w ExpressRoute?

Środowiska Dynamics 365 i Common Data Service (CD) są hostowane na platformie Azure, dlatego klienci korzystają z podstawowej obsługi ExpressRoute zasobów platformy Azure. Można nawiązać połączenie z punktami końcowymi usługi, jeśli filtr routera obejmuje regiony platformy Azure, w których są hostowane środowiska usługi Dynamics 365/CD.

> [!NOTE]
> Usługa [ExpressRoute Premium](#expressroute-premium) **nie** jest wymagana dla łączności z usługą Dynamics 365 za pośrednictwem usługi Azure ExpressRoute, jeśli obwód ExpressRoute jest wdrożony w tym samym [regionie geopolitycznym](./expressroute-locations-providers.md#expressroute-locations).

## <a name="data-and-connections"></a>Dane i połączenia

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Czy istnieją ograniczenia dotyczące ilości danych, które można przenieść za pomocą ExpressRoute?

Nie ustawimy limitu liczby transferów danych. Aby uzyskać informacje dotyczące stawek przepustowości, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Jakie szybkości połączeń są obsługiwane przez ExpressRoute?

Obsługiwane oferty przepustowości:

50 Mb/s, 100 Mb/s, 200 Mb/s, 500 Mb/s, 1 Gb/s, 2 Gb/s, 5 Gb/s, 10 Gb/s

### <a name="which-service-providers-are-available"></a>Którzy dostawcy usług są dostępni?

Listę dostawców usług i lokalizacji można znaleźć w temacie [ExpressRoute Partners and Locations](expressroute-locations.md) .

## <a name="technical-details"></a>Szczegóły techniczne

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Jakie są wymagania techniczne dotyczące łączenia mojej lokalizacji lokalnej z platformą Azure?

Wymagania można znaleźć na [stronie Wymagania wstępne ExpressRoute](expressroute-prerequisites.md) .

### <a name="are-connections-to-expressroute-redundant"></a>Czy połączenia z ExpressRoute nadmiarowe?

Tak. Każdy obwód ExpressRoute ma nadmiarową parę połączeń krzyżowych skonfigurowanych do zapewnienia wysokiej dostępności.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Czy nastąpi utrata łączności w przypadku niepowodzenia jednego z linków ExpressRoute?

Jeśli jedno z połączeń krzyżowych zakończy się niepowodzeniem, nastąpi utrata połączenia. Dostępne jest nadmiarowe połączenie obsługujące obciążenie sieci i zapewniające wysoką dostępność obwodu usługi ExpressRoute. Dodatkowo można utworzyć obwód w innej lokalizacji komunikacji równorzędnej w celu uzyskania odporności poziomu obwodu.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Jak mogę zaimplementować nadmiarowości prywatnej komunikacji równorzędnej?

Wiele obwodów usługi ExpressRoute z różnych lokalizacji komunikacji równorzędnej lub maksymalnie czterech połączeń z tej samej lokalizacji komunikacji równorzędnej można podłączyć do tej samej sieci wirtualnej, aby zapewnić wysoką dostępność w przypadku, gdy jeden obwód stanie się niedostępny. Następnie można [przypisać wyższe wagi](./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection) do jednego z połączeń lokalnych w celu preferowania określonego obwodu. Zdecydowanie zalecamy, aby klienci mogli skonfigurować co najmniej dwa obwody usługi ExpressRoute, aby uniknąć pojedynczych punktów awarii. 

Zobacz [tutaj](./designing-for-high-availability-with-expressroute.md) , aby zapoznać się z projektowaniem pod kątem wysokiej dostępności i w [tym miejscu](./designing-for-disaster-recovery-with-expressroute-privatepeering.md) na potrzeby projektowania odzyskiwania po awarii.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Jak wdrożyć nadmiarowość w komunikacji równorzędnej firmy Microsoft?

Zdecydowanie zaleca się, aby klienci korzystali z komunikacji równorzędnej firmy Microsoft w celu uzyskiwania dostępu do usług publicznych platformy Azure, takich jak Azure Storage lub Azure SQL, a także klientów korzystających z komunikacji równorzędnej firmy Microsoft dla Microsoft 365, że implementują wiele obwodów w różnych lokalizacjach komunikacji równorzędnej w celu uniknięcia pojedynczych punktów awarii. Klienci mogą anonsować ten sam prefiks na obu obwodach i używać [jako ścieżki oczekujących](./expressroute-optimize-routing.md#solution-use-as-path-prepending) lub anonsować różne prefiksy w celu określenia ścieżki ze środowisk lokalnych.

Zobacz [tutaj](./designing-for-high-availability-with-expressroute.md) , aby zapoznać się z projektowaniem pod kątem wysokiej dostępności.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Jak mogę zapewnić wysoką dostępność w sieci wirtualnej połączonej z usługą ExpressRoute?

Wysoką dostępność można osiągnąć, łącząc maksymalnie 16 obwodów usługi ExpressRoute w tej samej lokalizacji komunikacji równorzędnej z siecią wirtualną lub łącząc obwody usługi ExpressRoute w różnych lokalizacjach komunikacji równorzędnej (na przykład Singapur, Singapur2) z siecią wirtualną. Jeśli jeden obwód usługi ExpressRoute ulegnie awarii, nastąpi przełączenie w tryb failover do innego obwodu ExpressRoute. Domyślnie ruch wychodzący z sieci wirtualnej jest kierowany w oparciu o równy koszt Routing z obsługą wielu ścieżek (ECMP). Możesz użyć wagi połączeń, aby preferować jeden obwód do innego. Aby uzyskać więcej informacji, zobacz [Optymalizacja routingu ExpressRoute](expressroute-optimize-routing.md).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Jak mogę upewnić się, że mój ruch przeznaczony dla usług publicznych platformy Azure, takich jak Azure Storage i Azure SQL w komunikacji równorzędnej firmy Microsoft lub publicznej komunikacji równorzędnej, jest preferowany na ścieżce ExpressRoute?

Musisz zaimplementować lokalny atrybut *preferencji* na routerach, aby upewnić się, że ścieżka z lokalnego do platformy Azure jest zawsze preferowana w obwodach usługi ExpressRoute.

[Więcej szczegółowych informacji](./expressroute-optimize-routing.md#path-selection-on-microsoft-and-public-peerings) znajduje się w sekcji Wybór ścieżki BGP i typowe konfiguracje routera. 

### <a name="if-im-not-co-located-at-a-cloud-exchange-and-my-service-provider-offers-point-to-point-connection-do-i-need-to-order-two-physical-connections-between-my-on-premises-network-and-microsoft"></a><a name="onep2plink"></a>Jeśli nie znajduje się na serwerze Exchange w chmurze, a dostawca usług oferuje połączenie punkt-punkt, czy muszę zamówić dwa połączenia fizyczne między siecią lokalną a firmą Microsoft?

Jeśli usługodawca może ustanowić dwa wirtualne obwody Ethernet za pośrednictwem połączenia fizycznego, potrzebne jest tylko jedno połączenie fizyczne. Połączenie fizyczne (na przykład Optical Fiber) zostało zakończone na urządzeniu warstwy 1 (L1) (Zobacz obraz). Dwa obwody wirtualne Ethernet są znakowane przy użyciu różnych identyfikatorów sieci VLAN, jeden dla obwodu podstawowego, a drugi dla elementu pomocniczego. Identyfikatory sieci VLAN znajdują się w zewnętrznym nagłówku 802.1 Q. Wewnętrzny nagłówek 802.1 Q (niepokazywany) jest mapowany na konkretną [domenę routingu ExpressRoute](expressroute-circuit-peerings.md).

![Diagram wyróżniający podstawowe i pomocnicze obwody wirtualne warstwy 1 (L1), które składają się na fizyczne połączenie między przełącznikami w witrynie klienta i lokalizacją ExpressRoute.](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Czy mogę rozłożyć jedną z moich sieci VLAN na platformę Azure przy użyciu usługi ExpressRoute?

Nie. Nie obsługujemy rozszerzeń łączności warstwy 2 na platformie Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Czy mogę mieć więcej niż jeden obwód usługi ExpressRoute w mojej subskrypcji?

Tak. W ramach subskrypcji można korzystać z więcej niż jednego obwodu usługi ExpressRoute. Domyślny limit jest ustawiony na 10. Możesz skontaktować się z pomoc techniczna firmy Microsoft, aby zwiększyć limit, w razie potrzeby.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Czy mogę mieć ExpressRoute obwodów od różnych dostawców usług?

Tak. Możesz mieć ExpressRoute obwodów z wieloma dostawcami usług. Każdy obwód usługi ExpressRoute jest skojarzony tylko z jednym dostawcą usług. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Widzę dwie lokalizacje komunikacji równorzędnej ExpressRoute w tej samej linii metra, na przykład Singapur i Singapur2. Którą lokalizację komunikacji równorzędnej należy wybrać, aby utworzyć mój obwód ExpressRoute?
Jeśli dostawca usług oferuje ExpressRoute w obu lokacjach, możesz współpracować z dostawcą i wybrać dowolną lokację w celu skonfigurowania usługi ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Czy mogę mieć wiele obwodów usługi ExpressRoute w tej samej linii metra? Czy mogę połączyć je z tą samą siecią wirtualną?

Tak. Można mieć wiele obwodów usługi ExpressRoute z tymi samymi lub różnymi dostawcami usług. Jeśli Metro ma wiele lokalizacji komunikacji równorzędnej ExpressRoute, a obwody są tworzone w różnych lokalizacjach komunikacji równorzędnej, można połączyć je z tą samą siecią wirtualną. Jeśli obwody są tworzone w tej samej lokalizacji komunikacji równorzędnej, można połączyć maksymalnie cztery obwody z tą samą siecią wirtualną.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Jak mogę połączyć moje sieci wirtualne z obwodem ExpressRoute?

Podstawowe kroki są następujące:

* Ustanów obwód usługi ExpressRoute i włącz go przez dostawcę usług.
* Użytkownik lub dostawca musi skonfigurować komunikację równorzędną BGP.
* Połącz sieć wirtualną z obwodem ExpressRoute.

Aby uzyskać więcej informacji, zobacz [przepływy pracy ExpressRoute dla aprowizacji obwodów i Stanów obwodów](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Czy istnieją granice łączności dla mojego obwodu usługi ExpressRoute?

Tak. Artykuł dotyczący [partnerów i lokalizacji ExpressRoute](expressroute-locations.md) zawiera omówienie granic łączności dla obwodu usługi ExpressRoute. Łączność dla obwodu usługi ExpressRoute jest ograniczona do jednego regionu geopolitycznego. Łączność może być rozszerzona na regiony międzypolityczne, włączając funkcję ExpressRoute Premium.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Czy mogę połączyć się z więcej niż jedną siecią wirtualną do obwodu ExpressRoute?

Tak. Możesz mieć maksymalnie 10 połączeń sieci wirtualnych w standardowym obwodzie ExpressRoute, a nawet 100 w [obwodzie ExpressRoute w warstwie Premium](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Mam wiele subskrypcji platformy Azure, które zawierają sieci wirtualne. Czy można połączyć sieci wirtualne, które znajdują się w osobnych subskrypcjach, do pojedynczego obwodu ExpressRoute?

Tak. Możesz połączyć maksymalnie 10 sieci wirtualnych w tej samej subskrypcji co obwód lub różne subskrypcje, korzystając z pojedynczego obwodu usługi ExpressRoute. Ten limit można zwiększyć, włączając funkcję ExpressRoute Premium. Należy zauważyć, że połączenia i opłaty za przepustowość dedykowanego obwodu będą stosowane do właściciela obwodu usługi ExpressRoute; wszystkie sieci wirtualne mają tę samą przepustowość.

Aby uzyskać więcej informacji, zobacz [udostępnianie obwodu usługi ExpressRoute w wielu subskrypcjach](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Mam wiele subskrypcji platformy Azure skojarzonych z różnymi dzierżawami Azure Active Directory lub Enterprise Agreement rejestracji. Czy można połączyć sieci wirtualne, które znajdują się w osobnych dzierżawcach i rejestracje do pojedynczego obwodu ExpressRoute, nie w tej samej dzierżawie lub rejestracji?

Tak. Autoryzacje ExpressRoute mogą obejmować granice subskrypcji, dzierżawy i rejestracji bez konieczności dodatkowej konfiguracji. Należy zauważyć, że połączenia i opłaty za przepustowość dedykowanego obwodu będą stosowane do właściciela obwodu usługi ExpressRoute; wszystkie sieci wirtualne mają tę samą przepustowość.

Aby uzyskać więcej informacji, zobacz [udostępnianie obwodu usługi ExpressRoute w wielu subskrypcjach](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Czy sieci wirtualne są połączone z tym samym obwodem odizolowanym od siebie?

Nie. Z punktu widzenia routingu wszystkie sieci wirtualne połączone z tym samym obwodem usługi ExpressRoute są częścią tej samej domeny routingu i nie są od siebie odizolowane. Jeśli wymagana jest izolacja trasy, należy utworzyć oddzielny obwód ExpressRoute.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Czy jedna sieć wirtualna jest połączona z więcej niż jednym obwodem ExpressRoute?

Tak. Można połączyć pojedynczą sieć wirtualną z maksymalnie czterema obwodami ExpressRoute w jednej lub innej lokalizacji komunikacji równorzędnej. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Czy mogę uzyskać dostęp do Internetu z sieci wirtualnych podłączonych do obwodów usługi ExpressRoute?

Tak. Jeśli trasy domyślne (0.0.0.0/0) lub trasy internetowe nie są anonsowane przez sesję protokołu BGP, można nawiązać połączenie z Internetem z sieci wirtualnej połączonej z obwodem usługi ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Czy mogę zablokować łączność internetową z sieciami wirtualnymi podłączonymi do obwodów usługi ExpressRoute?

Tak. Można anonsować trasy domyślne (0.0.0.0/0), aby blokować wszystkie połączenia internetowe z maszynami wirtualnymi wdrożonymi w ramach sieci wirtualnej i rozsyłać cały ruch za pośrednictwem obwodu usługi ExpressRoute.

> [!NOTE]
> Jeśli anonsowana trasa o wartości 0.0.0.0/0 zostanie wycofana z anonsów anonsowanych (na przykład ze względu na awarię lub nieprawidłową konfigurację), platforma Azure udostępni [trasę systemową](../virtual-network/virtual-networks-udr-overview.md#system-routes) do zasobów na połączonej Virtual Network w celu zapewnienia łączności z Internetem.  Aby zapewnić, że ruch wychodzący do Internetu jest blokowany, zaleca się umieszczenie sieciowej grupy zabezpieczeń we wszystkich podsieciach z regułą odmowy ruchu przychodzącego dla Internetu.

W przypadku anonsowania tras domyślnych Wymuś ruch do usług oferowanych za pośrednictwem komunikacji równorzędnej firmy Microsoft (np. usługi Azure Storage i bazy danych SQL) z powrotem do lokalizacji lokalnej. Należy skonfigurować routery, aby zwracały ruch do platformy Azure za pośrednictwem ścieżki komunikacji równorzędnej firmy Microsoft lub za pośrednictwem Internetu. Jeśli włączono punkt końcowy usługi dla usługi, ruch do usługi nie jest wymuszany w środowisku lokalnym. Ruch pozostaje w sieci szkieletowej platformy Azure. Aby dowiedzieć się więcej o punktach końcowych usługi, zobacz [punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Czy sieci wirtualne połączone z tym samym obwodem ExpressRoute komunikują się ze sobą?

Tak. Maszyny wirtualne wdrożone w sieciach wirtualnych podłączonych do tego samego obwodu ExpressRoute mogą komunikować się ze sobą. Zalecamy skonfigurowanie komunikacji [równorzędnej sieci wirtualnej](../virtual-network/virtual-network-peering-overview.md) w celu ułatwienia tej komunikacji.

### <a name="can-i-set-up-a-site-to-site-vpn-connection-to-my-virtual-network-in-conjunction-with-expressroute"></a>Czy można skonfigurować połączenie sieci VPN typu lokacja-lokacja z moją siecią wirtualną w połączeniu z usługą ExpressRoute?

Tak. ExpressRoute może współistnieć z sieciami VPN typu lokacja-lokacja. Zobacz [Konfigurowanie współistniejących połączeń ExpressRoute i lokacja-lokacja](expressroute-howto-coexist-resource-manager.md).

### <a name="how-do-i-enable-routing-between-my-site-to-site-vpn-connection-and-my-expressroute"></a>Jak mogę włączyć routing między moim połączeniem sieci VPN typu lokacja-lokacja a moją ExpressRoute?

Jeśli chcesz włączyć routing między gałęzią połączoną z usługą ExpressRoute i gałęzią połączoną z połączeniem sieci VPN typu lokacja-lokacja, musisz skonfigurować [serwer usługi Azure Route](../route-server/expressroute-vpn-support.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Dlaczego istnieje Publiczny adres IP skojarzony z bramą ExpressRoute w sieci wirtualnej?

Publiczny adres IP jest używany wyłącznie do zarządzania wewnętrznego i nie stanowi zagrożenia dla sieci wirtualnej.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Czy istnieją limity liczby tras, które mogę anonsować?

Tak. Akceptujemy do 4000 prefiksy trasy prywatnej komunikacji równorzędnej i 200 dla komunikacji równorzędnej firmy Microsoft. W przypadku włączenia funkcji ExpressRoute Premium można zwiększyć tę wartość do 10 000 tras prywatnej komunikacji równorzędnej.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Czy istnieją ograniczenia dotyczące zakresów adresów IP, które mogę anonsować za pośrednictwem sesji BGP?

Nie akceptujemy prywatnych prefiksów (RFC1918) dla sesji protokołu BGP komunikacji równorzędnej firmy Microsoft. Akceptujemy każdy rozmiar prefiksu (do/32) zarówno przez firmę Microsoft, jak i prywatną komunikację równorzędną.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Co się stanie w przypadku przekroczenia limitów protokołu BGP?

Sesje protokołu BGP zostaną usunięte. Zostaną zresetowane, gdy liczba prefiksów spadnie poniżej limitu.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Co to jest czas wstrzymania BGP ExpressRoute? Czy można je dostosować?

Czas wstrzymania to 180. Komunikaty Keep-Alive są wysyłane co 60 sekund. Są to ustalone ustawienia po stronie firmy Microsoft, które nie mogą zostać zmienione. Istnieje możliwość skonfigurowania różnych czasomierzy, a parametry sesji protokołu BGP zostaną odpowiednio wynegocjowane.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Czy mogę zmienić przepustowość obwodu ExpressRoute?

Tak, możesz próbować zwiększyć przepustowość obwodu usługi ExpressRoute w Azure Portal lub przy użyciu programu PowerShell. Jeśli na porcie fizycznym, na którym został utworzony obwód, jest dostępna pojemność, zmiana zakończy się pomyślnie. 

Jeśli zmiana nie powiedzie się, oznacza to, że nie ma wystarczającej wydajności w bieżącym porcie i konieczne jest utworzenie nowego obwodu ExpressRoute o wyższej przepustowości lub brak dodatkowej pojemności w tej lokalizacji. w takim przypadku nie będzie można zwiększyć przepustowości. 

Należy również wykonać monit z dostawcą połączenia, aby upewnić się, że aktualizuje ograniczenia w ramach sieci, aby zapewnić obsługę większej przepustowości. Nie można jednak zmniejszyć przepustowości obwodu ExpressRoute. Konieczne jest utworzenie nowego obwodu ExpressRoute o mniejszej przepustowości i usunięcie starego obwodu.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Jak mogę zmienić przepustowości obwodu ExpressRoute?

Przepustowość obwodu usługi ExpressRoute można zaktualizować za pomocą interfejsu API REST lub polecenia cmdlet programu PowerShell.

### <a name="i-received-a-notification-about-maintenance-on-my-expressroute-circuit-what-is-the-technical-impact-of-this-maintenance"></a>Otrzymuję powiadomienie o konserwacji w ramach obwodu ExpressRoute. Jaki jest techniczny wpływ tej konserwacji?

W przypadku korzystania z obwodu w [trybie aktywny-aktywny](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute#active-active-connections)należy mieć minimalny wpływ na czas konserwacji. Przeprowadzamy konserwację na głównych i pomocniczych połączeniach obwodu osobno. Zaplanowana konserwacja zwykle będzie wykonywana poza godzinami pracy w strefie czasowej lokalizacji komunikacji równorzędnej i nie można wybrać czasu konserwacji.

### <a name="i-received-a-notification-about-a-software-upgrade-or-maintenance-on-my-expressroute-gateway-what-is-the-technical-impact-of-this-maintenance"></a>Otrzymałem powiadomienie o uaktualnieniu oprogramowania lub obsłudze w mojej bramie ExpressRoute. Jaki jest techniczny wpływ tej konserwacji?

Podczas uaktualniania oprogramowania lub konserwacji w ramach bramy należy mieć minimalne znaczenie. Brama ExpressRoute składa się z wielu wystąpień i podczas uaktualnień, wystąpienia są przełączane do trybu offline po jednym naraz. Chociaż może to spowodować, że brama tymczasowo obsłuży niższą przepływność sieci do sieci wirtualnej, sama Brama nie będzie powodować żadnych przestojów.


## <a name="expressroute-premium"></a>ExpressRoute Premium

### <a name="what-is-expressroute-premium"></a>Co to jest ExpressRoute Premium?

ExpressRoute Premium to zbiór następujących funkcji:

* Zwiększono limit tabeli routingu z 4000 tras do 10 000 tras dla prywatnej komunikacji równorzędnej.
* Zwiększona liczba połączeń sieci wirtualnych i ExpressRoute Global Reach, które można włączyć w obwodzie ExpressRoute (wartość domyślna to 10). Aby uzyskać więcej informacji, zobacz tabelę [limitów ExpressRoute](#limits) .
* Łączność z Microsoft 365
* Globalna łączność przez sieć podstawową firmy Microsoft. Teraz można połączyć sieć wirtualną w jednym regionie geopolitycznym z obwodem ExpressRoute w innym regionie.<br>
    **Przykłady:**

    *  Można połączyć sieć wirtualną utworzoną w Europie zachodniej z obwodem ExpressRoute utworzonym w Dolina krzemu. 
    *  W przypadku komunikacji równorzędnej firmy Microsoft są anonsowane prefiksy z innych regionów geopolitycznych, które mogą nawiązywać połączenia, na przykład, SQL Azure w Europie Zachodniej od obwodu w Dolina krzemu.


### <a name="how-many-vnets-and-expressroute-global-reach-connections-can-i-enable-on-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a><a name="limits"></a>Ile połączeń sieci wirtualnych i ExpressRoute Global Reach można włączyć w obwodzie ExpressRoute, jeśli włączono opcję ExpressRoute Premium?

W poniższych tabelach przedstawiono limity ExpressRoute oraz liczbę połączeń sieci wirtualnych i ExpressRoute Global Reach na obwód ExpressRoute:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Jak mogę włączyć usługę ExpressRoute Premium?

Funkcje ExpressRoute Premium można włączyć, gdy ta funkcja jest włączona, i można ją wyłączyć, aktualizując stan obwodu. Możesz włączyć funkcję ExpressRoute Premium w czasie tworzenia obwodu lub wywołać interfejs API REST/polecenie cmdlet programu PowerShell.

### <a name="how-do-i-disable-expressroute-premium"></a>Jak mogę wyłączyć usługę ExpressRoute Premium?

Możesz wyłączyć usługę ExpressRoute Premium, wywołując interfejs API REST lub polecenie cmdlet programu PowerShell. Przed wyłączeniem usługi ExpressRoute Premium należy upewnić się, że masz skalowane skalowanie, aby spełnić domyślne limity. Jeśli użycie nie przekroczy limitów domyślnych, żądanie wyłączenia ExpressRoute Premium zakończy się niepowodzeniem.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Czy mogę wybrać wybrane funkcje z zestawu funkcji w warstwie Premium?

Nie. Nie można wybrać funkcji. Wszystkie funkcje są włączane po włączeniu opcji ExpressRoute Premium.

### <a name="how-much-does-expressroute-premium-cost"></a>Ile kosztuje ExpressRoute?

Zapoznaj się ze [szczegółami cennika](https://azure.microsoft.com/pricing/details/expressroute/) kosztu.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Czy oprócz standardowych opłat za usługę ExpressRoute płacisz za usługę ExpressRoute Premium?

Tak. Opłaty za usługę ExpressRoute Premium stosują się do opłat za obwód ExpressRoute i opłat wymaganych przez dostawcę połączenia.

## <a name="expressroute-local"></a>ExpressRoute lokalny

### <a name="what-is-expressroute-local"></a>Co to jest ExpressRoute Local?

ExpressRoute Local to jednostka SKU obwodu ExpressRoute, oprócz standardowej jednostki SKU i jednostki SKU w warstwie Premium. Kluczową funkcją lokalną jest to, że lokalny obwód w lokalizacji komunikacji równorzędnej ExpressRoute zapewnia dostęp tylko do jednego lub dwóch regionów świadczenia usługi Azure w lub w prawie jednej linii metra. Natomiast standardowy obwód zapewnia dostęp do wszystkich regionów świadczenia usługi Azure w obszarze geopolitycznym oraz obwodu Premium do wszystkich regionów świadczenia usługi Azure globalnie. W odniesieniu do lokalnej jednostki SKU można anonsować tylko trasy (przez firmę Microsoft i prywatną komunikację równorzędną) z odpowiedniego lokalnego regionu obwodu usługi ExpressRoute. Nie będzie można odbierać tras dla innych regionów niż zdefiniowany region lokalny.

### <a name="what-are-the-benefits-of-expressroute-local"></a>Jakie korzyści wynikają z ExpressRoute lokalnego?

Gdy musisz zapłacać transfer danych wychodzących dla obwodu usługi ExpressRoute w warstwie Standardowa lub Premium, nie płacisz transferów danych wychodzących oddzielnie dla lokalnego obwodu usługi ExpressRoute. Innymi słowy, Cena ExpressRoute lokalnego obejmuje opłaty za transfer danych. ExpressRoute Local to bardziej ekonomiczne rozwiązanie, jeśli masz ogromne ilości danych do przetransferowania, a dane można przenieść za pośrednictwem połączenia prywatnego do lokalizacji komunikacji równorzędnej ExpressRoute znajdującej się blisko żądanych regionów świadczenia usługi Azure. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Jakie funkcje są dostępne i co nie jest ExpressRoute lokalne?

W porównaniu do standardowego obwodu ExpressRoute, obwód lokalny ma ten sam zestaw funkcji, z wyjątkiem:
* Zakres dostępu do regionów platformy Azure zgodnie z powyższym opisem
* ExpressRoute Global Reach nie jest dostępna w lokalnym

ExpressRoute lokalna ma również te same limity dotyczące zasobów (np. liczbę sieci wirtualnych na obwód) jako standard. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Gdzie jest dostępna usługa ExpressRoute lokalna i które regiony platformy Azure są mapowane na poszczególne lokalizacje komunikacji równorzędnej?

ExpressRoute Local jest dostępny w lokalizacjach komunikacji równorzędnej, w których jeden lub dwa regiony platformy Azure są zamknięte. Nie jest on dostępny w lokalizacji komunikacji równorzędnej, w której nie ma regionu świadczenia usługi Azure w tym stanie lub województwie lub kraju/regionie. Sprawdź dokładne mapowania na [stronie Lokalizacje](expressroute-locations-providers.md).  

## <a name="expressroute-for-microsoft-365"></a>ExpressRoute Microsoft 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-microsoft-365-services"></a>Jak mogę utworzyć obwód usługi ExpressRoute, aby połączyć się z usługami Microsoft 365 Services?

1. Przejrzyj [stronę wymagań wstępnych ExpressRoute](expressroute-prerequisites.md) , aby upewnić się, że spełniasz wymagania.
2. Aby upewnić się, że wymagania dotyczące łączności są spełnione, zapoznaj się z listą dostawców usług i lokalizacji w artykule [ExpressRoute partnerzy i lokalizacje](expressroute-locations.md) .
3. Zaplanuj wymagania dotyczące pojemności, przeglądając temat [Planowanie sieci i dostrajanie wydajności dla Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance).
4. Wykonaj kroki wymienione w obszarze przepływy pracy, aby skonfigurować [przepływy pracy ExpressRoute łączności dla aprowizacji obwodów i Stanów obwodów](expressroute-workflows.md).

> [!IMPORTANT]
> Upewnij się, że dodatek ExpressRoute Premium został włączony podczas konfigurowania łączności z usługami Microsoft 365.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-microsoft-365-services"></a>Czy istniejące obwody usługi ExpressRoute obsługują łączność z usługami Microsoft 365ymi?

Tak. Istniejący obwód usługi ExpressRoute można skonfigurować do obsługi łączności z usługami Microsoft 365. Upewnij się, że masz wystarczającą pojemność, aby nawiązać połączenie z usługami Microsoft 365 i że włączono dodatek Premium. [Planowanie sieci i dostrajanie wydajności dla Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance) ułatwia planowanie potrzeb związanych z łącznością. Ponadto zobacz [Tworzenie i modyfikowanie obwodu ExpressRoute](expressroute-howto-circuit-classic.md).

### <a name="what-microsoft-365-services-can-be-accessed-over-an-expressroute-connection"></a>Do jakich usług Microsoft 365 można uzyskać dostęp za pośrednictwem połączenia ExpressRoute?

Zapoznaj się ze stroną [Microsoft 365 adresów URL i zakresów adresów IP,](/microsoft-365/enterprise/urls-and-ip-address-ranges) Aby uzyskać aktualną listę usług obsługiwanych za pośrednictwem usługi ExpressRoute.

### <a name="how-much-does-expressroute-for-microsoft-365-services-cost"></a>Ile kosztuje ExpressRoute usługi Microsoft 365 Services?

Usługi Microsoft 365 wymagają włączenia dodatku Premium. Aby uzyskać informacje o kosztach, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="what-regions-is-expressroute-for-microsoft-365-supported-in"></a>Jakie regiony są ExpressRoute Microsoft 365 obsługiwane w programie?

Aby uzyskać informacje [, zobacz ExpressRoute partnerzy i lokalizacje](expressroute-locations.md) .

### <a name="can-i-access-microsoft-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Czy mogę uzyskać dostęp do Microsoft 365 za pośrednictwem Internetu, nawet jeśli ExpressRoute został skonfigurowany dla mojej organizacji?

Tak. Punkty końcowe usługi Microsoft 365 są dostępne za pośrednictwem Internetu, nawet jeśli skonfigurowano ExpressRoute dla sieci. Skontaktuj się z zespołem sieci w organizacji, jeśli sieć w lokalizacji jest skonfigurowana do łączenia się z usługami Microsoft 365 za pomocą ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-microsoft-365-network-traffic-on-azure-expressroute"></a>Jak zaplanować wysoką dostępność Microsoft 365 ruchu sieciowego na platformie Azure ExpressRoute?
Zapoznaj się z zaleceniami dotyczącymi [wysokiej dostępności i trybu failover za pomocą usługi Azure ExpressRoute](/microsoft-365/enterprise/network-planning-with-expressroute)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Czy mogę uzyskać dostęp do usług Office 365 dla instytucji rządowych USA (Microsoft Community) w ramach obwodu ExpressRoute dla instytucji rządowych USA platformy Azure?

Tak. Punkty końcowe usługi 365 Microsoft Azure w zatoce są dostępne za pomocą ExpressRoute dla instytucji rządowych USA. Należy jednak najpierw otworzyć bilet pomocy technicznej na Azure Portal, aby podać prefiksy, które mają być anonsowane firmie Microsoft. Po rozwiązaniu biletu pomocy technicznej zostanie nawiązane połączenie z usługami w ramach programu Office 365 w zatoce. 

## <a name="route-filters-for-microsoft-peering"></a>Filtry tras dla komunikacji równorzędnej firmy Microsoft

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Po raz pierwszy włączam komunikację równorzędną firmy Microsoft?

Nie będą wyświetlane żadne trasy. Musisz dołączyć filtr trasy do obwodu, aby rozpocząć anonse z prefiksami. Aby uzyskać instrukcje, zobacz [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Mam włączoną komunikację równorzędną firmy Microsoft i teraz próbuję wybrać opcję Exchange Online, ale jest to błąd, do którego nie mam autoryzacji.

W przypadku korzystania z filtrów tras każdy klient może włączyć komunikację równorzędną firmy Microsoft. Jednak w przypadku używania usług Microsoft 365 nadal trzeba uzyskać autoryzację przez Microsoft 365.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Po włączeniu komunikacji równorzędnej firmy Microsoft przed 1 sierpnia 2017, jak mogę wykorzystać filtry tras?

Istniejący obwód będzie kontynuował anonsowanie prefiksów Microsoft 365. Jeśli chcesz dodać anonse publicznych prefiksów platformy Azure w ramach tej samej komunikacji równorzędnej firmy Microsoft, możesz utworzyć filtr tras, wybrać usługi, które są anonsowane (w tym usługi Microsoft 365, których potrzebujesz), i dołączyć filtr do komunikacji równorzędnej firmy Microsoft. Aby uzyskać instrukcje, zobacz [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Mam dostęp do komunikacji równorzędnej firmy Microsoft w jednej lokalizacji, teraz próbuję włączyć ją w innej lokalizacji i nie widzę żadnych prefiksów.

* Komunikacja równorzędna firmy Microsoft ze obwodów usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017, będzie miała wszystkie prefiksy usług anonsowane za pomocą komunikacji równorzędnej firmy Microsoft, nawet jeśli filtry tras nie są zdefiniowane.

* Komunikacja równorzędna firmy Microsoft obwodów usługi ExpressRoute, które są skonfigurowane w dniu lub po 1 sierpnia 2017, nie będzie miała żadnych prefiksów anonsowanych do momentu dołączenia do obwodu filtru tras. Domyślnie nie będą wyświetlane żadne prefiksy.

## <a name="expressroute-direct"></a><a name="expressRouteDirect"></a>ExpressRoute bezpośrednie

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="global-reach"></a><a name="globalreach"></a>Global Reach

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]

## <a name="privacy"></a>Prywatność

### <a name="does-the-expressroute-service-store-customer-data"></a>Czy usługa ExpressRoute przechowuje dane klienta?

Nie.
