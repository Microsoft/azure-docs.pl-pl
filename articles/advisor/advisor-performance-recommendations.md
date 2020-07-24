---
title: Poprawianie wydajności aplikacji platformy Azure za pomocą usługi Advisor
description: Użyj zaleceń dotyczących wydajności w Azure Advisor, aby zwiększyć szybkość i czas odpowiedzi aplikacji o krytycznym znaczeniu dla firmy.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 7ecd6a45dc255f4748ed5074a3adb3d948f4122e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87057568"
---
# <a name="improve-the-performance-of-azure-applications-by-using-azure-advisor"></a>Poprawianie wydajności aplikacji platformy Azure za pomocą Azure Advisor

Zalecenia dotyczące wydajności w Azure Advisor mogą pomóc zwiększyć szybkość i czas odpowiedzi aplikacji o krytycznym znaczeniu dla firmy. Zalecenia dotyczące wydajności można uzyskać od klasyfikatora na karcie **wydajność** na pulpicie nawigacyjnym usługi Advisor.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Skrócenie czasu wygaśnięcia systemu DNS w profilu Traffic Manager w celu szybszego przełączenia w tryb failover pod kątem prawidłowych punktów końcowych

Możesz użyć [ustawień czasu wygaśnięcia (TTL)](../traffic-manager/traffic-manager-performance-considerations.md) w profilu usługi Azure Traffic Manager, aby określić, jak szybko przełączyć punkty końcowe, jeśli dany punkt końcowy przestanie odpowiadać na zapytania. W przypadku zmniejszenia wartości czasu wygaśnięcia klienci będą kierowani do szybszego działania punktów końcowych.

Azure Advisor identyfikuje Traffic Manager profile, dla których skonfigurowano dłuższy czas TTL. Zaleca się skonfigurowanie czasu wygaśnięcia na 20 sekund lub 60 sekund, w zależności od tego, czy profil jest skonfigurowany do [szybkie przejście w tryb failover](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-by-using-sql-database-advisor"></a>Poprawianie wydajności bazy danych za pomocą SQL Database Advisor

Azure Advisor zapewnia spójny, skonsolidowany widok zaleceń dla wszystkich zasobów platformy Azure. Integruje się z SQL Database Advisor, aby uzyskać zalecenia dotyczące poprawy wydajności baz danych.SQL Database Advisor ocenia wydajność baz danych, analizując historię użycia. Następnie oferuje rekomendacje, które najlepiej nadają się do uruchamiania typowego obciążenia bazy danych.

> [!NOTE]
> Zanim będzie można uzyskać zalecenia, baza danych musi być używana przez około tydzień, a w tym tygodniu musi być spójne działanie. SQL Database Advisor można łatwo zoptymalizować dla spójnych wzorców zapytań niż w przypadku losowych obciążeń aktywności.

Aby uzyskać więcej informacji, zobacz [SQL Database Advisor](../azure-sql/database/database-advisor-implement-performance-recommendations.md).

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>Uaktualnij bibliotekę klienta usługi Storage do najnowszej wersji, aby uzyskać lepszą niezawodność i wydajność

Najnowsza wersja zestawu SDK biblioteki klienta usługi Storage zawiera poprawki do problemów raportowanych przez klientów i aktywnie identyfikowanych przez nasz proces pytań i odpowiedzi. Najnowsza wersja zapewnia również optymalizację niezawodności i wydajności wraz z nowymi funkcjami, które mogą ulepszyć ogólne środowisko korzystania z usługi Azure Storage. Doradca zawiera zalecenia i kroki wymagane do uaktualnienia do najnowszej wersji zestawu SDK, jeśli używana jest nieodświeżona wersja. Zalecenia dotyczą obsługiwanych języków: C++ i .NET.

## <a name="improve-app-service-performance-and-reliability"></a>Zwiększ wydajność i niezawodność App Service

Azure Advisor integruje zalecenia dotyczące ulepszania środowiska App Service i wykrywania odpowiednich możliwości platformy. Przykłady App Service zalecenia:
* Wykrywanie wystąpień, w których zasoby pamięci lub procesora są wyczerpane przez środowiska uruchomieniowe aplikacji, z opcjami ograniczenia.
* Wykrywanie wystąpień, gdzie Lokalizowanie zasobów, takich jak aplikacje sieci Web i bazy danych, może zwiększyć wydajność i obniżyć koszty.

Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące Azure App Service](../app-service/app-service-best-practices.md).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Używanie dysków zarządzanych do zapobiegania ograniczaniu wydajności dysków we/wy

Klasyfikator identyfikuje maszyny wirtualne należące do konta magazynu, które osiągnęło swój obiekt docelowy skalowalności. Ten stan sprawia, że te maszyny wirtualne są podatne na ograniczenie we/wy. Program Advisor zaleca użycie dysków zarządzanych, aby zapobiec obniżeniu wydajności.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Zwiększenie wydajności i niezawodności dysków maszyny wirtualnej za pomocą Premium Storage

Program Advisor identyfikuje maszyny wirtualne z dyskami standardowymi, które mają dużą liczbę transakcji na koncie magazynu. Zalecamy uaktualnienie do dysków w warstwie Premium. 

Usługa Azure Premium Storage zapewnia obsługę dysków o wysokiej wydajności i małych opóźnieniach dla maszyn wirtualnych, na których działają duże obciążenia we/wy. Dyski maszyny wirtualnej korzystające z kont Premium Storage przechowują dane na dyskach półprzewodnikowych (dysków SSD). Aby uzyskać najlepszą wydajność aplikacji, zalecamy Migrowanie wszystkich dysków maszyn wirtualnych wymagających dużej liczby operacji we/wy Premium Storage.

## <a name="remove-data-skew-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Usuń pochylenie danych w tabelach SQL Data Warehouse, aby zwiększyć wydajność zapytań

Pochylenie danych może spowodować niepotrzebne przemieszczenie danych lub wąskie gardła zasobów podczas uruchamiania obciążenia. Program Advisor wykrywa pochylenie danych dystrybucji większe niż 15%. Zalecamy ponowną dystrybucję danych i ponowne odwiedzenie wybranych kluczy dystrybucji tabeli. Aby dowiedzieć się więcej na temat identyfikowania i usuwania pochylenia, zobacz [Rozwiązywanie problemów](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-in-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Utwórz lub zaktualizuj nieaktualne statystyki tabeli w tabelach SQL Data Warehouse, aby zwiększyć wydajność zapytań

Program Advisor identyfikuje tabele, które nie mają aktualnych [statystyk tabeli](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-statistics.md) i zaleca Tworzenie lub aktualizowanie statystyk. Optymalizator zapytań w Azure SQL Data Warehouse używa aktualnych statystyk, aby oszacować Kardynalność lub liczbę wierszy w wynikach zapytania. Te oszacowania umożliwiają Optymalizatorowi zapytań utworzenie planu zapytania w celu zapewnienia najszybszej wydajności.

## <a name="improve-mysql-connection-management"></a>Ulepszanie zarządzania połączeniami z serwerem MySQL

Analiza klasyfikatora może wskazywać, że aplikacja łącząca się z serwerem MySQL może nie zarządzać połączeniami efektywnie. Ten stan może prowadzić do niepotrzebnego zużycia zasobów i całkowitego opóźnienia aplikacji. Aby usprawnić zarządzanie połączeniami, zalecamy zmniejszenie liczby połączeń krótkoterminowych i wyeliminowanie niepotrzebnych połączeń bezczynnych. Te ulepszenia można wprowadzić, konfigurując pulę połączeń po stronie serwera, na przykład ProxySQL.


## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Skalowanie w górę w celu zoptymalizowania użycia pamięci podręcznej w tabelach SQL Data Warehouse, aby zwiększyć wydajność zapytań

Azure Advisor wykrywa, czy tabele SQL Data Warehouse mają dużą wartość procentową użycia pamięci podręcznej oraz niską wartość procentową trafień. Ten stan wskazuje duże wykluczenia pamięci podręcznej, co może wpłynąć na wydajność wystąpienia SQL Data Warehouse. Doradca zaleca skalowanie w górę wystąpienia SQL Data Warehouse, aby zapewnić przydzielenie wystarczającej pojemności pamięci podręcznej dla obciążenia.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Konwertowanie tabel SQL Data Warehouse na zreplikowane tabele w celu zwiększenia wydajności zapytań

Klasyfikator identyfikuje tabele, które nie są zreplikowane, ale byłyby korzystne dla konwersji. Sugeruje to przekonwertowanie tych tabel. Zalecenia są oparte na:
- Rozmiar zreplikowanej tabeli. 
- Liczba kolumn 
- Typ dystrybucji tabeli. 
- Liczba partycji w tabeli SQL Data Warehouse. 

Dodatkowe algorytmy heurystyczne mogą być podane w zaleceniach dotyczących kontekstu. Aby dowiedzieć się więcej na temat sposobu ustalenia tego zalecenia, zobacz [SQL Data Warehouse zalecenia](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-concept-recommendations.md#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-the-latest-azure-features"></a>Migrowanie konta magazynu w celu Azure Resource Manager w celu uzyskania najnowszych funkcji platformy Azure

Migruj model wdrażania konta magazynu, aby Azure Resource Manager w celu skorzystania z zalet:
- Wdrożenia szablonów.
- Dodatkowe opcje zabezpieczeń.
- Możliwość uaktualnienia do konta GPv2, dzięki czemu można korzystać z najnowszych funkcji usługi Azure Storage. 

Doradca identyfikuje wszystkie autonomiczne konta magazynu, które korzystają z klasycznego modelu wdrażania, i zaleca Migrowanie do modelu wdrażania Menedżer zasobów.

> [!NOTE]
> Alerty klasyczne w Azure Monitor zostały wycofane w sierpniu 2019. Zalecamy uaktualnienie klasycznego konta magazynu w celu użycia Menedżer zasobów, aby zachować funkcje alertów na nowej platformie. Aby uzyskać więcej informacji, zobacz [klasyczne alerty](../azure-monitor/platform/monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)dotyczące wycofywania.

## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Zaprojektuj konta magazynu, aby zapobiec osiągnięciu maksymalnego limitu subskrypcji

Region świadczenia usługi Azure obsługuje maksymalnie 250 kont magazynu na subskrypcję. Po osiągnięciu tego limitu nie będzie można tworzyć kont magazynu w ramach tej kombinacji regionu/subskrypcji. Doradca sprawdza subskrypcje i oferuje zalecenia dotyczące projektowania w poszukiwaniu mniejszej liczby kont magazynu dla każdej subskrypcji/regionu, która jest bliska osiągnięcia maksymalnego limitu.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-p2s-use"></a>Rozważ zwiększenie rozmiaru jednostki SKU VPN Gateway, aby rozwiązać P2S użycie

Każda jednostka SKU platformy Azure VPN Gateway może obsługiwać tylko określoną liczbę współbieżnych połączeń P2S. Jeśli liczba połączeń zbliża się do limitu bramy, kolejne próby połączenia mogą zakończyć się niepowodzeniem. Jeśli zwiększy się rozmiar bramy, będzie można obsługiwać bardziej współbieżnych użytkowników P2S. Doradca zawiera zalecenia i instrukcje zwiększania rozmiaru bramy.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-cpu"></a>Rozważ zwiększenie rozmiaru jednostki SKU VPN Gateway, aby rozwiązać wysoki poziom procesora

W przypadku dużego obciążenia ruchem bramy sieci VPN może porzucać pakiety z powodu dużego procesora CPU. Rozważ uaktualnienie VPN Gateway jednostki SKU. Zwiększenie rozmiaru bramy sieci VPN zapewnia, że połączenia nie będą porzucane z powodu dużego procesora. Usługa Advisor oferuje zalecenia dotyczące aktywnego rozwiązania tego problemu. 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>Zwiększanie rozmiaru partii podczas ładowania w celu zmaksymalizowania przepływności ładowania, kompresji danych i wydajności wykonywania zapytań

Doradca wykrywa, czy można zwiększyć wydajność ładowania i przepływność przez zwiększenie rozmiaru partii podczas ładowania do bazy danych. Można rozważyć użycie instrukcji COPY. Jeśli nie można użyć instrukcji COPY, należy rozważyć zwiększenie rozmiaru partii podczas korzystania z narzędzi do ładowania, takich jak interfejs API SQLBulkCopy lub BCP. Dobrą ogólną regułą jest użycie rozmiaru partii z przedziału od 100 000 do 1 000 000 wierszy. Zwiększenie rozmiaru partii spowoduje zwiększenie przepływności ładowania, kompresji danych i wydajności zapytań.

## <a name="co-locate-the-storage-account-in-the-same-region-to-minimize-latency-when-loading"></a>Lokalizowanie konta magazynu w tym samym regionie w celu zminimalizowania opóźnień podczas ładowania

Doradca wykrywa, czy ładujesz z regionu innego niż Pula SQL. Rozważ załadowanie z konta magazynu znajdującego się w tym samym regionie co Pula SQL, aby zminimalizować opóźnienie podczas ładowania danych. Ta zmiana pomoże zminimalizować opóźnienia i zwiększyć wydajność ładowania.

## <a name="use-a-supported-kubernetes-version"></a>Użyj obsługiwanej wersji Kubernetes

Klasyfikator wykrywa nieobsługiwane wersje programu Kubernetes.

## <a name="optimize-the-performance-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers"></a>Optymalizowanie wydajności serwerów Azure Database for MySQL, Azure Database for PostgreSQL i Azure Database for MariaDB

### <a name="fix-the-cpu-pressure-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-with-cpu-bottlenecks"></a>Naprawianie Azure Database for MySQL, Azure Database for PostgreSQL i serwerów Azure Database for MariaDB z wąskimi gardłami procesora CPU
Wysokie wykorzystanie procesora CPU w dłuższym okresie może spowodować powolne działanie zapytań dla obciążenia. Zwiększenie rozmiaru procesora pomoże zoptymalizować środowisko uruchomieniowe zapytań bazy danych i poprawić ogólną wydajność. Klasyfikator identyfikuje serwery o wysokim poziomie użycia procesora CPU, które mogą uruchamiać obciążenia z ograniczeniami procesora i zalecać skalowanie obliczeń.

### <a name="reduce-memory-constraints-on-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Ogranicz ograniczenia pamięci na serwerach Azure Database for MySQL, Azure Database for PostgreSQL i Azure Database for MariaDB lub przejdź do jednostki SKU zoptymalizowanej pod kątem pamięci
Współczynnik trafień w pamięci podręcznej może spowodować wolniejszą wydajność zapytań i większe liczby operacji we/wy na sekundę. Ten stan może być spowodowany przez zły plan zapytania lub obciążenie czasochłonne w pamięci. Ustalenie planu zapytania lub [zwiększenie ilości pamięci](../postgresql/concepts-pricing-tiers.md) Azure Database for PostgreSQL, Azure Database for MySQL lub Azure Database for MariaDB Server pomoże zoptymalizować wykonywanie obciążenia bazy danych. Azure Advisor identyfikuje serwery, na które mają wpływ te duże zmiany puli buforów. Zalecane jest wykonanie jednej z następujących akcji: 
- Popraw plan zapytania
- Przejdź do jednostki SKU, która ma więcej pamięci 
- Zwiększ rozmiar magazynu, aby uzyskać więcej operacji we/wy na sekundę.

### <a name="use-an-azure-database-for-mysql-or-azure-database-for-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Użyj Azure Database for MySQL lub Azure Database for PostgreSQL odczytu repliki, aby skalować w poziomie odczyty dla obciążeń intensywnie korzystających z odczytu
Doradca korzysta z algorytmów heurystycznych opartych na obciążeniu, takich jak stosunek odczytów w ciągu ostatnich siedmiu dni, aby identyfikować obciążenia intensywnie korzystające z odczytu. Zasób Azure Database for PostgreSQL lub Azure Database for MySQL o wysokim współczynniku odczytu/zapisu może spowodować rywalizację o procesor lub pamięć i prowadzić do powolnej wydajności zapytań. Dodanie [repliki](../postgresql/howto-read-replicas-portal.md) ułatwia skalowanie odczytów do serwera repliki i zapobiega ograniczeń procesora CPU lub pamięci na serwerze podstawowym. Klasyfikator identyfikuje serwery z obciążeniami intensywnie korzystających z odczytu i zaleca się dodanie [repliki odczytu](../postgresql/concepts-read-replicas.md)   w celu odciążenia niektórych obciążeń odczytu.


### <a name="scale-your-azure-database-for-mysql-azure-database-for-postgresql-or-azure-database-for-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Skalowanie serwera Azure Database for MySQL, Azure Database for PostgreSQL lub Azure Database for MariaDB do wyższej jednostki SKU w celu uniknięcia ograniczeń połączenia
Każde nowe połączenie z serwerem bazy danych zajmuje pamięć. Obniżenie wydajności serwera bazy danych, jeśli połączenia z serwerem kończą się niepowodzeniem z powodu [górnego limitu](../postgresql/concepts-limits.md) w pamięci. Azure Advisor identyfikuje serwery z wieloma niepowodzeńmi połączeń. Zalecamy uaktualnienie limitów połączeń serwera w celu zapewnienia większej ilości pamięci dla serwera, wykonując jedną z następujących czynności:
- Skalowanie zasobów obliczeniowych w górę. 
- Użyj zoptymalizowanych pod kątem pamięci jednostek SKU, które mają więcej obliczeń na rdzeń.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Skalowanie pamięci podręcznej do innego rozmiaru lub jednostki SKU w celu zwiększenia wydajności pamięci podręcznej i aplikacji

Wystąpienia pamięci podręcznej działają najlepiej, gdy nie są uruchamiane w ramach dużej ilości pamięci, dużego obciążenia serwera lub dużej przepustowości sieci. Te warunki mogą spowodować, że nie odpowiadają, utracie danych lub staną się niedostępne. Usługi Advisor identyfikują wystąpienia pamięci podręcznej w tych warunkach. Zalecane jest wykonanie jednej z następujących akcji:
- Zastosuj najlepsze rozwiązania, aby zmniejszyć wykorzystanie pamięci, obciążenie serwera lub przepustowość sieci.
- Skalowanie do innego rozmiaru lub jednostki SKU o większej pojemności.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Dodawanie regionów z ruchem do konta Azure Cosmos DB

Program Advisor wykrywa konta Azure Cosmos DB, które mają ruch z regionu, który nie jest aktualnie skonfigurowany. Zalecamy dodanie tego regionu. Poprawia to opóźnienia dla żądań pochodzących z tego regionu i zapewnia dostępność w przypadku awarii regionu. [Dowiedz się więcej o globalnej dystrybucji danych za pomocą Azure Cosmos DB.](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-by-using-custom-included-or-excluded-paths"></a>Konfigurowanie zasad indeksowania Azure Cosmos DB przy użyciu niestandardowych ścieżek uwzględnionych lub wykluczonych

Doradca identyfikuje Azure Cosmos DB kontenery, które używają domyślnych zasad indeksowania, ale mogą korzystać z niestandardowych zasad indeksowania. To oznaczenie jest oparte na wzorcu obciążenia. Domyślne zasady indeksowania indeksów wszystkie właściwości. Niestandardowe zasady indeksowania z jawnymi dołączonymi lub wykluczonymi ścieżkami używanymi w filtrach zapytań mogą zmniejszyć liczbę jednostek ru i miejsce do magazynowania używane do indeksowania. [Dowiedz się więcej o modyfikowaniu zasad indeksu.](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="set-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Ustaw rozmiar strony zapytania Azure Cosmos DB (MaxItemCount) na-1 

Azure Advisor identyfikuje kontenery Azure Cosmos DB używające rozmiaru strony zapytania 100. Zalecane jest użycie rozmiaru strony-1 w celu przyspieszenia skanowania. [Dowiedz się więcej o MaxItemCount.](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Jak uzyskać dostęp do zaleceń dotyczących wydajności w usłudze Advisor

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie otwórz program [Advisor](https://aka.ms/azureadvisordashboard).

2.  Na pulpicie nawigacyjnym usługi Advisor wybierz kartę **wydajność** .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zaleceń klasyfikatora, zobacz:

* [Wprowadzenie do usługi Advisor](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Zalecenia usługi Advisor dotyczące kosztów](advisor-cost-recommendations.md)
* [Zalecenia dotyczące niezawodności usługi Advisor](advisor-high-availability-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej klasyfikatora](advisor-operational-excellence-recommendations.md)
* [Interfejs API REST usługi Advisor](/rest/api/advisor/)
