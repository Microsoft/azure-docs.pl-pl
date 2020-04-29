---
title: Co to są Strefy dostępności platformy Azure?
description: Aby utworzyć aplikacje o wysokiej dostępności i odporności na platformie Azure, Strefy dostępności zapewnić fizycznie oddzielne lokalizacje, za pomocą których można uruchamiać zasoby.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 10/17/2019
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: e7ba62a96a6fc76ad63960cfe57f5b1f85589c32
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085046"
---
# <a name="what-are-availability-zones-in-azure"></a>Co to są strefy dostępności na platformie Azure?
Strefy dostępności to oferta wysokiej dostępności, która chroni Twoje aplikacje i dane przed awariami centrów danych. Strefy dostępności to unikatowe fizyczne lokalizacje w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach. Fizyczna separacja stref dostępności w ramach regionu chroni aplikacje i dane przed awariami centrum danych. Usługi strefowo nadmiarowe replikujeją aplikacje i dane między Strefy dostępności, aby chronić je przed awariami jednego punktu. Dzięki strefom dostępności platforma Azure oferuje najlepszą w branży umowę dotycząca poziomu usług (SLA) gwarantującą czas działania na poziomie 99,99%. Pełna treść [umowy SLA dotyczącej usługi Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wyjaśnia w całości kwestię gwarantowanej dostępności platformy Azure.

Strefa dostępności w regionie świadczenia usługi Azure jest kombinacją domeny błędów i domeny aktualizacji. Jeśli na przykład utworzysz trzy lub więcej maszyn wirtualnych w trzech strefach w regionie świadczenia usługi Azure, maszyny wirtualne będą rozproszone w trzech domenach błędów i trzech domenach aktualizacji. Platforma Azure rozpoznaje to rozproszenie w domenach aktualizacji, aby upewnić się, że maszyny wirtualne w różnych strefach nie są aktualizowane w tym samym czasie.

Twórz wysoką dostępność w architekturze aplikacji dzięki umieszczeniu na niej zasobów obliczeniowych, magazynu, sieci i danych w ramach strefy i replikacji w innych strefach. Usługi platformy Azure, które obsługują strefy dostępności, dzielą się na dwie kategorie:

- **Usługi strefowe** — przypinasz zasób do określonej strefy (na przykład maszyny wirtualne, dyski zarządzane, standardowe adresy IP) lub
- **Usługi strefowo-nadmiarowe** — platforma automatycznie replikuje zasoby w strefach (na przykład magazyn strefowo-nadmiarowy, baza danych SQL).

Aby zapewnić kompleksową ciągłość biznesową na platformie Azure, skompiluj architekturę aplikacji przy użyciu kombinacji Strefy dostępności z parami regionów platformy Azure. Można synchronicznie replikować aplikacje i dane za pomocą Strefy dostępności w regionie świadczenia usługi Azure, aby zapewnić wysoką dostępność i asynchroniczną replikację w regionach platformy Azure w celu ochrony przed odzyskiwaniem po awarii.
 
![Widok koncepcyjny jednej strefy przechodzącej w regionie](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Identyfikatory stref dostępności (numery 1, 2 i 3 na zdjęciu powyżej) są logicznie mapowane na rzeczywiste strefy fizyczne dla każdej subskrypcji. Oznacza to, że dostępność Strefa 1 w danej subskrypcji może odwoływać się do innej strefy fizycznej niż Strefa 1 dostępności w innej subskrypcji. W związku z tym zaleca się, aby nie polegać na identyfikatorach strefy dostępności w różnych subskrypcjach na potrzeby umieszczania maszyn wirtualnych.

## <a name="services-support-by-region"></a>Obsługa usług według regionów

Kombinacje usług i regionów platformy Azure, które obsługują Strefy dostępności są następujące:


|                                 |Ameryki |              |           |           | Europa |              |          |              | Azja i Pacyfik |                 |                |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|-------------|
|          |Środkowe stany USA|Wschodnie stany USA|Wschodnie stany USA 2|Zachodnie stany USA 2|Francja Środkowa|Europa Północna|Południowe Zjednoczone Królestwo|Europa Zachodnia|Japonia Wschodnia|Azja Południowo-Wschodnia|Australia Wschodnia|
| **Wystąpienia obliczeniowe**                         |            |              |           |           |                |              |          |             |            |                |                |
| Maszyny wirtualne z systemem Linux          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Maszyny wirtualne z systemem Windows        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Zestawy skali maszyn wirtualnych      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| ILB środowiska Azure App Service | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Azure Kubernetes Service        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Storage**   |            |              |           |           |                |              |          |             |            |                |                |
| Dyski zarządzane                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Magazyn strefowo nadmiarowy          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Networking**                     |            |              |           |           |                |              |          |             |            |                |                |
| Standardowy adres IP        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| usługa Load Balancer w warstwie Standardowa     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| VPN Gateway            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Brama ExpressRoute   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Application Gateway (wersja 2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Azure Firewall           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| **Bazy danych**                     |            |              |           |           |                |              |          |             |            |                |                |
| Azure Data Explorer                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |                |
| Baza danych SQL                    | &#10003;   | &#10003;     | &#10003;  | &#10003; (wersja zapoznawcza) | &#10003;       | &#10003; (wersja zapoznawcza)     | &#10003; | &#10003;    | &#10003;       | &#10003;       |&#10003;        |
| Azure Cache for Redis           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| Azure Cosmos DB                    | &#10003;   |  &#10003;  |  &#10003; | &#10003; |       |     | &#10003; |  &#10003;   |            | &#10003;       |                |
| **Analiza**                       |            |              |           |           |                |              |          |             |            |                |                |
| Event Hubs                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |                |
| **Integracja**                     |            |              |           |           |                |              |          |             |            |                |                |
| Service Bus (tylko warstwa Premium) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| Event Grid | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| **Tożsamość**                     |            |              |           |           |                |              |          |             |            |                |                |
| Azure AD Domain Services | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |

## <a name="services-resiliency"></a>Odporność usług
Wszystkie usługi zarządzania systemu Azure są zaprojektowane tak, aby były odporne na awarie na poziomie regionu. W spektrum błędów co najmniej jedna awaria strefy dostępności w obrębie regionu ma mniejszą wartość promienia błędu w porównaniu z awarią całego regionu. Platforma Azure może wykonać odzyskiwanie z niepowodzeń usług zarządzania w obrębie regionu lub z innego regionu platformy Azure. Platforma Azure wykonuje krytyczną konserwację jedną strefę w danym momencie w regionie, aby zapobiec wszelkim awariom wpływającym na zasoby klienta wdrożone w ramach Strefy dostępności w regionie.

## <a name="pricing"></a>Cennik
Nie ma dodatkowych opłat za maszyny wirtualne wdrożone w strefie dostępności. Umowa SLA na 99,99% czasu maszyny wirtualnej jest oferowana w przypadku wdrożenia co najmniej dwóch maszyn wirtualnych w ramach dwóch lub więcej Strefy dostępności w regionie świadczenia usługi Azure. Będzie dostępnych dodatkowych opłat za transfer danych między MASZYNami wirtualnymi między strefami dostępności. Aby uzyskać więcej informacji, zapoznaj się ze stroną [cennika przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/) .


## <a name="get-started-with-availability-zones"></a>Wprowadzenie do Strefy dostępności
- [Tworzenie maszyny wirtualnej](../virtual-machines/windows/create-portal-availability-zone.md)
- [Dodawanie dysku zarządzanego przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Utwórz strefowo nadmiarowy zestaw skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Równoważenie obciążenia maszyn wirtualnych między strefami przy użyciu usługa Load Balancer w warstwie Standardowa ze strefowo nadmiarowy fronton](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Równoważenie obciążenia maszyn wirtualnych w strefie przy użyciu usługa Load Balancer w warstwie Standardowa z strefą frontonu](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Magazyn strefowo nadmiarowy](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Geograficzne odzyskiwanie po awarii w usłudze Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geograficzne odzyskiwanie po awarii w usłudze Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Tworzenie strefowo nadmiarowej bramy sieci wirtualnej](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Dodaj region nadmiarowy strefy dla Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Wprowadzenie usługi Azure cache for Redis Strefy dostępności](https://aka.ms/redis/az/getstarted)
- [Utwórz wystąpienie usługi Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Utwórz klaster usługi Azure Kubernetes Service (AKS), który używa Strefy dostępności](../aks/availability-zones.md)

## <a name="next-steps"></a>Następne kroki
- [Szablony szybkiego startu](https://aka.ms/azqs)
