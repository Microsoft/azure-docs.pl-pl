---
title: 'Azure SQL Database: Zarządzanie długoterminowym przechowywaniem kopii zapasowych'
description: Dowiedz się, jak przechowywać i przywracać automatyczne kopie zapasowe Azure SQL Database w usłudze Azure Storage (przez maksymalnie 10 lat) przy użyciu Azure Portal i programu PowerShell
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/16/2020
ms.openlocfilehash: a0653f24eeb0a96c28714d00f1d943dfc7d336db
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979712"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Zarządzanie długoterminowym przechowywaniem kopii zapasowych Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

W Azure SQL Database można skonfigurować bazę danych z zasadami [długoterminowego przechowywania kopii zapasowych](long-term-retention-overview.md) (LTR), aby automatycznie zachować kopie zapasowe bazy danych w oddzielnych kontenerach usługi Azure Blob Storage przez maksymalnie 10 lat. Następnie można odzyskać bazę danych przy użyciu tych kopii zapasowych przy użyciu Azure Portal lub programu PowerShell. Możesz również skonfigurować długoterminowe przechowywanie dla [wystąpienia zarządzanego usługi Azure SQL](../managed-instance/long-term-backup-retention-configure.md) , ale jest ono obecnie w ograniczonej publicznej wersji zapoznawczej.

## <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

W poniższych sekcjach pokazano, jak za pomocą Azure Portal skonfigurować długoterminowe przechowywanie, wyświetlać kopie zapasowe w długoterminowym przechowywaniu oraz przywracać kopie zapasowe z długoterminowego przechowywania.

### <a name="configure-long-term-retention-policies"></a>Konfigurowanie zasad przechowywania długoterminowego

Można skonfigurować SQL Database, aby [zachować automatyczne kopie zapasowe](long-term-retention-overview.md) przez okres dłuższy niż okres przechowywania w warstwie usług.

1. W Azure Portal wybierz wystąpienie SQL Server, a następnie kliknij pozycję **Zarządzaj kopiami zapasowymi**. Na karcie **Konfigurowanie zasad** zaznacz pole wyboru dla bazy danych, dla której chcesz ustawić lub zmodyfikować długoterminowe zasady przechowywania kopii zapasowych. Jeśli pole wyboru obok bazy danych nie jest zaznaczone, zmiany zasad nie będą miały zastosowania do tej bazy danych.  

   ![Link zarządzania kopiami zapasowymi](./media/long-term-backup-retention-configure/ltr-configure-ltr.png)

2. W okienku **Konfigurowanie zasad** wybierz, czy chcesz zachować tygodniowe, miesięczne i roczne kopie zapasowe, a także określić okres przechowywania dla każdego z nich.

   ![Konfigurowanie zasad](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

3. Po zakończeniu kliknij przycisk **Zastosuj**.

> [!IMPORTANT]
> Włączenie długoterminowych zasad przechowywania kopii zapasowych może zająć do 7 dni, zanim pierwsza kopia zapasowa stanie się widoczna i będzie dostępna do przywrócenia. Aby uzyskać szczegółowe informacje dotyczące CADANCE kopii zapasowych LTR, zobacz [długoterminowe przechowywanie kopii zapasowych](long-term-retention-overview.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Wyświetl kopie zapasowe i Przywróć z kopii zapasowej

Wyświetl kopie zapasowe, które są zachowywane dla określonej bazy danych za pomocą zasad oddzielania i Przywróć te kopie zapasowe.

1. W Azure Portal wybierz serwer, a następnie kliknij pozycję **Zarządzaj kopiami zapasowymi**. Na karcie **dostępne kopie zapasowe** wybierz bazę danych, dla której chcesz wyświetlić dostępne kopie zapasowe.

   ![Wybierz bazę danych](./media/long-term-backup-retention-configure/ltr-available-backups-select-database.png)

1. Zapoznaj się z dostępnymi kopiami zapasowymi w okienku **dostępne kopie zapasowe** .

   ![Wyświetl kopie zapasowe](./media/long-term-backup-retention-configure/ltr-available-backups.png)

1. Wybierz kopię zapasową, z której chcesz przywrócić, a następnie określ nową nazwę bazy danych.

   ![Przywracanie](./media/long-term-backup-retention-configure/ltr-restore.png)

1. Kliknij przycisk **OK** , aby przywrócić bazę danych z kopii zapasowej w usłudze Azure Storage do nowej bazy danych.

1. Na pasku narzędzi kliknij ikonę powiadomienia, aby wyświetlić stan zadania przywracania.

   ![postęp zadania przywracania](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. Po zakończeniu zadania przywracania Otwórz stronę **bazy danych SQL** , aby wyświetlić nowo przywróconą bazę danych.

> [!NOTE]
> W tym miejscu możesz połączyć się z przywróconą bazą danych przy użyciu programu SQL Server Management Studio, aby wykonać niezbędne zadania, takie jak [wyodrębnienie bitu danych z przywróconej bazy danych w celu skopiowania do istniejącej bazy danych lub usunięcie istniejącej bazę danych i zmiana nazwy przywróconej bazy danych na istniejącą nazwę bazy danych](recovery-using-backups.md#point-in-time-restore).

## <a name="using-powershell"></a>Korzystanie z programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

W poniższych sekcjach pokazano, jak za pomocą programu PowerShell skonfigurować długoterminowe przechowywanie kopii zapasowych, wyświetlać kopie zapasowe w usłudze Azure Storage i przywracać je z kopii zapasowej w usłudze Azure Storage.

### <a name="azure-roles-to-manage-long-term-retention"></a>Role platformy Azure do zarządzania długoterminowym przechowywaniem

W przypadku funkcji **Get-AzSqlDatabaseLongTermRetentionBackup** i **Restore-AzSqlDatabase** konieczne będzie posiadanie jednej z następujących ról:

- Rola właściciela subskrypcji lub
- SQL Server rolę współautor lub
- Rola niestandardowa z następującymi uprawnieniami:

   Microsoft. SQL/Locations/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Read

W przypadku **usunięcia-AzSqlDatabaseLongTermRetentionBackup** należy mieć jedną z następujących ról:

- Rola właściciela subskrypcji lub
- Rola niestandardowa z następującymi uprawnieniami:

   Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Delete

> [!NOTE]
> Rola współautor SQL Server nie ma uprawnień do usuwania kopii zapasowych LTR.

Uprawnienia kontroli RBAC platformy Azure można udzielić w zakresie *subskrypcji* lub *grupy zasobów* . Aby jednak uzyskać dostęp do kopii zapasowych LTR należących do porzuconego serwera, należy przyznać uprawnienie w zakresie *subskrypcji* tego serwera.

- Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Delete

### <a name="create-an-ltr-policy"></a>Tworzenie zasad odltr

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Wyświetl zasady LTR

Ten przykład pokazuje, jak wyświetlić listę zasad LTR na serwerze

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $serverName | `
    Get-AzSqlDatabaseLongTermRetentionPolicy

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup
```

### <a name="clear-an-ltr-policy"></a>Wyczyść zasady odltr

Ten przykład pokazuje, jak wyczyścić zasady LTR z bazy danych

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Wyświetl kopie zapasowe LTR

Ten przykład pokazuje, jak wyświetlić listę kopii zapasowych LTR na serwerze.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Usuwanie kopii zapasowych LTR

Ten przykład pokazuje, jak usunąć kopię zapasową LTR z listy kopii zapasowych.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Usuwanie kopii zapasowej LTR jest nieodwracalne. Aby usunąć kopię zapasową LTR po usunięciu serwera lub grupy zasobów, musisz mieć uprawnienia do zakresu subskrypcji. Można skonfigurować powiadomienia dotyczące każdego usunięcia w Azure Monitor przez filtrowanie operacji "usuwa długoterminową kopię zapasową przechowywania". Dziennik aktywności zawiera informacje o tym, kto i kiedy żądanie zostało zgłoszone. Szczegółowe instrukcje znajdują się w temacie [tworzenie alertów dziennika aktywności](../../azure-monitor/platform/alerts-activity-log.md) .

### <a name="restore-from-ltr-backups"></a>Przywracanie z kopii zapasowych LTR

Ten przykład pokazuje, jak przywrócić z kopii zapasowej LTR. Zwróć uwagę, że ten interfejs nie został zmieniony, ale parametr identyfikatora zasobu wymaga teraz identyfikatora zasobu kopii zapasowej LTR.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Aby przywrócić dane z kopii zapasowej LTR po usunięciu serwera lub grupy zasobów, musisz mieć uprawnienia do zakresu subskrypcji serwera, a subskrypcja musi być aktywna. Należy również pominąć opcjonalny parametr-ResourceGroupName.

> [!NOTE]
> W tym miejscu możesz połączyć się z przywróconą bazą danych przy użyciu programu SQL Server Management Studio, aby wykonać niezbędne zadania, takie jak wyodrębnienie bitu danych z przywróconej bazy danych w celu skopiowania do istniejącej bazy danych lub usunięcie istniejącej bazę danych i zmiana nazwy przywróconej bazy danych na istniejącą nazwę bazy danych. Zobacz [przywracanie do punktu w czasie](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations"></a>Ograniczenia
- W przypadku przywracania z kopii zapasowej LTR Właściwość Skala odczytu jest wyłączona. Aby włączyć, odczytywać skalowanie w przywróconej bazie danych, zaktualizuj bazę danych po jej utworzeniu.
- Należy określić docelowy cel poziomu usługi podczas przywracania z kopii zapasowej LTR, która została utworzona, gdy baza danych była w puli elastycznej. 

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o automatycznych kopiach zapasowych generowanych przez usługi, zobacz artykuł dotyczący [automatycznych kopii zapasowych](automated-backups-overview.md)
- Aby dowiedzieć się więcej o długoterminowym przechowywaniu kopii zapasowych, zobacz temat dotyczący [długoterminowego przechowywania kopii zapasowych](long-term-retention-overview.md)
