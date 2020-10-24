---
title: Monitorowanie obciążenia puli SQL przy użyciu widoków DMV
description: Dowiedz się, jak monitorować obciążenie puli SQL usługi Azure Synapse Analytics i wykonywanie zapytań przy użyciu widoków DMV.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/24/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: synapse-analytics
ms.openlocfilehash: 70ce0d6aada2b03646500720b0eba980a1f2d8f8
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515733"
---
# <a name="monitor-your-azure-synapse-analytics-sql-pool-workload-using-dmvs"></a>Monitorowanie obciążenia puli SQL usługi Azure Synapse Analytics przy użyciu widoków DMV

W tym artykule opisano sposób korzystania z dynamicznych widoków zarządzania (widoków DMV) do monitorowania obciążenia, w tym badania wykonania zapytania w puli SQL.

## <a name="permissions"></a>Uprawnienia

Aby wykonać zapytanie dotyczące widoków DMV w tym artykule, musisz **wyświetlić stan bazy danych** lub uprawnienie **Kontrola** . Zazwyczaj przyznanie **widoku stanu bazy danych** jest preferowanym uprawnieniem, które jest bardziej restrykcyjne.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Monitoruj połączenia

Wszystkie logowania do magazynu danych są rejestrowane w [sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Ten DMV zawiera ostatnie identyfikatory logowania 10 000.  Session_id jest kluczem podstawowym i jest przypisywana sekwencyjnie dla każdego nowego logowania.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Monitoruj wykonywanie zapytań

Wszystkie zapytania wykonane w puli SQL są rejestrowane w [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Ten DMV zawiera ostatnie 10 000 wykonanych zapytań.  Request_id jednoznacznie identyfikuje poszczególne zapytania i jest kluczem podstawowym dla tego DMV.  Request_id jest przypisywana sekwencyjnie dla każdego nowego zapytania i ma prefiks QID, który oznacza identyfikator zapytania.  Wykonywanie zapytania dotyczącego tego DMV dla danego session_id zawiera wszystkie zapytania dotyczące danego logowania.

> [!NOTE]
> Procedury składowane używają wielu identyfikatorów żądań.  Identyfikatory żądań są przypisywane w kolejności sekwencyjnej.

Poniżej przedstawiono kroki, które należy wykonać w celu zbadania planów i czasów wykonywania zapytań dotyczących określonego zapytania.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>Krok 1. określenie zapytania, które chcesz zbadać

```sql
-- Monitor active queries
SELECT *
FROM sys.dm_pdw_exec_requests
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 *
FROM sys.dm_pdw_exec_requests
ORDER BY total_elapsed_time DESC;

```

Z poprzednich wyników zapytania należy **zwrócić uwagę na identyfikator żądania** zapytania, które chcesz zbadać.

Zapytania w stanie **wstrzymania** można umieścić w kolejce ze względu na dużą liczbę aktywnych uruchomionych zapytań. Te zapytania są również wyświetlane w kwerendzie [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) czeka na zapytanie o typie UserConcurrencyResourceType. Aby uzyskać informacje o ograniczeniach współbieżności, zobacz [pamięć i limity współbieżności](memory-concurrency-limits.md) lub [klasy zasobów do zarządzania obciążeniami](resource-classes-for-workload-management.md). Zapytania mogą również poczekać z innych powodów, takich jak w przypadku blokad obiektów.  Jeśli zapytanie oczekuje na zasób, zobacz [Badanie zapytań oczekujących na zasoby](#monitor-waiting-queries) w dalszej sekcji tego artykułu.

Aby uprościć wyszukiwanie zapytania w tabeli [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) , użyj [etykiety](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) , aby przypisać komentarz do zapytania, które można wyszukać w widoku sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

### <a name="step-2-investigate-the-query-plan"></a>Krok 2. Badanie planu zapytania

Użyj identyfikatora żądania, aby pobrać plan rozproszonej bazy danych SQL (DSQL) zapytania z [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Gdy plan DSQL trwa dłużej niż oczekiwano, przyczyną może być złożony plan z wieloma krokami DSQL lub tylko jeden krok trwający.  Jeśli plan zawiera wiele kroków o kilku operacjach przenoszenia, należy rozważyć optymalizację dystrybucji tabel, aby zmniejszyć przenoszenie danych. W artykule dotyczącym [dystrybucji tabeli](sql-data-warehouse-tables-distribute.md) wyjaśniono, dlaczego należy przenieść dane w celu rozwiązania problemu. W tym artykule opisano również pewne strategie dystrybucji, które minimalizują przenoszenie danych.

Aby zbadać dalsze szczegóły dotyczące pojedynczego kroku, kolumny *operation_type* długotrwałego zapytania i zanotuj **indeks kroku**:

* Przejdź do kroku 3 dla **operacji SQL**: onoperation, RemoteOperation, ReturnOperation.
* Przejdź do kroku 4 dla **operacji przenoszenia danych**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3-investigate-sql-on-the-distributed-databases"></a>Krok 3. Badanie SQL w rozproszonych bazach danych

Użyj identyfikatora żądania i indeksu kroku, aby pobrać szczegóły z [sys.dm_pdw_sql_requests](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), który zawiera informacje o wykonywaniu kroku zapytania dla wszystkich rozproszonych baz danych.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Gdy krok zapytania jest uruchomiony, [polecenie DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) może zostać użyte do pobrania SQL Server szacowanego planu z pamięci podręcznej planu SQL Server dla kroku uruchomionego w określonej dystrybucji.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL pool or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-4-investigate-data-movement-on-the-distributed-databases"></a>Krok 4. Badanie przenoszenia danych w rozproszonych bazach danych

Użyj identyfikatora żądania i indeksu kroku, aby pobrać informacje o kroku przenoszenia danych uruchomionym w każdej dystrybucji z [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
-- Find information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Sprawdź kolumnę *total_elapsed_time* , aby sprawdzić, czy dana dystrybucja trwa znacznie dłużej niż inne do przenoszenia danych.
* W przypadku długotrwałej dystrybucji Sprawdź kolumnę *rows_processed* , aby sprawdzić, czy liczba wierszy przenoszonych z tej dystrybucji jest znacznie większa niż w przypadku innych. Jeśli tak, to znalezienie może wskazywać pochylenie danych źródłowych. Jedną z przyczyn pochylania danych jest dystrybucja w kolumnie z wieloma wartościami NULL (w których wszystkie wiersze będą w tej samej dystrybucji). Zapobiegaj powolnym kwerendom, unikając dystrybucji dla tych typów kolumn lub filtrowania zapytania, aby wyeliminować wartości NULL, jeśli jest to możliwe. 

Jeśli zapytanie jest uruchomione, można użyć [polecenia DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) do pobrania SQL Server szacowanego planu z pamięci podręcznej planu SQL Server dla aktualnie uruchomionego kroku SQL w ramach określonej dystrybucji.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL pool Compute or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Monitoruj oczekujące zapytania

Jeśli okaże się, że zapytanie nie postępuje, ponieważ oczekuje na zasób, w tym miejscu jest zapytanie, które pokazuje wszystkie zasoby, dla których zapytanie oczekuje.

```sql
-- Find queries
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Jeśli zapytanie aktywnie oczekuje na zasoby z innego zapytania, stan zostanie **AcquireResources**.  Jeśli zapytanie zawiera wszystkie wymagane zasoby, zostanie **udzielony**stan.

## <a name="monitor-tempdb"></a>Monitoruj bazę danych tempdb

Baza danych tempdb jest używana do przechowywania pośrednich wyników podczas wykonywania zapytania. Wysokie wykorzystanie bazy danych tempdb może prowadzić do powolnej wydajności zapytań. Dla każdego skonfigurowanego DW100c jest przydzielono 399 GB miejsca na przestrzeni tempdb (DW1000c będzie mieć 3,99 TB całkowitej ilości miejsca w bazie danych tempdb).  Poniżej znajdują się porady dotyczące monitorowania użycia bazy danych tempdb oraz zmniejszania użycia bazy danych tempdb w zapytaniach.

### <a name="monitoring-tempdb-with-views"></a>Monitorowanie bazy danych tempdb z widokami

Aby monitorować użycie bazy danych tempdb, najpierw zainstaluj widok [Microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) z [zestawu Microsoft Toolkit dla puli SQL](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring). Następnie można wykonać następujące zapytanie, aby zobaczyć użycie bazy danych tempdb na węzeł dla wszystkich wykonanych zapytań:

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN microsoft.vw_sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa'
ORDER BY sr.request_id;
```

Jeśli masz zapytanie, które zużywa dużą ilość pamięci lub otrzymasz komunikat o błędzie związany z przydzieleniem bazy danych tempdb, może to być spowodowane bardzo dużym [CREATE TABLE jak SELECT (CTAs)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) lub [Wstawianie instrukcji SELECT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) , która kończy się niepowodzeniem w końcowej operacji przenoszenia danych. Może to być zwykle identyfikowane jako operacja ShuffleMove w planie zapytania rozproszonego bezpośrednio przed ostatnim WSTAWIANIEm.  Użyj [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) , aby monitorować operacje ShuffleMove.

Najbardziej typowym środkiem ograniczającym jest przerwanie CTAS lub wstawianie instrukcji SELECT do wielu instrukcji obciążenia, tak aby wolumin danych nie przekroczy limitu 1 TB na węzeł w ramach platformy tempdb. Możesz również skalować klaster do większego rozmiaru, co spowoduje rozłożenie rozmiaru bazy danych tempdb na więcej węzłów, zmniejszając bazę danych tempdb w każdym węźle.

Oprócz instrukcji CTAS i INSERT SELECT, duże, złożone zapytania z niewystarczającą ilością pamięci można rozlać do bazy danych tempdb, powodując niepowodzenie zapytań.  Rozważ uruchomienie z większą [klasą zasobów](resource-classes-for-workload-management.md) , aby uniknąć rozlewania w bazie danych tempdb.

## <a name="monitor-memory"></a>Monitoruj pamięć

Pamięć może być główną przyczyną niskiej wydajności i braku pamięci. Rozważ przeskalowanie magazynu danych, jeśli okaże się, że SQL Server użycie pamięci osiągnie swoje limity podczas wykonywania zapytania.

Następujące zapytanie zwraca SQL Server użycie pamięci i wykorzystanie pamięci na węzeł:

```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB,
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```

## <a name="monitor-transaction-log-size"></a>Monitorowanie rozmiaru dziennika transakcji

Poniższe zapytanie zwraca rozmiar dziennika transakcji dla każdej dystrybucji. Jeśli jeden z plików dziennika osiągnie 160 GB, należy rozważyć skalowanie w górę wystąpienia lub ograniczenie rozmiaru transakcji.

```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id
FROM sys.dm_pdw_nodes_os_performance_counters
WHERE
instance_name like 'Distribution_%'
AND counter_name = 'Log File(s) Used Size (KB)'
```

## <a name="monitor-transaction-log-rollback"></a>Monitorowanie wycofywania dziennika transakcji

Jeśli wykonywanie zapytań kończy się niepowodzeniem lub trwa zbyt długo, możesz sprawdzić i monitorować, czy są wycofywane transakcje.

```sql
-- Monitor rollback
SELECT
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="monitor-polybase-load"></a>Monitoruj obciążenie wielopodstawowe

Następujące zapytanie zawiera przybliżony szacunek postępu ładowania. Zapytanie wyświetla tylko aktualnie przetwarzane pliki.

```sql

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat widoków DMV, zobacz [widoki systemowe](../sql/reference-tsql-system-views.md).
