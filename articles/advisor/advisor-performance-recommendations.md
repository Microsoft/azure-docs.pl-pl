---
title: Poprawianie wydajności aplikacji platformy Azure za pomocą Azure Advisor
description: Użyj klasyfikatora, aby zoptymalizować wydajność wdrożeń platformy Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 230466c7c0e8de2681737bbf9d74341dea7f7b8f
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84196413"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Poprawianie wydajności aplikacji platformy Azure za pomocą Azure Advisor

Azure Advisor zalecenia dotyczące wydajności pomagają zwiększyć szybkość i czas odpowiedzi aplikacji o krytycznym znaczeniu dla firmy. Zalecenia dotyczące wydajności można uzyskać od klasyfikatora na karcie **wydajność** na pulpicie nawigacyjnym usługi Advisor.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Skracanie czasu usługi DNS na żywo w profilu Traffic Manager, aby szybciej pracować w trybie failover z prawidłowymi punktami końcowymi

[Ustawienia czasu wygaśnięcia (TTL)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) w profilu Traffic Manager umożliwiają określenie, jak szybko przełączać punkty końcowe, jeśli dany punkt końcowy przestanie odpowiadać na zapytania. Zmniejszenie wartości czasu wygaśnięcia oznacza, że klienci będą kierowani do szybszego działania punktów końcowych.

Azure Advisor identyfikuje profile Traffic Manager o dłuższym czasie TTL skonfigurowanym i zaleca skonfigurowanie czasu wygaśnięcia na 20 sekund lub 60 sekund w zależności od tego, czy profil jest skonfigurowany do [szybkie przejście w tryb failover](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Zwiększanie wydajności bazy danych przy użyciu funkcji SQL DB Advisor

Usługa Advisor zapewnia spójny, skonsolidowany widok zaleceń dotyczących wszystkich zasobów platformy Azure. Integruje się z SQL Database Advisor, aby uzyskać zalecenia dotyczące poprawy wydajności bazy danych.SQL Database Advisor ocenia wydajność baz danych, analizując historię użycia. Następnie oferuje rekomendacje, które najlepiej nadają się do uruchamiania typowego obciążenia bazy danych.

> [!NOTE]
> Aby uzyskać zalecenia, baza danych musi mieć co tydzień użytkowania i w tym tygodniu musi mieć pewne spójne działanie. SQL Database Advisor można łatwo zoptymalizować dla spójnych wzorców zapytań niż w przypadku losowych obciążeń aktywności.

Aby uzyskać więcej informacji na temat SQL Database Advisor, zobacz [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>Uaktualnij bibliotekę klienta usługi Storage do najnowszej wersji, aby uzyskać większą niezawodność i wydajność

Najnowsza wersja biblioteki klienta usługi Storage i zestawu SDK zawiera poprawki do problemów raportowanych przez klientów i proaktywnie identyfikowanych w naszym procesie pytań i odpowiedzi. Najnowsza wersja zawiera również optymalizację niezawodności i wydajności, a także nowe funkcje, które mogą ulepszyć ogólne środowisko pracy z usługą Azure Storage. Doradca zawiera zalecenia i kroki umożliwiające uaktualnienie do najnowszej wersji zestawu SDK, jeśli używasz starej wersji. Zalecenia dotyczą języków obsługiwanych w języku C++ i .NET.

## <a name="improve-app-service-performance-and-reliability"></a>Zwiększ wydajność i niezawodność App Service

Azure Advisor integruje zalecenia dotyczące najlepszych rozwiązań w zakresie ulepszania App Services środowiska i wykrywania odpowiednich możliwości platformy. Przykłady App Services zalecenia:
* Wykrywanie wystąpień, w których zasoby pamięci lub procesora są wyczerpane przez środowisko uruchomieniowe aplikacji z opcjami ograniczenia.
* Wykrywanie wystąpień, gdzie kolokacja zasobów, takich jak aplikacje sieci Web i bazy danych, może zwiększyć wydajność i obniżyć koszty.

Aby uzyskać więcej informacji na temat App Services zaleceń, zobacz [najlepsze rozwiązania dotyczące Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Użyj Managed Disks, aby zapobiec ograniczaniu wydajności dysku we/wy

Program Advisor zidentyfikuje maszyny wirtualne należące do konta magazynu, które osiąga swój obiekt docelowy skalowalności. Ten stan sprawia, że te maszyny wirtualne są podatne na ograniczenie we/wy. Program Advisor zaleca użycie Managed Disks, aby zapobiec obniżeniu wydajności.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Zwiększenie wydajności i niezawodności dysków maszyny wirtualnej za pomocą Premium Storage

Usługa Advisor identyfikuje maszyny wirtualne z dyskami standardowymi, które mają dużą liczbę transakcji na koncie magazynu i zaleca uaktualnianie do dysków w warstwie Premium. 

Usługa Azure Premium Storage zapewnia obsługę dysków o wysokiej wydajności i małych opóźnieniach dla maszyn wirtualnych, na których działają duże obciążenia we/wy. Dyski maszyn wirtualnych korzystające z kont magazynu w warstwie Premium przechowują dane na dyskach półprzewodnikowych (dysków SSD). W celu uzyskania najlepszej wydajności aplikacji zalecamy przeprowadzenie migracji wszystkich dysków maszyn wirtualnych wymagających dużej liczby operacji we/wy na sekundę w warstwie Premium.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Usuń pochylenie danych z tabeli w usłudze SQL Data Warehouse, aby zwiększyć wydajność zapytań

Pochylenie danych może spowodować niepotrzebne przemieszczenie danych lub wąskie gardła zasobów podczas uruchamiania obciążenia. Program Advisor wykryje pochylenie danych dystrybucji większe niż 15% i zaleca ponowne rozproszenie danych i ponowne odwiedzenie wybranych opcji klucza dystrybucji tabeli. Aby dowiedzieć się więcej na temat identyfikowania i usuwania pochylenia, zobacz [Rozwiązywanie problemów](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Utwórz lub zaktualizuj nieaktualne statystyki tabeli w tabeli magazynu danych SQL, aby zwiększyć wydajność zapytań

Klasyfikator identyfikuje tabele, które nie mają aktualnych [statystyk tabeli](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) i zaleca Tworzenie lub aktualizowanie statystyk tabeli. Optymalizator zapytań w usłudze SQL Data Warehouse korzysta z aktualnych wartości statycznych, aby oszacować Kardynalność lub liczbę wierszy w wyniku zapytania, dzięki czemu optymalizator zapytań może utworzyć plan zapytania o wysokiej jakości w celu uzyskania najszybszej wydajności.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Skalowanie w górę w celu zoptymalizowania użycia pamięci podręcznej w tabelach SQL Data Warehouse, aby zwiększyć wydajność zapytań

Azure Advisor wykrywa, czy SQL Data Warehouse ma dużą wartość procentową wykorzystania pamięci podręcznej oraz niską wartość procentową trafienia. Ten stan wskazuje duże wykluczenia pamięci podręcznej, co może mieć wpływ na wydajność SQL Data Warehouse. W usłudze Advisor zawarto skalowanie w górę SQL Data Warehouse, aby zapewnić przydzielenie wystarczającej pojemności pamięci podręcznej dla obciążenia.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Konwertowanie tabel SQL Data Warehouse na zreplikowane tabele w celu zwiększenia wydajności zapytań

Klasyfikator identyfikuje tabele, które nie są zreplikowane, ale byłyby korzystne dzięki konwersji i sugestii konwersji tych tabel. Zalecenia są oparte na rozmiarze zreplikowanej tabeli, liczbie kolumn, typie dystrybucji tabeli i liczbie partycji tabeli SQL Data Warehouse. Dodatkowe algorytmy heurystyczne mogą być podane w zaleceniach dotyczących kontekstu. Aby dowiedzieć się więcej na temat sposobu ustalenia tego zalecenia, zobacz [SQL Data Warehouse zalecenia](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Przeprowadź migrację konta magazynu do Azure Resource Manager, aby uzyskać wszystkie najnowsze funkcje platformy Azure

Migruj model wdrażania konta magazynu do Azure Resource Manager (Menedżer zasobów), aby skorzystać z wdrożeń szablonów, dodatkowych opcji zabezpieczeń i możliwości uaktualnienia do konta GPv2 w celu użycia najnowszych funkcji usługi Azure Storage. Program Advisor zidentyfikuje wszystkie autonomiczne konta magazynu korzystające z klasycznego modelu wdrażania i zaleca migrację do modelu wdrażania Menedżer zasobów.

> [!NOTE]
> Alerty klasyczne w Azure Monitor zostały wycofane w sierpniu 2019. Zalecamy uaktualnienie klasycznego konta magazynu w celu użycia Menedżer zasobów, aby zachować funkcje alertów na nowej platformie. Aby uzyskać więcej informacji, zobacz [klasyczne alerty](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform)dotyczące wycofywania.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Zaprojektuj konta magazynu, aby zapobiec osiągnięciu maksymalnego limitu subskrypcji

Region świadczenia usługi Azure może obsłużyć maksymalnie 250 kont magazynu na subskrypcję. Po osiągnięciu limitu nie będzie można utworzyć więcej kont magazynu w ramach tej kombinacji regionu/subskrypcji. Doradca sprawdzi Twoje subskrypcje i zalecenia dotyczące powierzchni, aby zaprojektować mniejszą liczbę kont magazynu dla dowolnego miejsca, które zbliżają się do maksymalnego limitu.

## <a name="consider-increasing-the-size-of-your-vnet-gateway-sku-to-adress-high-p2s-use"></a>Rozważ zwiększenie rozmiaru jednostki SKU bramy sieci wirtualnej w celu uzyskania P2Sego użycia

Każda jednostka SKU bramy może obsługiwać wyłącznie określoną liczbę współbieżnych połączeń typu punkt-lokacja. Jeśli liczba połączeń zbliża się do limitu bramy, to dodatkowe próby połączenia mogą zakończyć się niepowodzeniem. Zwiększenie rozmiaru bramy umożliwi obsługę większej liczby współbieżnych użytkowników P2S. Doradca zawiera rekomendacje i kroki, które należy wykonać, aby to zrobić.

## <a name="consider-increasing-the-size-of-your-vnet-gateway-sku-to-address-high-cpu"></a>Rozważ zwiększenie rozmiaru jednostki SKU bramy sieci wirtualnej, aby rozwiązać problem intensywnego użycia procesora

W sytuacji dużego obciążenia ruchem brama sieci VPN może porzucać pakiety z uwagi na intensywne użycie procesora. Należy rozważyć uaktualnienie VPN Gateway jednostki SKU, ponieważ sieć VPN była stale uruchomiona w systemie. Zwiększenie rozmiaru bramy sieci VPN zapewni, że połączenia nie zostaną usunięte ze względu na wysoki procesor CPU. Zalecenie provdes doradcy, aby rozwiązać ten problem. 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>Zwiększanie rozmiaru partii podczas ładowania w celu zmaksymalizowania przepływności ładowania, kompresji danych i wydajności wykonywania zapytań

Klasyfikator może wykryć, że można zwiększyć wydajność ładowania i przepływność przez zwiększenie rozmiaru partii podczas ładowania do bazy danych. Można rozważyć użycie instrukcji COPY. Jeśli nie możesz użyć instrukcji COPY, rozważ zwiększenie rozmiaru partii podczas korzystania z narzędzi ładowania, takich jak interfejs API SQLBulkCopy lub narzędzie BCP — jako ogólną regułę można przyjąć rozmiar partii między 100 000 a 1 mln wierszy. Spowoduje to zwiększenie przepływności obciążenia, kompresji danych i wydajności zapytań.

## <a name="co-locate-the-storage-account-within-the-same-region-to-minimize-latency-when-loading"></a>Umieszczanie konta magazynu w tym samym regionie w celu zminimalizowania opóźnień podczas ładowania

Klasyfikator może wykryć, że ładujesz z regionu innego niż Pula SQL. Rozważ ładowanie z konta magazynu znajdującego się w tym samym regionie co pula SQL w celu zminimalizowania opóźnień podczas ładowania danych. Pomoże to zminimalizować opóźnienia i zwiększyć wydajność ładowania.

## <a name="unsupported-kubernetes-version-is-detected"></a>Wykryto nieobsługiwaną wersję Kubernetes

Klasyfikator może wykryć, czy wykryto nieobsługiwaną wersję Kubernetes. Zalecenie pomoże zapewnić, że klaster Kubernetes będzie działać z obsługiwaną wersją.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Optymalizacja wydajności serwerów Azure MySQL, Azure PostgreSQL i Azure MariaDB 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Popraw wykorzystanie procesora CPU serwerów Azure MySQL, Azure PostgreSQL i Azure MariaDB z wąskimi gardłami procesora CPU
Bardzo wysokie wykorzystanie procesora CPU w dłuższym okresie może spowodować niską wydajność zapytań dla obciążenia. Zwiększenie rozmiaru procesora pomoże zoptymalizować środowisko uruchomieniowe zapytań bazy danych i poprawić ogólną wydajność. Azure Advisor zidentyfikuje serwery o dużym wykorzystaniu procesora CPU, które prawdopodobnie obsługują ograniczone obciążenia procesora CPU, i zaleca skalowanie obliczeń.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Ogranicz ograniczenia pamięci na serwerach Azure MySQL, Azure PostgreSQL i Azure MariaDB lub przejdź do jednostki SKU zoptymalizowanej pod kątem pamięci
Współczynnik trafień w pamięci podręcznej może spowodować wolniejszą wydajność zapytań i większe liczby operacji we/wy na sekundę. Może to być spowodowane nieprawidłowym planem zapytania lub uruchomieniem intensywnego obciążenia pamięci. Rozwiązanie planu zapytania lub [zwiększenie ilości pamięci](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) serwera bazy danych Azure Database for PostgreSQL, serwera bazy danych Azure MySQL lub serwera usługi Azure MariaDB pomoże zoptymalizować wykonywanie obciążenia bazy danych. Azure Advisor identyfikuje serwery, których dotyczy ten wysoki poziom zmian puli buforów i zaleca ustalenie planu zapytania, przechodzenie do wyższej jednostki SKU z większą ilością pamięci lub zwiększenie rozmiaru magazynu w celu uzyskania większej liczby operacji we/wy na sekundę.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Korzystanie z repliki usługi Azure MySQL lub Azure PostgreSQL do odczytu w celu skalowania odczytów dla intensywnie korzystających z odczytu obciążeń
Azure Advisor wykorzystuje algorytmy heurystyczne oparte na obciążeniu, takie jak stosunek odczytów do zapisu na serwerze w ciągu ostatnich siedmiu dni w celu zidentyfikowania obciążeń intensywnie korzystających z odczytu. Zasób usługi Azure Database for PostgreSQL lub zasób usługi Azure Database for MySQL z bardzo wysokim współczynnikiem odczytu/zapisu może spowodować, że rywalizacja procesora i/lub pamięci prowadzi do spowolnienia wydajności zapytań. Dodanie [repliki](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) ułatwi skalowanie odczytów do serwera repliki, uniemożliwiając procesor i/lub ograniczenia pamięci na serwerze podstawowym. Program Advisor zidentyfikuje serwery z takimi dużymi obciążeniami wymagającymi odczytu i zaleca się dodanie [repliki odczytu](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas)   w celu odciążenia niektórych obciążeń odczytu.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Skaluj platformę Azure MySQL, Azure PostgreSQL lub Azure MariaDB Server do wyższej jednostki SKU, aby zapobiec ograniczeniom połączeń
Każde nowe połączenie z serwerem bazy danych zajmuje trochę pamięci. Obniżenie wydajności serwera bazy danych, jeśli połączenia z serwerem kończą się niepowodzeniem z powodu [górnego limitu](https://docs.microsoft.com/azure/postgresql/concepts-limits) w pamięci. Azure Advisor będzie identyfikować serwery z wieloma błędami połączeń i zalecać uaktualnienie limitów połączeń serwera w celu zapewnienia większej ilości pamięci dla serwera przez skalowanie obliczeniowe lub użycie zoptymalizowanych pod kątem pamięci jednostek SKU, które mają więcej obliczeń na rdzeń.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Skalowanie pamięci podręcznej do innego rozmiaru lub jednostki SKU w celu zwiększenia wydajności pamięci podręcznej i aplikacji

Wystąpienia pamięci podręcznej działają najlepiej, gdy nie są uruchamiane w dużej ilości pamięci, dużym obciążeniu serwera lub dużej przepustowości sieci, co może spowodować, że nie odpowiada, utracie danych lub stanie się niedostępne. Program Advisor zidentyfikuje wystąpienia pamięci podręcznej w tych warunkach i zaleca zastosowanie najlepszych rozwiązań w celu ograniczenia wykorzystania pamięci, obciążenia serwera lub przepustowości sieci lub skalowania do innego rozmiaru lub jednostki SKU o większej pojemności.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Dodawanie regionów z ruchem do konta Azure Cosmos DB

Program Advisor wykrywa konta Azure Cosmos DB, które mają ruch z regionu, który nie jest aktualnie skonfigurowany i zalecamy dodanie tego regionu. Pozwoli to zwiększyć opóźnienia żądań pochodzących z tego regionu i zapewni dostępność w przypadku awarii regionu. [Dowiedz się więcej o globalnej dystrybucji danych za pomocą Azure Cosmos DB](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>Konfigurowanie zasad indeksowania Azure Cosmos DB przy użyciu ścieżek uwzględnionych w klientach lub wykluczonych

Azure Advisor będzie identyfikować kontenery Cosmos DB, które używają domyślnych zasad indeksowania, ale mogą korzystać z niestandardowych zasad indeksowania opartych na wzorcu obciążenia. Domyślna zasada indeksowania indeksuje wszystkie właściwości, ale przy użyciu niestandardowych zasad indeksowania z jawnymi dołączonymi lub wykluczonymi ścieżkami używanymi w filtrach zapytań można zmniejszyć jednostek ru i magazyny używane do indeksowania. [Dowiedz się więcej o modyfikowaniu zasad indeksu](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Konfigurowanie rozmiaru strony zapytania usługi Azure Cosmos DB (MaxItemCount) na wartość -1 

Azure Advisor zidentyfikuje Azure Cosmos DB kontenery używające rozmiaru strony zapytania 100 i zaleca użycie rozmiaru strony-1 w celu przyspieszenia skanowania. [Dowiedz się więcej o maksymalnej liczbie elementów](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Jak uzyskać dostęp do zaleceń dotyczących wydajności w usłudze Advisor

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie otwórz program [Advisor](https://aka.ms/azureadvisordashboard).

2.  Na pulpicie nawigacyjnym usługi Advisor kliknij kartę **wydajność** .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zaleceń klasyfikatora, zobacz:

* [Wprowadzenie do usługi Advisor](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Zalecenia dotyczące kosztów usługi Advisor](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wysokiej dostępności usługi Advisor](advisor-high-availability-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej klasyfikatora](advisor-operational-excellence-recommendations.md)
