---
title: Usługa Azure Virtual WAN — omówienie | Microsoft Docs
description: Dowiedz się Virtual WAN o zautomatyzowanej, skalowalnej łączności między oddziałami, dostępnych regionach i partnerach.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ab54192480a1c36a27c175254d6d4d275b67c8b7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835330"
---
# <a name="what-is-azure-virtual-wan"></a>Co to jest usługa Azure Virtual WAN?

Azure Virtual WAN to usługa sieciowa, która łączy wiele funkcji sieciowych, zabezpieczeń i routingu w celu zapewnienia jednego interfejsu operacyjnego. Te funkcje obejmują łączność między oddziałami (za pośrednictwem automatyzacji połączeń z urządzeń partnera Virtual WAN, takich jak SD-WAN lub VPN CPE), łączność sieci VPN typu lokacja-lokacja, łączność sieci VPN użytkownika zdalnego (punkt-lokacja), łączność prywatna (ExpressRoute), łączność wewnątrz chmury (łączność przechodnia dla sieci wirtualnych), łączność między sieciami wirtualnymi usługi VPN ExpressRoute, routing, Azure Firewall i szyfrowanie dla łączności prywatnej. Nie trzeba mieć wszystkich tych przypadków użycia, aby rozpocząć korzystanie z Virtual WAN. Możesz po prostu rozpocząć pracę od jednego przypadku użycia, a następnie dostosować sieć w przypadku jej rozwoju.

Architektura Virtual WAN to architektura piasty i szprych ze skalą i wydajnością wbudowaną dla gałęzi (urządzeń VPN/SD-WAN), użytkowników (klientów Azure VPN/OpenVPN/IKEv2), obwodów usługi ExpressRoute i sieci wirtualnych. Umożliwia ona [globalną architekturę](virtual-wan-global-transit-network-architecture.md)sieci tranzytowej , w której "piasta" hostowanej w chmurze sieci umożliwia przechodnie połączenia między punktami końcowymi, które mogą być dystrybuowane między różnymi typami "szprych".

Regiony platformy Azure pełnią funkcję centrów, z których można się łączyć. Wszystkie koncentratory są połączone w pełnej siatce w standardzie Virtual WAN co ułatwia użytkownikowi korzystanie z sieci szkieletowej firmy Microsoft w celu połączenia typu dowolna-dowolna (dowolna szprycha). W przypadku łączności szprychy z urządzeniami SD-WAN/VPN użytkownicy mogą ręcznie skonfigurować ją w usłudze Azure Virtual WAN lub użyć rozwiązania partnerskiego Virtual WAN CPE (SD-WAN/VPN) w celu skonfigurowania łączności z platformą Azure. Mamy listę partnerów, którzy obsługują automatyzację łączności (możliwość eksportowania informacji o urządzeniu na platformę Azure, pobierania konfiguracji platformy Azure i ustanawiania łączności) z Azure Virtual WAN. Aby uzyskać więcej informacji, zobacz [artykuł Virtual WAN partnerów i lokalizacji.](virtual-wan-locations-partners.md)

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwan1.png)

Ten artykuł zawiera szybki widok łączności sieciowej w Azure Virtual WAN. Usługa Virtual WAN oferuje następujące korzyści:

* **Zintegrowane rozwiązania łączności w piasty i szprychach:** Automatyzowanie konfiguracji lokacja-lokacja i łączności między lokacjami lokalnymi i centrum platformy Azure.
* **Automatyczna instalacja i konfiguracja szprych:** bezproblemowe łączenie sieci wirtualnych i obciążeń z koncentratorem na platformie Azure.
* **Intuicyjne rozwiązywanie problemów:** Możesz wyświetlić przepływ end-to-end na platformie Azure, a następnie użyć tych informacji do podjęcia wymaganych akcji.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Podstawowe i standardowe wirtualne sieci WAN

Istnieją dwa typy wirtualnych sieci WAN: Podstawowa i Standardowa. W poniższej tabeli przedstawiono dostępne konfiguracje dla każdego typu.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Aby uzyskać instrukcje dotyczące uaktualniania wirtualnej sieci WAN, zobacz [Upgrade a virtual WAN from Basic to Standard (Uaktualnianie wirtualnej sieci WAN z podstawowej do standardowej).](upgrade-virtual-wan.md)

## <a name="architecture"></a><a name="architecture"></a>Architektura

Informacje o architekturze Virtual WAN migrację do usługi Virtual WAN można znaleźć w następujących artykułach:

* [Virtual WAN architektury](migrate-from-hub-spoke-topology.md)
* [Architektura globalnej sieci tranzytowej](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Zasoby usługi Virtual WAN

Aby skonfigurować kompleksową wirtualną sieć WAN, należy utworzyć następujące zasoby:

* **virtualWAN:** zasób virtualWAN reprezentuje wirtualną nakładkę sieci platformy Azure i stanowi zbiór kilku zasobów. Zawiera linki do wszystkich koncentratorów wirtualnych, które mają się znaleźć w wirtualnej sieci WAN. Zasoby usługi Virtual WAN są wzajemnie izolowane i nie mogą zawierać wspólnego koncentratora. Koncentratory wirtualne Virtual WAN nie komunikują się ze sobą.

* **Koncentrator:** koncentrator wirtualny to sieć wirtualna zarządzana przez firmę Microsoft. Koncentrator zawiera różne punkty końcowe usługi umożliwiające łączność. Z sieci lokalnej (lokacja sieci VPN) możesz nawiązać połączenie z usługą VPN Gateway wewnątrz koncentratora wirtualnego, połączyć obwody usługi ExpressRoute z koncentratorem wirtualnym, a nawet połączyć użytkowników mobilnych z bramą punkt-lokacja w koncentratorze wirtualnym. Koncentrator to podstawowy element sieci w danym regionie. W jednym regionie świadczenia usługi Azure może znajdować się tylko jeden koncentrator.

  Brama koncentratora różni się od bramy sieci wirtualnej używanej w usługach ExpressRoute i VPN Gateway. Na przykład w Virtual WAN nie tworzy się połączenia lokacja-lokacja z lokacji lokalnej bezpośrednio do sieci wirtualnej. Zamiast tego należy utworzyć połączenie lokacja-lokacja z centrum. Ruch zawsze jest kierowany przez bramę koncentratora. Oznacz to, że sieci wirtualne nie muszą mieć własnych bram sieci wirtualnej. Usługa Virtual WAN umożliwia łatwe skalowanie sieci wirtualnych za pośrednictwem koncentratora wirtualnego i bramy koncentratora wirtualnego.

* **Połączenie sieci wirtualnej koncentratora:** zasób połączenia sieci wirtualnej koncentratora umożliwia bezproblemowe łączenie koncentratora z siecią wirtualną.

* **Połączenie typu piasta-koncentrator:** Wszystkie koncentratory są połączone ze sobą w wirtualnej sieci WAN. Oznacza to, że gałąź, użytkownik lub sieć wirtualna połączona z koncentratorem lokalnym może komunikować się z inną gałęzią lub siecią wirtualną przy użyciu architektury pełnej siatki połączonych centrów. Możesz również połączyć sieci wirtualne w obrębie koncentratora przechodniego przez koncentrator wirtualny, a także sieci wirtualne między koncentratorami przy użyciu platformy połączonej piasta-piasta.

* **Tabela tras koncentratora:** możesz utworzyć trasę koncentratora wirtualnego i zastosować tę trasę do tabeli tras koncentratora wirtualnego. Możesz zastosować wiele tras do tabeli tras koncentratora wirtualnego.

**Dodatkowe Virtual WAN zasobów**

* **Lokacja:** Ten zasób jest używany tylko w przypadku połączeń typu lokacja-lokacja. Zasób lokacji to **vpnsite**. Reprezentuje lokalne urządzenie sieci VPN i jego ustawienia. Dzięki współpracy z partnerem usługi Virtual WAN otrzymasz wbudowane rozwiązanie do automatycznego eksportowania tych informacji na platformę Azure.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Typy łączności

Virtual WAN następujące typy połączeń: sieć VPN typu lokacja-lokacja, sieć VPN użytkownika (punkt-lokacja) i usługa ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Połączenia sieci VPN typu lokacja-lokacja

Możesz połączyć się z zasobami na platformie Azure za pośrednictwem połączenia IPsec/IKE (IKEv2) typu lokacja-lokacja. Aby uzyskać więcej informacji, [zobacz Tworzenie połączenia lokacja-lokacja przy użyciu Virtual WAN](virtual-wan-site-to-site-portal.md). 

Ten typ połączenia wymaga urządzenia sieci VPN lub Virtual WAN Partnera. Virtual WAN zapewniają automatyzację łączności, czyli możliwość eksportowania informacji o urządzeniu na platformę Azure, pobierania konfiguracji platformy Azure i ustanawiania łączności z centrum Azure Virtual WAN azure. Aby uzyskać listę dostępnych partnerów i lokalizacji, zobacz artykuł [Virtual WAN partnerów i lokalizacji.](virtual-wan-locations-partners.md) Jeśli dostawca urządzenia VPN/SD-WAN nie znajduje się na liście wymienionego linku, możesz po prostu użyć instrukcji krok po kroku Tworzenie połączenia lokacja-lokacja przy użyciu programu [Virtual WAN](virtual-wan-site-to-site-portal.md) w celu skonfigurowania połączenia.

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Połączenia sieci VPN użytkownika (punkt-lokacja)

Możesz połączyć się z zasobami na platformie Azure za pośrednictwem połączenia protokołu IPsec/IKE (IKEv2) lub OpenVPN. Ten typ połączenia wymaga skonfigurowania klienta sieci VPN na komputerze klienckim. Aby uzyskać więcej informacji, [zobacz Create a point-to-site connection](virtual-wan-point-to-site-portal.md)(Tworzenie połączenia punkt-lokacja).

### <a name="expressroute-connections"></a><a name="er"></a>Połączenia ExpressRoute
Usługa ExpressRoute umożliwia łączenie sieci lokalnej z platformą Azure za pośrednictwem połączenia prywatnego. Aby utworzyć połączenie, zobacz [Tworzenie połączenia usługi ExpressRoute przy użyciu Virtual WAN](virtual-wan-expressroute-portal.md).

### <a name="hub-to-vnet-connections"></a><a name="hub"></a>Połączenia typu piasta-sieć wirtualna

Sieć wirtualną platformy Azure można połączyć z koncentratorem wirtualnym. Aby uzyskać więcej informacji, zobacz [Connect your VNet to a hub (Łączenie sieci wirtualnej z koncentratorem).](virtual-wan-site-to-site-portal.md#vnet)

### <a name="transit-connectivity"></a><a name="transit"></a>Łączność tranzytowa

#### <a name="transit-connectivity-between-vnets"></a><a name="transit-vnet"></a>Łączność tranzytowa między sieciami wirtualnmi

Virtual WAN umożliwia łączność tranzytową między sieciami wirtualnmi. Sieci wirtualne łączą się z koncentratorem wirtualnym za pośrednictwem połączenia sieci wirtualnej. Łączność tranzytowa między sieciami wirtualnymi w **sieciach Virtual WAN** Standardowa jest włączona z powodu obecności routera w każdym koncentratorze wirtualnym. Ten router jest tworzony podczas tworzenia koncentratora wirtualnego po raz pierwszy.

Router może mieć cztery stany routingu: Aprowizowany, Aprowizowanie, Niepowodzenie lub Brak. Stan **routingu** znajduje się w Azure Portal, przechodząc do strony Koncentrator wirtualny.

* Stan **Brak** wskazuje, że koncentrator wirtualny nie aprowizuje routera. Taka możliwość może wystąpić, Virtual WAN jest typu *Podstawowa* lub jeśli koncentrator wirtualny został wdrożony przed rozpoczęciem świadczenia usługi.
* Stan **Niepowodzenie wskazuje** niepowodzenie podczas wystąpienia. Aby utworzyć wystąpienia lub zresetować router, możesz zlokalizować opcję Resetuj **router,** przechodząc do strony Przegląd koncentratora wirtualnego w Azure Portal.

Każdy router koncentratora wirtualnego obsługuje zagregowaną przepływność do 50 Gb/s. Łączność między połączeniami sieci wirtualnej zakłada, że obciążenie maszyn wirtualnych wynosi łącznie 2000 we wszystkich sieciach wirtualnych połączonych z pojedynczym koncentratorem wirtualnym.

#### <a name="transit-connectivity-between-vpn-and-expressroute"></a><a name="transit-er"></a>Łączność tranzytowa między siecią VPN i usługą ExpressRoute

Virtual WAN umożliwia łączność tranzytową między siecią VPN i usługą ExpressRoute. Oznacza to, że lokacje połączone z siecią VPN lub użytkownicy zdalni mogą komunikować się z lokacjami połączonymi z usługą ExpressRoute. Istnieje również niejawne założenie, że **flaga Branch-to-branch jest** włączona, a BGP jest obsługiwana w połączeniach VPN i ExpressRoute. Ta flaga może być zlokalizowana w Azure Virtual WAN ustawieniach Azure Portal. Całe zarządzanie trasami jest zapewniane przez router koncentratora wirtualnego, co umożliwia również łączność tranzytową między sieciami wirtualnymi.

### <a name="custom-routing"></a><a name="routing"></a>Routing niestandardowy

Virtual WAN udostępnia zaawansowane ulepszenia routingu. Możliwość skonfigurowania niestandardowych tabel tras, optymalizacji routingu sieci wirtualnej za pomocą skojarzenia i propagacji tras, logicznego grupowania tabel tras z etykietami oraz uproszczenia wielu scenariuszy routingu wirtualnych urządzeń sieciowych (WUS) lub usług udostępnionych.

### <a name="global-vnet-peering"></a><a name="global"></a>Globalna komunikacja równorzędna sieci wirtualnych

Globalna komunikacja równorzędna sieci wirtualnych udostępnia mechanizm łączenia dwóch sieci wirtualnych w różnych regionach. W Virtual WAN połączenia sieci wirtualnej łączą sieci wirtualne z koncentratorami wirtualnymi. Użytkownik nie musi jawnie skonfigurować globalnej komunikacji równorzędnej sieci wirtualnych. W przypadku sieci wirtualnych połączonych z koncentratorem wirtualnym w tym samym regionie są naliczane opłaty za wirtualne sieci równorzędne. W przypadku sieci wirtualnych połączonych z koncentratorem wirtualnym w innym regionie są naliczane opłaty za globalne wirtualne sieci równorzędne.

### <a name="expressroute-traffic-encryption"></a><a name="encryption"></a>Szyfrowanie ruchu w uchu ExpressRoute

Azure Virtual WAN umożliwia szyfrowanie ruchu usługi ExpressRoute. Ta technika zapewnia szyfrowany tranzyt między sieciami lokalnymi i sieciami wirtualnymi platformy Azure za pośrednictwem usługi ExpressRoute bez konieczności korzystania z publicznego Internetu ani używania publicznych adresów IP. Aby uzyskać więcej informacji, zobacz [IPsec over ExpressRoute (IPsec za pośrednictwem expressRoute) Virtual WAN](vpn-over-expressroute.md).

## <a name="locations"></a><a name="locations"></a>Lokalizacje

Aby uzyskać informacje o lokalizacji, zobacz [Virtual WAN partnerów i lokalizacji.](virtual-wan-locations-partners.md)

## <a name="route-tables-for-basic-and-standard-virtual-wans"></a><a name="route"></a>Tabele tras dla wirtualnych sieci WAN w standardowych i podstawowych sieciach WNS

Tabele tras mają teraz funkcje skojarzenia i propagacji. Istniejąca tabela tras to tabela tras, która nie ma tych funkcji. Jeśli masz już istniejące trasy w routingu koncentratora i chcesz korzystać z nowych możliwości, rozważ następujące kwestie:

* **Klienci** usługi Virtual WAN Standard Virtual WAN z już istniejącymi trasami w koncentratorze wirtualnym: jeśli masz już istniejące trasy w sekcji Routing dla koncentratora w centrum usługi Azure Portal, musisz najpierw je usunąć, a następnie spróbować utworzyć nowe tabele tras (dostępne w sekcji Tabele tras dla centrum w programie Azure Portal). Zdecydowanie zaleca się, aby wykonać krok usuwania dla wszystkich centrów w Virtual WAN.

* **Podstawowi klienci** usługi Virtual WAN z istniejącymi trasami w koncentratorze wirtualnym: jeśli masz już istniejące trasy w sekcji Routing dla  koncentratora w ucieku Azure Portal, musisz najpierw je usunąć, a następnie uaktualnić usługę Basic Virtual WAN do standardowego Virtual WAN. Zobacz Upgrade a virtual WAN from Basic to Standard (Uaktualnianie [wirtualnej sieci WAN z podstawowej do standardowej).](upgrade-virtual-wan.md) Zdecydowanie zaleca się, aby wykonać krok usuwania dla wszystkich centrów w Virtual WAN.

## <a name="faq"></a><a name="faq"></a>Często zadawane pytania

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="whats-new"></a><a name="new"></a>Co nowego?

Subskrybowanie źródła danych RSS i wyświetlanie najnowszych Virtual WAN aktualizacji funkcji na [stronie Aktualizacje platformy Azure.](https://azure.microsoft.com/updates/?category=networking&query=VIRTUAL%20WAN)

## <a name="next-steps"></a>Następne kroki

[Tworzenie połączenia lokacja-lokacja przy użyciu Virtual WAN](virtual-wan-site-to-site-portal.md)
