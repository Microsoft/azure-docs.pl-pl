---
title: Twórz zadania Elastic Database i zarządzaj nimi (wersja zapoznawcza) przy użyciu języka Transact-SQL (T-SQL)
description: Uruchamianie skryptów w wielu bazach danych za pomocą Elastic Database agenta zadań przy użyciu języka Transact-SQL (T-SQL).
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
ms.date: 02/07/2020
ms.openlocfilehash: bbecfac4bfd3d5ce1510cb671b93df5f4982cbc4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91803861"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs-preview"></a>Używanie języka Transact-SQL (T-SQL) do tworzenia zadań Elastic Database i zarządzania nimi (wersja zapoznawcza)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ten artykuł zawiera wiele przykładowych scenariuszy ułatwiających rozpoczęcie pracy z zadaniami elastycznymi przy użyciu języka T-SQL.

W przykładach użyto [procedur składowanych](#job-stored-procedures) i [widoków](#job-views) dostępnych w [*bazie danych zadań*](job-automation-overview.md#job-database).

Język Transact-SQL (T-SQL) służy do tworzenia, konfigurowania, wykonywania i zarządzania zadaniami. Tworzenie agenta zadań elastycznych nie jest obsługiwane w języku T-SQL, dlatego należy najpierw utworzyć *agenta elastycznego zadania* przy użyciu portalu lub [programu PowerShell](elastic-jobs-powershell-create.md#create-the-elastic-job-agent).

## <a name="create-a-credential-for-job-execution"></a>Utwórz poświadczenie do wykonania zadania

Poświadczenie jest używane do nawiązywania połączenia z docelowymi bazami danych w celu wykonania skryptu. Poświadczenie musi mieć odpowiednie uprawnienia do baz danych określonych przez grupę docelową, aby pomyślnie wykonać skrypt. W przypadku korzystania z elementu członkowskiego [logicznego serwera SQL](logical-servers.md) i/lub grupy docelowej puli zdecydowanie zaleca się utworzenie poświadczeń głównych do użycia w celu odświeżenia poświadczenia przed rozszerzeniem serwera i/lub puli w czasie wykonywania zadania. Poświadczenia w zakresie bazy danych są tworzone w bazie danych agenta zadań. To samo poświadczenie musi być użyte do *utworzenia nazwy logowania* i *utworzenia użytkownika z logowania w celu przyznania uprawnień bazy danych logowania do* docelowych baz danych.

```sql
--Connect to the job database specified when creating the job agent

-- Create a db master key if one does not already exist, using your own password.  
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';  
  
-- Create a database scoped credential.  
CREATE DATABASE SCOPED CREDENTIAL myjobcred WITH IDENTITY = 'jobcred',
    SECRET = '<EnterStrongPasswordHere>';
GO

-- Create a database scoped credential for the master database of server1.
CREATE DATABASE SCOPED CREDENTIAL mymastercred WITH IDENTITY = 'mastercred',
    SECRET = '<EnterStrongPasswordHere>';
GO
```

## <a name="create-a-target-group-servers"></a>Tworzenie grupy docelowej (serwery)

Poniższy przykład pokazuje, jak wykonać zadanie dla wszystkich baz danych na serwerze.  
Nawiąż połączenie z [*bazą danych zadań*](job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
-- Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group 'ServerGroup1'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
'ServerGroup1',
@target_type = 'SqlServer',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in a server
@server_name='server1.database.windows.net'

--View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name='ServerGroup1';
SELECT * FROM jobs.target_group_members WHERE target_group_name='ServerGroup1';
```

## <a name="exclude-an-individual-database"></a>Wykluczanie pojedynczej bazy danych

Poniższy przykład pokazuje, jak wykonać zadanie dla wszystkich baz danych na serwerze, z wyjątkiem bazy danych o nazwie *MappingDB*.  
Nawiąż połączenie z [*bazą danych zadań*](job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group N'ServerGroup'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in a server
@server_name=N'London.database.windows.net'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in a server
@server_name='server2.database.windows.net'
GO

--Exclude a database target member from the server target group
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@membership_type = N'Exclude',
@target_type = N'SqlDatabase',
@server_name = N'server1.database.windows.net',
@database_name =N'MappingDB'
GO

--View the recently created target group and target group members
SELECT * FROM [jobs].target_groups WHERE target_group_name = N'ServerGroup';
SELECT * FROM [jobs].target_group_members WHERE target_group_name = N'ServerGroup';
```

## <a name="create-a-target-group-pools"></a>Tworzenie grupy docelowej (pule)

Poniższy przykład pokazuje, jak skierować wszystkie bazy danych w co najmniej jednej puli elastycznej.  
Nawiąż połączenie z [*bazą danych zadań*](job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing pool(s)
EXEC jobs.sp_add_target_group 'PoolGroup'

-- Add an elastic pool(s) target member
EXEC jobs.sp_add_target_group_member
'PoolGroup',
@target_type = 'SqlElasticPool',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in a server
@server_name='server1.database.windows.net',
@elastic_pool_name='ElasticPool-1'

-- View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name = N'PoolGroup';
SELECT * FROM jobs.target_group_members WHERE target_group_name = N'PoolGroup';
```

## <a name="deploy-new-schema-to-many-databases"></a>Wdróż nowy schemat w wielu bazach danych

Poniższy przykład pokazuje, jak wdrożyć nowy schemat dla wszystkich baz danych.  
Nawiąż połączenie z [*bazą danych zadań*](job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

--Add job for create table
EXEC jobs.sp_add_job @job_name='CreateTableTest', @description='Create Table Test'

-- Add job step for create table
EXEC jobs.sp_add_jobstep @job_name='CreateTableTest',
@command=N'IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id(''Test''))
CREATE TABLE [dbo].[Test]([TestId] [int] NOT NULL);',
@credential_name='myjobcred',
@target_group_name='PoolGroup'
```

## <a name="data-collection-using-built-in-parameters"></a>Zbieranie danych przy użyciu wbudowanych parametrów

W wielu scenariuszach zbierania danych przydatne może być uwzględnienie niektórych z tych zmiennych skryptów, aby pomóc w zainicjowaniu przetwarzania wyników zadania.

- $ (job_name)
- $ (job_id)
- $ (job_version)
- $ (step_id)
- $ (step_name)
- $ (job_execution_id)
- $ (job_execution_create_time)
- $ (target_group_name)

Aby na przykład zgrupować wszystkie wyniki z tego samego wykonania zadania, należy użyć *$ (job_execution_id)* , jak pokazano w następującym poleceniu:

```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```

## <a name="monitor-database-performance"></a>Monitorowanie wydajności bazy danych

Poniższy przykład tworzy nowe zadanie w celu zbierania danych wydajności z wielu baz danych.

Domyślnie Agent zadania utworzy tabelę wyjściową do przechowywania zwracanych wyników. W związku z tym podmiot zabezpieczeń bazy danych skojarzony z poświadczeniem danych wyjściowych musi mieć co najmniej następujące uprawnienia: `CREATE TABLE` w bazie danych,,, `ALTER` `SELECT` `INSERT` `DELETE` w tabeli wyjściowej lub jej schemacie oraz `SELECT` w widoku wykazu [sys. indexes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql) .

Jeśli chcesz ręcznie utworzyć tabelę przed czasem, musi ona mieć następujące właściwości:

1. Kolumny o poprawnej nazwie i typach danych dla zestawu wyników.
2. Dodatkowa kolumna dla internal_execution_id z typem danych unikatowym.
3. Indeks nieklastrowany o nazwie `IX_<TableName>_Internal_Execution_ID` w kolumnie internal_execution_id.
4. Wszystkie uprawnienia wymienione powyżej, z wyjątkiem `CREATE TABLE` uprawnień w bazie danych.

Nawiąż połączenie z [*bazą danych zadań*](job-automation-overview.md#job-database) i uruchom następujące polecenia:

```sql
--Connect to the job database specified when creating the job agent

-- Add a job to collect perf results
EXEC jobs.sp_add_job @job_name ='ResultsJob', @description='Collection Performance data from all customers'

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsJob',
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());',
@credential_name='myjobcred',
@target_group_name='PoolGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='<resultsdb>',
@output_table_name='<resutlstable>'
Create a job to monitor pool performance
--Connect to the job database specified when creating the job agent

-- Add a target group containing master database
EXEC jobs.sp_add_target_group 'MasterGroup'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name='MasterGroup',
@target_type='SqlDatabase',
@server_name='server1.database.windows.net',
@database_name='master'

-- Add a job to collect perf results
EXEC jobs.sp_add_job
@job_name='ResultsPoolsJob',
@description='Demo: Collection Performance data from all pools',
@schedule_interval_type='Minutes',
@schedule_interval_count=15

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsPoolsJob',
@command=N'declare @now datetime
DECLARE @startTime datetime
DECLARE @endTime datetime
DECLARE @poolLagMinutes datetime
DECLARE @poolStartTime datetime
DECLARE @poolEndTime datetime
SELECT @now = getutcdate ()
SELECT @startTime = dateadd(minute, -15, @now)
SELECT @endTime = @now
SELECT @poolStartTime = dateadd(minute, -30, @startTime)
SELECT @poolEndTime = dateadd(minute, -30, @endTime)

SELECT elastic_pool_name , end_time, elastic_pool_dtu_limit, avg_cpu_percent, avg_data_io_percent, avg_log_write_percent, max_worker_percent, max_session_percent,
        avg_storage_percent, elastic_pool_storage_limit_mb FROM sys.elastic_pool_resource_stats
        WHERE end_time > @poolStartTime and end_time <= @poolEndTime;
'),
@credential_name='myjobcred',
@target_group_name='MasterGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='resultsdb',
@output_table_name='resutlstable'
```

## <a name="view-job-definitions"></a>Wyświetl definicje zadań

Poniższy przykład pokazuje, jak wyświetlić bieżące definicje zadań.  
Nawiąż połączenie z [*bazą danych zadań*](job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

-- View all jobs
SELECT * FROM jobs.jobs

-- View the steps of the current version of all jobs
SELECT js.* FROM jobs.jobsteps js
JOIN jobs.jobs j
  ON j.job_id = js.job_id AND j.job_version = js.job_version

-- View the steps of all versions of all jobs
select * from jobs.jobsteps
```

## <a name="begin-unplanned-execution-of-a-job"></a>Rozpocznij nieplanowane wykonywanie zadania

Poniższy przykład pokazuje, jak natychmiast uruchomić zadanie.  
Nawiąż połączenie z [*bazą danych zadań*](job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

-- Execute the latest version of a job
EXEC jobs.sp_start_job 'CreateTableTest'

-- Execute the latest version of a job and receive the execution id
declare @je uniqueidentifier
exec jobs.sp_start_job 'CreateTableTest', @job_execution_id = @je output
select @je

select * from jobs.job_executions where job_execution_id = @je

-- Execute a specific version of a job (e.g. version 1)
exec jobs.sp_start_job 'CreateTableTest', 1
```

## <a name="schedule-execution-of-a-job"></a>Zaplanuj wykonywanie zadania

Poniższy przykład pokazuje, jak zaplanować zadanie do wykonania w przyszłości.  
Nawiąż połączenie z [*bazą danych zadań*](job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>Monitorowanie stanu wykonania zadania

Poniższy przykład pokazuje, jak wyświetlić szczegóły stanu wykonania dla wszystkich zadań.  
Nawiąż połączenie z [*bazą danych zadań*](job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named 'ResultsPoolJob'
SELECT * FROM jobs.job_executions
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC

--View all execution statuses for job named 'ResultsPoolsJob'
SELECT * FROM jobs.job_executions
WHERE job_name = 'ResultsPoolsJob'
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions
WHERE is_active = 1
ORDER BY start_time DESC
```

## <a name="cancel-a-job"></a>Anulowanie zadania

Poniższy przykład pokazuje, jak anulować zadanie.  
Nawiąż połączenie z [*bazą danych zadań*](job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

-- View all active executions to determine job execution id
SELECT * FROM jobs.job_executions
WHERE is_active = 1 AND job_name = 'ResultPoolsJob'
ORDER BY start_time DESC
GO

-- Cancel job execution with the specified job execution id
EXEC jobs.sp_stop_job '01234567-89ab-cdef-0123-456789abcdef'
```

## <a name="delete-old-job-history"></a>Usuń starą historię zadania

Poniższy przykład pokazuje, jak usunąć historię zadania przed określoną datą.  
Nawiąż połączenie z [*bazą danych zadań*](job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Usuń zadanie i całą jego historię zadań

Poniższy przykład pokazuje, jak usunąć zadanie i wszystkie powiązane z nimi historię zadań.  
Nawiąż połączenie z [*bazą danych zadań*](job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="job-stored-procedures"></a>Procedury składowane zadań

Poniższe procedury składowane znajdują się w [bazie danych zadań](job-automation-overview.md#job-database).

|Procedura składowana  |Opis  |
|---------|---------|
|[sp_add_job](#sp_add_job)     |     Dodaje nowe zadanie.    |
|[sp_update_job](#sp_update_job)    |      Aktualizuje istniejące zadanie.   |
|[sp_delete_job](#sp_delete_job)     |      Usuwa istniejące zadanie.   |
|[sp_add_jobstep](#sp_add_jobstep)    |    Dodaje krok do zadania.     |
|[sp_update_jobstep](#sp_update_jobstep)     |     Aktualizuje krok zadania.    |
|[sp_delete_jobstep](#sp_delete_jobstep)     |     Usuwa krok zadania.    |
|[sp_start_job](#sp_start_job)    |  Uruchamia wykonywanie zadania.       |
|[sp_stop_job](#sp_stop_job)     |     Kończy wykonywanie zadania.   |
|[sp_add_target_group](#sp_add_target_group)    |     Dodaje grupę docelową.    |
|[sp_delete_target_group](#sp_delete_target_group)     |    Usuwa grupę docelową.     |
|[sp_add_target_group_member](#sp_add_target_group_member)     |    Dodaje bazę danych lub grupę baz danych do grupy docelowej.     |
|[sp_delete_target_group_member](#sp_delete_target_group_member)     |     Usuwa element członkowski grupy docelowej z grupy docelowej.    |
|[sp_purge_jobhistory](#sp_purge_jobhistory)    |    Usuwa rekordy historii zadania.     |

### <a name="sp_add_job"></a><a name="sp_add_job"></a>sp_add_job

Dodaje nowe zadanie.
  
#### <a name="syntax"></a>Składnia  
  
```syntaxsql
[jobs].sp_add_job [ @job_name = ] 'job_name'  
  [ , [ @description = ] 'description' ]
  [ , [ @enabled = ] enabled ]
  [ , [ @schedule_interval_type = ] schedule_interval_type ]  
  [ , [ @schedule_interval_count = ] schedule_interval_count ]
  [ , [ @schedule_start_time = ] schedule_start_time ]
  [ , [ @schedule_end_time = ] schedule_end_time ]
  [ , [ @job_id = ] job_id OUTPUT ]
```

#### <a name="arguments"></a>Argumenty  

[ ** \@ job_name =** ] "job_name"  
Nazwa zadania. Nazwa musi być unikatowa i nie może zawierać wartości procentowej (%) Opis. job_name jest nvarchar (128), bez domyślnego.

[ ** \@ Description =** ] "Description"  
Opis zadania. Opis to nvarchar (512) z wartością domyślną NULL. Jeśli opis zostanie pominięty, zostanie użyty pusty ciąg.

[ ** \@ Enabled =** ] włączone  
Czy harmonogram zadania jest włączony. Włączony jest bit z wartością domyślną 0 (wyłączone). Jeśli wartość wynosi 0, zadanie nie jest włączone i nie jest uruchamiane zgodnie ze swoim harmonogramem. można go jednak uruchomić ręcznie. Jeśli 1, zadanie zostanie uruchomione zgodnie z harmonogramem i może być również uruchamiane ręcznie.

[ ** \@ schedule_interval_type =**] schedule_interval_type  
Wartość wskazuje, kiedy zadanie ma zostać wykonane. schedule_interval_type jest nvarchar (50) z wartością domyślną jednokrotnie i może być jedną z następujących wartości:

- "Raz",
- "Min",
- "Godz.",
- "Dni",
- "Tygodnie",
- Od

[ ** \@ schedule_interval_count =** ] schedule_interval_count  
Liczba schedule_interval_countych okresów między każdym wykonaniem zadania. schedule_interval_count jest int z wartością domyślną 1. Wartość musi być większa lub równa 1.

[ ** \@ schedule_start_time =** ] schedule_start_time  
Data rozpoczęcia wykonywania zadania. schedule_start_time to DATETIME2, z wartością domyślną 0001-01-01 00:00:00.0000000.

[ ** \@ schedule_end_time =** ] schedule_end_time  
Data, w której wykonywanie zadania może zostać zatrzymane. schedule_end_time to DATETIME2, a wartość domyślna to 9999-12-31 11:59:59.0000000.

[ ** \@ job_id =** ] job_id dane wyjściowe  
Numer identyfikacyjny zadania przypisany do zadania, jeśli został utworzony pomyślnie. job_id to zmienna wyjściowa typu o unikatowych wartościach.

#### <a name="return-code-values"></a>Zwróć wartości kodu

0 (sukces) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi

sp_add_job należy uruchomić z bazy danych agenta zadań określonej podczas tworzenia agenta zadań.
Gdy sp_add_job zostało wykonane w celu dodania zadania, sp_add_jobstep może służyć do dodawania kroków, które wykonują działania dla tego zadania. Początkowy numer wersji zadania to 0, który zostanie zwiększony do 1 po dodaniu pierwszego kroku.

#### <a name="permissions"></a>Uprawnienia

Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ograniczają użytkownikowi możliwość monitorowania zadań, ale użytkownik może należeć do następującej roli bazy danych w bazie danych agentów zadań określonej podczas tworzenia agenta zadania:

- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról, zobacz sekcję dotyczącą uprawnień w tym dokumencie. Tylko członkowie roli sysadmin mogą używać tej procedury składowanej do edytowania atrybutów zadań należących do innych użytkowników.

### <a name="sp_update_job"></a><a name="sp_update_job"></a>sp_update_job

Aktualizuje istniejące zadanie.

#### <a name="syntax"></a>Składnia

```syntaxsql
[jobs].sp_update_job [ @job_name = ] 'job_name'  
  [ , [ @new_name = ] 'new_name' ]
  [ , [ @description = ] 'description' ]
  [ , [ @enabled = ] enabled ]
  [ , [ @schedule_interval_type = ] schedule_interval_type ]  
  [ , [ @schedule_interval_count = ] schedule_interval_count ]
  [ , [ @schedule_start_time = ] schedule_start_time ]
  [ , [ @schedule_end_time = ] schedule_end_time ]
```

#### <a name="arguments"></a>Argumenty

[ ** \@ job_name =** ] "job_name"  
Nazwa zadania do zaktualizowania. job_name jest nvarchar (128).

[ ** \@ new_name =** ] "new_name"  
Nowa nazwa zadania. new_name jest nvarchar (128).

[ ** \@ Description =** ] "Description"  
Opis zadania. Opis to nvarchar (512).

[ ** \@ Enabled =** ] włączone  
Określa, czy harmonogram zadania jest włączony (1) czy nie jest włączony (0). Włączony jest bit.

[ ** \@ schedule_interval_type =** ] schedule_interval_type  
Wartość wskazuje, kiedy zadanie ma zostać wykonane. schedule_interval_type jest nvarchar (50) i może być jedną z następujących wartości:

- "Raz",
- "Min",
- "Godz.",
- "Dni",
- "Tygodnie",
- Od

[ ** \@ schedule_interval_count =** ] schedule_interval_count  
Liczba schedule_interval_countych okresów między każdym wykonaniem zadania. schedule_interval_count jest int z wartością domyślną 1. Wartość musi być większa lub równa 1.

[ ** \@ schedule_start_time =** ] schedule_start_time  
Data rozpoczęcia wykonywania zadania. schedule_start_time to DATETIME2, z wartością domyślną 0001-01-01 00:00:00.0000000.

[ ** \@ schedule_end_time =** ] schedule_end_time  
Data, w której wykonywanie zadania może zostać zatrzymane. schedule_end_time to DATETIME2, a wartość domyślna to 9999-12-31 11:59:59.0000000.

#### <a name="return-code-values"></a>Zwróć wartości kodu

0 (sukces) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi

Gdy sp_add_job zostało wykonane w celu dodania zadania, sp_add_jobstep może służyć do dodawania kroków, które wykonują działania dla tego zadania. Początkowy numer wersji zadania to 0, który zostanie zwiększony do 1 po dodaniu pierwszego kroku.

#### <a name="permissions"></a>Uprawnienia

Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ograniczają użytkownikowi możliwość monitorowania zadań, ale użytkownik może należeć do następującej roli bazy danych w bazie danych agentów zadań określonej podczas tworzenia agenta zadania:

- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról, zobacz sekcję dotyczącą uprawnień w tym dokumencie. Tylko członkowie roli sysadmin mogą używać tej procedury składowanej do edytowania atrybutów zadań należących do innych użytkowników.

### <a name="sp_delete_job"></a><a name="sp_delete_job"></a>sp_delete_job

Usuwa istniejące zadanie.

#### <a name="syntax"></a>Składnia

```syntaxsql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
  [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argumenty

[ ** \@ job_name =** ] "job_name"  
Nazwa zadania, które ma zostać usunięte. job_name jest nvarchar (128).

[ ** \@ Force =** ] Wymuś  
Określa, czy należy usunąć, czy zadanie ma wszystkie wykonania w toku, i anulować wszystkie wykonania w toku (1) lub zakończyć się niepowodzeniem, jeśli jakieś wykonania zadania są w toku (0). Wymuś to bit.

#### <a name="return-code-values"></a>Zwróć wartości kodu

0 (sukces) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi

Historia zadania jest automatycznie usuwana po usunięciu zadania.

#### <a name="permissions"></a>Uprawnienia

Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ograniczają użytkownikowi możliwość monitorowania zadań, ale użytkownik może należeć do następującej roli bazy danych w bazie danych agentów zadań określonej podczas tworzenia agenta zadania:

- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról, zobacz sekcję dotyczącą uprawnień w tym dokumencie. Tylko członkowie roli sysadmin mogą używać tej procedury składowanej do edytowania atrybutów zadań należących do innych użytkowników.

### <a name="sp_add_jobstep"></a><a name="sp_add_jobstep"></a>sp_add_jobstep

Dodaje krok do zadania.

#### <a name="syntax"></a>Składnia

```syntaxsql
[jobs].sp_add_jobstep [ @job_name = ] 'job_name'
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] step_name ]
     [ , [ @command_type = ] 'command_type' ]
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]
     [ , [ @retry_attempts = ] retry_attempts ]
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]
     [ , [ @output_type = ] 'output_type' ]
     [ , [ @output_credential_name = ] 'output_credential_name' ]
     [ , [ @output_subscription_id = ] 'output_subscription_id' ]
     [ , [ @output_resource_group_name = ] 'output_resource_group_name' ]
     [ , [ @output_server_name = ] 'output_server_name' ]
     [ , [ @output_database_name = ] 'output_database_name' ]
     [ , [ @output_schema_name = ] 'output_schema_name' ]
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumenty

[ ** \@ job_name =** ] "job_name"  
Nazwa zadania, do którego ma zostać dodany krok. job_name jest nvarchar (128).

[ ** \@ step_id =** ] step_id  
Numer identyfikacyjny sekwencji dla kroku zadania. Numery identyfikacyjne kroków rozpoczynają się od 1 i zwiększają się bez przerw. Jeśli istniejący krok ma już ten identyfikator, ten krok i wszystkie poniższe kroki zostaną narastane, aby można było wstawić ten nowy krok do sekwencji. Jeśli nie zostanie określony, step_id zostanie automatycznie przypisana do ostatniego z sekwencji kroków. step_id jest liczbą całkowitą.

[ ** \@ step_name =** ] step_name  
Nazwa kroku. Musi być określony, z wyjątkiem pierwszego kroku zadania, które (dla wygody) ma domyślną nazwę "JobStep". step_name jest nvarchar (128).

[ ** \@ command_type =** ] "command_type"  
Typ polecenia, które jest wykonywane przez ten JobStep. command_type jest nvarchar (50) z wartością domyślną TSql, co oznacza, że wartość @command_type parametru jest skrypt T-SQL.

Jeśli ta wartość jest określona, musi być TSql.

[ ** \@ command_source =** ] "command_source"  
Typ lokalizacji, w której jest przechowywane Polecenie. command_source jest nvarchar (50) z wartością domyślną Śródwierszowego, co oznacza, że wartość @command_source parametru jest tekstem literału polecenia.

Jeśli ta wartość jest określona, musi być wbudowana.

[ ** \@ Command =** ] "polecenie"  
Polecenie musi być prawidłowym skryptem T-SQL i jest wykonywane przez ten krok zadania. polecenie jest typu nvarchar (max) i ma domyślnie wartość NULL.

[ ** \@ credential_name =** ] "credential_name"  
Nazwa poświadczenia zakresu bazy danych przechowywanej w tej bazie danych kontroli zadań, która jest używana do nawiązywania połączenia z każdą z docelowych baz danych w grupie docelowej po wykonaniu tego kroku. credential_name jest nvarchar (128).

[ ** \@ target_group_name =** ] "Target-Group_Name"  
Nazwa grupy docelowej zawierającej docelowe bazy danych, w których zostanie wykonany krok zadania. target_group_name jest nvarchar (128).

[ ** \@ initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Opóźnienie przed pierwszym ponowieniem próby, jeśli etap zadania zakończy się niepowodzeniem podczas próby wykonania początkowej. initial_retry_interval_seconds jest int z wartością domyślną 1.

[ ** \@ maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maksymalne opóźnienie między ponownymi próbami. Jeśli opóźnienie między kolejnymi próbami będzie większe niż ta wartość, zostanie ona ograniczona do tej wartości. maximum_retry_interval_seconds jest int z wartością domyślną 120.

[ ** \@ retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Mnożnik, który ma zostać zastosowany do opóźnienia ponowienia próby w przypadku niepowodzenia wykonywania wielu kroków zadania. Na przykład jeśli pierwsze ponowienie próby miało opóźnienie 5 sekund, a mnożnik wycofywania to 2,0, drugie ponowienie próby będzie miało opóźnienie 10 sekund, a trzecia ponowna próba będzie miała opóźnienie 20 sekund. retry_interval_backoff_multiplier jest prawdziwy, a wartość domyślna to 2,0.

[ ** \@ retry_attempts =** ] retry_attempts  
Liczba ponownych prób wykonania, jeśli próba początkowa nie powiedzie się. Jeśli na przykład wartość retry_attempts wynosi 10, zostanie wykorzystana 1 próba początkowa i 10 ponownych prób, co spowoduje całkowite 11 prób. Jeśli końcowa próba ponowienia nie powiedzie się, wykonywanie zadania zakończy się niepowodzeniem z cyklem życia. retry_attempts jest int, z wartością domyślną 10.

[ ** \@ step_timeout_seconds =** ] step_timeout_seconds  
Maksymalny czas, jaki może wykonać krok. W przypadku przekroczenia tego czasu wykonywanie zadania zakończy się z cyklem życia TimedOut. step_timeout_seconds jest int z wartością domyślną 43 200 sekund (12 godzin).

[ ** \@ output_type =** ] "output_type"  
Jeśli wartość nie jest równa null, typ lokalizacji docelowej, w której jest zapisywana pierwszy zestaw wyników polecenia. output_type jest nvarchar (50) z wartością domyślną RÓWNą NULL.

Jeśli ta wartość jest określona, musi to być SQLDatabase.

[ ** \@ output_credential_name =** ] "output_credential_name"  
Jeśli wartość nie jest równa null, nazwa poświadczenia zakresu bazy danych, która jest używana do nawiązywania połączenia z wyjściową docelową bazą danych. Należy określić, jeśli output_type jest równa SQLDatabase. output_credential_name jest nvarchar (128) z wartością domyślną RÓWNą NULL.

[ ** \@ output_subscription_id =** ] "output_subscription_id"  
Wymaga opisu.

[ ** \@ output_resource_group_name =** ] "output_resource_group_name"  
Wymaga opisu.

[ ** \@ output_server_name =** ] "output_server_name"  
Jeśli wartość nie jest równa null, w pełni kwalifikowana nazwa DNS serwera, który zawiera wyjściową bazę danych wyjściowych. Należy określić, jeśli output_type jest równa SQLDatabase. output_server_name jest nvarchar (256), z wartością domyślną NULL.

[ ** \@ output_database_name =** ] "output_database_name"  
Jeśli wartość nie jest równa null, nazwa bazy danych zawierającej wyjściową tabelę docelową. Należy określić, jeśli output_type jest równa SQLDatabase. output_database_name jest nvarchar (128), z domyślną wartością NULL.

[ ** \@ output_schema_name =** ] "output_schema_name"  
Jeśli wartość nie jest równa null, nazwa schematu SQL zawierającego wyjściową tabelę docelową. Jeśli output_type jest równe SQLDatabase, wartość domyślna to dbo. output_schema_name jest nvarchar (128).

[ ** \@ output_table_name =** ] "output_table_name"  
Jeśli wartość nie jest równa null, nazwa tabeli, w której zostanie zapisany pierwszy zestaw wyników polecenia. Jeśli tabela jeszcze nie istnieje, zostanie utworzona na podstawie schematu zestawu wynik zwracanego. Należy określić, jeśli output_type jest równa SQLDatabase. output_table_name jest nvarchar (128) z wartością domyślną RÓWNą NULL.

[ ** \@ job_version =** ] job_version dane wyjściowe  
Parametr wyjściowy, do którego zostanie przypisany nowy numer wersji zadania. job_version jest int.

[ ** \@ max_parallelism =** ] max_parallelism dane wyjściowe  
Maksymalny poziom równoległości na pulę elastyczną. Jeśli ta wartość jest ustawiona, krok zadania będzie ograniczony tylko do liczby baz danych na pulę elastyczną. Dotyczy to każdej puli elastycznej, która jest bezpośrednio uwzględniona w grupie docelowej lub znajduje się wewnątrz serwera, który znajduje się w grupie docelowej. max_parallelism jest int.

#### <a name="return-code-values"></a>Zwróć wartości kodu

0 (sukces) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi

Po pomyślnym zasp_add_jobstepu numer bieżącej wersji zadania zostanie zwiększony. Przy następnym wykonaniu zadania zostanie użyta Nowa wersja. Jeśli zadanie jest aktualnie wykonywane, wykonanie nie będzie zawierać nowego kroku.

#### <a name="permissions"></a>Uprawnienia

Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ograniczają użytkownikowi możliwość monitorowania zadań, ale użytkownik może należeć do następującej roli bazy danych w bazie danych agentów zadań określonej podczas tworzenia agenta zadania:  

- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról, zobacz sekcję dotyczącą uprawnień w tym dokumencie. Tylko członkowie roli sysadmin mogą używać tej procedury składowanej do edytowania atrybutów zadań należących do innych użytkowników.

### <a name="sp_update_jobstep"></a><a name="sp_update_jobstep"></a>sp_update_jobstep

Aktualizuje krok zadania.

#### <a name="syntax"></a>Składnia

```syntaxsql
[jobs].sp_update_jobstep [ @job_name = ] 'job_name'
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]
     [ , [ @new_id = ] new_id ]
     [ , [ @new_name = ] 'new_name' ]
     [ , [ @command_type = ] 'command_type' ]
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]
     [ , [ @retry_attempts = ] retry_attempts ]
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]
     [ , [ @output_type = ] 'output_type' ]
     [ , [ @output_credential_name = ] 'output_credential_name' ]
     [ , [ @output_server_name = ] 'output_server_name' ]
     [ , [ @output_database_name = ] 'output_database_name' ]
     [ , [ @output_schema_name = ] 'output_schema_name' ]
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumenty

[ ** \@ job_name =** ] "job_name"  
Nazwa zadania, do którego należy ten krok. job_name jest nvarchar (128).

[ ** \@ step_id =** ] step_id  
Numer identyfikacyjny kroku zadania, który ma zostać zmodyfikowany. Należy określić wartość step_id lub step_name. step_id jest liczbą całkowitą.

[ ** \@ step_name =** ] "step_name"  
Nazwa kroku do zmodyfikowania. Należy określić wartość step_id lub step_name. step_name jest nvarchar (128).

[ ** \@ new_id =** ] new_id  
Numer identyfikacyjny nowej sekwencji dla kroku zadania. Numery identyfikacyjne kroków rozpoczynają się od 1 i zwiększają się bez przerw. W przypadku zmiany kolejności kroków kolejne kroki zostaną automatycznie roznumerowane.

[ ** \@ new_name =** ] "new_name"  
Nowa nazwa kroku. new_name jest nvarchar (128).

[ ** \@ command_type =** ] "command_type"  
Typ polecenia, które jest wykonywane przez ten JobStep. command_type jest nvarchar (50) z wartością domyślną TSql, co oznacza, że wartość @command_type parametru jest skrypt T-SQL.

Jeśli ta wartość jest określona, musi być TSql.

[ ** \@ command_source =** ] "command_source"  
Typ lokalizacji, w której jest przechowywane Polecenie. command_source jest nvarchar (50) z wartością domyślną Śródwierszowego, co oznacza, że wartość @command_source parametru jest tekstem literału polecenia.

Jeśli ta wartość jest określona, musi być wbudowana.

[ ** \@ Command =** ] "polecenie"  
Polecenia muszą być prawidłowym skryptem T-SQL i są następnie wykonywane przez ten krok zadania. polecenie jest typu nvarchar (max) i ma domyślnie wartość NULL.

[ ** \@ credential_name =** ] "credential_name"  
Nazwa poświadczenia zakresu bazy danych przechowywanej w tej bazie danych kontroli zadań, która jest używana do nawiązywania połączenia z każdą z docelowych baz danych w grupie docelowej po wykonaniu tego kroku. credential_name jest nvarchar (128).

[ ** \@ target_group_name =** ] "Target-Group_Name"  
Nazwa grupy docelowej zawierającej docelowe bazy danych, w których zostanie wykonany krok zadania. target_group_name jest nvarchar (128).

[ ** \@ initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Opóźnienie przed pierwszym ponowieniem próby, jeśli etap zadania zakończy się niepowodzeniem podczas próby wykonania początkowej. initial_retry_interval_seconds jest int z wartością domyślną 1.

[ ** \@ maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maksymalne opóźnienie między ponownymi próbami. Jeśli opóźnienie między kolejnymi próbami będzie większe niż ta wartość, zostanie ona ograniczona do tej wartości. maximum_retry_interval_seconds jest int z wartością domyślną 120.

[ ** \@ retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Mnożnik, który ma zostać zastosowany do opóźnienia ponowienia próby w przypadku niepowodzenia wykonywania wielu kroków zadania. Na przykład jeśli pierwsze ponowienie próby miało opóźnienie 5 sekund, a mnożnik wycofywania to 2,0, drugie ponowienie próby będzie miało opóźnienie 10 sekund, a trzecia ponowna próba będzie miała opóźnienie 20 sekund. retry_interval_backoff_multiplier jest prawdziwy, a wartość domyślna to 2,0.

[ ** \@ retry_attempts =** ] retry_attempts  
Liczba ponownych prób wykonania, jeśli próba początkowa nie powiedzie się. Jeśli na przykład wartość retry_attempts wynosi 10, zostanie wykorzystana 1 próba początkowa i 10 ponownych prób, co spowoduje całkowite 11 prób. Jeśli końcowa próba ponowienia nie powiedzie się, wykonywanie zadania zakończy się niepowodzeniem z cyklem życia. retry_attempts jest int, z wartością domyślną 10.

[ ** \@ step_timeout_seconds =** ] step_timeout_seconds  
Maksymalny czas, jaki może wykonać krok. W przypadku przekroczenia tego czasu wykonywanie zadania zakończy się z cyklem życia TimedOut. step_timeout_seconds jest int z wartością domyślną 43 200 sekund (12 godzin).

[ ** \@ output_type =** ] "output_type"  
Jeśli wartość nie jest równa null, typ lokalizacji docelowej, w której jest zapisywana pierwszy zestaw wyników polecenia. Aby zresetować wartość output_type z powrotem do wartości NULL, ustaw wartość tego parametru na "" (pusty ciąg). output_type jest nvarchar (50) z wartością domyślną RÓWNą NULL.

Jeśli ta wartość jest określona, musi to być SQLDatabase.

[ ** \@ output_credential_name =** ] "output_credential_name"  
Jeśli wartość nie jest równa null, nazwa poświadczenia zakresu bazy danych, która jest używana do nawiązywania połączenia z wyjściową docelową bazą danych. Należy określić, jeśli output_type jest równa SQLDatabase. Aby zresetować wartość output_credential_name z powrotem do wartości NULL, ustaw wartość tego parametru na "" (pusty ciąg). output_credential_name jest nvarchar (128) z wartością domyślną RÓWNą NULL.

[ ** \@ output_server_name =** ] "output_server_name"  
Jeśli wartość nie jest równa null, w pełni kwalifikowana nazwa DNS serwera, który zawiera wyjściową bazę danych wyjściowych. Należy określić, jeśli output_type jest równa SQLDatabase. Aby zresetować wartość output_server_name z powrotem do wartości NULL, ustaw wartość tego parametru na "" (pusty ciąg). output_server_name jest nvarchar (256), z wartością domyślną NULL.

[ ** \@ output_database_name =** ] "output_database_name"  
Jeśli wartość nie jest równa null, nazwa bazy danych zawierającej wyjściową tabelę docelową. Należy określić, jeśli output_type jest równa SQLDatabase. Aby zresetować wartość output_database_name z powrotem do wartości NULL, ustaw wartość tego parametru na "" (pusty ciąg). output_database_name jest nvarchar (128), z domyślną wartością NULL.

[ ** \@ output_schema_name =** ] "output_schema_name"  
Jeśli wartość nie jest równa null, nazwa schematu SQL zawierającego wyjściową tabelę docelową. Jeśli output_type jest równe SQLDatabase, wartość domyślna to dbo. Aby zresetować wartość output_schema_name z powrotem do wartości NULL, ustaw wartość tego parametru na "" (pusty ciąg). output_schema_name jest nvarchar (128).

[ ** \@ output_table_name =** ] "output_table_name"  
Jeśli wartość nie jest równa null, nazwa tabeli, w której zostanie zapisany pierwszy zestaw wyników polecenia. Jeśli tabela jeszcze nie istnieje, zostanie utworzona na podstawie schematu zestawu wynik zwracanego. Należy określić, jeśli output_type jest równa SQLDatabase. Aby zresetować wartość output_server_name z powrotem do wartości NULL, ustaw wartość tego parametru na "" (pusty ciąg). output_table_name jest nvarchar (128) z wartością domyślną RÓWNą NULL.

[ ** \@ job_version =** ] job_version dane wyjściowe  
Parametr wyjściowy, do którego zostanie przypisany nowy numer wersji zadania. job_version jest int.

[ ** \@ max_parallelism =** ] max_parallelism dane wyjściowe  
Maksymalny poziom równoległości na pulę elastyczną. Jeśli ta wartość jest ustawiona, krok zadania będzie ograniczony tylko do liczby baz danych na pulę elastyczną. Dotyczy to każdej puli elastycznej, która jest bezpośrednio uwzględniona w grupie docelowej lub znajduje się wewnątrz serwera, który znajduje się w grupie docelowej. Aby zresetować wartość max_parallelism z powrotem do wartości null, ustaw wartość tego parametru na-1. max_parallelism jest int.

#### <a name="return-code-values"></a>Zwróć wartości kodu

0 (sukces) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi

Nie wpłynie to na żadne wykonywanie zadania w toku. Po pomyślnym zasp_update_jobstepu numer wersji zadania zostanie zwiększony. Przy następnym wykonaniu zadania zostanie użyta Nowa wersja.

#### <a name="permissions"></a>Uprawnienia

Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ograniczają użytkownikowi możliwość monitorowania zadań, ale użytkownik może należeć do następującej roli bazy danych w bazie danych agentów zadań określonej podczas tworzenia agenta zadania:

- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról, zobacz sekcję dotyczącą uprawnień w tym dokumencie. Tylko członkowie roli sysadmin mogą używać tej procedury składowanej do edytowania atrybutów zadań należących do innych użytkowników

### <a name="sp_delete_jobstep"></a><a name="sp_delete_jobstep"></a>sp_delete_jobstep

Usuwa krok zadania z zadania.

#### <a name="syntax"></a>Składnia

```syntaxsql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argumenty

[ ** \@ job_name =** ] "job_name"  
Nazwa zadania, z którego zostanie usunięty krok. job_name jest nvarchar (128), bez domyślnego.

[ ** \@ step_id =** ] step_id  
Numer identyfikacyjny kroku zadania, który ma zostać usunięty. Należy określić wartość step_id lub step_name. step_id jest liczbą całkowitą.

[ ** \@ step_name =** ] "step_name"  
Nazwa kroku, który ma zostać usunięty. Należy określić wartość step_id lub step_name. step_name jest nvarchar (128).

[ ** \@ job_version =** ] job_version dane wyjściowe  
Parametr wyjściowy, do którego zostanie przypisany nowy numer wersji zadania. job_version jest int.

#### <a name="return-code-values"></a>Zwróć wartości kodu

0 (sukces) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi

Nie wpłynie to na żadne wykonywanie zadania w toku. Po pomyślnym zasp_update_jobstepu numer wersji zadania zostanie zwiększony. Przy następnym wykonaniu zadania zostanie użyta Nowa wersja.

Pozostałe kroki zadania zostaną automatycznie zmienione w celu wypełnienia przerwy pozostawionej przez usunięty krok zadania.

#### <a name="permissions"></a>Uprawnienia

Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ograniczają użytkownikowi możliwość monitorowania zadań, ale użytkownik może należeć do następującej roli bazy danych w bazie danych agentów zadań określonej podczas tworzenia agenta zadania:

- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról, zobacz sekcję dotyczącą uprawnień w tym dokumencie. Tylko członkowie roli sysadmin mogą używać tej procedury składowanej do edytowania atrybutów zadań należących do innych użytkowników.

### <a name="sp_start_job"></a><a name="sp_start_job"></a>sp_start_job

Uruchamia wykonywanie zadania.

#### <a name="syntax"></a>Składnia

```syntaxsql
[jobs].sp_start_job [ @job_name = ] 'job_name'
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]
```

#### <a name="arguments"></a>Argumenty

[ ** \@ job_name =** ] "job_name"  
Nazwa zadania, z którego zostanie usunięty krok. job_name jest nvarchar (128), bez domyślnego.

[ ** \@ job_execution_id =** ] job_execution_id dane wyjściowe  
Parametr wyjściowy, do którego zostanie przypisany identyfikator wykonywania zadania. job_version jest unikatowym identyfikatorem.

#### <a name="return-code-values"></a>Zwróć wartości kodu

0 (sukces) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi

Brak.

#### <a name="permissions"></a>Uprawnienia

Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ograniczają użytkownikowi możliwość monitorowania zadań, ale użytkownik może należeć do następującej roli bazy danych w bazie danych agentów zadań określonej podczas tworzenia agenta zadania:

- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról, zobacz sekcję dotyczącą uprawnień w tym dokumencie. Tylko członkowie roli sysadmin mogą używać tej procedury składowanej do edytowania atrybutów zadań należących do innych użytkowników.

### <a name="sp_stop_job"></a><a name="sp_stop_job"></a>sp_stop_job

Kończy wykonywanie zadania.

#### <a name="syntax"></a>Składnia

```syntaxsql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```

#### <a name="arguments"></a>Argumenty

[ ** \@ job_execution_id =** ] job_execution_id  
Numer identyfikacyjny wykonania zadania, który ma zostać zatrzymany. job_execution_id jest identyfikatorem unikatowym z wartością domyślną NULL.

#### <a name="return-code-values"></a>Zwróć wartości kodu

0 (sukces) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi

Brak.

#### <a name="permissions"></a>Uprawnienia

Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ograniczają użytkownikowi możliwość monitorowania zadań, ale użytkownik może należeć do następującej roli bazy danych w bazie danych agentów zadań określonej podczas tworzenia agenta zadania:

- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról, zobacz sekcję dotyczącą uprawnień w tym dokumencie. Tylko członkowie roli sysadmin mogą używać tej procedury składowanej do edytowania atrybutów zadań należących do innych użytkowników.

### <a name="sp_add_target_group"></a><a name="sp_add_target_group"></a>sp_add_target_group

Dodaje grupę docelową.

#### <a name="syntax"></a>Składnia

```syntaxsql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```

#### <a name="arguments"></a>Argumenty

[ ** \@ target_group_name =** ] "target_group_name"  
Nazwa grupy docelowej do utworzenia. target_group_name jest nvarchar (128), bez domyślnego.

[ ** \@ target_group_id =** ] target_group_id dane wyjściowe numeru identyfikacyjnego grupy docelowej przypisanego do zadania, jeśli zostało pomyślnie utworzone. target_group_id to zmienna wyjściowa typu o unikatowych wartościach z wartością NULL.

#### <a name="return-code-values"></a>Zwróć wartości kodu

0 (sukces) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi

Grupy docelowe umożliwiają łatwe kierowanie zadania w kolekcji baz danych.

#### <a name="permissions"></a>Uprawnienia

Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ograniczają użytkownikowi możliwość monitorowania zadań, ale użytkownik może należeć do następującej roli bazy danych w bazie danych agentów zadań określonej podczas tworzenia agenta zadania:

- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról, zobacz sekcję dotyczącą uprawnień w tym dokumencie. Tylko członkowie roli sysadmin mogą używać tej procedury składowanej do edytowania atrybutów zadań należących do innych użytkowników.

### <a name="sp_delete_target_group"></a><a name="sp_delete_target_group"></a>sp_delete_target_group

Usuwa grupę docelową.

#### <a name="syntax"></a>Składnia

```syntaxsql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```

#### <a name="arguments"></a>Argumenty

[ ** \@ target_group_name =** ] "target_group_name"  
Nazwa grupy docelowej do usunięcia. target_group_name jest nvarchar (128), bez domyślnego.

#### <a name="return-code-values"></a>Zwróć wartości kodu

0 (sukces) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi

Brak.

#### <a name="permissions"></a>Uprawnienia

Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ograniczają użytkownikowi możliwość monitorowania zadań, ale użytkownik może należeć do następującej roli bazy danych w bazie danych agentów zadań określonej podczas tworzenia agenta zadania:

- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról, zobacz sekcję dotyczącą uprawnień w tym dokumencie. Tylko członkowie roli sysadmin mogą używać tej procedury składowanej do edytowania atrybutów zadań należących do innych użytkowników.

### <a name="sp_add_target_group_member"></a><a name="sp_add_target_group_member"></a>sp_add_target_group_member

Dodaje bazę danych lub grupę baz danych do grupy docelowej.

#### <a name="syntax"></a>Składnia

```syntaxsql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] 'membership_type' ]
        [ , [ @target_type = ] 'target_type' ]
        [ , [ @refresh_credential_name = ] 'refresh_credential_name' ]
        [ , [ @server_name = ] 'server_name' ]
        [ , [ @database_name = ] 'database_name' ]
        [ , [ @elastic_pool_name = ] 'elastic_pool_name' ]
        [ , [ @shard_map_name = ] 'shard_map_name' ]
        [ , [ @target_id = ] 'target_id' OUTPUT ]
```

#### <a name="arguments"></a>Argumenty

[ ** \@ target_group_name =** ] "target_group_name"  
Nazwa grupy docelowej, do której zostanie dodany członek. target_group_name jest nvarchar (128), bez domyślnego.

[ ** \@ membership_type =** ] "membership_type"  
Określa, czy element członkowski grupy docelowej zostanie uwzględniony lub wykluczony. target_group_name jest nvarchar (128) z wartością domyślną "include". Prawidłowe wartości dla membership_type to "include" lub "exclude".

[ ** \@ target_type =** ] "target_type"  
Typ docelowej bazy danych lub kolekcji baz danych, w tym wszystkich baz danych na serwerze, wszystkich baz danych w puli elastycznej, wszystkich baz danych na mapie fragmentu lub pojedynczej bazy danych. target_type jest nvarchar (128), bez domyślnego. Prawidłowe wartości target_type to "SqlServer", "SqlElasticPool", "SQLDatabase" i "SqlShardMap".

[ ** \@ refresh_credential_name =** ] "refresh_credential_name"  
Nazwa poświadczenia w zakresie bazy danych. refresh_credential_name jest nvarchar (128), bez domyślnego.

[ ** \@ server_name =** ] "SERVER_NAME"  
Nazwa serwera, który ma zostać dodany do określonej grupy docelowej. server_name należy określić, gdy target_type to "SqlServer". server_name jest nvarchar (128), bez domyślnego.

[ ** \@ database_name =** ] "database_name"  
Nazwa bazy danych, która powinna zostać dodana do określonej grupy docelowej. database_name należy określić, gdy target_type to "SQLDatabase". database_name jest nvarchar (128), bez domyślnego.

[ ** \@ elastic_pool_name =** ] "elastic_pool_name"  
Nazwa puli elastycznej, która powinna zostać dodana do określonej grupy docelowej. elastic_pool_name należy określić, gdy target_type jest "SqlElasticPool". elastic_pool_name jest nvarchar (128), bez domyślnego.

[ ** \@ shard_map_name =** ] "shard_map_name"  
Nazwa puli map fragmentu, która powinna zostać dodana do określonej grupy docelowej. elastic_pool_name należy określić, gdy target_type jest "SqlShardMap". shard_map_name jest nvarchar (128), bez domyślnego.

[ ** \@ target_id =** ] target_group_id dane wyjściowe  
Docelowy numer identyfikacyjny przypisany do elementu członkowskiego grupy docelowej, jeśli został dodany do grupy docelowej. target_id to zmienna wyjściowa typu o unikatowych wartościach z wartością NULL.
Zwróć wartości kodu 0 (sukces) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi

Zadanie jest wykonywane na wszystkich pojedynczych bazach danych na serwerze lub w elastycznej puli w czasie wykonywania, gdy serwer lub Pula elastyczna jest uwzględniona w grupie docelowej.

#### <a name="permissions"></a>Uprawnienia

Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ograniczają użytkownikowi możliwość monitorowania zadań, ale użytkownik może należeć do następującej roli bazy danych w bazie danych agentów zadań określonej podczas tworzenia agenta zadania:

- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról, zobacz sekcję dotyczącą uprawnień w tym dokumencie. Tylko członkowie roli sysadmin mogą używać tej procedury składowanej do edytowania atrybutów zadań należących do innych użytkowników.

#### <a name="examples"></a>Przykłady

Poniższy przykład dodaje wszystkie bazy danych na serwerach Londyn i NewYork do serwerów grup, które utrzymują informacje o klientach. Należy nawiązać połączenie z bazą danych zadań określoną podczas tworzenia agenta zadań, w tym przypadku ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ;
GO

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group @target_group_name =  N'Servers Maintaining Customer Information'
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net' ;
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'NewYork.database.windows.net' ;
GO

--View the recently added members to the target group
SELECT * FROM [jobs].target_group_members WHERE target_group_name= N'Servers Maintaining Customer Information';
GO
```

### <a name="sp_delete_target_group_member"></a><a name="sp_delete_target_group_member"></a>sp_delete_target_group_member

Usuwa element członkowski grupy docelowej z grupy docelowej.

#### <a name="syntax"></a>Składnia

```syntaxsql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
   [ , [ @target_id = ] 'target_id']
```

#### <a name="arguments"></a>Argumenty

[ @target_group_name =] "target_group_name"  
Nazwa grupy docelowej, z której ma zostać usunięty członek grupy docelowej. target_group_name jest nvarchar (128), bez domyślnego.

[ @target_id =] target_id  
 Docelowy numer identyfikacyjny przypisany do elementu członkowskiego grupy docelowej, który ma zostać usunięty. target_id jest unikatowym identyfikatorem i ma domyślnie wartość NULL.

#### <a name="return-code-values"></a>Zwróć wartości kodu

0 (sukces) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi

Grupy docelowe umożliwiają łatwe kierowanie zadania w kolekcji baz danych.

#### <a name="permissions"></a>Uprawnienia

Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ograniczają użytkownikowi możliwość monitorowania zadań, ale użytkownik może należeć do następującej roli bazy danych w bazie danych agentów zadań określonej podczas tworzenia agenta zadania:

- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról, zobacz sekcję dotyczącą uprawnień w tym dokumencie. Tylko członkowie roli sysadmin mogą używać tej procedury składowanej do edytowania atrybutów zadań należących do innych użytkowników.

#### <a name="examples"></a>Przykłady

Poniższy przykład usuwa serwer Londyn z serwerów grupy, które utrzymują informacje o klientach. Należy nawiązać połączenie z bazą danych zadań określoną podczas tworzenia agenta zadań, w tym przypadku ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ;
GO

-- Retrieve the target_id for a target_group_members
declare @tid uniqueidentifier
SELECT @tid = target_id FROM [jobs].target_group_members WHERE target_group_name = 'Servers Maintaining Customer Information' and server_name = 'London.database.windows.net'

-- Remove a target group member of type server
EXEC jobs.sp_delete_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_id = @tid
GO
```

### <a name="sp_purge_jobhistory"></a><a name="sp_purge_jobhistory"></a>sp_purge_jobhistory

Usuwa rekordy historii zadania.

#### <a name="syntax"></a>Składnia

```syntaxsql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argumenty

[ ** \@ job_name =** ] "job_name"  
Nazwa zadania, dla którego mają zostać usunięte rekordy historii. job_name jest nvarchar (128), z domyślną wartością NULL. Należy określić parametr job_id lub job_name, ale nie można określić obu tych wartości.

[ ** \@ job_id =** ] job_id  
 Numer identyfikacyjny zadania, dla którego mają zostać usunięte rekordy. job_id jest unikatowym identyfikatorem z wartością NULL. Należy określić parametr job_id lub job_name, ale nie można określić obu tych wartości.

[ ** \@ oldest_date =** ] oldest_date  
 Najstarszy rekord do zachowania w historii. oldest_date jest DATETIME2, a wartością domyślną jest NULL. Gdy oldest_date jest określony, sp_purge_jobhistory usuwa tylko te rekordy, które są starsze niż określona wartość.

#### <a name="return-code-values"></a>Zwróć wartości kodu

0 (sukces) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi

Grupy docelowe umożliwiają łatwe kierowanie zadania w kolekcji baz danych.

#### <a name="permissions"></a>Uprawnienia

Domyślnie członkowie stałej roli serwera sysadmin mogą wykonać tę procedurę składowaną. Ograniczają użytkownikowi możliwość monitorowania zadań, ale użytkownik może należeć do następującej roli bazy danych w bazie danych agentów zadań określonej podczas tworzenia agenta zadania:

- jobs_reader

Aby uzyskać szczegółowe informacje o uprawnieniach tych ról, zobacz sekcję dotyczącą uprawnień w tym dokumencie. Tylko członkowie roli sysadmin mogą używać tej procedury składowanej do edytowania atrybutów zadań należących do innych użytkowników.

#### <a name="examples"></a>Przykłady

Poniższy przykład dodaje wszystkie bazy danych na serwerach Londyn i NewYork do serwerów grup, które utrzymują informacje o klientach. Należy nawiązać połączenie z bazą danych zadań określoną podczas tworzenia agenta zadań, w tym przypadku ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```

## <a name="job-views"></a>Widoki zadań

Poniższe widoki są dostępne w [bazie danych zadań](job-automation-overview.md#job-database).

|Widok  |Opis  |
|---------|---------|
|[job_executions](#job_executions-view)     |  Pokazuje historię wykonywania zadań.      |
|[zadania](#jobs-view)     |   Pokazuje wszystkie zadania.      |
|[job_versions](#job_versions-view)     |   Pokazuje wszystkie wersje zadań.      |
|[jobsteps](#jobsteps-view)     |     Przedstawia wszystkie kroki w bieżącej wersji każdego zadania.    |
|[jobstep_versions](#jobstep_versions-view)     |     Przedstawia wszystkie kroki we wszystkich wersjach każdego zadania.    |
|[target_groups](#target_groups-view)     |      Pokazuje wszystkie grupy docelowe.   |
|[target_group_members](#target_group_members-view)     |   Pokazuje wszystkie elementy członkowskie wszystkich grup docelowych.      |

### <a name="job_executions-view"></a><a name="job_executions-view"></a>Widok job_executions

[zadania]. [job_executions]

Pokazuje historię wykonywania zadań.

|Nazwa kolumny | Typ danych | Opis |
|---------|---------|---------|
|**job_execution_id** | uniqueidentifier | Unikatowy identyfikator wystąpienia wykonania zadania.
|**job_name** | nvarchar (128) | Nazwa zadania.
|**job_id** | uniqueidentifier | Unikatowy identyfikator zadania.
|**job_version** | int | Wersja zadania (automatycznie aktualizowana za każdym razem, gdy zadanie jest modyfikowane).
|**step_id** |int | Unikatowy identyfikator (dla tego zadania) dla kroku. Wartość NULL wskazuje, że jest to zadanie nadrzędne.
|**is_active** | bit | Wskazuje, czy informacje są aktywne, czy nieaktywne. 1 oznacza aktywne zadania, a wartość 0 oznacza nieaktywną.
|**witrynę** | nvarchar (50) | Wartość wskazująca stan zadania: "created", "w toku", "zakończony niepowodzeniem", "powodzenie", "Pominięcie", "SucceededWithSkipped"|
|**create_time**| datetime2 (7) | Data i godzina utworzenia zadania.
|**start_time** | datetime2 (7) | Data i godzina wykonania zadania. Wartość NULL, jeśli zadanie nie zostało jeszcze wykonane.
|**end_time** | datetime2 (7) | Data i godzina zakończenia wykonywania zadania. Wartość NULL, jeśli zadanie nie zostało jeszcze wykonane lub nie zostało jeszcze ukończone.
|**current_attempts** | int | Liczba ponownych prób wykonania kroku. Zadanie nadrzędne zostanie równe 0, a podrzędne wykonania zadania będą mieć wartość 1 lub wyższą na podstawie zasad wykonywania.
|**current_attempt_start_time** | datetime2 (7) | Data i godzina wykonania zadania. Wartość NULL wskazuje, że jest to zadanie nadrzędne.
|**last_message** | nvarchar (max) | Komunikat historii zadania lub kroku.
|**target_type** | nvarchar (128) | Typ docelowej bazy danych lub kolekcji baz danych, w tym wszystkich baz danych na serwerze, wszystkich baz danych w puli elastycznej lub w bazie danych. Prawidłowe wartości dla target_type to "SqlServer", "SqlElasticPool" lub "SQLDatabase". Wartość NULL wskazuje, że jest to zadanie nadrzędne.
|**target_id** | uniqueidentifier | Unikatowy identyfikator członka grupy docelowej.  Wartość NULL wskazuje, że jest to zadanie nadrzędne.
|**target_group_name** | nvarchar (128) | Nazwa grupy docelowej. Wartość NULL wskazuje, że jest to zadanie nadrzędne.
|**target_server_name** | nvarchar (256)  | Nazwa serwera znajdującego się w grupie docelowej. Określany tylko wtedy, gdy target_type to "SqlServer". Wartość NULL wskazuje, że jest to zadanie nadrzędne.
|**target_database_name** | nvarchar (128) | Nazwa bazy danych zawartej w grupie docelowej. Określany tylko wtedy, gdy target_type jest "SQLDatabase". Wartość NULL wskazuje, że jest to zadanie nadrzędne.

### <a name="jobs-view"></a>Widok zadań

[zadania]. zadania

Pokazuje wszystkie zadania.

|Nazwa kolumny | Typ danych |Opis|
|------|------|-------|
|**job_name** | nvarchar (128) | Nazwa zadania.|
|**job_id**| uniqueidentifier |Unikatowy identyfikator zadania.|
|**job_version** |int |Wersja zadania (automatycznie aktualizowana za każdym razem, gdy zadanie jest modyfikowane).|
|**zharmonizowan** |nvarchar (512)| Opis zadania. Włączony bit: wskazuje, czy zadanie jest włączone, czy wyłączone. 1 oznacza włączone zadania, a wartość 0 oznacza wyłączone zadania.|
|**schedule_interval_type**|nvarchar (50) |Wartość wskazująca, kiedy zadanie ma zostać wykonane: "raz", "min", "godz.", "Days", "tygodnie", "months"
|**schedule_interval_count**|int|Liczba schedule_interval_typeych okresów między każdym wykonaniem zadania.|
|**schedule_start_time**|datetime2 (7)|Data i godzina ostatniego uruchomienia zadania.|
|**schedule_end_time**|datetime2 (7)|Data i godzina ostatniego wykonania zadania.|

### <a name="job_versions-view"></a><a name="job_versions-view"></a>Widok job_versions

[zadania]. [job_versions]

Pokazuje wszystkie wersje zadań.

|Nazwa kolumny|Typ danych|Opis|
|------|------|-------|
|**job_name**|nvarchar (128)|Nazwa zadania.|
|**job_id**|uniqueidentifier|Unikatowy identyfikator zadania.|
|**job_version**|int|Wersja zadania (automatycznie aktualizowana za każdym razem, gdy zadanie jest modyfikowane).|

### <a name="jobsteps-view"></a>Widok JobSteps

[zadania]. [jobsteps]

Przedstawia wszystkie kroki w bieżącej wersji każdego zadania.

|Nazwa kolumny|Typ danych|Opis|
|------|------|-------|
|**job_name**|nvarchar (128)|Nazwa zadania.|
|**job_id**|uniqueidentifier|Unikatowy identyfikator zadania.|
|**job_version**|int|Wersja zadania (automatycznie aktualizowana za każdym razem, gdy zadanie jest modyfikowane).|
|**step_id**|int|Unikatowy identyfikator (dla tego zadania) dla kroku.|
|**step_name**|nvarchar (128)|Unikatowa nazwa (dla tego zadania) dla tego kroku.|
|**command_type**|nvarchar (50)|Typ polecenia do wykonania w kroku zadania. Dla wersji 1 musi być równa i domyślna wartość "TSql".|
|**command_source**|nvarchar (50)|Lokalizacja polecenia. W wersji 1 wartość "inline" jest wartością domyślną i jedyną zaakceptowaną wartością.|
|**dotyczące**|nvarchar (max)|Polecenia, które mają być wykonywane przez zadania elastyczne za pomocą command_type.|
|**credential_name**|nvarchar (128)|Nazwa poświadczenia w zakresie bazy danych używanej do wykonywania zadania.|
|**target_group_name**|nvarchar (128)|Nazwa grupy docelowej.|
|**target_group_id**|uniqueidentifier|Unikatowy identyfikator grupy docelowej.|
|**initial_retry_interval_seconds**|int|Opóźnienie przed pierwszym ponowieniem próby. Wartość domyślna to 1.|
|**maximum_retry_interval_seconds**|int|Maksymalne opóźnienie między ponownymi próbami. Jeśli opóźnienie między kolejnymi próbami będzie większe niż ta wartość, zostanie ona ograniczona do tej wartości. Wartość domyślna to 120.|
|**retry_interval_backoff_multiplier**|liczba rzeczywista|Mnożnik, który ma zostać zastosowany do opóźnienia ponowienia próby w przypadku niepowodzenia wykonywania wielu kroków zadania. Wartość domyślna to 2,0.|
|**retry_attempts**|int|Liczba ponownych prób do użycia, jeśli ten krok zakończy się niepowodzeniem. Wartość domyślna to 10, co oznacza, że nie ponowienia próby.|
|**step_timeout_seconds**|int|Czas (w minutach) między ponownymi próbami. Wartość domyślna to 0, co oznacza interwał 0 minut.|
|**output_type**|nvarchar (11)|Lokalizacja polecenia. W bieżącej wersji zapoznawczej "inline" jest wartością domyślną i jedyną zaakceptowaną wartością.|
|**output_credential_name**|nvarchar (128)|Nazwa poświadczeń do użycia w celu nawiązania połączenia z serwerem docelowym w celu zapisania zestawu wyników.|
|**output_subscription_id**|uniqueidentifier|Unikatowy identyfikator subskrypcji server\database docelowego dla zestawu wyników w wyniku wykonywania zapytania.|
|**output_resource_group_name**|nvarchar (128)|Nazwa grupy zasobów, w której znajduje się serwer docelowy.|
|**output_server_name**|nvarchar (256)|Nazwa serwera docelowego dla zestawu wyników.|
|**output_database_name**|nvarchar (128)|Nazwa docelowej bazy danych dla zestawu wyników.|
|**output_schema_name**|nvarchar (max)|Nazwa schematu docelowego. Jeśli nie zostanie określony, domyślnie jest używany obiekt dbo.|
|**output_table_name**|nvarchar (max)|Nazwa tabeli, w której mają być przechowywane wyniki z wyników zapytania. Tabela zostanie utworzona automatycznie na podstawie schematu zestawu wyników, jeśli jeszcze nie istnieje. Schemat musi być zgodny ze schematem zestawu wyników.|
|**max_parallelism**|int|Maksymalna liczba baz danych na pulę elastyczną, w której krok zadania będzie uruchamiany w danym momencie. Wartość domyślna to NULL, co oznacza brak limitu. |

### <a name="jobstep_versions-view"></a><a name="jobstep_versions-view"></a>Widok jobstep_versions

[zadania]. [jobstep_versions]

Przedstawia wszystkie kroki we wszystkich wersjach każdego zadania. Schemat jest taki sam jak [JobSteps](#jobsteps-view).

### <a name="target_groups-view"></a><a name="target_groups-view"></a>Widok target_groups

[zadania]. [target_groups]

Wyświetla listę wszystkich grup docelowych.

|Nazwa kolumny|Typ danych|Opis|
|-----|-----|-----|
|**target_group_name**|nvarchar (128)|Nazwa grupy docelowej, kolekcja baz danych.
|**target_group_id**|uniqueidentifier|Unikatowy identyfikator grupy docelowej.

### <a name="target_group_members-view"></a><a name="target_group_members-view"></a>Widok target_group_members

[zadania]. [target_group_members]

Pokazuje wszystkie elementy członkowskie wszystkich grup docelowych.

|Nazwa kolumny|Typ danych|Opis|
|-----|-----|-----|
|**target_group_name**|nvarchar (128|Nazwa grupy docelowej, kolekcja baz danych. |
|**target_group_id**|uniqueidentifier|Unikatowy identyfikator grupy docelowej.|
|**membership_type**|int|Określa, czy element członkowski grupy docelowej jest dołączony lub wykluczony w grupie docelowej. Prawidłowe wartości dla target_group_name to "include" lub "exclude".|
|**target_type**|nvarchar (128)|Typ docelowej bazy danych lub kolekcji baz danych, w tym wszystkich baz danych na serwerze, wszystkich baz danych w puli elastycznej lub w bazie danych. Prawidłowe wartości target_type to "SqlServer", "SqlElasticPool", "SQLDatabase" i "SqlShardMap".|
|**target_id**|uniqueidentifier|Unikatowy identyfikator członka grupy docelowej.|
|**refresh_credential_name**|nvarchar (128)|Nazwa poświadczenia w zakresie bazy danych używanej do łączenia się z członkiem grupy docelowej.|
|**subscription_id**|uniqueidentifier|Unikatowy identyfikator subskrypcji.|
|**resource_group_name**|nvarchar (128)|Nazwa grupy zasobów, w której znajduje się członek grupy docelowej.|
|**server_name**|nvarchar (128)|Nazwa serwera znajdującego się w grupie docelowej. Określany tylko wtedy, gdy target_type to "SqlServer". |
|**database_name**|nvarchar (128)|Nazwa bazy danych zawartej w grupie docelowej. Określany tylko wtedy, gdy target_type jest "SQLDatabase".|
|**elastic_pool_name**|nvarchar (128)|Nazwa elastycznej puli zawartej w grupie docelowej. Określany tylko wtedy, gdy target_type jest "SqlElasticPool".|
|**shard_map_name**|nvarchar (128)|Nazwa map fragmentu znajdujących się w grupie docelowej. Określany tylko wtedy, gdy target_type jest "SqlShardMap".|

## <a name="resources"></a>Zasoby

- ![Ikona linku tematu](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "Ikona linku tematu") [— konwencje języka Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  

## <a name="next-steps"></a>Następne kroki

- [Tworzenie zadań elastycznych i zarządzanie nimi przy użyciu programu PowerShell](elastic-jobs-powershell-create.md)
- [Autoryzacja i uprawnienia](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
