---
title: High availability in Azure Cosmos DB (Wysoka dostępność w usłudze Azure Cosmos DB)
description: W tym artykule opisano, jak Azure Cosmos DB zapewnia wysoką dostępność
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 9667d82551e169dcc4a4bfd3ac79b15390f58aa0
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82609225"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Wysoka dostępność w usłudze Azure Cosmos DB

Azure Cosmos DB w sposób przezroczysty replikuje dane do wszystkich regionów świadczenia usługi Azure skojarzonych z Twoim kontem Cosmos. Cosmos DB wykorzystuje wiele warstw nadmiarowości danych, jak pokazano na poniższej ilustracji:

![Partycjonowanie fizyczne](./media/high-availability/cosmosdb-data-redundancy.png)

- Dane w kontenerach Cosmos są [podzielone na partycje w poziomie](partitioning-overview.md).

- W każdym regionie każda partycja jest chroniona przez zestaw replik, a wszystkie zapisy są replikowane i trwale zatwierdzone przez większość replik. Repliki są dystrybuowane w wielu domenach błędów 10-20.

- Każda partycja we wszystkich regionach jest replikowana. Każdy region zawiera wszystkie partycje danych kontenera Cosmos i może akceptować zapisy i obsłużyć odczyty.  

Jeśli Twoje konto Cosmos jest dystrybuowane w *regionach platformy Azure* , będą dostępne co najmniej *N* x 4 kopie wszystkich danych. Oprócz zapewniania dostępu do danych o małym opóźnieniu i skalowanie przepływności zapisu/odczytu w regionach skojarzonych z kontem Cosmos, mając więcej regionów (wyższy *N*) jeszcze bardziej zwiększa dostępność.  

## <a name="slas-for-availability"></a>Umowy SLA do dostępności

Jako globalnie dystrybuowana baza danych Cosmos DB zapewnia kompleksową umowy SLA, która obejmuje przepływność, opóźnienie w 99 percentylu, spójności i wysokiej dostępności. W poniższej tabeli przedstawiono gwarancje dotyczące wysokiej dostępności zapewnianej przez Cosmos DB w przypadku kont z pojedynczym i wieloregionem. Aby zapewnić wysoką dostępność, należy zawsze skonfigurować konta usługi Cosmos tak, aby zawierały wiele regionów zapisu.

|Typ operacji  | Pojedynczy region |Wiele regionów (zapis w jednym regionie)|Wiele regionów (zapisy w regionie wieloregionowym) |
|---------|---------|---------|-------|
|Powoduje    | 99,99    |99,99   |99,999|
|Odczytywan     | 99,99    |99,999  |99,999|

> [!NOTE]
> W ramach tej usługi rzeczywista dostępność zapisu dla nieodświeżonego, sesji, spójnego prefiksu i wzorów spójności ostatecznej jest znacznie wyższa niż opublikowana umowy SLA. Rzeczywista dostępność odczytu dla wszystkich poziomów spójności jest znacznie wyższa niż opublikowana umowy SLA.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Zapewnianie wysokiej dostępności za pomocą usługi Cosmos DB w przypadku awarii regionalnych

Awarie regionalne nie są niczym niezwykłym, a usługa Azure Cosmos DB zapewnia, że baza danych jest zawsze wysoko dostępna. Następujące szczegóły przechwytują Cosmos DB zachowanie podczas przestoju, w zależności od konfiguracji konta Cosmos:

- Gdy Cosmos DB, przed potwierdzeniem operacji zapisu do klienta dane są trwale przez kworum replik w regionie, który akceptuje operacje zapisu.

- Konta z wieloma regionami skonfigurowane z regionami z wieloma zapisami będą mieć wysoką dostępność dla operacji zapisu i odczytu. Regionalne przejścia w tryb failover są chwilowe i nie wymagają żadnych zmian w aplikacji.

- Konta w jednym regionie mogą utracić dostępność po awarii regionalnej. Zawsze zaleca się skonfigurowanie **co najmniej dwóch regionów** (najlepiej co najmniej dwóch regionów zapisu) przy użyciu konta Cosmos, aby zapewnić wysoką dostępność przez cały czas.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Konta wieloregionowe z regionem jednokrotnego zapisu (przestój w regionie zapisu)

- Podczas awarii regionu zapisu konto Cosmos automatycznie promuje region pomocniczy jako nowy podstawowy region zapisu, gdy na koncie usługi Azure Cosmos zostanie skonfigurowane **Automatyczne przełączanie do trybu failover** . Po włączeniu tryb failover nastąpi w innym regionie w podanej kolejności regionu.
- Gdy wcześniej zmieniony region jest w trybie online, wszelkie dane zapisu, które nie zostały zreplikowane w przypadku niepowodzenia regionu, są udostępniane za pomocą [źródła konfliktów](how-to-manage-conflicts.md#read-from-conflict-feed). Aplikacje mogą odczytywać źródła konfliktów, rozwiązywać konflikty na podstawie logiki specyficznej dla aplikacji, a następnie zapisywać zaktualizowane dane z powrotem do kontenera platformy Azure Cosmos zgodnie z potrzebami.
- Po odzyskaniu wcześniej zmienionego regionu zapisu zostanie on automatycznie udostępniony jako region odczytu. Można wrócić do odzyskanego regionu jako region zapisu. Regiony można przełączyć przy użyciu [programu PowerShell, interfejsu wiersza polecenia platformy Azure lub Azure Portal](how-to-manage-database-account.md#manual-failover). Przed, w trakcie lub po przełączeniu regionu zapisu **nie ma utraty danych ani dostępności** , a aplikacja nadal będzie o wysokiej dostępności.

> [!IMPORTANT]
> Zdecydowanie zaleca się skonfigurowanie kont usługi Azure Cosmos używanych na potrzeby obciążeń produkcyjnych, aby **umożliwić automatyczne przełączanie do trybu failover**. Ręczne przełączenie w tryb failover wymaga łączności między dodatkowym i podstawowym regionem zapisu, aby ukończyć sprawdzanie spójności, aby upewnić się, że podczas pracy w trybie failover nie ma utraty Jeśli region podstawowy jest niedostępny, to sprawdzanie spójności nie będzie możliwe, a ręczne przełączenie w tryb failover nie powiedzie się, co spowodowało utratę dostępności zapisu.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Konta wieloregionowe z regionem jednokrotnego zapisu (awaria regionu odczytu)

- Podczas przestoju regionu odczytu konta usługi Cosmos z zastosowaniem dowolnego poziomu spójności lub silnej spójności z co najmniej trzema regionami odczytu pozostaną wysoce dostępne do odczytu i zapisu.
- Region, którego dotyczy problem, jest automatycznie rozłączany i zostanie oznaczony jako w trybie offline. [Zestawy sdk Azure Cosmos DB](sql-api-sdk-dotnet.md) będą przekierowywać wywołania odczytu do następnego dostępnego regionu na liście preferowanych regionów.
- Jeśli żaden z regionów na liście preferowanych regionów nie jest dostępny, wywołania automatycznie wracają do bieżącego regionu zapisu.
- W kodzie aplikacji nie są wymagane żadne zmiany w celu obsługi awarii regionu odczytu. Gdy region odczytu, którego dotyczy problem, zostanie przywrócony do trybu online, zostanie automatycznie zsynchronizowany z bieżącym regionem zapisu i będzie ponownie dostępny do obsłużynia żądań odczytu.
- Dalsze operacje odczytu są przekierowywane do odzyskanego regionu bez konieczności wprowadzania jakichkolwiek zmian w kodzie aplikacji. Podczas pracy w trybie failover i ponownego przyłączania do poprzedniego regionu, gwarancje spójności odczytu są nadal uznawane przez Cosmos DB.

> [!IMPORTANT]
> Konta usługi Azure Cosmos z silną spójnością z co najmniej dwoma regionami odczytu utracą dostępność zapisu podczas awarii regionu odczytu, ale będą mieć dostęp do odczytu dla pozostałych regionów.

- Nawet w rzadkich i niezbyt krótkich przypadkach, gdy region platformy Azure jest nieodwracalnie nieodwracalny, nie ma utraty danych, jeśli konto Cosmos dla wieloregionu jest skonfigurowane z *silną* spójnością. W przypadku trwałego odzyskania regionu zapisu, wieloregionowego konta Cosmos skonfigurowanego ze spójnością nieodświeżoną, potencjalne okno utraty danych jest ograniczone do okna nieodświeżonego (*K* lub *T*), gdzie K = 100 000 aktualizacji i T = 5 minut. W przypadku sesji, spójnego i ostatecznego poziomu spójności, potencjalne okno utraty danych jest ograniczone do maksymalnie 15 minut. Aby uzyskać więcej informacji na temat elementów docelowych RTO i RPO dla Azure Cosmos DB, zobacz [poziomy spójności i trwałość danych](consistency-levels-tradeoffs.md#rto)

## <a name="availability-zone-support"></a>Obsługa strefy dostępności

Oprócz odporności między regionami można teraz włączyć **nadmiarowość stref** podczas wybierania regionu do skojarzenia z bazą danych Azure Cosmos.

W przypadku obsługi stref dostępności Azure Cosmos DB zapewnia, że repliki są umieszczane w wielu strefach w danym regionie w celu zapewnienia wysokiej dostępności i odporności podczas awarii stref. W tej konfiguracji nie wprowadzono żadnych zmian w czasie oczekiwania i innych umowy SLA. W przypadku awarii pojedynczej strefy nadmiarowość stref zapewnia pełną trwałość danych z elementem RPO = 0 i dostępnością z RTO = 0.

Nadmiarowość stref to *dodatkowa* funkcja [replikacji z wieloma wzorcami](how-to-multi-master.md) . W celu uzyskania odporności regionalnej nie można korzystać z samej nadmiarowości strefy. Na przykład w przypadku regionalnego systemu przestoju lub małego opóźnienia w regionach zaleca się, aby oprócz nadmiarowości strefy była dostępna wiele regionów zapisu.

Podczas konfigurowania wieloregionowych zapisów dla konta usługi Azure Cosmos możesz zrezygnować z nadmiarowości strefy bez dodatkowych kosztów. W przeciwnym razie zapoznaj się z poniższymi uwagami dotyczącymi cen obsługi nadmiarowości stref. Nadmiarowość strefy można włączyć w istniejącym regionie konta usługi Azure Cosmos, usuwając region i dodając go ponownie z włączonym nadmiarowością strefy.

Ta funkcja jest dostępna w następujących regionach świadczenia usługi Azure:

- Południowe Zjednoczone Królestwo

- Azja Południowo-Wschodnia

- Wschodnie stany USA

- Wschodnie stany USA 2

- Środkowe stany USA

- Europa Zachodnia

- Zachodnie stany USA 2

- Australia Wschodnia

- Japonia Wschodnia

- Europa Północna

> [!NOTE]
> Włączenie Strefy dostępności dla jednego regionu konta platformy Azure Cosmos spowoduje naliczenie opłat, które są równoznaczne z dodaniem dodatkowego regionu do konta. Aby uzyskać szczegółowe informacje o cenach, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/) oraz [Koszt dla wieloregionu w](optimize-cost-regions.md) artykułach Azure Cosmos DB.

Poniższa tabela zawiera podsumowanie możliwości wysokiej dostępności różnych konfiguracji konta:

|Wskaźnik KPI  |Pojedynczy region bez Strefy dostępności (nie AZ)  |Pojedynczy region z Strefy dostępności (AZ)  |Zapisy w wielu regionach z Strefy dostępności (AZ, 2 regiony) — najbardziej zalecanym ustawieniem |
|---------|---------|---------|---------|
|Umowa SLA dotycząca dostępności zapisu | 99,99% | 99,99% | 99.999% |
|Umowa SLA dotycząca dostępności odczytu  | 99,99% | 99,99% | 99.999% |
|Price | Stawka rozliczenia w jednym regionie | Stawka rozliczenia strefy dostępności pojedynczego regionu | Wieloregionowa stawka rozliczeniowa |
|Awarie stref — utrata danych | Utrata danych | Brak utraty danych | Brak utraty danych |
|Awarie stref — dostępność | Utrata dostępności | Brak utraty dostępności | Brak utraty dostępności |
|Opóźnienie odczytu | Między regionami | Między regionami | Małe |
|Opóźnienie zapisu | Między regionami | Między regionami | Małe |
|Awaria regionalna — utrata danych | Utrata danych |  Utrata danych | Utrata danych <br/><br/> W przypadku używania ograniczonej spójności niezgodności z wieloma wzorcami i więcej niż jednym regionem utrata danych jest ograniczona do granicy nieaktualnej konfiguracji skonfigurowanej na Twoim koncie. <br /><br />Można uniknąć utraty danych podczas regionalnej awarii, konfigurując silną spójność z wieloma regionami. Ta opcja ma wpływ na dostępność i wydajność. Można ją skonfigurować tylko na kontach, które są skonfigurowane do zapisu w jednym regionie. |
|Awaria regionalna — dostępność | Utrata dostępności | Utrata dostępności | Brak utraty dostępności |
|Przepływność | X RU/s zainicjowana przepływność | X RU/s zainicjowana przepływność | przepustowość z obsługą jednostki RU/s <br/><br/> Ten tryb konfiguracji wymaga dwukrotnej ilości przepływności w porównaniu do jednego regionu z Strefy dostępności, ponieważ istnieją dwa regiony. |

> [!NOTE]
> Aby włączyć obsługę strefy dostępności dla konta usługi Azure Cosmos w wielu regionach, konto musi mieć włączone zapisywanie z wieloma wzorcami.

Nadmiarowość strefy można włączyć podczas dodawania regionu do nowych lub istniejących kont usługi Azure Cosmos. Aby włączyć nadmiarowość strefy na koncie usługi Azure Cosmos, należy ustawić `isZoneRedundant` flagę na `true` dla określonej lokalizacji. Tę flagę można ustawić we właściwości Locations. Na przykład poniższy fragment kodu programu PowerShell umożliwia nadmiarowość strefy dla regionu "Azja Południowo-Wschodnia":

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1; "isZoneRedundant"= "true" }
)
```

Następujące polecenie pokazuje, jak włączyć nadmiarowość strefy dla regionów "Wschodnie" i "WestUS2":

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

Strefy dostępności można włączyć przy użyciu Azure Portal podczas tworzenia konta usługi Azure Cosmos. Podczas tworzenia konta, upewnij się, że włączono **geograficzną**i **Wieloregionową zapis**, i wybierz region, w którym strefy dostępności są obsługiwane:

![Włącz Strefy dostępności przy użyciu Azure Portal](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Tworzenie aplikacji o wysokiej dostępności

- Aby zapewnić wysoką dostępność i odczyt, skonfiguruj konto Cosmos tak, aby obejmowało co najmniej dwa regiony z regionami wielokrotnego zapisu. Ta konfiguracja zapewnia najwyższą dostępność, najniższy czas oczekiwania i najlepszą skalowalność dla operacji odczytu i zapisu, które są obsługiwane przez umowy SLA. Aby dowiedzieć się więcej, zobacz jak [skonfigurować konto Cosmos z wieloma regionami zapisu](tutorial-global-distribution-sql-api.md).

- W przypadku kont Cosmos z wieloregionem, które są skonfigurowane w regionie jednokrotnego zapisu, należy [włączyć funkcję automatycznego przełączania do trybu failover przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure Portal](how-to-manage-database-account.md#automatic-failover). Po włączeniu automatycznego trybu failover, gdy wystąpi awaria regionalna, Cosmos DB automatycznie przejdzie w tryb failover na koncie.  

- Nawet jeśli Twoje konto usługi Azure Cosmos ma wysoką dostępność, aplikacja może nie być prawidłowo zaprojektowana tak, aby pozostawała wysoce dostępna. Aby przetestować kompleksową wysoką dostępność aplikacji, w ramach przechodzenia do testowania aplikacji lub odzyskiwania po awarii (DR), tymczasowo wyłącz automatyczne przełączanie do trybu failover dla konta, wywołaj [ręczną pracę awaryjną przy użyciu programu PowerShell, interfejsu wiersza polecenia platformy Azure lub Azure Portal](how-to-manage-database-account.md#manual-failover), a następnie Monitoruj tryb failover aplikacji. Po zakończeniu można przeprowadzić powrót po awarii do regionu podstawowego i przywrócić automatyczne przejście w tryb failover dla konta.

- W całym globalnie rozproszonym środowisku bazy danych istnieje bezpośrednia relacja między poziomem spójności a trwałością danych w przypadku awarii całego regionu. Podczas opracowywania planu ciągłości biznesowej należy zrozumieć maksymalny akceptowalny czas, po upływie którego aplikacja zostanie w pełni odzyskana po zdarzeniu zakłócania. Czas wymagany do pełnego odzyskania aplikacji jest znany jako cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalny okres ostatnich aktualizacji danych, które aplikacja może tolerować podczas odzyskiwania po wystąpieniu zdarzenia zakłócenia. Okres aktualizacji, którego utrata może być tolerowana, jest określany jako cel punktu odzyskiwania (RPO, recovery point objective). Aby zobaczyć cel punktu odzyskiwania i RTO dla Azure Cosmos DB, zobacz [poziomy spójności i trwałość danych](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Następne kroki

Następnie możesz zapoznać się z następującymi artykułami:

- [Wady dostępności i wydajności dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
- [Globalne skalowanie aprowizowanej przepływności](scaling-throughput.md)
- [Dystrybucja globalna — szczegóły działania](global-dist-under-the-hood.md)
- [Poziomy spójności w Azure Cosmos DB](consistency-levels.md)
- [Jak skonfigurować konto Cosmos z wieloma regionami zapisu](how-to-multi-master.md)
