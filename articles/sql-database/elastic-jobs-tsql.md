---
title: Tworzenie zadań elastycznej bazy danych i zarządzanie nimi za pomocą programu Transact-SQL (T-SQL)
description: Uruchom skrypty w wielu bazach danych za pomocą agenta zadania elastycznej bazy danych przy użyciu usługi Transact-SQL (T-SQL).
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
ms.date: 02/07/2020
ms.openlocfilehash: 740a42dc94cdfa8d5c5a91b32b58cbff4c1bcda0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687766"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>Tworzenie zadań elastycznej bazy danych za pomocą funkcji Transact-SQL (T-SQL) i zarządzanie nimi

Ten artykuł zawiera wiele przykładowych scenariuszy, aby rozpocząć pracę z zadaniami elastycznymi przy użyciu języka T-SQL.

W przykładach użyto [procedur składowanych](#job-stored-procedures) i [widoków](#job-views) dostępnych w [*bazie danych zadań*](sql-database-job-automation-overview.md#job-database).

Transact-SQL (T-SQL) służy do tworzenia, konfigurowania, wykonywania i zarządzania zadaniami. Tworzenie agenta zadania elastycznego nie jest obsługiwane w języku T-SQL, dlatego należy najpierw utworzyć *agenta zadania elastycznego* za pomocą portalu lub [programu PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent).


## <a name="create-a-credential-for-job-execution"></a>Tworzenie poświadczenia do wykonania zadania

Poświadczenia są używane do łączenia się z docelowymi bazami danych w celu wykonania skryptu. Poświadczenie wymaga odpowiednich uprawnień w bazach danych określonych przez grupę docelową, aby pomyślnie wykonać skrypt. Podczas korzystania z serwera i/lub puli element członkowski grupy docelowej, jest wysoce zalecane, aby utworzyć poświadczenia głównego do użycia w celu odświeżenia poświadczeń przed rozszerzeniem serwera i/lub puli w czasie wykonywania zadania. Poświadczenia o określonym zakresie bazy danych są tworzone w bazie danych agenta zadań. Te same poświadczenia muszą być używane do *tworzenia logowania* i tworzenia użytkownika *z logowania, aby udzielić uprawnień bazy danych logowania* w docelowych bazach danych.


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

## <a name="create-a-target-group-servers"></a>Tworzenie grupy docelowej (serwerów)

W poniższym przykładzie pokazano, jak wykonać zadanie dla wszystkich baz danych na serwerze.  
Połącz się z [*bazą danych zadań*](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:


```sql
-- Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group 'ServerGroup1'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
'ServerGroup1',
@target_type = 'SqlServer',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net'

--View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name='ServerGroup1';
SELECT * FROM jobs.target_group_members WHERE target_group_name='ServerGroup1';
```


## <a name="exclude-an-individual-database"></a>Wykluczanie pojedynczej bazy danych

W poniższym przykładzie pokazano, jak wykonać zadanie dla wszystkich baz danych na serwerze bazy danych SQL, z wyjątkiem bazy danych o nazwie *MappingDB*.  
Połącz się z [*bazą danych zadań*](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group N'ServerGroup'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
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

W poniższym przykładzie pokazano, jak kierować wszystkie bazy danych w jednej lub więcej pul elastycznych.  
Połącz się z [*bazą danych zadań*](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing pool(s)
EXEC jobs.sp_add_target_group 'PoolGroup'

-- Add an elastic pool(s) target member
EXEC jobs.sp_add_target_group_member
'PoolGroup',
@target_type = 'SqlElasticPool',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net',
@elastic_pool_name='ElasticPool-1'

-- View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name = N'PoolGroup';
SELECT * FROM jobs.target_group_members WHERE target_group_name = N'PoolGroup';
```


## <a name="deploy-new-schema-to-many-databases"></a>Wdrażanie nowego schematu w wielu bazach danych

W poniższym przykładzie pokazano, jak wdrożyć nowy schemat do wszystkich baz danych.  
Połącz się z [*bazą danych zadań*](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:


```sql
--Connect to the job database specified when creating the job agent

--Add job for create table
EXEC jobs.sp_add_job @job_name='CreateTableTest', @description='Create Table Test'

-- Add job step for create table
EXEC jobs.sp_add_jobstep @job_name='CreateTableTest',
@command=N'IF NOT EXISTS (SELECT * FROM sys.tables 
            WHERE object_id = object_id(''Test''))
CREATE TABLE [dbo].[Test]([TestId] [int] NOT NULL);',
@credential_name='myjobcred',
@target_group_name='PoolGroup'
```


## <a name="data-collection-using-built-in-parameters"></a>Zbieranie danych przy użyciu wbudowanych parametrów

W wielu scenariuszach zbierania danych może być przydatne do uwzględnienia niektórych z tych zmiennych skryptów, aby pomóc po przetworzeniu wyników zadania.

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

Na przykład, aby zgrupować wszystkie wyniki z tego samego wykonania zadania razem, użyj *$(job_execution_id),* jak pokazano w następującym poleceniu:


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>Monitorowanie wydajności bazy danych

Poniższy przykład tworzy nowe zadanie do zbierania danych o wydajności z wielu baz danych.

Domyślnie agent zadania utworzy tabelę danych wyjściowych do przechowywania zwróconych wyników. W związku z tym podmiot bazy danych skojarzony z poświadczeniami `CREATE TABLE` wyjściowymi musi `ALTER` `SELECT`mieć `INSERT` `DELETE` co najmniej następujące uprawnienia: w `SELECT` bazie danych , , , w tabeli danych wyjściowych lub jej schemacie i w widoku katalogu [sys.indexes.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql)

Jeśli chcesz ręcznie utworzyć tabelę z wyprzedzeniem, musi mieć następujące właściwości:
1. Kolumny z poprawną nazwą i typami danych dla zestawu wyników.
2. Dodatkowa kolumna dla internal_execution_id z typem danych uniqueidentifier.
3. Indeks nieklastrowany `IX_<TableName>_Internal_Execution_ID` nazwany w kolumnie internal_execution_id.
4. Wszystkie uprawnienia wymienione powyżej `CREATE TABLE` z wyjątkiem uprawnień do bazy danych.

Połącz się z [*bazą danych zadań*](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenia:

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


## <a name="view-job-definitions"></a>Wyświetlanie definicji zadań

W poniższym przykładzie pokazano, jak wyświetlić bieżące definicje zadań.  
Połącz się z [*bazą danych zadań*](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:

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


## <a name="begin-ad-hoc-execution-of-a-job"></a>Rozpoczynanie doraźnego wykonywania zadania

W poniższym przykładzie pokazano, jak natychmiast rozpocząć zadanie.  
Połącz się z [*bazą danych zadań*](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:

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


## <a name="schedule-execution-of-a-job"></a>Planowanie wykonywania zadania

W poniższym przykładzie pokazano, jak zaplanować zadanie do wykonania w przyszłości.  
Połącz się z [*bazą danych zadań*](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>Monitorowanie stanu wykonania zadania

W poniższym przykładzie pokazano, jak wyświetlić szczegóły stanu wykonania dla wszystkich zadań.  
Połącz się z [*bazą danych zadań*](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:

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

W poniższym przykładzie pokazano, jak anulować zadanie.  
Połącz się z [*bazą danych zadań*](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:

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


## <a name="delete-old-job-history"></a>Usuwanie starej historii zadań

W poniższym przykładzie pokazano, jak usunąć historię zadań przed określoną datą.  
Połącz się z [*bazą danych zadań*](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Usuwanie zadania i całej jego historii zadań

W poniższym przykładzie pokazano, jak usunąć zadanie i całą powiązaną historię zadań.  
Połącz się z [*bazą danych zadań*](sql-database-job-automation-overview.md#job-database) i uruchom następujące polecenie:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>Procedury przechowywane w zadaniach

Poniższe procedury przechowywane znajdują się w [bazie danych zadań](sql-database-job-automation-overview.md#job-database).



|Procedura składowana  |Opis  |
|---------|---------|
|[sp_add_job](#sp_add_job)     |     Dodaje nowe zadanie.    |
|[Sp_update_job](#sp_update_job)    |      Aktualizuje istniejące zadanie.   |
|[sp_delete_job](#sp_delete_job)     |      Usuwa istniejące zadanie.   |
|[Sp_add_jobstep](#sp_add_jobstep)    |    Dodaje krok do zadania.     |
|[sp_update_jobstep](#sp_update_jobstep)     |     Aktualizuje krok zadania.    |
|[sp_delete_jobstep](#sp_delete_jobstep)     |     Usuwa krok zadania.    |
|[Sp_start_job](#sp_start_job)    |  Rozpoczyna wykonywanie zadania.       |
|[Sp_stop_job](#sp_stop_job)     |     Zatrzymuje wykonywanie zadania.   |
|[sp_add_target_group](#sp_add_target_group)    |     Dodaje grupę docelową.    |
|[sp_delete_target_group](#sp_delete_target_group)     |    Usuwa grupę docelową.     |
|[sp_add_target_group_member](#sp_add_target_group_member)     |    Dodaje bazę danych lub grupę baz danych do grupy docelowej.     |
|[sp_delete_target_group_member](#sp_delete_target_group_member)     |     Usuwa członka grupy docelowej z grupy docelowej.    |
|[Sp_purge_jobhistory](#sp_purge_jobhistory)    |    Usuwa rekordy historii dla zadania.     |





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

[ ** \@job_name =** ] "job_name"  
Nazwa zadania. Nazwa musi być unikatowa i nie może zawierać procentu (%) Znaków. job_name jest nvarchar(128), bez wartości domyślnej.

[ ** \@opis =** ] "opis"  
Opis zadania. opis to nvarchar(512), z wartością null. Jeśli opis zostanie pominięty, używany jest pusty ciąg.

[ ** \@włączony =** ] włączony  
Czy harmonogram zadania jest włączony. Enabled jest bit, z domyślną wartością 0 (wyłączona). Jeśli 0, zadanie nie jest włączone i nie działa zgodnie z jego harmonogramem; jednak można go uruchomić ręcznie. Jeśli 1, zadanie będzie działać zgodnie z jego harmonogramem i może być również uruchamiane ręcznie.

[ ** \@schedule_interval_type =**] schedule_interval_type  
Wartość wskazuje, kiedy zadanie ma zostać wykonane. schedule_interval_type jest nvarchar(50), z domyślną wartośćą Once i może być jedną z następujących wartości:
- "Raz",
- "Minuty",
- "Godziny",
- "Dni",
- "Tygodnie",
- "Miesiące"

[ ** \@schedule_interval_count =** ] schedule_interval_count  
Liczba schedule_interval_count okresów, które mają wystąpić między każdym wykonaniem zadania. schedule_interval_count jest int, z domyślnym 1. Wartość musi być większa lub równa 1.

[ ** \@schedule_start_time =** ] schedule_start_time  
Data rozpoczęcia wykonywania zadania. schedule_start_time jest DATETIME2, z domyślną wartością 0001-01-01 00:00:00.000000.

[ ** \@schedule_end_time =** ] schedule_end_time  
Data zatrzymania wykonania zadania. schedule_end_time jest DATETIME2, z domyślną wartością 9999-12-31 11:59:59.0000000. 

[ ** \@job_id =** ] job_id WYJŚCIE  
Numer identyfikacyjny zadania przypisany do zadania, jeśli został pomyślnie utworzony. job_id jest zmienną wyjściową typu uniqueidentifier.

#### <a name="return-code-values"></a>Zwracane wartości kodu

0 (sukces) lub 1 (porażka)

#### <a name="remarks"></a>Uwagi
sp_add_job musi być uruchamiany z bazy danych agenta zadań określonej podczas tworzenia agenta zadania.
Po wykonaniu sp_add_job, aby dodać zadanie, sp_add_jobstep może służyć do dodawania kroków, które wykonują działania dla zadania. Początkowy numer wersji zadania wynosi 0, który zostanie zwiększony do 1 po dodaniu pierwszego kroku.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie roli serwera stałego sysadmin mogą wykonać tę procedurę składowaną. Ograniczają one użytkownika do po prostu być w stanie monitorować zadania, można przyznać użytkownikowi, aby być częścią następującej roli bazy danych w bazie danych agenta zadań określony podczas tworzenia agenta zadania:

- jobs_reader

Aby uzyskać szczegółowe informacje na temat uprawnień tych ról, zobacz permission sekcji w tym dokumencie. Tylko członkowie sysadmin można użyć tej procedury składowanej do edycji atrybutów zadań, które są własnością innych użytkowników.

### <a name="sp_update_job"></a><a name="sp_update_job"></a>Sp_update_job

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
[ ** \@job_name =** ] "job_name"  
Nazwa zadania, które ma zostać zaktualizowane. job_name jest nvarchar(128).

[ ** \@new_name =** ] "new_name"  
Nowa nazwa zadania. new_name jest nvarchar(128).

[ ** \@opis =** ] "opis"  
Opis zadania. opis jest nvarchar(512).

[ ** \@włączony =** ] włączony  
Określa, czy harmonogram zadania jest włączony (1) czy nie włączony (0). Włączona jest bit.

[ ** \@schedule_interval_type =** ] schedule_interval_type  
Wartość wskazuje, kiedy zadanie ma zostać wykonane. schedule_interval_type jest nvarchar(50) i może być jedną z następujących wartości:

- "Raz",
- "Minuty",
- "Godziny",
- "Dni",
- "Tygodnie",
- "Miesiące"

[ ** \@schedule_interval_count =** ] schedule_interval_count  
Liczba schedule_interval_count okresów, które mają wystąpić między każdym wykonaniem zadania. schedule_interval_count jest int, z domyślnym 1. Wartość musi być większa lub równa 1.

[ ** \@schedule_start_time =** ] schedule_start_time  
Data rozpoczęcia wykonywania zadania. schedule_start_time jest DATETIME2, z domyślną wartością 0001-01-01 00:00:00.000000.

[ ** \@schedule_end_time =** ] schedule_end_time  
Data zatrzymania wykonania zadania. schedule_end_time jest DATETIME2, z domyślną wartością 9999-12-31 11:59:59.0000000. 

#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (sukces) lub 1 (porażka)

#### <a name="remarks"></a>Uwagi
Po wykonaniu sp_add_job, aby dodać zadanie, sp_add_jobstep może służyć do dodawania kroków, które wykonują działania dla zadania. Początkowy numer wersji zadania wynosi 0, który zostanie zwiększony do 1 po dodaniu pierwszego kroku.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie roli serwera stałego sysadmin mogą wykonać tę procedurę składowaną. Ograniczają one użytkownika do po prostu być w stanie monitorować zadania, można przyznać użytkownikowi, aby być częścią następującej roli bazy danych w bazie danych agenta zadań określony podczas tworzenia agenta zadania:
- jobs_reader

Aby uzyskać szczegółowe informacje na temat uprawnień tych ról, zobacz permission sekcji w tym dokumencie. Tylko członkowie sysadmin można użyć tej procedury składowanej do edycji atrybutów zadań, które są własnością innych użytkowników.



### <a name="sp_delete_job"></a><a name="sp_delete_job"></a>sp_delete_job

Usuwa istniejące zadanie.

#### <a name="syntax"></a>Składnia

```syntaxsql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argumenty
[ ** \@job_name =** ] "job_name"  
Nazwa zadania do usunięcia. job_name jest nvarchar(128).

[ ** \@force =** ] siła  
Określa, czy usunąć, jeśli zadanie ma żadnych wykonań w toku i anulować wszystkie wykonania w toku (1) lub zakończyć się niepowodzeniem, jeśli wszystkie wykonania zadania są w toku (0). siła jest nieco.

#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (sukces) lub 1 (porażka)

#### <a name="remarks"></a>Uwagi
Historia zadań jest automatycznie usuwana po usunięciu zadania.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie roli serwera stałego sysadmin mogą wykonać tę procedurę składowaną. Ograniczają one użytkownika do po prostu być w stanie monitorować zadania, można przyznać użytkownikowi, aby być częścią następującej roli bazy danych w bazie danych agenta zadań określony podczas tworzenia agenta zadania:
- jobs_reader

Aby uzyskać szczegółowe informacje na temat uprawnień tych ról, zobacz permission sekcji w tym dokumencie. Tylko członkowie sysadmin można użyć tej procedury składowanej do edycji atrybutów zadań, które są własnością innych użytkowników.



### <a name="sp_add_jobstep"></a><a name="sp_add_jobstep"></a>Sp_add_jobstep

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

[ ** \@job_name =** ] "job_name"  
Nazwa zadania, do którego należy dodać krok. job_name jest nvarchar(128).

[ ** \@step_id =** ] step_id  
Numer identyfikacyjny sekwencji dla kroku zadania. Numery identyfikacyjne kroków zaczynają się od 1 i przyrostowe bez przerw. Jeśli istniejący krok ma już ten identyfikator, ten krok i wszystkie poniższe kroki będą zwiększane w celu zwiększenia ich identyfikatora, tak aby ten nowy krok można było wstawić do sekwencji. Jeśli nie zostanie określony, step_id zostanie automatycznie przypisany do ostatniego w sekwencji kroków. step_id jest int.

[ ** \@step_name =** ] step_name  
Nazwa kroku. Należy określić, z wyjątkiem pierwszego kroku zadania, które (dla wygody) ma domyślną nazwę "JobStep". step_name jest nvarchar(128).

[ ** \@command_type =** ] "command_type"  
Typ polecenia wykonywanego przez ten krok zadania. command_type jest nvarchar(50), z domyślną wartością TSql, co @command_type oznacza, że wartość parametru jest skryptem T-SQL.

Jeśli jest określony, wartość musi być TSql.

[ ** \@command_source =** ] "command_source"  
Typ lokalizacji, w której jest przechowywane polecenie. command_source jest nvarchar(50), z domyślną wartością Inline, co @command_source oznacza, że wartość parametru jest tekstem dosłownym polecenia.

Jeśli określono, wartość musi być wbudowana.

[ ** \@polecenie =** ] 'polecenie'  
Polecenie musi być prawidłowy skrypt T-SQL, a następnie wykonywane przez ten krok zadania. jest nvarchar(max), z wartością null.

[ ** \@credential_name =** ] "credential_name"  
Nazwa poświadczeń o zakresie bazy danych przechowywanych w tej bazie danych kontroli zadania, która jest używana do łączenia się z każdą z docelowych baz danych w grupie docelowej podczas wykonywania tego kroku. credential_name jest nvarchar(128).

[ ** \@target_group_name =** ] "group_name docelowa"  
Nazwa grupy docelowej, która zawiera docelowe bazy danych, na których zostanie wykonany krok zadania. target_group_name jest nvarchar(128).

[ ** \@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Opóźnienie przed pierwszą próbą ponowienia próby, jeśli krok zadania nie powiedzie się przy próbie wykonania początkowego. initial_retry_interval_seconds jest int, z domyślną wartością 1.

[ ** \@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maksymalne opóźnienie między próbami ponawiania. Jeśli opóźnienie między ponownych prób wzrośnie większy niż ta wartość, jest ograniczona do tej wartości zamiast tego. maximum_retry_interval_seconds jest int, z domyślną wartością 120.

[ ** \@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Mnożnik do zastosowania do opóźnienia ponawiania próby, jeśli wiele prób wykonania kroku zadania zakończy się niepowodzeniem. Na przykład jeśli pierwsza ponowna próbę miała opóźnienie 5 sekund, a mnożnik wycofywania wynosi 2,0, druga ponowna próby będzie miała opóźnienie 10 sekund, a trzecia ponowna próbę będzie miała opóźnienie 20 sekund. retry_interval_backoff_multiplier jest rzeczywista, z domyślną wartością 2.0.

[ ** \@retry_attempts =** ] retry_attempts  
Liczba ponownych ponów wykonania, jeśli początkowa próba nie powiedzie się. Na przykład jeśli wartość retry_attempts wynosi 10, będzie 1 próba początkowa i 10 ponownych prób, co daje łącznie 11 prób. Jeśli ostateczna próba ponowienia nie powiedzie się, wykonanie zadania zakończy się z cyklem życia niepowodzenie. retry_attempts jest int, z domyślną wartością 10.

[ ** \@step_timeout_seconds =** ] step_timeout_seconds  
Maksymalny czas wykonania kroku. Jeśli ten czas zostanie przekroczony, wykonanie zadania zakończy się z cyklem życia TimedOut. step_timeout_seconds jest int, z domyślną wartością 43 200 sekund (12 godzin).

[ ** \@output_type =** ] "output_type"  
Jeśli nie null, typ miejsca docelowego, do których jest zapisywany pierwszy zestaw wyników polecenia. output_type jest nvarchar(50), z wartością null.

Jeśli określono, wartość musi być SqlDatabase.

[ ** \@output_credential_name =** ] "output_credential_name"  
Jeśli nie wartość null, nazwa poświadczenia o zakresie bazy danych, który jest używany do łączenia się z wyjściową docelową bazą danych. Należy określić, jeśli output_type jest równa SqlDatabase. output_credential_name jest nvarchar(128), z domyślną wartością NULL.

[ ** \@output_subscription_id =** ] "output_subscription_id"  
Potrzebuje opisu.

[ ** \@output_resource_group_name =** ] "output_resource_group_name"  
Potrzebuje opisu.

[ ** \@output_server_name =** ] "output_server_name"  
Jeśli nie wartość null, w pełni kwalifikowana nazwa DNS serwera zawierającego wyjściową docelową bazę danych. Należy określić, jeśli output_type jest równa SqlDatabase. output_server_name jest nvarchar(256), z wartością null.

[ ** \@output_database_name =** ] "output_database_name"  
Jeśli nie null, nazwa bazy danych, która zawiera tabelę docelową danych wyjściowych. Należy określić, jeśli output_type jest równa SqlDatabase. output_database_name jest nvarchar(128), z wartością null.

[ ** \@output_schema_name =** ] "output_schema_name"  
Jeśli nie wartość null, nazwa schematu SQL, który zawiera tabelę docelową danych wyjściowych. Jeśli output_type jest równa SqlDatabase, wartość domyślna jest dbo. output_schema_name jest nvarchar(128).

[ ** \@output_table_name =** ] "output_table_name"  
Jeśli nie null, nazwa tabeli, do których zostanie zapisany pierwszy zestaw wyników polecenia. Jeśli tabela jeszcze nie istnieje, zostanie utworzona na podstawie schematu zwracanego zestawu wyników. Należy określić, jeśli output_type jest równa SqlDatabase. output_table_name jest nvarchar(128), z domyślną wartością NULL.

[ ** \@job_version =** ] job_version WYJŚCIE  
Parametr wyjściowy, który zostanie przypisany nowy numer wersji zadania. job_version jest int.

[ ** \@max_parallelism =** ] max_parallelism WYJŚCIE  
Maksymalny poziom równoległości na elastyczną pulę. Jeśli ustawiona, krok zadania będzie ograniczony tylko do uruchamiania na maksymalnie tyle baz danych na pulę elastyczną. Dotyczy to każdej puli elastycznej, która jest bezpośrednio uwzględniona w grupie docelowej lub znajduje się wewnątrz serwera, który znajduje się w grupie docelowej. max_parallelism jest int.


#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (sukces) lub 1 (porażka)

#### <a name="remarks"></a>Uwagi
Gdy sp_add_jobstep powiedzie się, bieżący numer wersji zadania jest zwiększany. Przy następnym wykonaniu zadania zostanie użyta nowa wersja. Jeśli zadanie jest obecnie wykonywane, to wykonanie nie będzie zawierać nowy krok.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie roli serwera stałego sysadmin mogą wykonać tę procedurę składowaną. Ograniczają one użytkownika do po prostu być w stanie monitorować zadania, można przyznać użytkownikowi, aby być częścią następującej roli bazy danych w bazie danych agenta zadań określony podczas tworzenia agenta zadania:  

- jobs_reader

Aby uzyskać szczegółowe informacje na temat uprawnień tych ról, zobacz permission sekcji w tym dokumencie. Tylko członkowie sysadmin można użyć tej procedury składowanej do edycji atrybutów zadań, które są własnością innych użytkowników.



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
[ ** \@job_name =** ] "job_name"  
Nazwa zadania, do którego należy krok. job_name jest nvarchar(128).

[ ** \@step_id =** ] step_id  
Numer identyfikacyjny kroku zadania, który ma zostać zmodyfikowany. Należy określić step_id lub step_name. step_id jest int.

[ ** \@step_name =** ] "step_name"  
Nazwa kroku, który ma zostać zmodyfikowany. Należy określić step_id lub step_name. step_name jest nvarchar(128).

[ ** \@new_id =** ] new_id  
Nowy numer identyfikacyjny sekwencji dla kroku zadania. Numery identyfikacyjne kroków zaczynają się od 1 i przyrostowe bez przerw. Jeśli krok zostanie ponownie zamówiony, inne kroki zostaną automatycznie ponumerowane.

[ ** \@new_name =** ] "new_name"  
Nowa nazwa kroku. new_name jest nvarchar(128).

[ ** \@command_type =** ] "command_type"  
Typ polecenia wykonywanego przez ten krok zadania. command_type jest nvarchar(50), z domyślną wartością TSql, co @command_type oznacza, że wartość parametru jest skryptem T-SQL.

Jeśli jest określony, wartość musi być TSql.

[ ** \@command_source =** ] "command_source"  
Typ lokalizacji, w której jest przechowywane polecenie. command_source jest nvarchar(50), z domyślną wartością Inline, co @command_source oznacza, że wartość parametru jest tekstem dosłownym polecenia.

Jeśli określono, wartość musi być wbudowana.

[ ** \@polecenie =** ] 'polecenie'  
Polecenia muszą być prawidłowym skryptem T-SQL, a następnie wykonywane przez ten krok zadania. jest nvarchar(max), z wartością null.

[ ** \@credential_name =** ] "credential_name"  
Nazwa poświadczeń o zakresie bazy danych przechowywanych w tej bazie danych kontroli zadania, która jest używana do łączenia się z każdą z docelowych baz danych w grupie docelowej podczas wykonywania tego kroku. credential_name jest nvarchar(128).

[ ** \@target_group_name =** ] "group_name docelowa"  
Nazwa grupy docelowej, która zawiera docelowe bazy danych, na których zostanie wykonany krok zadania. target_group_name jest nvarchar(128).

[ ** \@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Opóźnienie przed pierwszą próbą ponowienia próby, jeśli krok zadania nie powiedzie się przy próbie wykonania początkowego. initial_retry_interval_seconds jest int, z domyślną wartością 1.

[ ** \@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Maksymalne opóźnienie między próbami ponawiania. Jeśli opóźnienie między ponownych prób wzrośnie większy niż ta wartość, jest ograniczona do tej wartości zamiast tego. maximum_retry_interval_seconds jest int, z domyślną wartością 120.

[ ** \@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Mnożnik do zastosowania do opóźnienia ponawiania próby, jeśli wiele prób wykonania kroku zadania zakończy się niepowodzeniem. Na przykład jeśli pierwsza ponowna próbę miała opóźnienie 5 sekund, a mnożnik wycofywania wynosi 2,0, druga ponowna próby będzie miała opóźnienie 10 sekund, a trzecia ponowna próbę będzie miała opóźnienie 20 sekund. retry_interval_backoff_multiplier jest rzeczywista, z domyślną wartością 2.0.

[ ** \@retry_attempts =** ] retry_attempts  
Liczba ponownych ponów wykonania, jeśli początkowa próba nie powiedzie się. Na przykład jeśli wartość retry_attempts wynosi 10, będzie 1 próba początkowa i 10 ponownych prób, co daje łącznie 11 prób. Jeśli ostateczna próba ponowienia nie powiedzie się, wykonanie zadania zakończy się z cyklem życia niepowodzenie. retry_attempts jest int, z domyślną wartością 10.

[ ** \@step_timeout_seconds =** ] step_timeout_seconds  
Maksymalny czas wykonania kroku. Jeśli ten czas zostanie przekroczony, wykonanie zadania zakończy się z cyklem życia TimedOut. step_timeout_seconds jest int, z domyślną wartością 43 200 sekund (12 godzin).

[ ** \@output_type =** ] "output_type"  
Jeśli nie null, typ miejsca docelowego, do których jest zapisywany pierwszy zestaw wyników polecenia. Aby zresetować wartość output_type z powrotem do wartości NULL, ustaw wartość tego parametru na '' (pusty ciąg). output_type jest nvarchar(50), z wartością null.

Jeśli określono, wartość musi być SqlDatabase.

[ ** \@output_credential_name =** ] "output_credential_name"  
Jeśli nie wartość null, nazwa poświadczenia o zakresie bazy danych, który jest używany do łączenia się z wyjściową docelową bazą danych. Należy określić, jeśli output_type jest równa SqlDatabase. Aby zresetować wartość output_credential_name z powrotem do wartości NULL, ustaw wartość tego parametru na '' (pusty ciąg). output_credential_name jest nvarchar(128), z domyślną wartością NULL.

[ ** \@output_server_name =** ] "output_server_name"  
Jeśli nie wartość null, w pełni kwalifikowana nazwa DNS serwera zawierającego wyjściową docelową bazę danych. Należy określić, jeśli output_type jest równa SqlDatabase. Aby zresetować wartość output_server_name z powrotem do wartości NULL, ustaw wartość tego parametru na '' (pusty ciąg). output_server_name jest nvarchar(256), z wartością null.

[ ** \@output_database_name =** ] "output_database_name"  
Jeśli nie null, nazwa bazy danych, która zawiera tabelę docelową danych wyjściowych. Należy określić, jeśli output_type jest równa SqlDatabase. Aby zresetować wartość output_database_name z powrotem do wartości NULL, ustaw wartość tego parametru na '' (pusty ciąg). output_database_name jest nvarchar(128), z wartością null.

[ ** \@output_schema_name =** ] "output_schema_name"  
Jeśli nie wartość null, nazwa schematu SQL, który zawiera tabelę docelową danych wyjściowych. Jeśli output_type jest równa SqlDatabase, wartość domyślna jest dbo. Aby zresetować wartość output_schema_name z powrotem do wartości NULL, ustaw wartość tego parametru na '' (pusty ciąg). output_schema_name jest nvarchar(128).

[ ** \@output_table_name =** ] "output_table_name"  
Jeśli nie null, nazwa tabeli, do których zostanie zapisany pierwszy zestaw wyników polecenia. Jeśli tabela jeszcze nie istnieje, zostanie utworzona na podstawie schematu zwracanego zestawu wyników. Należy określić, jeśli output_type jest równa SqlDatabase. Aby zresetować wartość output_server_name z powrotem do wartości NULL, ustaw wartość tego parametru na '' (pusty ciąg). output_table_name jest nvarchar(128), z domyślną wartością NULL.

[ ** \@job_version =** ] job_version WYJŚCIE  
Parametr wyjściowy, który zostanie przypisany nowy numer wersji zadania. job_version jest int.

[ ** \@max_parallelism =** ] max_parallelism WYJŚCIE  
Maksymalny poziom równoległości na elastyczną pulę. Jeśli ustawiona, krok zadania będzie ograniczony tylko do uruchamiania na maksymalnie tyle baz danych na pulę elastyczną. Dotyczy to każdej puli elastycznej, która jest bezpośrednio uwzględniona w grupie docelowej lub znajduje się wewnątrz serwera, który znajduje się w grupie docelowej. Aby zresetować wartość max_parallelism z powrotem do wartości null, należy ustawić wartość tego parametru na -1. max_parallelism jest int.


#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (sukces) lub 1 (porażka)

#### <a name="remarks"></a>Uwagi
Nie będzie to miało wpływu na wszelkie wykonania zadania w toku. Gdy sp_update_jobstep powiedzie się, numer wersji zadania jest zwiększany. Przy następnym wykonaniu zadania zostanie użyta nowa wersja.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie roli serwera stałego sysadmin mogą wykonać tę procedurę składowaną. Ograniczają one użytkownika do po prostu być w stanie monitorować zadania, można przyznać użytkownikowi, aby być częścią następującej roli bazy danych w bazie danych agenta zadań określony podczas tworzenia agenta zadania:

- jobs_reader

Aby uzyskać szczegółowe informacje na temat uprawnień tych ról, zobacz permission sekcji w tym dokumencie. Tylko członkowie sysadmin mogą używać tej procedury składowanej do edytowania atrybutów zadań należących do innych użytkowników




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
[ ** \@job_name =** ] "job_name"  
Nazwa zadania, z którego zostanie usunięty krok. job_name jest nvarchar(128), bez wartości domyślnej.

[ ** \@step_id =** ] step_id  
Numer identyfikacyjny kroku zadania do usunięcia. Należy określić step_id lub step_name. step_id jest int.

[ ** \@step_name =** ] "step_name"  
Nazwa kroku do usunięcia. Należy określić step_id lub step_name. step_name jest nvarchar(128).

[ ** \@job_version =** ] job_version WYJŚCIE  
Parametr wyjściowy, który zostanie przypisany nowy numer wersji zadania. job_version jest int.

#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (sukces) lub 1 (porażka)

#### <a name="remarks"></a>Uwagi
Nie będzie to miało wpływu na wszelkie wykonania zadania w toku. Gdy sp_update_jobstep powiedzie się, numer wersji zadania jest zwiększany. Przy następnym wykonaniu zadania zostanie użyta nowa wersja.

Pozostałe kroki zadania zostaną automatycznie ponumerowane, aby wypełnić lukę pozostawioną przez usunięty krok zadania.
 
#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie roli serwera stałego sysadmin mogą wykonać tę procedurę składowaną. Ograniczają one użytkownika do po prostu być w stanie monitorować zadania, można przyznać użytkownikowi, aby być częścią następującej roli bazy danych w bazie danych agenta zadań określony podczas tworzenia agenta zadania:
- jobs_reader

Aby uzyskać szczegółowe informacje na temat uprawnień tych ról, zobacz permission sekcji w tym dokumencie. Tylko członkowie sysadmin można użyć tej procedury składowanej do edycji atrybutów zadań, które są własnością innych użytkowników.






### <a name="sp_start_job"></a><a name="sp_start_job"></a>Sp_start_job

Rozpoczyna wykonywanie zadania.

#### <a name="syntax"></a>Składnia


```syntaxsql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>Argumenty
[ ** \@job_name =** ] "job_name"  
Nazwa zadania, z którego zostanie usunięty krok. job_name jest nvarchar(128), bez wartości domyślnej.

[ ** \@job_execution_id =** ] job_execution_id WYJŚCIE  
Parametr wyjściowy, który zostanie przypisany identyfikator wykonania zadania. job_version jest unikalnyidentyfikator.

#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (sukces) lub 1 (porażka)

#### <a name="remarks"></a>Uwagi
Brak.
 
#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie roli serwera stałego sysadmin mogą wykonać tę procedurę składowaną. Ograniczają one użytkownika do po prostu być w stanie monitorować zadania, można przyznać użytkownikowi, aby być częścią następującej roli bazy danych w bazie danych agenta zadań określony podczas tworzenia agenta zadania:
- jobs_reader

Aby uzyskać szczegółowe informacje na temat uprawnień tych ról, zobacz permission sekcji w tym dokumencie. Tylko członkowie sysadmin można użyć tej procedury składowanej do edycji atrybutów zadań, które są własnością innych użytkowników.

### <a name="sp_stop_job"></a><a name="sp_stop_job"></a>Sp_stop_job

Zatrzymuje wykonywanie zadania.

#### <a name="syntax"></a>Składnia


```syntaxsql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>Argumenty
[ ** \@job_execution_id =** ] job_execution_id  
Numer identyfikacyjny wykonania zadania do zatrzymania. job_execution_id jest unikatowyidentyfikator, z domyślną wartością NULL.

#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (sukces) lub 1 (porażka)

#### <a name="remarks"></a>Uwagi
Brak.
 
#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie roli serwera stałego sysadmin mogą wykonać tę procedurę składowaną. Ograniczają one użytkownika do po prostu być w stanie monitorować zadania, można przyznać użytkownikowi, aby być częścią następującej roli bazy danych w bazie danych agenta zadań określony podczas tworzenia agenta zadania:
- jobs_reader

Aby uzyskać szczegółowe informacje na temat uprawnień tych ról, zobacz permission sekcji w tym dokumencie. Tylko członkowie sysadmin można użyć tej procedury składowanej do edycji atrybutów zadań, które są własnością innych użytkowników.


### <a name="sp_add_target_group"></a><a name="sp_add_target_group"></a>sp_add_target_group

Dodaje grupę docelową.

#### <a name="syntax"></a>Składnia


```syntaxsql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>Argumenty
[ ** \@target_group_name =** ] "target_group_name"  
Nazwa grupy docelowej do utworzenia. target_group_name jest nvarchar(128), bez wartości domyślnej.

[ ** \@target_group_id =** ] target_group_id DANE WYJŚCIOWE Numer identyfikacyjny grupy docelowej przypisany do zadania, jeśli został pomyślnie utworzony. target_group_id jest zmienną wyjściową typu uniqueidentifier, z wartością domyślną NULL.

#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (sukces) lub 1 (porażka)

#### <a name="remarks"></a>Uwagi
Grupy docelowe zapewniają łatwy sposób kierowania zadania w kolekcji baz danych.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie roli serwera stałego sysadmin mogą wykonać tę procedurę składowaną. Ograniczają one użytkownika do po prostu być w stanie monitorować zadania, można przyznać użytkownikowi, aby być częścią następującej roli bazy danych w bazie danych agenta zadań określony podczas tworzenia agenta zadania:
- jobs_reader

Aby uzyskać szczegółowe informacje na temat uprawnień tych ról, zobacz permission sekcji w tym dokumencie. Tylko członkowie sysadmin można użyć tej procedury składowanej do edycji atrybutów zadań, które są własnością innych użytkowników.

### <a name="sp_delete_target_group"></a><a name="sp_delete_target_group"></a>sp_delete_target_group

Usuwa grupę docelową.

#### <a name="syntax"></a>Składnia


```syntaxsql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>Argumenty
[ ** \@target_group_name =** ] "target_group_name"  
Nazwa grupy docelowej do usunięcia. target_group_name jest nvarchar(128), bez wartości domyślnej.

#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (sukces) lub 1 (porażka)

#### <a name="remarks"></a>Uwagi
Brak.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie roli serwera stałego sysadmin mogą wykonać tę procedurę składowaną. Ograniczają one użytkownika do po prostu być w stanie monitorować zadania, można przyznać użytkownikowi, aby być częścią następującej roli bazy danych w bazie danych agenta zadań określony podczas tworzenia agenta zadania:
- jobs_reader

Aby uzyskać szczegółowe informacje na temat uprawnień tych ról, zobacz permission sekcji w tym dokumencie. Tylko członkowie sysadmin można użyć tej procedury składowanej do edycji atrybutów zadań, które są własnością innych użytkowników.

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
[ ** \@target_group_name =** ] "target_group_name"  
Nazwa grupy docelowej, do której zostanie dodany element członkowski. target_group_name jest nvarchar(128), bez wartości domyślnej.

[ ** \@membership_type =** ] "membership_type"  
Określa, czy członek grupy docelowej zostanie uwzględniony lub wykluczony. target_group_name jest nvarchar(128), z domyślnym "Include". Prawidłowe wartości dla target_group_name to "Uwzględnij" lub "Wyklucz".

[ ** \@target_type =** ] "target_type"  
Typ docelowej bazy danych lub kolekcji baz danych, w tym wszystkie bazy danych na serwerze, wszystkie bazy danych w puli elastycznej, wszystkie bazy danych na mapie niezależnego fragmentu lub pojedynczej bazy danych. target_type jest nvarchar(128), bez wartości domyślnej. Prawidłowe wartości dla target_type to "SqlServer", "SqlElasticPool", "SqlDatabase" lub "SqlShardMap". 

[ ** \@refresh_credential_name =** ] "refresh_credential_name"  
Nazwa serwera bazy danych SQL. refresh_credential_name jest nvarchar(128), bez wartości domyślnej.

[ ** \@server_name =** ] "server_name"  
Nazwa serwera bazy danych SQL, który powinien zostać dodany do określonej grupy docelowej. server_name należy określić, gdy target_type jest "SqlServer". server_name jest nvarchar(128), bez wartości domyślnej.

[ ** \@database_name =** ] "database_name"  
Nazwa bazy danych, która powinna zostać dodana do określonej grupy docelowej. database_name należy określić, gdy target_type jest "SqlDatabase". database_name jest nvarchar(128), bez wartości domyślnej.

[ ** \@elastic_pool_name =** ] "elastic_pool_name"  
Nazwa puli elastycznej, która powinna zostać dodana do określonej grupy docelowej. elastic_pool_name należy określić, gdy target_type jest "SqlElasticPool". elastic_pool_name jest nvarchar(128), bez wartości domyślnej.

[ ** \@shard_map_name =** ] "shard_map_name"  
Nazwa puli map niezależnego fragmentu, która powinna zostać dodana do określonej grupy docelowej. elastic_pool_name należy określić, gdy target_type jest "SqlSqlShardMap". shard_map_name jest nvarchar(128), bez wartości domyślnej.

[ ** \@target_id =** ] target_group_id WYJŚCIE  
Docelowy numer identyfikacyjny przypisany do członka grupy docelowej, jeśli został utworzony, został dodany do grupy docelowej. target_id jest zmienną wyjściową typu uniqueidentifier, z wartością domyślną NULL.
Wartości kodu zwrotu 0 (sukces) lub 1 (niepowodzenie)

#### <a name="remarks"></a>Uwagi
Zadanie jest wykonywane we wszystkich pojedynczych bazach danych w ramach serwera bazy danych SQL lub w puli elastycznej w czasie wykonywania, gdy serwer bazy danych SQL lub pula elastyczna znajduje się w grupie docelowej.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie roli serwera stałego sysadmin mogą wykonać tę procedurę składowaną. Ograniczają one użytkownika do po prostu być w stanie monitorować zadania, można przyznać użytkownikowi, aby być częścią następującej roli bazy danych w bazie danych agenta zadań określony podczas tworzenia agenta zadania:
- jobs_reader

Aby uzyskać szczegółowe informacje na temat uprawnień tych ról, zobacz permission sekcji w tym dokumencie. Tylko członkowie sysadmin można użyć tej procedury składowanej do edycji atrybutów zadań, które są własnością innych użytkowników.

#### <a name="examples"></a>Przykłady
Poniższy przykład dodaje wszystkie bazy danych na serwerach Londyn i NewYork do grupy Serwery obsługi informacji o klientach. Należy połączyć się z bazą danych zadań określoną podczas tworzenia agenta zadania, w tym przypadku ElasticJobs.

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

Usuwa członka grupy docelowej z grupy docelowej.

#### <a name="syntax"></a>Składnia


```syntaxsql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] 'target_id']
```



Argumenty @target_group_name [ = ] 'target_group_name'  
Nazwa grupy docelowej, z której ma usunąć członka grupy docelowej. target_group_name jest nvarchar(128), bez wartości domyślnej.

[ @target_id = ] target_id  
 Docelowy numer identyfikacyjny przypisany do członka grupy docelowej do usunięcia. target_id jest unikatowymidentyfikatorem, z wartością null.

#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (sukces) lub 1 (porażka)

#### <a name="remarks"></a>Uwagi
Grupy docelowe zapewniają łatwy sposób kierowania zadania w kolekcji baz danych.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie roli serwera stałego sysadmin mogą wykonać tę procedurę składowaną. Ograniczają one użytkownika do po prostu być w stanie monitorować zadania, można przyznać użytkownikowi, aby być częścią następującej roli bazy danych w bazie danych agenta zadań określony podczas tworzenia agenta zadania:
- jobs_reader

Aby uzyskać szczegółowe informacje na temat uprawnień tych ról, zobacz permission sekcji w tym dokumencie. Tylko członkowie sysadmin można użyć tej procedury składowanej do edycji atrybutów zadań, które są własnością innych użytkowników.

#### <a name="examples"></a>Przykłady
Poniższy przykład usuwa serwer w Londynie z grupy Serwery obsługi informacji o klientach. Należy połączyć się z bazą danych zadań określoną podczas tworzenia agenta zadania, w tym przypadku ElasticJobs.

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

### <a name="sp_purge_jobhistory"></a><a name="sp_purge_jobhistory"></a>Sp_purge_jobhistory

Usuwa rekordy historii dla zadania.

#### <a name="syntax"></a>Składnia


```syntaxsql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argumenty
[ ** \@job_name =** ] "job_name"  
Nazwa zadania, dla którego ma być usuwana historia rekordów. job_name jest nvarchar(128), z wartością null. Należy określić job_id lub job_name, ale nie można określić obu tych rozwiązań.

[ ** \@job_id =** ] job_id  
 Numer identyfikacyjny zadania zadania dla rekordów do usunięcia. job_id jest unikatowyidentyfikator, z wartością null. Należy określić job_id lub job_name, ale nie można określić obu tych rozwiązań.

[ ** \@oldest_date =** ] oldest_date  
 Najstarszy rekord do zachowania w historii. oldest_date jest DATETIME2, z wartością null. Po określeniu oldest_date sp_purge_jobhistory usuwa tylko rekordy starsze niż określona wartość.

#### <a name="return-code-values"></a>Zwracane wartości kodu
0 (sukces) lub 1 (niepowodzenie) Uwagi Grupy docelowe zapewniają łatwy sposób kierowania zadania w kolekcji baz danych.

#### <a name="permissions"></a>Uprawnienia
Domyślnie członkowie roli serwera stałego sysadmin mogą wykonać tę procedurę składowaną. Ograniczają one użytkownika do po prostu być w stanie monitorować zadania, można przyznać użytkownikowi, aby być częścią następującej roli bazy danych w bazie danych agenta zadań określony podczas tworzenia agenta zadania:
- jobs_reader

Aby uzyskać szczegółowe informacje na temat uprawnień tych ról, zobacz permission sekcji w tym dokumencie. Tylko członkowie sysadmin można użyć tej procedury składowanej do edycji atrybutów zadań, które są własnością innych użytkowników.

#### <a name="examples"></a>Przykłady
Poniższy przykład dodaje wszystkie bazy danych na serwerach Londyn i NewYork do grupy Serwery obsługi informacji o klientach. Należy połączyć się z bazą danych zadań określoną podczas tworzenia agenta zadania, w tym przypadku ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>Widoki zadań

Następujące widoki są dostępne w [bazie danych zadań](sql-database-job-automation-overview.md#job-database).


|Widok  |Opis  |
|---------|---------|
|[job_executions](#job_executions-view)     |  Pokazuje historię wykonywania zadań.      |
|[Zadania](#jobs-view)     |   Pokazuje wszystkie zadania.      |
|[job_versions](#job_versions-view)     |   Pokazuje wszystkie wersje zadań.      |
|[Jobsteps](#jobsteps-view)     |     Pokazuje wszystkie kroki w bieżącej wersji każdego zadania.    |
|[jobstep_versions](#jobstep_versions-view)     |     Pokazuje wszystkie kroki we wszystkich wersjach każdego zadania.    |
|[target_groups](#target_groups-view)     |      Pokazuje wszystkie grupy docelowe.   |
|[target_group_members](#target_group_members-view)     |   Pokazuje wszystkich członków wszystkich grup docelowych.      |


### <a name="job_executions-view"></a><a name="job_executions-view"></a>Widok job_executions

[praca]. [job_executions]

Pokazuje historię wykonywania zadań.


|Nazwa kolumny|   Typ danych   |Opis|
|---------|---------|---------|
|**job_execution_id**   |uniqueidentifier|  Unikatowy identyfikator wystąpienia wykonania zadania.
|**Job_name**   |nvarchar(128)  |Nazwa zadania.
|**Job_id** |uniqueidentifier|  Unikatowy identyfikator zadania.
|**job_version**    |int    |Wersja zadania (automatycznie aktualizowana za każdym razem, gdy zadanie jest modyfikowane).
|**step_id**    |int|   Unikatowy identyfikator (dla tego zadania) dla kroku. NULL wskazuje, że jest to zadanie nadrzędne.
|**is_active**| bit |Wskazuje, czy informacje są aktywne, czy nieaktywne. 1 oznacza aktywne zadania, a 0 oznacza nieaktywne.
|**Cyklem życia**| nvarchar(50)|Wartość wskazująca stan zadania: "Utworzony", "W toku", "Nie powiodło się", "Powiódł się", "Pominięty", "Zastąpiony z paskiem"|
|**create_time**|   datetime2(7)|   Data i godzina utworzenia zadania.
|**Start_time** |datetime2(7)|  Data i godzina rozpoczęcia wykonywania zadania. NULL, jeśli zadanie nie zostało jeszcze wykonane.
|**End_time**|  datetime2(7)    |Data i godzina wykonania zadania. NULL, jeśli zadanie nie zostało jeszcze wykonane lub nie zostało jeszcze ukończone wykonanie.
|**current_attempts**   |int    |Ile razy krok został ponowiony. Zadanie nadrzędne będzie 0, wykonanie zadań podrzędnych będzie 1 lub więcej na podstawie zasad wykonywania.
|**current_attempt_start_time** |datetime2(7)|  Data i godzina rozpoczęcia wykonywania zadania. NULL wskazuje, że jest to zadanie nadrzędne.
|**last_message**   |Nvarchar(max)| Komunikat historii zadania lub kroku. 
|**target_type**|   nvarchar(128)   |Typ docelowej bazy danych lub kolekcji baz danych, w tym wszystkie bazy danych na serwerze, wszystkie bazy danych w puli elastycznej lub bazy danych. Prawidłowe wartości dla target_type to "SqlServer", "SqlElasticPool" lub "SqlDatabase". NULL wskazuje, że jest to zadanie nadrzędne.
|**target_id**  |uniqueidentifier|  Unikatowy identyfikator członka grupy docelowej.  NULL wskazuje, że jest to zadanie nadrzędne.
|**target_group_name**  |nvarchar(128)  |Nazwa grupy docelowej. NULL wskazuje, że jest to zadanie nadrzędne.
|**target_server_name**|    nvarchar(256)|  Nazwa serwera bazy danych SQL zawartej w grupie docelowej. Określono tylko wtedy, gdy target_type jest "SqlServer". NULL wskazuje, że jest to zadanie nadrzędne.
|**target_database_name**   |nvarchar(128)| Nazwa bazy danych zawartej w grupie docelowej. Określony tylko wtedy, gdy target_type jest "SqlDatabase". NULL wskazuje, że jest to zadanie nadrzędne.


### <a name="jobs-view"></a>widok zadań

[praca]. [praca]

Pokazuje wszystkie zadania.

|Nazwa kolumny|   Typ danych|  Opis|
|------|------|-------|
|**Job_name**|  nvarchar(128)   |Nazwa zadania.|
|**Job_id**|    uniqueidentifier    |Unikatowy identyfikator zadania.|
|**job_version**    |int    |Wersja zadania (automatycznie aktualizowana za każdym razem, gdy zadanie jest modyfikowane).|
|**Opis**    |nvarchar(512)| Opis zadania. bit z włączoną włączona Wskazuje, czy zadanie jest włączone, czy wyłączone. 1 wskazuje włączone zadania, a 0 oznacza zadania niepełnosprawne.|
|**schedule_interval_type** |nvarchar(50)   |Wartość wskazująca, kiedy zadanie ma zostać wykonane:'Once', 'Minutes', 'Hours', 'Days', 'Weeks', 'Months'
|**schedule_interval_count**|   int|    Liczba schedule_interval_type okresów, które mają wystąpić między każdym wykonaniem zadania.|
|**schedule_start_time**    |datetime2(7)|  Data i godzina ostatniego rozpoczęcia wykonywania zadania.|
|**schedule_end_time**| datetime2(7)|   Data i godzina ostatniego wykonania zadania.|


### <a name="job_versions-view"></a><a name="job_versions-view"></a>widok job_versions

[praca]. [job_versions]

Pokazuje wszystkie wersje zadań.

|Nazwa kolumny|   Typ danych|  Opis|
|------|------|-------|
|**Job_name**|  nvarchar(128)   |Nazwa zadania.|
|**Job_id**|    uniqueidentifier    |Unikatowy identyfikator zadania.|
|**job_version**    |int    |Wersja zadania (automatycznie aktualizowana za każdym razem, gdy zadanie jest modyfikowane).|


### <a name="jobsteps-view"></a>widok jobsteps

[praca]. [jobsteps]

Pokazuje wszystkie kroki w bieżącej wersji każdego zadania.

|Nazwa kolumny    |Typ danych| Opis|
|------|------|-------|
|**Job_name**   |nvarchar(128)| Nazwa zadania.|
|**Job_id** |uniqueidentifier   |Unikatowy identyfikator zadania.|
|**job_version**|   int|    Wersja zadania (automatycznie aktualizowana za każdym razem, gdy zadanie jest modyfikowane).|
|**step_id**    |int    |Unikatowy identyfikator (dla tego zadania) dla kroku.|
|**step_name**  |nvarchar(128)  |Unikatowa (dla tego zadania) nazwa kroku.|
|**command_type**   |nvarchar(50)   |Typ polecenia do wykonania w kroku zadania. W przypadku wersji 1 wartość musi być równa wartości "TSql" i wartość domyślna.|
|**command_source** |nvarchar(50)|  Lokalizacja polecenia. W przypadku wersji 1 wartość "Inline" jest wartością domyślną i akceptowaną tylko jako akceptowaną.|
|**Polecenia**|   Nvarchar(max)|  Polecenia, które mają być wykonywane przez zadania elastyczne za pośrednictwem command_type.|
|**credential_name**|   nvarchar(128)   |Nazwa poświadczenia o zakresie bazy danych używanego do wykonania zadania.|
|**target_group_name**| nvarchar(128)   |Nazwa grupy docelowej.|
|**target_group_id**|   uniqueidentifier|   Unikatowy identyfikator grupy docelowej.|
|**initial_retry_interval_seconds**|    int |Opóźnienie przed pierwszą próbą ponowienia próby. Wartość domyślna to 1.|
|**maximum_retry_interval_seconds** |int|   Maksymalne opóźnienie między próbami ponawiania. Jeśli opóźnienie między ponownych prób wzrośnie większy niż ta wartość, jest ograniczona do tej wartości zamiast tego. Wartość domyślna to 120.|
|**retry_interval_backoff_multiplier**  |rzeczywiste|  Mnożnik do zastosowania do opóźnienia ponawiania próby, jeśli wiele prób wykonania kroku zadania zakończy się niepowodzeniem. Wartość domyślna to 2.0.|
|**retry_attempts** |int|   Liczba ponownych prób użycia, jeśli ten krok nie powiedzie się. Domyślnie 10, co oznacza, że nie ma prób ponowienia próby.|
|**step_timeout_seconds**   |int|   Czas w minutach między próbami ponawiania. Wartość domyślna to 0, która wskazuje interwał 0-minutowy.|
|**output_type**    |nvarchar(11)|  Lokalizacja polecenia. W bieżącej wersji zapoznawczej wartość "Inline" jest wartością domyślną i akceptowaną tylko jako akceptowaną.|
|**output_credential_name**|    nvarchar(128)   |Nazwa poświadczeń, które mają być używane do łączenia się z serwerem docelowym do przechowywania zestawu wyników.|
|**output_subscription_id**|    uniqueidentifier|   Unikatowy identyfikator subskrypcji serwera docelowego\bazy danych dla wyników ustawionych na podstawie wykonania kwerendy.|
|**output_resource_group_name** |nvarchar(128)| Nazwa grupy zasobów, w której znajduje się serwer docelowy.|
|**output_server_name**|    nvarchar(256)   |Nazwa serwera docelowego dla zestawu wyników.|
|**output_database_name**   |nvarchar(128)| Nazwa docelowej bazy danych dla zestawu wyników.|
|**output_schema_name** |Nvarchar(max)| Nazwa schematu docelowego. Domyślnie dbo, jeśli nie określono.|
|**output_table_name**| Nvarchar(max)|  Nazwa tabeli do przechowywania wyników zestawu z wyników kwerendy. Tabela zostanie utworzona automatycznie na podstawie schematu zestawu wyników, jeśli jeszcze nie istnieje. Schemat musi być zgodny ze schematem zestawu wyników.|
|**max_parallelism**|   int|    Maksymalna liczba baz danych na pulę elastyczną, na której zostanie uruchomiony krok zadania w czasie. Wartość domyślna to NULL, co oznacza brak limitu. |


### <a name="jobstep_versions-view"></a><a name="jobstep_versions-view"></a>Widok jobstep_versions

[praca]. [jobstep_versions]

Pokazuje wszystkie kroki we wszystkich wersjach każdego zadania. Schemat jest identyczny z [jobsteps](#jobsteps-view).

### <a name="target_groups-view"></a><a name="target_groups-view"></a>Widok target_groups

[praca]. [target_groups]

Wyświetla listę wszystkich grup docelowych.

|Nazwa kolumny|Typ danych| Opis|
|-----|-----|-----|
|**target_group_name**| nvarchar(128)   |Nazwa grupy docelowej, zbiór baz danych. 
|**target_group_id**    |uniqueidentifier   |Unikatowy identyfikator grupy docelowej.

### <a name="target_group_members-view"></a><a name="target_group_members-view"></a>widok target_group_members

[praca]. [target_group_members]

Pokazuje wszystkich członków wszystkich grup docelowych.

|Nazwa kolumny|Typ danych| Opis|
|-----|-----|-----|
|**target_group_name**  |nvarchar(128|Nazwa grupy docelowej, zbiór baz danych. |
|**target_group_id**    |uniqueidentifier   |Unikatowy identyfikator grupy docelowej.|
|**membership_type**    |int|   Określa, czy członek grupy docelowej jest uwzględniony lub wykluczony w grupie docelowej. Prawidłowe wartości dla target_group_name to "Uwzględnij" lub "Wyklucz".|
|**target_type**    |nvarchar(128)| Typ docelowej bazy danych lub kolekcji baz danych, w tym wszystkie bazy danych na serwerze, wszystkie bazy danych w puli elastycznej lub bazy danych. Prawidłowe wartości dla target_type to "SqlServer", "SqlElasticPool", "SqlDatabase" lub "SqlShardMap".|
|**target_id**  |uniqueidentifier|  Unikatowy identyfikator członka grupy docelowej.|
|**refresh_credential_name**    |nvarchar(128)  |Nazwa poświadczenia o zakresie bazy danych używanego do łączenia się z członkiem grupy docelowej.|
|**subscription_id**    |uniqueidentifier|  Unikatowy identyfikator subskrypcji.|
|**resource_group_name**    |nvarchar(128)| Nazwa grupy zasobów, w której znajduje się członek grupy docelowej.|
|**Nazwa_serwera**    |nvarchar(128)  |Nazwa serwera bazy danych SQL zawartej w grupie docelowej. Określono tylko wtedy, gdy target_type jest "SqlServer". |
|**Nazwa_bazy_danych**  |nvarchar(128)  |Nazwa bazy danych zawartej w grupie docelowej. Określony tylko wtedy, gdy target_type jest "SqlDatabase".|
|**elastic_pool_name**  |nvarchar(128)| Nazwa puli elastycznej zawartej w grupie docelowej. Określony tylko wtedy, gdy target_type jest "SqlElasticPool".|
|**shard_map_name** |nvarchar(128)| Nazwa mapy niezależnego fragmentu zawartej w grupie docelowej. Określony tylko wtedy, gdy target_type jest "SqlShardMap".|


## <a name="resources"></a>Resources

 - ![Ikona łącza tematu](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "Ikona linku tematu") [Konwencje składniowe Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  


## <a name="next-steps"></a>Następne kroki

- [Tworzenie zadań elastycznych i zarządzanie nimi przy użyciu programu PowerShell](elastic-jobs-powershell.md)
- [Autoryzacja i uprawnienia PROGRAMU SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
