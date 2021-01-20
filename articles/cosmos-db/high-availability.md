---
title: High availability in Azure Cosmos DB (Wysoka dostępność w usłudze Azure Cosmos DB)
description: W tym artykule opisano, jak Azure Cosmos DB zapewnia wysoką dostępność
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/18/2021
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: d827011c4f831433a7446c90eed0c30c7b1e94d7
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600564"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Jak Azure Cosmos DB zapewniać wysoką dostępność
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB zapewnia wysoką dostępność na dwa podstawowe sposoby. Najpierw Azure Cosmos DB replikuje dane między regionami skonfigurowanymi w ramach konta Cosmos. Po drugie, Azure Cosmos DB przechowuje 4 repliki danych w danym regionie.

Azure Cosmos DB to globalnie dystrybuowana usługa bazy danych i usługa jest dostępna we [wszystkich regionach, w których platforma Azure jest dostępna](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all). Możesz skojarzyć dowolną liczbę regionów świadczenia usługi Azure z kontem usługi Azure Cosmos, a Twoje dane są automatycznie i w sposób niewidoczny dla użytkownika zreplikowane. Możesz w dowolnym momencie dodać lub usunąć region do konta usługi Azure Cosmos. Cosmos DB jest dostępny we wszystkich pięciu różnych środowiskach chmury platformy Azure dostępnych dla klientów:

* Chmura **publiczna platformy Azure** , która jest dostępna globalnie.

* **Usługa Azure Chiny 21Vianet** jest dostępna za pomocą unikatowego powiązania między firmą Microsoft i 21Vianet — jednym z największych dostawców Internetu w danym kraju w Chinach.

* **Platforma Azure (Niemcy** ) udostępnia usługi w ramach modelu zarządca danych, co zapewnia, że dane klienta pozostają w Niemczech pod kontrolą międzyplatformowych usług, a w przypadku międzynarodowej GmbH, a także w ramach niemieckiego zarządca danych.

* **Azure Government** jest dostępna w czterech regionach Stany Zjednoczone do instytucji RZĄDowych Stanów Zjednoczonych i ich partnerów.

* **Azure Government dla Departamentu Obrony (DoD)** jest dostępny w dwóch regionach w Stany Zjednoczone do Departamentu Obrony Stanów Zjednoczonych.

W ramach regionu Azure Cosmos DB przechowuje cztery kopie danych jako repliki w ramach partycji fizycznych, jak pokazano na poniższej ilustracji:

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Partycjonowanie fizyczne" border="false":::

* Dane w kontenerach usługi Azure Cosmos są [podzielone na partycje w poziomie](partitioning-overview.md).

* Zestaw partycji jest kolekcją wielu zestawów replik. W każdym regionie każda partycja jest chroniona przez zestaw replik, a wszystkie zapisy są replikowane i trwale zatwierdzone przez większość replik. Repliki są dystrybuowane w wielu domenach błędów 10-20.

* Każda partycja we wszystkich regionach jest replikowana. Każdy region zawiera wszystkie partycje danych kontenera usługi Azure Cosmos i może obsłużyć operacje odczytu, a także obsłużyć zapisy w przypadku włączenia zapisu w ramach wieloregionu.  

Jeśli Twoje konto usługi Azure Cosmos jest dystrybuowane w obrębie *N* regionów platformy Azure, wszystkie dane będą mieć co najmniej *N* x 4 kopii. Posiadanie konta usługi Azure Cosmos w więcej niż 2 regionach zwiększa dostępność aplikacji i zapewnia małe opóźnienia w skojarzonych regionach.

## <a name="slas-for-availability"></a>Umowy SLA do dostępności

Azure Cosmos DB zapewnia kompleksową umowy SLA, która obejmuje przepływność, opóźnienie w 99 percentylu, spójność i wysoką dostępność. W poniższej tabeli przedstawiono gwarancje dotyczące wysokiej dostępności zapewnianej przez Azure Cosmos DB w przypadku kont z pojedynczym i wieloregionem. Aby uzyskać większą dostępność zapisu, skonfiguruj konto usługi Azure Cosmos w taki sposób, aby miało wiele regionów zapisu.

|Typ operacji  | Jeden region |Wiele regionów (zapis w jednym regionie)|Wiele regionów (zapisy w regionie wieloregionowym) |
|---------|---------|---------|-------|
|Zapisy    | 99,99    |99,99   |99,999|
|Odczyty     | 99,99    |99,999  |99,999|

> [!NOTE]
> W ramach tej usługi rzeczywista dostępność zapisu dla nieodświeżonego, sesji, spójnego prefiksu i wzorów spójności ostatecznej jest znacznie wyższa niż opublikowana umowy SLA. Rzeczywista dostępność odczytu dla wszystkich poziomów spójności jest znacznie wyższa niż opublikowana umowy SLA.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>Wysoka dostępność z Azure Cosmos DB w przypadku awarii regionalnej

W rzadkich przypadkach regionalnych awarii Azure Cosmos DB gwarantuje, że baza danych zawsze ma wysoką dostępność. Poniższe informacje szczegółowe przechwytują zachowanie Azure Cosmos DB podczas przestoju, w zależności od konfiguracji konta usługi Azure Cosmos:

* Gdy Azure Cosmos DB, przed potwierdzeniem operacji zapisu do klienta dane są trwale przez kworum replik w regionie, który akceptuje operacje zapisu. Aby uzyskać więcej informacji, zobacz [poziomy spójności i przepływność](./consistency-levels.md#consistency-levels-and-throughput)

* Konta z wieloma regionami skonfigurowane z regionami z wieloma zapisami będą mieć wysoką dostępność dla operacji zapisu i odczytu. Regionalne tryb failover są wykrywane i obsługiwane przez klienta Azure Cosmos DB. Są one również chwilowe i nie wymagają żadnych zmian w aplikacji.

* Konta w jednym regionie mogą utracić dostępność po awarii regionalnej. Zawsze zaleca się skonfigurowanie **co najmniej dwóch regionów** (najlepiej co najmniej dwóch regionów zapisu) przy użyciu konta usługi Azure Cosmos, aby zapewnić wysoką dostępność.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Konta wieloregionowe z regionem jednokrotnego zapisu (przestój w regionie zapisu)

* Podczas przestoju w regionie zapisu konto usługi Azure Cosmos automatycznie promuje region pomocniczy jako nowy podstawowy region zapisu, gdy w ramach konta usługi Azure Cosmos zostanie skonfigurowane **Automatyczne przełączanie do trybu failover** . Po włączeniu tryb failover nastąpi w innym regionie w podanej kolejności regionu.

* Gdy wcześniej zmieniony region jest w trybie online, wszelkie dane zapisu, które nie zostały zreplikowane w przypadku niepowodzenia regionu, są udostępniane za pomocą [źródła konfliktów](how-to-manage-conflicts.md#read-from-conflict-feed). Aplikacje mogą odczytywać źródła konfliktów, rozwiązywać konflikty na podstawie logiki specyficznej dla aplikacji, a następnie zapisywać zaktualizowane dane z powrotem do kontenera platformy Azure Cosmos zgodnie z potrzebami.

* Po odzyskaniu wcześniej zmienionego regionu zapisu zostanie on automatycznie udostępniony jako region odczytu. Można wrócić do odzyskanego regionu jako region zapisu. Regiony można przełączyć przy użyciu [programu PowerShell, interfejsu wiersza polecenia platformy Azure lub Azure Portal](how-to-manage-database-account.md#manual-failover). Przed, w trakcie lub po przełączeniu regionu zapisu **nie ma utraty danych ani dostępności** , a aplikacja nadal będzie o wysokiej dostępności.

> [!IMPORTANT]
> Zdecydowanie zaleca się skonfigurowanie kont usługi Azure Cosmos używanych na potrzeby obciążeń produkcyjnych, aby **umożliwić automatyczne przełączanie do trybu failover**. Ręczne przełączenie w tryb failover wymaga łączności między dodatkowym i podstawowym regionem zapisu, aby ukończyć sprawdzanie spójności, aby upewnić się, że podczas pracy w trybie failover nie ma utraty Jeśli region podstawowy jest niedostępny, to sprawdzanie spójności nie będzie możliwe, a ręczne przełączenie w tryb failover nie powiedzie się, co spowodowało utratę dostępności zapisu na czas trwania regionalnej awarii.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Konta wieloregionowe z regionem jednokrotnego zapisu (awaria regionu odczytu)

* Podczas awarii regionu odczytu konta usługi Azure Cosmos korzystające z dowolnego poziomu spójności lub silnej spójności z co najmniej trzema regionami odczytu pozostaną wysoce dostępne do odczytu i zapisu.

* Konta usługi Azure Cosmos z silną spójnością w przypadku dwóch lub mniej regionów odczytu (w tym regionu zapisu & odczytu) utracą dostępność zapisu podczas awarii regionu odczytu.

* Region, którego dotyczy problem, jest automatycznie rozłączany i zostanie oznaczony jako w trybie offline. [Zestawy sdk Azure Cosmos DB](sql-api-sdk-dotnet.md) będą przekierowywać wywołania odczytu do następnego dostępnego regionu na liście preferowanych regionów.

* Jeśli żaden z regionów na liście preferowanych regionów nie jest dostępny, wywołania automatycznie wracają do bieżącego regionu zapisu.

* W kodzie aplikacji nie są wymagane żadne zmiany w celu obsługi awarii regionu odczytu. Gdy region odczytu, którego dotyczy problem, zostanie przywrócony do trybu online, zostanie automatycznie zsynchronizowany z bieżącym regionem zapisu i będzie ponownie dostępny do obsłużynia żądań odczytu.

* Dalsze operacje odczytu są przekierowywane do odzyskanego regionu bez konieczności wprowadzania jakichkolwiek zmian w kodzie aplikacji. Podczas pracy w trybie failover i ponownego przyłączania do poprzedniego regionu, gwarancje spójności odczytu są nadal uznawane przez Azure Cosmos DB.

* Nawet w rzadkich i niezbyt krótkich przypadkach, gdy region platformy Azure jest nieodwracalnie nieodwracalny, nie ma utraty danych, jeśli konto usługi Azure Cosmos dla wieloregionu jest skonfigurowane z *silną* spójnością. W przypadku trwałego odzyskania regionu zapisu w wieloregionowym koncie usługi Azure Cosmos skonfigurowanym z ograniczoną spójnością nieodświeżoną, potencjalne okno utraty danych jest ograniczone do okna nieodświeżonego (*K* lub *T*), gdzie K = 100 000 aktualizacji lub T = 5 minut, które kiedykolwiek się powtarzają. W przypadku sesji, spójnego i ostatecznego poziomu spójności, potencjalne okno utraty danych jest ograniczone do maksymalnie 15 minut. Aby uzyskać więcej informacji na temat elementów docelowych RTO i RPO dla Azure Cosmos DB, zobacz [poziomy spójności i trwałość danych](./consistency-levels.md#rto)

## <a name="availability-zone-support"></a>Obsługa strefy dostępności

Oprócz odporności między regionami Azure Cosmos DB obsługuje również **nadmiarowość stref** w obsługiwanych regionach podczas wybierania regionu do skojarzenia z kontem usługi Azure Cosmos.

Dzięki obsłudze strefy dostępności (AZ) Azure Cosmos DB zapewnią, że repliki są umieszczane w wielu strefach w danym regionie w celu zapewnienia wysokiej dostępności i odporności na awarie stref. Strefy dostępności udostępnić umowę SLA dotyczącą dostępności na 99,995% bez zmian opóźnienia. W przypadku awarii pojedynczej strefy nadmiarowość stref zapewnia pełną trwałość danych z elementem RPO = 0 i dostępnością z RTO = 0. Nadmiarowość stref jest dodatkową możliwością dla replikacji regionalnej. W celu uzyskania odporności regionalnej nie można korzystać z samej nadmiarowości strefy.

Nadmiarowość strefy można skonfigurować tylko w przypadku dodawania nowego regionu do konta usługi Azure Cosmos. W przypadku istniejących regionów nadmiarowość stref można włączyć, usuwając region, a następnie dodając go ponownie z włączonym nadmiarowością strefy. W przypadku konta z jednym regionem należy dodać jeden dodatkowy region do tymczasowego przejścia w tryb failover, a następnie usunąć i dodać żądany region z włączoną nadmiarowością strefy.

Podczas konfigurowania wieloregionowych zapisów dla konta usługi Azure Cosmos możesz zrezygnować z nadmiarowości strefy bez dodatkowych kosztów. W przeciwnym razie zapoznaj się z poniższą tabelą dotyczącą cennika obsługi nadmiarowości strefy. Aby zapoznać się z listą regionów, w których dostępne są strefy dostępności, zobacz [strefy dostępności](../availability-zones/az-region.md).

Poniższa tabela zawiera podsumowanie możliwości wysokiej dostępności różnych konfiguracji konta:

|KPI|Pojedynczy region bez AZs|Pojedynczy region z AZs|Wieloregionowe zapisy w jednym regionie z AZs|Wieloregionowe zapisy wieloregionowe z AZs|
|---------|---------|---------|---------|---------|
|Umowa SLA dotycząca dostępności zapisu | 99,99% | 99,995% | 99,995% | 99.999% |
|Umowa SLA dotycząca dostępności odczytu  | 99,99% | 99,995% | 99,995% | 99.999% |
|Awarie stref — utrata danych | Utrata danych | Brak utraty danych | Brak utraty danych | Brak utraty danych |
|Awarie stref — dostępność | Utrata dostępności | Brak utraty dostępności | Brak utraty dostępności | Brak utraty dostępności |
|Awaria regionalna — utrata danych | Utrata danych |  Utrata danych | Zależne od poziomu spójności. Aby uzyskać więcej informacji [, zobacz kompromisy dotyczące spójności, dostępności i wydajności](consistency-levels-tradeoffs.md) . | Zależne od poziomu spójności. Aby uzyskać więcej informacji [, zobacz kompromisy dotyczące spójności, dostępności i wydajności](consistency-levels-tradeoffs.md) .
|Awaria regionalna — dostępność | Utrata dostępności | Utrata dostępności | Brak utraty dostępności dla niepowodzenia odczytu regionu, tymczasowego dla niepowodzenia w regionie zapisu | Brak utraty dostępności |
|Cena (**_1_* _) | Nie dotyczy | Liczba zainicjowanych jednostek RU/s x 1,25 | Liczba zainicjowanych jednostek RU/s x 1,25 (_*_2_*_) | Wieloregionowa stawka zapisu |

_*_1_*_ w przypadku jednostek żądań bezserwerowych (ru) są mnożone przez współczynnik 1,25.

_*_2_*_ 1,25 stawka stosowana tylko do tych regionów, w których jest włączona funkcja AZ.

Strefy dostępności można włączyć za pośrednictwem:

_ [Azure Portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [Azure PowerShell](manage-with-powershell.md#create-account)

* [Interfejs wiersza polecenia platformy Azure](manage-with-cli.md#add-or-remove-regions)

* [Szablony usługi Azure Resource Manager](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>Tworzenie aplikacji o wysokiej dostępności

* Zapoznaj się z oczekiwanym [zachowaniem zestawów SDK usługi Azure Cosmos](troubleshoot-sdk-availability.md) w ramach tych zdarzeń, które są konfiguracjami, które mają na nie wpływ.

* Aby zapewnić wysoką dostępność i odczyt, skonfiguruj konto usługi Azure Cosmos tak, aby obejmowało co najmniej dwa regiony z regionami wielokrotnego zapisu. Ta konfiguracja zapewnia najwyższą dostępność, najniższy czas oczekiwania i najlepszą skalowalność dla operacji odczytu i zapisu, które są obsługiwane przez umowy SLA. Aby dowiedzieć się więcej, zobacz jak [skonfigurować konto platformy Azure Cosmos z wieloma regionami zapisu](tutorial-global-distribution-sql-api.md).

* Dla wieloregionowych kont usługi Azure Cosmos, które są skonfigurowane za pomocą regionu jednokrotnego zapisu, [Włącz automatyczne przełączanie do trybu failover przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure Portal](how-to-manage-database-account.md#automatic-failover). Po włączeniu automatycznego trybu failover, gdy wystąpi awaria regionalna, Cosmos DB automatycznie przejdzie w tryb failover na koncie.  

* Nawet jeśli Twoje konto usługi Azure Cosmos ma wysoką dostępność, aplikacja może nie być prawidłowo zaprojektowana tak, aby pozostawała wysoce dostępna. Aby przetestować kompleksową wysoką dostępność aplikacji, w ramach przechodzenia do testowania aplikacji lub odzyskiwania po awarii (DR), tymczasowo wyłącz automatyczne przełączanie do trybu failover dla konta, wywołaj [ręczną pracę awaryjną przy użyciu programu PowerShell, interfejsu wiersza polecenia platformy Azure lub Azure Portal](how-to-manage-database-account.md#manual-failover), a następnie Monitoruj tryb failover aplikacji. Po zakończeniu można przeprowadzić powrót po awarii do regionu podstawowego i przywrócić automatyczne przejście w tryb failover dla konta.

* W całym globalnie rozproszonym środowisku bazy danych istnieje bezpośrednia relacja między poziomem spójności a trwałością danych w przypadku awarii całego regionu. Podczas opracowywania planu ciągłości biznesowej należy zrozumieć maksymalny akceptowalny czas, po upływie którego aplikacja zostanie w pełni odzyskana po zdarzeniu zakłócania. Czas wymagany do pełnego odzyskania aplikacji jest znany jako cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalny okres ostatnich aktualizacji danych, które aplikacja może tolerować podczas odzyskiwania po wystąpieniu zdarzenia zakłócenia. Okres aktualizacji, którego utrata może być tolerowana, jest określany jako cel punktu odzyskiwania (RPO, recovery point objective). Aby zobaczyć cel punktu odzyskiwania i RTO dla Azure Cosmos DB, zobacz [poziomy spójności i trwałość danych](./consistency-levels.md#rto)

## <a name="next-steps"></a>Następne kroki

Następnie możesz zapoznać się z następującymi artykułami:

* [Wady dostępności i wydajności dla różnych poziomów spójności](./consistency-levels.md)

* [Globalne skalowanie aprowizowanej przepływności](./request-units.md)

* [Dystrybucja globalna — szczegóły działania](global-dist-under-the-hood.md)

* [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md)

* [Jak skonfigurować konto Cosmos z wieloma regionami zapisu](how-to-multi-master.md)

* [Zachowanie zestawu SDK w środowiskach z obsługą wielu regionów](troubleshoot-sdk-availability.md)