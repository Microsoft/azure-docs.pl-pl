---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/24/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a2d79391832bbb5424c6d4096eb5c1a597623367
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81421979"
---
### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Czy użytkownik musi mieć koncentrator i rozmawiać z urządzeniami SD-WAN/VPN, aby korzystać z wirtualnej sieci WAN platformy Azure?

Wirtualna sieć WAN oferuje wiele funkcji wbudowanych w jedno okienko szkła, takich jak łączność sieci VPN lokacja/lokacja, łączność użytkownika/P2S, łączność Usługi ExpressRoute, łączność z siecią wirtualną, wzajemne połączenia VPN ExpressRoute, łączność przechodnie sieci wirtualnej, centralizacja routingu, zabezpieczenia zapory platformy Azure i menedżera zapory, monitorowanie, szyfrowanie ExpressRoute i wiele innych funkcji. Nie trzeba mieć wszystkie te przypadki użycia, aby rozpocząć korzystanie z wirtualnej sieci WAN. Możesz po prostu zacząć z tylko jednym przypadkiem użycia. Architektura Wirtualnej sieci WAN to architektura centrum i szprychy z wbudowaną skalą i wydajnością, w której oddziały (urządzenia VPN/SD-WAN), użytkownicy (klienci sieci VPN platformy Azure, openVPN lub klienci IKEv2), obwody usługi ExpressRoute, sieci wirtualne służą jako szprychy do centrów wirtualnych. Wszystkie koncentratory są połączone w pełnej siatce w standardowej wirtualnej sieci WAN, co ułatwia użytkownikowi korzystanie z szkieletu firmy Microsoft dla dowolnej (dowolnej szprychy). W przypadku centrum i rozmawiał z urządzeniami SD-WAN/VPN użytkownicy mogą ręcznie skonfigurować go w portalu Azure Virtual WAN lub użyć wirtualnego partnera WAN CPE (SD-WAN/VPN) do skonfigurowania łączności z platformą Azure. Wirtualni partnerzy sieci WAN zapewniają automatyzację łączności, czyli możliwość eksportowania informacji o urządzeniu na platformę Azure, pobierania konfiguracji platformy Azure i ustanawiania łączności z centrum wirtualnej sieci WAN platformy Azure. W przypadku łączności sieć VPN typu punkt-lokacja/użytkownik obsługujemy [klienta sieci VPN platformy Azure,](https://go.microsoft.com/fwlink/?linkid=2117554)klienta OpenVPN lub IKEv2. 

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Jaki klient obsługuje sieć VPN użytkownika wirtualnej sieci WAN platformy Azure (point-to-site)?

Wirtualna sieć WAN obsługuje [klienta sieci VPN platformy Azure,](https://go.microsoft.com/fwlink/?linkid=2117554)klienta OpenVPN lub dowolnego klienta IKEv2. Uwierzytelnianie usługi Azure AD jest obsługiwane przez klienta sieci VPN platformy Azure.Co najmniej systemu operacyjnego klienta systemu Windows 10 w wersji 17763.0 lub nowszej jest wymagane.  Klienci OpenVPN mogą obsługiwać uwierzytelnianie oparte na certyfikatach. Po wybraniu auth oparte na cert na bramie, zobaczysz .ovpn plik do pobrania na urządzenie. Zarówno certyfikat, jak i ujęcie radius jest obsługiwane przez IKEv2. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Dla sieci VPN użytkowników (point-to-site)- Dlaczego pula klientów P2S jest podzielona na dwie trasy?

Każda brama ma dwa wystąpienia, podział dzieje się tak, że każde wystąpienie bramy może niezależnie przydzielać adresy IP klienta dla połączonych klientów i ruch z sieci wirtualnej jest kierowany z powrotem do wystąpienia poprawnej bramy, aby uniknąć przeskoku wystąpienia między bramami.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Jak dodać serwery DNS dla klientów P2S?

Istnieją dwie opcje dodawania serwerów DNS dla klientów P2S.

1. Otwórz bilet pomocy technicznej w firmie Microsoft i poproś o dodanie serwerów DNS do centrum
2. Lub, jeśli używasz klienta sieci VPN platformy Azure dla systemu Windows 10, możesz zmodyfikować pobrany plik XML profilu i dodać ** \<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>** tagi przed zaimportowaniem.

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>Dla sieci VPN użytkownika (point-to-site) - ilu klientów jest obsługiwanych?

Każdy użytkownik bramy SIECI VPN P2S ma dwa wystąpienia i każde wystąpienie obsługuje do niektórych użytkowników w miarę zmiany jednostki skalowania. Jednostka skalowania 1-3 obsługuje 500 połączeń, jednostka Scale 4-6 obsługuje 1000 połączeń, jednostka Scale 7-10 obsługuje 5000 połączeń, a jednostka Scale 11+ obsługuje do 10 000 połączeń. Na przykład załóżmy, że użytkownik wybiera jednostkę skalowania 1. Każda jednostka skalowania oznaczałaby wdrożoną bramę aktywny-aktywną, a każde z wystąpień (w tym przypadku 2) obsługiwałoby połączenia do 500. Ponieważ można uzyskać 500 połączeń * 2 na bramę, nie oznacza to, że planujesz 1000 zamiast 500 dla tej jednostki skalowania, ponieważ wystąpienia mogą wymagać serwisowania, podczas których łączność dla dodatkowych 500 może zostać przerwana, jeśli przekroczysz zalecaną liczbę połączeń.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Jaka jest różnica między bramą sieci wirtualnej platformy Azure (brama sieci VPN) a bramą sieci VPN wirtualnej sieci WAN platformy Azure?

Wirtualna sieć WAN zapewnia łączność typu lokacja-lokacja w dużej skali oraz oferuje przepływność, skalowalność i łatwość użycia. Po podłączeniu lokacji do bramy wirtualnej sieci VPN sieci WAN różni się od zwykłej bramy sieci wirtualnej, która używa typu bramy "VPN". Podobnie po podłączeniu obwodu usługi ExpressRoute do koncentratora wirtualnej sieci WAN używa innego zasobu dla bramy usługi ExpressRoute niż zwykła brama sieci wirtualnej, która używa typu bramy "ExpressRoute". Wirtualna sieć WAN obsługuje agregowaną przepustowość do 20 Gb/s zarówno dla sieci VPN, jak i usługi ExpressRoute. Wirtualna sieć WAN ma również automatyzację łączności z ekosystemem partnerów urządzeń oddziału CPE. Urządzenia oddziałowe CPE mają wbudowaną automatyzację, która automatycznie udostępnia i łączy się z wirtualną siecią WAN platformy Azure. Te urządzenia są dostępne w rozwijającym się ekosystemie partnerów sieci SD-WAN i VPN. Zobacz [listę preferowanych partnerów](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Czym wirtualna sieć WAN różni się od bramy sieci wirtualnej platformy Azure?

Sieć VPN bramy sieci wirtualnej jest ograniczona do 30 tuneli. W przypadku połączeń należy używać usługi Virtual WAN dla sieci VPN na dużą skalę. Można połączyć maksymalnie 1000 połączeń w oddziale na region (koncentrator wirtualny) z łączną sumą 20 Gb/s na koncentrator. Połączenie to tunel typu aktywny-aktywny od lokalnego urządzenia sieci VPN do koncentratora wirtualnego. Możesz mieć jedno centrum na region, co oznacza, że możesz połączyć ponad 1000 oddziałów w centrach.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Co to jest jednostka skalowania bramy wirtualnej sieci WAN
Jednostka skalowania jest jednostką zdefiniowaną w celu pobrania agregującej przepływności bramy w centrum wirtualnym. 1 jednostka skalowania VPN = 500 Mb/s . 1 jednostka skalowania usługi ExpressRoute = 2 Gb/s. Przykład: 10-skalowa jednostka SIECI VPN oznaczałaby 500 Mb/s * 10 = 5 Gb/s

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Którzy dostawcy urządzeń (wirtualni partnerzy sieci WAN) są obsługiwani?

Obecnie wielu partnerów obsługuje w pełni zautomatyzowane środowisko usługi Virtual WAN. Aby uzyskać więcej informacji, zobacz [Virtual WAN partners (Partnerzy wirtualnej sieci WAN)](../articles/virtual-wan/virtual-wan-locations-partners.md). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Jakie są kroki automatyzacji dla partnerów usługi Virtual WAN?

Aby uzyskać informacje o krokach automatyzacji dla partnerów, zobacz [Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) (Automatyzacja dla partnerów usługi Virtual WAN).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Czy muszę korzystać z urządzenia preferowanego przez partnera?

Nie. Możesz używać dowolnego urządzenia obsługującego sieć VPN, które spełnia wymagania platformy Azure pod kątem obsługi protokołów IKEv2/IKEv1 IPsec.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Jak partnerzy wirtualnej sieci WAN automatyzują połączenie z wirtualną siecią WAN platformy Azure?

Zdefiniowane programowo rozwiązania w zakresie łączności zwykle umożliwiają zarządzanie urządzeniami oddziału przy użyciu kontrolera lub centrum aprowizacji urządzeń. Kontroler może zautomatyzować połączenie z wirtualną siecią WAN platformy Azure za pomocą interfejsów API platformy Azure. Automatyzacja obejmuje przekazywanie informacji o gałęzi, pobieranie konfiguracji platformy Azure, konfigurowanie tuneli IPSec w centrach wirtualnych platformy Azure i automatyczne konfigurowanie łączności z urządzeniem oddziału do wirtualnej sieci WAN platformy Azure. Gdy masz setki oddziałów, łączenie się przy użyciu wirtualnej sieci WAN CPE Partners jest łatwe, ponieważ środowisko dołączania odbiera potrzebę konfigurowania, konfigurowania i zarządzania łącznością IPsec na dużą skalę. Aby uzyskać więcej informacji, zobacz [Automatyzacja partnerów wirtualnej sieci WAN](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).


### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>W jaki sposób wirtualna sieć WAN obsługuje urządzenia SD-WAN?

Wirtualni partnerzy sieci WAN automatyzują łączność IPsec z punktami końcowymi sieci VPN platformy Azure. Jeśli wirtualny partner sieci WAN jest dostawcą sieci SD-WAN, oznacza to, że kontroler SD-WAN zarządza automatyzacją i łącznością IPsec z punktami końcowymi sieci VPN platformy Azure. Jeśli urządzenie SD-WAN wymaga własnego punktu końcowego zamiast sieci VPN platformy Azure dla dowolnej zastrzeżonej funkcji SD-WAN, można wdrożyć punkt końcowy SD-WAN w sieci wirtualnej platformy Azure i współistnieć z wirtualną siecią WAN platformy Azure.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Czy wirtualna sieć WAN zmienia istniejące funkcje łączności?

Nie ma żadnych zmian istniejących funkcji łączności platformy Azure.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Czy są dostępne nowe zasoby usługi Resource Manager dla wirtualnej sieci WAN?
  
Tak. Wraz z wirtualną siecią WAN wprowadzono nowe zasoby usługi Resource Manager. Aby uzyskać więcej informacji, zobacz [Omówienie](../articles/virtual-wan/virtual-wan-about.md).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Ile urządzeń SIECI VPN może połączyć się z jednym koncentratorem?

Na koncentrator wirtualny obsługiwanych jest do 1000 połączeń. Każde połączenie składa się z czterech łączy, a każde połączenie łączy obsługuje dwa tunele, które są w konfiguracji aktywny-aktywny. Tunele kończą się w wirtualnym koncentratorze platformy Azure.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Czy lokalne urządzenie sieci VPN może łączyć się z wieloma koncentratorami?

Tak. Przepływ ruchu podczas rozpoczynania jest z urządzenia lokalnego do najbliższej krawędzi sieci firmy Microsoft, a następnie do koncentratora wirtualnego.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Czy mogę wdrożyć moje ulubione wirtualne urządzenie sieciowe (w sieci wirtualnej wirtualnego urządzenia sieciowego) i używać go za pomocą wirtualnej sieci WAN platformy Azure?

Tak, można połączyć swoje ulubione wirtualne urządzenie sieciowe (WUS) sieci wirtualnej z usługą Azure Virtual WAN. Najpierw połącz wirtualne urządzenie sieciowe sieci wirtualnej za pomocą połączenia sieci wirtualnej koncentratora. Następnie utwórz wirtualną trasę koncentratora z następnym przeskokiem wskazującym na urządzenie wirtualne. Do tabeli tras koncentratora wirtualnego można zastosować wiele tras. Wszystkie szprychy połączone z siecią wirtualną wirtualnego urządzenia sieciowego muszą być dodatkowo połączone z koncentratorem wirtualnym, aby zapewnić propagowanie tras sieci wirtualnych będących szprychami do systemów lokalnych.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Czy można utworzyć wirtualne urządzenie sieciowe wewnątrz koncentratora wirtualnego?

Nie można wdrożyć sieciowego urządzenia wirtualnego (NVA) wewnątrz koncentratora wirtualnego. Można jednak utworzyć go w sieci wirtualnej szprychy, która jest połączona z koncentratorem wirtualnym i włączyć trasę w centrum do bezpośredniego ruchu dla docelowej sieci wirtualnej za pośrednictwem adresu IP urządzenia NVA (karty sieciowej).

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Czy sieć wirtualna szprychy może mieć bramę sieci wirtualnej?

Nie. Szprychowa sieć wirtualna nie może mieć bramy sieci wirtualnej, jeśli jest podłączona do koncentratora wirtualnego.

### <a name="is-there-support-for-bgp"></a>Czy jest obsługiwany protokół BGP?

Tak. Protokół BGP jest obsługiwany. Podczas tworzenia witryny sieci VPN można podać parametry BGP w niej. Oznacza to, że wszystkie połączenia utworzone na platformie Azure dla tej lokacji zostaną włączone dla protokołu BGP. Ponadto jeśli masz sieć wirtualną z siecią WUS i jeśli ta sieć wirtualna WVA została dołączona do koncentratora wirtualnej sieci WAN, aby zapewnić odpowiednie anonsowanie tras z sieci wirtualnej systemu WUS, szprychy dołączone do sieci wirtualnej WVA muszą wyłączyć protokół BGP. Ponadto należy połączyć te szprychowe sieci wirtualne z siecią wirtualną koncentratora wirtualnego wirtualnego, aby upewnić się, że trasy sieci wirtualnej szprychy są propagowane do systemów lokalnych.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Czy można kierować ruchem przy użyciu trasy zdefiniowanej przez użytkownika w koncentratorze wirtualnym?

Tak, można kierować ruch do sieci wirtualnej przy użyciu tabeli tras koncentratora wirtualnego. Dzięki temu można ustawić trasy dla docelowych sieci wirtualnych na platformie Azure za pośrednictwem określonego adresu IP (zazwyczaj karty sieciowej sieciowej sieciowej sieciowej NVA).

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Czy są dostępne informacje na temat licencjonowania i cen wirtualnej sieci WAN?

Tak. Zobacz stronę z [cennikiem](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Jak obliczyć cenę koncentratora?

* Trzeba by zapłacić za usługi w centrum. Na przykład załóżmy, że masz 10 oddziałów lub urządzeń lokalnych wymagających łączenia się z wirtualną siecią WAN platformy Azure oznaczałoby połączenie z punktami końcowymi sieci VPN w centrum. Powiedzmy, że jest to VPN w skali 1 jednostki = 500 Mbps, jest to opłata w wysokości $ 0.361 / h. Opłata za każde połączenie wynosi 0,05 USD/h. W przypadku 10 połączeń całkowita opłata za usługę/godzinę wyniesie 0,361 USD + 1,5 USD/h. Obowiązują opłaty za przesyłanie danych za ruch wychodzący z platformy Azure.

* Pobierana jest dodatkowa opłata za koncentrator. Zobacz stronę z [cennikiem](https://azure.microsoft.com/pricing/details/virtual-wan/).

* Jeśli brama usługi ExpressRoute została powiązana z układami usługi ExpressRoute łączącymi się z koncentratorem wirtualnym, zapłacisz za cenę jednostkową skali. Każda jednostka skalowania w ER wynosi 2 Gb/s, a każda jednostka połączenia jest naliczana według tej samej stawki co jednostka połączenia sieci VPN.

* Jeśli miałeś spoke VNETs podłączony do koncentratora, opłaty za komunikację równorzędnych w spoke VNETs nadal mają zastosowanie. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>W jaki sposób partnerzy, którzy nie są wymienieni na liście partnerów uruchomienia, mogą dołączyć do programu?

Wszystkie wirtualne interfejsy API sieci WAN są otwartym interfejsem API. Możesz przejść do dokumentacji, aby ocenić wykonalność techniczną. Jeśli masz jakieś pytania, wyślij azurevirtualwan@microsoft.comwiadomość e-mail na adres . Idealny partner powinien dysponować urządzeniem, które można aprowizować na potrzeby połączeń IKEv1 lub IKEv2 IPSec.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Co zrobić, jeśli używane przeze mnie urządzenie nie znajduje się na liście partnerów wirtualnej sieci WAN? Czy nadal mogę go używać do łączenia się z wirtualną siecią VPN usługi Azure?

Tak, o ile urządzenie obsługuje protokół IKEv1 lub IKEv2. Wirtualni partnerzy sieci WAN automatyzują łączność z urządzenia do punktów końcowych sieci VPN platformy Azure. Oznacza to automatyzację kroków, takich jak "przekazywanie informacji o gałęziach", "Protokół IPsec i konfiguracja" oraz "łączność". Ponieważ urządzenie nie pochodzi z ekosystemu partnerów wirtualnej sieci WAN, należy wykonać podnoszenie ciężkich ręcznezastępowanie konfiguracji platformy Azure i aktualizowanie urządzenia w celu skonfigurowania łączności IPsec.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Czy istnieje możliwość skonstruowania sieci usługi Azure Virtual WAN przy użyciu szablonu usługi Resource Manager?

Prostą konfigurację jednej wirtualnej sieci WAN z jednym koncentratorem i jedną lokacją vpnsite można utworzyć za pomocą [szablonu szybkiego startu.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network) Wirtualna sieć WAN to przede wszystkim usługa REST lub oparta na portalu.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Czy wirtualna sieć WAN platformy Azure obsługuje globalne wirtualne sieci równorzędne? 

Sieć wirtualną można połączyć w innym regionie niż wirtualna sieć WAN.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Czy sieci wirtualne połączone z wirtualnym koncentratorem mogą komunikować się ze sobą (V2V Transit)?

Tak. Standardowa wirtualna sieć WAN obsługuje przejściową łączność sieci wirtualnej z siecią wirtualną za pośrednictwem koncentratora wirtualnej sieci WAN, z którą są połączone sieci wirtualne. W terminologii wirtualnej sieci WAN odnosimy się do tych ścieżek jako "lokalny wirtualny tranzyt sieci wirtualnej sieci wirtualnej WAN" dla sieci wirtualnych połączonych z centrum wirtualnego Wan w jednym regionie oraz "globalny tranzyt wirtualnej sieci wirtualnej sieci wirtualnej" dla sieci wirtualnych połączonych za pośrednictwem wielu wirtualnych koncentratorów sieci WAN w dwóch lub więcej regionach. Tranzyt sieci wirtualnej obsługuje przepustowość do 3 Gb/s podczas publicznej wersji zapoznawczej. Przepustowość zostanie rozszerzona, gdy globalny tranzyt przejdzie ga.

UWAGA: Obecnie wersja zapoznawcza przesyłania V2V wymaga wdrożenia GW sieci VPN w centrum wirtualnym w celu wyzwolenia elementów routingu, które mają zostać uruchomione. Ta GW sieci VPN nie jest używana dla ścieżki tranzytowej V2V. Jest to znane ograniczenie i zostaną usunięte w czasie V2V GA. Bramę sieci VPN można usunąć w centrum po jej pełnym uruchomieniu, ponieważ nie jest ona potrzebna dla funkcji przesyłania v2V. 

W niektórych scenariuszach sieci wirtualne szprychy mogą być również bezpośrednio równorzędne ze sobą przy użyciu [komunikacji równorzędnej sieci wirtualnej](../articles/virtual-network/virtual-network-peering-overview.md) oprócz lokalnego lub globalnego tranzytu wirtualnej sieci wirtualnej sieci wirtualnej WAN. W takim przypadku komunikacja równorzędna sieci wirtualnej ma pierwszeństwo przed połączeniem przechodnim za pośrednictwem koncentratora wirtualnej sieci WAN. 

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Co to jest połączenie w oddziale do wirtualnej sieci WAN platformy Azure?

Połączenie z urządzenia w oddziale do wirtualnej sieci WAN platformy Azure obsługuje maksymalnie cztery łącza. Łącze to fizyczne łącze łączności w lokalizacji oddziału (na przykład: ATT, Verizon itp.). Każde połączenie łączy składa się z dwóch aktywnych/aktywnych tuneli IPsec.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Czy łączność między oddziałami jest dozwolona w usłudze Virtual WAN?

Tak. Łączność między oddziałami jest dostępna w usłudze Virtual WAN dla sieci VPN i od sieci VPN do usługi ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Czy ruch między gałęziami przechodzi przez wirtualną sieć WAN platformy Azure?

Tak.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Czy wirtualna sieć WAN wymaga usługi ExpressRoute z każdej witryny?

Nie. Usługa Virtual WAN nie wymaga usługi ExpressRoute w każdej lokacji. Używa standardowej łączności IPsec między lokacjami za pośrednictwem łączy internetowych z urządzenia do centrum wirtualnej sieci WAN platformy Azure. Lokacje mogą być połączone z siecią dostawcy przy użyciu obwodu usługi ExpressRoute. W przypadku witryn połączonych za pomocą usługi ExpressRoute w centrum wirtualnym witryny mogą mieć odgałęzienie do odgałęzienia przepływu ruchu między siecią VPN a usługą ExpressRoute.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Czy istnieje limit przepływności sieci podczas korzystania z usługi Azure Virtual WAN?

Liczba oddziałów jest ograniczona do 1000 połączeń na koncentrator/region i łącznie 20 Gb/s w centrum. Możesz mieć 1 koncentrator na region.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Ile połączeń sieci VPN obsługuje koncentrator wirtualnej sieci WAN?

Centrum wirtualnej sieci WAN platformy Azure może obsługiwać do 1000 połączeń S2S, 10 000 połączeń P2S i 4 połączenia usługi ExpressRoute jednocześnie.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Jaka jest całkowita przepustowość sieci VPN tunelu sieci VPN i połączenia?

Całkowita przepustowość sieci VPN koncentratora wynosi do 20 Gb/s na podstawie wybranej jednostki skalowania. Przepływność jest współużytkowana przez wszystkie istniejące połączenia. Każdy tunel w połączeniu może obsługiwać do 1 Gb/s.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>Nie widzę ustawienia 20 Gb/s dla wirtualnego koncentratora w portalu. Jak to skonfigurować?

Przejdź do bramy sieci VPN wewnątrz koncentratora w portalu i kliknij jednostkę skalowania, aby zmienić ją na odpowiednie ustawienie.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Czy wirtualna sieć WAN umożliwia urządzeniu lokalnemu równoległe korzystanie z wielu usług internetowych, czy też jest to zawsze jeden tunel sieci VPN?

Połączenie przychodzące do wirtualnej sieci VPN WAN jest zawsze aktywnym tunelem (dla odporności w tym samym centrum/regionie) przy użyciu łącza dostępnego w gałęzi. To łącze może być łączem usługodawcy sieciowego w oddziale lokalnym. Wirtualna sieć WAN "VPNSite" umożliwia dodawanie informacji o linkach do witryny. Jeśli w oddziale znajduje się wielu adresów internetowych, a każdy z usług internetowych udostępnia łącze, informacje te można skonfigurować w informacjach o lokacji sieci VPN na platformie Azure. Jednak zarządzanie pracy awaryjnej między usługami isp w oddziale jest całkowicie operacji routingu zorientowanych na gałąź.

### <a name="what-is-global-transit-architecture"></a>Co to jest globalna architektura tranzytowa?

Aby uzyskać informacje na temat globalnej architektury tranzytowej, zobacz [Architektura globalnej sieci tranzytowej i Wirtualna sieć WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>W jaki sposób ruch jest kierowany w sieci szkieletowej platformy Azure?

Ruch jest zgodny ze wzorcem: urządzenie oddziału ->>>sieci Microsoft edge->Microsoft DC (hub VNet)->urządzenie >oddziału >>>sieci firmy Microsoft

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Które elementy tego modelu muszą znajdować się w każdej lokacji? Czy wystarczy tylko połączenie internetowe?

Tak. Połączenie internetowe i fizyczne urządzenie obsługujące protokół IPsec, najlepiej od naszych zintegrowanych [partnerów Wirtualnej sieci WAN.](../articles/virtual-wan/virtual-wan-locations-partners.md) Opcjonalnie można ręcznie zarządzać konfiguracją i łącznością z platformą Azure z preferowanego urządzenia.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Jak włączyć trasę domyślną (0.0.0.0/0) w połączeniu (VPN, ExpressRoute lub Virtual Network):

Koncentrator wirtualny może propagować wyuczonej domyślnej trasy do sieci wirtualnej/lokacji do lokacji połączenia VPN/ExpressRoute, jeśli flaga jest "Włączona" w połączeniu. Ta flaga jest widoczna, gdy użytkownik edytuje połączenie sieci wirtualnej, połączenie sieci VPN lub połączenie usługi ExpressRoute. Domyślnie ta flaga jest wyłączona, gdy lokacja lub obwód usługi ExpressRoute jest podłączony do koncentratora. Jest domyślnie włączona po dodaniu połączenia sieci wirtualnej w celu połączenia sieci wirtualnej z koncentratorem wirtualnym. Trasa domyślna nie pochodzi z wirtualnej sieci WAN hub; trasa domyślna jest propagowana, jeśli jest już prowadzona przez koncentrator wirtualnej sieci WAN w wyniku wdrożenia zapory w centrum lub jeśli inna połączona lokacja ma włączone tunelowanie wymuszone.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Jak koncentrator wirtualny w wirtualnej sieci WAN wybiera najlepszą ścieżkę dla trasy z wielu koncentratorów

Jeśli Virtual Hub uczy się tej samej trasy z wielu zdalnych koncentratorów, kolejność, w jakiej się zdecyduje, jest następująca
1) Route Origin a) Trasy sieciowe — prefiksy sieci wirtualnej, których bezpośrednio doczone są bramy wirtualnego koncentratora b) BGP c) Hub RouteTable (trasy skonfigurowane statycznie) d) trasy InterHub
2)  Metryka trasy: Wirtualna sieć WAN preferuje usługę ExpressRoute niż sieć VPN. Peer usługi ExpressRoute ma większą wagę w porównaniu z elementem równorzędnym sieci VPN
3)  Długość ścieżki AS

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Jakie są różnice między typami wirtualnej sieci WAN (podstawowe i standardowe)?

Typ WAN "Basic" umożliwia utworzenie podstawowego koncentratora (SKU = Basic). Typ WAN "Standard" umożliwia utworzenie koncentratora standardowego (SKU = Standard). Podstawowe koncentratory są ograniczone do funkcji sieci VPN typu lokacja lokacja. Koncentratory standardowe umożliwiają przesyłanie usługi ExpressRoute, User VPN (P2S), koncentratora pełnej siatki i sieci wirtualnej do sieci wirtualnej przesyłanych przez koncentratory. Płacisz opłatę podstawową w wysokości 0,25 USD/h za standardowe koncentratory i opłatę za przetwarzanie danych za przesyłanie danych przez koncentratory podczas łączności sieci wirtualnej do sieci wirtualnej, a także przetwarzanie danych dla ruchu hub-hub. Aby uzyskać więcej informacji, zobacz [Podstawowe i standardowe wirtualne sieci WAN](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Aby uzyskać informacje o cenach, zobacz stronę [Cennik.](https://azure.microsoft.com/pricing/details/virtual-wan/)
