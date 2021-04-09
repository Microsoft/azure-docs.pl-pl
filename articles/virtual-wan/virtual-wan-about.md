---
title: Usługa Azure Virtual WAN — omówienie | Microsoft Docs
description: Dowiedz się więcej o wirtualnej skalowalnej gałęzi sieci WAN, dostępnych regionach i partnerach.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 7f8bc4cadc720c7ac02a7246e741552e81eb3876
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057154"
---
# <a name="what-is-azure-virtual-wan"></a>Co to jest usługa Azure Virtual WAN?

Wirtualna sieć WAN platformy Azure to usługa sieciowa, która udostępnia wiele funkcji sieci, zabezpieczeń i routingu w celu zapewnienia pojedynczego interfejsu operacyjnego. Te funkcje obejmują łączność z gałęzią (za pośrednictwem automatyzacji łączności z wirtualnych urządzeń partnerskich sieci WAN, takich jak SD-WAN lub VPN CPE). połączenie sieci VPN typu lokacja-lokacja, połączenie VPN użytkownika zdalnego (punkt-lokacja), połączenie prywatne (ExpressRoute), łączność w chmurze (łączność przechodnia dla sieci wirtualnych), Sieć VPN ExpressRoute między łącznością, routingiem, zaporą platformy Azure i szyfrowaniem w celu połączenia prywatnego. Nie musisz mieć wszystkich tych przypadków użycia, aby rozpocząć korzystanie z wirtualnej sieci WAN. Możesz po prostu rozpocząć pracę z tylko jednym przypadkiem użycia, a następnie dostosować sieć w miarę rozwoju.

Wirtualna architektura sieci WAN to architektura koncentratora i szprych ze skalą i wydajnością wbudowaną dla gałęzi (urządzenia sieci VPN/SD-WAN), użytkownicy (klienci sieci VPN/OpenVPN/IKEv2), obwody usługi ExpressRoute i sieci wirtualne. Umożliwia ona [globalną architekturę sieci tranzytowej](virtual-wan-global-transit-network-architecture.md), w której sieć hostowana w chmurze "Hub" umożliwia przechodnią łączność między punktami końcowymi, które mogą być rozproszone dla różnych typów "szprych".

Regiony platformy Azure służą jako centra, z którymi można nawiązać połączenie. Wszystkie centra są połączone z pełną siatką w standardowej wirtualnej sieci WAN, dzięki czemu użytkownik może łatwo korzystać z sieci szkieletowej firmy Microsoft w przypadku połączeń dowolnego typu (dowolnych). W przypadku połączeń szprych z urządzeniami z SD/WAN/VPN użytkownicy mogą ręcznie skonfigurować je w wirtualnej sieci WAN platformy Azure lub użyć rozwiązania partnerskiego sieci WAN CPE (SD-WAN/VPN) w celu skonfigurowania połączenia z platformą Azure. Mamy listę partnerów, którzy obsługują automatyzację łączności (możliwość eksportowania informacji o urządzeniu do platformy Azure, pobierania konfiguracji platformy Azure i nawiązywania połączeń) przy użyciu wirtualnej sieci WAN platformy Azure. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [partnerów i lokalizacji wirtualnych sieci WAN](virtual-wan-locations-partners.md) .

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwan1.png)

Ten artykuł zawiera szybki podgląd łączności sieciowej w wirtualnej sieci WAN platformy Azure. Usługa Virtual WAN oferuje następujące korzyści:

* **Zintegrowane rozwiązania łączności w centrum i szprychie:** Automatyzowanie konfiguracji lokacja-lokacja i łączności między lokacjami lokalnymi i centrum platformy Azure.
* **Automatyczna instalacja i konfiguracja szprych:** bezproblemowe łączenie sieci wirtualnych i obciążeń z koncentratorem na platformie Azure.
* **Intuicyjne Rozwiązywanie problemów:** Możesz zobaczyć kompleksowy przepływ na platformie Azure, a następnie użyć tych informacji do podjęcia wymaganych akcji.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Wirtualne sieci WAN w warstwach Podstawowa i Standardowa

Istnieją dwa typy wirtualnych sieci WAN: podstawowa i standardowa. W poniższej tabeli przedstawiono dostępne konfiguracje dla każdego typu.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Aby uzyskać instrukcje dotyczące uaktualniania wirtualnej sieci WAN, zobacz [uaktualnianie wirtualnej sieci WAN z warstwy Podstawowa do standardowa](upgrade-virtual-wan.md).

## <a name="architecture"></a><a name="architecture"></a>Architektura

Aby uzyskać informacje o architekturze wirtualnej sieci WAN i sposobach migracji do wirtualnej sieci WAN, zobacz następujące artykuły:

* [Architektura wirtualnej sieci WAN](migrate-from-hub-spoke-topology.md)
* [Architektura globalnej sieci tranzytowej](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Zasoby usługi Virtual WAN

Aby skonfigurować kompleksową wirtualną sieć WAN, należy utworzyć następujące zasoby:

* **virtualWAN:** zasób virtualWAN reprezentuje wirtualną nakładkę sieci platformy Azure i stanowi zbiór kilku zasobów. Zawiera linki do wszystkich koncentratorów wirtualnych, które mają się znaleźć w wirtualnej sieci WAN. Zasoby usługi Virtual WAN są wzajemnie izolowane i nie mogą zawierać wspólnego koncentratora. Koncentratory wirtualne między wirtualną siecią WAN nie komunikują się ze sobą.

* **Koncentrator:** koncentrator wirtualny to sieć wirtualna zarządzana przez firmę Microsoft. Koncentrator zawiera różne punkty końcowe usługi umożliwiające łączność. Z sieci lokalnej (vpnsite) można nawiązać połączenie z VPN Gateway wewnątrz koncentratora wirtualnego, podłączyć obwody usługi ExpressRoute do koncentratora wirtualnego, a nawet podłączyć użytkowników mobilnych do bramy typu punkt-lokacja w koncentratorze wirtualnym. Koncentrator to podstawowy element sieci w danym regionie. W jednym regionie świadczenia usługi Azure może znajdować się tylko jeden koncentrator.

  Brama koncentratora różni się od bramy sieci wirtualnej używanej w usługach ExpressRoute i VPN Gateway. Na przykład w przypadku korzystania z wirtualnej sieci WAN nie można utworzyć połączenia typu lokacja-lokacja z lokacji lokalnej bezpośrednio do sieci wirtualnej. Zamiast tego należy utworzyć połączenie lokacja-lokacja z centrum. Ruch zawsze jest kierowany przez bramę koncentratora. Oznacz to, że sieci wirtualne nie muszą mieć własnych bram sieci wirtualnej. Usługa Virtual WAN umożliwia łatwe skalowanie sieci wirtualnych za pośrednictwem koncentratora wirtualnego i bramy koncentratora wirtualnego.

* **Połączenie sieci wirtualnej koncentratora:** zasób połączenia sieci wirtualnej koncentratora umożliwia bezproblemowe łączenie koncentratora z siecią wirtualną.

* **Połączenie między centrum:** Koncentratory są połączone ze sobą w wirtualnej sieci WAN. Oznacza to, że gałąź, użytkownik lub Sieć wirtualna połączona z koncentratorem lokalnym może komunikować się z inną gałęzią lub siecią wirtualną przy użyciu pełnej architektury sieci podłączonych centrów. Można także połączyć sieci wirtualnych w ramach przechodzenia przez centrum wirtualne, a także sieci wirtualnych między centrami przy użyciu platformy połączonej z centrum-centrum.

* **Tabela tras koncentratora:** możesz utworzyć trasę koncentratora wirtualnego i zastosować tę trasę do tabeli tras koncentratora wirtualnego. Możesz zastosować wiele tras do tabeli tras koncentratora wirtualnego.

**Dodatkowe zasoby wirtualnej sieci WAN**

* **Lokacja:** Ten zasób jest używany tylko w przypadku połączeń lokacja-lokacja. Zasób lokacji to **vpnsite**. Reprezentuje lokalne urządzenie sieci VPN i jego ustawienia. Dzięki współpracy z partnerem usługi Virtual WAN otrzymasz wbudowane rozwiązanie do automatycznego eksportowania tych informacji na platformę Azure.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Typy łączności

Wirtualna sieć WAN umożliwia łączenie następujących typów: sieci VPN typu lokacja-lokacja, sieci VPN użytkownika (punkt-lokacja) i ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Połączenia sieci VPN typu lokacja-lokacja

Możesz połączyć się z zasobami na platformie Azure za pośrednictwem połączenia typu lokacja-lokacja IPsec/IKE (IKEv2). Aby uzyskać więcej informacji, zobacz [Tworzenie połączenia typu lokacja-lokacja przy użyciu wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md). 

Ten typ połączenia wymaga urządzenia sieci VPN lub wirtualnego urządzenia partnerskiego sieci WAN. Wirtualne partnerzy sieci WAN zapewniają automatyzację łączności, która umożliwia eksportowanie informacji o urządzeniu do platformy Azure, pobieranie konfiguracji platformy Azure i nawiązywanie połączenia z wirtualnym koncentratorem sieci WAN platformy Azure. Listę dostępnych partnerów i lokalizacji można znaleźć w artykule dotyczącym [partnerów i lokalizacji wirtualnych sieci WAN](virtual-wan-locations-partners.md) . Jeśli dostawca urządzeń sieci VPN/SD-WAN nie jest wymieniony w wymienionym łączu, można uprościć korzystanie z instrukcji krok po kroku [Tworzenie połączenia typu lokacja-lokacja przy użyciu wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md) w celu skonfigurowania połączenia.

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Połączenia sieci VPN użytkownika (punkt-lokacja)

Możesz połączyć się z zasobami na platformie Azure za pośrednictwem protokołu IPsec/IKE (IKEv2) lub połączenia OpenVPN. Ten typ połączenia wymaga skonfigurowania klienta sieci VPN na komputerze klienckim. Aby uzyskać więcej informacji, zobacz [Tworzenie połączenia typu punkt-lokacja](virtual-wan-point-to-site-portal.md).

### <a name="expressroute-connections"></a><a name="er"></a>Połączenia ExpressRoute
ExpressRoute umożliwia łączenie sieci lokalnej z platformą Azure za pośrednictwem połączenia prywatnego. Aby utworzyć połączenie, zobacz [Tworzenie połączenia usługi ExpressRoute przy użyciu wirtualnej sieci WAN](virtual-wan-expressroute-portal.md).

### <a name="hub-to-vnet-connections"></a><a name="hub"></a>Połączenia między sieciami wirtualnymi

Możesz połączyć sieć wirtualną platformy Azure z koncentratorem wirtualnym. Aby uzyskać więcej informacji, zobacz [łączenie sieci wirtualnej z centrum](virtual-wan-site-to-site-portal.md#vnet).

### <a name="transit-connectivity"></a><a name="transit"></a>Łączność tranzytowa

#### <a name="transit-connectivity-between-vnets"></a><a name="transit-vnet"></a>Komunikacja tranzytowa między sieci wirtualnych

Wirtualna sieć WAN umożliwia przesyłanie połączeń między sieci wirtualnych. Sieci wirtualnych nawiązuje połączenie z koncentratorem wirtualnym za pośrednictwem połączenia sieci wirtualnej. Komunikacja tranzytowa między sieci wirtualnych w **standardowej wirtualnej sieci WAN** jest włączona z powodu obecności routera w każdym koncentratorze wirtualnym. Ten router jest inicjowany podczas pierwszego tworzenia koncentratora wirtualnego.

Router może mieć cztery Stany routingu: zainicjowane, Inicjowanie obsługi administracyjnej, Niepowodzenie lub brak. **Stan routingu** znajduje się w Azure Portal, przechodząc do strony centrum wirtualne.

* Stan **Brak** oznacza, że koncentrator nie udostępnił routera. Taka sytuacja może wystąpić, Jeśli wirtualna sieć WAN jest typu *Basic* lub jeśli koncentrator wirtualny został wdrożony przed udostępnieniem usługi.
* Stan **niepowodzenia wskazuje** , że wystąpił błąd podczas tworzenia wystąpienia. W celu utworzenia wystąpienia lub zresetowania routera można zlokalizować opcję **Reset routera** , przechodząc do strony omówienia koncentratora wirtualnego w Azure Portal.

Każdy router koncentratora wirtualnego obsługuje zagregowaną przepływność do 50 GB/s. Łączność między połączeniami sieci wirtualnej polega na przyłożeniu całkowitej 2000 obciążeń maszyny wirtualnej na wszystkie sieci wirtualnych połączone z jednym koncentratorem wirtualnym.

#### <a name="transit-connectivity-between-vpn-and-expressroute"></a><a name="transit-er"></a>Transport łączności między sieciami VPN i ExpressRoute

Wirtualna sieć WAN umożliwia przesyłanie połączeń między sieciami VPN i ExpressRoute. Oznacza to, że witryny połączone z siecią VPN lub Użytkownicy zdalni mogą komunikować się z witrynami połączonymi z ExpressRoute. Istnieje również niejawne założenie, że **Flaga odgałęzienie do gałęzi** jest włączona, a protokół BGP jest obsługiwany w połączeniach VPN i ExpressRoute. Ta flaga może znajdować się w ustawieniach sieci wirtualnej platformy Azure w Azure Portal. Wszystkie Zarządzanie trasami jest dostarczane przez router koncentratora wirtualnego, co umożliwia także łączność między sieciami wirtualnymi.

### <a name="custom-routing"></a><a name="routing"></a>Routing niestandardowy

Wirtualna sieć WAN zapewnia zaawansowane ulepszenia routingu. Możliwość konfigurowania niestandardowych tabel tras, optymalizowania routingu sieci wirtualnych za pomocą kojarzenia tras i propagacji, logiczne grupowanie tabel tras z etykietami i uproszczenie wielu różnych wirtualnych urządzeń sieciowych (urządzenie WUS) lub scenariuszy routingu usług udostępnionych.

### <a name="global-vnet-peering"></a><a name="global"></a>Globalna komunikacja równorzędna sieci wirtualnych

Globalna komunikacja równorzędna sieci wirtualnych zapewnia mechanizm łączenia dwóch sieci wirtualnych w różnych regionach. W obszarze wirtualna sieć WAN połączenia sieci wirtualnej łączą sieci wirtualnych z koncentratorami wirtualnymi. Użytkownik nie musi jawnie konfigurować globalnej komunikacji równorzędnej sieci wirtualnej. Sieci wirtualnych połączone z koncentratorem wirtualnym w tym samym regionie wiążą się z opłatami za wirtualne sieci równorzędne. Sieci wirtualnych połączone z koncentratorem wirtualnym w innym regionie wiążą się z opłatami za globalne wirtualne sieci równorzędne.

### <a name="expressroute-traffic-encryption"></a><a name="encryption"></a>Szyfrowanie ruchu ExpressRoute

Wirtualna sieć WAN platformy Azure zapewnia możliwość szyfrowania ruchu ExpressRoute. Technika zawiera zaszyfrowane przedziały między sieciami lokalnymi i sieciami wirtualnymi platformy Azure za pośrednictwem usługi ExpressRoute, bez przechodzenia przez publiczny Internet ani publicznych adresów IP. Aby uzyskać więcej informacji, zobacz [IPSec over ExpressRoute dla wirtualnej sieci WAN](vpn-over-expressroute.md).

## <a name="locations"></a><a name="locations"></a>Miejsce

Informacje o lokalizacji znajdują się w artykule dotyczącym [partnerów i lokalizacji wirtualnych sieci WAN](virtual-wan-locations-partners.md) .

## <a name="route-tables-for-basic-and-standard-virtual-wans"></a><a name="route"></a>Tabele tras dla wirtualnych sieci WAN w warstwach Podstawowa i Standardowa

Tabele tras mają teraz funkcje do skojarzenia i propagacji. Istniejąca tabela tras jest tabelą tras, która nie ma tych funkcji. Jeśli masz już istniejące trasy w usłudze Routing centralny i chcesz korzystać z nowych funkcji, weź pod uwagę następujące kwestie:

* **Klienci sieci wirtualnej w warstwie Standardowa ze wstępnie istniejącymi trasami w koncentratorze wirtualnym**: Jeśli w sekcji routingu znajduje się wstępnie istniejące trasy dla centrum w Azure Portal, musisz najpierw je usunąć, a następnie próbować utworzyć nowe tabele tras (dostępne w sekcji tabele tras dla centrum w Azure Portal). Zdecydowanie zaleca się wykonanie kroku usuwania dla wszystkich centrów w wirtualnej sieci WAN.

* **Podstawowa klienci wirtualnych sieci WAN ze wstępnie istniejącymi trasami w koncentratorze wirtualnym**: Jeśli w sekcji Routing znajduje się wstępnie istniejące trasy dla centrum w Azure Portal, musisz najpierw je usunąć, a następnie **uaktualnić** podstawową wirtualną sieć WAN do standardowej wirtualnej sieci WAN. Zobacz [uaktualnianie wirtualnej sieci WAN z warstwy Podstawowa do standardowa](upgrade-virtual-wan.md). Zdecydowanie zaleca się wykonanie kroku usuwania dla wszystkich centrów w wirtualnej sieci WAN.

## <a name="faq"></a><a name="faq"></a>Często zadawane pytania

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="whats-new"></a><a name="new"></a>Co nowego?

Zasubskrybuj źródło danych RSS i zapoznaj się z najnowszymi aktualizacjami funkcji wirtualnej sieci WAN na stronie [aktualizacji platformy Azure](https://azure.microsoft.com/updates/?category=networking&query=VIRTUAL%20WAN) .

## <a name="next-steps"></a>Następne kroki

[Tworzenie połączenia typu lokacja-lokacja przy użyciu wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md)
