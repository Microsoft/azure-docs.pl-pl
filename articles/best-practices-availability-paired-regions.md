---
title: Zapewnianie ciągłości działania & odzyskiwania po awarii przy użyciu sparowanych regionów platformy Azure
description: Zapewnianie odporności aplikacji przy użyciu funkcji kojarzenia regionalnego platformy Azure
author: martinekuan
manager: martinekuan
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: martinek
ms.custom: references_regions
ms.openlocfilehash: 3310d4a7d86db9dee7d5f71fc9410545817886f3
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511233"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Ciągłość działania i odzyskiwanie po awarii — BCDR: Regiony sparowane platformy Azure

## <a name="what-are-paired-regions"></a>Jakie są sparowane regiony?

Region świadczenia usługi Azure składa się z zestawu centrów danych wdrożonych w ramach określonego czasu oczekiwania i połączonego za pomocą dedykowanej sieci o małym opóźnieniu.  Dzięki temu usługi platformy Azure w ramach regionu platformy Azure zapewniają najlepszą możliwą wydajność i bezpieczeństwo.  

Lokalizacja geograficzna platformy Azure definiuje obszar świata zawierający co najmniej jeden region świadczenia usługi Azure. Lokalizacje geograficzne definiują odrębny rynek, zwykle zawierający co najmniej dwa regiony, które zachowują miejsca zamieszkania danych i zgodności.  Więcej informacji o globalnej infrastrukturze platformy Azure znajduje się [tutaj](https://azure.microsoft.com/global-infrastructure/regions/)

Para regionalna składa się z dwóch regionów w tej samej lokalizacji geograficznej. Platforma Azure serializacji aktualizacje platformy (planowana konserwacja) między parami regionalnymi, co zapewnia, że tylko jeden region w każdej parze aktualizacji w danym momencie. Jeśli awaria dotyczy wielu regionów, co najmniej jeden region w każdej parze będzie miał priorytet na potrzeby odzyskiwania.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Niektóre usługi platformy Azure w większym stopniu korzystają z sparowanych regionów w celu zapewnienia ciągłości działania i ochrony przed utratą danych.  Platforma Azure udostępnia kilka [rozwiązań do magazynowania](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) , które korzystają z sparowanych regionów w celu zapewnienia dostępności danych. Na przykład [Magazyn Geograficznie nadmiarowy platformy Azure](./storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) replikuje dane do regionu pomocniczego automatycznie, dzięki czemu dane są trwałe nawet w przypadku, gdy region podstawowy nie jest możliwy do odzyskania. 

Należy pamiętać, że nie wszystkie usługi platformy Azure automatycznie replikujeją dane, ani nie wszystkie usługi platformy Azure są automatycznie wycofywane z nieuszkodzonego regionu do jego pary.  W takich przypadkach odzyskiwanie i replikacja muszą zostać skonfigurowane przez klienta.

## <a name="can-i-select-my-regional-pairs"></a>Czy mogę wybrać moje pary regionalne?

Nie. Niektóre usługi platformy Azure są zależne od lokalnych par, takich jak [Magazyn nadmiarowy](./storage/common/storage-redundancy.md)platformy Azure. Te usługi nie umożliwiają tworzenia nowych par regionalnych.  Podobnie, ponieważ platforma Azure kontroluje planowaną konserwację i priorytetyzację odzyskiwania dla par regionalnych, nie można definiować własnych par regionalnych, aby korzystać z tych usług. Można jednak utworzyć własne rozwiązanie do odzyskiwania po awarii, tworząc usługi w dowolnej liczbie regionów i korzystając z usług platformy Azure, aby je sparować. 

Na przykład możesz użyć usług platformy Azure, takich jak [AzCopy](./storage/common/storage-use-azcopy-v10.md) , aby zaplanować tworzenie kopii zapasowych danych na koncie magazynu w innym regionie.  Korzystając z [Azure DNS i Traffic Manager platformy Azure](./networking/disaster-recovery-dns-traffic-manager.md), klienci mogą zaprojektować odporną architekturę dla swoich aplikacji, które przestają utratę regionu podstawowego.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>Czy są ograniczone do korzystania z usług w ramach moich par regionalnych?

Nie. Chociaż dana usługa platformy Azure może polegać na parze regionalnym, można hostować inne usługi w dowolnym regionie, który spełnia Twoje wymagania biznesowe.  Rozwiązanie Azure GRS Storage może sparować dane w Kanadzie centralnych z elementem równorzędnym w Kanadzie i korzystać z zasobów obliczeniowych znajdujących się w regionie Wschodnie stany USA.  

## <a name="must-i-use-azure-regional-pairs"></a>Czy muszę używać par regionalnych platformy Azure?

Nie. Klienci mogą korzystać z usług platformy Azure, aby zaprojektować odporną usługę bez polegania na lokalnych parach platformy Azure.  Firma Microsoft zaleca jednak skonfigurowanie odzyskiwania po awarii (BCDR) ciągłości biznesowej w celu uzyskania korzyści z [izolacji](./security/fundamentals/isolation-choices.md) i zwiększenia [dostępności](./availability-zones/az-overview.md). W przypadku aplikacji obsługujących wiele aktywnych regionów zalecamy używanie obu regionów z pary regionalnej, gdy jest to możliwe. Zapewnia to optymalną dostępność aplikacji i zminimalizowany czas odzyskiwania w przypadku awarii. Zawsze, gdy to możliwe, Zaprojektuj aplikację pod kątem [maksymalnej odporności](/azure/architecture/framework/resiliency/overview) i łatwości [odzyskiwania po awarii](/azure/architecture/framework/resiliency/backup-and-recovery).

## <a name="azure-regional-pairs"></a>Pary regionalne platformy Azure

| Lokalizacja geograficzna | Para regionalna A | Para regionalna B  |
|:--- |:--- |:--- |
| Asia-Pacific |Azja Wschodnia (Hongkong SAR) | Azja Południowo-Wschodnia (Singapur) |
| Australia |Australia Wschodnia |Australia Południowo-Wschodnia |
| Australia |Australia Środkowa |Australia Środkowa 2 |
| Brazylia |Brazil South |South Central US |
| Kanada |Kanada Środkowa |Kanada Wschodnia |
| Chiny |Chiny Północne |Chiny Wschodnie|
| Chiny |Chiny Północne 2 |Chiny Wschodnie 2|
| Europa |Europa Północna (Irlandia) |Europa Zachodnia (Holandia) |
| Francja |Francja Środkowa|Francja Południowa|
| Niemcy |Niemcy Środkowe |Niemcy Północno-Wschodnie |
| Indie |Indie Środkowe |Indie Południowe |
| Indie |Indie Zachodnie |Indie Południowe |
| Japonia |Japonia Wschodnia |Japonia Zachodnia |
| Korea |Korea Środkowa |Korea Południowa |
| Ameryka Północna |East US |Zachodnie stany USA |
| Ameryka Północna |Wschodnie stany USA 2 |Central US |
| Ameryka Północna |Północno-środkowe stany USA |South Central US |
| Ameryka Północna |Zachodnie stany USA 2 |Zachodnio-środkowe stany USA |
| Norwegia | Norwegia Wschodnia | Norwegia Zachodnia |
| Republika Południowej Afryki | Północna Republika Południowej Afryki |Zachodnia Republika Południowej Afryki |
| Szwajcaria | Szwajcaria Północna |Szwajcaria Zachodnia |
| Zjednoczone Królestwo |Zachodnie Zjednoczone Królestwo |Południowe Zjednoczone Królestwo |
| Zjednoczone Emiraty Arabskie | Północne Zjednoczone Emiraty Arabskie | Środkowy Zjednoczone Emiraty Arabskie
| US Department of Defense |US DoD (region wschodni) |US DoD (region środkowy) |
| US Government |US Gov Arizona |US Gov Teksas |
| US Government |US Gov Iowa |US Gov Wirginia |
| US Government |US Gov Wirginia |US Gov Teksas |

> [!Important]
> - Indie Zachodnie są sparowane tylko w jednym kierunku. Region pomocniczy Indie Zachodnie to Indie Południowe, ale region pomocniczy w Republice Południowej Indie to środkowe Indie.
> - Brazylia Południowa jest unikatowa, ponieważ jest sparowany z regionem spoza jego lokalizacji geograficznej. Region w regionie Brazylia Południowa jest Południowo-środkowe stany USA. Region w regionie Południowo-środkowe stany USA nie jest Brazylia Południowa.


## <a name="an-example-of-paired-regions"></a>Przykład sparowanych regionów
Poniższy obraz ilustruje hipotetyczną aplikację, która używa pary regionalnej do odzyskiwania po awarii. Zielone numery zawierają działania obejmujące wiele regionów z trzech usług platformy Azure (Azure COMPUTE, Storage i Database) oraz sposób ich konfiguracji w celu replikowania między regionami. Unikatowe korzyści wynikające z wdrożenia w różnych regionach są wyróżniane przez pomarańczowe liczby.

![Omówienie zalet par regionów](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Rysunek 2 — hipotetyczna para regionalna platformy Azure

## <a name="cross-region-activities"></a>Działania obejmujące wiele regionów
Jak określono na rysunku 2.

1. **Azure COMPUTE (IaaS)** — w celu zapewnienia dostępności zasobów w innym regionie w trakcie awarii należy zainicjować obsługę administracyjną dodatkowych zasobów obliczeniowych. Aby uzyskać więcej informacji, zobacz [Wskazówki techniczne dotyczące odporności na platformie Azure](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md). 

2. **Azure Storage** — Jeśli używasz dysków zarządzanych, Dowiedz się więcej o [kopiach zapasowych między regionami](/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) i Azure Backup i [replikowanie maszyn wirtualnych](./site-recovery/azure-to-azure-tutorial-enable-replication.md) z jednego regionu do innego przy użyciu Azure Site Recovery. Jeśli używasz kont magazynu, to magazyn Geograficznie nadmiarowy (GRS) jest konfigurowany domyślnie podczas tworzenia konta usługi Azure Storage. W przypadku GRS dane są automatycznie replikowane trzy razy w regionie podstawowym, a trzy razy w sparowanym regionie. Aby uzyskać więcej informacji, zobacz [Opcje nadmiarowości usługi Azure Storage](storage/common/storage-redundancy.md).

3. **Azure SQL Database** — z Azure SQL Database replikacją geograficzną można skonfigurować asynchroniczne replikację transakcji do dowolnego regionu na świecie; Zalecamy jednak wdrożenie tych zasobów w sparowanym regionie w przypadku większości scenariuszy odzyskiwania po awarii. Aby uzyskać więcej informacji, zobacz [replikację geograficzną w Azure SQL Database](./azure-sql/database/auto-failover-group-overview.md).

4. **Azure Resource Manager** — usługa Resource Manager z założenia zapewnia logiczną izolację składników w różnych regionach. Oznacza to, że logiczne błędy w jednym regionie są mniej podobne do innych.

## <a name="benefits-of-paired-regions"></a>Zalety par regionów

5. **Izolacja fizyczna** — Jeśli to możliwe, platforma Azure 300 preferuje rozdzielenie między centrami danych w parze regionalnym, chociaż nie jest to praktyczne ani możliwe w przypadku wszystkich lokalizacje geograficzne. Fizyczne rozdzielenie centrów danych zmniejsza prawdopodobieństwo wystąpienia klęsk żywiołowych, awarii cywilnej, przerwy w zasilaniu lub sieci fizycznej jednocześnie mających wpływ na oba regiony jednocześnie. Izolacja podlega ograniczeniom w lokalizacji geograficznej (rozmiar geografii, dostępność infrastruktury mocy/sieci, regulacje itp.).  

6. **Replikacja udostępniona przez platformę** — niektóre usługi, takie jak Geo-Redundant Storage, zapewniają automatyczną replikację do sparowanego regionu.

7. **Kolejność odzyskiwania regionów** — w przypadku rozległej awarii odzyskiwanie jednego regionu jest ustalane z uwzględnieniem każdej pary. Aplikacje wdrożone w ramach sparowanych regionów mają gwarancję, że jeden z regionów zostanie odzyskany priorytetowo. Jeśli aplikacja jest wdrożona w różnych regionach, które nie są sparowane, odzyskiwanie może zostać opóźnione — w najgorszym przypadku wybrane regiony mogą być ostatnimi do odzyskania.

8. **Aktualizacje sekwencyjne** — planowane aktualizacje systemu platformy Azure są zestawiane w sparowanych regionach sekwencyjnie (nie w tym samym czasie), aby zminimalizować przestoje, wpływ błędów i błędy logiczne w rzadkim zdarzeniu nieprawidłowej aktualizacji.

9. Miejsce **zamieszkania** — region znajduje się w tej samej lokalizacji geograficznej, w której znajduje się para (z wyjątkiem Brazylii Południowej) w celu spełnienia wymagań dotyczących miejsca zamieszkania w celach podatkowych i prawnych.