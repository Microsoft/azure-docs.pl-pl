---
title: Monitorowanie obciążenia puli SQL przy użyciu dmv
description: Dowiedz się, jak monitorować obciążenie puli SQL usługi Azure Synapse Analytics i wykonywanie zapytań przy użyciu modułów DMV.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/24/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: synapse-analytics
ms.openlocfilehash: 5360d91a17a7eee2dd0373ac311c79d22e085939
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416097"
---
# <a name="monitor-your-azure-synapse-analytics-sql-pool-workload-using-dmvs"></a>Monitorowanie obciążenia puli SQL usługi Azure Synapse Analytics przy użyciu dysków DMV

W tym artykule opisano sposób używania dynamicznych widoków zarządzania (DMV) do monitorowania obciążenia, w tym badania wykonywania zapytań w puli SQL.

## <a name="permissions"></a>Uprawnienia

Aby zbadać dmvs w tym artykule, należy **albo WYŚWIETL STAN BAZY DANYCH** lub **control** uprawnienia. Zazwyczaj przyznanie **view database state** jest preferowane uprawnienie, ponieważ jest znacznie bardziej restrykcyjne.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Monitorowanie połączeń

Wszystkie loginy do magazynu danych są rejestrowane na [sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Ten DMV zawiera ostatnie 10 000 loginów.  session_id jest kluczem podstawowym i jest przypisywany sekwencyjnie dla każdego nowego logowania.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Monitorowanie wykonywania kwerendy

Wszystkie zapytania wykonywane w puli SQL są rejestrowane w [pliku sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Ten DMV zawiera ostatnie 10 000 wykonanych zapytań.  request_id jednoznacznie identyfikuje każdą kwerendę i jest kluczem podstawowym dla tego DMV.  request_id jest przypisywany sekwencyjnie dla każdej nowej kwerendy i jest poprzedzony QID, co oznacza identyfikator zapytania.  Kwerenda ten DMV dla danego session_id pokazuje wszystkie zapytania dla danego logowania.

> [!NOTE]
> Procedury przechowywane używają wielu identyfikatorów żądań.  Identyfikatory żądań są przypisywane w kolejności.

Oto kroki, które należy wykonać w celu zbadania planów wykonywania kwerendy i razy dla określonej kwerendy.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>KROK 1: Zidentyfikuj zapytanie, które chcesz zbadać

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

Na podstawie poprzednich wyników kwerendy **zanotuj identyfikator żądania** kwerendy, którą chcesz zbadać.

Kwerendy w stanie **Zawieszone** mogą być umieszczane w kolejce z powodu dużej liczby aktywnych uruchomionych kwerend. Te zapytania pojawiają się również w [pliku sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) czeka na kwerendę z typem UserConcurrencyResourceType. Aby uzyskać informacje na temat limitów współbieżności, zobacz [Limity pamięci i współbieżności](memory-concurrency-limits.md) lub [Klasy zasobów do zarządzania obciążeniem](resource-classes-for-workload-management.md). Kwerendy mogą również czekać z innych powodów, takich jak blokady obiektów.  Jeśli zapytanie oczekuje na zasób, zobacz [Badanie kwerend oczekujących na zasoby](#monitor-waiting-queries) w dalszej części tego artykułu.

Aby uprościć wyszukiwanie kwerendy w tabeli [sys.dm_pdw_exec_requests,](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) użyj [funkcji LABEL,](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) aby przypisać komentarz do kwerendy, który można wyszukiwać w widoku sys.dm_pdw_exec_requests.

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

### <a name="step-2-investigate-the-query-plan"></a>KROK 2: Badanie planu kwerend

Użyj identyfikatora żądania, aby pobrać plan rozproszonego programu SQL (DSQL) kwerendy z [pliku sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Gdy plan DSQL trwa dłużej niż oczekiwano, przyczyną może być złożony plan z wieloma krokami DSQL lub tylko jeden krok, który zajmuje dużo czasu.  Jeśli plan jest wiele kroków z kilku operacji przenoszenia, należy rozważyć optymalizację dystrybucji tabeli w celu zmniejszenia przepływu danych. Artykuł [dystrybucji tabeli](sql-data-warehouse-tables-distribute.md) wyjaśnia, dlaczego dane muszą zostać przeniesione, aby rozwiązać kwerendę. W artykule wyjaśniono również niektóre strategie dystrybucji, aby zminimalizować przenoszenie danych.

Aby zbadać dalsze szczegóły dotyczące pojedynczego kroku, *operation_type* kolumnę długotrwałego kroku kwerendy i zanotuj **indeks kroku:**

* Przejdź do kroku 3a dla **operacji SQL:** OnOperation, RemoteOperation, ReturnOperation.
* Przejdź do kroku 3b dla **operacji przenoszenia danych:** ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3-investigate-sql-on-the-distributed-databases"></a>KROK 3: Badanie sql na rozproszonych baz danych

Użyj identyfikatora żądania i indeksu kroku, aby pobrać szczegóły z [pliku sys.dm_pdw_sql_requests](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), który zawiera informacje o wykonaniu kroku kwerendy we wszystkich rozproszonych bazach danych.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Gdy krok kwerendy jest uruchomiony, [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) może służyć do pobierania programu SQL Server szacowany plan z pamięci podręcznej planu programu SQL Server dla kroku uruchomionego w określonej dystrybucji.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL pool or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-4-investigate-data-movement-on-the-distributed-databases"></a>KROK 4: Badanie przepływu danych w rozproszonych bazach danych

Użyj identyfikatora żądania i indeksu kroku, aby pobrać informacje o kroku przenoszenia danych uruchomionym dla każdej dystrybucji z [pliku sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
-- Find information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Sprawdź *kolumnę total_elapsed_time,* aby sprawdzić, czy określona dystrybucja trwa znacznie dłużej niż inne dla przenoszenia danych.
* W przypadku dystrybucji długotrwałej sprawdź *kolumnę rows_processed,* aby sprawdzić, czy liczba wierszy przenoszonych z tej dystrybucji jest znacznie większa niż inne. Jeśli tak, to odkrycie może wskazywać na pochylenie danych źródłowych.

Jeśli kwerenda jest uruchomiona, można użyć [PDW_SHOWEXECUTIONPLAN DBCC,](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) aby pobrać plan szacowany programu SQL Server z pamięci podręcznej planu programu SQL Server dla aktualnie uruchomionego kroku SQL w określonej dystrybucji.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL pool Compute or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Monitorowanie zapytań oczekujących

Jeśli odkryjesz, że kwerenda nie robi postępu, ponieważ oczekuje na zasób, oto zapytanie, które pokazuje wszystkie zasoby, na które oczekuje zapytanie.

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

Jeśli kwerenda aktywnie czeka na zasoby z innej kwerendy, a następnie stan będzie **AcquireResources**.  Jeśli kwerenda ma wszystkie wymagane zasoby, stan zostanie **przyznany**.

## <a name="monitor-tempdb"></a>Monitor tempdb

Tempdb jest używany do przechowywania wyników pośrednich podczas wykonywania kwerendy. Wysokie wykorzystanie bazy danych tempdb może prowadzić do powolnej wydajności kwerendy. Dla każdego skonfigurowanego dw100c przydzielane jest 399 GB miejsca tempdb (DW1000c miałoby 3,99 TB całkowitej przestrzeni tempdb).  Poniżej znajdują się wskazówki dotyczące monitorowania użycia tempdb i zmniejszania użycia tempdb w zapytaniach.

### <a name="monitoring-tempdb-with-views"></a>Monitorowanie tempdb z widokami

Aby monitorować użycie bazy danych tempdb, należy najpierw zainstalować widok [microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) z [puli narzędzi Microsoft Toolkit for SQL](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring). Następnie można wykonać następującą kwerendę, aby wyświetlić użycie tempdb na węzeł dla wszystkich wykonanych kwerend:

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

Jeśli masz kwerendę, która zużywa dużą ilość pamięci lub otrzymał komunikat o błędzie związane z alokacji tempdb, może to być spowodowane bardzo dużą [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) lub [WSTAWIĆ SELECT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) instrukcji uruchomione, która kończy się niepowodzeniem w końcowej operacji przenoszenia danych. Zazwyczaj można to zidentyfikować jako operację ShuffleMove w rozproszonym planie kwerend tuż przed ostatecznym insert select.  Użyj [pliku sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) do monitorowania operacji ShuffleMove.

Najczęstszym środkiem zaradczym jest przerwanie instrukcji CTAS lub INSERT SELECT na wiele instrukcji obciążenia, aby wolumin danych nie przekraczał limitu 1 TB na węzeł tempdb. Można również skalować klastra do większego rozmiaru, który będzie rozłożyć rozmiar bazy danych tempdb na więcej węzłów zmniejszając tempdb w każdym węźle.

Oprócz instrukcji CTAS i INSERT SELECT duże, złożone kwerendy uruchomione z niewystarczającą ilością pamięci mogą rozlać się do bazy danych tempdb, powodując niepowodzenie kwerend.  Należy rozważyć uruchomienie z większą [klasą zasobów,](resource-classes-for-workload-management.md) aby uniknąć rozlania do tempdb.

## <a name="monitor-memory"></a>Pamięć monitora

Pamięć może być główną przyczyną powolnej wydajności i problemów z pamięcią. Należy rozważyć skalowanie magazynu danych, jeśli znajdziesz użycie pamięci programu SQL Server osiągając jego limity podczas wykonywania kwerendy.

Następująca kwerenda zwraca użycie pamięci i ciśnienie pamięci programu SQL Server na węzeł:

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

Następująca kwerenda zwraca rozmiar dziennika transakcji dla każdej dystrybucji. Jeśli jeden z plików dziennika osiąga 160 GB, należy rozważyć skalowanie wystąpienia lub ograniczenie rozmiaru transakcji.

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

Jeśli zapytania są nieskuteczne lub zajmuje dużo czasu, aby kontynuować, można sprawdzić i monitorować, czy masz żadnych transakcji wycofywania.

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

## <a name="monitor-polybase-load"></a>Monitorowanie obciążenia PolyBase

Poniższa kwerenda zawiera przybliżone oszacowanie postępu obciążenia. Kwerenda pokazuje tylko pliki aktualnie przetwarzane.

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

Aby uzyskać więcej informacji na temat dmvs, zobacz [Widoki systemu](../sql/reference-tsql-system-views.md).
