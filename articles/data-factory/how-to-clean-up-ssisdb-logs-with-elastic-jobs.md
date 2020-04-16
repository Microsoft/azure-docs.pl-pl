---
title: Oczyszczanie dzienników usługi SSISDB za pomocą zadań elastycznej bazy danych platformy Azure
description: W tym artykule opisano sposób czyszczenia dzienników SSISDB przy użyciu zadań elastycznej bazy danych azure do wyzwalania procedury składowanej, która istnieje w tym celu
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/13/2018
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: 9d509a40c129cab0f5d2f614c6989f81a928a254
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81412915"
---
# <a name="clean-up-ssisdb-logs-with-azure-elastic-database-jobs"></a>Oczyszczanie dzienników usługi SSISDB za pomocą zadań elastycznej bazy danych platformy Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano sposób używania zadań elastycznej bazy danych platformy Azure do wyzwalania procedury składowanej, która czyści dzienniki bazy danych wykazu usług SQL Server Integration Services, `SSISDB`.

Zadania elastycznej bazy danych to usługa platformy Azure, która ułatwia automatyzację i uruchamianie zadań w bazie danych lub grupie baz danych. Można zaplanować, uruchomić i monitorować te zadania przy użyciu interfejsu API Azure portal, Transact-SQL, PowerShell lub REST. Użyj zadania elastycznej bazy danych, aby wyzwolić procedurę składowaną do oczyszczania dziennika jeden raz lub zgodnie z harmonogramem. Można wybrać interwał harmonogramu na podstawie użycia zasobów SSISDB, aby uniknąć dużego obciążenia bazy danych.

Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami baz danych za pomocą zadań elastycznej bazy danych](../sql-database/elastic-jobs-overview.md).

W poniższych sekcjach opisano sposób `[internal].[cleanup_server_retention_window_exclusive]`wyzwalania procedury składowanej, która usuwa dzienniki SSISDB, które znajdują się poza oknem przechowywania ustawionym przez administratora.

## <a name="clean-up-logs-with-power-shell"></a>Czyszczenie dzienników za pomocą powłoki zasilania

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Poniższe przykładowe skrypty programu PowerShell tworzą nowe zadanie elastyczne, aby wyzwolić procedurę składowaną dla oczyszczania dziennika SSISDB. Aby uzyskać więcej informacji, zobacz [Tworzenie agenta zadania elastycznego przy użyciu programu PowerShell](../sql-database/elastic-jobs-powershell.md).

### <a name="create-parameters"></a>Tworzenie parametrów

``` powershell
# Parameters needed to create the Job Database
param(
$ResourceGroupName = $(Read-Host "Please enter an existing resource group name"),
$AgentServerName = $(Read-Host "Please enter the name of an existing Azure SQL server(for example, yhxserver) to hold the SSISDBLogCleanup job database"),
$SSISDBLogCleanupJobDB = $(Read-Host "Please enter a name for the Job Database to be created in the given SQL Server"),
# The Job Database should be a clean,empty,S0 or higher service tier. We set S0 as default.
$PricingTier = "S0",

# Parameters needed to create the Elastic Job agent
$SSISDBLogCleanupAgentName = $(Read-Host "Please enter a name for your new Elastic Job agent"),

# Parameters needed to create the job credential in the Job Database to connect to SSISDB
$PasswordForSSISDBCleanupUser = $(Read-Host "Please provide a new password for SSISDBLogCleanup job user to connect to SSISDB database for log cleanup"),
# Parameters needed to create a login and a user in the SSISDB of the target server
$SSISDBServerEndpoint = $(Read-Host "Please enter the name of the target Azure SQL server which contains SSISDB you need to cleanup, for example, myserver") + '.database.windows.net',
$SSISDBServerAdminUserName = $(Read-Host "Please enter the target server admin username for SQL authentication"),
$SSISDBServerAdminPassword = $(Read-Host "Please enter the target server admin password for SQL authentication"),
$SSISDBName = "SSISDB",

# Parameters needed to set job scheduling to trigger execution of cleanup stored procedure
$RunJobOrNot = $(Read-Host "Please indicate whether you want to run the job to cleanup SSISDB logs outside the log retention window immediately(Y/N). Make sure the retention window is set appropriately before running the following powershell scripts. Those removed SSISDB logs cannot be recoverd"),
$IntervalType = $(Read-Host "Please enter the interval type for the execution schedule of SSISDB log cleanup stored procedure. For the interval type, Year, Month, Day, Hour, Minute, Second can be supported."),
$IntervalCount = $(Read-Host "Please enter the detailed interval value in the given interval type for the execution schedule of SSISDB log cleanup stored procedure"),
# StartTime of the execution schedule is set as the current time as default. 
$StartTime = (Get-Date)
```

### <a name="trigger-the-cleanup-stored-procedure"></a>Wyzwalanie procedury oczyszczania składowanej

```powershell
# Install the latest PackageManagement powershell package which PowershellGet v1.6.5 is dependent on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force
# You may need to restart the powershell session
# Install the latest PowershellGet module which adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Place AzureRM.Sql preview cmdlets side by side with existing AzureRM.Sql version
Install-Module -Name AzureRM.Sql -AllowPrerelease -Force

# Sign in to your Azure account
Connect-AzureRmAccount

# Create a Job Database which is used for defining jobs of triggering SSISDB log cleanup stored procedure and tracking cleanup history of jobs
Write-Output "Creating a blank SQL database to be used as the SSISDBLogCleanup  Job Database ..."
$JobDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $SSISDBLogCleanupJobDB -RequestedServiceObjectiveName $PricingTier
$JobDatabase

# Enable the Elastic Jobs preview in your Azure subscription
Register-AzureRmProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql

# Create the Elastic Job agent
Write-Output "Creating the Elastic Job agent..."
$JobAgent = $JobDatabase | New-AzureRmSqlElasticJobAgent -Name $SSISDBLogCleanupAgentName
$JobAgent

# Create the job credential in the Job Database to connect to SSISDB database in the target server for log cleanup
Write-Output "Creating job credential to connect to SSISDB database..."
$JobCredSecure = ConvertTo-SecureString -String $PasswordForSSISDBCleanupUser -AsPlainText -Force
$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "SSISDBLogCleanupUser", $JobCredSecure
$JobCred = $JobAgent | New-AzureRmSqlElasticJobCredential -Name "SSISDBLogCleanupUser" -Credential $JobCred

# In the master database of the target SQL server which contains SSISDB to cleanup 
# - Create the job user login
Write-Output "Grant permissions on the master database of the target server..."
$Params = @{
  'Database' = 'master'
  'ServerInstance' = $SSISDBServerEndpoint
  'Username' = $SSISDBServerAdminUserName
  'Password' = $SSISDBServerAdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '" + $PasswordForSSISDBCleanupUser + "'"
}
Invoke-SqlCmd @Params

# For SSISDB database of the target SQL server
# - Create the SSISDBLogCleanup user from the SSISDBlogCleanup user login
# - Grant permissions for the execution of SSISDB log cleanup stored procedure 
Write-Output "Grant appropriate permissions on SSISDB database..."
$TargetDatabase = $SSISDBName
$CreateJobUser = "CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser"
$GrantStoredProcedureExecution = "GRANT EXECUTE ON internal.cleanup_server_retention_window_exclusive TO SSISDBLogCleanupUser"

$TargetDatabase | % {
  $Params.Database = $_
  $Params.Query = $CreateJobUser
  Invoke-SqlCmd @Params
  $Params.Query = $GrantStoredProcedureExecution
  Invoke-SqlCmd @Params
}

# Create a target group which includes SSISDB database needed to cleanup
Write-Output "Creating the target group including only SSISDB database needed to cleanup ..."
$SSISDBTargetGroup = $JobAgent | New-AzureRmSqlElasticJobTargetGroup -Name "SSISDBTargetGroup"
$SSISDBTargetGroup | Add-AzureRmSqlElasticJobTarget -ServerName $SSISDBServerEndpoint -Database $SSISDBName 

# Create the job to trigger execution of SSISDB log cleanup stored procedure
Write-Output "Creating a new job to trigger execution of the stored procedure for SSISDB log cleanup"
$JobName = "CleanupSSISDBLog"
$Job = $JobAgent | New-AzureRmSqlElasticJob -Name $JobName -RunOnce
$Job

# Add the job step to execute internal.cleanup_server_retention_window_exclusive
Write-Output "Adding the job step for the cleanup stored procedure execution"
$SqlText = "EXEC internal.cleanup_server_retention_window_exclusive"
$Job | Add-AzureRmSqlElasticJobStep -Name "step to execute cleanup stored procedure" -TargetGroupName $SSISDBTargetGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText

# Run the job to immediately start cleanup stored procedure execution for once
IF(($RunJobOrNot = "Y") -Or ($RunJobOrNot = "y"))
{
Write-Output "Start a new execution of the stored procedure for SSISDB log cleanup immediately..."
$JobExecution = $Job | Start-AzureRmSqlElasticJob
$JobExecution
}

# Schedule the job running to trigger stored procedure execution on schedule for removing SSISDB logs outside the retention window
Write-Output "Start the execution schedule of the stored procedure for SSISDB log cleanup..."
$Job | Set-AzureRmSqlElasticJob -IntervalType $IntervalType -IntervalCount $IntervalCount -StartTime $StartTime -Enable
```

## <a name="clean-up-logs-with-transact-sql"></a>Oczyszczanie dzienników za pomocą usługi Transact-SQL

Poniższe przykładowe skrypty Transact-SQL tworzą nowe zadanie elastyczne, aby wyzwolić procedurę składowaną dla oczyszczania dziennika SSISDB. Aby uzyskać więcej informacji, zobacz [Tworzenie zadań elastycznej bazy danych za pomocą funkcji Transact-SQL (T-SQL) i zarządzanie nimi.](../sql-database/elastic-jobs-tsql.md)

1. Tworzenie lub identyfikowanie pustej bazy danych SQL Azure Azure jako bazy danych zadań SSISDBCleanup. Następnie utwórz elastycznego agenta zadań w [witrynie Azure portal](https://ms.portal.azure.com/#create/Microsoft.SQLElasticJobAgent).

2. W bazie danych zadań utwórz poświadczenia dla zadania oczyszczania dziennika SSISDB. To poświadczenie jest używane do łączenia się z bazą danych SSISDB w celu oczyszczenia dzienników.

    ```sql
    -- Connect to the job database specified when creating the job agent
    -- Create a database master key if one does not already exist, using your own password.  
    CREATE MASTER KEY ENCRYPTION BY PASSWORD= '<EnterStrongPasswordHere>';  

    -- Create a credential for SSISDB log cleanup.  
    CREATE DATABASE SCOPED CREDENTIAL SSISDBLogCleanupCred WITH IDENTITY = 'SSISDBLogCleanupUser', SECRET = '<EnterStrongPasswordHere>'; 
    ```

3. Zdefiniuj grupę docelową, która zawiera bazę danych SSISDB, dla której chcesz uruchomić procedurę składowaną oczyszczania.

    ```sql
    -- Connect to the job database 
    -- Add a target group 
    EXEC jobs.sp_add_target_group 'SSISDBTargetGroup'

    -- Add SSISDB database into the target group
    EXEC jobs.sp_add_target_group_member 'SSISDBTargetGroup',
    @target_type = 'SqlDatabase',
    @server_name = '<EnterSSISDBTargetServerName>',
    @database_name = '<EnterSSISDBName>'

    --View the recently created target group and target group members
    SELECT * FROM jobs.target_groups WHERE target_group_name = 'SSISDBTargetGroup';
    SELECT * FROM jobs.target_group_members WHERE target_group_name = 'SSISDBTargetGroup';
    ```
4. Udziel odpowiednich uprawnień dla bazy danych SSISDB. Katalog SSISDB musi mieć odpowiednie uprawnienia do procedury składowanej, aby pomyślnie uruchomić oczyszczanie dziennika SSISDB. Aby uzyskać szczegółowe wskazówki, zobacz [Zarządzanie loginami](../sql-database/sql-database-manage-logins.md).

    ```sql
    -- Connect to the master database in the target server including SSISDB 
    CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '<strong_password>';

    -- Connect to SSISDB database in the target server to cleanup logs
    CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser;
    GRANT EXECUTE ON internal.cleanup_server_retention_window_exclusive TO SSISDBLogCleanupUser
    ```
5. Utwórz zadanie i dodaj krok zadania, aby wyzwolić wykonanie procedury składowanej dla oczyszczania dziennika SSISDB.

    ```sql
    --Connect to the job database 
    --Add the job for the execution of SSISDB log cleanup stored procedure.
    EXEC jobs.sp_add_job @job_name='CleanupSSISDBLog', @description='Remove SSISDB logs which are outside the retention window'

    --Add a job step to execute internal.cleanup_server_retention_window_exclusive
    EXEC jobs.sp_add_jobstep @job_name='CleanupSSISDBLog',
    @command=N'EXEC internal.cleanup_server_retention_window_exclusive',
    @credential_name='SSISDBLogCleanupCred',
    @target_group_name='SSISDBTargetGroup'
    ```
6. Przed kontynuowaniem upewnij się, że okno przechowywania zostało odpowiednio ustawione. Dzienniki SSISDB poza oknem są usuwane i nie można ich odzyskać.

   Następnie można natychmiast uruchomić zadanie, aby rozpocząć oczyszczanie dziennika SSISDB.

    ```sql
    --Connect to the job database 
    --Run the job immediately to execute the stored procedure for SSISDB log cleanup
    declare @je uniqueidentifier
    exec jobs.sp_start_job 'CleanupSSISDBLog', @job_execution_id = @je output

    --Watch the execution results for SSISDB log cleanup 
    select @je
    select * from jobs.job_executions where job_execution_id = @je
    ```
7. Opcjonalnie należy zaplanować wykonanie zadań, aby usunąć dzienniki SSISDB poza oknem przechowywania zgodnie z harmonogramem. Użyj podobnej instrukcji, aby zaktualizować parametry zadania.

    ```sql
    --Connect to the job database 
    EXEC jobs.sp_update_job
    @job_name='CleanupSSISDBLog',
    @enabled=1,
    @schedule_interval_type='<EnterIntervalType(Month,Day,Hour,Minute,Second)>',
    @schedule_interval_count='<EnterDetailedIntervalValue>',
    @schedule_start_time='<EnterProperStartTimeForSchedule>',
    @schedule_end_time='<EnterProperEndTimeForSchedule>'
    ```

## <a name="monitor-the-cleanup-job-in-the-azure-portal"></a>Monitorowanie zadania oczyszczania w witrynie Azure portal

Można monitorować wykonywanie zadania oczyszczania w witrynie Azure portal. Dla każdego wykonania jest widoczny stan, godzina rozpoczęcia i godzina zakończenia zadania.

![Monitorowanie zadania oczyszczania w witrynie Azure portal](media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/monitor-cleanup-job-portal.png)

## <a name="monitor-the-cleanup-job-with-transact-sql"></a>Monitorowanie zadania oczyszczania za pomocą usługi Transact-SQL

Można również użyć Transact-SQL, aby wyświetlić historię wykonywania zadania oczyszczania.

```sql
--Connect to the job database 
--View all execution statuses for the job to cleanup SSISDB logs
SELECT * FROM jobs.job_executions WHERE job_name = 'CleanupSSISDBLog' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions WHERE is_active = 1
ORDER BY start_time DESC
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać zadania zarządzania i monitorowania związane z środowiska wykonawczego integracji Azure-SSIS, zobacz następujące artykuły. Azure-SSIS IR jest aparatem środowiska wykonawczego dla pakietów SSIS przechowywanych w SSISDB w bazie danych SQL Azure.

-   [Ponowne konfigurowanie środowiska Azure SSIS Integration Runtime](manage-azure-ssis-integration-runtime.md)

-   [Monitoruj środowisko uruchomieniowe integracji platformy Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).
