---
title: Azure Virtual Network — często zadawane pytania
titlesuffix: Azure Virtual Network
description: Odpowiedzi na najczęściej zadawane pytania dotyczące Microsoft Azure sieci wirtualnych.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/26/2020
ms.author: kumud
ms.openlocfilehash: d676d891683cc11dd8c1999c26464373d17e97be
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932026"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Często zadawane pytania dotyczące sieci wirtualnych platformy Azure

## <a name="virtual-network-basics"></a>Podstawowe informacje Virtual Network

### <a name="what-is-an-azure-virtual-network-vnet"></a>Co to jest usługa Azure Virtual Network (VNet)?
Usługa Azure Virtual Network (VNet) to reprezentacja własnej sieci w chmurze. Jest logiczną izolacją chmury Azure przeznaczoną dla Twojej subskrypcji. Za pomocą sieci wirtualnych można udostępniać wirtualne sieci prywatne (VPN) na platformie Azure i zarządzać nimi, opcjonalnie połączyć sieci wirtualnych z innymi sieci wirtualnych na platformie Azure lub z lokalną infrastrukturą INFORMATYCZNą w celu tworzenia rozwiązań hybrydowych lub obejmujących wiele lokalizacji. Każda utworzona sieć wirtualna ma swój własny blok CIDR i może być połączona z innymi sieci wirtualnych i sieciami lokalnymi, o ile bloki CIDR nie nakładają się na siebie. Masz również kontrolę nad ustawieniami serwera DNS dla sieci wirtualnych i segmentacją sieci wirtualnej na podsieci.

Użyj sieci wirtualnych, aby:

* Utwórz dedykowaną sieć wirtualną obsługującą tylko chmurę prywatną. Czasami nie jest wymagane, aby Twoje rozwiązanie nie wymagało konfiguracji obejmującej wiele lokalizacji. Podczas tworzenia sieci wirtualnej usługi i maszyny wirtualne w sieci wirtualnej mogą komunikować się bezpośrednio i bezpiecznie ze sobą w chmurze. Nadal można skonfigurować połączenia punktów końcowych dla maszyn wirtualnych i usług, które wymagają komunikacji z Internetem w ramach rozwiązania.

* Bezpieczne rozszeranie centrum danych. Za pomocą sieci wirtualnych można tworzyć tradycyjne sieci VPN typu lokacja-lokacja (S2S) w celu bezpiecznego skalowania pojemności centrum danych. Sieci VPN S2S używają protokołu IPSEC do zapewnienia bezpiecznego połączenia między firmową bramą sieci VPN i platformą Azure.

* Włącz scenariusze chmury hybrydowej. Sieci wirtualnych zapewniają elastyczność umożliwiającą obsługę szeregu scenariuszy chmury hybrydowej. Aplikacje oparte na chmurze można bezpiecznie połączyć z dowolnym typem systemu lokalnego, takim jak Komputery mainframe i systemy UNIX.

### <a name="how-do-i-get-started"></a>Jak rozpocząć?
Przejdź do [dokumentacji sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/) , aby rozpocząć pracę. Ta zawartość zawiera przegląd i informacje dotyczące wdrażania dla wszystkich funkcji sieci wirtualnej.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Czy mogę używać sieci wirtualnych bez połączenia między różnymi lokalizacjami?
Tak. Możesz użyć sieci wirtualnej bez łączenia jej z Twoim lokalem. Można na przykład uruchomić system Microsoft Windows Server Active Directory kontrolery domeny i farmy programu SharePoint wyłącznie w sieci wirtualnej platformy Azure.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Czy mogę przeprowadzić optymalizację sieci WAN między sieci wirtualnychą a siecią wirtualną i lokalnym centrum danych?
Tak. [Wirtualne urządzenie sieciowe optymalizacji sieci WAN](https://azuremarketplace.microsoft.com/en-us/marketplace/?term=wan%20optimization) można wdrożyć od kilku dostawców za pośrednictwem portalu Azure Marketplace.

## <a name="configuration"></a>Konfiguracja

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Jakie narzędzia są używane do tworzenia sieci wirtualnej?
Aby utworzyć lub skonfigurować sieć wirtualną, można użyć następujących narzędzi:

* Azure Portal
* PowerShell
* Interfejs wiersza polecenia platformy Azure
* Plik konfiguracji sieci (netcfg-tylko klasyczny sieci wirtualnych). Zapoznaj się z artykułem [Konfigurowanie sieci wirtualnej przy użyciu pliku konfiguracji sieciowej](virtual-networks-using-network-configuration-file.md) .

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Jakie zakresy adresów mogę użyć w mojej sieci wirtualnych?
Zalecamy używanie zakresów adresów wyliczonych w [dokumencie RFC 1918](https://tools.ietf.org/html/rfc1918), które zostały odłożone przez grupę IETF dla prywatnych przestrzeni adresowych bez obsługi routingu:
* 10.0.0.0-10.255.255.255 (10/8 prefiks)
* 172.16.0.0-172.31.255.255 (prefiks 172.16/12)
* 192.168.0.0-192.168.255.255 (prefiks 192.168/16)

Inne przestrzenie adresowe mogą współdziałać, ale mogą mieć niepożądane skutki uboczne.

Ponadto nie można dodać następujących zakresów adresów:
* 224.0.0.0/4 (multiemisja)
* 255.255.255.255/32 (emisja)
* 127.0.0.0/8 (sprzężenie zwrotne)
* 169.254.0.0/16 (połączenie lokalne)
* 168.63.129.16/32 (wewnętrzna usługa DNS)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Czy mogę mieć publiczne adresy IP w mojej sieci wirtualnych?
Tak. Aby uzyskać więcej informacji na temat zakresów publicznych adresów IP, zobacz [Tworzenie sieci wirtualnej](manage-virtual-network.md#create-a-virtual-network). Publiczne adresy IP nie są bezpośrednio dostępne z Internetu.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Czy istnieje ograniczenie liczby podsieci w sieci wirtualnej?
Tak. Aby uzyskać szczegółowe informacje, zobacz [limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) . Przestrzenie adresowe podsieci nie mogą nakładać się na siebie nawzajem.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Czy istnieją jakieś ograniczenia dotyczące używania adresów IP w tych podsieciach?
Tak. Platforma Azure rezerwuje 5 adresów IP dla każdej podsieci. Są to x. x. x. 0-x. x. x. 3 i ostatni adres podsieci. x. x. x. 1-x. x. x. 3 jest zarezerwowane w każdej podsieci dla usług platformy Azure.   
- x. x. x. 0: adres sieciowy
- x. x. x. 1: zarezerwowane przez platformę Azure dla bramy domyślnej
- x. x. x. 2, x. x. x. 3: zarezerwowane przez platformę Azure do mapowania adresów IP Azure DNS na przestrzeń wirtualną
- x. x. x. 255: adres emisji sieci

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Jak mały i jak duży może być sieci wirtualnych i podsieci?
Najmniejsza obsługiwana podsieć IPv4 to/29, a największe to/8 (przy użyciu definicji podsieci CIDR).  Podsieci IPv6 muszą mieć rozmiar dokładnie/64.  

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Czy mogę przenieść moje sieci VLAN na platformę Azure przy użyciu usługi sieci wirtualnych?
Nie. Sieci wirtualnych to nakładki warstwy 3. Platforma Azure nie obsługuje żadnej semantyki warstwy 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Czy mogę określić niestandardowe zasady routingu w moich sieci wirtualnychach i podsieciach?
Tak. Można utworzyć tabelę tras i skojarzyć ją z podsiecią. Aby uzyskać więcej informacji na temat routingu na platformie Azure, zobacz [Omówienie routingu](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Czy sieci wirtualnych obsługuje multiemisję czy emisję?
Nie. Multiemisja i emisja nie są obsługiwane.

### <a name="what-protocols-can-i-use-within-vnets"></a>Jakich protokołów mogę używać w ramach sieci wirtualnych?
W programie sieci wirtualnych można używać protokołów TCP, UDP i ICMP protokołu TCP/IP. Emisja pojedyncza jest obsługiwana w sieci wirtualnych, z wyjątkiem Dynamic Host Configuration Protocol (DHCP) za pośrednictwem emisji pojedynczej (port źródłowy UDP/68/port docelowy UDP/67) i portu źródłowego UDP 65330, który jest zarezerwowany dla hosta. Pakiety multiemisji, emisji, pakiety IP w IP hermetyzowane i pakietów GRE (Generic Routing Encapsulation) są blokowane w ramach sieci wirtualnych. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Czy mogę wysłać polecenie ping do moich domyślnych routerów w sieci wirtualnej?
Nie.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Czy mogę użyć polecenia tracert do diagnozowania łączności?
Nie.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Czy mogę dodać podsieci po utworzeniu sieci wirtualnej?
Tak. Podsieci można dodać do sieci wirtualnych w dowolnym momencie, o ile zakres adresów podsieci nie jest częścią innej podsieci i dostępne miejsce pozostawiono w zakresie adresów sieci wirtualnej.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Czy mogę zmienić rozmiar podsieci po utworzeniu?
Tak. Możesz dodawać, usuwać, rozszerzać lub zmniejszać podsieć, jeśli nie wdrożono w niej żadnych maszyn wirtualnych ani usług.

### <a name="can-i-modify-vnet-after-i-created-them"></a>Czy mogę zmodyfikować sieć wirtualną po jej utworzeniu?
Tak. Bloki CIDR używane przez sieć wirtualną można dodawać, usuwać i modyfikować.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Czy w przypadku korzystania z usług w sieci wirtualnej można nawiązać połączenie z Internetem?
Tak. Wszystkie usługi wdrożone w sieci wirtualnej mogą łączyć ruch wychodzący z Internetem. Aby dowiedzieć się więcej o wychodzących połączeniach internetowych na platformie Azure, zobacz [połączenia wychodzące](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Jeśli chcesz nawiązać połączenie przychodzące z zasobem wdrożonym za pomocą Menedżer zasobów, do zasobu musi być przypisany publiczny adres IP. Aby dowiedzieć się więcej o publicznych adresach IP, zobacz [publiczne adresy IP](virtual-network-public-ip-address.md). Każda usługa w chmurze platformy Azure wdrożona na platformie Azure ma publicznie przypisany adres VIP. Należy zdefiniować wejściowe punkty końcowe dla PaaS ról i punktów końcowych dla maszyn wirtualnych, aby umożliwić tym usługom akceptowanie połączeń z Internetu.

### <a name="do-vnets-support-ipv6"></a>Czy sieci wirtualnych obsługuje protokół IPv6?
Tak, sieci wirtualnych może być tylko IPv4 lub podwójnym stosem (IPv4 + IPv6).  Aby uzyskać szczegółowe informacje, zobacz [Omówienie protokołu IPv6 dla sieci wirtualnych platformy Azure](./ipv6-overview.md).

### <a name="can-a-vnet-span-regions"></a>Czy mogą istnieć regiony zakresu sieci wirtualnej?
Nie. Sieć wirtualna jest ograniczona do jednego regionu. Sieć wirtualna obejmuje jednak strefy dostępności. Aby dowiedzieć się więcej o strefach dostępności, zobacz [Availability zones overview (Omówienie stref dostępności)](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Sieci wirtualne można łączyć w różnych regionach za pomocą komunikacji równorzędnej sieci wirtualnych. Aby uzyskać szczegółowe informacje, zobacz [Omówienie komunikacji równorzędnej sieci wirtualnych](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Czy mogę połączyć sieć wirtualną z inną siecią wirtualną na platformie Azure?
Tak. Można połączyć jedną sieć wirtualną z inną siecią wirtualną przy użyciu:
- **Komunikacja równorzędna sieci wirtualnych**: Aby uzyskać szczegółowe informacje, zobacz [Omówienie komunikacji równorzędnej VNET](virtual-network-peering-overview.md)
- **VPN Gateway platformy Azure**: Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie połączenia między sieciami wirtualnymi](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Rozpoznawanie nazw (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Jakie są moje Opcje DNS dla sieci wirtualnych?
Za pomocą tabeli decyzji na stronie [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień ról](virtual-networks-name-resolution-for-vms-and-role-instances.md) można przekierować wszystkie dostępne opcje systemu DNS.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Czy mogę określić serwery DNS dla sieci wirtualnej?
Tak. W ustawieniach sieci wirtualnej można określić adresy IP serwerów DNS. To ustawienie jest stosowane jako domyślne serwery DNS dla wszystkich maszyn wirtualnych w sieci wirtualnej.

### <a name="how-many-dns-servers-can-i-specify"></a>Ile serwerów DNS można określić?
Odwołania do [limitów platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Czy mogę zmodyfikować moje serwery DNS po utworzeniu sieci?
Tak. W dowolnym momencie możesz zmienić listę serwerów DNS dla sieci wirtualnej. W przypadku zmiany listy serwerów DNS należy wykonać odnowienie dzierżawy DHCP na wszystkich maszynach wirtualnych, których dotyczy ta sieć wirtualna, aby nowe ustawienia DNS zaczęły obowiązywać. W przypadku maszyn wirtualnych z systemem operacyjnym Windows można to zrobić, wpisując je `ipconfig /renew` bezpośrednio na maszynie wirtualnej. W przypadku innych typów systemów operacyjnych zapoznaj się z dokumentacją dotyczącą odnawiania dzierżawy DHCP dla określonego typu systemu operacyjnego. 

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Co to jest usługa DNS udostępniona przez platformę Azure i czy działa ona z usługą sieci wirtualnych?
System DNS udostępniany przez platformę Azure to wielodostępna usługa DNS oferowana przez firmę Microsoft. Platforma Azure rejestruje wszystkie maszyny wirtualne i wystąpienia roli usługi w chmurze w tej usłudze. Ta usługa zapewnia rozpoznawanie nazw według nazwy hosta dla maszyn wirtualnych i wystąpień ról zawartych w tej samej usłudze w chmurze oraz według nazwy FQDN dla maszyn wirtualnych i wystąpień roli w tej samej sieci wirtualnej. Aby dowiedzieć się więcej na temat usługi DNS, zobacz [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień ról Cloud Services](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Istnieje ograniczenie do pierwszych 100 usług w chmurze w sieci wirtualnej na potrzeby rozpoznawania nazw między dzierżawcami przy użyciu systemu DNS udostępnianego przez platformę Azure. Jeśli używasz własnego serwera DNS, to ograniczenie nie ma zastosowania.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Czy mogę zastąpić moje ustawienia DNS dla maszyny wirtualnej lub usługi w chmurze?
Tak. Można ustawić serwery DNS na maszynę wirtualną lub usługę w chmurze w celu zastąpienia domyślnych ustawień sieci. Zaleca się jednak używanie systemu DNS w całej sieci, jak to możliwe.

### <a name="can-i-bring-my-own-dns-suffix"></a>Czy mogę przenieść mój własny sufiks DNS?
Nie. Nie można określić niestandardowego sufiksu DNS dla sieci wirtualnych.

## <a name="connecting-virtual-machines"></a>Łączenie maszyn wirtualnych

### <a name="can-i-deploy-vms-to-a-vnet"></a>Czy mogę wdrożyć maszyny wirtualne w sieci wirtualnej?
Tak. Wszystkie interfejsy sieciowe dołączone do maszyny wirtualnej wdrożonej za pomocą modelu wdrażania Menedżer zasobów muszą być połączone z siecią wirtualną. Maszyny wirtualne wdrożone za pośrednictwem klasycznego modelu wdrażania mogą być opcjonalnie połączone z siecią wirtualną.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Jakie są różne typy adresów IP, które można przypisać do maszyn wirtualnych?
* **Prywatny:** Przypisane do każdej karty sieciowej w ramach każdej maszyny wirtualnej. Adres jest przypisany przy użyciu metody statycznej lub dynamicznej. Prywatne adresy IP są przypisywane z zakresu określonego w ustawieniach podsieci sieci wirtualnej. Do zasobów wdrożonych za pomocą klasycznego modelu wdrażania są przypisywane prywatne adresy IP, nawet jeśli nie są połączone z siecią wirtualną. Zachowanie metody alokacji jest inne w zależności od tego, czy zasób został wdrożony przy użyciu Menedżer zasobów lub klasycznego modelu wdrażania: 

  - **Menedżer zasobów**: prywatny adres IP przypisany za pomocą metody dynamicznej lub statycznej pozostaje przypisany do maszyny wirtualnej (Menedżer zasobów) do momentu usunięcia zasobu. Różnica polega na tym, że wybierasz adres, który ma zostać przypisany przy użyciu statycznej, a platforma Azure wybiera przy użyciu opcji dynamiczny. 
  - **Klasyczny**: prywatny adres IP przypisany za pomocą metody dynamicznej może ulec zmianie, gdy maszyna wirtualna (klasyczna) zostanie uruchomiona ponownie po przejściu w stan zatrzymania (cofnięto przydział). Jeśli chcesz mieć pewność, że prywatny adres IP zasobu wdrożonego za pomocą klasycznego modelu wdrażania nigdy nie ulegnie zmianie, przypisz prywatny adres IP za pomocą metody statycznej.

* **Publiczny:** Opcjonalnie przypisane do kart sieciowych dołączonych do maszyn wirtualnych wdrożonych za pomocą modelu wdrażania Azure Resource Manager. Adres można przypisać za pomocą metody alokacji statycznej lub dynamicznej. Wszystkie maszyny wirtualne i wystąpienia ról Cloud Services wdrożone za pośrednictwem klasycznego modelu wdrażania istnieją w ramach usługi w chmurze, która ma przypisany *dynamiczny*, publiczny wirtualny adres IP (VIP). Publiczny *statyczny* adres IP o nazwie [adres zastrzeżony adres IP](virtual-networks-reserved-public-ip.md)można opcjonalnie przypisać do adresu VIP. Publiczne adresy IP można przypisywać do poszczególnych maszyn wirtualnych lub Cloud Services wystąpień ról wdrożonych za pomocą klasycznego modelu wdrażania. Te adresy są nazywane [publicznym adresem IP na poziomie wystąpienia (ILPIP](virtual-networks-instance-level-public-ip.md) ) i mogą być przypisywane dynamicznie.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Czy można zarezerwować prywatny adres IP dla maszyny wirtualnej, która zostanie utworzona w późniejszym czasie?
Nie. Nie można zarezerwować prywatnego adresu IP. Jeśli prywatny adres IP jest dostępny, jest on przypisywany do maszyny wirtualnej lub wystąpienia roli przez serwer DHCP. Maszyna wirtualna może być taka sama jak ta, do której ma przypisany prywatny adres IP. Można jednak zmienić prywatny adres IP już utworzonej maszyny wirtualnej na dowolny dostępny prywatny adres IP.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Czy prywatne adresy IP są zmieniane dla maszyn wirtualnych w sieci wirtualnej?
To zależy. Jeśli maszyna wirtualna została wdrożona za pomocą Menedżer zasobów nie, niezależnie od tego, czy adres IP został przypisany za pomocą metody alokacji statycznej, czy dynamicznej. Jeśli maszyna wirtualna została wdrożona za pomocą klasycznego modelu wdrażania, dynamiczne adresy IP mogą ulec zmianie, gdy maszyna wirtualna jest uruchomiona po przejściu w stan zatrzymania (cofnięto przydział). Adres jest wydawany z maszyny wirtualnej wdrożonej przy użyciu modelu wdrażania podczas usuwania maszyny wirtualnej.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Czy mogę ręcznie przypisywać adresy IP do kart sieciowych w ramach systemu operacyjnego maszyny wirtualnej?
Tak, ale nie jest to zalecane, o ile nie jest to konieczne, na przykład podczas przypisywania wielu adresów IP do maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie wielu adresów IP do maszyny wirtualnej](virtual-network-multiple-ip-addresses-portal.md#os-config). Jeśli adres IP przypisany do karty sieciowej platformy Azure dołączony do maszyny wirtualnej ulegnie zmianie, a adres IP w systemie operacyjnym maszyny wirtualnej jest inny, nastąpi utrata łączności z maszyną wirtualną.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Jeśli zatrzymasz miejsce wdrożenia usługi w chmurze lub wyzamykasz maszynę wirtualną z poziomu systemu operacyjnego, co się stanie z moimi adresami IP?
Nic. Adresy IP (Public VIP, Public i Private) pozostają przypisane do miejsca wdrożenia usługi w chmurze lub maszyny wirtualnej.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Czy mogę przenieść maszyny wirtualne z jednej podsieci do innej podsieci w sieci wirtualnej bez ponownego wdrażania?
Tak. Więcej informacji można znaleźć w artykule [jak przenieść wystąpienie maszyny wirtualnej lub roli do innego artykułu podsieci](virtual-networks-move-vm-role-to-subnet.md) .

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Czy mogę skonfigurować statyczny adres MAC dla mojej maszyny wirtualnej?
Nie. Adres MAC nie może być skonfigurowany statycznie.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>Czy adres MAC będzie nadal taki sam dla mojej maszyny wirtualnej po jej utworzeniu?
Tak, adres MAC pozostaje taki sam dla maszyny wirtualnej wdrożonej w ramach Menedżer zasobów i klasycznych modeli wdrażania, dopóki nie zostanie usunięty. Wcześniej adres MAC został ogłoszony, jeśli maszyna wirtualna została zatrzymana (bez przydziału), ale teraz adres MAC jest zachowywany nawet wtedy, gdy maszyna wirtualna jest w stanie cofania przydziału. Adres MAC pozostaje przypisany do interfejsu sieciowego do momentu usunięcia interfejsu sieciowego lub zmiany prywatnego adresu IP przypisanego do podstawowej konfiguracji adresu IP podstawowego interfejsu sieciowego. 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Czy mogę połączyć się z Internetem z maszyny wirtualnej w sieci wirtualnej?
Tak. Wszystkie maszyny wirtualne i wystąpienia roli Cloud Services wdrożone w sieci wirtualnej mogą łączyć się z Internetem.

## <a name="azure-services-that-connect-to-vnets"></a>Usługi platformy Azure, które łączą się z usługą sieci wirtualnych

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Czy można używać Azure App Service Web Apps z siecią wirtualną?
Tak. Web Apps można wdrożyć wewnątrz sieci wirtualnej przy użyciu środowiska ASE (App Service Environment), połączyć zaplecze aplikacji z usługą sieci wirtualnych z integracją sieci wirtualnej i zablokować ruch przychodzący do aplikacji za pomocą punktów końcowych usługi. Aby uzyskać więcej informacji zobacz następujące artykuły:

* [App Service funkcje sieciowe](../app-service/networking-features.md)
* [Tworzenie Web Apps w App Service Environment](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrowanie aplikacji z usługą Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Ograniczenia dostępu App Service](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Czy można wdrażać Cloud Services z rolą sieci Web i procesu roboczego (PaaS) w sieci wirtualnej?
Tak. Możesz (opcjonalnie) wdrożyć wystąpienia roli Cloud Services w sieci wirtualnych. W tym celu należy określić nazwę sieci wirtualnej i mapowania role/podsieć w konfiguracji usługi. Nie trzeba aktualizować żadnych plików binarnych.

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>Czy można połączyć zestaw skalowania maszyn wirtualnych z siecią wirtualną?
Tak. Należy podłączyć zestaw skalowania maszyn wirtualnych do sieci wirtualnej.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Czy istnieje kompletna lista usług platformy Azure, które umożliwiają wdrażanie zasobów w sieci wirtualnej?
Tak, aby uzyskać szczegółowe informacje, zobacz [integracja z siecią wirtualną dla usług platformy Azure](virtual-network-for-azure-services.md).

### <a name="how-can-i-restrict-access-to-azure-paas-resources-from-a-vnet"></a>Jak ograniczyć dostęp do zasobów usługi Azure PaaS z sieci wirtualnej?

Zasoby wdrożone za pomocą niektórych usług PaaS platformy Azure (takich jak Azure Storage i Azure SQL Database) mogą ograniczyć dostęp sieciowy do sieci wirtualnej za pomocą punktów końcowych usługi sieci wirtualnej lub prywatnego linku platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Omówienie punktów końcowych usługi sieci wirtualnej](virtual-network-service-endpoints-overview.md), [Omówienie linku prywatnego platformy Azure](../private-link/private-link-overview.md)

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Czy mogę przenieść usługi do i z sieci wirtualnych?
Nie. Nie można przenieść usług do i z sieci wirtualnych. Aby przenieść zasób do innej sieci wirtualnej, należy usunąć i ponownie wdrożyć zasób.

## <a name="security"></a>Zabezpieczenia

### <a name="what-is-the-security-model-for-vnets"></a>Jaki jest model zabezpieczeń sieci wirtualnych?
Sieci wirtualnych są odizolowane od siebie nawzajem i inne usługi hostowane w infrastrukturze platformy Azure. Sieć wirtualna jest granicą zaufania.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Czy mogę ograniczyć przepływ ruchu przychodzącego lub wychodzącego do zasobów podłączonych do sieci wirtualnej?
Tak. [Sieciowe grupy zabezpieczeń](security-overview.md) można stosować do poszczególnych podsieci w sieci wirtualnej, kart sieciowych podłączonych do sieci wirtualnej lub obu.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Czy mogę zaimplementować zaporę między zasobami połączonymi z siecią wirtualną?
Tak. [Urządzenie wirtualne sieci zapory](https://azure.microsoft.com/marketplace/?term=firewall) można wdrożyć z kilku dostawców za pośrednictwem portalu Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Czy istnieją informacje o zabezpieczaniu sieci wirtualnych?
Tak. Aby uzyskać szczegółowe informacje, zobacz [Omówienie zabezpieczeń sieci platformy Azure](../security/fundamentals/network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>Interfejsy API, schematy i narzędzia

### <a name="can-i-manage-vnets-from-code"></a>Czy mogę zarządzać sieci wirtualnych z kodu?
Tak. Interfejsów API REST można używać dla sieci wirtualnych w ramach [Azure Resource Manager](/rest/api/virtual-network) i [klasycznych](https://go.microsoft.com/fwlink/?LinkId=296833) modeli wdrażania.

### <a name="is-there-tooling-support-for-vnets"></a>Czy istnieją narzędzia obsługujące sieci wirtualnych?
Tak. Dowiedz się więcej o korzystaniu z programu:
- Azure Portal wdrażania sieci wirtualnych za pomocą [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) i [klasycznych](virtual-networks-create-vnet-classic-pportal.md) modeli wdrażania.
- Program PowerShell do zarządzania usługą sieci wirtualnych wdrożoną za pomocą [Menedżer zasobów](/powershell/module/az.network) i [klasycznych](/powershell/module/servicemanagement/azure.service/?view=azuresmps-3.7.0) modeli wdrażania.
- Interfejs wiersza polecenia (CLI) platformy Azure do wdrażania sieci wirtualnych wdrożonych w ramach [Menedżer zasobów](/cli/azure/network/vnet) i [klasycznych](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-resources) modeli wdrażania oraz zarządzania nimi.  

## <a name="vnet-peering"></a>Komunikacja równorzędna sieci wirtualnych

### <a name="what-is-vnet-peering"></a>Co to jest Komunikacja równorzędna sieci wirtualnych?
Wirtualne sieci równorzędne (lub Komunikacja równorzędna sieci wirtualnych) umożliwiają łączenie z siecią wirtualną. Połączenie komunikacji równorzędnej wirtualnej między sieciami wirtualnymi pozwala na kierowanie ruchu między nimi prywatnie przy użyciu adresów IPv4. Maszyny wirtualne w sieci wirtualnych równorzędnym mogą komunikować się ze sobą, tak jakby znajdowały się w tej samej sieci. Te sieci wirtualne mogą znajdować się w tym samym regionie lub w różnych regionach (nazywanych również globalnymi sieciami równorzędnymi). Połączenia komunikacji równorzędnej sieci wirtualnej można również tworzyć w ramach subskrypcji platformy Azure.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Czy można utworzyć połączenie komunikacji równorzędnej z siecią wirtualną w innym regionie?
Tak. Globalna komunikacja równorzędna sieci wirtualnych umożliwia sieci wirtualnych elementów równorzędnych w różnych regionach. Globalna komunikacja równorzędna sieci wirtualnych jest dostępna we wszystkich regionach publicznych platformy Azure, regionach chmury w Chinach i regionach w chmurze dla instytucji rządowych. Nie można globalnie elementów równorzędnych z publicznych regionów platformy Azure do regionów w chmurze krajowej.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>Jakie są ograniczenia dotyczące globalnej komunikacji równorzędnej sieci wirtualnej i modułów równoważenia obciążenia?
Jeśli dwie sieci wirtualne w dwóch różnych regionach są połączone za pomocą komunikacji równorzędnej globalnej sieci równorzędnej, nie można połączyć się z zasobami, które znajdują się za podstawową Load Balancer za pośrednictwem adresu IP frontonu Load Balancer. To ograniczenie nie istnieje dla usługa Load Balancer w warstwie Standardowa.
Poniższe zasoby mogą korzystać z podstawowych modułów równoważenia obciążenia, co oznacza, że nie można uzyskać dostępu do nich za pośrednictwem adresu IP frontonu Load Balancer w ramach globalnej komunikacji równorzędnej sieci wirtualnej. Można jednak użyć globalnej komunikacji równorzędnej sieci wirtualnej, aby uzyskać dostęp do zasobów bezpośrednio za pomocą prywatnych adresów IP, jeśli jest to dozwolone. 
- Maszyny wirtualne za podstawowymi usługami równoważenia obciążenia
- Zestawy skalowania maszyn wirtualnych z podstawowymi usługami równoważenia obciążenia 
- Pamięć podręczna Redis 
- Jednostka SKU Application Gateway (v1)
- Service Fabric
- API Management
- Usługa domena usługi Active Directory (dodaje)
- Logic Apps
- HDInsight
-   Azure Batch
- Środowisko usługi App Service

Możesz połączyć się z tymi zasobami za pośrednictwem ExpressRoute lub sieci VNet-to-VNet za pośrednictwem bram sieci wirtualnej.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Czy mogę włączyć komunikację równorzędną wirtualnych, jeśli moje sieci wirtualne należą do subskrypcji w ramach różnych Azure Active Directory dzierżawców?
Tak. Istnieje możliwość ustanowienia komunikacji równorzędnej sieci wirtualnej (lokalnego lub globalnego), jeśli subskrypcje należą do różnych dzierżaw Azure Active Directory. Można to zrobić za pomocą portalu, programu PowerShell lub interfejsu wiersza polecenia.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>Połączenie komunikacji równorzędnej sieci wirtualnej jest w stanie *inicjowania* , dlaczego nie mogę nawiązać połączenia?
Jeśli połączenie komunikacji równorzędnej jest w stanie *zainicjowanym* , oznacza to, że utworzono tylko jeden link. Aby można było nawiązać połączenie, należy utworzyć dwukierunkowy link. Na przykład aby połączyć równorzędną sieć wirtualną z siecią VNet B, należy utworzyć łącze z VNetA do Vnetc i z Vnetc do VNetA. Utworzenie obu linków spowoduje zmianę stanu na *połączony*.

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>Połączenie komunikacji równorzędnej sieci wirtualnej jest w stanie *rozłączenia* , dlaczego nie można utworzyć połączenia komunikacji równorzędnej?
Jeśli połączenie komunikacji równorzędnej sieci wirtualnej jest w stanie *odłączonym* , oznacza to, że jedno z utworzonych linków zostało usunięte. Aby można było ponownie ustanowić połączenie komunikacji równorzędnej, należy usunąć link i ponownie go utworzyć.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Czy mogę połączyć sieć wirtualną z siecią wirtualną w ramach innej subskrypcji?
Tak. Możesz sieci wirtualnych elementy równorzędne w różnych subskrypcjach i w różnych regionach.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Czy mogę nawiązać połączenie równorzędne dwóch sieci wirtualnych z pasującymi lub nakładanymi zakresami adresów?
Nie. Przestrzenie adresowe nie mogą się nakładać, aby umożliwić komunikację równorzędną sieci wirtualnych.

### <a name="how-much-do-vnet-peering-links-cost"></a>Ile jest koszt linków komunikacji równorzędnej sieci wirtualnej?
Nie jest naliczana opłata za utworzenie połączenia komunikacji równorzędnej sieci wirtualnej. Opłaty za transfer danych między połączeniami równorzędnymi są naliczone. [Zobacz tutaj](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>Czy ruch komunikacji równorzędnej sieci wirtualnej jest szyfrowany?
Nie. Ruch między zasobami w sieci wirtualnych komunikacji równorzędnej jest prywatny i izolowany. Pozostanie w całości w sieci szkieletowej firmy Microsoft.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Dlaczego moje połączenie komunikacji równorzędnej jest w stanie *odłączonym* ?
Połączenia komunikacji równorzędnej sieci wirtualnych przechodzą w stan *rozłączenia* , gdy zostanie usunięte łącze komunikacji równorzędnej sieci wirtualnej. Należy usunąć oba linki, aby ponownie ustanowić pomyślne połączenie komunikacji równorzędnej.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Jeśli VNetA równorzędny do Vnetc i i elementów równorzędnych Vnetc do VNetC, czy oznacza to, że jako VNetA i VNetC są połączone za pomocą komunikacji równorzędnej?
Nie. Przechodnia Komunikacja równorzędna nie jest obsługiwana. Aby to zrobić, musisz mieć VNetAy elementów równorzędnych i VNetC.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Czy istnieją ograniczenia przepustowości dla połączeń komunikacji równorzędnej?
Nie. Wirtualne sieci równorzędne, lokalne lub globalne, nie nakładają żadnych ograniczeń przepustowości. Przepustowość jest ograniczona tylko przez maszynę wirtualną lub zasób obliczeniowy.

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>Jak mogę rozwiązać problemy dotyczące komunikacji równorzędnej sieci wirtualnej?
Oto [Przewodnik dotyczący narzędzia do rozwiązywania problemów](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues) , który można wypróbować.

## <a name="virtual-network-tap"></a>Virtual Network TAP

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Które regiony platformy Azure są dostępne dla NACISKania sieci wirtualnej?
Usługa Virtual Network TAP w wersji zapoznawczej jest dostępna we wszystkich regionach świadczenia usługi Azure. Monitorowane interfejsy sieciowe, zasób TAP sieci wirtualnej i rozwiązanie zbierające lub analityczne muszą być wdrożone w tym samym regionie.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>Czy Virtual Network TAP obsługuje jakiekolwiek możliwości filtrowania na dublowanych pakietach?
Możliwości filtrowania nie są obsługiwane w przypadku WYBRANia pozycji Podgląd sieci wirtualnej. Po dodaniu konfiguracji TAP do interfejsu sieciowego Szczegółowa kopia całego ruchu przychodzącego i wychodzącego w interfejsie sieciowym jest przesyłana strumieniowo do lokalizacji docelowej.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Czy do monitorowanego interfejsu sieciowego można dodać wiele konfiguracji TAP?
Monitorowany interfejs sieciowy może mieć tylko jedną konfigurację TAP. Zapoznaj się z [rozwiązaniem partnera](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) indywidualnego, aby przesłać strumieniowo wiele kopii ruchu TAP do wybranych narzędzi analitycznych.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>Czy ta sama sieć wirtualna umożliwia wybranie zasobów zagregowanych z monitorowanych interfejsów sieciowych w więcej niż jednej sieci wirtualnej?
Tak. Tego samego zasobu sieci wirtualnej można użyć do agregowania dublowanego ruchu z monitorowanych interfejsów sieciowych w równorzędnych sieciach wirtualnych w ramach tej samej subskrypcji lub innej subskrypcji. Zasób TAP sieci wirtualnej i docelowy moduł równoważenia obciążenia lub docelowy interfejs sieciowy muszą znajdować się w tej samej subskrypcji. Wszystkie subskrypcje muszą znajdować się w tej samej dzierżawie Azure Active Directory.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Czy w przypadku włączenia konfiguracji sieci wirtualnej w interfejsie sieciowym są brane pod uwagę wydajność w ruchu produkcyjnym?

Wybór sieci wirtualnej jest w wersji zapoznawczej. W trakcie okresu zapoznawczego nie ma umowy dotyczącej poziomu usług. Tej możliwości nie należy używać w przypadku obciążeń produkcyjnych. Gdy interfejs sieciowy maszyny wirtualnej jest włączony z konfiguracją TAP, te same zasoby na hoście platformy Azure przydzieleni do maszyny wirtualnej w celu wysłania ruchu produkcyjnego są używane do wykonywania funkcji dublowania i wysyłania dublowanych pakietów. Wybierz prawidłowy rozmiar maszyny wirtualnej z systemem [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , aby upewnić się, że dla maszyny wirtualnej są dostępne wystarczające zasoby, które będą wysyłać ruch produkcyjny i wolumin dublowany.

### <a name="is-accelerated-networking-for-linux-or-windows-supported-with-virtual-network-tap"></a>Czy jest to przyspieszone sieci dla systemu [Linux](create-vm-accelerated-networking-cli.md) lub [Windows](create-vm-accelerated-networking-powershell.md) obsługiwane z naciskiem sieci wirtualnej?

Można dodać konfigurację TAP w interfejsie sieciowym podłączonym do maszyny wirtualnej, która jest włączona za pomocą przyspieszonej sieci. Jednak wydajność i opóźnienie na maszynie wirtualnej mają wpływ na dodanie konfiguracji TAP, ponieważ nie jest to obecnie obsługiwane przez przyspieszone sieci platformy Azure.

## <a name="virtual-network-service-endpoints"></a>Punkty końcowe usługi dla sieci wirtualnej

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Jaka jest właściwa sekwencja operacji w celu skonfigurowania punktów końcowych usługi dla usługi platformy Azure?
Aby zabezpieczyć zasób usługi platformy Azure za pomocą punktów końcowych usługi, należy wykonać dwa kroki:
1. Włącz punkty końcowe usługi dla usługi platformy Azure.
2. Skonfiguruj listy ACL sieci wirtualnej w usłudze platformy Azure.

Pierwszym krokiem jest operacja po stronie sieci, a drugi krok to operacja po stronie zasobów usługi. Obie kroki mogą być wykonywane przez tego samego administratora lub różnych administratorów na podstawie uprawnień RBAC przyznanych roli administratora. Zalecamy najpierw włączyć punkty końcowe usługi dla sieci wirtualnej przed skonfigurowaniem list kontroli dostępu wirtualnej w ramach usługi platformy Azure. W związku z tym kroki muszą być wykonywane w sekwencji wymienionej powyżej w celu skonfigurowania punktów końcowych usługi sieci wirtualnej.

>[!NOTE]
> Obie operacje opisane powyżej muszą zostać wykonane, aby można było ograniczyć dostęp usługi platformy Azure do dozwolonej sieci wirtualnej i podsieci. Włączenie punktów końcowych usługi dla usługi platformy Azure po stronie sieci nie zapewnia ograniczonego dostępu. Ponadto należy również skonfigurować listy ACL sieci wirtualnej na stronie usługi platformy Azure.

Niektóre usługi (takie jak SQL i CosmosDB) umożliwiają wyjątki od powyższej sekwencji przez flagę **IgnoreMissingVnetServiceEndpoint** . Gdy flaga ma **wartość true**, po stronie usługi platformy Azure można ustawić listy ACL sieci wirtualnej przed skonfigurowaniem punktów końcowych usługi po stronie sieci. Usługi platformy Azure zapewniają tę flagę, aby pomóc klientom w przypadkach, w których określone zapory IP zostały skonfigurowane w usługach platformy Azure i włączające punkty końcowe usługi po stronie sieci mogą prowadzić do porzucenia połączeń, ponieważ źródłowy adres IP zmieni się z publicznego adresu IPv4 na adres prywatny. Skonfigurowanie list ACL sieci wirtualnej na stronie usługi platformy Azure przed ustawieniem punktów końcowych usługi po stronie sieci może pomóc w uniknięciu usuwania łączności.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Czy wszystkie usługi platformy Azure znajdują się w sieci wirtualnej platformy Azure dostarczonej przez klienta? Jak działa punkt końcowy usługi sieci wirtualnej z usługami platformy Azure?

Nie, nie wszystkie usługi platformy Azure znajdują się w sieci wirtualnej klienta. Większość usług danych platformy Azure, takich jak Azure Storage, Azure SQL i Azure Cosmos DB, to usługi wielodostępne, do których można uzyskać dostęp za pośrednictwem publicznych adresów IP. Więcej informacji na temat integracji sieci wirtualnej dla usług platformy Azure można znaleźć [tutaj](virtual-network-for-azure-services.md). 

W przypadku korzystania z funkcji punktów końcowych usługi sieci wirtualnej (Włączanie punktu końcowego usługi wirtualnej po stronie sieciowej i konfigurowania odpowiednich list ACL sieci wirtualnej na stronie usługi Azure) dostęp do usługi platformy Azure jest ograniczony z dozwolonej sieci wirtualnej i podsieci.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>Jak punkt końcowy usługi sieci wirtualnej zapewnia zabezpieczenia?

Funkcja punktu końcowego usługi sieci wirtualnej (włączenie punktu końcowego usługi wirtualnej po stronie sieci i skonfigurowania odpowiednich list ACL sieci wirtualnej na stronie usługi platformy Azure) ogranicza dostęp usługi platformy Azure do dopuszczalnej sieci wirtualnej i podsieci, zapewniając w ten sposób zabezpieczenia na poziomie sieci i izolację ruchu usługi platformy Azure. Cały ruch korzystający z punktów końcowych usługi sieci wirtualnej przechodzi przez sieć szkieletową firmy Microsoft, co zapewnia kolejną warstwę izolacji z publicznego Internetu. Ponadto klienci mogą wybrać całkowite usunięcie publicznego dostępu do zasobów usługi platformy Azure i Zezwalanie na ruch tylko z sieci wirtualnych za pośrednictwem kombinacji list kontroli dostępu i zapory IP, a tym samym ochronę zasobów usługi platformy Azure przed nieautoryzowanym dostępem.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>Co to jest Ochrona punktu końcowego usługi sieci wirtualnej lub usługi platformy Azure?
Punkty końcowe usługi sieci wirtualnej ułatwiają ochronę zasobów usługi platformy Azure. Zasoby sieci wirtualnej są chronione za poorednictwem sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń).

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>Czy istnieją jakieś koszty używania punktów końcowych usługi sieci wirtualnej?

Nie. nie ma dodatkowych kosztów za korzystanie z punktów końcowych usługi sieci wirtualnej.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>Czy mogę włączyć punkty końcowe usługi VNet i skonfigurować listy ACL sieci wirtualnej, jeśli sieć wirtualna i zasoby usługi platformy Azure należą do różnych subskrypcji?

Tak, jest to możliwe. Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Jedyny wymóg polega na tym, że zarówno zasoby sieci wirtualnej, jak i usługi platformy Azure muszą znajdować się w tej samej dzierżawie Active Directory (AD).

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>Czy mogę włączyć punkty końcowe usługi VNet i skonfigurować listy ACL sieci wirtualnej, jeśli sieć wirtualna i zasoby usługi platformy Azure należą do różnych dzierżawców usługi AD?
Tak, jest możliwe, gdy korzystasz z punktów końcowych usługi Azure Storage i Azure Key Vault. W przypadku pozostałych usług punkty końcowe usługi sieci wirtualnej i listy kontroli dostępu wirtualne nie są obsługiwane w dzierżawach usługi AD.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>Czy można korzystać z adresu IP urządzenia lokalnego, który jest połączony za pośrednictwem usługi Azure Virtual Network Gateway (VPN) lub ExpressRoute Gateway przez punkty końcowe usługi sieci wirtualnej?
Domyślnie nie można uzyskać dostępu do zasobów usługi platformy Azure zabezpieczonych w sieciach wirtualnych z sieci lokalnych. Jeśli chcesz zezwolić na ruch z lokalnego, musisz również zezwolić na publiczne (zazwyczaj NAT) adresy IP z lokalnego lub ExpressRoute. Te adresy IP można dodać za pomocą konfiguracji zapory adresów IP dla zasobów usługi platformy Azure.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-within-a-virtual-network-or-across-multiple-virtual-networks"></a>Czy można użyć funkcji punktu końcowego usługi sieci wirtualnej do zabezpieczenia usługi platformy Azure w wielu podsieciach w ramach sieci wirtualnej lub wielu sieci wirtualnych?
Aby zabezpieczyć usługi platformy Azure w wielu podsieciach w sieci wirtualnej lub w wielu sieciach wirtualnych, Włącz punkty końcowe usługi w sieci w każdej podsieci niezależnie, a następnie Zabezpiecz zasoby usługi platformy Azure we wszystkich podsieciach przez skonfigurowanie odpowiednich list ACL sieci wirtualnej na stronie usługi platformy Azure.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>Jak można filtrować ruch wychodzący z sieci wirtualnej do usług platformy Azure i nadal korzystać z punktów końcowych usługi?
jeśli chcesz sprawdzić lub filtrować ruch kierowany do usługi platformy Azure z sieci wirtualnej, możesz wdrożyć urządzenie sieci wirtualnej w ramach tej sieci wirtualnej. Następnie można zastosować punkty końcowe usługi do podsieci, w której wdrożono wirtualne urządzenie sieciowe i zabezpieczyć zasoby usługi platformy Azure tylko do tej podsieci za pomocą list ACL sieci wirtualnej. Ten scenariusz może być również przydatny, jeśli chcesz ograniczyć dostęp do usługi platformy Azure z sieci wirtualnej tylko do określonych zasobów platformy Azure przy użyciu filtrowania wirtualnego urządzenia sieciowego. Aby uzyskać więcej informacji, zobacz [ruch wychodzący z sieciowych urządzeń wirtualnych](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-a-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>Co się stanie w przypadku uzyskania dostępu do konta usługi platformy Azure z włączoną listą kontroli dostępu do sieci wirtualnej z poziomu poza siecią wirtualną?
Zwracany jest błąd HTTP 403 lub HTTP 404.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>Czy podsieci sieci wirtualnej utworzonej w różnych regionach mogą uzyskać dostęp do konta usługi platformy Azure w innym regionie? 
Tak. w przypadku większości usług platformy Azure sieci wirtualne utworzone w różnych regionach mogą uzyskiwać dostęp do usług platformy Azure w innym regionie za pomocą punktów końcowych usługi sieci wirtualnej. Na przykład jeśli konto Azure Cosmos DB znajduje się w regionie zachodnie stany USA lub Wschodnie stany USA, a sieci wirtualne znajdują się w wielu regionach, Sieć wirtualna może uzyskać dostęp do Azure Cosmos DB. Magazyn i SQL to wyjątki, które są regionalne, a zarówno w przypadku sieci wirtualnej, jak i usługi platformy Azure, muszą znajdować się w tym samym regionie.
  
### <a name="can-an-azure-service-have-both-a-vnet-acl-and-an-ip-firewall"></a>Czy usługa platformy Azure ma zarówno listę ACL sieci wirtualnej, jak i zaporę IP?
Tak, ta lista ACL sieci wirtualnej i Zapora IP mogą współistnieć. Obie funkcje uzupełniają się wzajemnie, aby zapewnić izolację i bezpieczeństwo.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>Co się stanie w przypadku usunięcia sieci wirtualnej lub podsieci z włączonym punktem końcowym usługi dla usługi Azure?
Usuwanie sieci wirtualnych i podsieci są niezależnymi operacjami i są obsługiwane nawet wtedy, gdy punkty końcowe usługi są włączone dla usług platformy Azure. W przypadkach, gdy usługi platformy Azure mają skonfigurowane listy ACL sieci wirtualnej dla tych sieci wirtualnych i podsieci, informacje o liście ACL sieci wirtualnej skojarzone z usługą platformy Azure są wyłączone, gdy zostanie usunięta Sieć wirtualna lub podsieci z włączonym punktem końcowym usługi sieci wirtualnej.
 
### <a name="what-happens-if-an-azure-service-account-that-has-a-vnet-service-endpoint-enabled-is-deleted"></a>Co się stanie, jeśli konto usługi platformy Azure z włączonym punktem końcowym usługi sieci wirtualnej zostanie usunięte?
Usuwanie konta usługi platformy Azure jest niezależną operacją i jest obsługiwane nawet wtedy, gdy punkt końcowy usługi jest włączony po stronie sieci, a usługi dostępu wirtualne sieci wirtualnej są skonfigurowane po stronie usług platformy Azure. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>Co się dzieje ze źródłowym adresem IP zasobu (np. maszyną wirtualną w podsieci), dla którego jest włączony punkt końcowy usługi sieci wirtualnej?
Po włączeniu punktów końcowych usługi sieci wirtualnej źródłowe adresy IP zasobów w podsieci sieci wirtualnej przełączają się z używania publicznych adresów IPV4 do prywatnych adresów IP sieci wirtualnej platformy Azure dla ruchu do usługi platformy Azure. Należy zauważyć, że może to spowodować niepowodzenie określonych zapór IP skonfigurowanych na publiczny adres IPV4 w usługach platformy Azure. 

### <a name="does-the-service-endpoint-route-always-take-precedence"></a>Czy ma zawsze pierwszeństwo trasy punktu końcowego usługi?
Punkty końcowe usługi dodają trasę systemową, która ma pierwszeństwo przed trasami BGP i zapewnia optymalny Routing ruchu punktu końcowego usługi. Punkty końcowe usługi zawsze pobierają ruch bezpośrednio z sieci wirtualnej do usługi w sieci szkieletowej Microsoft Azure. Aby uzyskać więcej informacji o tym, jak platforma Azure wybiera trasę, zobacz [routing ruchu w sieci wirtualnej platformy Azure](virtual-networks-udr-overview.md).
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>Jak sieciowej grupy zabezpieczeń w podsieci działa z punktami końcowymi usługi?
Aby dotrzeć do usługi platformy Azure, sieciowych grup zabezpieczeń musi zezwalać na łączność wychodzącą. Jeśli sieciowych grup zabezpieczeń są otwarte dla całego ruchu wychodzącego z Internetu, należy obejść ruch punktu końcowego usługi. Można także ograniczyć ruch wychodzący do adresów IP usługi tylko przy użyciu tagów usługi.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>Jakie uprawnienia muszę skonfigurować punkty końcowe usługi?
Punkty końcowe usługi można skonfigurować w sieci wirtualnej niezależnie od użytkownika z dostępem do zapisu do sieci wirtualnej. Aby zabezpieczyć zasoby usługi platformy Azure w sieci wirtualnej, użytkownik musi mieć uprawnienia **Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/Action** dla dodawanych podsieci. To uprawnienie jest domyślnie dołączone do wbudowanej roli administratora usługi i można je zmodyfikować, tworząc role niestandardowe. Dowiedz się więcej na temat wbudowanych ról i przypisywaniu określonych uprawnień do [ról niestandardowych](https://docs.microsoft.com/azure/role-based-access-control/custom-roles?toc=%2fazure%2fvirtual-network%2ftoc.json).
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>Czy można filtrować ruch w sieci wirtualnej do usług platformy Azure, zezwalając na tylko określone zasoby usługi platformy Azure, za pośrednictwem punktów końcowych usługi sieci wirtualnej? 

Zasady punktu końcowego usługi sieci wirtualnej umożliwiają filtrowanie ruchu w sieci wirtualnej do usług platformy Azure, co pozwala na dostęp tylko do określonych zasobów usługi platformy Azure za pośrednictwem punktów końcowych usługi. Zasady punktu końcowego zapewniają szczegółową kontrolę dostępu z ruchu w sieci wirtualnej do usług platformy Azure. Więcej informacji na temat zasad punktu końcowego usługi można znaleźć [tutaj](virtual-network-service-endpoint-policies-overview.md).

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>Czy Azure Active Directory (Azure AD) obsługuje punkty końcowe usługi sieci wirtualnej?

Azure Active Directory (Azure AD) nie obsługuje natywnych punktów końcowych usługi. Pełną listę usług platformy Azure obsługujących punkty końcowe usługi sieci wirtualnej można zobaczyć [tutaj](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). Należy zauważyć, że tag "Microsoft. usługi azureactivedirectory" wymieniony w obszarze punkty końcowe usługi obsługiwanej przez usługi jest używany do obsługi punktów końcowych usługi ADLS Gen 1. W przypadku usługi ADLS Gen 1 Integracja sieci wirtualnej dla Azure Data Lake Storage Gen1 wykorzystuje zabezpieczenia punktu końcowego usługi sieci wirtualnej między siecią wirtualną i Azure Active Directory (Azure AD) w celu wygenerowania dodatkowych oświadczeń zabezpieczeń w tokenie dostępu. Te oświadczenia są następnie używane do uwierzytelniania sieci wirtualnej na koncie usługi Data Lake Storage Gen1 i uzyskania dostępu. Dowiedz się więcej o integracji z siecią [wirtualną Azure Data Lake Store generacji 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>Czy istnieją jakieś ograniczenia dotyczące liczby punktów końcowych usługi sieci wirtualnej, które mogę skonfigurować przy użyciu mojej VNet?
Nie ma limitu całkowitej liczby punktów końcowych usługi wirtualnej w sieci wirtualnej. W przypadku zasobu usługi platformy Azure (takiego jak konto usługi Azure Storage) usługi mogą wymuszać limity liczby podsieci używanych do zabezpieczania zasobu. W poniższej tabeli przedstawiono niektóre przykładowe limity: 

|Usługa platformy Azure| Limity dla reguł sieci wirtualnej|
|---|---|
|Azure Storage| 100|
|Azure SQL| 128|
|Azure Synapse Analytics|   128|
|Azure KeyVault|    127|
|Azure Cosmos DB|   64|
|Centrum zdarzeń Azure|   128|
|Azure Service Bus| 128|
|Azure Data Lake Store v1|  100|
 
>[!NOTE]
> Limity są uzależnione od zmiany uznania usługi platformy Azure. Szczegóły dotyczące usług można znaleźć w odpowiedniej dokumentacji usługi. 




  



