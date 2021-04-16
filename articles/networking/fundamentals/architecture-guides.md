---
title: Azure Networking dokumentacji architektury
description: Zapoznaj się z dokumentacją architektury referencyjnej dostępną dla usług sieciowych platformy Azure.
services: networking
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 03/30/2021
ms.author: kumud
ms.openlocfilehash: c98bdbb9fba2a6ba01e4ce590c36d57e68390f17
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484799"
---
# <a name="azure-networking-architecture-documentation"></a>Azure Networking dokumentacji architektury

Ten artykuł zawiera informacje o przewodnikach dotyczących architektury, które mogą ułatwić eksplorowanie różnych usług sieciowych na platformie Azure dostępnych do tworzenia aplikacji.

## <a name="networking-overview"></a>Omówienie sieci

W poniższej tabeli przedstawiono artykuły, które zawierają omówienie sieci wirtualnego centrum danych oraz topologii gwiazdy na platformie Azure.

|Tytuł |Opis  |
|---------|---------|
|[Wirtualne centra danych](/azure/architecture/vdc/networking-virtual-datacenter)   | Przedstawia perspektywę sieci wirtualnego centrum danych na platformie Azure.       |
|[Topologia piasty i szprych](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)  |Zawiera omówienie topologii sieci piasty i szprych na platformie Azure wraz z informacjami o limitach subskrypcji i wielu centrach.          |

## <a name="connect-to-azure-resources"></a>Nawiązywanie połączenia z zasobami platformy Azure

W poniższej tabeli przedstawiono artykuły dotyczące usług Azure Networking, które zapewniają łączność między zasobami platformy Azure, łączność z sieci lokalnej do zasobów platformy Azure oraz łączność między oddziałami na platformie Azure.

|Tytuł |Opis  |
|---------|---------|
|[Dodawanie przestrzeni adresowych IP do wirtualnych sieci równorzędnych](/azure/architecture/networking/prefixes/add-ip-space-peered-vnet)     | Udostępnia skrypty, które ułatwiają dodawanie przestrzeni adresowych IP do równorzędnych sieci wirtualnych.        |
|[Łączenie serwerów autonomicznych za pomocą karty sieciowej platformy Azure](/azure/architecture/hybrid/azure-network-adapter)   | Przedstawia sposób łączenia lokalnego serwera autonomicznego z sieciami wirtualnymi Microsoft Azure przy użyciu karty sieciowej platformy Azure wdrożonej za pośrednictwem usługi Windows Admin Center.        |
|[Wybieranie między komunikacją równorzędną sieci wirtualnych i bramami sieci VPN](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering)   | Porównuje dwa sposoby łączenia sieci wirtualnych na platformie Azure: komunikacja równorzędna sieci wirtualnych i bramy sieci VPN.        |
|[Łączenie sieci lokalnej z platformą Azure](/azure/architecture/reference-architectures/hybrid-networking/)  | Porównuje opcje łączenia sieci lokalnej z siecią azure Virtual Network (VNet). Dla każdej opcji jest dostępna bardziej szczegółowa architektura referencyjna.        |
|[Architektura łączności SD-WAN z Azure Virtual WAN](../../virtual-wan/sd-wan-connectivity-architecture.md)|W tym artykule opisano różne opcje łączności służące do łączenia prywatnej sieci WAN zdefiniowanej programowo (SD-WAN) z Azure Virtual WAN.|

## <a name="deploy-highly-available-applications"></a>Wdrażanie aplikacji o wysokiej dostępie

W poniższej tabeli przedstawiono artykuły opisujące sposób wdrażania aplikacji w celu wysokiej dostępności przy użyciu kombinacji Azure Networking usług.

|Tytuł |Opis  |
|---------|---------|
|[Aplikacja N-warstwowa w wielu regionach](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server))  | Opisuje n-warstwową aplikację w wielu regionach, która używa usługi Traffic Manager do przekierowania żądań przychodzących do regionu podstawowego. Jeśli ten region stanie się niedostępny, program Traffic Manager do regionu pomocniczego.      |
| [Wielodostępna usługa SaaS na platformie Azure](https://docs.microsoft.com/azure/architecture/example-scenario/multi-saas/multitenant-saas)       |   Korzysta z rozwiązania wielodostępowego, które obejmuje kombinację Front Door i Application Gateway.  Front Door równoważenie obciążenia ruchu między regionami i trasami usługi Application Gateway a obciążenie równoważy ruch wewnętrznie w aplikacji do różnych usług, które spełniają potrzeby biznesowe klientów.  |
| [Wielowarstwowa aplikacja internetowa o wysokiej dostępności i odzyskiwaniu po awarii ](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)        |      Wdraża odporne na błędy aplikacje wielowarstwowe, które są odporne na wysoką dostępność i odzyskiwanie po awarii. Jeśli region podstawowy stanie się niedostępny, Traffic Manager do regionu pomocniczego.  |
|[IaaS: Aplikacja internetowa z relacyjną bazą danych](/azure/architecture/high-availability/ref-arch-iaas-web-and-db)    |   Opisuje sposób używania zasobów rozrzucanych w wielu strefach w celu zapewnienia architektury wysokiej dostępności do hostowania aplikacji internetowej infrastruktury jako usługi (IaaS) i SQL Server bazy danych.     |
|[Współużytkowanie lokalizacji w czasie rzeczywistym za pomocą niedrogich bezserwerowych usług platformy Azure](/azure/architecture/example-scenario/signalr/#azure-front-door)       |   Używa Azure Front Door, aby zapewnić wyższą dostępność aplikacji niż wdrażanie w jednym regionie. W przypadku wystąpienia regionalnej awarii, która będzie miała wpływ na region podstawowy, korzystając z usługi Front Door, możesz przejść w trybie failover do regionu pomocniczego.      |
|[Urządzenia wirtualne sieci o wysokiej dostępności](/azure/architecture/reference-architectures/dmz/nva-ha)     | Przedstawia sposób wdrażania zestawu wirtualnych urządzeń sieciowych (WUS) w celu wysokiej dostępności na platformie Azure.        |
|[Równoważenie obciążenia w wielu regionach przy Traffic Manager i Application Gateway](/azure/architecture/high-availability/reference-architecture-traffic-manager-application-gateway)     | Opisuje sposób wdrażania odpornych aplikacji wielowarstwowych w wielu regionach świadczenia usługi Azure w celu zapewnienia dostępności i niezawodnej infrastruktury odzyskiwania po awarii.        |

## <a name="secure-your-network-resources"></a>Zabezpieczanie zasobów sieciowych

W poniższej tabeli przedstawiono artykuły opisujące sposób ochrony zasobów sieciowych przy użyciu Azure Networking sieciowych.

|Tytuł |Opis  |
|---------|---------|
|[Najlepsze rozwiązania z zakresu zabezpieczeń sieci](../../security/fundamentals/network-best-practices.md) |Omówienie kolekcji najlepszych rozwiązań platformy Azure w celu zwiększenia bezpieczeństwa sieci.         |
[Przewodnik po architekturze usługi Azure Firewall](/azure/architecture/example-scenario/firewalls/) | Zapewnia ustrukturyzowane podejście do projektowania zapór o wysokiej dostępie na platformie Azure przy użyciu urządzeń wirtualnych innych firm.        |
|[Implementowanie bezpiecznej sieci hybrydowej](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)     | Opisuje architekturę, która implementuje strefy DMZ, nazywane również siecią obwodową, między siecią lokalną a siecią wirtualną platformy Azure. Cały ruch przychodzący i wychodzący przechodzi przez Azure Firewall.        |
|[Zabezpieczanie obciążeń i zarządzanie nimi przy użyciu segmentacji na poziomie sieci](/azure/architecture/reference-architectures/hybrid-networking/network-level-segmentation) | Opisuje trzy typowe wzorce używane do organizowania obciążeń na platformie Azure z perspektywy sieci.   Każdy z tych wzorców zapewnia inny typ izolacji i łączności.      |
|[Zapora i usługa Application Gateway dla sieci wirtualnych](/azure/architecture/example-scenario/gateway/firewall-application-gateway) | Opisuje usługi zabezpieczeń Virtual Network Azure, takie jak Azure Firewall i Azure Application Gateway, kiedy używać poszczególnych usług i opcje projektowania sieci, które łączą obie te opcje.      |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [o usłudze Azure Virtual Network.](../../virtual-network/virtual-networks-overview.md)
