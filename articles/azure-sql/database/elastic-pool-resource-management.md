---
title: Zarządzanie zasobami w ramach gęstych pul elastycznych
description: Zarządzanie zasobami obliczeniowymi w Azure SQL Database elastycznych pulach z wieloma bazami danych.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: sstein
ms.date: 09/16/2020
ms.openlocfilehash: 1398745a485b57c3a739c25dbca77d7b21f19758
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792161"
---
# <a name="resource-management-in-dense-elastic-pools"></a>Zarządzanie zasobami w ramach gęstych pul elastycznych
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database [Pule elastyczne](./elastic-pool-overview.md) to ekonomiczne rozwiązanie służące do zarządzania wieloma bazami danych o różnym zużyciu zasobów. Wszystkie bazy danych w elastycznej puli współdzielą te same alokacje zasobów, takie jak procesor CPU, pamięć, wątki robocze, miejsce do magazynowania, baza danych tempdb, w założeniu, że **tylko podzbiór z bazami w puli będzie używać zasobów obliczeniowych w danym momencie** . To założenie umożliwia opłacalność pul elastycznych. Zamiast płacić za wszystkie zasoby poszczególnych baz danych, klienci płacą za dużo mniejszego zestawu zasobów, współdzielonych między wszystkimi bazami danych w puli.

## <a name="resource-governance"></a>Nadzór nad zasobami

Udostępnianie zasobów wymaga od systemu dokładnego sterowania użyciem zasobów, aby zminimalizować efekt "przekształcenia", w którym baza danych o wysokim zużyciu zasobów wpływa na inne bazy danych w tej samej puli elastycznej. W tym samym czasie system musi zapewnić wystarczającą ilość zasobów dla funkcji, takich jak wysoka dostępność i odzyskiwanie po awarii (HADR cluster), kopia zapasowa i przywracanie, monitorowanie, magazyn zapytań, dostrajanie automatyczne itd., aby działać niezawodnie.

Azure SQL Database osiąga te cele przy użyciu wielu mechanizmów nadzoru zasobów, w tym [obiektów zadań](/windows/win32/procthread/job-objects) systemu Windows do zarządzania zasobami na poziomie procesu, [Menedżer zasobów serwera plików systemu Windows (Menedżer](/windows-server/storage/fsrm/fsrm-overview) zasobów usługi plików) do obsługi przydziałów magazynowania, a także zmodyfikowaną i rozszerzoną wersję [prezesa zasobów](/sql/relational-databases/resource-governor/resource-governor) SQL Server do wdrożenia zarządzania zasobami w ramach SQL Database.

Podstawowym celem projektu pul elastycznych jest ekonomiczne. Z tego powodu system umożliwia klientom tworzenie _montowanych_ pul, które są pulami o liczbie baz danych zbliżających się do maksymalnej dozwolonej, ale przy umiarkowanej alokacji zasobów obliczeniowych. Z tego samego powodu system nie rezerwuje wszystkich potencjalnie wymaganych zasobów dla swoich procesów wewnętrznych, ale umożliwia udostępnianie zasobów między procesami wewnętrznymi i obciążeniami użytkowników.

Takie podejście umożliwia klientom korzystanie z dużych pul elastycznych w celu zapewnienia odpowiedniej wydajności i oszczędności kosztów. Jeśli jednak obciążenie wielu baz danych w puli gęstej jest dostatecznie intensywne, rywalizacja o zasoby stanie się znacząca. Rywalizacja o zasoby zmniejsza wydajność obciążeń użytkowników i może mieć negatywny wpływ na procesy wewnętrzne.

> [!IMPORTANT]
> W przypadku gęstych pul z wieloma aktywnymi bazami danych może nie być możliwe zwiększenie liczby baz danych w puli do wartości maksymalnych udokumentowanych w przypadku pul elastycznych [jednostek DTU](resource-limits-dtu-elastic-pools.md) i [rdzeń wirtualny](resource-limits-vcore-elastic-pools.md) .
>
> Liczba baz danych, które mogą być umieszczone w basenie montowanym bez powodowania problemów z rywalizacją o zasoby i wydajności, zależy od liczby współbieżnych aktywnych baz danych i użycia zasobów przez obciążenia użytkowników w każdej bazie danych. Ta liczba może się zmieniać w miarę upływu czasu w miarę zmiany obciążeń użytkowników.

Gdy rywalizacja zasobów występuje w puli gęsto spakowanej, klienci mogą wybrać co najmniej jedną z następujących czynności, aby je ograniczyć:

- Dostrajaj obciążenie zapytań, aby zmniejszyć zużycie zasobów, lub rozłożyć użycie zasobów na wiele baz danych w czasie.
- Zmniejszenie gęstości puli przez przeniesienie niektórych baz danych do innej puli lub utworzenie autonomicznych baz danych.
- Skaluj pulę w górę, aby uzyskać więcej zasobów.

Aby uzyskać informacje na temat sposobu implementacji ostatnich dwóch akcji, zobacz [zalecenia dotyczące działania](#operational-recommendations) w dalszej części tego artykułu. Zmniejszenie rywalizacji o zasoby wiąże się zarówno z obciążeniami użytkowników, jak i procesami wewnętrznymi, a system umożliwia niezawodne zachowanie oczekiwanego poziomu usługi.

## <a name="monitoring-resource-consumption"></a>Monitorowanie użycia zasobów

Aby uniknąć obniżenia wydajności ze względu na rywalizację o zasoby, klienci korzystający z gęstych pul elastycznych powinni aktywnie monitorować użycie zasobów i podejmować działania w odpowiednim czasie, jeśli zwiększenie rywalizacji o zasoby zacznie wpływać na obciążenia. Ciągłe monitorowanie jest ważne, ponieważ użycie zasobów w puli zmienia się wraz z upływem czasu, ze względu na zmiany obciążeń użytkowników, zmiany woluminów i dystrybucji danych, zmiany gęstości puli i zmiany w usłudze Azure SQL Database.

Azure SQL Database zawiera kilka metryk, które są istotne dla tego typu monitorowania. Przekroczenie zalecanej wartości średniej dla każdej metryki wskazuje rywalizację zasobów w puli i powinna być rozmieszczona przy użyciu jednej z wymienionych wcześniej akcji.

|Nazwa metryki|Opis|Zalecana średnia wartość|
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|Użycie procesora CPU przez proces SQL skojarzony z elastyczną pulą, mierzony przez podstawowy system operacyjny. Dostępne w widoku [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) w każdej bazie danych, a w widoku [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) w `master` bazie danych. Ta Metryka jest również emitowana do Azure Monitor, gdzie ma [nazwę](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) `sqlserver_process_core_percent` , i może być wyświetlana w Azure Portal. Ta wartość jest taka sama dla każdej bazy danych w tej samej puli elastycznej.|Poniżej 70%. Można zaakceptować sporadyczne krótkie skoki do 90%.|
|`max_worker_percent`|Wykorzystanie [wątku roboczego]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) . Dostępne dla każdej bazy danych w puli, a także dla samej puli. Istnieją różne limity liczby wątków roboczych na poziomie bazy danych i na poziomie puli, dlatego zaleca się monitorowanie tej metryki na obu poziomach. Dostępne w widoku [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) w każdej bazie danych, a w widoku [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) w `master` bazie danych. Ta Metryka jest również emitowana do Azure Monitor, gdzie ma [nazwę](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) `workers_percent` , i może być wyświetlana w Azure Portal.|Poniżej 80%. Powoduje, że do 100% zostanie podjęta próba połączenia i niepowodzenie zapytań.|
|`avg_data_io_percent`|Użycie IOPS do odczytu i zapisu fizycznego we/wy. Dostępne dla każdej bazy danych w puli, a także dla samej puli. Istnieją różne limity liczby operacji we/wy na poziomie bazy danych i na poziomie puli, dlatego zaleca się monitorowanie tej metryki na obu poziomach. Dostępne w widoku [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) w każdej bazie danych, a w widoku [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) w `master` bazie danych. Ta Metryka jest również emitowana do Azure Monitor, gdzie ma [nazwę](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) `physical_data_read_percent` , i może być wyświetlana w Azure Portal.|Poniżej 80%. Można zaakceptować sporadyczne krótkie skoki do 100%.|
|`avg_log_write_percent`|Wykorzystanie przepływności dla operacji we/wy zapisu w dzienniku transakcji. Dostępne dla każdej bazy danych w puli, a także dla samej puli. Istnieją różne limity przepływności dziennika na poziomie bazy danych i na poziomie puli, dlatego zaleca się monitorowanie tej metryki na obu poziomach. Dostępne w widoku [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) w każdej bazie danych, a w widoku [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) w `master` bazie danych. Ta Metryka jest również emitowana do Azure Monitor, gdzie ma [nazwę](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) `log_write_percent` , i może być wyświetlana w Azure Portal. Gdy ta Metryka jest bliska 100%, wszystkie modyfikacje bazy danych (INSERT, UPDATE, DELETE, MERGE, SELECT... DO, BULK INSERT itd.) będzie wolniejsze.|Poniżej 90%. Można zaakceptować sporadyczne krótkie skoki do 100%.|
|`oom_per_second`|Częstotliwość występowania błędów poza pamięcią (OOM) w puli elastycznej, która jest wskaźnikiem wykorzystania pamięci. Dostępne w widoku [sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) . Zobacz [przykłady](#examples) przykładowego zapytania, aby obliczyć tę metrykę.|0|
|`avg_storage_percent`|Całkowite miejsce w magazynie używane przez dane we wszystkich bazach danych w puli elastycznej. Nie uwzględnia pustego miejsca w plikach bazy danych. Dostępne w widoku [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) w `master` bazie danych programu. Ta Metryka jest również emitowana do Azure Monitor, gdzie ma [nazwę](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) `storage_percent` , i może być wyświetlana w Azure Portal.|Poniżej 80%. Może obejść 100% dla pul bez wzrostu danych.|
|`avg_allocated_storage_percent`|Całkowite miejsce do magazynowania używane przez pliki bazy danych w magazynie we wszystkich bazach danych w puli elastycznej. Zawiera puste miejsce w plikach bazy danych. Dostępne w widoku [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) w `master` bazie danych programu. Ta Metryka jest również emitowana do Azure Monitor, gdzie ma [nazwę](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) `allocated_data_storage_percent` , i może być wyświetlana w Azure Portal.|Poniżej 90%. Może obejść 100% dla pul bez wzrostu danych.|
|`tempdb_log_used_percent`|Użycie przestrzeni dziennika transakcji w `tempdb` bazie danych programu. Mimo że obiekty tymczasowe utworzone w jednej bazie danych nie są widoczne w innych bazach danych w tej samej puli elastycznej, `tempdb` to zasób udostępniony dla wszystkich baz danych w tej samej puli. Długotrwała lub oddzielona transakcja w ramach `tempdb` uruchomienia z jednej bazy danych w puli może zużywać znaczną część dziennika transakcji i spowodować błędy zapytań w innych bazach danych w tej samej puli. Pochodzące z widoków [sys.dm_db_log_space_usage](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) i [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) . Ta Metryka jest również emitowana do Azure Monitor i może być wyświetlana w Azure Portal. Zobacz [przykłady](#examples) przykładowego zapytania, aby zwrócić bieżącą wartość tej metryki.|Poniżej 50%. Dopuszcza się sporadycznie do 80%.|
|||

Oprócz tych metryk Azure SQL Database zapewnia widok, który zwraca rzeczywiste limity zarządzania zasobami, a także dodatkowe widoki, które zwracają statystyki wykorzystania zasobów na poziomie puli zasobów i na poziomie grupy obciążenia.

|Nazwa widoku|Opis|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|Zwraca rzeczywiste ustawienia konfiguracji i pojemności używane przez mechanizmy ładu zasobów w bieżącej bazie danych lub puli elastycznej.|
|[sys.dm_resource_governor_resource_pools](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|Zwraca informacje o bieżącym stanie puli zasobów, bieżącej konfiguracji pul zasobów i statystykach puli zasobów zbiorczych.|
|[sys.dm_resource_governor_workload_groups](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|Zwraca statystyki grup obciążeń skumulowanych i bieżącą konfigurację grupy obciążeń. Ten widok można dołączyć do sys.dm_resource_governor_resource_pools w kolumnie, `pool_id` Aby uzyskać informacje o puli zasobów.|
|[sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|Zwraca statystykę wykorzystania puli zasobów dla ostatnich 32 minut. Każdy wiersz reprezentuje interwał 20 sekund. `delta_`Kolumny zwracają zmiany w każdej statystyce w interwale.|
|[sys.dm_resource_governor_workload_groups_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|Zwraca statystykę wykorzystania grup obciążeń dla ostatnich 32 minut. Każdy wiersz reprezentuje interwał 20 sekund. `delta_`Kolumny zwracają zmiany w każdej statystyce w interwale.|
|||

Te widoki mogą służyć do monitorowania wykorzystania zasobów i rozwiązywania problemów z rywalizacją o zasoby niemal w czasie rzeczywistym. Obciążenie użytkownikami na głównych i czytelnych replikach pomocniczych, łącznie z replikami geograficznymi, jest klasyfikowane do `SloSharedPool1` puli zasobów i `UserPrimaryGroup.DBId[N]` grupy obciążeń, gdzie `N` oznacza wartość identyfikatora bazy danych.

Oprócz monitorowania bieżącego wykorzystania zasobów klienci korzystający z pul gęstych mogą przechowywać historyczne dane użycia zasobów w osobnym magazynie danych. Te dane mogą służyć do analizy predykcyjnej w celu aktywnego zarządzania użyciem zasobów w oparciu o trendy historyczne i sezonowe.

## <a name="operational-recommendations"></a>Zalecenia dotyczące działania

**Pozostaw wystarczającą ilość zasobów** . W przypadku wystąpienia rywalizacji o zasoby i obniżenia wydajności środki zaradcze mogą polegać na przeniesieniu niektórych baz danych z puli elastycznej, których dotyczy problem, lub skalowaniu w górę puli, jak wspomniano wcześniej. Jednak te akcje wymagają wykonania dodatkowych zasobów obliczeniowych. W szczególności w przypadku pul w warstwie Premium i Krytyczne dla działania firmy te działania wymagają transferu wszystkich danych dla przenoszonych baz danych lub dla wszystkich baz danych w puli elastycznej, jeśli Pula jest skalowana w górę. Transfer danych jest długotrwałą operacją i intensywnym użyciem zasobów. Jeśli pula jest już w ramach dużego nacisku zasobów, sama operacja ograniczająca znacznie obniży wydajność. W skrajnych przypadkach może nie być możliwe rozwiązanie rywalizacji o zasoby za pośrednictwem skalowania bazy danych lub puli, ponieważ wymagane zasoby nie są dostępne. W takim przypadku tymczasowe zmniejszenie obciążenia zapytania w puli elastycznej, której dotyczy problem, może być jedynym rozwiązaniem.

Klienci korzystający z pul silnie powinni dokładnie monitorować trendy użycia zasobów zgodnie z wcześniejszym opisem i podejmować środki zaradcze, gdy metryki pozostają w ramach zalecanych zakresów i nadal są wystarczające zasoby w puli elastycznej.

Użycie zasobów zależy od wielu czynników, które zmieniają się wraz z upływem czasu dla każdej bazy danych i każdej elastycznej puli. Osiągnięcie optymalnego współczynnika cen/wydajności w ramach pul gęstych wymaga ciągłego monitorowania i ponownego zrównoważenia, które przenosi bazy danych z bardziej używanych pul do mniej używanych pul i tworzy nowe pule w razie potrzeby w celu uwzględnienia zwiększonego obciążenia.

**Nie przenoś baz danych "gorąca"** . Jeśli rywalizacja o zasoby na poziomie puli jest przede wszystkim spowodowane przez małą liczbę wysoce wykorzystywanych baz danych, może być skłonne przenieść te bazy danych do mniejszej używanej puli lub utworzyć autonomiczne bazy danych. Nie zaleca się jednak, aby mimo że baza danych była wysoce wykorzystywana, nie jest zalecana, ponieważ operacja przenoszenia znacznie obniży wydajność, zarówno dla przenoszonej bazy danych, jak i dla całej puli. Zamiast tego należy poczekać na podwyższenie poziomu lub przenieść mniej używane bazy danych zamiast naciskania zasobów na poziomie puli. Jednak przeniesienie baz danych z bardzo niskim wykorzystaniem nie zapewnia żadnych korzyści w tym przypadku, ponieważ nie zmniejsza to ilości zasobów na poziomie puli.

**Utwórz nowe bazy danych w puli "Kwarantanna"** . W scenariuszach, w których często tworzone są nowe bazy danych, takie jak aplikacje korzystające z modelu dzierżawców dla bazy danych, istnieje ryzyko, że nowa baza danych umieszczona w istniejącej puli elastycznej nieoczekiwanie zużywa znaczące zasoby i wpłynie na inne bazy danych i procesy wewnętrzne w puli. Aby zmniejszyć to ryzyko, należy utworzyć oddzielną pulę "Kwarantanna" z dużą ilością alokacji zasobów. Użyj tej puli dla nowych baz danych, które mają jeszcze nieznane wzorce zużycia zasobów. Gdy baza danych została już w tej puli dla cyklu biznesowego, na przykład tygodnia lub miesiąca, a jego użycie zasobów jest znane, można je przenieść do puli o wystarczającej pojemności, aby pomieścić dodatkowe użycie zasobów.

**Monitoruj zarówno zajęte, jak i przydzieloną przestrzeń** . Gdy przydzielono miejsce w puli (całkowity rozmiar wszystkich plików bazy danych w magazynie dla wszystkich baz danych w puli) osiągnie maksymalny rozmiar puli, mogą wystąpić błędy braku miejsca. W przypadku dużej ilości przyznanych trendów miejsca i jest on śledzony do osiągnięcia maksymalnego rozmiaru puli, opcje ograniczenia obejmują:
- Przenieś niektóre bazy danych poza pulę, aby zmniejszyć łączną przydzieloną przestrzeń
- [Zmniejsz](file-space-manage.md) pliki bazy danych, aby zmniejszyć liczbę pustych przyznanych miejsc w plikach
- Skalowanie w górę puli do celu usługi o większym maksymalnym rozmiarze puli

Jeśli używane miejsce w puli (całkowity rozmiar danych we wszystkich bazach danych w puli, bez uwzględnienia pustego miejsca w plikach) trendy wysokie i jest śledzone w celu osiągnięcia maksymalnego rozmiaru puli, opcje ograniczenia obejmują:
- Przenoszenie niektórych baz danych poza pulę w celu zmniejszenia całkowitego zajętego miejsca
- Przenoszenie (archiwizowanie) danych poza bazą danych lub usuwanie danych, które nie są już potrzebne
- Implementuj [kompresję danych](/sql/relational-databases/data-compression/data-compression)
- Skalowanie w górę puli do celu usługi o większym maksymalnym rozmiarze puli

**Unikaj nadmiernie gęstych serwerów** . Azure SQL Database [obsługuje](./resource-limits-logical-server.md) do 5000 baz danych na serwer. Klienci korzystający z pul elastycznych z tysiącami baz danych mogą rozważyć umieszczenie wielu pul elastycznych na jednym serwerze z łączną liczbą baz danych do obsługiwanego limitu. Jednak serwery z wieloma tysiącami baz danych tworzą wyzwania operacyjne. Operacje wymagające wyliczenia wszystkich baz danych na serwerze, na przykład wyświetlanie baz danych w portalu, będą wolniejsze. Błędy operacyjne, takie jak nieprawidłowe modyfikacje nazw logowania na poziomie serwera lub reguł zapory, wpłyną na większą liczbę baz danych. Przypadkowe usunięcie serwera wymaga pomocy pomoc techniczna firmy Microsoft do odzyskania baz danych na usuniętym serwerze i spowoduje długotrwałą awarię dla wszystkich odpowiednich baz danych.

Zalecamy ograniczenie liczby baz danych na serwer do mniejszej liczby niż wartość maksymalna obsługiwana. W wielu scenariuszach optymalna jest użycie do 1000-2000 baz danych na serwer. Aby zmniejszyć prawdopodobieństwo przypadkowego usunięcia serwera, zalecamy umieszczenie [blokady usuwania](../../azure-resource-manager/management/lock-resources.md) na serwerze lub jej grupie zasobów.

W przeszłości niektóre scenariusze obejmujące przeniesienie baz danych do, out lub między pulami elastycznymi na tym samym serwerze były szybsze niż w przypadku przechodzenia między serwerami. Obecnie wszystkie bazy danych są wykonywane z tą samą szybkością niezależnie od serwera źródłowego i docelowego.

## <a name="examples"></a>Przykłady

### <a name="monitoring-memory-utilization"></a>Monitorowanie wykorzystania pamięci

To zapytanie oblicza `oom_per_second` metrykę dla każdej puli zasobów w ciągu ostatnich 32 minut. To zapytanie można wykonać w dowolnej bazie danych w puli elastycznej.

```sql
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```

### <a name="monitoring-tempdb-log-space-utilization"></a>Monitorowanie `tempdb` wykorzystania miejsca w dzienniku

To zapytanie zwraca bieżącą wartość `tempdb_log_used_percent` metryki, pokazując względne użycie `tempdb` dziennika transakcji względem jego maksymalnego dozwolonego rozmiaru. To zapytanie można wykonać w dowolnej bazie danych w puli elastycznej.

```sql
SELECT (lsu.used_log_space_in_bytes / df.log_max_size_bytes) * 100 AS tempdb_log_space_used_percent
FROM tempdb.sys.dm_db_log_space_usage AS lsu
CROSS JOIN (
           SELECT SUM(CAST(max_size AS bigint)) * 8 * 1024. AS log_max_size_bytes
           FROM tempdb.sys.database_files
           WHERE type_desc = N'LOG'
           ) AS df
;
```

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z wprowadzeniem do pul elastycznych, zobacz [Pule elastyczne ułatwiają zarządzanie wieloma bazami danych w Azure SQL Database i skalowanie](./elastic-pool-overview.md)ich.
- Aby uzyskać informacje na temat dostrajania obciążeń zapytań w celu ograniczenia wykorzystania zasobów, zobacz [monitorowanie i dostrajanie]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index)oraz [dostrajanie wydajności i monitorowanie](./monitor-tune-overview.md).