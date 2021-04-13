---
title: High availability in Azure Cosmos DB (Wysoka dostępność w usłudze Azure Cosmos DB)
description: W tym artykule opisano, Azure Cosmos DB zapewnia wysoką dostępność
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ac1e77d99707cdaa34ef42eb9b327a62f4e864c0
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365369"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Jak Azure Cosmos DB wysoką dostępność
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB zapewnia wysoką dostępność na dwa podstawowe sposoby. Najpierw Azure Cosmos DB dane między regionami skonfigurowanymi w ramach konta usługi Cosmos. Po drugie Azure Cosmos DB 4 repliki danych w obrębie regionu.

Azure Cosmos DB jest globalnie rozproszoną usługą bazy danych i jest usługą bazową dostępną we wszystkich regionach, w których platforma [Azure jest dostępna.](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all) Z kontem usługi Azure Cosmos można skojarzyć dowolną liczbę regionów świadczenia usługi Azure, a dane są replikowane automatycznie i w sposób niewidoczny dla użytkownika. W dowolnym momencie możesz dodać lub usunąć region do konta usługi Azure Cosmos. Cosmos DB jest dostępna we wszystkich pięciu odrębnych środowiskach chmury platformy Azure dostępnych dla klientów:

* **Chmura publiczna** platformy Azure, która jest dostępna globalnie.

* **Azure (Chiny) — 21Vianet** jest dostępna za pośrednictwem unikatowego partnerstwa między firmą Microsoft i firmą 21Vianet, jednym z największych dostawców internetu w Chinach.

* **Platforma Azure (Niemcy)** oferuje usługi w ramach modelu powierni danych, który gwarantuje, że dane klientów pozostają pod kontrolą firmy T-Systems International GmbH, podmiotu zależnego Firmy Zygmejs Telekom działającej jako niemiecki powiernica danych.

* **Azure Government** jest dostępna w czterech regionach na Stany Zjednoczone dla instytucji rządowych USA i ich partnerów.

* Azure Government departamentu obrony **(DoD, Department of Defense)** jest dostępny w dwóch regionach na Stany Zjednoczone departamentu obrony Stanów Zjednoczonych.

W obrębie regionu Azure Cosmos DB przechowuje cztery kopie danych jako repliki w ramach partycji fizycznych, jak pokazano na poniższej ilustracji:

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Partycjonowanie fizyczne" border="false":::

* Dane w kontenerach usługi Azure Cosmos są [partycjonowane w poziomie.](partitioning-overview.md)

* Zestaw partycji jest kolekcją wielu zestawów replik. W każdym regionie każda partycja jest chroniona przez zestaw replik ze wszystkimi zapisami replikowanych i trwale zatwierdzone przez większość replik. Repliki są dystrybuowane do nawet 10–20 domen błędów.

* Każda partycja we wszystkich regionach jest replikowana. Każdy region zawiera wszystkie partycje danych kontenera usługi Azure Cosmos i może obsługiwać odczyty oraz obsługiwać zapis po włączeniu zapisu w wielu regionach.  

Jeśli Konto usługi Azure Cosmos jest dystrybuowane w *N* regionach świadczenia usługi Azure, wszystkie dane będą mieć co najmniej *N* x 4 kopie. Posiadanie konta usługi Azure Cosmos w więcej niż 2 regionach zwiększa dostępność aplikacji i zapewnia małe opóźnienia w skojarzonych regionach.

## <a name="slas-for-availability"></a>Slas for availability (Slas for availability)

Azure Cosmos DB kompleksowe sla, które obejmują przepływność, opóźnienie na poziomie 99. percentyla, spójność i wysoką dostępność. W poniższej tabeli przedstawiono gwarancje wysokiej dostępności zapewniane przez Azure Cosmos DB dla kont w jednym i wielu regionach. Aby uzyskać wyższą dostępność zapisu, skonfiguruj konto usługi Azure Cosmos, aby mieć wiele regionów zapisu.

|Typ operacji  | Jeden region |Wiele regionów (zapis w jednym regionie)|Wiele regionów (zapis w wielu regionach) |
|---------|---------|---------|-------|
|Zapisy    | 99,99    |99,99   |99,999|
|Odczyty     | 99,99    |99,999  |99,999|

> [!NOTE]
> W praktyce rzeczywista dostępność zapisu dla powiązanej nieaktualności, sesji, spójnych prefiksów i modeli spójności ostateczna jest znacznie wyższa niż w opublikowanych postanowieniach SLA. Rzeczywista dostępność odczytu dla wszystkich poziomów spójności jest znacznie wyższa niż w opublikowanych usługach SLA.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>Wysoka dostępność Azure Cosmos DB w przypadku regionalnych outages

W rzadkich przypadkach regionalnej Azure Cosmos DB zapewnia, że baza danych jest zawsze wysoce dostępna. Następujące szczegóły przechwytują Azure Cosmos DB podczas 300 minut w zależności od konfiguracji konta usługi Azure Cosmos:

* W Azure Cosmos DB, zanim operacja zapisu zostanie potwierdzony do klienta, dane są trwale zatwierdzone przez kworum replik w regionie, które akceptuje operacje zapisu. Aby uzyskać więcej informacji, zobacz [Poziomy spójności i przepływność](./consistency-levels.md#consistency-levels-and-throughput)

* Konta w wielu regionach skonfigurowane z regionami wielokrotnego zapisu będą wysoce dostępne zarówno dla zapisu, jak i odczytu. Regionalne trybu failover są wykrywane i obsługiwane w Azure Cosmos DB klienta. Są one również natychmiastowe i nie wymagają żadnych zmian w aplikacji.

* Konta w jednym regionie mogą utracić dostępność po regionalnej utracie dostępności. Zawsze zaleca się skonfigurowanie  co najmniej dwóch regionów (najlepiej, co najmniej dwóch regionów zapisu) przy użyciu konta usługi Azure Cosmos w celu zapewnienia wysokiej dostępności przez cały czas.

> [!IMPORTANT]
> W przypadku korzystania z interfejsów API SQL należy skonfigurować zestaw SDK usługi Cosmos DB do korzystania ze wszystkich określonych regionów odczytu w celu skorzystania ze zwiększonej dostępności. Aby uzyskać [więcej informacji, zapoznaj](troubleshoot-sdk-availability.md) się z tym artykułem.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Konta w wielu regionach z regionem z pojedynczym zapisem (przekwitanie regionu zapisu)

* Podczas awarii regionu zapisu konto usługi Azure Cosmos automatycznie podniesie region pomocniczy do nowego podstawowego regionu zapisu po skonfigurowaniu automatycznego trybu **failover** na koncie usługi Azure Cosmos. Po włączeniu tej opcji tryb failover będzie miał miejsce w innym regionie w kolejności podanego priorytetu regionu.

* Należy pamiętać, że ręcznego trybu failover nie należy wyzwalać i nie powiedzie się w przypadku awarii regionu źródłowego lub docelowego. Jest to spowodowane sprawdzaniem spójności wymaganym przez procedurę trybu failover, która wymaga łączności między regionami.

* Gdy region, który wcześniej miał wpływ, jest ponownie w trybie online, wszystkie dane zapisu, które nie zostały zreplikowane, gdy region zakończył się niepowodzeniem, są udostępniane za pośrednictwem kanału [informacyjnego konfliktów](how-to-manage-conflicts.md#read-from-conflict-feed). Aplikacje mogą odczytywać źródło danych konfliktów, rozwiązywać konflikty na podstawie logiki specyficznej dla aplikacji i zapisywać zaktualizowane dane z powrotem w kontenerze usługi Azure Cosmos zgodnie z potrzebami.

* Po odzyskaniu regionu zapisu, na który miało to wpływ, stanie się on automatycznie dostępny jako region odczytu. Możesz przełączyć się z powrotem do odzyskanego regionu jako region zapisu. Regiony można przełączać przy użyciu programu [PowerShell,](how-to-manage-database-account.md#manual-failover)interfejsu wiersza polecenia platformy Azure lub Azure Portal . Nie ma **utraty danych ani dostępności** przed przełączeniem regionu zapisu ani po nim, a aplikacja nadal jest wysoce dostępna.

> [!IMPORTANT]
> Zdecydowanie zaleca się skonfigurowanie kont usługi Azure Cosmos używanych dla obciążeń produkcyjnych w celu włączenia **automatycznego trybu failover.** Umożliwia to Cosmos DB trybu failover baz danych kont w regionach dostępności automatycznie. W przypadku braku tej konfiguracji na koncie wystąpi utrata dostępności zapisu przez cały czas trwania awarii regionu zapisu, ponieważ ręcznego trybu failover nie powiedzie się z powodu braku łączności z regionem.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Konta w wielu regionach z regionem z pojedynczym zapisem (przepłytanie regionu odczytu)

* Podczas 3 lub większej liczby regionów odczytu konta usługi Azure Cosmos korzystające z dowolnego poziomu spójności lub silnej spójności z co najmniej trzema regionami odczytu pozostaną wysoce dostępne dla odczytów i zapisu.

* Konta usługi Azure Cosmos korzystające z silnej spójności z trzema regionami (jeden zapis, dwa odczyt) zachowają dostępność zapisu podczas 3000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 W przypadku kont z dwoma regionami i włączonym automatycznym trybem failover konto przestanie akceptować zapis, dopóki region nie zostanie oznaczony jako nieudany i nastąpi automatyczny tryb failover.

* Region, na który ma to wpływ, zostanie automatycznie rozłączony i zostanie oznaczony jako offline. Zestawy [Azure Cosmos DB przekierowują](sql-api-sdk-dotnet.md) wywołania odczytu do następnego dostępnego regionu na liście preferowanych regionów.

* Jeśli żaden z regionów na liście preferowanych regionów nie jest dostępny, wywołania automatycznie wracają do bieżącego regionu zapisu.

* Żadne zmiany w kodzie aplikacji nie są wymagane, aby obsłużyć przechył w regionie odczytu. Gdy region odczytu, na który ma to wpływ, wróci do trybu online, zostanie on automatycznie zsynchronizowany z bieżącym regionem zapisu i będzie ponownie dostępny do obsługi żądań odczytu.

* Dalsze operacje odczytu są przekierowywane do odzyskanego regionu bez konieczności wprowadzania jakichkolwiek zmian w kodzie aplikacji. Podczas pracy w trybu failover i ponownego dołączania do regionu, który wcześniej zakończył się niepowodzeniem, gwarancje spójności odczytu są nadal Azure Cosmos DB.

* Nawet w rzadkich i nietrwałych zdarzeniach, gdy region świadczenia usługi Azure jest trwale nieodzyskiwalny, nie  ma utraty danych, jeśli konto usługi Azure Cosmos w wielu regionach jest skonfigurowane z silną spójnością. W przypadku trwale nieodzyskiwalnego regionu zapisu dla wielo regionów konta usługi Azure Cosmos skonfigurowanego ze spójnością powiązaną nieaktualność okno potencjalnej utraty danych jest ograniczone do okna nieaktualności *(K* lub *T),* gdzie K=100 000 aktualizacji lub T=5 minut, co kiedykolwiek nastąpi najpierw. W przypadku sesji, spójnych prefiksów i poziomów spójności ostateczna okno potencjalnej utraty danych jest ograniczone do maksymalnie 15 minut. Aby uzyskać więcej informacji o celach RTO i RPO dla Azure Cosmos DB, zobacz Poziomy spójności [i trwałość danych](./consistency-levels.md#rto)

## <a name="availability-zone-support"></a>Obsługa strefy dostępności

Oprócz odporności między regionami usługa Azure Cosmos DB  również nadmiarowość stref w obsługiwanych regionach podczas wybierania regionu do skojarzenia z kontem usługi Azure Cosmos.

Dzięki obsługi strefy dostępności (AZ) Azure Cosmos DB repliki są umieszczane w wielu strefach w danym regionie, aby zapewnić wysoką dostępność i odporność na awarie strefowe. Strefy dostępności dostępność na poziomie 99,995% bez zmian w opóźnieniu. W przypadku awarii jednej strefy nadmiarowość strefy zapewnia pełną trwałość danych z RPO = 0 i dostępności z RTO = 0. Nadmiarowość strefowa jest dodatkową możliwością replikacji regionalnej. Nie można polegać na nadmiarowości strefowej w celu zapewnienia odporności regionalnej.

Nadmiarowość stref można skonfigurować tylko podczas dodawania nowego regionu do konta usługi Azure Cosmos. W przypadku istniejących regionów nadmiarowość stref można włączyć, usuwając region, a następnie dodając go ponownie z włączoną nadmiarowością strefy. W przypadku pojedynczego konta regionu wymaga to dodania jednego dodatkowego regionu w celu tymczasowego trybu failover, a następnie usunięcia i dodania żądanego regionu z włączoną nadmiarowością strefy.

Podczas konfigurowania zapisu w wielu regionach dla konta usługi Azure Cosmos możesz wybrać nadmiarowość strefową bez dodatkowych kosztów. W przeciwnym razie zapoznaj się z tabelą poniżej dotyczącą cen obsługi nadmiarowości stref. Aby uzyskać listę regionów, w których są dostępne strefy dostępności, zobacz [Strefy dostępności](../availability-zones/az-region.md).

W poniższej tabeli przedstawiono podsumowanie możliwości wysokiej dostępności dla różnych konfiguracji kont:

|KPI|Pojedynczy region bez obszarów AZ|Pojedynczy region ze obszarami AZ|Zapis w wielu regionach i w jednym regionie za pomocą obszarów AZ|Zapis w wielu regionach i wielu regionach za pomocą obszarów AZ|
|---------|---------|---------|---------|---------|
|Umowa SLA z dostępnością zapisu | 99,99% | 99.995% | 99.995% | 99.999% |
|Umowa SLA dostępności odczytu  | 99,99% | 99.995% | 99.995% | 99.999% |
|Awarie strefy — utrata danych | Utrata danych | Brak utraty danych | Brak utraty danych | Brak utraty danych |
|Błędy stref — dostępność | Utrata dostępności | Brak utraty dostępności | Brak utraty dostępności | Brak utraty dostępności |
|Regional outage – data loss (Regional outage — utrata danych) | Utrata danych |  Utrata danych | Zależy od poziomu spójności. Aby [uzyskać więcej informacji,](./consistency-levels.md) zobacz Kompromisy w zakresie spójności, dostępności i wydajności. | Zależy od poziomu spójności. Aby [uzyskać więcej informacji,](./consistency-levels.md) zobacz Kompromisy w zakresie spójności, dostępności i wydajności.
|Regional outage – availability (Regional outage – availability) | Utrata dostępności | Utrata dostępności | Brak utraty dostępności dla błędu regionu odczytu, tymczasowy dla błędu regionu zapisu | Brak utraty dostępności |
|Price (***1** _) | Nie dotyczy | Aprowizowana szybkość RU/s x 1,25 | Aprowizowana szybkość RU/s x 1,25 (_*_2_**) | Szybkość zapisu w wielu regionach |

***1*** Jednostki żądań kont bez serwera (RU) są mnożone przez współczynnik 1,25.

***2*** Stawka 1,25 jest stosowana tylko do regionów, w których jest włączona opcja AZ.

Strefy dostępności można włączyć za pośrednictwem:

* [Azure Portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [Azure PowerShell](manage-with-powershell.md#create-account)

* [Interfejs wiersza polecenia platformy Azure](manage-with-cli.md#add-or-remove-regions)

* [Szablony usługi Azure Resource Manager](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>Tworzenie aplikacji o wysokiej dostępie

* Zapoznaj się z [oczekiwanym zachowaniem zestawów SDK usługi Azure Cosmos](troubleshoot-sdk-availability.md) podczas tych zdarzeń i konfiguracjami, które na nie wpływają.

* Aby zapewnić wysoką dostępność zapisu i odczytu, skonfiguruj konto usługi Azure Cosmos tak, aby obejmować co najmniej dwa regiony z regionami z wieloma zapisami. Ta konfiguracja zapewni najwyższą dostępność, najmniejsze opóźnienie i najlepszą skalowalność zarówno dla operacji odczytu, jak i zapisu w przypadku sla sla. Aby dowiedzieć się więcej, zobacz jak [skonfigurować konto usługi Azure Cosmos z wieloma regionami zapisu.](tutorial-global-distribution-sql-api.md)

* W przypadku kont usługi Azure Cosmos w wielu regionach, które są skonfigurowane z regionem z pojedynczym zapisem, włącz automatyczne włączanie trybu [failover](how-to-manage-database-account.md#automatic-failover)przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure Portal . Po włączeniu automatycznego trybu failover w przypadku awarii regionalnej program Cosmos DB automatycznie przełączyć konto w tryb failover.  

* Nawet jeśli Twoje konto usługi Azure Cosmos jest wysoce dostępne, aplikacja może nie być prawidłowo zaprojektowana tak, aby nadal była wysoce dostępna. Aby przetestować pełną wysoką dostępność aplikacji, w ramach testów aplikacji lub testowania odzyskiwania po awarii tymczasowo wyłącz automatyczne przechodzenie do trybu failover dla konta, wywołaj ręczne przechodzenie w tryb failover przy użyciu programu [PowerShell,](how-to-manage-database-account.md#manual-failover)interfejsu wiersza polecenia platformy Azure lub usługi Azure Portal, a następnie monitoruj tryb failover aplikacji. Po zakończeniu możesz wrócić po awarii do regionu podstawowego i przywrócić automatyczne tryb failover dla konta.

> [!IMPORTANT]
> Nie należy wywoływać ręcznego trybu failover podczas awarii Cosmos DB w regionach źródłowym lub docelowym, ponieważ wymaga to łączności regionów w celu zachowania spójności danych i nie powiedzie się.

* W środowisku globalnie rozproszonej bazy danych istnieje bezpośrednia relacja między poziomem spójności a trwałością danych w przypadku 3000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 Podczas opracowywania planu ciągłości działania należy zrozumieć maksymalny dopuszczalny czas, po upływie których aplikacja zostanie w pełni odzyskana po zdarzeniu zakłócania pracy. Czas wymagany do pełnego odzyskania aplikacji jest znany jako cel czasu odzyskiwania (RTO). Należy również zrozumieć maksymalny okres ostatnich aktualizacji danych, które aplikacja może tolerować utratę podczas odzyskiwania po zdarzeniu zakłócanym. Okres aktualizacji, którego utrata może być tolerowana, jest określany jako cel punktu odzyskiwania (RPO, recovery point objective). Aby wyświetlić RPO i RTO for Azure Cosmos DB, zobacz [Consistency levels and data durability](./consistency-levels.md#rto) (Poziomy spójności i trwałość danych)

## <a name="what-to-expect-during-a-cosmos-db-region-outage"></a>Czego można oczekiwać podczas Cosmos DB regionu

W przypadku kont w jednym regionie klienci będą doświadczać utraty dostępności odczytu i zapisu.

W zależności od poniższej tabeli na kontach w wielu regionach będą wystąpić różne zachowania.

| Regiony zapisu | Automatyczne przełączanie w tryb failover | Czego oczekiwać | Co należy zrobić |
| -- | -- | -- | -- |
| Pojedynczy region zapisu | Niewłączone | W przypadku outage w regionie odczytu wszyscy klienci będą przekierowywani do innych regionów. Brak utraty dostępności odczytu lub zapisu. Brak utraty danych. <p/> W przypadku 30. 000 000 000 000 000 000 000 000 000 000 000 000 0 Jeśli nie wybrano silnego poziomu spójności, niektóre dane mogą nie zostać zreplikowane do pozostałych aktywnych regionów. Zależy to od poziomu selektego wybranego zgodnie z opisem w [tej sekcji](consistency-levels.md#rto). W przypadku trwałej utraty danych w regionie, którego dotyczy problem, mogą zostać utracone niereplikowane dane. <p/> Cosmos DB automatycznie przywróci dostępność zapisu po zakończeniu 30. | Podczas 3.00 upewnij się, że w pozostałych regionach jest wystarczająca aprowizowana ilość zaaprowizowanych procesorów, aby obsługiwać ruch odczytu. <p/> Nie *wyzwalaj* ręcznego trybu failover podczas awarii, ponieważ nie powiedzie się. <p/> Gdy przejdą 3000, odpowiednio dostosuj aprowizowane ponownie aprowizowane procesory. |
| Pojedynczy region zapisu | Enabled (Włączony) | W przypadku outage w regionie odczytu wszyscy klienci będą przekierowywani do innych regionów. Brak utraty dostępności odczytu lub zapisu. Brak utraty danych. <p/> W przypadku Cosmos DB 3000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 Jeśli nie wybrano silnego poziomu spójności, niektóre dane mogą nie zostać zreplikowane do pozostałych aktywnych regionów. Zależy to od poziomu selektego wybranego zgodnie z opisem w [tej sekcji](consistency-levels.md#rto). W przypadku trwałej utraty danych w regionie, którego dotyczy problem, niereplikowane dane mogą zostać utracone. | Podczas 3.00 upewnij się, że w pozostałych regionach jest wystarczająca aprowizowana ilość zaaprowizowanych procesorów, aby obsługiwać ruch odczytu. <p/> Nie *wyzwalaj* ręcznego trybu failover podczas awarii, ponieważ nie powiedzie się. <p/> Gdy przejdą przeskoki, możesz przenieść region zapisu z powrotem do oryginalnego regionu i ponownie dostosować aprowizowane procesory USA zgodnie z potrzebami. Konta korzystające z interfejsów API SQL mogą również odzyskać niereplikowane dane w regionie, który zakończył się niepowodzeniem, ze źródła [konfliktów.](how-to-manage-conflicts.md#read-from-conflict-feed) |
| Wiele regionów zapisu | Nie dotyczy | Brak utraty dostępności odczytu lub zapisu. <p/> Ostatnio zaktualizowane dane w regionie, który zakończył się niepowodzeniem, mogą być niekwałe w pozostałych aktywnych regionach. Ostateczna, spójny prefiks i poziomy spójności sesji gwarantują nieaktualność <15 minut. Powiązana nieaktualność gwarantuje mniej niż K aktualizacji lub T sekund, w zależności od konfiguracji. W przypadku trwałej utraty danych w regionie, którego dotyczy problem, mogą zostać utracone niereplikowane dane. | Podczas 3.00 upewnij się, że w pozostałych regionach jest wystarczająca ilość aprowowanych procesorów w celu obsługi dodatkowego ruchu. <p/> Gdy przejdą jak najszybciej, można ponownie dostosować aprowizowane zaaprowizowane procesory. Jeśli to możliwe, usługa Cosmos DB automatycznie odzyska niereplikowane dane w regionie, w przypadku których występuje błąd, korzystając ze skonfigurowanej metody rozwiązywania konfliktów dla kont interfejsu API SQL, a ostatni zapis wygrywa dla kont korzystających z innych interfejsów API. |

## <a name="next-steps"></a>Następne kroki

Następnie możesz przeczytać następujące artykuły:

* [Kompromisy w zakresie dostępności i wydajności dla różnych poziomów spójności](./consistency-levels.md)

* [Globalne skalowanie aprowizowanej przepływności](./request-units.md)

* [Dystrybucja globalna — szczegóły działania](global-dist-under-the-hood.md)

* [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md)

* [Jak skonfigurować konto usługi Cosmos w wielu regionach zapisu](how-to-multi-master.md)

* [Zachowanie zestawu SDK w środowiskach z wieloma regionami](troubleshoot-sdk-availability.md)