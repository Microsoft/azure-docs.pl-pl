---
title: Zrozumienie i rozwiązywanie problemów z blokowaniem usługi Azure SQL
titleSuffix: Azure SQL Database
description: Omówienie tematów dotyczących usługi Azure SQL Database dotyczących blokowania i rozwiązywania problemów.
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 3/02/2021
ms.openlocfilehash: 3d64336184450514d52095097343a4588213f111
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034901"
---
# <a name="understand-and-resolve-azure-sql-database-blocking-problems"></a>Zrozumienie i rozwiązywanie problemów z blokowaniem Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="objective"></a>Cel

W tym artykule opisano blokowanie w bazach danych Azure SQL i przedstawiono sposób rozwiązywania problemów i usuwania blokowania. 

W tym artykule termin połączenie dotyczy pojedynczej zalogowanej sesji bazy danych programu. Każde połączenie jest wyświetlane jako identyfikator sesji (SPID) lub session_id w wielu widoków dmvach. Każdy z tych identyfikatorów SPID jest często określany jako proces, chociaż nie jest to odrębny kontekst procesu w typowym sensie. Natomiast każdy identyfikator SPID składa się z zasobów serwera i struktur danych niezbędnych do obsługi żądań pojedynczego połączenia z danego klienta. Pojedyncza aplikacja kliencka może mieć jedno lub więcej połączeń. Z punktu widzenia Azure SQL Database nie ma różnicy między wieloma połączeniami z pojedynczej aplikacji klienckiej na pojedynczym komputerze klienckim i wieloma połączeniami z wielu aplikacji klienckich lub wielu komputerów klienckich; są one niepodzielne. Jedno połączenie może blokować inne połączenie, niezależnie od klienta źródłowego.

> [!NOTE]
> **Ta zawartość koncentruje się na Azure SQL Database.** Azure SQL Database jest oparta na najnowszej stabilnej wersji aparatu bazy danych Microsoft SQL Server, dlatego większość zawartości jest podobna, ale opcje rozwiązywania problemów i narzędzia mogą się różnić. Aby uzyskać więcej informacji na temat blokowania w SQL Server, zobacz [Omówienie i rozwiązywanie problemów z blokowaniem SQL Server](/troubleshoot/sql/performance/understand-resolve-blocking).

## <a name="understand-blocking"></a>Omówienie blokowania 
 
Blokowanie jest niemożliwym do uniknięcia i przez projektowanie cech dowolnego systemu zarządzania relacyjnymi bazami danych (RDBMS) z współbieżnością opartą na blokadzie. Jak wspomniano wcześniej, w SQL Server blokowanie ma miejsce, gdy jedna sesja utrzymuje blokadę dla określonego zasobu, a drugi Identyfikator SPID próbuje uzyskać typ blokady powodującej konflikt w tym samym zasobie. Zwykle przedział czasu, w którym pierwszy blok SPID blokuje zasób jest mały. Gdy sesja będąca właścicielem zwalnia blokadę, drugie połączenie jest następnie bezpłatne, aby uzyskać własną blokadę zasobu i kontynuować przetwarzanie. Jest to normalne zachowanie i może wystąpić wiele razy w ciągu dnia, bez zauważalnego wpływu na wydajność systemu.

Czas trwania i kontekst transakcji zapytania określają, jak długo blokady są przechowywane i w związku z tym wpływają na inne zapytania. Jeśli zapytanie nie jest wykonywane w ramach transakcji (i nie są używane żadne wskazówki blokady), blokady instrukcji SELECT będą przechowywane tylko dla zasobu w czasie, w którym jest w rzeczywistości odczytywane, a nie w zapytaniu. W przypadku instrukcji INSERT, UPDATE i DELETE blokady są przechowywane w czasie wykonywania zapytania, zarówno w przypadku spójności danych, jak i w razie potrzeby.

W przypadku zapytań wykonywanych w ramach transakcji czas trwania blokowania jest określany na podstawie typu zapytania, poziomu izolacji transakcji oraz tego, czy wskazówki blokady są używane w zapytaniu. Opis blokowania, wskazówek dotyczących blokady i poziomów izolacji transakcji znajduje się w następujących artykułach:

* [Blokowanie w aparacie bazy danych](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [Dostosowywanie blokowania i przechowywania wersji wierszy](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#customizing-locking-and-row-versioning)
* [Tryby blokowania](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_modes)
* [Zablokuj zgodność](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_compatibility)
* [Transakcje](/sql/t-sql/language-elements/transactions-transact-sql)

W przypadku zablokowania i blokowania utrzymuje się w punkcie, w którym istnieje szkodliwy wpływ na wydajność systemu, jest to spowodowane jedną z następujących przyczyn:

* Identyfikator SPID przechowuje blokady zestawu zasobów przez dłuższy czas przed ich zwolnieniem. Ten typ blokowania jest rozpoznawany w czasie, ale może spowodować spadek wydajności.

* Identyfikator SPID utrzymuje blokady zestawu zasobów i nigdy go nie zwalnia. Ten typ blokowania nie rozwiązuje samego siebie i uniemożliwia dostęp do zasobów, których dotyczy problem.

W pierwszym scenariuszu sytuacja może być bardzo płynna, ponieważ różne identyfikatory SPID powodują blokowanie różnych zasobów z upływem czasu, tworząc element docelowy przemieszczania. W takich sytuacjach trudno jest rozwiązywać problemy przy użyciu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) , aby zawęzić problem do poszczególnych zapytań. Z kolei Druga sytuacja powoduje, że jest to spójny stan, który można łatwiej zdiagnozować.

## <a name="applications-and-blocking"></a>Aplikacje i blokowanie

W przypadku problemów z blokowaniem mogą wystąpić tendencje do skoncentrowania się na dostosowywaniu po stronie serwera i problemach z platformą. Jednak Uwaga płatna tylko do bazy danych może nie prowadzić do rozwiązania problemu i może pochłaniać czas i lepszą wydajność w trakcie badania aplikacji klienta i przesyłania zapytań. Niezależnie od tego, jaki poziom widoczności aplikacja ujawnia w odniesieniu do wykonywanych wywołań bazy danych, problem z blokowaniem często wymaga inspekcji dokładnej instrukcji SQL przesłanej przez aplikację oraz dokładnego zachowania aplikacji dotyczącej anulowania zapytań, zarządzania połączeniami, pobierania wszystkich wierszy wyników itd. Jeśli narzędzie programistyczne nie zezwala na jawną kontrolę nad zarządzaniem połączeniami, anulowanie zapytania, limit czasu zapytania, pobieranie wyników i tak dalej, nie można rozpoznać problemów z blokowaniem. Ten potencjał należy uważnie zbadać przed wybraniem narzędzia do tworzenia aplikacji dla Azure SQL Database, szczególnie w przypadku środowisk OLTP z uwzględnieniem wydajności. 

Zwróć uwagę na wydajność bazy danych w fazie projektowania i konstruowania bazy danych i aplikacji. W szczególności należy oszacować użycie zasobów, poziom izolacji i długość ścieżki transakcji dla każdego zapytania. Każde zapytanie i transakcja powinny być możliwie jak najuproszczone. Dobre dyscypliny zarządzania połączeniami muszą być wykonywane bez konieczności, aby aplikacja mogła mieć akceptowalną wydajność przy niskiej liczbie użytkowników, ale wydajność może znacząco wzrosnąć, ponieważ liczba użytkowników skaluje się w górę. 

W przypadku prawidłowego projektu aplikacji i zapytań Azure SQL Database jest w stanie obsłużyć wiele tysięcy jednoczesnych użytkowników na jednym serwerze z małym blokowaniem.

> [!Note]
> Aby uzyskać więcej wskazówek dotyczących tworzenia aplikacji, zobacz [Rozwiązywanie problemów z łącznością i inne błędy w Azure SQL Database i wystąpieniu zarządzanym usługi Azure SQL](troubleshoot-common-errors-issues.md) i [Obsługa błędów przejściowych](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling).

## <a name="troubleshoot-blocking"></a>Rozwiązywanie problemów z blokowaniem

Bez względu na to, która sytuacja blokowania znajduje się w, metodologia blokowania rozwiązywania problemów jest taka sama. Te podziały logiczne to to, co pociąga za resztę kompozycji tego artykułu. Koncepcja polega na znalezieniu bloku głównego i zidentyfikowaniu działania wykonywanego przez zapytanie i przyczyny jego blokowania. Po zidentyfikowaniu problematycznego zapytania (to znaczy, co to jest blokada przez czas dłuższy), następnym krokiem jest przeanalizowanie i określenie przyczyny zablokowania. Po zrozumieniu przyczyny możemy wprowadzić zmiany, przeprojektować zapytanie i transakcję.

Kroki rozwiązywania problemów:

1. Identyfikowanie głównej sesji blokowania (blok główny)

2. Znajdź zapytanie i transakcję, które powoduje blokowanie (co to jest blokada wstrzymania przez długi czas)

3. Analizuj/rozumiej, dlaczego jest wykonywane długotrwałe blokowanie

4. Rozwiąż problem z blokowaniem przez przeprojektowanie zapytania i transakcji

Teraz szczegółowemy się w celu omówienia głównej sesji blokowania przy użyciu odpowiedniego przechwytywania danych.

## <a name="gather-blocking-information"></a>Zbierz informacje o blokowaniu

Aby przeciwdziałać problemom z blokowaniem problemów, administrator bazy danych może używać skryptów SQL, które stale monitorują stan blokowania i blokowania w usłudze Azure SQL Database. Aby zebrać te dane, Istnieją zasadniczo dwie metody. 

Pierwszym z nich jest zbadanie obiektów dynamicznej zarządzania (DMOs) i zapisanie wyników do porównania w czasie. Niektóre obiekty, do których odwołuje się ten artykuł, to dynamiczne widoki zarządzania (widoków DMV), a niektóre są dynamicznymi funkcjami zarządzania (DMFs). Druga metoda polega na użyciu XEvents do przechwytywania działania wykonywanego przez program. 


## <a name="gather-information-from-dmvs"></a>Zbierz informacje z widoków DMV

Odwołanie widoków DMV do rozwiązywania problemów z blokowaniem ma na celu identyfikację identyfikatora SPID (identyfikatora sesji) na początku łańcucha blokującego i instrukcji SQL. Wyszukaj identyfikatory SPID poszkodowanych, które są blokowane. Jeśli którykolwiek z identyfikatorów SPID jest blokowany przez inny identyfikator SPID, zbadaj Identyfikator SPID będący właścicielem zasobu (blokujący SPID). Czy identyfikator SPID właściciela jest również blokowany? Można przeanalizować łańcuch, aby znaleźć blok główny, a następnie zbadać, dlaczego jest on utrzymując blokadę.

Należy pamiętać o uruchomieniu każdego z tych skryptów w docelowej bazie danych Azure SQL.

* Sp_who i sp_who2 polecenia są starszymi poleceniami, aby pokazać wszystkie bieżące sesje. DMV sys.dm_exec_sessions zwraca więcej danych w zestawie wyników, który jest łatwiejszy do zapytania i filtrowania. Sys.dm_exec_sessions można znaleźć na początku innych zapytań. 

* Jeśli masz już zidentyfikowaną konkretną sesję, możesz użyć, `DBCC INPUTBUFFER(<session_id>)` Aby znaleźć ostatnią instrukcję, która została przesłana przez sesję. Podobne wyniki można zwrócić za pomocą funkcji dynamicznego zarządzania sys.dm_exec_input_buffer (DMF), w zestawie wyników, który jest łatwiejszy do zapytania i filtrowania, co zapewnia session_id i request_id. Na przykład w celu zwrócenia najnowszej kwerendy przesłanej przez session_id 66 i request_id 0:

```sql
SELECT * FROM sys.dm_exec_input_buffer (66,0);
```

* Odwołaj się do sys.dm_exec_requests i odwołaj się do kolumny blocking_session_id. Gdy blocking_session_id = 0, sesja nie jest blokowana. Gdy sys.dm_exec_requests wyświetla tylko aktualnie wykonywane żądania, wszystkie połączenia (aktywne lub nie) zostaną wyświetlone w sys.dm_exec_sessions. Kompiluj dla tego wspólnego sprzężenia między sys.dm_exec_requests i sys.dm_exec_sessions w następnym zapytaniu.

* Uruchom to przykładowe zapytanie, aby znaleźć aktywne zapytania i ich bieżący tekst wsadowy lub tekst w buforze SQL przy użyciu [sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) lub [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) widoków DMV. Jeśli dane zwrócone przez `text` pole sys.dm_exec_sql_text ma wartość null, zapytanie nie jest aktualnie wykonywane. W takim przypadku `event_info` pole sys.dm_exec_input_buffer będzie zawierać ostatni ciąg polecenia przekazaną do aparatu SQL. Za pomocą tego zapytania można również identyfikować sesje blokujące inne sesje, w tym listę session_ids zablokowanych dla session_id. 

```sql
WITH cteBL (session_id, blocking_these) AS 
(SELECT s.session_id, blocking_these = x.blocking_these FROM sys.dm_exec_sessions s 
CROSS APPLY    (SELECT isnull(convert(varchar(6), er.session_id),'') + ', '  
                FROM sys.dm_exec_requests as er
                WHERE er.blocking_session_id = isnull(s.session_id ,0)
                AND er.blocking_session_id <> 0
                FOR XML PATH('') ) AS x (blocking_these)
)
SELECT s.session_id, blocked_by = r.blocking_session_id, bl.blocking_these
, batch_text = t.text, input_buffer = ib.event_info, * 
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
INNER JOIN cteBL as bl on s.session_id = bl.session_id
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) t
OUTER APPLY sys.dm_exec_input_buffer(s.session_id, NULL) AS ib
WHERE blocking_these is not null or r.blocking_session_id > 0
ORDER BY len(bl.blocking_these) desc, r.blocking_session_id desc, r.session_id;
```

* Uruchom to bardziej rozbudowane przykładowe zapytanie dostarczone przez pomoc techniczna firmy Microsoft, aby zidentyfikować nagłówek łańcucha blokowania wielu sesji, włącznie z tekstem zapytania sesji w łańcuchu blokującym.

```sql
WITH cteHead ( session_id,request_id,wait_type,wait_resource,last_wait_type,is_user_process,request_cpu_time
,request_logical_reads,request_reads,request_writes,wait_time,blocking_session_id,memory_usage
,session_cpu_time,session_reads,session_writes,session_logical_reads
,percent_complete,est_completion_time,request_start_time,request_status,command
,plan_handle,sql_handle,statement_start_offset,statement_end_offset,most_recent_sql_handle
,session_status,group_id,query_hash,query_plan_hash) 
AS ( SELECT sess.session_id, req.request_id, LEFT (ISNULL (req.wait_type, ''), 50) AS 'wait_type'
    , LEFT (ISNULL (req.wait_resource, ''), 40) AS 'wait_resource', LEFT (req.last_wait_type, 50) AS 'last_wait_type'
    , sess.is_user_process, req.cpu_time AS 'request_cpu_time', req.logical_reads AS 'request_logical_reads'
    , req.reads AS 'request_reads', req.writes AS 'request_writes', req.wait_time, req.blocking_session_id,sess.memory_usage
    , sess.cpu_time AS 'session_cpu_time', sess.reads AS 'session_reads', sess.writes AS 'session_writes', sess.logical_reads AS 'session_logical_reads'
    , CONVERT (decimal(5,2), req.percent_complete) AS 'percent_complete', req.estimated_completion_time AS 'est_completion_time'
    , req.start_time AS 'request_start_time', LEFT (req.status, 15) AS 'request_status', req.command
    , req.plan_handle, req.[sql_handle], req.statement_start_offset, req.statement_end_offset, conn.most_recent_sql_handle
    , LEFT (sess.status, 15) AS 'session_status', sess.group_id, req.query_hash, req.query_plan_hash
    FROM sys.dm_exec_sessions AS sess
    LEFT OUTER JOIN sys.dm_exec_requests AS req ON sess.session_id = req.session_id
    LEFT OUTER JOIN sys.dm_exec_connections AS conn on conn.session_id = sess.session_id 
    )
, cteBlockingHierarchy (head_blocker_session_id, session_id, blocking_session_id, wait_type, wait_duration_ms,
wait_resource, statement_start_offset, statement_end_offset, plan_handle, sql_handle, most_recent_sql_handle, [Level])
AS ( SELECT head.session_id AS head_blocker_session_id, head.session_id AS session_id, head.blocking_session_id
    , head.wait_type, head.wait_time, head.wait_resource, head.statement_start_offset, head.statement_end_offset
    , head.plan_handle, head.sql_handle, head.most_recent_sql_handle, 0 AS [Level]
    FROM cteHead AS head
    WHERE (head.blocking_session_id IS NULL OR head.blocking_session_id = 0)
    AND head.session_id IN (SELECT DISTINCT blocking_session_id FROM cteHead WHERE blocking_session_id != 0)
    UNION ALL
    SELECT h.head_blocker_session_id, blocked.session_id, blocked.blocking_session_id, blocked.wait_type,
    blocked.wait_time, blocked.wait_resource, h.statement_start_offset, h.statement_end_offset,
    h.plan_handle, h.sql_handle, h.most_recent_sql_handle, [Level] + 1
    FROM cteHead AS blocked
    INNER JOIN cteBlockingHierarchy AS h ON h.session_id = blocked.blocking_session_id and h.session_id!=blocked.session_id --avoid infinite recursion for latch type of blocking
    WHERE h.wait_type COLLATE Latin1_General_BIN NOT IN ('EXCHANGE', 'CXPACKET') or h.wait_type is null
    )
SELECT bh.*, txt.text AS blocker_query_or_most_recent_query 
FROM cteBlockingHierarchy AS bh 
OUTER APPLY sys.dm_exec_sql_text (ISNULL ([sql_handle], most_recent_sql_handle)) AS txt;
```

* Aby przechwytywać długotrwałe lub niezatwierdzone transakcje, użyj innego zestawu widoków DMV do wyświetlania bieżących otwartych transakcji, w tym [sys.dm_tran_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql), [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql), [sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql)i sys.dm_exec_sql_text. Istnieje kilka widoków DMV skojarzonych z transakcjami śledzenia. Zobacz tutaj więcej [widoków DMV transakcji](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql) . 

```sql
SELECT [s_tst].[session_id],
[database_name] = DB_NAME (s_tdt.database_id),
[s_tdt].[database_transaction_begin_time], 
[sql_text] = [s_est].[text] 
FROM sys.dm_tran_database_transactions [s_tdt]
INNER JOIN sys.dm_tran_session_transactions [s_tst] ON [s_tst].[transaction_id] = [s_tdt].[transaction_id]
INNER JOIN sys.dm_exec_connections [s_ec] ON [s_ec].[session_id] = [s_tst].[session_id]
CROSS APPLY sys.dm_exec_sql_text ([s_ec].[most_recent_sql_handle]) AS [s_est];
```

* [Sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) referencyjny, który znajduje się w warstwie wątku/zadania w programie SQL Server. Spowoduje to zwrócenie informacji o tym, co typ oczekiwania SQL jest aktualnie występują. Podobnie jak sys.dm_exec_requests, tylko aktywne żądania są zwracane przez sys.dm_os_waiting_tasks. 

> [!Note]
> Aby uzyskać więcej informacji na temat typów oczekiwania, łącznie z zagregowanymi statystykami oczekiwania w czasie, zobacz DMV [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database). Ta DMV zwraca statystykę odczekania agregacji tylko dla bieżącej bazy danych.

* Użyj [sys.dm_tran_locks](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql) DMV, aby uzyskać bardziej szczegółowe informacje na temat tego, jakie blokady zostały umieszczone przez zapytania. Ten DMV może zwracać duże ilości danych na SQL Server produkcyjnej i jest przydatny do diagnozowania, które blokady są obecnie przechowywane. 

Z powodu wewnętrznego SPRZĘŻENIa w sys.dm_os_waiting_tasks, następujące zapytanie ogranicza dane wyjściowe z sys.dm_tran_locks tylko do obecnie zablokowanych żądań, ich stanu oczekiwania i ich blokad:

```sql
SELECT table_name = schema_name(o.schema_id) + '.' + o.name
, wt.wait_duration_ms, wt.wait_type, wt.blocking_session_id, wt.resource_description
, tm.resource_type, tm.request_status, tm.request_mode, tm.request_session_id
FROM sys.dm_tran_locks AS tm
INNER JOIN sys.dm_os_waiting_tasks as wt ON tm.lock_owner_address = wt.resource_address
LEFT OUTER JOIN sys.partitions AS p on p.hobt_id = tm.resource_associated_entity_id
LEFT OUTER JOIN sys.objects o on o.object_id = p.object_id or tm.resource_associated_entity_id = o.object_id
WHERE resource_database_id = DB_ID()
AND object_name(p.object_id) = '<table_name>';
```

* Dzięki widoków DMV, przechowywanie wyników zapytania w czasie spowoduje udostępnienie punktów danych, które umożliwią sprawdzenie blokowania w określonym przedziale czasu w celu zidentyfikowania utrwalonego blokowania lub trendów. 

## <a name="gather-information-from-extended-events"></a>Zbierz informacje z zdarzeń rozszerzonych

Oprócz powyższych informacji często konieczne jest przechwycenie śledzenia działań na serwerze w celu dokładnego zbadania problemu blokującego na Azure SQL Database. Na przykład jeśli sesja wykonuje wiele instrukcji w ramach transakcji, zostanie reprezentowana tylko Ostatnia instrukcja, która została przesłana. Jednak jedną z wcześniejszych instrukcji może być to, że blokady przyczyn nadal są przechowywane. Śledzenie umożliwi wyświetlenie wszystkich poleceń wykonywanych przez sesję w ramach bieżącej transakcji.

Istnieją dwa sposoby przechwytywania śladów w SQL Server; Zdarzenia rozszerzone (XEvents) i ślady profilera. Jednak [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) jest przestarzała technologia śledzenia nie jest obsługiwana w przypadku Azure SQL Database. [Zdarzenia rozszerzone](/sql/relational-databases/extended-events/extended-events) to nowsza technologia śledzenia, która zapewnia większą uniwersalność i mniej wpływ na zaobserwowany system, a interfejs jest zintegrowany z SQL Server Management Studio (SSMS). 

Zapoznaj się z dokumentem zawierającym opis sposobu korzystania z [Kreatora nowej sesji zdarzeń rozszerzonych](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) w programie SSMS. W przypadku baz danych Azure SQL Database program SSMS udostępnia podfolder zdarzeń rozszerzonych pod każdą bazą danych w Eksplorator obiektów. Użyj Kreatora sesji zdarzeń rozszerzonych, aby przechwycić te przydatne zdarzenia: 

-   Błędy kategorii:
    -   Uwaga
    -   Error_reported  
    -   Execution_warning

-   Ostrzeżenia kategorii: 
    -   Missing_join_predicate

-   Wykonywanie kategorii:
    -   Rpc_completed
    -   Rpc_starting 
    -   Sql_batch_completed
    -   Sql_batch_starting

-   Blokuj
    -   Lock_deadlock

-   Sesja
    -   Existing_connection
    -   Zaloguj się
    -   Logout

## <a name="identify-and-resolve-common-blocking-scenarios"></a>Identyfikowanie i rozwiązywanie typowych scenariuszy blokowania

Sprawdzając poprzednie informacje, można określić przyczynę większości problemów z blokowaniem. Pozostała część tego artykułu zawiera omówienie sposobów korzystania z tych informacji w celu identyfikowania i rozwiązywania typowych scenariuszy blokowania. W tej dyskusji założono, że użyto skryptów blokowania (przywoływanych wcześniej) do przechwytywania informacji na temat blokowania identyfikatorów SPID i przechwycenia działania aplikacji przy użyciu sesji systemu XEvent.

## <a name="analyze-blocking-data"></a>Analizowanie danych blokowania 

* Sprawdź dane wyjściowe sys.dm_exec_requests widoków DMV i sys.dm_exec_sessions, aby określić głowice łańcuchów blokowania przy użyciu blocking_these i session_id. Będzie to jasno identyfikować, które żądania są blokowane i które blokuje. Przyjrzyj się dalszej do zablokowanych i blokowanych sesji. Czy istnieje wspólna lub główna blokada łańcucha blokującego? Prawdopodobnie współużytkują wspólną tabelę, a co najmniej jedna z sesji występujących w łańcuchu blokującym wykonuje operację zapisu. 

* Przejrzyj dane wyjściowe sys.dm_exec_requests i sys.dm_exec_sessions, aby uzyskać informacje na temat identyfikatorów SPID znajdujących się na końcu łańcucha blokującego. Wyszukaj następujące pola: 

    -    `sys.dm_exec_requests.status`  
    W tej kolumnie jest wyświetlany stan określonego żądania. Zwykle stan uśpienia wskazuje, że identyfikator SPID zakończył wykonywanie i oczekuje na przesłanie kolejnego zapytania lub partii przez aplikację. Stan możliwy do uruchomienia lub uruchomiona wskazuje, że identyfikator SPID przetwarza obecnie zapytanie. Poniższa tabela zawiera krótkie objaśnienia różnych wartości stanu.

    | Stan | Znaczenie |
    |:-|:-|
    | Tło | Identyfikator SPID uruchamia zadanie w tle, takie jak wykrywanie zakleszczenia, moduł zapisujący dzienników lub punkty kontrolne. |
    | Nocleg | Identyfikator SPID nie jest aktualnie wykonywany. Zazwyczaj oznacza to, że identyfikator SPID czeka na polecenie z aplikacji. |
    | Uruchomienie | Identyfikator SPID jest obecnie uruchomiony w harmonogramie. |
    | Możliwy do uruchomienia | Identyfikator SPID znajduje się w kolejce możliwy do uruchomienia harmonogramu i oczekuje na uzyskanie czasu planisty. |
    | Suspended | Identyfikator SPID czeka na zasób, taki jak blokada lub zatrzaśnięcie. | 
                       
    -    `sys.dm_exec_sessions.open_transaction_count`  
    To pole informuje o liczbie otwartych transakcji w tej sesji. Jeśli ta wartość jest większa niż 0, identyfikator SPID znajduje się w otwartej transakcji i może być blokowany przez każdą instrukcję w ramach transakcji.

    -    `sys.dm_exec_requests.open_transaction_count`  
    Podobnie to pole informuje o liczbie otwartych transakcji w ramach tego żądania. Jeśli ta wartość jest większa niż 0, identyfikator SPID znajduje się w otwartej transakcji i może być blokowany przez każdą instrukcję w ramach transakcji.

    -   `sys.dm_exec_requests.wait_type`, `wait_time` i `last_wait_type`  
    Jeśli  `sys.dm_exec_requests.wait_type`   ma wartość null, żądanie nie jest aktualnie oczekujące na wszystkie i  `last_wait_type`   wartość wskazuje ostatnią informację  `wait_type`   o napotkaniu żądania. Aby uzyskać więcej informacji na temat  `sys.dm_os_wait_stats` i opis najpopularniejszych typów oczekiwania, zobacz [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).  `wait_time`   Wartość może służyć do określenia, czy żądanie jest wykonywane. Gdy zapytanie względem tabeli sys.dm_exec_requests zwraca wartość w  `wait_time`   kolumnie, która jest mniejsza niż  `wait_time`   wartość z poprzedniej kwerendy sys.dm_exec_requests, oznacza to, że wcześniejsza Blokada została pobrana i wydana i teraz oczekuje na nową blokadę (przy założeniu, że jest różna od zera `wait_time` ). Można to zweryfikować `wait_resource`   , porównując między sys.dm_exec_requests dane wyjściowe, które wyświetla zasób, dla którego żądanie oczekuje.

    -   `sys.dm_exec_requests.wait_resource` To pole wskazuje zasób, w którym oczekiwane jest żądanie zablokowane. W poniższej tabeli wymieniono typowe  `wait_resource`   formaty i ich znaczenie:

    | Zasób | Format | Przykład | Wyjaśnienie | 
    |:-|:-|:-|:-|
    | Tabela | DatabaseID: ObjectID: IndexID | KARTA: 5:261575970:1 | W takim przypadku baza danych o IDENTYFIKATORze 5 to przykładowa baza danych pubs i identyfikator obiektu 261575970 jest tabelą tytułów i 1 jest indeksem klastrowanym. |
    | Strona | DatabaseID: FileID: PageID | STRONA: 5:1:104 | W takim przypadku baza danych o IDENTYFIKATORze 5 to pubs, plik o IDENTYFIKATORze 1 jest podstawowym plikiem danych, a strona 104 jest stroną należącą do tabeli titles. Aby zidentyfikować object_id, do którego należy strona, użyj dynamicznej funkcji zarządzania [sys.dm_db_page_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql), przekazując w DatabaseID, fileid, pageid z `wait_resource` . | 
    | Klucz | DatabaseID: Hobt_id (wartość skrótu dla klucza indeksu) | KLUCZ: 5:72057594044284928 (3300a4f361aa) | W takim przypadku baza danych o IDENTYFIKATORze 5 to pubs, Hobt_ID 72057594044284928 odpowiada index_id 2 dla object_id 261575970 (tytuł tabeli). Użyj widoku wykazu sys. partitions, aby skojarzyć hobt_id z konkretną index_id i object_id. Nie istnieje sposób, aby skrócić skrót klucza indeksu do określonej wartości klucza. |
    | Wiersz | DatabaseID: FileID: PageID: slot (wiersz) | IDENTYFIKATOR RID: 5:1:104:3 | W takim przypadku baza danych o IDENTYFIKATORze 5 to pubs, plik o IDENTYFIKATORze 1 jest podstawowym plikiem danych, strona 104 jest stroną należącym do tabeli Tytuły, a gniazdo 3 wskazuje położenie wiersza na stronie. |
    | Opracowania  | DatabaseID: FileID: PageID: slot (wiersz) | IDENTYFIKATOR RID: 5:1:104:3 | W takim przypadku baza danych o IDENTYFIKATORze 5 to pubs, plik o IDENTYFIKATORze 1 jest podstawowym plikiem danych, strona 104 jest stroną należącym do tabeli Tytuły, a gniazdo 3 wskazuje położenie wiersza na stronie. |

    -    `sys.dm_tran_active_transactions`[Sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql) DMV zawiera dane dotyczące otwartych transakcji, które można przyłączyć do innych widoków DMV w celu uzyskania pełnego obrazu transakcji oczekujących na zatwierdzenie lub wycofanie. Użyj następującego zapytania, aby zwrócić informacje o otwartych transakcjach, przyłączone do innych widoków DMV, w tym [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql). Należy wziąć pod uwagę bieżący stan transakcji, `transaction_begin_time` oraz inne dane dotyczące sytuacji, aby oszacować, czy może być źródłem blokowania.

    ```sql
    SELECT tst.session_id, [database_name] = db_name(s.database_id)
    , tat.transaction_begin_time
    , transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime()) 
    , transaction_type = CASE tat.transaction_type  WHEN 1 THEN 'Read/write transaction'
                                                    WHEN 2 THEN 'Read-only transaction'
                                                    WHEN 3 THEN 'System transaction'
                                                    WHEN 4 THEN 'Distributed transaction' END
    , input_buffer = ib.event_info, tat.transaction_uow     
    , transaction_state  = CASE tat.transaction_state    
                WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                WHEN 1 THEN 'The transaction has been initialized but has not started.'
                WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                WHEN 4 THEN 'The commit process has been initiated on the distributed transaction.'
                WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                WHEN 6 THEN 'The transaction has been committed.'
                WHEN 7 THEN 'The transaction is being rolled back.'
                WHEN 8 THEN 'The transaction has been rolled back.' END 
    , transaction_name = tat.name, request_status = r.status
    , azure_dtc_state = CASE tat.dtc_state 
                        WHEN 1 THEN 'ACTIVE'
                        WHEN 2 THEN 'PREPARED'
                        WHEN 3 THEN 'COMMITTED'
                        WHEN 4 THEN 'ABORTED'
                        WHEN 5 THEN 'RECOVERED' END
    , tst.is_user_transaction, tst.is_local
    , session_open_transaction_count = tst.open_transaction_count  
    , s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
    FROM sys.dm_tran_active_transactions tat 
    INNER JOIN sys.dm_tran_session_transactions tst  on tat.transaction_id = tst.transaction_id
    INNER JOIN Sys.dm_exec_sessions s on s.session_id = tst.session_id 
    LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
    CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib;
    ```

    -   Inne kolumny

        Pozostałe kolumny w [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) i [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) mogą również udostępniać informacje o katalogu głównym problemu. Ich użyteczność różni się w zależności od okoliczności problemu. Można na przykład określić, czy problem występuje tylko od niektórych klientów (hostname) w niektórych bibliotekach sieciowych (net_library), kiedy Ostatnia Partia przesłana przez identyfikator SPID była `last_request_start_time` w sys.dm_exec_sessions, jak długo żądanie było wykonywane przy użyciu `start_time` w sys.dm_exec_requests i tak dalej.


## <a name="common-blocking-scenarios"></a>Typowe scenariusze blokowania

Poniższa tabela mapuje typowe objawy na ich prawdopodobne przyczyny.  

`wait_type`Kolumny, `open_transaction_count` i `status` odnoszą się do informacji zwracanych przez [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql), inne kolumny mogą być zwracane przez [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql). "Rozwiązuje?" kolumna wskazuje, czy blokowanie będzie samodzielnie rozwiązywane, czy też czy sesja powinna zostać zabity przy użyciu `KILL` polecenia. Aby uzyskać więcej informacji, zobacz [Kill (Transact-SQL)](/sql/t-sql/language-elements/kill-transact-sql).

| Scenariusz | Waittype | Open_Tran | Stan | Nowo? | Inne objawy |  
|:-|:-|:-|:-|:-|:-|--|
| 1 | NIE MA WARTOŚCI NULL | >= 0 | możliwy do uruchomienia | Tak, po zakończeniu wykonywania zapytania. | W sys.dm_exec_sessions kolumny, **odczyty**, **cpu_time** i/lub **memory_usage** zostaną zwiększone z upływem czasu. Czas trwania zapytania będzie wysoki po zakończeniu. |
| 2 | NULL | \>2,0 | nocleg | Nie, ale można zabić Identyfikator SPID. | Sygnał uwagi może być widoczny w sesji zdarzeń rozszerzonych dla tego identyfikatora SPID, co oznacza, że upłynął limit czasu zapytania lub został anulowany. |
| 3 | NULL | \>= 0 | możliwy do uruchomienia | Nie. Nie zostanie rozwiązany, dopóki klient nie pobierze wszystkich wierszy lub nie zamknie połączenia. Można zabić Identyfikator SPID, ale może to potrwać do 30 sekund. | Jeśli open_transaction_count = 0, a identyfikator SPID utrzymuje blokady, gdy poziom izolacji transakcji jest domyślny (odczyt COMMMITTED), jest to prawdopodobnie powód. |  
| 4 | Różnie | \>= 0 | możliwy do uruchomienia | Nie. Nie zostanie rozpoznany, dopóki klient nie anuluje zapytań lub nie zamknie połączeń. Identyfikatory SPID można zabić, ale mogą upłynąć do 30 sekund. | Kolumna **hostname** w sys.dm_exec_sessions dla identyfikatora SPID na początku łańcucha blokującego będzie taka sama jak jeden z blokowanych identyfikatorów SPID. |  
| 5 | NULL | \>2,0 | Wycofywanie | Tak. | Sygnał uwagi może być widoczny w sesji zdarzeń rozszerzonych dla tego identyfikatora SPID, co oznacza, że upłynął limit czasu zapytania lub został anulowany, lub po prostu wydano instrukcję ROLLBACK. |  
| 6 | NULL | \>2,0 | nocleg | Czasie. Gdy system Windows NT określi, że sesja nie jest już aktywna, połączenie Azure SQL Database zostanie przerwane. | `last_request_start_time`Wartość w sys.dm_exec_sessions jest znacznie wcześniejsza niż bieżąca godzina. |

## <a name="detailed-blocking-scenarios"></a>Szczegółowe scenariusze blokowania

1.  Blokowanie spowodowane przez normalne uruchomienie zapytania o długim czasie wykonywania

    **Rozwiązanie**: rozwiązanie tego typu problemu blokującego ma na celu wyszukanie sposobów optymalizacji zapytania. W rzeczywistości ta klasa problemu blokującego może być przyczyną problemu z wydajnością i wymagać jego wykonania. Aby uzyskać informacje na temat rozwiązywania problemów dotyczących konkretnej długotrwałej kwerendy, zobacz [Jak rozwiązywać wolno działające zapytania na SQL Server](/troubleshoot/sql/performance/troubleshoot-slow-running-queries). Aby uzyskać więcej informacji, zobacz [monitorowanie i dostrajanie wydajności](/sql/relational-databases/performance/monitor-and-tune-for-performance). 

    Raporty z [magazynu zapytań](/sql/relational-databases/performance/best-practice-with-the-query-store) w programie SSMS są również wysoce zalecanym i cennym narzędziem do identyfikowania najczęściej zaraportowanych, nieoptymalnych planów wykonania. Zapoznaj się również z sekcją [Intelligent Performance](intelligent-insights-overview.md) w Azure Portal bazy danych Azure SQL, w tym [szczegółowe informacje o wydajności zapytań](query-performance-insight-use.md).

    Jeśli masz długotrwałe zapytanie, które blokuje innych użytkowników i nie można go zoptymalizować, rozważ przeniesienie go ze środowiska OLTP do dedykowanego systemu raportowania — [synchroniczną replikę tylko do odczytu bazy danych](read-scale-out.md).

1.  Blokowanie spowodowane przez uśpiony Identyfikator SPID, który ma niezadeklarowaną transakcję

    Ten typ blokowania może być często identyfikowany przez identyfikator SPID, który jest uśpiony lub czeka na polecenie, a mimo że poziom zagnieżdżenia transakcji ( `@@TRANCOUNT` `open_transaction_count` z sys.dm_exec_requests) jest większy od zera. Taka sytuacja może wystąpić, jeśli aplikacja działa w czasie wykonywania zapytania lub wygeneruje problem Anuluj bez również wydawania wymaganej liczby instrukcji WYCOFANia i/lub zatwierdzenia. Gdy identyfikator SPID odbiera limit czasu zapytania lub anuluje, zakończy bieżące zapytanie i partię, ale nie zostanie automatycznie wycofany ani zatwierdzić transakcji. Aplikacja jest odpowiedzialna za to, ponieważ Azure SQL Database nie może założyć, że cała transakcja musi zostać wycofana z powodu anulowania pojedynczego zapytania. Limit czasu zapytania lub przycisk Anuluj będzie wyświetlany jako zdarzenie sygnalizacji uwagi dla identyfikatora SPID w sesji zdarzeń rozszerzonych.

    Aby przedstawić niezadeklarowaną jawną transakcję, należy wydać następujące zapytanie:

    ```sql
    CREATE TABLE #test (col1 INT);
    INSERT INTO #test SELECT 1;
    BEGIN TRAN
    UPDATE #test SET col1 = 2 where col1 = 1;
    ```

    Następnie wykonaj to zapytanie w tym samym oknie:
    ```sql
    SELECT @@TRANCOUNT;
    ROLLBACK TRAN
    DROP TABLE #test;
    ```

    Wyjście drugiego zapytania wskazuje, że poziom zagnieżdżenia transakcji jest taki. Wszystkie blokady nabyte w ramach transakcji nadal są przechowywane, dopóki transakcja nie zostanie zatwierdzona lub wycofana. Jeśli aplikacje jawnie otwierają i zatwierdzają transakcje, komunikat lub inny błąd może opuścić sesję i jej transakcję w stanie otwartym. 

    Użyj skryptu znajdującego się wcześniej w tym artykule na podstawie sys.dm_tran_active_transactions, aby identyfikować obecnie niezatwierdzone transakcje w ramach wystąpienia.

    **Rozwiązania**:

     -   Ponadto ta klasa problemu blokującego może być również przyczyną problemu z wydajnością i wymaga, aby można było go wykonać. Jeśli można zmniejszyć czas wykonywania zapytania, limit czasu zapytania lub Anuluj nie wystąpi. Ważne jest, aby aplikacja mogła obsłużyć limit czasu lub anulować scenariusze, ale może też skorzystać z badania wydajności zapytania. 
     
     -    Aplikacje muszą prawidłowo zarządzać poziomami zagnieżdżenia transakcji lub mogą spowodować problem z blokowaniem po anulowaniu zapytania w ten sposób. Rozważ następujące źródła:  

            *    W procedurze obsługi błędów aplikacji klienckiej wykonaj `IF @@TRANCOUNT > 0 ROLLBACK TRAN` następujące błędy, nawet jeśli aplikacja kliencka nie jest uważana za otwartą transakcji. Sprawdzanie otwartych transakcji jest wymagane, ponieważ procedura składowana wywołana podczas wykonywania wsadowego mogła rozpocząć transakcję bez wiedzy aplikacji klienckiej. Niektóre warunki, takie jak anulowanie zapytania, uniemożliwiają wykonywanie procedury poza bieżącą instrukcją, więc nawet jeśli procedura ma logikę do sprawdzania `IF @@ERROR <> 0` i przerywania transakcji, ten kod wycofania nie zostanie wykonany w takich przypadkach.  
            *    Jeśli pule połączeń są używane w aplikacji, która otwiera połączenie i uruchamia niewielką liczbę zapytań przed zwolnieniem połączenia z powrotem do puli, takiej jak aplikacja oparta na sieci Web, tymczasowe wyłączenie puli połączeń może pomóc w zmniejszeniu problemu do momentu zmodyfikowania aplikacji klienckiej w celu odpowiedniego obsłużenia błędów. Wyłączenie puli połączeń zwalnia połączenie spowoduje fizyczne odłączenie połączenia Azure SQL Database, co spowoduje wycofanie wszelkich otwartych transakcji na serwerze.  
            *    Służy `SET XACT_ABORT ON` do połączenia lub w innych procedurach składowanych, które zaczynają transakcję i nie są oczyszczane po błędzie. W przypadku błędu czasu wykonywania to ustawienie spowoduje przerwanie wszelkich otwartych transakcji i zwrócenie kontroli klientowi. Aby uzyskać więcej informacji, zobacz [SET XACT_ABORT (Transact-SQL)](/sql/t-sql/statements/set-xact-abort-transact-sql).

    > [!NOTE]
    > Połączenie nie zostanie zresetowane, dopóki nie zostanie ponownie użyte z puli połączeń, więc użytkownik może otworzyć transakcję, a następnie zwolnić połączenie z pulą połączeń, ale może nie być ponownie używane przez kilka sekund, podczas którego transakcja pozostanie otwarta. Jeśli połączenie nie jest ponownie używane, transakcja zostanie przerwana, gdy połączenie zostanie przełączone i zostanie usunięte z puli połączeń. W ten sposób jest optymalne, aby aplikacja kliencka mogła przerwać transakcje w ramach procedury obsługi błędów lub użyć `SET XACT_ABORT ON` , aby uniknąć tego potencjalnego opóźnienia.

    > [!CAUTION]
    > Następujące `SET XACT_ABORT ON` instrukcje języka T-SQL po instrukcji, która powoduje błąd nie zostanie wykonany. Może to mieć wpływ na zamierzony przepływ istniejącego kodu.

1.  Blokowanie spowodowane przez identyfikator SPID, którego odpowiadająca aplikacja kliencka nie pobrała wszystkich wierszy wyników do ukończenia

    Po wysłaniu zapytania do serwera wszystkie aplikacje muszą natychmiast pobrać wszystkie wiersze wynikowe. Jeśli aplikacja nie pobiera wszystkich wierszy wynikowych, blokady mogą pozostać w tabelach, blokując innym użytkownikom. Jeśli używasz aplikacji, która w sposób przezroczysty przesyła instrukcje SQL do serwera, aplikacja musi pobrać wszystkie wiersze wynikowe. Jeśli nie (i jeśli nie można go skonfigurować w ten sposób), może być niemożliwe rozwiązanie problemu z blokowaniem. Aby uniknąć tego problemu, można ograniczyć niewłaściwie zachowywać się aplikacje do bazy danych raportowania lub pomocy technicznej, niezależnie od głównej bazy danych OLTP.
    
    > [!NOTE]
    > Zobacz [wskazówki dotyczące logiki ponawiania](./troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) dla aplikacji łączących się z Azure SQL Database. 
    
    **Rozwiązanie**: aplikacja musi zostać ponownie zapisywana, aby można było pobrać wszystkie wiersze wyniku do ukończenia. Nie powoduje to wykluczenia użycia [przesunięcia i pobrania w klauzuli Order by](/sql/t-sql/queries/select-order-by-clause-transact-sql#using-offset-and-fetch-to-limit-the-rows-returned) zapytania do wykonywania stronicowania po stronie serwera.

1.  Blokowanie spowodowane przez sesję w stanie wycofywania

    Kwerenda modyfikacji danych, która została przerwana lub anulowana poza transakcją zdefiniowaną przez użytkownika, zostanie wycofana. Może to również wystąpić podczas odłączania sesji sieci klienta lub gdy żądanie jest wybrane jako ofiara zakleszczenia. Może to być często identyfikowane przez obserwowanie danych wyjściowych sys.dm_exec_requests, które mogą wskazywać na WYCOFANie **polecenia**, a **kolumna percent_complete** może pokazywać postęp. 

    Dzięki [przyspieszonej funkcji odzyskiwania bazy danych](../accelerated-database-recovery.md) wprowadzonej w 2019, długie wycofywanie powinny być rzadkie.
    
    **Rozwiązanie**: Poczekaj na zakończenie wycofywania wprowadzonych zmian przez identyfikator SPID. 

    Aby uniknąć tej sytuacji, nie wykonuj dużych operacji zapisu wsadowego ani operacji tworzenia indeksu ani konserwacji w godzinach zajętości w systemach OLTP. Jeśli to możliwe, wykonaj takie operacje w okresach z niską aktywnością.

1.  Blokowanie spowodowane połączeniem oddzielonym

    Jeśli błędy pułapki aplikacji klienta lub stacja robocza klienta zostanie ponownie uruchomiona, sesja sieciowa z serwerem może nie zostać natychmiast anulowana w pewnych warunkach. Z perspektywy Azure SQL Database klient nadal wydaje się być obecny i wszystkie odkupione blokady mogą nadal być zachowywane. Aby uzyskać więcej informacji, zobacz [Jak rozwiązywać problemy z połączeniami oddzielonymi w SQL Server](/sql/t-sql/language-elements/kill-transact-sql#remarks). 

    **Rozwiązanie**: Jeśli aplikacja kliencka została odłączona bez odpowiednio czyszczenie zasobów, możesz przerwać Identyfikator SPID przy użyciu `KILL` polecenia. `KILL`Polecenie przyjmuje wartość SPID jako dane wejściowe. Na przykład aby skasować Identyfikator SPID 99, wydaj następujące polecenie:

    ```sql
    KILL 99
    ```

## <a name="see-also"></a>Zobacz też

* [Monitorowanie i dostrajanie wydajności usługi Azure SQL Database i wystąpienia zarządzanego Azure SQL](./monitor-tune-overview.md)
* [Monitorowanie wydajności za pomocą magazynu zapytań](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [Przewodnik dotyczący blokowania transakcji i przechowywania wersji wierszy](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [USTAW POZIOM IZOLACJI TRANSAKCJI](/sql/t-sql/statements/set-transaction-isolation-level-transact-sql)
* [Szybki start: zdarzenia rozszerzone w programie SQL Server](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
* [Intelligent Insights przy użyciu AI do monitorowania i rozwiązywania problemów z wydajnością bazy danych](intelligent-insights-overview.md)

## <a name="learn-more"></a>Więcej tutaj

* [Azure SQL Database: ulepszanie dostrajania wydajności za pomocą dostrajania automatycznego](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Improving-Performance-Tuning-with-Automatic-Tuning)
* [Poprawianie wydajności Azure SQL Database przy użyciu dostrajania automatycznego](https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning)
* [Zapewnianie spójnej wydajności za pomocą usługi Azure SQL](/learn/modules/azure-sql-performance/)
* [Rozwiązywanie problemów z łącznością i innych błędów przy użyciu Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL](troubleshoot-common-errors-issues.md)
* [Obsługa błędów przejściowych](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)
