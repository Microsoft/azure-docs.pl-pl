---
title: Co to są strefy dostępności platformy Azure?
description: Aby utworzyć aplikacje o wysokiej dostępności i odporne na platformie Azure, strefy dostępności zapewniają fizycznie oddzielne lokalizacje, których można używać do uruchamiania zasobów.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 10/17/2019
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: e7ba62a96a6fc76ad63960cfe57f5b1f85589c32
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085046"
---
# <a name="what-are-availability-zones-in-azure"></a>Co to są strefy dostępności na platformie Azure?
Strefy dostępności to oferta o wysokiej dostępności, która chroni aplikacje i dane przed awariami centrów danych. Strefy dostępności to unikatowe fizyczne lokalizacje w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach. Fizyczna separacja stref dostępności w ramach regionu chroni aplikacje i dane przed awariami centrum danych. Usługi nadmiarowe strefowe replikują aplikacje i dane w strefach dostępności, aby chronić je przed pojedynczymi punktami awarii. Dzięki strefom dostępności platforma Azure oferuje najlepszą w branży umowę dotycząca poziomu usług (SLA) gwarantującą czas działania na poziomie 99,99%. Pełna treść [umowy SLA dotyczącej usługi Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wyjaśnia w całości kwestię gwarantowanej dostępności platformy Azure.

Strefa dostępności w regionie świadczenia usługi Azure jest kombinacją domeny błędów i domeny aktualizacji. Jeśli na przykład utworzysz trzy lub więcej maszyn wirtualnych w trzech strefach w regionie świadczenia usługi Azure, maszyny wirtualne będą rozproszone w trzech domenach błędów i trzech domenach aktualizacji. Platforma Azure rozpoznaje to rozproszenie w domenach aktualizacji, aby upewnić się, że maszyny wirtualne w różnych strefach nie są aktualizowane w tym samym czasie.

Tworzenie wysokiej dostępności w architekturze aplikacji przez współlokowanie zasobów obliczeniowych, magazynowych, sieciowych i danych w strefie i replikowanie w innych strefach. Usługi platformy Azure, które obsługują strefy dostępności, dzielą się na dwie kategorie:

- **Usługi strefowe** — przypiąć zasób do określonej strefy (na przykład maszyny wirtualne, dyski zarządzane, standardowe adresy IP) lub
- **Usługi strefowo-nadmiarowe** — platforma automatycznie replikuje zasoby w strefach (na przykład magazyn strefowo-nadmiarowy, baza danych SQL).

Aby osiągnąć kompleksową ciągłość działania na platformie Azure, skompiluj architekturę aplikacji przy użyciu kombinacji stref dostępności z parami regionów platformy Azure. Synchronizację aplikacji i danych można synchronicznie replikować przy użyciu stref dostępności w regionie platformy Azure w celu zapewnienia wysokiej dostępności i asynchronicznie replikacji w regionach platformy Azure w celu ochrony przed odzyskiwaniem po awarii.
 
![widok koncepcyjny jednej strefy zejdzie w dół w regionie](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Identyfikatory strefy dostępności (numery 1, 2 i 3 na powyższym rysunku) są logicznie mapowane na rzeczywiste strefy fizyczne dla każdej subskrypcji niezależnie. Oznacza to, że strefa dostępności 1 w danej subskrypcji może odnosić się do innej strefy fizycznej niż Strefa dostępności 1 w innej subskrypcji. W związku z tym zaleca się, aby nie polegać na identyfikatory strefy dostępności w różnych subskrypcji dla umieszczania maszyn wirtualnych.

## <a name="services-support-by-region"></a>Wsparcie usług według regionów

Kombinacje usług platformy Azure i regionów, które obsługują strefy dostępności są:


|                                 |Ameryki |              |           |           | Europa |              |          |              | Azja i Pacyfik |                 |                |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|-------------|
|          |Środkowe stany USA|Wschodnie stany USA|Wschodnie stany USA 2|Zachodnie stany USA 2|Francja Środkowa|Europa Północna|Południowe Zjednoczone Królestwo|Europa Zachodnia|Japonia Wschodnia|Azja Południowo-Wschodnia|Australia Wschodnia|
| **Wystąpienia obliczeniowe**                         |            |              |           |           |                |              |          |             |            |                |                |
| Maszyny wirtualne z systemem Linux          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Maszyny wirtualne z systemem Windows        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Zestawy skali maszyn wirtualnych      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Środowisko iwład usługi azure app service | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Azure Kubernetes Service        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Storage**   |            |              |           |           |                |              |          |             |            |                |                |
| Dyski zarządzane                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Magazyn nadmiarowy strefowy          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Networking**                     |            |              |           |           |                |              |          |             |            |                |                |
| Standardowy adres IP        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Standardowy moduł równoważenia obciążenia     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| VPN Gateway            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Brama ExpressRoute   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Brama aplikacji(V2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Azure Firewall           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| **Bazy danych**                     |            |              |           |           |                |              |          |             |            |                |                |
| Azure Data Explorer                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |                |
| Baza danych SQL                    | &#10003;   | &#10003;     | &#10003;  | &#10003;(Wersja zapoznawcza) | &#10003;       | &#10003;(Wersja zapoznawcza)     | &#10003; | &#10003;    | &#10003;       | &#10003;       |&#10003;        |
| Azure Cache for Redis           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| Azure Cosmos DB                    | &#10003;   |  &#10003;  |  &#10003; | &#10003; |       |     | &#10003; |  &#10003;   |            | &#10003;       |                |
| **Analiza**                       |            |              |           |           |                |              |          |             |            |                |                |
| Event Hubs                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |                |
| **Integracja**                     |            |              |           |           |                |              |          |             |            |                |                |
| Usługa Service Bus (tylko warstwa Premium) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| Event Grid | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| **Tożsamość**                     |            |              |           |           |                |              |          |             |            |                |                |
| Azure AD Domain Services | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |

## <a name="services-resiliency"></a>Odporność usług
Wszystkie usługi zarządzania platformy Azure są architektoniczne, aby być odporne na awarie na poziomie regionu. W spektrum awarii co najmniej jeden błąd strefy dostępności w regionie ma mniejszy promień awarii w porównaniu do błędu całego regionu. Platforma Azure może odzyskać dane po awarii usług zarządzania na poziomie strefy w regionie lub z innego regionu platformy Azure. Platforma Azure wykonuje krytyczną konserwację po jednej strefie naraz w regionie, aby zapobiec wszelkim awariom wpływającym na zasoby klientów wdrożone w strefach dostępności w regionie.

## <a name="pricing"></a>Cennik
Nie ma żadnych dodatkowych kosztów dla maszyn wirtualnych wdrożonych w strefie dostępności. 99,99% umowy SLA dostępności maszyny wirtualnej jest oferowana, gdy dwa lub więcej maszyn wirtualnych są wdrażane w dwóch lub więcej stref dostępności w regionie platformy Azure. Będą dodatkowe opłaty za transfer danych maszyny wirtualnej do maszyny wirtualnej w strefie między dostępnością. Aby uzyskać więcej informacji, zapoznaj się ze stroną [Cennik przepustowości.](https://azure.microsoft.com/pricing/details/bandwidth/)


## <a name="get-started-with-availability-zones"></a>Wprowadzenie do stref dostępności
- [Tworzenie maszyny wirtualnej](../virtual-machines/windows/create-portal-availability-zone.md)
- [Dodawanie dysku zarządzanego przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Tworzenie strefy nadmiarowej maszyny wirtualnej zestaw skalowania](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Maszyny wirtualne równoważenia obciążenia w różnych strefach przy użyciu standardowego modułu równoważenia obciążenia z nadmiarowym frontendem strefowym](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Maszyny wirtualne równoważenia obciążenia w strefie przy użyciu standardowego modułu równoważenia obciążenia z strefowym frontonem](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Magazyn strefowo nadmiarowy](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Geograficzne odzyskiwanie po awarii w usłudze Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geograficzne odzyskiwanie po awarii w usłudze Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Tworzenie strefowo nadmiarowej bramy sieci wirtualnej](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Dodawanie regionu nadmiarowego strefy dla usługi Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Wprowadzenie do pamięci podręcznej platformy Azure dla stref dostępności Redis](https://aka.ms/redis/az/getstarted)
- [Utwórz wystąpienie usługi Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Tworzenie klastra usługi Azure Kubernetes (AKS), który używa stref dostępności](../aks/availability-zones.md)

## <a name="next-steps"></a>Następne kroki
- [Szablony szybkiego startu](https://aka.ms/azqs)
