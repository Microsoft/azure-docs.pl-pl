---
title: High availability in Azure Cosmos DB (Wysoka dostępność w usłudze Azure Cosmos DB)
description: W tym artykule opisano, jak usługa Azure Cosmos DB zapewnia wysoką dostępność
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 0a67360ab840b7897c85a382dfc3965bd27f6a3e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81688056"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Wysoka dostępność w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB w sposób przejrzysty replikuje dane we wszystkich regionach platformy Azure skojarzonych z kontem usługi Cosmos. Usługa Cosmos DB wykorzystuje wiele warstw nadmiarowości danych, jak pokazano na poniższej ilustracji:

![Partycjonowanie fizyczne](./media/high-availability/cosmosdb-data-redundancy.png)

- Dane w kontenerach usługi Cosmos są [podzielone poziomo na partycje](partitioning-overview.md).

- W każdym regionie każda partycja jest chroniona przez zestaw replik z wszystkich zapisów replikowane i trwale zatwierdzone przez większość replik. Repliki są dystrybuowane w aż 10-20 domen błędów.

- Każda partycja we wszystkich regionach jest replikowana. Każdy region zawiera wszystkie partycje danych kontenera usługi Cosmos i może akceptować zapisy i służyć odczyty.  

Jeśli twoje konto usługi Cosmos jest dystrybuowane w regionach platformy *Azure N,* będzie co najmniej *N* x 4 kopie wszystkich danych. Oprócz zapewnienia dostępu do danych o małym opóźnieniu i skalowania przepływności zapisu/odczytu w regionach skojarzonych z kontem usługi Cosmos, posiadanie większej liczby regionów (wyższe *N)* dodatkowo zwiększa dostępność.  

## <a name="slas-for-availability"></a>La dla dostępności

Jako globalnie rozproszona baza danych usługa Cosmos DB udostępnia kompleksowe łata, które obejmują przepływność, opóźnienie w 99 percentylu, spójność i wysoką dostępność. W poniższej tabeli przedstawiono gwarancje wysokiej dostępności zapewniane przez usługi Cosmos DB dla kont jedno- i wieloregionowych. Aby uzyskać wysoką dostępność, zawsze skonfiguruj konta usługi Cosmos, aby miały wiele regionów zapisu.

|Typ operacji  | Pojedynczy region |Wieloregion (zapisy w jednym regionie)|Wieloregion (zapisy wieloregionowe) |
|---------|---------|---------|-------|
|Zapisuje    | 99,99    |99,99   |99,999|
|Odczytuje     | 99,99    |99,999  |99,999|

> [!NOTE]
> W praktyce rzeczywista dostępność zapisu dla ograniczonego nieaktualności, sesji, spójnego prefiksu i modeli spójności ostatecznej jest znacznie wyższa niż opublikowane ławy SLA. Rzeczywista dostępność odczytu dla wszystkich poziomów spójności jest znacznie wyższa niż opublikowane ławy SLA.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Zapewnianie wysokiej dostępności za pomocą usługi Cosmos DB w przypadku awarii regionalnych

Awarie regionalne nie są niczym niezwykłym, a usługa Azure Cosmos DB zapewnia, że baza danych jest zawsze wysoko dostępna. Następujące szczegóły przechwytują zachowanie usługi Cosmos DB podczas awarii, w zależności od konfiguracji konta usługi Cosmos:

- W usłudze Cosmos DB, zanim operacja zapisu zostanie potwierdzona do klienta, dane są trwale zatwierdzane przez kworum replik w regionie, który akceptuje operacje zapisu.

- Konta wieloregionowe skonfigurowane z wieloma regionami zapisu będą bardzo dostępne zarówno dla zapisów, jak i odczytów. Regionalne trybu failover są natychmiastowe i nie wymagają żadnych zmian z aplikacji.

- Konta jednoregionalne mogą utracić dostępność po regionalnej awarii. Zawsze zaleca się skonfigurowanie **co najmniej dwóch regionów** (najlepiej co najmniej dwóch regionów zapisu) za pomocą konta usługi Cosmos, aby zapewnić wysoką dostępność przez cały czas.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Konta wieloregionowe z regionem pojedynczego zapisu (awaria regionu zapisu)

- Podczas awarii regionu zapisu konto usługi Cosmos automatycznie podwyższy region pomocniczy jako nowy podstawowy region zapisu po **skonfigurowaniu automatycznego trybu failover** na koncie usługi Azure Cosmos. Po włączeniu pracy awaryjnej nastąpi do innego regionu w kolejności priorytetu regionu, który został określony.
- Gdy poprzednio dotknięty region jest z powrotem w trybie online, wszystkie dane zapisu, które nie zostały zreplikowane, gdy region nie powiódł się, są udostępniane za pośrednictwem [źródła konfliktów](how-to-manage-conflicts.md#read-from-conflict-feed). Aplikacje mogą odczytywać źródła danych konfliktów, rozwiązywać konflikty na podstawie logiki specyficznej dla aplikacji i zapisywać zaktualizowane dane z powrotem do kontenera usługi Azure Cosmos odpowiednio.
- Po odzyskaniu wcześniej dotkniętego regionu zapisu staje się on automatycznie dostępny jako region odczytu. Można przełączyć się z powrotem do odzyskanego regionu jako region zapisu. Regiony można przełączać za pomocą programu [PowerShell, interfejsu wiersza polecenia platformy Azure lub portalu Azure.](how-to-manage-database-account.md#manual-failover) Nie **ma żadnych danych lub utraty dostępności** przed, w trakcie lub po przełączeniu regionu zapisu i aplikacja nadal jest wysoce dostępna.

> [!IMPORTANT]
> Zdecydowanie zaleca się skonfigurowanie kont usługi Azure Cosmos używanych dla obciążeń produkcyjnych w celu **włączenia automatycznego trybu failover.** Ręczne tryb failover wymaga łączności między pomocniczym i podstawowym regionem zapisu, aby zakończyć sprawdzanie spójności, aby upewnić się, że nie ma utraty danych podczas pracy awaryjnej. Jeśli region podstawowy jest niedostępny, to sprawdzanie spójności nie może zakończyć się i ręcznezamykanie awaryjne nie zakończy się pomyślnie, co powoduje utratę dostępności zapisu.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Konta wieloregionowe z regionem pojedynczego zapisu (awaria regionu odczytu)

- Podczas awarii regionu odczytu kont usługi Cosmos przy użyciu dowolnego poziomu spójności lub silnej spójności z trzech lub więcej regionów odczytu pozostanie wysoce dostępne dla odczytów i zapisów.
- Region, do jakiego oddziaływuje, zostanie automatycznie rozłączony i zostanie oznaczony w trybie offline. ZestawY [SDK usługi Azure Cosmos DB](sql-api-sdk-dotnet.md) przekierują wywołania odczytu do następnego dostępnego regionu na liście preferowanego regionu.
- Jeśli żaden z regionów na liście preferowanych regionów nie jest dostępny, wywołania automatycznie wracają do bieżącego regionu zapisu.
- Żadne zmiany nie są wymagane w kodzie aplikacji do obsługi awarii regionu odczytu. Gdy region odczytu, na który ma wpływ, zostanie ponownie w trybie online, zostanie automatycznie zsynchronizowany z bieżącym regionem zapisu i będzie ponownie dostępny do obsługi żądań odczytu.
- Dalsze operacje odczytu są przekierowywane do odzyskanego regionu bez konieczności wprowadzania jakichkolwiek zmian w kodzie aplikacji. Podczas pracy awaryjnej i ponownego dołączania do regionu wcześniej nie powiodło się, gwarancje spójności odczytu nadal są honorowane przez usługi Cosmos DB.

> [!IMPORTANT]
> Konta usługi Azure Cosmos przy użyciu silnej spójności z dwóch lub mniej regionów odczytu utraci dostępność zapisu podczas awarii regionu odczytu, ale zachowa dostępność odczytu dla pozostałych regionów.

- Nawet w rzadkich i niefortunnych zdarzeń, gdy region platformy Azure jest trwale nieodwracalne, nie ma utraty danych, jeśli konto usługi Cosmos wielu regionu jest skonfigurowany z *silną* spójność. W przypadku trwale nieodwracalnego regionu zapisu, wieloregionowego konta usługi Cosmos skonfigurowanego z spójnością ograniczonego nieaktualności, okno potencjalnej utraty danych jest ograniczone do okna nieaktualność *(K* lub *T),* gdzie K = 100 000 aktualizacji i T = 5 minut. W przypadku poziomów sesji, spójnego prefiksu i spójności ostatecznej okno potencjalnej utraty danych jest ograniczone do maksymalnie 15 minut. Aby uzyskać więcej informacji na temat obiektów obiektów obiektów RTO i obiektów celnych celnych dla usługi Azure Cosmos DB, zobacz [Poziomy spójności i trwałość danych](consistency-levels-tradeoffs.md#rto)

## <a name="availability-zone-support"></a>Obsługa strefy dostępności

Oprócz odporności między regionami można teraz włączyć **nadmiarowość stref** podczas wybierania regionu do skojarzenia z bazą danych usługi Azure Cosmos.

Dzięki obsłudze strefy dostępności usługa Azure Cosmos DB zapewni, że repliki są umieszczane w wielu strefach w danym regionie, aby zapewnić wysoką dostępność i odporność podczas awarii strefowych. W tej konfiguracji nie ma żadnych zmian w opóźnieniu i innych ław sla. W przypadku awarii pojedynczej strefy nadmiarowość strefy zapewnia pełną trwałość danych z RPO = 0 i dostępności z RTO = 0.

Nadmiarowość stref jest *dodatkową możliwością* funkcji [replikacji wielowzorcowej.](how-to-multi-master.md) Nie można polegać na samej redundancji strefowej w celu osiągnięcia odporności regionalnej. Na przykład w przypadku regionalnych awarii lub dostępu o małym opóźnieniu w regionach zaleca się, aby oprócz nadmiarowości strefy było wiele regionów zapisu.

Podczas konfigurowania zapisów wieloregionowych dla konta usługi Azure Cosmos, można zdecydować się na nadmiarowość strefy bez dodatkowych kosztów. W przeciwnym razie zapoznaj się z poniższą uwagą dotyczącą cen obsługi nadmiarowości strefy. Nadmiarowość strefy można włączyć w istniejącym regionie konta usługi Azure Cosmos, usuwając region i dodając go z powrotem z włączoną nadmiarowością strefy.

Ta funkcja jest dostępna w następujących regionach platformy Azure:

- Południowe Zjednoczone Królestwo

- Azja Południowo-Wschodnia

- Wschodnie stany USA

- Wschodnie stany USA 2

- Środkowe stany USA

- Europa Zachodnia

- Zachodnie stany USA 2

- Australia Wschodnia

> [!NOTE]
> Włączenie stref dostępności dla pojedynczego regionu konta usługi Azure Cosmos spowoduje naliczenie opłat, które są równoważne dodaniu dodatkowego regionu do konta. Aby uzyskać szczegółowe informacje na temat cen, zobacz [stronę cennika](https://azure.microsoft.com/pricing/details/cosmos-db/) i [koszt wielu regionów w artykułach usługi Azure Cosmos DB.](optimize-cost-regions.md)

W poniższej tabeli podsumowano możliwości wysokiej dostępności różnych konfiguracji kont:

|Wskaźnik KPI  |Pojedynczy region bez stref dostępności (inne niż AZ)  |Pojedynczy region ze strefami dostępności (AZ)  |Zapisy w wielu regionach ze strefami dostępności (AZ, 2 regiony) — najbardziej zalecane ustawienie |
|---------|---------|---------|---------|
|SLA dostępności zapisu | 99,99% | 99,99% | 99.999% |
|Przeczytaj SLA dostępności  | 99,99% | 99,99% | 99.999% |
|Price | Stawka rozliczeń w jednym regionie | Stawka rozliczeniowa strefy dostępności w jednym regionie | Stawka rozliczeniowa dla wielu regionów |
|Awarie stref – utrata danych | Utrata danych | Brak utraty danych | Brak utraty danych |
|Awarie stref – dostępność | Utrata dostępności | Brak utraty dostępności | Brak utraty dostępności |
|Opóźnienie odczytu | Region krzyżowy | Region krzyżowy | Małe |
|Opóźnienie zapisu | Region krzyżowy | Region krzyżowy | Małe |
|Awaria regionalna – utrata danych | Utrata danych |  Utrata danych | Utrata danych <br/><br/> W przypadku korzystania z ograniczonej spójności nieaktualności z wieloma wzorcami i więcej niż jednym regionem, utrata danych jest ograniczona do ograniczonego nieaktualnego nieaktualnego skonfigurowania na koncie <br /><br />Można uniknąć utraty danych podczas regionalnej awarii, konfigurując silną spójność z wieloma regionami. Ta opcja zawiera kompromisy, które wpływają na dostępność i wydajność. Można go skonfigurować tylko na kontach, które są skonfigurowane dla zapisów jednoregionowych. |
|Awaria regionalna – dostępność | Utrata dostępności | Utrata dostępności | Brak utraty dostępności |
|Przepływność | X Przepływność aprowizowana ru/s | X Przepływność aprowizowana ru/s | 2X Aprowizowana przepustowość ru/s <br/><br/> Ten tryb konfiguracji wymaga dwukrotnie większej przepływności w porównaniu do jednego regionu ze strefami dostępności, ponieważ istnieją dwa regiony. |

> [!NOTE]
> Aby włączyć obsługę strefy dostępności dla wieloregionowego konta usługi Azure Cosmos, konto musi mieć włączone zapisy z wieloma wzorcami.

Nadmiarowość strefy można włączyć podczas dodawania regionu do nowych lub istniejących kont usługi Azure Cosmos. Aby włączyć nadmiarowość strefy na koncie usługi `isZoneRedundant` Azure `true` Cosmos, należy ustawić flagę dla określonej lokalizacji. Tę flagę można ustawić we właściwości locations. Na przykład następujący fragment kodu programu PowerShell umożliwia nadmiarowość stref dla regionu "Azja Południowo-Wschodnia":

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1; "isZoneRedundant"= "true" }
)
```

Następujące polecenie pokazuje, jak włączyć nadmiarowość stref dla regionów "EastUS" i "WestUS2":

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

Strefy dostępności można włączyć przy użyciu witryny Azure portal podczas tworzenia konta usługi Azure Cosmos. Podczas tworzenia konta należy włączyć opcję **Nadmiarowość geograficzna**, **Zapisy wieloregionowe**i wybrać region, w którym obsługiwane są strefy dostępności:

![Włączanie stref dostępności przy użyciu witryny Azure portal](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Tworzenie aplikacji o wysokiej dostępności

- Aby zapewnić wysoką dostępność zapisu i odczytu, skonfiguruj konto usługi Cosmos tak, aby obejmowało co najmniej dwa regiony z regionami wielokrotnego zapisu. Ta konfiguracja zapewni najwyższą dostępność, najniższe opóźnienie i najlepszą skalowalność zarówno dla odczytów, jak i zapisów wspieranych przez ławy SLA. Aby dowiedzieć się więcej, zobacz, jak [skonfigurować konto usługi Cosmos w wielu regionach zapisu](tutorial-global-distribution-sql-api.md).

- W przypadku wieloregionalnych kont usługi Cosmos skonfigurowanych z regionem pojedynczego zapisu [należy włączyć automatyczne tryb failover przy użyciu interfejsu wiersza polecenia platformy Azure lub portalu Azure.](how-to-manage-database-account.md#automatic-failover) Po włączeniu automatycznego trybu failover, gdy wystąpi regionalna awaria, usługa Cosmos DB automatycznie przejdzie w tryb failover konta.  

- Nawet jeśli twoje konto usługi Azure Cosmos jest wysoce dostępne, aplikacja może nie być poprawnie zaprojektowana tak, aby była dostępna. Aby przetestować end-to-end wysokiej dostępności aplikacji, w ramach testowania aplikacji lub odzyskiwania po awarii (DR) wiertła, tymczasowo wyłączyć automatyczne pracy awaryjnej dla konta, wywołać [ręczne pracy awaryjnej przy użyciu programu PowerShell, interfejsu wiersza polecenia polecenia platformy Azure lub witryny Azure portal,](how-to-manage-database-account.md#manual-failover)a następnie monitorować pracy awaryjnej aplikacji. Po zakończeniu można ponownie przejść w trybie failover do regionu podstawowego i przywrócić automatyczne tryb failover dla konta.

- W środowisku globalnie rozproszonej bazy danych istnieje bezpośrednia relacja między poziomem spójności a trwałością danych w obecności awarii w całym regionie. Podczas opracowywania planu ciągłości działania należy zrozumieć maksymalny dopuszczalny czas, zanim aplikacja zostanie w pełni odzyskana po przełomowym zdarzeniu. Czas wymagany dla aplikacji, aby w pełni odzyskać jest znany jako cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalny okres ostatnich aktualizacji danych aplikacja może tolerować utraty podczas odzyskiwania po zdarzenie zakłócające. Okres aktualizacji, którego utrata może być tolerowana, jest określany jako cel punktu odzyskiwania (RPO, recovery point objective). Aby wyświetlić obiekt RPO i obiekt RTO dla usługi Azure Cosmos DB, zobacz [Poziomy spójności i trwałość danych](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Następne kroki

Następnie możesz przeczytać następujące artykuły:

- [Kompromisy w zakresie dostępności i wydajności dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
- [Globalne skalowanie aprowizowanej przepływności](scaling-throughput.md)
- [Dystrybucja globalna — szczegóły działania](global-dist-under-the-hood.md)
- [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md)
- [Jak skonfigurować konto usługi Cosmos w wielu regionach zapisu](how-to-multi-master.md)
