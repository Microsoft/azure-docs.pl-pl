---
title: Dokumentacja architektury sieci platformy Azure
description: Zapoznaj się z dokumentacją architektury referencyjnej dostępną dla usług sieciowych platformy Azure.
services: networking
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 03/30/2021
ms.author: kumud
ms.openlocfilehash: 9b608312d66e6a3e7455c4577ea4644b33e4e82e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080396"
---
# <a name="azure-networking-architecture-documentation"></a>Dokumentacja architektury sieci platformy Azure

Ten artykuł zawiera informacje na temat przewodników architektury, które mogą ułatwić zapoznanie się z różnymi usługami sieciowymi na platformie Azure dostępnymi dla użytkownika w celu tworzenia aplikacji.

## <a name="networking-overview"></a>Omówienie sieci

Poniższa tabela zawiera artykuły, które zawierają omówienie sieci wirtualnych centrów danych i topologii gwiazdy na platformie Azure.

|Tytuł |Opis  |
|---------|---------|
|[Wirtualne centra danych](/azure/architecture/vdc/networking-virtual-datacenter)   | Oferuje perspektywę sieci wirtualnego centrum danych na platformie Azure.       |
|[Topologia piasty i szprych](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)  |Zawiera omówienie topologii sieci Hub i szprych na platformie Azure wraz z informacjami o limitach subskrypcji i wielu centrach.          |

## <a name="connect-to-azure-resources"></a>Nawiązywanie połączenia z zasobami platformy Azure

Poniższa tabela zawiera artykuły dotyczące usług sieciowych platformy Azure, które zapewniają łączność między zasobami platformy Azure, połączeniem z siecią lokalną i zasobami platformy Azure oraz z rozgałęzieniem do łączności z gałęzią na platformie Azure.

|Tytuł |Opis  |
|---------|---------|
|[Dodawanie przestrzeni adresów IP do sieci wirtualnych w komunikacji równorzędnej](/azure/architecture/networking/prefixes/add-ip-space-peered-vnet)     | Udostępnia skrypty, które ułatwiają dodawanie przestrzeni adresów IP do sieci wirtualnych w komunikacji równorzędnej.        |
|[Łączenie serwerów autonomicznych za pomocą karty sieciowej platformy Azure](/azure/architecture/hybrid/azure-network-adapter)   | Pokazuje, jak połączyć lokalny serwer autonomiczny w celu Microsoft Azure sieci wirtualnych przy użyciu karty sieciowej platformy Azure, która jest wdrażana za pośrednictwem Centrum administracyjnego systemu Windows.        |
|[Wybieranie między komunikacją równorzędną sieci wirtualnych i bramami sieci VPN](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering)   | Porównuje dwa sposoby łączenia sieci wirtualnych na platformie Azure: wirtualne sieci równorzędne i bramy sieci VPN.        |
|[Łączenie sieci lokalnej z platformą Azure](/azure/architecture/reference-architectures/hybrid-networking/)  | Porównuje Opcje łączenia sieci lokalnej z usługą Azure Virtual Network (VNet). Dla każdej opcji jest dostępna bardziej szczegółowa architektura referencyjna.        |
|[Architektura łączności między SIECIami wirtualnymi z usługą Azure Virtual WAN](../../virtual-wan/sd-wan-connectivity-architecture.md)|W tym artykule opisano różne opcje łączności dotyczące łączenia prywatnego oprogramowania sieci WAN (SD-WAN) z wirtualną siecią WAN platformy Azure.|

## <a name="deploy-highly-available-applications"></a>Wdrażanie aplikacji o wysokiej dostępności

Poniższa tabela zawiera artykuły opisujące sposób wdrażania aplikacji w celu zapewnienia wysokiej dostępności przy użyciu kombinacji usług sieciowych platformy Azure.

|Tytuł |Opis  |
|---------|---------|
|[Wieloregionowa aplikacja N-warstwowa](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server))  | Opisuje wieloregionową aplikację N-warstwową, która używa Traffic Manager do kierowania żądań przychodzących do regionu podstawowego, a jeśli ten region będzie niedostępny, Traffic Manager przechodzi w tryb failover do regionu pomocniczego.      |
| [Wielodostępna usługa SaaS na platformie Azure](https://docs.microsoft.com/azure/architecture/example-scenario/multi-saas/multitenant-saas)       |   Używa rozwiązania z wieloma dzierżawcami, które zawiera kombinację zewnętrznych drzwi i Application Gateway.  Drzwi z przodu pomagają zrównoważyć obciążenie ruchu między regionami i Application Gateway trasy i zrównoważyć ruch wewnętrznie w aplikacji do różnych usług, które spełniają potrzeby biznesowe klienta.  |
| [Wielowarstwowa aplikacja sieci Web skompilowana pod kątem wysokiej dostępności i odzyskiwania po awarii ](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)        |      Wdraża odporne aplikacje wielowarstwowe skompilowane pod kątem wysokiej dostępności i odzyskiwania po awarii. Jeśli region podstawowy stał się niedostępny, Traffic Manager przechodzi w tryb failover do regionu pomocniczego.  |
|[IaaS: aplikacja sieci Web z relacyjną bazą danych](/azure/architecture/high-availability/ref-arch-iaas-web-and-db)    |   Opisuje sposób używania zasobów rozproszonych w wielu strefach w celu zapewnienia wysokiej dostępności architektury do hostowania aplikacji sieci Web infrastruktury jako usługi (IaaS) i bazy danych SQL Server.     |
|[Współużytkowanie lokalizacji w czasie rzeczywistym za pomocą niedrogich bezserwerowych usług platformy Azure](/azure/architecture/example-scenario/signalr/#azure-front-door)       |   Używa platformy Azure Front drzwiczki w celu zapewnienia lepszej dostępności aplikacji niż wdrożenie w jednym regionie. W przypadku wystąpienia regionalnej awarii, która będzie miała wpływ na region podstawowy, korzystając z usługi Front Door, możesz przejść w trybie failover do regionu pomocniczego.      |
|[Urządzenia wirtualne sieci o wysokiej dostępności](/azure/architecture/reference-architectures/dmz/nva-ha)     | Pokazuje, jak wdrożyć zestaw wirtualnych urządzeń sieciowych (urządzeń WUS) w celu zapewnienia wysokiej dostępności na platformie Azure.        |

## <a name="secure-your-network-resources"></a>Zabezpieczanie zasobów sieciowych

Poniższa tabela zawiera artykuły opisujące, jak chronić zasoby sieciowe przy użyciu usług sieciowych platformy Azure.

|Tytuł |Opis  |
|---------|---------|
|[Najlepsze rozwiązania z zakresu zabezpieczeń sieci](../../security/fundamentals/network-best-practices.md) |Omawia zbiór najlepszych rozwiązań dotyczących platformy Azure w celu zwiększenia bezpieczeństwa sieci.         |
[Przewodnik po architekturze usługi Azure Firewall](/azure/architecture/example-scenario/firewalls/) | Zapewnia strukturalne podejście do projektowania zapór o wysokiej dostępności na platformie Azure przy użyciu urządzeń wirtualnych innych firm.        |
|[Implementowanie bezpiecznej sieci hybrydowej](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)     | Opisuje architekturę implementującą strefę DMZ, nazywaną również siecią obwodową, między siecią lokalną a siecią wirtualną platformy Azure. Cały ruch przychodzący i wychodzący przechodzi przez zaporę platformy Azure.        |
|[Zabezpieczanie obciążeń i zarządzanie nimi przy użyciu segmentacji na poziomie sieci](/azure/architecture/reference-architectures/hybrid-networking/network-level-segmentation) | Opisuje trzy typowe wzorce używane do organizowania obciążeń na platformie Azure z perspektywy sieci.   Każdy z tych wzorców zapewnia inny typ izolacji i łączności.      |
|[Zapora i usługa Application Gateway dla sieci wirtualnych](/azure/architecture/example-scenario/gateway/firewall-application-gateway) | Zawiera opis usług zabezpieczeń Azure Virtual Network, takich jak Zapora platformy Azure i Azure Application Gateway, kiedy należy używać poszczególnych usług i opcji projektowania sieci, które łączą oba te elementy.      |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [usłudze Azure Virtual Network](../../virtual-network/virtual-networks-overview.md).
