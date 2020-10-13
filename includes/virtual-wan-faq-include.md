---
title: plik dołączany
description: plik dołączany
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/02/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6fac71572446a80ff00368991d6cb80c572864ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91859581"
---
### <a name="is-azure-virtual-wan-in-ga"></a>Czy usługa Azure Virtual WAN jest dostępna w wersji GA?

Tak, usługa Azure Virtual WAN jest ogólnie dostępna (GA). Wirtualna sieć WAN obejmuje jednak kilka funkcji i scenariuszy. W ramach wirtualnej sieci WAN istnieje funkcja lub scenariusze, w których firma Microsoft stosuje tag podglądu. W takich przypadkach konkretna funkcja lub sam scenariusz są w wersji zapoznawczej. Jeśli nie korzystasz z określonej funkcji w wersji zapoznawczej, zostanie zastosowana zwykła pomoc techniczna. Aby uzyskać więcej informacji na temat pomocy technicznej dotyczącej wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania w wersjach](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Czy użytkownik musi dysponować centrum i szprychą z urządzeniami z systemem SD/WAN/VPN, aby można było korzystać z wirtualnej sieci WAN platformy Azure?

Wirtualna sieć WAN oferuje wiele funkcji wbudowanych w jedno tafle szkła, takich jak połączenie sieci VPN typu lokacja-lokacja, łączność z użytkownikami/P2S, łączność ExpressRoute, łączność Virtual Network, połączenia sieci VPN ExpressRoute, połączenia przechodnie między firmami, scentralizowane Routing, Zapora platformy Azure i zabezpieczenia Menedżera zapory, monitorowanie, szyfrowanie ExpressRoute i wiele innych funkcji. Nie trzeba mieć wszystkich tych przypadków użycia, aby rozpocząć korzystanie z wirtualnej sieci WAN. Możesz rozpocząć pracę z tylko jednym przypadkiem użycia. Wirtualna architektura sieci WAN to architektura koncentratora i satelity ze skalą i wydajnością wbudowaną w miejsce, w której gałęzie (urządzenia sieci VPN/SD-WAN), użytkownicy (klienci sieci VPN platformy Azure, openVPN lub IKEv2), obwody usługi ExpressRoute, sieci wirtualne umożliwiają współdziałanie z koncentratorami wirtualnymi. Wszystkie centra są połączone z pełną siatką w standardowej wirtualnej sieci WAN, dzięki czemu użytkownik może łatwo korzystać z sieci szkieletowej firmy Microsoft w przypadku połączeń dowolnego typu (dowolnych). W przypadku urządzeń z systemem SD/WAN/VPN użytkownicy mogą ręcznie skonfigurować je w portalu Azure Virtual WAN lub korzystać z wirtualnego partnera sieci WAN CPE (SD-WAN/VPN) w celu skonfigurowania łączności z platformą Azure. Wirtualne partnerzy sieci WAN zapewniają automatyzację łączności, która umożliwia eksportowanie informacji o urządzeniu do platformy Azure, pobieranie konfiguracji platformy Azure i ustanawianie łączności z wirtualnym koncentratorem sieci WAN platformy Azure. W przypadku połączenia sieci VPN typu punkt-lokacja i użytkownika obsługiwana jest obsługa [klienta sieci VPN platformy Azure](https://go.microsoft.com/fwlink/?linkid=2117554), OpenVPN lub klienta IKEv2. 

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>Czy można wyłączyć w pełni przesiatkowe centra w wirtualnej sieci WAN?

Wirtualna sieć WAN jest dostępna w dwóch wersjach: podstawowa i standardowa. W podstawowej wirtualnej sieci WAN centra nie są podłączane. W standardowej wirtualnej sieci WAN centra są podłączane i automatycznie łączone podczas pierwszej konfiguracji wirtualnej sieci WAN. Użytkownik nie musi wykonywać żadnych określonych czynności. Użytkownik nie musi także wyłączać ani włączać funkcji w celu uzyskania koncentratorów z siatką. Wirtualna sieć WAN udostępnia wiele opcji routingu, które umożliwiają kierowanie ruchu między dowolnymi szprych (VNet, VPN lub ExpressRoute). Zapewnia to łatwą w pełni siatkę koncentratorów, a także elastyczność ruchu sieciowego routingu według własnych potrzeb. 

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>Jak Strefy dostępności i odporność jest obsługiwana w wirtualnej sieci WAN?

Wirtualna sieć WAN to kolekcja centrów i usług udostępnianych w centrum. Użytkownik może mieć dowolną liczbę wirtualnych sieci WAN na ich potrzeby. W wirtualnym koncentratorze sieci WAN istnieje wiele usług, takich jak VPN, ExpressRoute itp. Każda z tych usług (z wyjątkiem zapory platformy Azure) jest wdrażana w regionie Strefy dostępności, czyli jeśli region obsługuje Strefy dostępności. Jeśli region stanie się strefą dostępności po początkowym wdrożeniu w centrum, użytkownik może odtworzyć bramy, co spowoduje wyzwolenie wdrożenia strefy dostępności. Wszystkie bramy są inicjowane w centrum jako aktywne-aktywne, co oznacza, że istnieje odporność wbudowana w centrum. Użytkownicy mogą łączyć się z wieloma centrami, jeśli chcą mieć odporność między regionami. Chociaż pojęcie wirtualnej sieci WAN jest globalne, rzeczywiste wirtualne zasoby sieci WAN są oparte Menedżer zasobów i wdrażane regionalnie. Jeśli wirtualny region sieci WAN miał problem, wszystkie centra w tej wirtualnej sieci WAN będą nadal działać w taki sam sposób, ale użytkownik nie będzie mógł tworzyć nowych centrów do momentu udostępnienia wirtualnego regionu sieci WAN.

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Co klient obsługuje sieć VPN użytkownika wirtualnej sieci WAN platformy Azure (punkt-lokacja)?

Wirtualna sieć WAN obsługuje [klienta sieci VPN platformy Azure](https://go.microsoft.com/fwlink/?linkid=2117554), klienta OpenVPN lub dowolnego klienta protokołu IKEv2. Uwierzytelnianie usługi Azure AD jest obsługiwane przez klienta sieci VPN platformy Azure. wymagany jest co najmniej system operacyjny Windows 10 w wersji 17763,0 lub nowszej.  OpenVPN klienci mogą obsługiwać uwierzytelnianie oparte na certyfikatach. Po wybraniu uwierzytelniania opartego na certyfikatach na bramie zostanie wyświetlony plik *. ovpn* , który zostanie pobrany na urządzenie. Protokół IKEv2 obsługuje zarówno certyfikat, jak i uwierzytelnianie usługi RADIUS. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>W przypadku sieci VPN użytkownika (punkt-lokacja) — Dlaczego Pula klienta P2S została podzielona na dwie trasy?

Każda brama ma dwa wystąpienia, podział odbywa się tak, że każde wystąpienie bramy może niezależnie przydzielić adresy IP klientów dla podłączonych klientów i ruch z sieci wirtualnej jest kierowany z powrotem do poprawnego wystąpienia bramy, aby uniknąć przeskoku wystąpienia między bramami.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Jak mogę dodać serwery DNS dla klientów P2S?

Dostępne są dwie opcje dodawania serwerów DNS dla klientów P2S. Pierwsza metoda jest preferowana, ponieważ dodaje do bramy niestandardowe serwery DNS zamiast klienta.

1. Aby dodać niestandardowe serwery DNS, użyj następującego skryptu programu PowerShell. Zastąp wartości dla danego środowiska.

   ```powershell
   // Define variables
   $rgName = "testRG1"
   $virtualHubName = "virtualHub1"
   $P2SvpnGatewayName = "testP2SVpnGateway1"
   $vpnClientAddressSpaces = 
   $vpnServerConfiguration1Name = "vpnServerConfig1"
   $vpnClientAddressSpaces = New-Object string[] 2
   $vpnClientAddressSpaces[0] = "192.168.2.0/24"
   $vpnClientAddressSpaces[1] = "192.168.3.0/24"
   $customDnsServers = New-Object string[] 2
   $customDnsServers[0] = "7.7.7.7"
   $customDnsServers[1] = "8.8.8.8"
   $virtualHub = $virtualHub = Get-AzVirtualHub -ResourceGroupName $rgName -Name $virtualHubName
   $vpnServerConfig1 = Get-AzVpnServerConfiguration -ResourceGroupName $rgName -Name $vpnServerConfiguration1Name

   // Specify custom dns servers for P2SVpnGateway VirtualHub while creating gateway
   createdP2SVpnGateway = New-AzP2sVpnGateway -ResourceGroupName $rgname -Name $P2SvpnGatewayName -VirtualHub $virtualHub -VpnGatewayScaleUnit 1 -VpnClientAddressPool $vpnClientAddressSpaces -VpnServerConfiguration $vpnServerConfig1 -CustomDnsServer $customDnsServers

   // Specify custom dns servers for P2SVpnGateway VirtualHub while updating existing gateway
   $P2SVpnGateway = Get-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName
   $updatedP2SVpnGateway = Update-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName  -CustomDnsServer $customDnsServers 

   // Re-generate Vpn profile either from PS/Portal for Vpn clients to have the specified dns servers
   ```
2. Lub, jeśli używasz klienta sieci VPN platformy Azure dla systemu Windows 10, możesz zmodyfikować pobrany plik XML profilu i dodać ** \<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers> ** znaczniki przed jego zaimportowaniem.

   ```powershell
      <azvpnprofile>
      <clientconfig>

          <dnsservers>
              <dnsserver>x.x.x.x</dnsserver>
              <dnsserver>y.y.y.y</dnsserver>
          </dnsservers>
    
      </clientconfig>
      </azvpnprofile>
   ```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>W przypadku sieci VPN użytkownika (punkt-lokacja) — ilu klientów jest obsługiwana?

Każda Brama sieci VPN P2S użytkownika ma dwa wystąpienia, a każde wystąpienie obsługuje maksymalnie niektórych użytkowników jako jednostkę skalowania. Jednostka skalowania 1-3 obsługuje połączenia 500, jednostka skalowania 4-6 obsługuje połączenia 1000, jednostka skalowania 7-12 obsługuje połączenia 5000 i jednostka skalowania, która obsługuje do 13-18 połączeń. 

Na przykład, użytkownik wybiera 1 jednostkę skalowania. Każda jednostka skalowania będzie oznaczać, że wdrożono bramę Active-Active i każde wystąpienie (w tym przypadku 2) będzie obsługiwało do 500 połączeń. Ponieważ możesz uzyskać 500 połączeń * 2 na bramę, nie oznacza to, że planujesz 1000 zamiast 500 dla tej jednostki skalowania. W przypadku przekroczenia zalecanej liczby połączeń mogą wystąpić problemy z obsługą wystąpień, w których może być wymagana łączność z dodatkowymi 500mi. Należy również zaplanować przestoje w przypadku podjęcia decyzji o skalowaniu w górę lub w dół w jednostce skalowania lub zmianie konfiguracji typu punkt-lokacja w bramie sieci VPN.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Jaka jest różnica między bramą sieci wirtualnej platformy Azure (VPN Gateway) i bramą sieci VPN Azure Virtual WAN?

Wirtualna sieć WAN zapewnia łączność typu lokacja-lokacja w dużej skali oraz oferuje przepływność, skalowalność i łatwość użycia. Połączenie lokacji z bramą sieci VPN wirtualnej sieci WAN różni się od zwykłej bramy sieci wirtualnej korzystającej z typu bramy "VPN". Podobnie w przypadku łączenia obwodu usługi ExpressRoute z wirtualnym koncentratorem sieci WAN w przypadku bramy usługi ExpressRoute jest stosowany inny zasób niż brama usługi Virtual Network, która używa bramy typu "ExpressRoute". 

Wirtualna sieć WAN obsługuje maksymalnie 20 GB/s zagregowaną przepływność w przypadku sieci VPN i ExpressRoute. Wirtualna sieć WAN również ma automatyzację łączności z ekosystemem partnerów urządzeń gałęziowych CPE. Urządzenia rozgałęzienia CPE mają wbudowaną automatyzację, która automatycznie inicjuje i nawiązuje połączenie z wirtualną siecią WAN platformy Azure. Te urządzenia są dostępne w rozwijającym się ekosystemie partnerów sieci SD-WAN i VPN. Zobacz [listę preferowanych partnerów](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Jak różnią się wirtualne sieci WAN od bramy sieci wirtualnej platformy Azure?

Sieć VPN bramy sieci wirtualnej jest ograniczona do 30 tuneli. W przypadku połączeń należy używać usługi Virtual WAN dla sieci VPN na dużą skalę. Można połączyć maksymalnie 1 000 połączeń gałęzi na region (koncentrator wirtualny) z zagregowaną ilością 20 GB/s na koncentrator. Połączenie to tunel typu aktywny-aktywny od lokalnego urządzenia sieci VPN do koncentratora wirtualnego. Można mieć jeden koncentrator na region, co oznacza, że można łączyć więcej niż 1 000 gałęzi w centrach.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Co to jest jednostka skalowania wirtualnej bramy sieci WAN

Jednostka skalowania jest jednostką zdefiniowaną w celu wybrania zagregowanej przepływności bramy w koncentratorze wirtualnym. 1 jednostka skalowania sieci VPN = 500 MB/s. 1 jednostka skalowania ExpressRoute = 2 GB/s. Przykład: 10 jednostek skalowania sieci VPN spowodowałoby 500 MB/s * 10 = 5 GB/s

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Którzy dostawcy urządzeń (wirtualne partnerzy sieci WAN) są obsługiwani?

Obecnie wielu partnerów obsługuje w pełni zautomatyzowane środowisko usługi Virtual WAN. Aby uzyskać więcej informacji, zobacz [Virtual WAN partners (Partnerzy wirtualnej sieci WAN)](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Jakie są kroki automatyzacji dla partnerów usługi Virtual WAN?

Aby uzyskać informacje o krokach automatyzacji dla partnerów, zobacz [Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) (Automatyzacja dla partnerów usługi Virtual WAN).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Czy muszę korzystać z urządzenia preferowanego przez partnera?

Nie. Możesz używać dowolnego urządzenia obsługującego sieć VPN, które spełnia wymagania platformy Azure pod kątem obsługi protokołów IKEv2/IKEv1 IPsec. Wirtualna sieć WAN również obejmuje rozwiązania partnerskie CPE, które automatyzują łączność z wirtualną siecią WAN platformy Azure, co ułatwia konfigurowanie połączeń sieci VPN IPsec na dużą skalę.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Jak partnerzy wirtualnej sieci WAN automatyzują połączenie z wirtualną siecią WAN platformy Azure?

Zdefiniowane programowo rozwiązania w zakresie łączności zwykle umożliwiają zarządzanie urządzeniami oddziału przy użyciu kontrolera lub centrum aprowizacji urządzeń. Kontroler może zautomatyzować połączenie z wirtualną siecią WAN platformy Azure za pomocą interfejsów API platformy Azure. Automatyzacja obejmuje przekazywanie informacji o gałęzi, pobranie konfiguracji platformy Azure, skonfigurowanie tuneli IPSec do centrów wirtualnych platformy Azure i automatyczne skonfigurowanie połączenia w usłudze Azure Virtual WAN. Jeśli masz setki gałęzi, nawiązywanie połączenia przy użyciu wirtualnych partnerów sieci WAN CPE jest proste, ponieważ środowisko dołączania zajmuje konieczność konfigurowania i konfigurowania łączności z dużą skalą oraz zarządzania nią. Aby uzyskać więcej informacji, zobacz [Automatyzacja wirtualnego partnera sieci WAN](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Co zrobić, jeśli urządzenie, którego używam, nie znajduje się na liście wirtualnych partnerów sieci WAN? Czy nadal mogę używać go do nawiązywania połączenia z wirtualną siecią WAN platformy Azure?

Tak długo, jak urządzenie obsługuje protokół IPsec IKEv1 lub IKEv2. Wirtualne partnerzy sieci WAN automatyzują łączność z urządzeniem z punktami końcowymi sieci VPN platformy Azure. Oznacza to automatyzację kroków, takich jak "przekazywanie informacji o rozgałęzieniu", "IPsec i konfiguracja" i "łączność". Ze względu na to, że urządzenie nie pochodzi z ekosystemu wirtualnego partnera sieci WAN, należy wykonać duże podnoszenie poziomu ręcznego konfigurowania platformy Azure i zaktualizować urządzenie w celu skonfigurowania połączenia IPsec.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>W jaki sposób partnerzy, którzy nie są wymienieni na liście partnerów uruchomienia, mogą dołączyć do programu?

Wszystkie wirtualne interfejsy API sieci WAN są otwarte. Aby ocenić wykonalność techniczną, możesz przejść do dokumentacji [Automatyzacja wirtualnego partnera sieci WAN](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) . Idealny partner powinien dysponować urządzeniem, które można aprowizować na potrzeby połączeń IKEv1 lub IKEv2 IPSec. Gdy firma ukończy pracę usługi Automation na urządzeniu CPE na podstawie przedstawionych powyżej wytycznych dotyczących automatyzacji, możesz skontaktować się z Tobą w azurevirtualwan@microsoft.com celu wystawienia [połączenia przez partnerów]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners). Jeśli jesteś klientem, który chce, aby określone rozwiązanie firmowe było wyświetlane jako wirtualny partner sieci WAN, skontaktuj się z wirtualną siecią WAN, wysyłając wiadomość e-mail na adres azurevirtualwan@microsoft.com .

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Jak wirtualna sieć WAN obsługuje urządzenia SD-WAN?

Wirtualne partnerzy sieci WAN automatyzują połączenia IPsec z punktami końcowymi sieci VPN platformy Azure. Jeśli wirtualny partner sieci WAN jest dostawcą SD-WAN, należy zastanowić się, że kontroler SD-WAN zarządza automatyzacją i łącznością IPsec z punktami końcowymi sieci VPN platformy Azure. Jeśli urządzenie SD-WAN wymaga własnego punktu końcowego zamiast sieci VPN platformy Azure dla dowolnej funkcjonalnej funkcjonalności SD-WAN, można wdrożyć punkt końcowy SD-WAN w sieci wirtualnej platformy Azure i współistnieć z wirtualną siecią WAN platformy Azure.

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Ile urządzeń sieci VPN może się połączyć z jednym koncentratorem?

Do 1 000 połączeń są obsługiwane na koncentratorze wirtualnym. Każde połączenie składa się z czterech linków, a każde połączenie linku obsługuje dwa tunele, które znajdują się w konfiguracji aktywne-aktywne. Tunele kończą się w bramie sieci VPN usługi Azure Virtual Hub. Linki reprezentują fizyczny link usługodawcy internetowego na urządzeniu rozgałęzienia/sieci VPN.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Co to jest połączenie gałęzi z wirtualną siecią WAN platformy Azure?

Połączenie między gałęzią lub urządzeniem sieci VPN a wirtualną siecią WAN platformy Azure to nic, ale połączenie sieci VPN, które łączy praktycznie lokację sieci VPN i VPN Gateway platformy Azure w koncentratorze wirtualnym.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Czy lokalne urządzenie sieci VPN może łączyć się z wieloma koncentratorami?

Tak. Przepływ ruchu, po rozpoczęciu, pochodzi z urządzenia lokalnego do najbliższej krawędzi sieci firmy Microsoft, a następnie do koncentratora wirtualnego.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Czy są dostępne nowe zasoby usługi Resource Manager dla wirtualnej sieci WAN?
  
Tak. wirtualne sieci WAN mają nowe zasoby Menedżer zasobów. Aby uzyskać więcej informacji, zobacz [Omówienie](../articles/virtual-wan/virtual-wan-about.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Czy mogę wdrożyć moje ulubione wirtualne urządzenie sieciowe (w sieci wirtualnej wirtualnego urządzenia sieciowego) i używać go za pomocą wirtualnej sieci WAN platformy Azure?

Tak, można połączyć swoje ulubione wirtualne urządzenie sieciowe (WUS) sieci wirtualnej z usługą Azure Virtual WAN.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Czy można utworzyć sieciowe urządzenie wirtualne w koncentratorze wirtualnym?

Nie można wdrożyć sieciowego urządzenia wirtualnego (urządzenie WUS) wewnątrz koncentratora wirtualnego. Można go jednak utworzyć w sieci wirtualnej szprych, która jest połączona z koncentratorem wirtualnym i umożliwić odpowiednie kierowanie ruchu sieciowego zgodnie z potrzebami.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Czy sieć wirtualna szprych ma bramę sieci wirtualnej?

Nie. Sieć wirtualna szprych nie może mieć bramy sieci wirtualnej, jeśli jest ona podłączona do koncentratora wirtualnego.

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>Czy jest obsługiwane połączenie BGP w sieci VPN?

Tak. Protokół BGP jest obsługiwany. Podczas tworzenia witryny sieci VPN można podać w niej parametry protokołu BGP. Oznacza to, że dla wszystkich połączeń utworzonych na platformie Azure dla tej lokacji zostanie włączone protokół BGP.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Czy są dostępne informacje na temat licencjonowania i cen wirtualnej sieci WAN?

Tak. Zobacz stronę z [cennikiem](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Czy istnieje możliwość skonstruowania sieci usługi Azure Virtual WAN przy użyciu szablonu usługi Resource Manager?

Prostą konfigurację jednej wirtualnej sieci WAN z jednym koncentratorem i jedną vpnsite można utworzyć przy użyciu [szablonu szybkiego startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Wirtualna sieć WAN jest głównie usługą opartą na usłudze REST lub portalu.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Czy sieci wirtualnych szprych podłączony do koncentratora wirtualnego komunikuje się ze sobą (V2V transport)?

Tak. Standardowa wirtualna sieć WAN obsługuje łączność między sieciami wirtualnymi za pośrednictwem wirtualnego koncentratora sieci WAN, z którym są połączone sieci wirtualnych. W terminologii wirtualnej sieci WAN odnosimy się do tych ścieżek jako "lokalna wirtualna sieć WAN — tranzyt wirtualny" dla sieci wirtualnych połączonej z wirtualnym koncentratorem sieci WAN w jednym regionie, a "globalna wirtualna sieć WAN — tranzyt wirtualny" dla sieci wirtualnych połączonych przez wiele wirtualnych koncentratorów sieci WAN w co najmniej dwóch regionach. W niektórych scenariuszach sieci wirtualnych może być również bezpośrednio komunikacji równorzędnej ze sobą przy użyciu [Virtual Network komunikacji równorzędnej](../articles/virtual-network/virtual-network-peering-overview.md) , a nie lokalnego lub globalnego tranzytu wirtualnego sieci WAN. W takim przypadku Komunikacja równorzędna sieci wirtualnej ma pierwszeństwo przed połączeniem przechodnim za pośrednictwem wirtualnego koncentratora sieci WAN.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Czy łączność między oddziałami jest dozwolona w usłudze Virtual WAN?

Tak. Łączność między oddziałami jest dostępna w usłudze Virtual WAN. Gałąź jest koncepcyjnie stosowana dla użytkowników sieci VPN, obwodów usługi ExpressRoute lub sieci VPN typu punkt-lokacja/użytkownika. Włączanie gałęzi do gałęzi jest domyślnie włączone i można ją znaleźć w ustawieniach konfiguracji sieci WAN. Dzięki temu oddziały sieci VPN/użytkownicy mogą łączyć się z innymi gałęziami sieci VPN, a także zapewniać łączność między użytkownikami sieci VPN i ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Czy ruch rozgałęzienia jest przechodzący przez wirtualną sieć WAN platformy Azure?

Tak.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Czy wirtualne sieci WAN wymagają ExpressRoute z każdej lokacji?

Nie. Wirtualna sieć WAN nie wymaga ExpressRoute z każdej lokacji. Lokacje mogą być połączone z siecią dostawcy przy użyciu obwodu usługi ExpressRoute. W przypadku lokacji połączonych przy użyciu usługi ExpressRoute z koncentratorem wirtualnym, a także sieci VPN IPsec do tego samego centrum, koncentrator wirtualny zapewnia łączność tranzytową między użytkownikami sieci VPN i ExpressRoute.

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>Czy istnieje przepustowość sieci lub limit połączeń w przypadku korzystania z wirtualnej sieci WAN platformy Azure?

Przepustowość sieci dotyczy usługi w wirtualnym koncentratorze sieci WAN. Chociaż możesz mieć dowolną liczbę wirtualnych sieci WAN, każda wirtualna sieć WAN zezwala na 1 Hub na region. W każdym centrum przepustowość zagregowana sieci VPN wynosi 20 GB/s, zagregowana przepływność ExpressRoute to maksymalnie 20 GB/s, a przepustowość sieci VPN typu punkt-lokacja jest równa 20 GB/s. Router w koncentratorze wirtualnym obsługuje do 50 GB/s w przypadku przepływów ruchu między sieciami wirtualnymi i zakłada, że w ramach wszystkich sieci wirtualnych podłączonych do jednego koncentratora jest przyjmowana łączna liczba 2000 obciążeń maszyn wirtualnych.

Gdy lokacje sieci VPN nawiązują połączenie z koncentratorem, wykonują połączenie. Wirtualna sieć WAN obsługuje do 1000 połączeń lub tunele IPsec 2000 na koncentratorze wirtualnym. Gdy użytkownicy zdalni łączą się z koncentratorem wirtualnym, łączą się z bramą sieci VPN P2S, która obsługuje do 10 000 użytkowników w zależności od wybranej jednostki skalowania (przepustowości) dla bramy sieci VPN P2S w koncentratorze wirtualnym.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Jaka jest całkowita przepustowość sieci VPN tunelu VPN i połączenia?

Całkowita przepustowość sieci VPN koncentratora to 20 GB/s w oparciu o wybraną jednostkę skalowania bramy sieci VPN. Przepływność jest współdzielona przez wszystkie istniejące połączenia. Każdy tunel w połączeniu może obsługiwać do 1 GB/s.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>Nie widzę ustawienia 20 GB/s dla koncentratora wirtualnego w portalu. Jak mogę skonfigurować?

Przejdź do bramy sieci VPN w centrum w portalu i kliknij jednostkę skalowania, aby zmienić to ustawienie na odpowiednie.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Czy wirtualne sieci WAN zezwalają na urządzenie lokalne w celu użycia wielu usługodawców równoległych, czy też jest to zawsze pojedynczy tunel VPN?

Rozwiązania urządzeń lokalnych mogą stosować zasady ruchu w celu kierowania ruchu między wieloma tunelami do wirtualnego centrum sieci WAN platformy Azure (bramy sieci VPN w koncentratorze wirtualnym).

### <a name="what-is-global-transit-architecture"></a>Co to jest globalna architektura tranzytowa?

Aby uzyskać informacje na temat globalnej architektury tranzytowej, zobacz [Globalna architektura sieci tranzytowej i wirtualna sieć WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>W jaki sposób ruch jest kierowany w sieci szkieletowej platformy Azure?

Ruch jest zgodny ze wzorcem: rozgałęzienie Device->usługodawca internetowy — >Microsoft Network Edge->Microsoft DC (Hub VNet) — >Microsoft Network Edge->usługodawca internetowy->rozgałęzienie

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Które elementy tego modelu muszą znajdować się w każdej lokacji? Czy wystarczy tylko połączenie internetowe?

Tak. Połączenie internetowe i urządzenie fizyczne obsługujące protokół IPsec najlepiej z naszych zintegrowanych [wirtualnych partnerów sieci WAN](../articles/virtual-wan/virtual-wan-locations-partners.md). Opcjonalnie możesz ręcznie zarządzać konfiguracją i łącznością z platformą Azure z poziomu preferowanego urządzenia.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Jak mogę włączyć trasy domyślnej (0.0.0.0/0) w połączeniu (VPN, ExpressRoute lub Virtual Network)?

Koncentrator wirtualny może propagować rozpoznajenie trasy domyślnej do sieci wirtualnej/połączenia sieci VPN typu lokacja-lokacja, jeśli flaga jest włączona w połączeniu. Ta flaga jest widoczna, gdy użytkownik edytuje połączenie sieci wirtualnej, połączenie sieci VPN lub połączenie ExpressRoute. Domyślnie ta flaga jest wyłączona, gdy lokacja lub obwód ExpressRoute jest podłączony do centrum. Jest ona domyślnie włączona po dodaniu połączenia sieci wirtualnej w celu podłączenia sieci wirtualnej do koncentratora wirtualnego. Trasa domyślna nie pochodzi z wirtualnego koncentratora sieci WAN; Trasa domyślna jest propagowana, jeśli jest już wykorzystana przez koncentrator wirtualnych sieci WAN w wyniku wdrożenia zapory w centrum lub jeśli w innej połączonej lokacji włączono tunelowanie wymuszone.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Jak koncentrator wirtualny w wirtualnej sieci WAN wybiera najlepszą ścieżkę dla trasy z wielu centrów

Jeśli koncentrator wirtualny nauczy się tej samej trasy z wielu centrów zdalnych, kolejność, w której jest ona poddana decyzji, jest następująca:

1. Najdłuższy odpowiednik prefiksu.
2. Trasy lokalne przez Interhub.
3. Trasy statyczne za pośrednictwem protokołu BGP: jest to w kontekście decyzji podejmowanej przez router koncentratora wirtualnego. Jeśli jednak producent sieci VPN jest bramą, w której lokacja anonsuje trasy za pośrednictwem protokołu BGP lub udostępnia prefiksy adresów statycznych, trasy statyczne mogą być preferowane za pośrednictwem tras protokołu BGP.
4. ExpressRoute (ER) za pośrednictwem sieci VPN: element ER jest preferowany przez sieć VPN, gdy kontekst jest centrum lokalnym. Łączność tranzytowa między obwodami usługi ExpressRoute jest dostępna tylko za pośrednictwem Global Reach. W związku z tym w scenariuszach, w których obwód usługi ExpressRoute jest podłączony do jednego centrum i istnieje inny obwód usługi ExpressRoute połączony z innym koncentratorem z połączeniem sieci VPN, Sieć VPN może być preferowana dla scenariuszy między centrami.
5. JAKO długość ścieżki.

### <a name="does-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>Czy wirtualne koncentrator sieci WAN zezwala na łączność między obwodami ExpressRoute.

Przesyłanie między elementami ER-a-ER jest zawsze realizowane za pośrednictwem globalnego zasięgu. Bramy koncentratora wirtualnego są wdrażane w regionach DC lub Azure. Gdy dwa obwody usługi ExpressRoute łączą się za pośrednictwem globalnego zasięgu, nie ma potrzeby, aby ruch był cały sposób od routerów brzegowych do kontrolera domeny koncentratora wirtualnego.

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-expressroute-circuits-or-vpn-connections"></a>Czy istnieje koncepcja wagi obwodów usługi Azure Virtual ExpressRoute lub połączeń sieci VPN

Gdy wiele obwodów usługi ExpressRoute jest podłączonych do koncentratora wirtualnego, Waga routingu w ramach połączenia zapewnia mechanizm dla ExpressRoute w koncentratorze wirtualnym do preferowania jednego obwodu. Nie ma mechanizmu ustawiania wagi dla połączenia sieci VPN. Platforma Azure zawsze preferuje połączenie ExpressRoute za pośrednictwem połączenia sieci VPN w ramach jednego centrum.

### <a name="does-virtual-wan-prefer-expressroute-over-vpn-for-traffic-egressing-azure"></a>Czy wirtualne sieci WAN preferują ExpressRoute za pośrednictwem sieci VPN dla ruchu wychodzącego z platformy Azure

Tak.

### <a name="when-a-virtual-wan-hub-has-an-expressroute-circuit-and-a-vpn-site-connected-to-it-what-would-cause-a-vpn-connection-route-to-be-preferred-over-expressroute"></a>Gdy wirtualny koncentrator sieci WAN ma obwód usługi ExpressRoute oraz podłączoną do niej lokację sieci VPN, co spowodowałoby preferowanie trasy połączenia sieci VPN względem ExpressRoute?

Gdy obwód usługi ExpressRoute jest podłączony do koncentratora wirtualnego, routery Microsoft Edge są pierwszym węzłem komunikacji między środowiskiem lokalnym i platformą Azure. Routery brzegowe komunikują się z wirtualnymi bramami ExpressRoute sieci WAN, które z kolei przepoznają trasy z routera koncentratora wirtualnego, który kontroluje wszystkie trasy między bramami w wirtualnej sieci WAN. Routery Microsoft Edge przetwarzają wirtualne centra ExpressRoute tras z wyższym priorytetem dla tras uzyskiwanych z poziomu lokalnego. Z jakiegoś powodu, jeśli połączenie sieci VPN stanie się podstawowym nośnikiem dla koncentratora wirtualnego, aby poznać trasy z (np. scenariusze trybu failover między usługą ExpressRoute i siecią VPN), chyba że lokacja sieci VPN nie ma dłuższej długości ścieżki, koncentrator wirtualny będzie nadal współużytkować trasy sieci VPN z bramą usługi ExpressRoute, powodując, że routery usługi Microsoft Edge preferują trasy VPN za pośrednictwem

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>Gdy są połączone dwa centra (koncentrator 1 i 2), a istnieje obwód usługi ExpressRoute podłączony jako dziob do obu centrów, co to jest ścieżka do sieci wirtualnej podłączonej do centrum 1 w celu połączenia z siecią wirtualną podłączoną do koncentratora 2?

Bieżące zachowanie polega na preferowaniu ścieżki obwodu usługi ExpressRoute za pośrednictwem piasty-to-Hub na potrzeby łączności między sieciami wirtualnymi. Nie jest to jednak zalecane w konfiguracji wirtualnej sieci WAN. Wirtualny zespół sieci WAN pracuje nad poprawką, aby włączyć preferencję dla piasty-Hub na ścieżce ExpressRoute. Zalecenie dotyczy wielu obwodów usługi ExpressRoute (różnych dostawców) do łączenia się z jednym koncentratorem i korzystania z łączności między centrami zapewnianą przez wirtualną sieć WAN w przypadku przepływów ruchu między regionami.

### <a name="can-hubs-be-created-in-different-resource-group-in-virtual-wan"></a>Czy centra mogą być tworzone w innej grupie zasobów w wirtualnej sieci WAN?
Tak. Ta opcja jest obecnie dostępna tylko za pośrednictwem programu PowerShell. Wirtualny Portal sieci WAN jest upoważniony do centrów w tej samej grupie zasobów co zasób wirtualnej sieci WAN.

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Czy w wirtualnej sieci WAN jest obsługiwane protokół IPv6?

Protokół IPv6 nie jest obsługiwany w przypadku koncentratora wirtualnego sieci WAN i jego bram. Jeśli masz sieć wirtualną z obsługą protokołów IPv4 i IPv6 i chcesz podłączyć sieć wirtualną do wirtualnej sieci WAN, ten scenariusz nie jest obecnie obsługiwany. 

### <a name="what-is-the-recommended-api-version-to-be-used-by-scripts-automating-various-virtual-wan-functionalities"></a>Co to jest zalecana wersja interfejsu API, która ma być używana przez skrypty automatyzując różne wirtualne funkcje sieci WAN?

Wymagana jest minimalna wersja 05-01-2020 (może 1 2020). 

### <a name="are-there-any-virtual-wan-limits"></a>Czy istnieją jakieś limity wirtualnej sieci WAN?

Zobacz sekcję [limity dotyczące wirtualnych sieci WAN](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#virtual-wan-limits) na stronie limitów subskrypcji i usług.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Jakie są różnice między wirtualnymi typami sieci WAN (podstawowa i standardowa)?

Zobacz [podstawowe i standardowe wirtualne sieci WAN](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Aby uzyskać cennik, zobacz stronę z [cennikiem](https://azure.microsoft.com/pricing/details/virtual-wan/) .
