---
title: Limity zasobów dla serwerów logicznych na platformie Azure
description: Ten artykuł zawiera omówienie limitów zasobów dla serwera logicznego na platformie Azure używanych przez Azure SQL Database i usługę Azure Synapse Analytics. Zawiera również informacje dotyczące tego, co się dzieje po osiągnięciu lub przekroczeniu limitów zasobów.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 02/02/2021
ms.openlocfilehash: e8f18f56c746f0d12f43cc2fb6ce9088a9b82b45
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492386"
---
# <a name="resource-limits-for-azure-sql-database-and-azure-synapse-analytics-servers"></a>Limity zasobów dla Azure SQL Database i serwerów analiz usługi Azure Synapse
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Ten artykuł zawiera omówienie limitów zasobów dla serwera logicznego używanego przez Azure SQL Database i usługę Azure Synapse Analytics. Zawiera informacje o tym, co się stanie w przypadku osiągnięcia lub przekroczenia limitów zasobów oraz opis mechanizmów ładu zasobów używanych do wymuszania tych limitów.

> [!NOTE]
> W przypadku limitów wystąpienia zarządzanego usługi Azure SQL zapoznaj się z tematem [SQL Database limitów zasobów dla wystąpień zarządzanych](../managed-instance/resource-limits.md).

## <a name="maximum-resource-limits"></a>Maksymalne limity zasobów

| Zasób | Limit |
| :--- | :--- |
| Bazy danych na serwer | 5000 |
| Domyślna liczba serwerów na subskrypcję w dowolnym regionie | 20 |
| Maksymalna liczba serwerów na subskrypcję w dowolnym regionie | 200 |  
| Przydział jednostek DTU/eDTU na serwer | 54 000 |  
| przydział rdzeń wirtualny na serwer/wystąpienie | 540 |
| Maksymalna liczba pul na serwer | Ograniczone przez liczbę DTU lub rdzeni wirtualnych. Na przykład jeśli każda pula ma 1000 DTU, serwer może obsługiwać pule 54.|
|||

> [!IMPORTANT]
> Ponieważ liczba baz danych zbliża się do limitu na serwer, mogą wystąpić następujące elementy:
>
> - Zwiększanie opóźnień w uruchamianiu zapytań względem bazy danych Master.  Obejmuje to widoki statystyk wykorzystania zasobów, takie jak sys.resource_stats.
> - Zwiększenie opóźnienia w operacjach zarządzania i portalu renderowania, który obejmuje wyliczanie baz danych na serwerze.

> [!NOTE]
> Aby uzyskać więcej wartości przydziału jednostek DTU/eDTU, przydziału rdzeń wirtualny lub większej liczby serwerów niż domyślna, Prześlij nowe żądanie obsługi w Azure Portal. Aby uzyskać więcej informacji, zobacz [zwiększenie przydziału żądań dla Azure SQL Database](quota-increase-request.md).

### <a name="storage-size"></a>Rozmiar magazynu

W przypadku magazynów zasobów o pojedynczej bazie danych zapoznaj się z limitami zasobów [opartymi](resource-limits-dtu-single-databases.md) na jednostkach DTU lub limitami [zasobów opartymi na rdzeń wirtualny](resource-limits-vcore-single-databases.md) dla limitów rozmiaru magazynu na warstwę cenową.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Co się stanie po osiągnięciu limitów zasobów bazy danych

### <a name="compute-cpu"></a>Procesor obliczeniowy

Gdy użycie procesora CPU obliczeniowej bazy danych stanie się wysokie, wzrasta opóźnienie zapytania, a kwerendy mogą nawet przekroczyć limit czasu. W tych warunkach zapytania mogą być umieszczane w kolejce przez usługę i są udostępniane zasoby do wykonania, ponieważ zasoby stają się bezpłatne.
W przypadku wystąpienia dużej mocy obliczeniowej opcje ograniczenia obejmują:

- Zwiększenie rozmiaru obliczeniowego bazy danych lub puli elastycznej w celu zapewnienia bazy danych większej ilości zasobów obliczeniowych. Zobacz [skalowanie zasobów pojedynczych baz danych](single-database-scale.md) i [skalowanie zasobów puli elastycznej](elastic-pool-scale.md).
- Optymalizowanie zapytań w celu zmniejszenia użycia zasobów procesora CPU przez poszczególne zapytania. Aby uzyskać więcej informacji, zobacz [Podpowiedzi i dostrajanie zapytań](performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

Gdy używane miejsce na bazę danych osiągnie limit rozmiaru, wstawia i aktualizuje bazę danych, która zwiększa niepowodzenie rozmiaru danych, a klienci odbierają [komunikat o błędzie](troubleshoot-common-errors-issues.md). Instrukcje SELECT i DELETE kontynuują się pomyślnie.

W przypadku wystąpienia dużej ilości miejsca, opcje ograniczenia obejmują:

- Zwiększenie maksymalnego rozmiaru bazy danych lub elastycznej puli lub dodanie większej ilości miejsca do magazynowania. Zobacz [skalowanie zasobów pojedynczych baz danych](single-database-scale.md) i [skalowanie zasobów puli elastycznej](elastic-pool-scale.md).
- Jeśli baza danych znajduje się w puli elastycznej, można przenieść bazę danych poza pulę, aby jej miejsce do magazynowania nie było udostępniane innym baz danych.
- Zmniejsz rozmiar bazy danych, aby odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem plików w Azure SQL Database](file-space-manage.md).
- Sprawdź, czy duże wykorzystanie miejsca jest spowodowane skokiem w rozmiarze trwałego magazynu wersji (PVS). PVS jest częścią każdej bazy danych i służy do implementowania  [przyspieszonego odzyskiwania bazy danych](../accelerated-database-recovery.md). Aby określić bieżący rozmiar PVS, zobacz [PVS Rozwiązywanie problemów](https://docs.microsoft.com/sql/relational-databases/accelerated-database-recovery-management#troubleshooting). Typowym powodem dużego rozmiaru PVS jest transakcja, która jest otwarta przez długi czas (godz.), uniemożliwiając czyszczenie starszych wersji w PVS.

### <a name="sessions-and-workers-requests"></a>Sesje i procesy robocze (żądania)

Maksymalna liczba sesji i procesów roboczych zależy od warstwy usług i rozmiaru obliczeń (DTU/jednostek eDTU lub rdzeni wirtualnych). Nowe żądania są odrzucane, gdy osiągnięto limity sesji lub procesu roboczego, a klienci otrzymają komunikat o błędzie. Liczba dostępnych połączeń może być kontrolowana przez aplikację, ale liczba współbieżnych procesów roboczych często jest trudniejsza do oszacowania i kontroli. Jest to szczególnie ważne w przypadku szczytowych okresów ładowania, gdy osiągnięto limity zasobów bazy danych i procesy robocze są ustawiane z powodu dłuższych przebiegów zapytań, dużych łańcuchów blokowania lub nadmiernej współbieżności zapytań.

W przypadku wystąpienia wysokiego poziomu sesji lub procesu wyłączania, opcje ograniczenia obejmują:

- Zwiększenie warstwy usług lub rozmiaru obliczeniowego bazy danych lub puli elastycznej. Zobacz [skalowanie zasobów pojedynczych baz danych](single-database-scale.md) i [skalowanie zasobów puli elastycznej](elastic-pool-scale.md).
- Optymalizowanie zapytań w celu ograniczenia wykorzystania zasobów dla każdego zapytania, jeśli przyczyną zwiększonego wykorzystania procesów roboczych jest rywalizacja o zasoby obliczeniowe. Aby uzyskać więcej informacji, zobacz [Podpowiedzi i dostrajanie zapytań](performance-guidance.md#query-tuning-and-hinting).
- Zmniejszenie ustawienia [MAXDOP](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (maksymalny stopień równoległości).
- Optymalizacja obciążenia zapytania, aby zmniejszyć liczbę wystąpień i czas trwania blokowania zapytań. Aby uzyskać więcej informacji, zobacz [Omówienie i rozwiązywanie problemów z blokowaniem usługi Azure SQL](understand-resolve-blocking.md).

### <a name="memory"></a>Pamięć

W przeciwieństwie do innych zasobów (procesora CPU, procesów roboczych, magazynu), osiągających limit pamięci nie wpływa negatywnie na wydajność zapytań i nie powoduje błędów i błędów. Jak opisano szczegółowo w [przewodniku dotyczącym architektury zarządzania pamięcią](/sql/relational-databases/memory-management-architecture-guide), aparat bazy danych SQL Server często używa całej dostępnej pamięci, przez zaprojektowanie. Pamięć jest używana głównie do buforowania danych, aby uniknąć tańszego dostępu do magazynu. W związku z tym wyższe użycie pamięci zwykle zwiększa wydajność zapytań z powodu szybszego odczytu z pamięci, a nie wolniejszych odczytów z magazynu.

Po uruchomieniu aparatu bazy danych, ponieważ obciążenie zaczyna odczytywać dane z magazynu, aparat bazy danych agresywnie buforuje dane w pamięci. Po upływie tego początkowego okresu wstępnego jest to typowy i oczekiwany na wyświetlenie `avg_memory_usage_percent` `avg_instance_memory_percent` kolumn i w [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) , które mają być zamknięte lub równe 100%, szczególnie w przypadku baz danych, które nie są bezczynne i nie mieszczą się w pełni w pamięci.

Oprócz pamięci podręcznej danych pamięć jest używana w innych składnikach aparatu bazy danych. Gdy jest zapotrzebowanie na pamięć, a cała dostępna pamięć jest używana przez pamięć podręczną danych, aparat bazy danych dynamicznie zmniejsza rozmiar pamięci podręcznej danych, aby udostępnić pamięć innym składnikom, i dynamicznie zwiększa pamięć podręczną danych, gdy inne składniki zwolnią pamięć.

W rzadkich przypadkach wystarczająco wysokie obciążenie może spowodować niespełnienie niewystarczającej ilości pamięci, co prowadzi do błędów braku pamięci. Może się to zdarzyć na dowolnym poziomie wykorzystania pamięci między 0% a 100%. Jest to bardziej duże wystąpienie w przypadku mniejszych rozmiarów obliczeniowych, które mają ograniczone ilości pamięci i/lub z obciążeniami, które używają większej ilości pamięci do przetwarzania zapytań, na przykład w przypadku [gęstych pul elastycznych](elastic-pool-resource-management.md).

Gdy wystąpią błędy braku pamięci, opcje ograniczenia obejmują:
- Zwiększenie warstwy usług lub rozmiaru obliczeniowego bazy danych lub puli elastycznej. Zobacz [skalowanie zasobów pojedynczych baz danych](single-database-scale.md) i [skalowanie zasobów puli elastycznej](elastic-pool-scale.md).
- Optymalizowanie zapytań i konfiguracji w celu zmniejszenia wykorzystania pamięci. Typowe rozwiązania zostały opisane w poniższej tabeli.

|Rozwiązanie|Opis|
| :----- | :----- |
|Zmniejszenie rozmiaru dotacji do pamięci|Aby uzyskać więcej informacji na temat przydziałów pamięci, zapoznaj się z wpisem w blogu [Informacje o rozłożeniu SQL Server pamięci](https://techcommunity.microsoft.com/t5/sql-server/understanding-sql-server-memory-grant/ba-p/383595) . Typowym rozwiązaniem w przypadku unikania nadmiernej ilości przydziałów pamięci jest aktualizowanie [statystyk](/sql/relational-databases/statistics/statistics) . Pozwala to na dokładniejsze oszacowanie zużycia pamięci przez aparat zapytań, unikając niepotrzebnych uprawnień dużych ilości pamięci.</br></br>W bazach danych korzystających z poziomu zgodności 140 i nowszych aparat bazy danych może automatycznie dostosować rozmiar przydziału pamięci przy użyciu [informacji zwrotnych dotyczących przydziału pamięci w trybie wsadowym](/sql/relational-databases/performance/intelligent-query-processing#batch-mode-memory-grant-feedback). W przypadku baz danych korzystających z poziomu zgodności 150 i nowszych aparat bazy danych podobnie używa [opinii o przydzieleniu pamięci w trybie wiersza](/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)w przypadku bardziej typowych zapytań w trybie wiersza. Ta wbudowana funkcja pomaga uniknąć błędów braku pamięci z powodu niepotrzebnych uprawnień dużych ilości pamięci.|
|Zmniejsz rozmiar pamięci podręcznej planu zapytania|Aparat bazy danych buforuje plany zapytania w pamięci, aby uniknąć kompilowania planu zapytania dla każdego wykonywania zapytania. Aby uniknąć przeładowanie pamięci podręcznej planu zapytania spowodowanej przez plany buforowania, które są używane tylko raz, należy włączyć [konfigurację z zakresem bazy danych](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)OPTIMIZE_FOR_AD_HOC_WORKLOADS.|
|Zmniejsz rozmiar pamięci blokady|Aparat bazy danych używa pamięci do [blokad](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#Lock_Engine). Jeśli to możliwe, unikaj dużych transakcji, które mogą uzyskać dużą liczbę blokad i spowodować duże użycie pamięci przez blokadę.|


## <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>Użycie zasobów według obciążeń użytkowników i procesów wewnętrznych

Użycie procesora CPU i pamięci przez obciążenia użytkowników w każdej bazie danych jest zgłaszane w widokach [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) i [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) , w `avg_cpu_percent` i `avg_memory_usage_percent` kolumnach. W przypadku pul elastycznych użycie zasobów na poziomie puli jest raportowane w widoku [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) . Użycie procesora CPU przez obciążenie użytkownikami jest również raportowane za pośrednictwem `cpu_percent` metryki Azure monitor dla [pojedynczych baz danych](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserversdatabases) i [pul elastycznych](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) na poziomie puli.

Azure SQL Database wymaga zasobów obliczeniowych w celu zaimplementowania podstawowych funkcji usługi, takich jak wysoka dostępność i odzyskiwanie po awarii, tworzenie kopii zapasowej i przywracanie bazy danych, monitorowanie, magazyn zapytań, Dostosowywanie automatyczne itd. System przypisuje pewnej ograniczonej części ogólnych zasobów dla tych wewnętrznych procesów przy użyciu mechanizmów nadzoru [zasobów](#resource-governance) , dzięki czemu pozostałe zasoby są dostępne dla obciążeń użytkowników. Czasami gdy wewnętrzne procesy nie używają zasobów obliczeniowych, system udostępni je obciążom użytkowników.

Całkowite użycie procesora i pamięci przez obciążenia użytkowników i procesy wewnętrzne jest zgłaszane w widokach [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) i [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) , w `avg_instance_cpu_percent` i `avg_instance_memory_percent` kolumnach. Te dane są również raportowane za pośrednictwem `sqlserver_process_core_percent` `sqlserver_process_memory_percent` metryk i Azure monitor dla [pojedynczych baz danych](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserversdatabases) i [pul elastycznych](../../azure-monitor/platform/metrics-supported.md#microsoftsqlserverselasticpools) na poziomie puli.

Bardziej szczegółowy podział użycia zasobów przez obciążenia użytkowników i procesy wewnętrzne jest raportowany w widokach [sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) i [sys.dm_resource_governor_workload_groups_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) . Aby uzyskać szczegółowe informacje na temat pul zasobów i grup obciążeń, do których odwołuje się te widoki, zobacz temat [Zarządzanie zasobami](#resource-governance). Te widoki raportują wykorzystanie zasobów według obciążeń użytkowników i określonych procesów wewnętrznych w skojarzonych pulach zasobów i grupach obciążeń.

W kontekście monitorowania wydajności i rozwiązywania problemów ważne jest, aby uwzględnić użycie **procesora przez użytkownika** ( `avg_cpu_percent` , `cpu_percent` ) i całkowite użycie **procesora** przez obciążenia użytkowników i procesy wewnętrzne ( `avg_instance_cpu_percent` , `sqlserver_process_core_percent` ).

Użycie **procesora CPU przez użytkownika** jest obliczane jako wartość procentowa limitów obciążeń użytkowników w każdym celu usługi. **Użycie procesora CPU przez użytkownika** w 100% wskazuje, że obciążenie użytkownikami osiągnęło limit celu usługi. Jednak gdy **całkowite użycie procesora CPU** osiągnie zakres 70-100%, możliwe jest wyświetlenie przepływności obciążeń użytkowników i zmniejszenie opóźnienia, nawet jeśli zgłoszone użycie **procesora CPU przez użytkownika** pozostanie znacznie poniżej 100%. Jest to korzystniejsze w przypadku używania mniejszych celów usługi z umiarkowaną alokacją zasobów obliczeniowych, ale stosunkowo intensywnymi obciążeniami użytkowników, takimi jak duże [Pule elastyczne](elastic-pool-resource-management.md). Może to również wystąpić w przypadku mniejszych celów usługi, gdy wewnętrzne procesy tymczasowo wymagają dodatkowych zasobów, na przykład podczas tworzenia nowej repliki bazy danych.

Gdy **całkowite użycie procesora CPU** jest wysokie, opcje łagodzenia są takie same, jak zanotowane wcześniej i obejmują optymalizację celu usługi i/lub Optymalizacja obciążeń użytkownika.

## <a name="resource-governance"></a>Nadzór nad zasobami

Aby wymusić limity zasobów, Azure SQL Database używa implementacji ładu zasobów, która jest oparta na SQL Server [gubernatora zasobów](/sql/relational-databases/resource-governor/resource-governor), modyfikowana i rozszerzona w celu uruchomienia w Azure SQL Database. W SQL Database, wielu [pul zasobów](/sql/relational-databases/resource-governor/resource-governor-resource-pool) i [grup obciążeń](/sql/relational-databases/resource-governor/resource-governor-workload-group)z limitami zasobów ustawionymi na poziomie puli i grup, należy zapewnić [zrównoważoną bazę danych jako usługę](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). Obciążenie użytkownikami i obciążenia wewnętrzne są klasyfikowane do oddzielnych pul zasobów i grup obciążeń. Obciążenie użytkownikami na głównych i czytelnych replikach pomocniczych, łącznie z replikami geograficznymi, jest klasyfikowane do `SloSharedPool1` puli zasobów i `UserPrimaryGroup.DBId[N]` grupy obciążeń, gdzie `N` oznacza wartość identyfikatora bazy danych. Ponadto istnieje wiele pul zasobów i grup obciążeń dla różnych obciążeń wewnętrznych.

Oprócz korzystania z regulatora zasobów w celu zarządzania zasobami w ramach procesu SQL, Azure SQL Database używa również [obiektów zadań](/windows/win32/procthread/job-objects) systemu Windows na potrzeby nadzoru zasobów na poziomie procesu i usługi Windows [File Server Menedżer zasobów (Menedżer obiektów (plików)](/windows-server/storage/fsrm/fsrm-overview) dla usługi Zarządzanie przydziałami magazynu.

Azure SQL Database Zarządzanie zasobami ma charakter hierarchiczny. Od góry do dołu limity są wymuszane na poziomie systemu operacyjnego i na poziomie woluminu magazynu przy użyciu mechanizmów zarządzania zasobami systemu operacyjnego i zarządca zasobów, a następnie na poziomie puli zasobów przy użyciu prezesa zasobów, a następnie na poziomie grupy obciążeń przy użyciu regulatora zasobów. Limity zarządzania zasobami obowiązujące w przypadku bieżącej bazy danych lub puli elastycznej są wyświetlane w widoku [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) .

### <a name="data-io-governance"></a>Zarządzanie we/wy danych

Zarządzanie we/wy danych to proces w Azure SQL Database używany do ograniczania fizycznych operacji we/wy odczytu i zapisu do plików danych bazy danych. Limity liczby IOPS są ustawiane dla każdego poziomu usługi, aby zminimalizować efekt "zakłócenia", aby zapewnić sprawiedliwość alokacji zasobów w usłudze z wieloma dzierżawcami i pozostać w ramach możliwości podstawowego sprzętu i magazynu.

W przypadku pojedynczych baz danych limity grup obciążeń są stosowane do wszystkich operacji we/wy magazynu związanych z bazą danych, podczas gdy limity puli zasobów dotyczą wszystkich baz danych w tej samej dedykowanej puli SQL, w tym `tempdb` bazy danych programu. W przypadku pul elastycznych limity grup obciążeń dotyczą każdej bazy danych w puli, a limit puli zasobów ma zastosowanie do całej puli elastycznej, łącznie z `tempdb` bazą danych, która jest współużytkowana przez wszystkie bazy danych w puli. Ogólnie rzecz biorąc, limity puli zasobów mogą nie być osiągalne w przypadku obciążenia dla bazy danych (pojedyncza lub w puli), ponieważ limity grup obciążeń są mniejsze niż limity puli zasobów i wkrótce ograniczają liczbę operacji we/wy na sekundę. Jednak limity puli mogą być osiągane przez połączone obciążenie dla wielu baz danych w tej samej puli.

Na przykład, jeśli zapytanie generuje 1000 IOPS bez żadnego ładu zasobów we/wy, ale limit liczby IOPS dla grupy obciążenia jest ustawiony na 900 IOPS, zapytanie nie będzie w stanie generować więcej niż 900 operacji wejścia/wyjścia. Jeśli jednak limit liczby IOPS dla puli zasobów jest ustawiony na 1500 operacji we/wy na sekundę, a łączny stan wejścia/wyjścia ze wszystkich grup obciążeń skojarzonych z pulą zasobów przekracza 1500 operacji we/wy, wówczas ta sama kwerenda może zostać zmniejszona poniżej limitu grupy roboczej dla 900 IOPS.

Wartości liczby IOPS i minimum/maksimum przepływności zwrócone przez [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) widoku działają jako limity/wersaliki, a nie jako gwarancje. Ponadto zarządzanie zasobami nie gwarantuje żadnych opóźnień związanych z magazynem. Najlepsze osiągalne opóźnienia, operacje we/wy i przepływność dla danego obciążenia użytkownika zależą nie tylko od limitów nadzoru zasobów we/wy, ale również w przypadku różnych użytych rozmiarów we/wykorzystaniu i na możliwościach magazynu bazowego. SQL Database używa systemu IOs, który różni się od 512 KB i 4 MB. W celu wymuszenia limitów IOPS każda operacja we/wy jest uwzględniana niezależnie od jej rozmiaru, z wyjątkiem baz danych z plikami danych w usłudze Azure Storage. W takim przypadku system IOs o rozmiarze większym niż 256 KB jest rozliczany jako wiele urządzeń z systemem IOs o pojemności 256 KB, aby dostosować je do usługi Azure Storage we/wy.

W przypadku baz danych w warstwach Podstawowa, standardowa i Ogólnego przeznaczenia, które korzystają z plików danych w usłudze Azure Storage, `primary_group_max_io` wartość może nie być osiągalna, jeśli baza danych nie ma wystarczającej ilości plików danych do rozłącznie, ale dane nie są dystrybuowane równomiernie między plikami, lub jeśli warstwa wydajności bazowych obiektów BLOB ogranicza liczbę IOPS/przepływność poniżej limitów zarządzania zasobami. Podobnie w przypadku małego systemu IOs wygenerowanego przez częste zatwierdzanie transakcji `primary_max_log_rate` wartość może nie być osiągalna przez obciążenie wynikające z limitu liczby IOPS w źródłowym obiekcie blob usługi Azure Storage. W przypadku baz danych korzystających z usługi Azure Premium Storage Azure SQL Database wykorzystuje wystarczająco duże obiekty blob magazynu, aby uzyskać wymaganą liczbę operacji we/wy na sekundę, niezależnie od rozmiaru bazy danych. W przypadku większych baz danych tworzone są wiele plików danych w celu zwiększenia łącznej liczby operacji we/wy na sekundę/przepływności.

Wartości wykorzystania zasobów, takie jak `avg_data_io_percent` i `avg_log_write_percent` , raportowane w widokach [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database),  [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)i [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) , są obliczane jako wartości procentowe maksymalnego limitu zarządzania zasobami. W związku z tym, gdy czynniki inne niż zarządzanie zasobami ograniczają liczbę operacji we/wy, można zobaczyć, że operacje we/wy na sekundę i opóźnienia zwiększają się wraz ze wzrostem obciążenia, nawet jeśli zgłoszone użycie zasobów pozostanie poniżej 100%.

Aby zobaczyć liczbę operacji we/wy odczytu i zapisu, przepływność i czas oczekiwania na plik bazy danych, użyj funkcji [sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) . Ta funkcja obejmuje wszystkie operacje we/wy względem bazy danych, w tym w przypadku operacji we/wy, która nie jest uwzględniana `avg_data_io_percent` , ale korzysta z IOPS i przepływności magazynu bazowego, a także może mieć wpływ na zaobserwowane opóźnienie magazynu. Funkcja umieszcza dodatkowe opóźnienia, które mogą zostać wprowadzone przez zarządzanie zasobami we/wy dla operacji odczytu i zapisu `io_stall_queued_read_ms` w `io_stall_queued_write_ms` kolumnach i.

### <a name="transaction-log-rate-governance"></a>Ocena szybkości dziennika transakcji

Współczynnik dziennika transakcji ładu to proces w Azure SQL Database używany do ograniczania wysokich szybkości pozyskiwania dla obciążeń takich jak wstawianie zbiorcze, wybieranie do i kompilacje indeksów. Limity te są śledzone i wymuszane na poziomie drugim do szybkości generowania rekordów dziennika, ograniczając przepływność bez względu na to, ile systemu IOs można wystawić dla plików danych.  Stawki generowania dziennika transakcji są obecnie skalowane liniowo do punktu, który jest zależny od sprzętu, a maksymalna szybkość rejestrowania może być 96 MB/s z modelem zakupów rdzeń wirtualny.

> [!NOTE]
> Rzeczywiste fizyczne pliki dziennika transakcji w systemie IOs nie podlegają ograniczeniom ani nie są ograniczone.

Stawki dzienników są ustawiane tak, aby można je było osiągnąć i utrzymywać w różnych scenariuszach, podczas gdy ogólny system może utrzymywać swoją funkcjonalność przy minimalnym wpływie na obciążenie użytkownikami. Częstotliwość rejestrowania gwarantuje, że kopie zapasowe dziennika transakcji pozostają w ramach opublikowanej umowy SLA.  To zarządzanie uniemożliwia również nadmierne zaległości w replikach pomocniczych.

Ponieważ rekordy dziennika są generowane, każda operacja jest oceniana i oceniana pod kątem tego, czy należy opóźnić, aby zachować maksymalny żądany współczynnik dziennika (MB/s na sekundę). Opóźnienia nie są dodawane, gdy rekordy dziennika są opróżniane do magazynu, a w przypadku samego wygenerowania współczynnika rejestrowania jest stosowane zarządzanie szybkością rejestrowania.

Rzeczywiste stawki za generowanie dzienników nałożone w czasie wykonywania mogą również mieć wpływ na mechanizmy przesyłania opinii, tymczasowo zmniejszając dopuszczalne stawki dzienników, dzięki czemu system może być stabilny. Zarządzanie miejscem w pliku dziennika, unikanie uruchamiania w warunkach wolnego miejsca w dzienniku i mechanizmów replikacji grupy dostępności może tymczasowo obniżyć ogólne limity systemu.

Współczynnik dziennika: kształtowanie ruchu gubernatora jest przedstawiane przy użyciu następujących typów oczekiwania (uwidocznionych w widokach [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) i [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) ):

| Typ oczekiwania | Uwagi |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Ograniczanie bazy danych |
| POOL_LOG_RATE_GOVERNOR | Ograniczanie puli |
| INSTANCE_LOG_RATE_GOVERNOR | Ograniczanie poziomu wystąpienia |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Kontrola opinii, replikacja fizyczna grupy dostępności w warstwie Premium/Krytyczne dla działania firmy nie jest zachowywana |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Kontrola opinii, ograniczanie stawek aby uniknąć braku miejsca w dzienniku |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | Kontrola opinii o replikacji geograficznej, ograniczanie szybkości rejestrowania, aby uniknąć opóźnień danych i niedostępności geograficznie|
|||

W przypadku napotkania limitu szybkości rejestrowania, który hamuje pożądaną skalowalność, należy wziąć pod uwagę następujące opcje:

- Skalowanie w górę do wyższego poziomu usługi w celu uzyskania maksymalnej liczby dzienników 96 MB/s lub przełączenia do innej warstwy usług. Warstwa usługi do [skalowania](service-tier-hyperscale.md) zapewnia 100 MB/s, niezależnie od wybranego poziomu usługi.
- Jeśli ładowane dane są przejściowe, takie jak dane przejściowe w procesie ETL, można je załadować do bazy danych tempdb (co jest zarejestrowana w minimalnym stopniu).
- W przypadku scenariuszy analitycznych Załaduj do tabeli objętej usługą magazynu kolumn. Zmniejsza to wymaganą szybkość rejestrowania z powodu kompresji. Ta technika zwiększa wykorzystanie procesora CPU i ma zastosowanie tylko do zestawów danych, które korzystają z klastrowanych indeksów magazynu kolumn.

### <a name="storage-space-governance"></a>Zarządzanie miejscem do magazynowania

W warstwach usług premium i Krytyczne dla działania firmy bazy danych i plików dziennika transakcji są przechowywane na lokalnym woluminie SSD komputera obsługującego bazę danych lub pulę elastyczną. Zapewnia to wysoką liczbę operacji we/wy na sekundę i przepływność oraz niskie opóźnienia wejścia/wyjścia. Rozmiar tego woluminu lokalnego zależy od możliwości sprzętu i jest ograniczony. Na danym komputerze lokalne miejsce na woluminie jest używane przez bazy danych klienta, `tempdb` w tym system operacyjny, oprogramowanie do zarządzania, dane monitorowania, dzienniki itp. Jako że bazy danych są tworzone, usuwane i zwiększają/zmniejszają wykorzystanie miejsca, lokalne użycie miejsca na maszynie zmienia się w miarę upływu czasu. 

Jeśli system wykryje, że dostępne wolne miejsce na komputerze ma niską wartość, a dla bazy danych lub puli elastycznej jest narażony brak miejsca, spowoduje to przeniesienie bazy danych lub puli elastycznej na inną maszynę z wystarczającą ilością wolnego miejsca, co pozwoli na zwiększenie maksymalnych limitów rozmiaru skonfigurowanego celu usługi. To przenoszenie odbywa się w sposób online, podobnie jak w przypadku operacji skalowania bazy danych i ma podobny [wpływ](single-database-scale.md#impact), w tym krótkie (sekundy) przejście w tryb failover na końcu operacji. Ta praca awaryjna kończy otwarte połączenia i wycofuje transakcje, które potencjalnie wpływają na aplikacje korzystające z bazy danych w tym czasie.

Ponieważ dane są fizycznie kopiowane na inną maszynę, przeniesienie większych baz danych może wymagać znacznego czasu. W tym czasie, jeśli lokalne użycie miejsca przez dużą bazę danych użytkownika lub pulę elastyczną lub przez `tempdb` bazę danych wydłuża się bardzo szybko, ryzyko związane z zwiększeniem ilości miejsca. System inicjuje przenoszenie bazy danych w sposób zrównoważony, aby zapobiec błędom braku miejsca i uniknąć niepotrzebnych przełączeń w tryb failover.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat ogólnych limitów platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Aby uzyskać informacje na temat DTU i jednostek eDTU, zobacz [DTU i jednostek eDTU](purchasing-models.md#dtu-based-purchasing-model).
- Aby uzyskać informacje na temat limitów rozmiaru bazy danych tempdb, zobacz [tempdb w Azure SQL Database](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
