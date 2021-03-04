---
title: 'Wystąpienie zarządzane Azure SQL: długoterminowe przechowywanie kopii zapasowych'
description: Dowiedz się, jak przechowywać i przywracać automatyczne kopie zapasowe w oddzielnych kontenerach usługi Azure Blob Storage dla wystąpienia zarządzanego Azure SQL przy użyciu programu PowerShell.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma, sstein
ms.date: 02/25/2021
ms.openlocfilehash: f298f0f9d76750be932db79b5a08b6385e984f88
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052030"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>Zarządzanie długoterminowym przechowywaniem kopii zapasowych wystąpienia zarządzanego usługi Azure SQL (program PowerShell)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

W wystąpieniu zarządzanym usługi Azure SQL można skonfigurować [długoterminowe zasady przechowywania kopii zapasowych](../database/long-term-retention-overview.md) (LTR) jako publiczną funkcję w wersji zapoznawczej. Pozwala to na automatyczne zachowywanie kopii zapasowych bazy danych w oddzielnych kontenerach usługi Azure Blob Storage przez maksymalnie 10 lat. Następnie można odzyskać bazę danych przy użyciu tych kopii zapasowych za pomocą programu PowerShell.

   > [!IMPORTANT]
   > Odmowa dla wystąpień zarządzanych jest obecnie dostępna w publicznej wersji zapoznawczej w regionach publicznych platformy Azure. 

W poniższych sekcjach pokazano, jak używać programu PowerShell do konfigurowania długoterminowego przechowywania kopii zapasowych, wyświetlania kopii zapasowych w usłudze Azure SQL Storage oraz przywracania danych z kopii zapasowej w usłudze Azure SQL Storage.


## <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

W poniższych sekcjach pokazano, jak za pomocą Azure Portal skonfigurować zasady przechowywania długoterminowego, zarządzać dostępnymi kopiami zapasowymi i przywracać je z dostępnej kopii zapasowej.

### <a name="configure-long-term-retention-policies"></a>Konfigurowanie zasad przechowywania długoterminowego

Wystąpienie zarządzane SQL można skonfigurować tak, aby [zachować automatyczne kopie zapasowe](../database/long-term-retention-overview.md) przez okres dłuższy niż okres przechowywania w warstwie usług.

1. W Azure Portal wybierz wystąpienie zarządzane, a następnie kliknij pozycję **kopie zapasowe**. Na karcie **zasady przechowywania** wybierz bazy danych, dla których chcesz ustawić lub zmodyfikować długoterminowe zasady przechowywania kopii zapasowych. Zmiany nie zostaną zastosowane do żadnej z pozostałych baz danych, które nie zostały zaznaczone. 

   ![Link zarządzania kopiami zapasowymi](./media/long-term-backup-retention-configure/ltr-configure-ltr.png)

2. W okienku **Konfigurowanie zasad** Określ żądany okres przechowywania dla cotygodniowych, comiesięcznych i corocznych kopii zapasowych. Wybierz okres przechowywania równy "0", aby wskazać, że nie powinno być ustawione długoterminowe przechowywanie kopii zapasowych.

   ![Konfigurowanie zasad](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

3. Po zakończeniu kliknij przycisk **Zastosuj**.

> [!IMPORTANT]
> Włączenie długoterminowych zasad przechowywania kopii zapasowych może zająć do 7 dni, zanim pierwsza kopia zapasowa stanie się widoczna i będzie dostępna do przywrócenia. Aby uzyskać szczegółowe informacje dotyczące CADANCE kopii zapasowych LTR, zobacz [długoterminowe przechowywanie kopii zapasowych](../database/long-term-retention-overview.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Wyświetl kopie zapasowe i Przywróć z kopii zapasowej

Wyświetl kopie zapasowe, które są zachowywane dla określonej bazy danych za pomocą zasad oddzielania i Przywróć te kopie zapasowe.

1. W Azure Portal wybierz wystąpienie zarządzane, a następnie kliknij pozycję **kopie zapasowe**. Na karcie **dostępne kopie zapasowe** wybierz bazę danych, dla której chcesz wyświetlić dostępne kopie zapasowe. Kliknij pozycję **Zarządzaj**.

   ![Wybierz bazę danych](./media/long-term-backup-retention-configure/ltr-available-backups-select-database.png)

1. W okienku **Zarządzanie kopiami zapasowymi** zapoznaj się z dostępnymi kopiami zapasowymi.

   ![Wyświetl kopie zapasowe](./media/long-term-backup-retention-configure/ltr-available-backups.png)

1. Wybierz kopię zapasową, z której chcesz przywrócić, kliknij przycisk **Przywróć**, a następnie na stronie przywracanie określ nową nazwę bazy danych. Kopia zapasowa i źródło zostaną wstępnie wypełnione na tej stronie. 

   ![Wybierz kopię zapasową do przywrócenia](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)
   
   ![Przywracanie](./media/long-term-backup-retention-configure/ltr-restore.png)

1. Kliknij przycisk **Przegląd + Utwórz** , aby przejrzeć szczegóły przywracania. Następnie kliknij przycisk **Utwórz** , aby przywrócić bazę danych z wybranej kopii zapasowej.

1. Na pasku narzędzi kliknij ikonę powiadomienia, aby wyświetlić stan zadania przywracania.

   ![postęp zadania przywracania](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. Po zakończeniu zadania przywracania Otwórz stronę **omówienia wystąpienia zarządzanego** , aby wyświetlić nowo przywróconą bazę danych.

> [!NOTE]
> W tym miejscu możesz połączyć się z przywróconą bazą danych przy użyciu programu SQL Server Management Studio, aby wykonać niezbędne zadania, takie jak [wyodrębnienie bitu danych z przywróconej bazy danych w celu skopiowania do istniejącej bazy danych lub usunięcie istniejącej bazę danych i zmiana nazwy przywróconej bazy danych na istniejącą nazwę bazy danych](../database/recovery-using-backups.md#point-in-time-restore).


## <a name="using-powershell"></a>Korzystanie z programu PowerShell
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, jednak przyszłe programowanie zostanie wykonane w module AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

W poniższych sekcjach pokazano, jak za pomocą programu PowerShell skonfigurować długoterminowe przechowywanie kopii zapasowych, wyświetlać kopie zapasowe w usłudze Azure Storage i przywracać je z kopii zapasowej w usłudze Azure Storage.

### <a name="azure-rbac-roles-to-manage-long-term-retention"></a>Role RBAC platformy Azure do zarządzania długoterminowym przechowywaniem

W przypadku funkcji **Get-AzSqlInstanceDatabaseLongTermRetentionBackup** i **Restore-AzSqlInstanceDatabase** konieczne będzie posiadanie jednej z następujących ról:

- Rola właściciela subskrypcji lub
- Rola współautor wystąpienia zarządzanego lub
- Rola niestandardowa z następującymi uprawnieniami:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

W przypadku **usunięcia-AzSqlInstanceDatabaseLongTermRetentionBackup** należy mieć jedną z następujących ról:

- Rola właściciela subskrypcji lub
- Rola niestandardowa z następującymi uprawnieniami:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> Rola współautor wystąpienia zarządzanego nie ma uprawnień do usuwania kopii zapasowych LTR.

Uprawnienia kontroli RBAC platformy Azure można udzielić w zakresie *subskrypcji* lub *grupy zasobów* . Aby jednak uzyskać dostęp do kopii zapasowych LTR należących do porzuconego wystąpienia, należy przyznać uprawnienie w zakresie *subskrypcji* tego wystąpienia.

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

### <a name="create-an-ltr-policy"></a>Tworzenie zasad odltr

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount

Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W' 
    YearlyRetention = 'P5Y' 
    WeekOfYear = '16'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-policies"></a>Wyświetl zasady LTR

Ten przykład pokazuje, jak wyświetlić listę zasad LTR w ramach wystąpienia dla pojedynczej bazy danych

```powershell
# gets the current version of LTR policy for a database
$LTRPolicies = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup
}
Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy 
```

Ten przykład pokazuje, jak wyświetlić listę zasad LTR dla wszystkich baz danych w wystąpieniu

```powershell
# gets the current version of LTR policy for all of the databases on an instance

$Databases = Get-AzSqlInstanceDatabase -ResourceGroupName $resourceGroup -InstanceName $instanceName

$LTRParams = @{
    InstanceName = $instanceName
    ResourceGroupName = $resourceGroup
}

foreach($database in $Databases.Name){
    Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRParams  -DatabaseName $database
 }
```

### <a name="clear-an-ltr-policy"></a>Wyczyść zasady odltr

Ten przykład pokazuje, jak wyczyścić zasady LTR z bazy danych

```powershell
# remove the LTR policy from a database
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    RemovePolicy = $true
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-backups"></a>Wyświetl kopie zapasowe LTR

Ten przykład pokazuje, jak wyświetlić listę kopii zapasowych LTR w ramach wystąpienia.

```powershell

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$LTRBackupParam = @{
    Location = $instance.Location 
    DatabaseState = 'Live'
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# only list the latest LTR backup for each database
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    OnlyLatestPerDatabase = $true
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 
```

### <a name="delete-ltr-backups"></a>Usuwanie kopii zapasowych LTR

Ten przykład pokazuje, jak usunąć kopię zapasową LTR z listy kopii zapasowych.

```powershell
# remove the earliest backup
# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Usuwanie kopii zapasowej LTR jest nieodwracalne. Aby usunąć kopię zapasową LTR po usunięciu wystąpienia, musisz mieć uprawnienia do zakresu subskrypcji. Można skonfigurować powiadomienia dotyczące każdego usunięcia w Azure Monitor przez filtrowanie operacji "usuwa długoterminową kopię zapasową przechowywania". Dziennik aktywności zawiera informacje o tym, kto i kiedy żądanie zostało zgłoszone. Szczegółowe instrukcje znajdują się w temacie [tworzenie alertów dziennika aktywności](../../azure-monitor/alerts/alerts-activity-log.md) .

### <a name="restore-from-ltr-backups"></a>Przywracanie z kopii zapasowych LTR

Ten przykład pokazuje, jak przywrócić z kopii zapasowej LTR. Zwróć uwagę, że ten interfejs nie został zmieniony, ale parametr identyfikatora zasobu wymaga teraz identyfikatora zasobu kopii zapasowej LTR.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbname
    OnlyLatestPerDatabase = $true
}
$ltrBackup = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 

$RestoreLTRParam = @{
    TargetInstanceName          = $instanceName 
    TargetResourceGroupName     = $resourceGroup 
    TargetInstanceDatabaseName  = $dbName
    FromLongTermRetentionBackup = $true
    ResourceId                  = $ltrBackup.ResourceId 
}
Restore-AzSqlInstanceDatabase @RestoreLTRParam
```

> [!IMPORTANT]
> Aby przywrócić dane z kopii zapasowej LTR po usunięciu wystąpienia, musisz mieć uprawnienia do zakresu subskrypcji wystąpienia, a subskrypcja musi być aktywna. Należy również pominąć opcjonalny parametr-ResourceGroupName.

> [!NOTE]
> W tym miejscu możesz połączyć się z przywróconą bazą danych przy użyciu programu SQL Server Management Studio, aby wykonać niezbędne zadania, takie jak wyodrębnienie bitu danych z przywróconej bazy danych w celu skopiowania do istniejącej bazy danych lub usunięcie istniejącej bazę danych i zmiana nazwy przywróconej bazy danych na istniejącą nazwę bazy danych. Zobacz [przywracanie do punktu w czasie](../database/recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o automatycznych kopiach zapasowych generowanych przez usługi, zobacz artykuł dotyczący [automatycznych kopii zapasowych](../database/automated-backups-overview.md)
- Aby dowiedzieć się więcej o długoterminowym przechowywaniu kopii zapasowych, zobacz temat dotyczący [długoterminowego przechowywania kopii zapasowych](../database/long-term-retention-overview.md)
