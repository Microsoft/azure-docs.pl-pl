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
ms.openlocfilehash: fad19d360f7c476ba71a9bbe00b58387b92f8ac4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690564"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Zarządzanie długoterminowym przechowywaniem kopii zapasowych Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Za pomocą Azure SQL Database można ustawić [długoterminowe zasady przechowywania kopii zapasowych](long-term-retention-overview.md) (LTR), aby automatycznie zachować kopie zapasowe w oddzielnych kontenerach usługi Azure Blob Storage przez maksymalnie 10 lat. Następnie można odzyskać bazę danych przy użyciu tych kopii zapasowych przy użyciu Azure Portal lub programu PowerShell. Zasady przechowywania długoterminowego są również obsługiwane w przypadku [wystąpienia zarządzanego Azure SQL](../managed-instance/long-term-backup-retention-configure.md).

## <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

W poniższych sekcjach pokazano, jak za pomocą Azure Portal skonfigurować zasady przechowywania długoterminowego, zarządzać dostępnymi kopiami zapasowymi i przywracać je z dostępnej kopii zapasowej.

### <a name="configure-long-term-retention-policies"></a>Konfigurowanie zasad przechowywania długoterminowego

Można skonfigurować SQL Database, aby [zachować automatyczne kopie zapasowe](long-term-retention-overview.md) przez okres dłuższy niż okres przechowywania w warstwie usług.

1. W Azure Portal przejdź do serwera, a następnie wybierz pozycję **kopie zapasowe**. Wybierz kartę **zasady przechowywania** , aby zmodyfikować ustawienia przechowywania kopii zapasowych.

   ![środowisko zasad przechowywania](./media/long-term-backup-retention-configure/ltr-policies-tab.png)

2. Na karcie Zasady przechowywania wybierz bazy danych, dla których chcesz ustawić lub zmodyfikować długoterminowe zasady przechowywania kopii zapasowych. Nie wpłynie to na niewybrane bazy danych.

   ![Wybierz bazę danych, aby skonfigurować zasady przechowywania kopii zapasowych](./media/long-term-backup-retention-configure/ltr-policies-tab-configure.png)

3. W okienku **Konfigurowanie zasad** Określ żądany okres przechowywania dla cotygodniowych, comiesięcznych i corocznych kopii zapasowych. Wybierz okres przechowywania równy "0", aby wskazać, że nie powinno być ustawione długoterminowe przechowywanie kopii zapasowych.

   ![okienko Konfigurowanie zasad](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

4. Wybierz pozycję **Zastosuj** , aby zastosować wybrane ustawienia przechowywania do wszystkich wybranych baz danych.

> [!IMPORTANT]
> Włączenie długoterminowych zasad przechowywania kopii zapasowych może zająć do 7 dni, zanim pierwsza kopia zapasowa stanie się widoczna i będzie dostępna do przywrócenia. Aby uzyskać szczegółowe informacje dotyczące CADANCE kopii zapasowych LTR, zobacz [długoterminowe przechowywanie kopii zapasowych](long-term-retention-overview.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Wyświetl kopie zapasowe i Przywróć z kopii zapasowej

Wyświetl kopie zapasowe, które są zachowywane dla określonej bazy danych za pomocą zasad oddzielania i Przywróć te kopie zapasowe.

1. W Azure Portal przejdź do serwera, a następnie wybierz pozycję **kopie zapasowe**. Aby wyświetlić dostępne kopie zapasowe programu LTR dla określonej bazy danych, wybierz pozycję **Zarządzaj** w kolumnie dostępne kopie zapasowe ltr. Zostanie wyświetlone okienko z listą dostępnych kopii zapasowych LTR dla wybranej bazy danych.

   ![dostępne środowisko wykonywania kopii zapasowych](./media/long-term-backup-retention-configure/ltr-available-backups-tab.png)

1. W wyświetlonym okienku **kopie zapasowe ltr** Przejrzyj dostępne kopie zapasowe. Możesz wybrać kopię zapasową do przywrócenia lub do usunięcia.

   ![Wyświetl dostępne kopie zapasowe LTR](./media/long-term-backup-retention-configure/ltr-available-backups-manage.png)

1. Aby przywrócić z dostępnej kopii zapasowej LTR, wybierz kopię zapasową, z której chcesz przywrócić, a następnie wybierz pozycję **Przywróć**.

   ![Przywróć z dostępnej kopii zapasowej LTR](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)

1. Wybierz nazwę nowej bazy danych, a następnie wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć szczegóły przywracania. Wybierz pozycję **Utwórz** , aby przywrócić bazę danych z wybranej kopii zapasowej.

   ![Skonfiguruj szczegóły przywracania](./media/long-term-backup-retention-configure/restore-ltr.png)

1. Na pasku narzędzi wybierz ikonę powiadomienia, aby wyświetlić stan zadania przywracania.

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
> Usuwanie kopii zapasowej LTR jest nieodwracalne. Aby usunąć kopię zapasową LTR po usunięciu serwera, musisz mieć uprawnienia do zakresu subskrypcji. Można skonfigurować powiadomienia dotyczące każdego usunięcia w Azure Monitor przez filtrowanie operacji "usuwa długoterminową kopię zapasową przechowywania". Dziennik aktywności zawiera informacje o tym, kto i kiedy żądanie zostało zgłoszone. Szczegółowe instrukcje znajdują się w temacie [tworzenie alertów dziennika aktywności](../../azure-monitor/alerts/alerts-activity-log.md) .

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
