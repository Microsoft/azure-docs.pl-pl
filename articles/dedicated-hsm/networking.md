---
title: Zagadnienia dotyczące sieci — dedykowany moduł HSM platformy Azure | Microsoft Docs
description: Omówienie zagadnień dotyczących sieci dotyczących dedykowanych wdrożeń modułu HSM platformy Azure
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbaldwin
ms.openlocfilehash: 3764b261b491c660da16d7989be20742fead1fbf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91359158"
---
# <a name="azure-dedicated-hsm-networking"></a>Dedykowana sieć HSM platformy Azure

Dedykowany moduł HSM platformy Azure wymaga wysoce bezpiecznego środowiska sieciowego. Jest to prawdziwe, czy pochodzi z chmury platformy Azure z powrotem do środowiska IT klienta (lokalnego), przy użyciu aplikacji rozproszonych lub scenariuszy wysokiej dostępności. Usługa Azure Networking zapewnia te informacje, a istnieją cztery różne obszary, które należy rozwiązać.

- Tworzenie urządzeń HSM w ramach Virtual Network (VNet) na platformie Azure
- Łączenie lokalnych z zasobami opartymi na chmurze w celu konfigurowania i zarządzania urządzeniami HSM
- Tworzenie i łączenie sieci wirtualnych w celu połączenia między zasobami aplikacji a urządzeniami modułu HSM
- Łączenie sieci wirtualnych między regionami w celu komunikacji wzajemnej oraz włączanie scenariuszy wysokiej dostępności

## <a name="virtual-network-for-your-dedicated-hsms"></a>Sieć wirtualna dla dedykowanego sprzętowych modułów zabezpieczeń

Dedykowane sprzętowych modułów zabezpieczeń są zintegrowane z Virtual Network i umieszczane w sieci prywatnej przez klientów na platformie Azure. Pozwala to na dostęp do urządzeń z maszyn wirtualnych lub zasobów obliczeniowych w sieci wirtualnej.  
Aby uzyskać więcej informacji na temat integrowania usług platformy Azure z siecią wirtualną i udostępnianych przez nią funkcji, zobacz temat Dokumentacja [usługi Virtual Network for Azure](../virtual-network/virtual-network-for-azure-services.md) .

### <a name="virtual-networks"></a>Sieci wirtualne

Przed zainicjowaniem obsługi dedykowanego urządzenia HSM klienci muszą najpierw utworzyć Virtual Network na platformie Azure lub użyć tego, który już istnieje w subskrypcji klientów. Sieć wirtualna definiuje obwód zabezpieczeń dla dedykowanego urządzenia HSM. Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnych, zobacz [dokumentację sieci wirtualnej](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Podsieci

Podsieci umożliwiają segmentację sieci wirtualnej na oddzielne przestrzenie adresowe, które są używane przez zasoby platformy Azure, które znajdują się w nich. Dedykowane sprzętowych modułów zabezpieczeń są wdrażane w podsieci w sieci wirtualnej. Każde urządzenie dedykowane HSM wdrożone w podsieci klienta otrzyma prywatny adres IP z tej podsieci. Podsieć, w której wdrożono urządzenie HSM, musi być jawnie delegowana do usługi: Microsoft. HardwareSecurityModules/modułów dedicatedhsms. Zapewnia to pewne uprawnienia do usługi HSM do wdrożenia w podsieci. Delegowanie do dedykowanej sprzętowych modułów zabezpieczeń nakłada pewne ograniczenia zasad w podsieci. Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) i trasy User-Defined (UDR) nie są obecnie obsługiwane w delegowanych podsieciach. W związku z tym, gdy podsieć zostanie delegowana do dedykowanej sprzętowych modułów zabezpieczeń, może być używana tylko do wdrażania zasobów modułu HSM. Wdrożenie innych zasobów klienta w podsieci zakończy się niepowodzeniem.


### <a name="expressroute-gateway"></a>Brama ExpressRoute

Wymagana bieżąca architektura jest konfiguracją bramy usługi ER w podsieci klientów, w której należy umieścić urządzenie HSM umożliwiające integrację urządzenia HSM z platformą Azure. Nie można użyć tej bramy usługi ER do łączenia lokalizacji lokalnych z urządzeniami modułu HSM klientów na platformie Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Łączenie lokalnego z platformą Azure

Podczas tworzenia zasobów opartych na chmurze jest typowe wymaganie połączenia prywatnego z lokalnymi zasobami IT. W przypadku dedykowanego modułu HSM, jest on głównie przeznaczony dla oprogramowania klienckiego modułu HSM, aby skonfigurować urządzenia HSM, a także dla działań takich jak kopie zapasowe i ściągania dzienników z sprzętowych modułów zabezpieczeń for Analysis. Kluczowym punktem decyzyjnym poniżej jest charakter połączenia, w którym są dostępne opcje.  Najbardziej elastyczną opcją jest sieć VPN typu lokacja-lokacja, ponieważ może istnieć wiele zasobów lokalnych, które wymagają bezpiecznej komunikacji z zasobami (w tym sprzętowych modułów zabezpieczeń) w chmurze platformy Azure. Będzie to wymagało, aby organizacja klienta mogła korzystać z urządzenia sieci VPN w celu ułatwienia połączenia. Połączenia sieci VPN typu punkt-lokacja można użyć, jeśli istnieje tylko jeden punkt końcowy w środowisku lokalnym, takim jak pojedyncza stacja robocza.
Aby uzyskać więcej informacji na temat opcji łączności, zobacz [VPN Gateway Opcje planowania](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> W tej chwili ExpressRoute nie jest opcją połączenia z zasobami lokalnymi. Należy również zauważyć, że brama ExpressRoute opisana powyżej nie dotyczy połączeń z infrastrukturą lokalną.

### <a name="point-to-site-vpn"></a>Sieć VPN typu punkt-lokacja

Wirtualna sieć prywatna typu punkt-lokacja to najprostsza forma bezpiecznego połączenia z lokalnym punktem końcowym. Może to być istotne, jeśli zamierzasz mieć tylko jedną stację roboczą administracji dla dedykowanych sprzętowych modułów zabezpieczeń opartych na platformie Azure.

### <a name="site-to-site-vpn"></a>Sieć VPN typu lokacja-lokacja

Wirtualna sieć prywatna typu lokacja-lokacja umożliwia bezpieczną komunikację między dedykowanymi sprzętowych modułów zabezpieczeńami opartymi na platformie Azure a lokalnymi. Przyczyną tego jest posiadanie funkcji tworzenia kopii zapasowych dla lokalnego modułu HSM i wymaganie połączenia między nimi w celu uruchomienia kopii zapasowej.

## <a name="connecting-virtual-networks"></a>Łączenie sieci wirtualnych

Typowa architektura wdrażania dedykowanego modułu HSM rozpocznie się z użyciem pojedynczej sieci wirtualnej i odpowiedniej podsieci, w której są tworzone i inicjowane urządzenia HSM. W tym samym regionie mogły istnieć dodatkowe sieci wirtualne i podsieci dla składników aplikacji, które mogłyby korzystać z dedykowanego modułu HSM. Aby umożliwić komunikację między tymi sieciami, używamy Virtual Network komunikacji równorzędnej.

### <a name="virtual-network-peering"></a>Komunikacja równorzędna sieci wirtualnej

Jeśli istnieje wiele sieci wirtualnych w regionie, który musi uzyskać dostęp do wszystkich zasobów, Virtual Network Komunikacja równorzędna może być używana do tworzenia bezpiecznych kanałów komunikacji między nimi.  Wirtualne sieci równorzędne nie tylko zapewniają bezpieczną komunikację, ale zapewniają również połączenia o małym opóźnieniu i dużej przepustowości między zasobami na platformie Azure.

![Komunikacja równorzędna sieci](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Łączenie w regionach platformy Azure

Urządzenia HSM umożliwiają przekierowanie ruchu do alternatywnego modułu HSM za pośrednictwem bibliotek oprogramowania. Przekierowywanie ruchu jest przydatne, jeśli urządzenia nie powiodą się lub dostęp do urządzenia zostanie utracony. Scenariusze awarii poziomu regionalnego można złagodzić przez wdrożenie sprzętowych modułów zabezpieczeń w innych regionach i umożliwienie komunikacji między sieciami wirtualnymi w różnych regionach.

### <a name="cross-region-ha-using-vpn-gateway"></a>Międzyregionowa HA przy użyciu bramy sieci VPN

W przypadku aplikacji rozproszonych globalnie lub dla regionalnych scenariuszy trybu failover o wysokiej dostępności wymagane jest połączenie sieci wirtualnych między regionami. Za pomocą dedykowanego modułu HSM platformy Azure można osiągnąć wysoką dostępność przy użyciu VPN Gateway, która zapewnia bezpieczny tunel między dwiema sieciami wirtualnymi. Aby uzyskać więcej informacji na temat połączeń między sieciami wirtualnymi przy użyciu VPN Gateway, zobacz artykuł [co to jest VPN Gateway?](../vpn-gateway/design.md#V2V)

> [!NOTE]
> Globalna komunikacja równorzędna sieci wirtualnych nie jest dostępna w scenariuszach łączności między regionami z dedykowanymi sprzętowych modułów zabezpieczeńami w tej chwili, a zamiast tego należy użyć bramy sieci VPN. 

![Diagram przedstawia dwa regiony połączone dwoma bramami V P N. Każdy region zawiera równorzędne sieci wirtualne.](media/networking/global-vnet.png)

## <a name="next-steps"></a>Następne kroki

- [Często zadawane pytania](faq.md)
- [Możliwości obsługi](supportability.md)
- [Wysoka dostępność](high-availability.md)
- [Zabezpieczenia fizyczne](physical-security.md)
- [Monitorowanie](monitoring.md)
- [Architektura wdrożenia](deployment-architecture.md)