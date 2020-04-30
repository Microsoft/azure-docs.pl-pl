---
title: Usługa Azure Virtual WAN — omówienie | Microsoft Docs
description: Dowiedz się więcej o wirtualnej skalowalnej gałęzi sieci WAN, dostępnych regionach i partnerach.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 02/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 927c09f61ce0847c72cefb51935116070e956861
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80743104"
---
# <a name="about-azure-virtual-wan"></a>Informacje o wirtualnej sieci WAN platformy Azure

Wirtualna sieć WAN platformy Azure to usługa sieciowa, która oferuje wiele funkcji sieciowych, zabezpieczeń i routingu w celu zapewnienia pojedynczego interfejsu operacyjnego. Te funkcje obejmują łączność z gałęzią (za pośrednictwem automatyzacji łączności z urządzeń partnerskich sieci WAN, takich jak SD-WAN lub VPN CPE), łączności sieci VPN typu lokacja-lokacja, sieci VPN użytkowników zdalnych (połączeń punkt-lokacja), połączeń prywatnych (ExpressRoute), łączności z chmurą (łączności przechodniej dla sieci wirtualnych), sieci VPN ExpressRoute, routingu, zapory systemu Nie musisz mieć wszystkich tych przypadków użycia, aby rozpocząć korzystanie z wirtualnej sieci WAN. Możesz po prostu rozpocząć pracę z tylko jednym przypadkiem użycia i dostosować sieć w miarę rozwoju. Wirtualna architektura sieci WAN jest architekturą Hub i szprych z wbudowaną skalą i wydajnością dla gałęzi (urządzeń sieci VPN/SD-WAN), użytkowników (klientów sieci VPN platformy Azure/OpenVPN/IKEv2), obwodów ExpressRoute i sieci wirtualnych. Umożliwia ona [globalną architekturę sieci tranzytowej](virtual-wan-global-transit-network-architecture.md) , w której sieć hostowana w chmurze "Hub" umożliwia przechodnią łączność między punktami końcowymi, które mogą być rozproszone dla różnych typów "szprych".

Regiony platformy Azure służą jako centra, z którymi można nawiązać połączenie. Wszystkie centra są połączone z pełną siatką w standardowej wirtualnej sieci WAN, dzięki czemu użytkownik może łatwo korzystać z sieci szkieletowej firmy Microsoft w przypadku połączeń dowolnego typu (dowolnych). W przypadku połączeń szprych z urządzeniami z SD/WAN/VPN użytkownicy mogą ręcznie skonfigurować ją w usłudze Azure Virtual WAN lub użyć rozwiązania partnerskiego wirtualnego urządzenia WAN (SD-WAN/VPN) w celu skonfigurowania łączności z platformą Azure. Mamy listę partnerów, którzy obsługują automatyzację łączności (możliwość eksportowania informacji o urządzeniu do platformy Azure, pobierania konfiguracji platformy Azure i nawiązywania połączeń) przy użyciu wirtualnej sieci WAN platformy Azure. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [partnerów i lokalizacji wirtualnych sieci WAN](virtual-wan-locations-partners.md) . 

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

* **(Wersja zapoznawcza) połączenia** między centrami — koncentratory są połączone ze sobą w wirtualnej sieci WAN. Oznacza to, że gałąź, użytkownik lub Sieć wirtualna połączona z koncentratorem lokalnym może komunikować się z inną gałęzią lub siecią wirtualną przy użyciu pełnej architektury sieci podłączonych centrów. Można także połączyć sieci wirtualnych w ramach przechodzenia przez centrum wirtualne, a także sieci wirtualnych między centrami przy użyciu platformy połączonej z centrum-centrum.

* **Tabela tras koncentratora:** możesz utworzyć trasę koncentratora wirtualnego i zastosować tę trasę do tabeli tras koncentratora wirtualnego. Możesz zastosować wiele tras do tabeli tras koncentratora wirtualnego.

**Dodatkowe zasoby wirtualnej sieci WAN**

  * **Lokacja:** Ten zasób jest używany tylko w przypadku połączeń lokacja-lokacja. Zasób lokacji to **vpnsite**. Reprezentuje lokalne urządzenie sieci VPN i jego ustawienia. Dzięki współpracy z partnerem usługi Virtual WAN otrzymasz wbudowane rozwiązanie do automatycznego eksportowania tych informacji na platformę Azure.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Typy łączności

Wirtualna sieć WAN umożliwia łączenie następujących typów: sieci VPN typu lokacja-lokacja, sieci VPN użytkownika (punkt-lokacja) i ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Połączenia sieci VPN typu lokacja-lokacja

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwan.png)

Podczas tworzenia połączenia sieci VPN typu lokacja-lokacja można korzystać z dostępnego partnera. Jeśli nie chcesz korzystać z partnera, możesz skonfigurować połączenie ręcznie. Aby uzyskać więcej informacji, zobacz [Tworzenie połączenia typu lokacja-lokacja przy użyciu wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md).

#### <a name="virtual-wan-partner-workflow"></a><a name="s2spartner"></a>Przepływ pracy wirtualnego partnera sieci WAN

Podczas pracy z partnerem wirtualnego sieci WAN przepływ pracy to:

1. Kontroler urządzenia w oddziale (VPN/SDWAN) jest uwierzytelniany w celu wyeksportowania informacji z lokacji na platformę Azure za pomocą [jednostki usługi platformy Azure](../active-directory/develop/howto-create-service-principal-portal.md).
2. Kontroler urządzenia w oddziale (VPN/SDWAN) uzyskuje konfigurację łączności z platformy Azure i aktualizuje urządzenie lokalne. To umożliwia zautomatyzowanie pobierania, edytowania i aktualizowania konfiguracji lokalnego urządzenia sieci VPN.
3. Gdy urządzenie jest prawidłowo skonfigurowane pod kątem platformy Azure, tworzone jest połączenie typu lokacja-lokacja (dwa aktywne tunele) z siecią WAN platformy Azure. Platforma Azure obsługuje zarówno protokół IKEv1, jak i IKEv2. Protokół BGP jest opcjonalny.

#### <a name="partners-for-site-to-site-virtual-wan-connections"></a><a name="partners"></a>Partnerzy dla połączeń wirtualnych sieci WAN między lokacjami

Listę dostępnych partnerów i lokalizacji można znaleźć w artykule dotyczącym [partnerów i lokalizacji wirtualnych sieci WAN](virtual-wan-locations-partners.md) .

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Połączenia sieci VPN użytkownika (punkt-lokacja)

Możesz połączyć się z zasobami na platformie Azure za pośrednictwem protokołu IPsec/IKE (IKEv2) lub połączenia OpenVPN. Ten typ połączenia wymaga skonfigurowania klienta sieci VPN na komputerze klienckim. Aby uzyskać więcej informacji, zobacz [Tworzenie połączenia typu punkt-lokacja](virtual-wan-point-to-site-portal.md).

### <a name="expressroute-connections"></a><a name="er"></a>Połączenia ExpressRoute
ExpressRoute umożliwia łączenie sieci lokalnej z platformą Azure za pośrednictwem połączenia prywatnego. Aby utworzyć połączenie, zobacz [Tworzenie połączenia usługi ExpressRoute przy użyciu wirtualnej sieci WAN](virtual-wan-expressroute-portal.md).

## <a name="locations"></a><a name="locations"></a>Lokalizacje

Informacje o lokalizacji znajdują się w artykule dotyczącym [partnerów i lokalizacji wirtualnych sieci WAN](virtual-wan-locations-partners.md) .

## <a name="faq"></a><a name="faq"></a>Najczęściej zadawane pytania

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Następne kroki

[Tworzenie połączenia typu lokacja-lokacja przy użyciu wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md)
