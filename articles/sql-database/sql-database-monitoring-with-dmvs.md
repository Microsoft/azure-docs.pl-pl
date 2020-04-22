---
title: Monitorowanie wydajności za pomocą dmv
description: Dowiedz się, jak wykrywać i diagnozować typowe problemy z wydajnością, korzystając z dynamicznych widoków zarządzania do monitorowania bazy danych SQL platformy Microsoft Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 04/19/2020
ms.openlocfilehash: 6f33f49be74419a8f0cd31d973d64798f5d76a2c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683019"
---
# <a name="monitoring-performance-azure-sql-database-using-dynamic-management-views"></a>Monitorowanie wydajności usługi Azure SQL Database przy użyciu dynamicznych widoków zarządzania

Microsoft Azure SQL Database umożliwia podzbiór widoków zarządzania dynamicznego do diagnozowania problemów z wydajnością, które mogą być spowodowane przez zablokowane lub długotrwałe kwerendy, wąskie gardła zasobów, plany kwerend ubogich i tak dalej. Ten temat zawiera informacje na temat wykrywania typowych problemów z wydajnością przy użyciu dynamicznych widoków zarządzania.

Baza danych SQL częściowo obsługuje trzy kategorie dynamicznych widoków zarządzania:

- Widoki dynamicznego zarządzania związane z bazami danych.
- Widoki dynamicznego zarządzania związane z wykonaniem.
- Dynamiczne widoki zarządzania związane z transakcjami.

Aby uzyskać szczegółowe informacje na temat dynamicznych widoków zarządzania, zobacz [Dynamiczne widoki zarządzania i funkcje (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) w programie SQL Server Books Online.

## <a name="permissions"></a>Uprawnienia

W bazie danych SQL wykonywanie zapytań o dynamiczny widok zarządzania wymaga uprawnień **VIEW DATABASE STATE.** Uprawnienie **WYŚWIETL STAN BAZY DANYCH** zwraca informacje o wszystkich obiektach w bieżącej bazie danych.
Aby udzielić uprawnienia **VIEW DATABASE STATE** określonemu użytkownikowi bazy danych, uruchom następującą kwerendę:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

W przypadku lokalnego programu SQL Server dynamiczne widoki zarządzania zwracają informacje o stanie serwera. W bazie danych SQL zwracają informacje dotyczące bieżącej bazy danych logicznej tylko.

Ten artykuł zawiera kolekcję zapytań DMV, które można wykonać przy użyciu programu SQL Server Management Studio lub usługi Azure Data Studio w celu wykrycia następujących typów problemów z wydajnością kwerendy:

- [Identyfikowanie zapytań związanych z nadmiernym zużyciem procesora CPU](#identify-cpu-performance-issues)
- [PAGELATCH_* i WRITE_LOG czeka związane z wąskich gardeł we/wy](#identify-io-performance-issues)
- [PAGELATCH_* czeka spowodowane przeztTempDB rywalizacji](#identify-tempdb-performance-issues)
- [RESOURCE_SEMAHPORE czeka spowodowane problemami z oczekiwaniem na przyznanie pamięci](#identify-memory-grant-wait-performance-issues)
- [Identyfikowanie rozmiarów bazy danych i obiektów](#calculating-database-and-objects-sizes)
- [Pobieranie informacji o aktywnych sesjach](#monitoring-connections)
- [Pobieranie informacji o użyciu zasobów w całym systemie i w zasobach bazy danych](#monitor-resource-use)
- [Pobieranie informacji o wydajności kwerendy](#monitoring-query-performance)

## <a name="identify-cpu-performance-issues"></a>Identyfikowanie problemów z wydajnością procesora

Jeśli zużycie procesora CPU przekracza 80% przez dłuższy czas, należy wziąć pod uwagę następujące kroki rozwiązywania problemów:

### <a name="the-cpu-issue-is-occurring-now"></a>Problem z procesorem występuje teraz

Jeśli problem występuje w tej chwili, istnieją dwa możliwe scenariusze:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Wiele pojedynczych zapytań, które łącznie zużywają wysoki poziom procesora

Użyj następującej kwerendy, aby zidentyfikować najważniejsze skróty kwerendy:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM (SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
    FROM (SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
                CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
    GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>Długo działające kwerendy, które zużywają procesor CPU, są nadal uruchomione

Użyj następującej kwerendy, aby zidentyfikować te kwerendy:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
    CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>Problem z procesorem wystąpił w przeszłości

Jeśli problem wystąpił w przeszłości i chcesz wykonać analizę przyczyn źródłowych, użyj [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). Użytkownicy z dostępem do bazy danych mogą używać funkcji T-SQL do wykonywania zapytań o dane magazynu zapytań. Domyślne konfiguracje magazynu zapytań używają szczegółowości 1 godziny. Użyj następującej kwerendy, aby sprawdzić aktywność dla kwerend zużywających procesor CPU o wysokiej wydajności. Ta kwerenda zwraca 15 najlepszych zapytań zużywających procesor CPU. Pamiętaj, `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()`aby zmienić:

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

Po zidentyfikowaniu kwerend problematycznych, nadszedł czas, aby dostroić te zapytania, aby zmniejszyć wykorzystanie procesora CPU.  Jeśli nie masz czasu, aby dostroić zapytania, można również uaktualnić SLO bazy danych, aby obejść ten problem.

## <a name="identify-io-performance-issues"></a>Identyfikowanie problemów z wydajnością we/wy

Podczas identyfikowania problemów z wydajnością we/wy najważniejsze typy oczekiwania skojarzone z problemami we/wy to:

- `PAGEIOLATCH_*`

  W przypadku problemów z `PAGEIOLATCH_SH`we/wy pliku danych (w tym , `PAGEIOLATCH_EX`, `PAGEIOLATCH_UP`).  Jeśli nazwa typu oczekiwania ma **we/wy we/wy,** wskazuje na problem we/wy. Jeśli w nazwie oczekiwania zatrzaśnięcia strony nie ma żadnych operacji **we/wy,** wskazuje ona inny typ problemu (na przykład rywalizacja tempdb).

- `WRITE_LOG`

  W przypadku problemów z we/wy dziennika transakcji.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Jeśli problem we/wy występuje właśnie teraz

Użyj [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) lub [sys.dm_os_waiting_tasks,](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) aby zobaczyć `wait_type` i `wait_time`.

#### <a name="identify-data-and-log-io-usage"></a>Identyfikowanie danych i logów użycia we/wy

Użyj następującej kwerendy do identyfikowania danych i log użycia we/wy. Jeśli we/wy danych lub dziennika jest powyżej 80%, oznacza to, że użytkownicy korzystali z dostępnych we/wy dla warstwy usług bazy danych SQL.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Jeśli limit we/wy został osiągnięty, dostępne są dwie opcje:

- Opcja 1: Uaktualnianie rozmiaru obliczeniowego lub warstwy usługi
- Opcja 2: Identyfikowanie i dostrajanie zapytań zużywających najwięcej we/wy.

#### <a name="view-buffer-related-io-using-the-query-store"></a>Wyświetlanie we/wy związanej z buforem przy użyciu Magazynu zapytań

Dla opcji 2 można użyć następującej kwerendy względem Magazynu zapytań dla we/wy związanej z buforem, aby wyświetlić ostatnie dwie godziny śledzonej aktywności:

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>Wyświetl całkowity dziennik We/Wy dla oczekiwania WRITELOG

Jeśli typem `WRITELOG`oczekiwania jest , użyj następującej kwerendy, aby wyświetlić sumę dziennika we/wy przez instrukcję:

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>Identyfikowanie `tempdb` problemów z wydajnością

Podczas identyfikowania problemów z wydajnością we/wy najważniejsze typy oczekiwania skojarzone z `tempdb` problemami są `PAGELATCH_*` (nie). `PAGEIOLATCH_*` Jednak `PAGELATCH_*` czeka nie zawsze oznacza, `tempdb` że masz sporów.  To oczekiwanie może również oznaczać, że masz rywalizację o stronę danych obiektu użytkownika z powodu równoczesnych żądań kierowanych na tę samą stronę danych. Aby dodatkowo `tempdb` potwierdzić rywalizację, użyj [pliku sys.dm_exec_requests,](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) aby potwierdzić, że `tempdb` wartość wait_resource zaczyna `x` się od tego, `y` `2:x:y` gdzie znajduje się 2, jest identyfikatorem bazy danych, identyfikatorem pliku i identyfikatorem strony.  

W przypadku rywalizacji tempdb powszechną metodą jest zmniejszenie `tempdb`lub ponowne zapisanie kodu aplikacji, który opiera się na .  Typowe `tempdb` obszary użytkowania obejmują:

- Tabele tymczasowe
- Zmienne tabeli
- Parametry wycenione w tabeli
- Użycie magazynu wersji (w szczególności związane z długotrwałymi transakcjami)
- Kwerendy, które mają plany kwerend, które używają sortowania, sprzężeń mieszania i buforów

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>Najpopularniejsze kwerendy korzystające ze zmiennych tabel i tabel tymczasowych

Użyj następującej kwerendy, aby zidentyfikować najważniejsze kwerendy korzystające ze zmiennych tabeli i tabel tymczasowych:

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>Identyfikowanie długotrwałych transakcji

Użyj następującej kwerendy, aby zidentyfikować długotrwałe transakcje. Długotrwałe transakcje uniemożliwiają oczyszczanie magazynu wersji.

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>Identyfikowanie problemów z wydajnością oczekiwania na przyznanie dotacji pamięci

Jeśli twój najwyższy `RESOURCE_SEMAHPORE` typ oczekiwania jest i nie masz problemu z wysokim użyciem procesora CPU, może to być problem z oczekiwaniem dotacji pamięci.

### <a name="determine-if-a-resource_semahpore-wait-is-a-top-wait"></a>Określanie, `RESOURCE_SEMAHPORE` czy oczekiwanie jest najlepszym oczekiwaniem

Użyj następującej kwerendy, `RESOURCE_SEMAHPORE` aby ustalić, czy oczekiwanie jest

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identity-high-memory-consuming-statements"></a>Tożsamości wysokiej pamięci zużywa instrukcji

Użyj następującej kwerendy, aby zidentyfikować instrukcje zużywające dużą ilość pamięci:

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>Określ 10 najważniejszych dotacji na pamięć aktywną

Użyj następującej kwerendy, aby zidentyfikować 10 najważniejszych dotacji pamięci aktywnej:

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>Obliczanie rozmiarów bazy danych i obiektów

Następująca kwerenda zwraca rozmiar bazy danych (w megabajtach):

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

Następująca kwerenda zwraca rozmiar poszczególnych obiektów (w megabajtach) w bazie danych:

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Monitorowanie połączeń

Można użyć widoku [sys.dm_exec_connections,](https://msdn.microsoft.com/library/ms181509.aspx) aby pobrać informacje o połączeniach nawiązanych z określonym serwerem bazy danych SQL platformy Azure i szczegóły każdego połączenia. Ponadto widok [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) jest przydatny podczas pobierania informacji o wszystkich aktywnych połączeniach użytkowników i zadaniach wewnętrznych.
Następująca kwerenda pobiera informacje o bieżącym połączeniu:

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> Podczas wykonywania **widoków sys.dm_exec_requests** i **sys.dm_exec_sessions,** jeśli masz uprawnienia **VIEW DATABASE STATE** w bazie danych, zobaczysz wszystkie sesje wykonywania w bazie danych; w przeciwnym razie zobaczysz tylko bieżącą sesję.

## <a name="monitor-resource-use"></a>Monitorowanie wykorzystania zasobów

Użycie zasobów można monitorować za pomocą [usługi SQL Database Query Performance Insight](sql-database-query-performance.md) i Query [Store](https://msdn.microsoft.com/library/dn817826.aspx).

Można również monitorować użycie przy użyciu tych dwóch widoków:

- [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdm_db_resource_stats"></a>sys.dm_db_resource_stats

Można użyć [widoku sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) w każdej bazie danych SQL. Widok **sys.dm_db_resource_stats** pokazuje dane użycia zasobów ostatnich względem warstwy usług. Średnie wartości procentowe procesora CPU, we/wy danych, zapisów dziennika i pamięci są rejestrowane co 15 sekund i są utrzymywane przez 1 godzinę.

Ponieważ ten widok zapewnia bardziej szczegółowe spojrzenie na wykorzystanie zasobów, użyj **sys.dm_db_resource_stats** najpierw dla każdej analizy bieżącego stanu lub rozwiązywania problemów. Na przykład ta kwerenda pokazuje średnie i maksymalne użycie zasobów dla bieżącej bazy danych w ciągu ostatniej godziny:

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

Aby zapoznać się z innymi kwerendami, zobacz przykłady w [pliku sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresource_stats"></a>sys.resource_stats

Widok [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) w **głównej** bazie danych ma dodatkowe informacje, które mogą pomóc w monitorowaniu wydajności bazy danych SQL w określonej warstwie usług i rozmiarze obliczeń. Dane są zbierane co 5 minut i są przechowywane przez około 14 dni. Ten widok jest przydatny w przypadku długoterminowej analizy historycznej sposobu używania zasobów przez bazę danych SQL.

Na poniższym wykresie przedstawiono użycie zasobów procesora CPU dla bazy danych premium o rozmiarze obliczeniowym P2 dla każdej godziny w tygodniu. Ten wykres rozpoczyna się w poniedziałek, pokazuje 5 dni roboczych, a następnie pokazuje weekend, gdy znacznie mniej dzieje się w aplikacji.

![Użycie zasobów bazy danych SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Z danych ta baza danych ma obecnie szczytowe obciążenie procesora CPU nieco ponad 50 procent użycia procesora w stosunku do rozmiaru obliczeniowego P2 (południe we wtorek). Jeśli procesor CPU jest czynnikiem dominującym w profilu zasobów aplikacji, można zdecydować, że P2 jest odpowiedni rozmiar obliczeń, aby zagwarantować, że obciążenie zawsze pasuje. Jeśli oczekujesz, że aplikacja będzie rosnąć wraz z czasem, to dobry pomysł, aby mieć dodatkowy bufor zasobów, tak aby aplikacja nigdy nie osiągnie limitu poziomu wydajności. Jeśli zwiększysz rozmiar obliczeń, można uniknąć błędów widocznych dla klienta, które mogą wystąpić, gdy baza danych nie ma wystarczającej mocy do skutecznego przetwarzania żądań, szczególnie w środowiskach wrażliwych na opóźnienia. Przykładem jest baza danych, która obsługuje aplikację, która maluje strony sieci Web na podstawie wyników wywołań bazy danych.

Inne typy aplikacji może interpretować ten sam wykres inaczej. Na przykład jeśli aplikacja próbuje przetwarzać dane listy płac każdego dnia i ma ten sam wykres, tego rodzaju "zadanie wsadowe" model może zrobić dobrze w rozmiarze obliczeń P1. Rozmiar obliczeń P1 ma 100 jednostek DTU w porównaniu do 200 jednostek DTU w rozmiarze obliczeniowym P2. Rozmiar obliczeń P1 zapewnia połowę wydajności rozmiaru obliczeń P2. Tak więc 50 procent użycia procesora w P2 jest równe 100 procent wykorzystania procesora w P1. Jeśli aplikacja nie ma limitów czasu, może nie mieć znaczenia, jeśli zadanie trwa 2 godziny lub 2,5 godziny, aby zakończyć, jeśli zostanie wykonana dzisiaj. Aplikacja w tej kategorii prawdopodobnie można użyć rozmiaru obliczeń P1. Możesz skorzystać z faktu, że istnieją okresy czasu w ciągu dnia, gdy zużycie zasobów jest niższe, tak, że każdy "wielki szczyt" może rozlać się na jeden z koryt później w ciągu dnia. Rozmiar obliczeń P1 może być dobry dla tego rodzaju aplikacji (i zaoszczędzić pieniądze), tak długo, jak zadania mogą zakończyć się na czas każdego dnia.

Usługa Azure SQL Database udostępnia informacje o zużytych zasobach dla każdej aktywnej bazy danych w widoku **sys.resource_stats** **głównej** bazy danych na każdym serwerze. Dane w tabeli są agregowane dla interwałów 5-minutowych. W warstwach usługi Basic, Standard i Premium dane mogą zająć więcej niż 5 minut, aby pojawić się w tabeli, więc te dane są bardziej przydatne w przypadku analizy historycznej, a nie analizy w czasie zbliżonym do rzeczywistego. Kwerenda **sys.resource_stats** widok, aby zobaczyć najnowszą historię bazy danych i sprawdzić, czy rezerwacja, którą wybrałeś dostarczone wydajność, którą chcesz w razie potrzeby.

> [!NOTE]
> Musisz być połączony z **główną** bazą danych serwera bazy danych SQL, aby zbadać **plik sys.resource_stats** w poniższych przykładach.

W tym przykładzie pokazano, jak dane w tym widoku jest narażony:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Widok katalogu sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

W następnym przykładzie przedstawiono różne sposoby użycia widoku katalogu **sys.resource_stats** katalogu, aby uzyskać informacje o tym, jak baza danych SQL korzysta z zasobów:

1. Aby przyjrzeć się wykorzystaniu zasobów w zeszłym tygodniu dla bazy danych userdb1, można uruchomić tę kwerendę:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. Aby ocenić, jak dobrze obciążenie odpowiada rozmiarowi obliczeń, należy przejść do szczegółów każdego aspektu metryk zasobów: procesora CPU, odczyty, zapisy, liczba pracowników i liczba sesji. Oto poprawiona kwerenda **używająca pliku sys.resource_stats** do raportowania średnich i maksymalnych wartości tych metryk zasobów:

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. Dzięki tym informacjom o średnich i maksymalnych wartościach każdego zasobu można ocenić, jak dobrze obciążenie mieści się w wybranym rozmiarze obliczeń. Zazwyczaj średnie wartości z **pliku sys.resource_stats** zapewniają dobrą linię bazową do użycia względem rozmiaru docelowego. Powinien to być twój podstawowy kij pomiarowy. Na przykład może być przy użyciu standardowej warstwy usług o rozmiarze obliczeniowym S2. Średnie wartości procentowe użycia procesora CPU i we/wy odczytuje i zapisuje są poniżej 40 procent, średnia liczba pracowników jest poniżej 50, a średnia liczba sesji jest poniżej 200. Obciążenie może pasować do rozmiaru obliczeniowego S1. Łatwo sprawdzić, czy baza danych mieści się w limitach procesu roboczego i sesji. Aby sprawdzić, czy baza danych mieści się w mniejszym rozmiarze obliczeń w odniesieniu do procesora CPU, odczytuje i zapisuje, podziel numer JEDNOSTKI DTU o niższym rozmiarze obliczeń przez numer JEDNOSTKI DTU bieżącego rozmiaru obliczeń, a następnie pomnóż wynik przez 100:

    `S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40`

    Wynik jest względną różnicą wydajności między dwoma rozmiarami obliczeń w procentach. Jeśli użycie zasobów nie przekracza tej kwoty, obciążenie może pasować do niższego rozmiaru obliczeń. Należy jednak przyjrzeć się wszystkim zakresom wartości użycia zasobów i określić, według wartości procentowej, jak często obciążenie bazy danych będzie pasować do niższego rozmiaru obliczeń. Następująca kwerenda wyprowadza wartość procentową dopasowania na wymiar zasobu na podstawie progu 40 procent obliczonego w tym przykładzie:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Na podstawie warstwy usług bazy danych można zdecydować, czy obciążenie mieści się w mniejszym rozmiarze obliczeń. Jeśli cel obciążenia bazy danych wynosi 99,9 procent, a poprzednia kwerenda zwraca wartości większe niż 99,9 procent dla wszystkich trzech wymiarów zasobów, obciążenie prawdopodobnie mieści się w niższym rozmiarze obliczeń.

    Patrząc na procent dopasowania daje również wgląd w to, czy należy przejść do następnego wyższego rozmiaru obliczeń, aby osiągnąć swój cel. Na przykład userdb1 pokazuje następujące użycie procesora CPU w ciągu ostatniego tygodnia:

   | Średni procent procesora | Maksymalny procent procesora |
   | --- | --- |
   | 24.5 |100,00 |

    Średni procesor CPU wynosi około jednej czwartej limitu rozmiaru obliczeń, który dobrze pasuje do rozmiaru obliczeniowego bazy danych. Jednak maksymalna wartość pokazuje, że baza danych osiąga limit rozmiaru obliczeń. Czy musisz przejść do następnego większego rozmiaru obliczeń? Zobacz, ile razy obciążenie osiąga 100 procent, a następnie porównaj je z celem obciążenia bazy danych.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Jeśli ta kwerenda zwraca wartość mniejszą niż 99,9 procent dla dowolnego z trzech wymiarów zasobów, należy rozważyć przeniesienie do następnego większego rozmiaru obliczeń lub użyć technik dostrajania aplikacji, aby zmniejszyć obciążenie bazy danych SQL.

4. W tym ćwiczeniu uwzględnino również przewidywany wzrost obciążenia w przyszłości.

W przypadku pul elastycznych można monitorować pojedyncze bazy danych w puli za pomocą metod opisanych w tej sekcji. Można jednak również monitorować pulę jako całość. Informacje na ten temat znajdziesz w artykule [Monitor and manage an elastic pool](sql-database-elastic-pool-manage-portal.md) (Monitorowanie puli elastycznej i zarządzanie nią).

### <a name="maximum-concurrent-requests"></a>Maksymalna liczba równoczesnych żądań

Aby wyświetlić liczbę równoczesnych żądań, uruchom tę kwerendę Transact-SQL w bazie danych SQL:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R;
```

Aby przeanalizować obciążenie lokalnej bazy danych programu SQL Server, zmodyfikuj tę kwerendę, aby filtrować określoną bazę danych, którą chcesz przeanalizować. Na przykład jeśli masz lokalną bazę danych o nazwie MyDatabase, ta kwerenda Transact-SQL zwraca liczbę równoczesnych żądań w tej bazie danych:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R
INNER JOIN sys.databases D ON D.database_id = R.database_id
AND D.name = 'MyDatabase';
```

Jest to tylko migawka w jednym punkcie w czasie. Aby lepiej zrozumieć obciążenie i wymagania dotyczące równoczesnych żądań, musisz zebrać wiele przykładów w czasie.

### <a name="maximum-concurrent-logins"></a>Maksymalna liczba równoczesnych logowań

Można analizować wzorce użytkowników i aplikacji, aby zorientować się o częstotliwości logowania. Można również uruchomić rzeczywistych obciążeń w środowisku testowym, aby upewnić się, że nie trafisz tego lub innych limitów omówimy w tym artykule. Nie ma jednego zapytania lub dynamicznego widoku zarządzania (DMV), który może wyświetlać równoczesne liczby logowania lub historię.

Jeśli wielu klientów używa tego samego ciągu połączenia, usługa uwierzytelnia każdy login. Jeśli 10 użytkowników jednocześnie połączyć się z bazą danych przy użyciu tej samej nazwy użytkownika i hasła, będzie 10 równoczesnych logowania. Ten limit dotyczy tylko czasu logowania i uwierzytelniania. Jeśli te same 10 użytkowników połączyć się z bazą danych sekwencyjnie, liczba równoczesnych logowania nigdy nie będzie większa niż 1.

> [!NOTE]
> Obecnie ten limit nie ma zastosowania do baz danych w pulach elastycznych.

### <a name="maximum-sessions"></a>Maksymalna liczba sesji

Aby wyświetlić liczbę bieżących aktywnych sesji, uruchom tę kwerendę Transact-SQL w bazie danych SQL:

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections
```

Jeśli analizujesz lokalne obciążenie programu SQL Server, zmodyfikuj kwerendę, aby skupić się na określonej bazie danych. Ta kwerenda pomaga określić możliwe potrzeby sesji dla bazy danych, jeśli rozważasz przeniesienie go do usługi Azure SQL Database.

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections C
INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
INNER JOIN sys.databases D ON (D.database_id = S.database_id)
WHERE D.name = 'MyDatabase'
```

Ponownie te kwerendy zwracają liczbę punktów w czasie. Jeśli zbierzesz wiele próbek w czasie, będziesz mieć najlepszą wiedzę na temat użycia sesji.

W przypadku analizy bazy danych SQL można uzyskać historyczne statystyki dotyczące sesji, odpytując widok [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) i przeglądając **kolumnę active_session_count.**

## <a name="monitoring-query-performance"></a>Monitorowanie wydajności kwerendy

Powolne lub długotrwałe kwerendy mogą zużywać znaczne zasoby systemowe. W tej sekcji pokazano, jak używać dynamicznych widoków zarządzania do wykrywania kilku typowych problemów z wydajnością kwerend. Starszy, ale nadal pomocne odwołanie do rozwiązywania problemów, jest [rozwiązywanie problemów z wydajnością w programie SQL Server 2008](https://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) artykuł w witrynie Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Znajdowanie najlepszych zapytań N

Poniższy przykład zwraca informacje o pięciu pierwszych kwerendach uszeregowane według średniego czasu procesora CPU. W tym przykładzie agreguje kwerendy zgodnie z ich hash kwerendy, tak aby logicznie równoważne kwerendy są pogrupowane według ich skumulowanego zużycia zasobów.

```sql
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
     MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
FROM sys.dm_exec_query_stats AS QS
CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Monitorowanie zablokowanych zapytań

Powolne lub długotrwałe kwerendy mogą przyczyniać się do nadmiernego zużycia zasobów i być konsekwencją zablokowanych zapytań. Przyczyną blokowania może być zły projekt aplikacji, złe plany zapytań, brak przydatnych indeksów i tak dalej. Można użyć widoku sys.dm_tran_locks, aby uzyskać informacje o bieżącej aktywności blokowania w bazie danych SQL azure. Na przykład kod można znaleźć w [pliku sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) w aplikacji SQL Server Books Online.

### <a name="monitoring-query-plans"></a>Monitorowanie planów kwerend

Nieefektywny plan kwerend może również zwiększyć zużycie procesora CPU. W poniższym przykładzie użyto widoku [sys.dm_exec_query_stats,](https://msdn.microsoft.com/library/ms189741.aspx) aby określić, która kwerenda używa najbardziej skumulowanego procesora CPU.

```sql
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Zobacz też

[Wprowadzenie do usługi SQL Database](sql-database-technical-overview.md)
