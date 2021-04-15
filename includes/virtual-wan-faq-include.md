---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2c1cf6e1d47f9bb78349e0846f624e1d6a484669
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386660"
---
### <a name="is-azure-virtual-wan-in-ga"></a>Czy Azure Virtual WAN jest w programie GA?

Tak, Azure Virtual WAN jest ogólnie dostępna. Jednak Virtual WAN składa się z kilku funkcji i scenariuszy. Istnieją funkcje lub scenariusze w ramach Virtual WAN, w których firma Microsoft stosuje tag Wersji zapoznawczej. W takich przypadkach konkretną funkcję lub sam scenariusz można wyświetlić w wersji zapoznawczej. Jeśli nie używasz określonej funkcji w wersji zapoznawczej, obowiązuje regularna pomoc techniczna w wersji gałęd. Aby uzyskać więcej informacji na temat obsługi wersji zapoznawczych, zobacz Dodatkowe warunki użytkowania dotyczące wersji [Microsoft Azure zapoznawczych.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Czy użytkownik musi mieć piastę i szprychy z urządzeniami SD-WAN/VPN, aby używać Azure Virtual WAN?

Usługa Virtual WAN udostępnia wiele funkcji wbudowanych w pojedyncze okienko, takie jak łączność sieci VPN typu lokacja-lokacja, łączność użytkownika/P2S, łączność expressRoute, łączność Virtual Network, łączność usługi VPN ExpressRoute, łączność przechodnia między sieciami wirtualnymi, scentralizowany routing, zabezpieczenia Azure Firewall i Firewall Manager, monitorowanie, szyfrowanie ExpressRoute i wiele innych możliwości. Nie trzeba mieć wszystkich tych przypadków użycia, aby rozpocząć korzystanie z Virtual WAN. Możesz rozpocząć od tylko jednego przypadku użycia.

Architektura Virtual WAN to architektura piasty i szprych ze skalą i wydajnością, w której gałęzie (urządzenia SIECI VPN/SD-WAN), użytkownicy (klienci sieci VPN platformy Azure, klienci openVPN lub IKEv2), obwody usługi ExpressRoute, sieci wirtualne służą jako szprychy do koncentratorów wirtualnych. Wszystkie koncentratory są połączone w pełnej siatce w standardzie Virtual WAN co ułatwia użytkownikowi korzystanie z sieci szkieletowej firmy Microsoft w celu połączenia typu dowolna-dowolna (dowolna szprycha). W przypadku piasty i szprych z urządzeniami SD-WAN/VPN użytkownicy mogą ręcznie skonfigurować ją w portalu usługi Azure Virtual WAN lub użyć urządzenia Virtual WAN Partner CPE (SD-WAN/VPN) w celu skonfigurowania łączności z platformą Azure.

Virtual WAN zapewniają automatyzację łączności, czyli możliwość eksportowania informacji o urządzeniu na platformę Azure, pobierania konfiguracji platformy Azure i ustanawiania łączności z centrum Azure Virtual WAN azure. W przypadku łączności sieci VPN typu punkt-lokacja/użytkownik obsługujemy klienta sieci VPN platformy [Azure,](https://go.microsoft.com/fwlink/?linkid=2117554)klienta OpenVPN lub klienta IKEv2.

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>Czy można wyłączyć w pełni siatki koncentratory w Virtual WAN?

Virtual WAN są w dwóch wersjach: Podstawowa i Standardowa. W podstawowym Virtual WAN koncentratory nie są siatki. W standardzie Virtual WAN koncentratory są siatki i automatycznie połączone podczas pierwszej skonfigurowania wirtualnej sieci WAN. Użytkownik nie musi nic konkretnego robić. Użytkownik nie musi również wyłączać ani włączać funkcji uzyskiwania centrów z siatki. Virtual WAN udostępnia wiele opcji routingu, które sterują ruchem między dowolną szprychą (siecią wirtualną, siecią VPN lub usługą ExpressRoute). Zapewnia ona łatwość obsługi w pełni siatki centrów, a także elastyczność kierowania ruchu na potrzeby użytkownika.

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>W jaki sposób Strefy dostępności i odporność są obsługiwane w Virtual WAN?

Virtual WAN to kolekcja centrów i usług dostępnych wewnątrz centrum. Użytkownik może mieć tyle samo Virtual WAN na swoje potrzeby. W centrum Virtual WAN istnieje wiele usług, takich jak VPN, ExpressRoute itp. Każda z tych usług (z wyjątkiem Azure Firewall) jest wdrażana w regionie Strefy dostępności, czyli jeśli region obsługuje Strefy dostępności. Jeśli region staje się strefą dostępności po początkowym wdrożeniu w centrum, użytkownik może ponownie utworzyć bramy, co spowoduje wyzwolenie wdrożenia strefy dostępności. Wszystkie bramy są aprowowane w centrum jako aktywne-aktywne, co oznacza, że w centrum jest wbudowana odporność. Użytkownicy mogą łączyć się z wieloma centrami, jeśli chcą mieć odporność w różnych regionach.

Mimo że koncepcja Virtual WAN jest globalna, rzeczywisty zasób Virtual WAN jest oparty Resource Manager i wdrażany regionalnie. Jeśli problem dotyczy samego regionu wirtualnej sieci WAN, wszystkie koncentratory w tej wirtualnej sieci WAN będą nadal działać bez problemów, ale użytkownik nie będzie mógł tworzyć nowych centrów, dopóki region wirtualnej sieci WAN nie będzie dostępny.

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Jakiego klienta obsługuje Azure Virtual WAN VPN użytkownika (punkt-lokacja)?

Virtual WAN obsługuje [klienta sieci VPN platformy Azure,](https://go.microsoft.com/fwlink/?linkid=2117554)klienta OpenVPN lub dowolnego klienta IKEv2. Uwierzytelnianie usługi Azure AD jest obsługiwane Azure VPN Client. Wymagana jest Windows 10 systemu operacyjnego klienta w wersji 17763.0 lub wyższej.  Klienci OpenVPN mogą obsługiwać uwierzytelnianie oparte na certyfikatach. Po wybraniu uwierzytelniania opartego na certyfikatach w bramie zobaczysz plik .ovpn* do pobrania na urządzenie. Protokół IKEv2 obsługuje zarówno uwierzytelnianie certyfikatu, jak i usługi RADIUS. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>W przypadku sieci VPN użytkownika (punkt-lokacja) — dlaczego pula klientów punkt-lokacja jest podzielona na dwie trasy?

Każda brama ma dwa wystąpienia. Podział odbywa się tak, aby każde wystąpienie bramy było w stanie niezależnie przydzielić adres IP klienta dla połączonych klientów, a ruch z sieci wirtualnej jest przekierowyny z powrotem do poprawnego wystąpienia bramy, aby uniknąć przeskoku między wystąpieniami bramy.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Jak mogę serwery DNS dla klientów P2S?

Istnieją dwie opcje dodawania serwerów DNS dla klientów P2S. Pierwsza metoda jest preferowana, ponieważ dodaje niestandardowe serwery DNS do bramy zamiast klienta.

1. Użyj poniższego skryptu programu PowerShell, aby dodać niestandardowe serwery DNS. Zastąp wartości dla swojego środowiska.

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
2. Jeśli natomiast używasz pliku Azure VPN Client for Windows 10, możesz zmodyfikować pobrany plik XML **\<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers>** profilu i dodać tagi przed jego zaimportowaniem.

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

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>W przypadku sieci VPN użytkownika (punkt-lokacja) — ilu klientów jest obsługiwanych?

Każda brama P2S sieci VPN użytkownika ma dwa wystąpienia. Każde wystąpienie obsługuje do określonej liczby połączeń w miarę zmiany jednostki skalowania. Jednostka skalowania 1–3 obsługuje 500 połączeń, jednostka skalowania 4–6 obsługuje 1000 połączeń, jednostka skalowania 7–12 obsługuje 5000 połączeń, a jednostka skalowania 13–18 obsługuje do 10 000 połączeń.

Załóżmy na przykład, że użytkownik wybiera 1 jednostkę skalowania. Każda jednostka skalowania oznaczałaby wdrożenie bramy aktywne-aktywne, a każde z wystąpień (w tym przypadku 2) obsługiłoby do 500 połączeń. Ponieważ możesz uzyskać 500 połączeń * 2 na bramę, nie oznacza to, że planujesz 1000 zamiast 500 dla tej jednostki skalowania. Może być konieczne obsługę wystąpień, podczas których łączność dla dodatkowych 500 może zostać przerwana w przypadku przekroczenia zalecanej liczby połączeń. Ponadto należy zaplanować przestoje w przypadku podjęcia decyzji o skalowania w górę lub w dół jednostki skalowania lub zmianie konfiguracji punkt-lokacja w bramie sieci VPN.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Jaka jest różnica między bramą sieci wirtualnej platformy Azure (VPN Gateway) a bramą Azure Virtual WAN SIECI VPN?

Wirtualna sieć WAN zapewnia łączność typu lokacja-lokacja w dużej skali oraz oferuje przepływność, skalowalność i łatwość użycia. Po połączeniu lokacji z bramą Virtual WAN SIECI VPN różni się ona od zwykłej bramy sieci wirtualnej korzystającej z typu bramy "VPN". Podobnie podczas łączenia obwodu usługi ExpressRoute z koncentratorem usługi Virtual WAN jest używany inny zasób bramy usługi ExpressRoute niż zwykła brama sieci wirtualnej korzystająca z typu bramy "ExpressRoute". 

Virtual WAN obsługuje zagregowaną przepływność do 20 Gb/s dla sieci VPN i usługi ExpressRoute. Virtual WAN ma również automatyzację łączności z ekosystemem partnerów urządzeń oddziałów CPE. Urządzenia gałęzi CPE mają wbudowaną automatyzację, która automatycznie aprowizuje i łączy się z Azure Virtual WAN. Te urządzenia są dostępne w rozwijającym się ekosystemie partnerów sieci SD-WAN i VPN. Zobacz [listę preferowanych partnerów](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Czym różni Virtual WAN od bramy sieci wirtualnej platformy Azure?

Sieć VPN bramy sieci wirtualnej jest ograniczona do 30 tuneli. W przypadku połączeń należy używać usługi Virtual WAN dla sieci VPN na dużą skalę. Można połączyć maksymalnie 1000 połączeń oddziałów na region (koncentrator wirtualny) z agregacją 20 Gb/s na koncentrator. Połączenie to tunel typu aktywny-aktywny od lokalnego urządzenia sieci VPN do koncentratora wirtualnego. Możesz mieć jeden koncentrator na region, co oznacza, że możesz połączyć ponad 1000 gałęzi między centrami.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Co to jest jednostka skalowania Virtual WAN bramy?

Jednostka skalowania to jednostka zdefiniowana w celu wyboru zagregowanej przepływności bramy w koncentratorze wirtualnym. 1 jednostka skalowania sieci VPN = 500 Mb/s. 1 jednostka skalowania expressRoute = 2 Gb/s. Przykład: 10 jednostek skalowania sieci VPN oznaczałoby 500 Mb/s * 10 = 5 Gb/s

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Którzy dostawcy urządzeń (Virtual WAN partnerów) są obsługiwani?

Obecnie wielu partnerów obsługuje w pełni zautomatyzowane środowisko usługi Virtual WAN. Aby uzyskać więcej informacji, zobacz [Virtual WAN partners (Partnerzy wirtualnej sieci WAN)](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Jakie są kroki automatyzacji dla partnerów usługi Virtual WAN?

Aby uzyskać informacje o krokach automatyzacji dla partnerów, zobacz [Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) (Automatyzacja dla partnerów usługi Virtual WAN).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Czy muszę korzystać z urządzenia preferowanego przez partnera?

Nie. Możesz używać dowolnego urządzenia obsługującego sieć VPN, które spełnia wymagania platformy Azure pod kątem obsługi protokołów IKEv2/IKEv1 IPsec. Virtual WAN oferuje również rozwiązania partnerskie CPE, które automatyzują łączność z usługą Azure Virtual WAN co ułatwia konfigurowanie połączeń sieci VPN IPsec na dużą skalę.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Jak partnerzy wirtualnej sieci WAN automatyzują połączenie z wirtualną siecią WAN platformy Azure?

Zdefiniowane programowo rozwiązania w zakresie łączności zwykle umożliwiają zarządzanie urządzeniami oddziału przy użyciu kontrolera lub centrum aprowizacji urządzeń. Kontroler może zautomatyzować połączenie z wirtualną siecią WAN platformy Azure za pomocą interfejsów API platformy Azure. Automatyzacja obejmuje przekazywanie informacji o gałęzi, pobieranie konfiguracji platformy Azure, konfigurowanie tuneli IPsec do usługi Azure Virtual Hubs oraz automatyczne konfigurowanie łączności z urządzeniem gałęzi do usługi Azure Virtual WAN. Jeśli masz setki oddziałów, nawiązywanie połączenia przy użyciu partnerów programu Virtual WAN CPE jest łatwe, ponieważ środowisko dołączania nie wymaga konfigurowania i zarządzania łącznością IPsec na dużą skalę. Aby uzyskać więcej informacji, zobacz [automatyzację Virtual WAN partnerów.](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Co zrobić, jeśli urządzenie, z których korzystam, nie znajduje się na Virtual WAN partnerów? Czy nadal mogę używać go do nawiązywania połączenia z Azure Virtual WAN VPN?

Tak, o ile urządzenie obsługuje protokołu IKEv1 lub IKEv2 protokołu IPsec. Virtual WAN automatyzują łączność z urządzenia do punktów końcowych sieci VPN platformy Azure. Oznacza to automatyzację kroków, takich jak "przekazywanie informacji o gałęziach", "IPsec i konfiguracja" oraz "łączność". Ponieważ urządzenie nie pochodzi z ekosystemu Virtual WAN partnerów, musisz wykonać wiele pracy, aby ręcznie pobrać konfigurację platformy Azure i zaktualizować urządzenie w celu skonfigurowania łączności IPsec.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>W jaki sposób partnerzy, którzy nie są wymienieni na liście partnerów uruchomienia, mogą dołączyć do programu?

Wszystkie wirtualne interfejsy API sieci WAN są otwartymi interfejsami API. Możesz przejść do dokumentacji dotyczącej [automatyzacji Virtual WAN partnerów,](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) aby ocenić możliwości techniczne. Idealny partner powinien dysponować urządzeniem, które można aprowizować na potrzeby połączeń IKEv1 lub IKEv2 IPSec. Gdy firma zakończy pracę z automatyzacją swojego urządzenia CPE zgodnie z powyższymi wytycznymi dotyczącymi automatyzacji, możesz się s dowiedzieć, aby znaleźć się tutaj Łączność za pośrednictwem azurevirtualwan@microsoft.com [partnerów.]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners) Jeśli jesteś klientem, który chce, aby określone rozwiązanie firmowe zostało wymienione jako partner Virtual WAN, skontaktuj się z Virtual WAN, wysyłając wiadomość e-mail na adres azurevirtualwan@microsoft.com .

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>W jaki Virtual WAN urządzenia SD-WAN?

Virtual WAN automatyzują łączność IPsec z punktami końcowymi sieci VPN platformy Azure. Jeśli partner Virtual WAN jest dostawcą sieci SD-WAN, zakłada się, że kontroler SD-WAN zarządza automatyzacją i łącznością IPsec z punktami końcowymi sieci VPN platformy Azure. Jeśli urządzenie SD-WAN wymaga własnego punktu końcowego zamiast sieci VPN platformy Azure dla dowolnej własnościowej funkcji SD-WAN, możesz wdrożyć punkt końcowy SD-WAN w sieci wirtualnej platformy Azure i współistnieć z Azure Virtual WAN.

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Ile urządzeń sieci VPN może połączyć się z jednym koncentratorem?

Na koncentrator wirtualny jest obsługiwanych maksymalnie 1000 połączeń. Każde połączenie składa się z czterech łączy, a każde połączenie obsługuje dwa tunele, które są w konfiguracji aktywne-aktywne. Tunele są przerywane w bramie sieci VPN koncentratora wirtualnego platformy Azure. Linki reprezentują link fizycznego dostawcy internetowego na urządzeniu gałęzi/sieci VPN.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Co to jest połączenie gałęzi z Azure Virtual WAN?

Połączenie z gałęzi lub urządzenia sieci VPN do sieci Azure Virtual WAN to połączenie sieci VPN, które łączy wirtualną lokację sieci VPN i usługę Azure VPN Gateway w koncentratorze wirtualnym.

### <a name="what-happens-if-the-on-premises-vpn-device-only-has-1-tunnel-to-an-azure-virtual-wan-vpn-gateway"></a>Co się stanie, jeśli lokalne urządzenie sieci VPN ma tylko 1 tunel do bramy Azure Virtual WAN SIECI VPN?

Połączenie Azure Virtual WAN składa się z 2 tuneli. Brama Virtual WAN sieci VPN jest wdrażana w koncentratorze wirtualnym w trybie aktywny-aktywny, co oznacza, że istnieją oddzielne tunele od urządzeń lokalnych kończących się na oddzielnych wystąpieniach. Jest to zalecenie dla wszystkich użytkowników. Jeśli jednak użytkownik wybierze tylko 1 tunel do jednego z wystąpień bramy sieci VPN usługi Virtual WAN, jeśli z jakiegoś powodu (konserwacja, poprawki itp.) wystąpienie bramy zostanie przeniesione w tryb offline, tunel zostanie przeniesiony do pomocniczego aktywnego wystąpienia i użytkownik może doświadczyć ponownego połączenia. Sesje protokołu BGP nie będą przechodzić między wystąpieniami.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Czy lokalne urządzenie sieci VPN może łączyć się z wieloma centrami?

Tak. Przepływ ruchu, po jego rozpoczęciem, jest przepływem od urządzenia lokalnego do najbliższej krawędzi sieci firmy Microsoft, a następnie do koncentratora wirtualnego.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Czy są dostępne nowe zasoby usługi Resource Manager dla wirtualnej sieci WAN?
  
Tak, Virtual WAN ma nowe Resource Manager zasobów. Aby uzyskać więcej informacji, zobacz [Omówienie](../articles/virtual-wan/virtual-wan-about.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Czy mogę wdrożyć moje ulubione wirtualne urządzenie sieciowe (w sieci wirtualnej wirtualnego urządzenia sieciowego) i używać go za pomocą wirtualnej sieci WAN platformy Azure?

Tak, można połączyć swoje ulubione wirtualne urządzenie sieciowe (WUS) sieci wirtualnej z usługą Azure Virtual WAN.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Czy można utworzyć wirtualne urządzenie sieciowe w koncentratorze wirtualnym?

Wirtualnego urządzenia sieciowego (WUS) nie można wdrożyć wewnątrz koncentratora wirtualnego. Można jednak utworzyć go w sieci wirtualnej szprychy połączonej z koncentratorem wirtualnym i włączyć odpowiedni routing, aby kierować ruchem zgodnie z potrzebami.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Czy sieć wirtualna szprychy może mieć bramę sieci wirtualnej?

Nie. Sieć wirtualna szprychy nie może mieć bramy sieci wirtualnej, jeśli jest połączona z koncentratorem wirtualnym.

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>Czy istnieje obsługa protokołu BGP w łączności sieci VPN?

Tak. Protokół BGP jest obsługiwany. Podczas tworzenia lokacji sieci VPN można podać w nim parametry protokołu BGP. Oznacza to, że wszystkie połączenia utworzone na platformie Azure dla tej lokacji będą włączone dla protokołu BGP.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Czy są dostępne informacje na temat licencjonowania i cen wirtualnej sieci WAN?

Tak. Zobacz stronę z [cennikiem](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Czy istnieje możliwość skonstruowania sieci usługi Azure Virtual WAN przy użyciu szablonu usługi Resource Manager?

Prostą konfigurację jednego Virtual WAN z jednym centrum i jedną lokacją vpnsite można utworzyć przy użyciu szablonu [szybkiego startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Virtual WAN jest przede wszystkim usługą REST lub usługą sterowanej przez portal.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Czy sieci wirtualne szprych połączone z koncentratorem wirtualnym mogą komunikować się ze sobą (tranzyt V2V)?

Tak. Standardowa Virtual WAN łączność przechodnia między sieciami wirtualnmi za pośrednictwem centrum Virtual WAN, z które są połączone sieci wirtualne. W terminologii usługi Virtual WAN te ścieżki są oznaczane jako Virtual WAN "lokalny tranzyt sieci wirtualnej" dla sieci wirtualnych połączonych z koncentratorem usługi Virtual Wan w jednym regionie oraz "globalny tranzyt sieci wirtualnej Virtual WAN" dla sieci wirtualnych połączonych za pośrednictwem wielu centrów usługi Virtual WAN w co najmniej dwóch regionach.

W niektórych scenariuszach sieci wirtualne szprych mogą być [](../articles/virtual-network/virtual-network-peering-overview.md) również bezpośrednio równorzędne za pomocą komunikacji równorzędnej sieci wirtualnych oprócz lokalnego lub globalnego przesyłania Virtual WAN wirtualnej. W takim przypadku komunikacja równorzędna sieci wirtualnych ma pierwszeństwo przed połączeniem przechodnim za pośrednictwem Virtual WAN wirtualnej.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Czy łączność między oddziałami jest dozwolona w usłudze Virtual WAN?

Tak. Łączność między oddziałami jest dostępna w usłudze Virtual WAN. Gałąź ma zastosowanie koncepcyjnie do lokacji sieci VPN, obwodów usługi ExpressRoute lub użytkowników sieci VPN typu punkt-lokacja/użytkownik. Włączanie funkcji branch-to-branch jest domyślnie włączone i można je znaleźć w ustawieniach **konfiguracji sieci** WAN. Dzięki temu gałęzie/użytkownicy sieci VPN mogą łączyć się z innymi gałęziami sieci VPN, a łączność tranzytowa jest również włączona między użytkownikami sieci VPN i usługi ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Czy ruch między oddziałami przechodzi przez Azure Virtual WAN?

Tak. Ruch między oddziałami przechodzi przez Azure Virtual WAN.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Czy Virtual WAN expressRoute z każdej lokacji?

Nie. Virtual WAN nie wymaga expressRoute z każdej lokacji. Lokacje mogą być połączone z siecią dostawcy przy użyciu obwodu usługi ExpressRoute. W przypadku lokacji połączonych za pomocą usługi ExpressRoute z koncentratorem wirtualnym i sieci VPN IPsec do tego samego koncentratora koncentrator wirtualny zapewnia łączność tranzytową między siecią VPN i użytkownikiem usługi ExpressRoute.

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>Czy istnieje limit przepływności sieci lub połączenia podczas korzystania z Azure Virtual WAN?

Przepływność sieci jest na usługę w koncentratorze wirtualnej sieci WAN. Chociaż możesz mieć tyle wirtualnych sieci WAN, ile chcesz, każda sieć Virtual WAN 1 koncentrator na region. W każdym koncentratorze przepływność agregacji sieci VPN wynosi do 20 Gb/s, zagregowana przepływność usługi ExpressRoute do 20 Gb/s, a zagregowana przepływność sieci VPN użytkownika/sieci VPN typu punkt-lokacja wynosi do 20 Gb/s. Router w koncentratorze wirtualnym obsługuje do 50 Gb/s dla przepływów ruchu między sieciami wirtualnymi i przyjmuje łącznie obciążenie 2000 maszyn wirtualnych we wszystkich sieciach wirtualnych połączonych z jednym koncentratorem wirtualnym.

Gdy lokacje sieci VPN łączą się z koncentratorem, robią to za pomocą połączeń. Virtual WAN obsługuje do 1000 połączeń lub 2000 tuneli IPsec na koncentrator wirtualny. Gdy użytkownicy zdalni łączą się z koncentratorem wirtualnym, łączą się z bramą sieci VPN typu P2S, która obsługuje do 10 000 użytkowników w zależności od jednostki skalowania (przepustowości) wybranej dla bramy sieci VPN typu P2S w koncentratorze wirtualnym.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Jaka jest łączna przepływność sieci VPN tunelu VPN i połączenia?

Łączna przepływność sieci VPN koncentratora wynosi do 20 Gb/s na podstawie wybranej jednostki skalowania bramy sieci VPN. Przepływność jest współdzielona przez wszystkie istniejące połączenia. Każdy tunel w połączeniu może obsługiwać do 1 Gb/s.

### <a name="can-i-use-nat-t-on-my-vpn-connections"></a>Czy mogę używać nat-T w połączeniach sieci VPN?

Tak, przechodzenie nat (NAT-T) jest obsługiwane. Brama Virtual WAN SIECI VPN NIE będzie wykonywać żadnych funkcji podobnych do NAT w pakietach wewnętrznych do/z tuneli IPsec. W tej konfiguracji upewnij się, że urządzenie lokalne inicjuje tunel IPsec.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>Nie widzę ustawienia 20 Gb/s dla koncentratora wirtualnego w portalu. Jak mogę to skonfigurować?

Przejdź do bramy sieci VPN wewnątrz centrum w portalu, a następnie kliknij jednostkę skalowania, aby zmienić ją na odpowiednie ustawienie.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Czy Virtual WAN umożliwia urządzeniu lokalnego równoległe korzystanie z wielu dostawców usług internetowych, czy zawsze jest to pojedynczy tunel VPN?

Rozwiązania urządzeń lokalnych mogą stosować zasady ruchu w celu kierowania ruchu przez wiele tuneli do centrum Azure Virtual WAN (bramy sieci VPN w koncentratorze wirtualnym).

### <a name="what-is-global-transit-architecture"></a>Co to jest architektura tranzytu globalnego?

Aby uzyskać informacje o globalnej architekturze tranzytowej, zobacz Architektura globalnej [sieci tranzytowej i Virtual WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>W jaki sposób ruch jest kierowany w sieci szkieletowej platformy Azure?

Ruch jest zgodny ze wzorcem: urządzenie-gałąź ->ISP->Microsoft network edge->Microsoft DC (sieć wirtualna piasty)->Microsoft network edge->ISP->branch device

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Które elementy tego modelu muszą znajdować się w każdej lokacji? Czy wystarczy tylko połączenie internetowe?

Tak. Połączenie internetowe i urządzenie fizyczne, które obsługuje protokołu IPsec, najlepiej od naszych zintegrowanych Virtual WAN [partnerów.](../articles/virtual-wan/virtual-wan-locations-partners.md) Opcjonalnie możesz ręcznie zarządzać konfiguracją i łącznością z platformą Azure z preferowanego urządzenia.

### <a name="how-do-i-enable-default-route-00000-for-a-connection-vpn-expressroute-or-virtual-network"></a>Jak mogę włączyć trasę domyślną (0.0.0.0/0) dla połączenia (VPN, ExpressRoute lub Virtual Network)?

Koncentrator wirtualny może propagować nauczoną trasę domyślną do sieci wirtualnej/sieci VPN typu lokacja-lokacja/połączenia usługi ExpressRoute, jeśli flaga jest włączona w połączeniu. Ta flaga jest widoczna, gdy użytkownik edytuje połączenie sieci wirtualnej, połączenie sieci VPN lub połączenie usługi ExpressRoute. Domyślnie ta flaga jest wyłączona, gdy lokacja lub obwód usługi ExpressRoute jest połączony z koncentratorem. Jest ona domyślnie włączona po dodaniu połączenia sieci wirtualnej w celu połączenia sieci wirtualnej z koncentratorem wirtualnym.

Trasa domyślna nie pochodzi z centrum Virtual WAN sieciowego. Trasa domyślna jest propagowana, jeśli jest już nauczona przez centrum usługi Virtual WAN w wyniku wdrożenia zapory w koncentratorze lub jeśli w innej połączonej lokacji włączono tunelowanie wymuszone. Trasa domyślna nie jest propagowana między koncentratorami (między piastami).

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>W jaki sposób koncentrator wirtualny w wirtualnej sieci WAN wybiera najlepszą ścieżkę dla trasy z wielu centrów?

Jeśli koncentrator wirtualny uczy się tej samej trasy z wielu zdalnych koncentratorów, kolejność, w jakiej decyduje, jest następująca:

1. Dopasowanie najdłuższego prefiksu.
1. Trasy lokalne za pośrednictwem usługi InterHub (koncentrator wirtualny przypisuje numer 65520–65520 dla usługi InterHub AS).
1. Trasy statyczne za pośrednictwem protokołu BGP: Jest to kontekst decyzji podejmowanej przez router koncentratora wirtualnego. Jeśli jednak twórca decyzji jest bramą sieci VPN, w której lokacja anonsuje trasy za pośrednictwem protokołu BGP lub udostępnia statyczne prefiksy adresów, trasy statyczne mogą być preferowane w witrynie niż trasy protokołu BGP.
1. Usługa ExpressRoute (ER) za pośrednictwem sieci VPN: usługa ER jest preferowana niż sieć VPN, gdy kontekst jest koncentratorem lokalnym. Łączność tranzytowa między obwodami usługi ExpressRoute jest dostępna tylko za pośrednictwem Global Reach. W związku z tym w scenariuszach, w których obwód usługi ExpressRoute jest połączony z jednym koncentratorem i istnieje inny obwód usługi ExpressRoute połączony z innym koncentratorem za pomocą połączenia sieci VPN, sieć VPN może być preferowana w scenariuszach między koncentratorami.
1. Długość ścieżki AS.

### <a name="does-the-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>Czy centrum Virtual WAN umożliwia łączność między obwodami usługi ExpressRoute?

Tranzyt między ER-to-ER jest zawsze za pośrednictwem zasięgu globalnego. Bramy koncentratora wirtualnego są wdrażane w regionach dc lub azure. Gdy dwa obwody usługi ExpressRoute łączą się za pośrednictwem zasięgu globalnego, nie ma potrzeby, aby ruch docierał przez cały czas z routerów brzegowych do kontrolera domeny koncentratora wirtualnego.

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-expressroute-circuits-or-vpn-connections"></a>Czy istnieje pojęcie wagi w obwodach Azure Virtual WAN ExpressRoute lub połączeniach sieci VPN

Gdy wiele obwodów usługi ExpressRoute jest połączonych z koncentratorem wirtualnym, waga routingu w połączeniu zapewnia mechanizm, który umożliwia ujście usługi ExpressRoute w koncentratorze wirtualnym w celu preferowania jednego obwodu od drugiego. Nie ma mechanizmu ustawiania wagi połączenia sieci VPN. Platforma Azure zawsze preferuje połączenie usługi ExpressRoute zamiast połączenia sieci VPN w ramach jednego centrum.

### <a name="does-virtual-wan-prefer-expressroute-over-vpn-for-traffic-egressing-azure"></a>Czy Virtual WAN preferuje usługę ExpressRoute zamiast sieci VPN w przypadku ruchu wychodzącego na platformie Azure

Tak. Virtual WAN preferuje usługę ExpressRoute zamiast sieci VPN w przypadku ruchu wychodzącego na platformie Azure.

### <a name="when-a-virtual-wan-hub-has-an-expressroute-circuit-and-a-vpn-site-connected-to-it-what-would-cause-a-vpn-connection-route-to-be-preferred-over-expressroute"></a>Jeśli koncentrator Virtual WAN ma obwód usługi ExpressRoute i lokację sieci VPN, co może spowodować, że trasa połączenia sieci VPN będzie preferowana w witrynie ExpressRoute?

Gdy obwód usługi ExpressRoute jest połączony z koncentratorem wirtualnym, routery brzegowe firmy Microsoft są pierwszym węzłem do komunikacji między środowiskiem lokalnym i platformą Azure. Te routery brzegowe komunikują się z bramami usługi Virtual WAN ExpressRoute, które z kolei uczą się tras z routera koncentratora wirtualnego, który kontroluje wszystkie trasy między dowolnymi bramami w Virtual WAN. Routery brzegowe firmy Microsoft przetwarzają trasy usługi ExpressRoute koncentratora wirtualnego z wyższymi preferencjami niż trasy wyuczonych ze środowiska lokalnego.

Z jakiegokolwiek powodu, jeśli połączenie sieci VPN staje się podstawowym nośnikiem dla koncentratora wirtualnego do uczenia się tras (np. scenariusze trybu failover między usługą ExpressRoute i siecią VPN), chyba że lokacja sieci VPN ma dłuższą długość ścieżki AS, koncentrator wirtualny będzie nadal udostępniać trasy poznane przez sieć VPN za pomocą bramy usługi ExpressRoute. Powoduje to, że routery brzegowe firmy Microsoft preferują trasy sieci VPN zamiast tras lokalnych.

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>Gdy dwa koncentratory (koncentrator 1 i 2) są połączone, a obwód usługi ExpressRoute jest połączony w sposób bow-tie z obu koncentratorami, jaka jest ścieżka dla sieci wirtualnej połączonej z koncentratorem 1 w celu połączenia z siecią wirtualną w koncentratorze 2?

Bieżące zachowanie to preferowanie ścieżki obwodu usługi ExpressRoute zamiast połączenia piasta-piasta w przypadku łączności między sieciami wirtualnimi. Nie jest to jednak zachęcane w przypadku konfiguracji wirtualnej sieci WAN. Zespół Virtual WAN pracuje nad poprawek, aby włączyć preferencje dotyczące piasty i piasty w ścieżce usługi ExpressRoute. Zaleca się, aby wiele obwodów usługi ExpressRoute (różnych dostawców) łączyło się z jednym koncentratorem i używało łączności typu piasta-piasta udostępnianej przez usługę Virtual WAN dla przepływów ruchu między regionami.

### <a name="can-hubs-be-created-in-different-resource-group-in-virtual-wan"></a>Czy centra można tworzyć w innej grupie zasobów w Virtual WAN?

Tak. Ta opcja jest obecnie dostępna tylko za pośrednictwem programu PowerShell. Portal Virtual WAN wymaga, aby koncentratory znajdują się w tej samej grupie zasobów co Virtual WAN zasobów.

### <a name="what-is-the-recommended-hub-address-space-during-hub-creation"></a>Jaka jest zalecana przestrzeń adresowa centrum podczas tworzenia centrum?

Zalecana przestrzeń Virtual WAN centrum danych to /23. Virtual WAN hub przypisuje podsieci do różnych bram (expressroute, sieć VPN typu lokacja-lokacja, sieć VPN typu punkt-lokacja, sieć Azure Firewall, router koncentratora wirtualnego). W scenariuszach, w których urządzenia WUS są wdrażane wewnątrz koncentratora wirtualnego, dla wystąpień urządzeń WUS zwykle jest stosowane urządzenie /28. Jeśli jednak użytkownik aprowizował wiele urządzeń WUS, można przypisać podsieć /27. W związku z tym mając na uwadze przyszłą architekturę, podczas gdy centra Virtual WAN są wdrażane z minimalnym rozmiarem /24, zalecana przestrzeń adresowa centrum w czasie tworzenia dla danych wejściowych użytkownika to /23.

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Czy obsługa protokołu IPv6 jest Virtual WAN?

Protokół IPv6 nie jest obsługiwany w centrum Virtual WAN i jego bramach. Jeśli masz sieć wirtualną, która obsługuje protokół IPv4 i IPv6, i chcesz połączyć sieć wirtualną z siecią Virtual WAN, ten scenariusz nie jest obecnie obsługiwany.

W scenariuszu sieci VPN typu punkt-lokacja z przerwą w dostępie do Internetu za pośrednictwem usługi Azure Firewall prawdopodobnie trzeba będzie wyłączyć łączność IPv6 na urządzeniu klienckim, aby wymusić ruch do centrum Virtual WAN sieci. Jest to spowodowane tym, że nowoczesne urządzenia domyślnie używają adresów IPv6.

### <a name="what-is-the-recommended-api-version-to-be-used-by-scripts-automating-various-virtual-wan-functionalities"></a>Jaka jest zalecana wersja interfejsu API do stosowania przez skrypty automatyzujące różne Virtual WAN funkcjonalności?

Wymagana jest minimalna wersja z 05-01-2020 (1 maja 2020 r.).

### <a name="are-there-any-virtual-wan-limits"></a>Czy istnieją jakieś Virtual WAN limity?

Zobacz [sekcję Virtual WAN limitów](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#virtual-wan-limits) na stronie Limity subskrypcji i usług.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Jakie są różnice między typami Virtual WAN (Podstawowa i Standardowa)?

Zobacz [podstawowe i standardowe wirtualne sieci WAN.](../articles/virtual-wan/virtual-wan-about.md#basicstandard) Aby uzyskać informacje o cenach, zobacz [stronę Cennik.](https://azure.microsoft.com/pricing/details/virtual-wan/)

### <a name="does-virtual-wan-store-customer-data"></a>Czy Virtual WAN dane klientów?

Nie. Virtual WAN nie przechowuje żadnych danych klienta.

### <a name="are-there-any-managed-service-providers-that-can-manage-virtual-wan-for-users-as-a-service"></a>Czy istnieją dostawcy usług zarządzanych, którzy mogą zarządzać Virtual WAN dla użytkowników jako usługi?

Tak. Aby uzyskać listę rozwiązań dostawcy usług zarządzanych (MSP) włączonych za pośrednictwem usługi Azure Marketplace, zobacz oferty Azure Marketplace przez Azure Networking [MSP.](../articles/networking/networking-partners-msp.md#msp)

### <a name="how-does-virtual-wan-hub-routing-differ-from-azure-route-server-in-a-vnet"></a>Czym różni Virtual WAN Hub od usługi Azure Route Server w sieci wirtualnej?

Serwer usługi Azure Route Server udostępnia usługę komunikacji równorzędnej Border Gateway Protocol (BGP), która może być używana przez urządzenia WUS (wirtualne urządzenie sieciowe) do uczenia tras z serwera tras w sieci wirtualnej koncentratora diy. routing Virtual WAN zapewnia wiele możliwości, w tym routing tranzytowy między sieciami wirtualnym, niestandardowy routing, niestandardowe skojarzenie i propagacja tras, a także usługę piasty w pełni z siatki bezdotykową wraz z usługami łączności usługi ExpressRoute, sieci VPN lokacji, sieci VPN typu użytkownik zdalny/sieć VPN typu punkt-lokacja w dużej skali oraz możliwościami bezpiecznego koncentratora (Azure Firewall). Po nawiązaniu komunikacji równorzędnej BGP między urządzeniem WUS i serwerem usługi Azure Route Server można anonsować adresy IP z urządzenia WUS do sieci wirtualnej. W przypadku wszystkich zaawansowanych możliwości routingu, takich jak routing tranzytowy, routing niestandardowy itp., można użyć Virtual WAN routingu.
