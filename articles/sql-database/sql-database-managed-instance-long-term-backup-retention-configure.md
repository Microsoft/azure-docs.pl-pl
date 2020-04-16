---
title: 'Wystąpienie zarządzane: długoterminowe przechowywanie kopii zapasowych (PowerShell)'
description: Dowiedz się, jak przechowywać i przywracać automatyczne kopie zapasowe w oddzielnych kontenerach magazynu obiektów Blob platformy Azure dla wystąpienia zarządzanego usługi Azure SQL Database przy użyciu programu PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/14/2020
ms.openlocfilehash: c9edbbf54696a817d0495f6890e0d796e482231f
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393714"
---
# <a name="manage-azure-sql-database-managed-instance-long-term-backup-retention-powershell"></a>Zarządzanie długotrwałym przechowywaniem kopii zapasowych w przypadku wystąpienia zarządzanego przez usługę Azure SQL Database (PowerShell)

W wystąpieniu zarządzanym usługi Azure SQL Database można skonfigurować długoterminowe zasady [przechowywania kopii zapasowych](sql-database-long-term-retention.md#managed-instance-support) (LTR) jako ograniczoną publiczną funkcję w wersji zapoznawczej. Dzięki temu można automatycznie przechowywać kopie zapasowe bazy danych w oddzielnych kontenerach magazynu obiektów Blob platformy Azure przez okres do 10 lat. Następnie można odzyskać bazę danych przy użyciu tych kopii zapasowych za pomocą programu PowerShell.

   > [!IMPORTANT]
   > LTR dla wystąpień zarządzanych jest obecnie w ograniczonej wersji zapoznawczej i dostępne dla subskrypcji EA i CSP w zależności od przypadku. Aby zażądać rejestracji, utwórz [bilet pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/) w temacie Pomocy technicznej Kopia **zapasowa, Przywracanie i ciągłość działania/Długoterminowe przechowywanie kopii zapasowych.** 


W poniższych sekcjach pokazano, jak używać programu PowerShell do konfigurowania długoterminowego przechowywania kopii zapasowych, wyświetlania kopii zapasowych w magazynie SQL platformy Azure i przywracania z kopii zapasowej w magazynie SQL platformy Azure.

## <a name="rbac-roles-to-manage-long-term-retention"></a>RBAC role do zarządzania długoterminową retencję

Dla **Get-AzSqlInstanceDatabaseLongTermRetentionBackup** i **Restore-AzSqlInstanceDatabase**, trzeba będzie mieć jedną z następujących ról:

- Rola właściciela subskrypcji lub
- Rola współautora wystąpienia zarządzanego lub
- Rola niestandardowa z następującymi uprawnieniami:

   ```Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read``` ```Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read```
   ```Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read```

Dla **Remove-AzSqlInstanceDatabaseLongTermRetentionBackup**, trzeba będzie mieć jedną z następujących ról:

- Rola właściciela subskrypcji lub
- Rola niestandardowa z następującym uprawnieniem:

   ```Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete```

> [!NOTE]
> Rola Współautora wystąpienia zarządzanego nie ma uprawnień do usuwania kopii zapasowych LTR.

Uprawnienia RBAC mogą być przyznawane w zakresie *subskrypcji* lub *grupy zasobów.* Jednak aby uzyskać dostęp do kopii zapasowych LTR, które należą do usuniętego wystąpienia, uprawnienie musi zostać udzielone w zakresie *subskrypcji* tego wystąpienia.

```Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete```

## <a name="create-an-ltr-policy"></a>Tworzenie zasad LTR

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
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16

```

## <a name="view-ltr-policies"></a>Wyświetlanie zasad LTR

W tym przykładzie pokazano, jak wyświetlić listę zasad LTR w wystąpieniu

```powershell
# gets the current version of LTR policy for the database
$ltrPolicies = Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup

```

## <a name="clear-an-ltr-policy"></a>Czyszczenie zasad LTR

W tym przykładzie pokazano, jak wyczyścić zasady LTR z bazy danych

```powershell
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

## <a name="view-ltr-backups"></a>Wyświetlanie kopii zapasowych LTR

W tym przykładzie pokazano, jak wyświetlić listę kopii zapasowych LTR w wystąpieniu.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName

# get the LTR backups for a specific database from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -OnlyLatestPerDatabase

```

## <a name="delete-ltr-backups"></a>Usuwanie kopii zapasowych LTR

W tym przykładzie pokazano, jak usunąć kopię zapasową LTR z listy kopii zapasowych.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Usunięcie kopii zapasowej LTR nie jest odwracalne. Aby usunąć kopię zapasową LTR po usunięciu wystąpienia, musisz mieć uprawnienie zakres subskrypcji. Powiadomienia dotyczące każdego usuwania można skonfigurować w usłudze Azure Monitor, filtrując pod kątem operacji "Usuwa długoterminową kopię zapasową przechowywania". Dziennik aktywności zawiera informacje o tym, kto i kiedy złożył żądanie. Zobacz [Tworzenie alertów dziennika aktywności, aby](../azure-monitor/platform/alerts-activity-log.md) uzyskać szczegółowe instrukcje.

## <a name="restore-from-ltr-backups"></a>Przywracanie z kopii zapasowych LTR

W tym przykładzie pokazano, jak przywrócić z kopii zapasowej LTR. Uwaga: ten interfejs nie zmienił się, ale parametr identyfikatora zasobu wymaga teraz identyfikatora zasobu kopii zapasowej LTR.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
Restore-AzSqlInstanceDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId `
   -TargetInstanceName $instanceName -TargetResourceGroupName $resourceGroup -TargetInstanceDatabaseName $dbName

```

> [!IMPORTANT]
> Aby przywrócić z kopii zapasowej LTR po usunięciu wystąpienia, musisz mieć uprawnienia o zakresie subskrypcji wystąpienia i że subskrypcja musi być aktywna. Należy również pominąć opcjonalny parametr -ResourceGroupName.

> [!NOTE]
> W tym miejscu możesz połączyć się z przywróconą bazą danych przy użyciu programu SQL Server Management Studio, aby wykonać niezbędne zadania, takie jak wyodrębnienie bitu danych z przywróconej bazy danych w celu skopiowania do istniejącej bazy danych lub usunięcie istniejącej bazę danych i zmiana nazwy przywróconej bazy danych na istniejącą nazwę bazy danych. Zobacz [przywracanie punktu w czasie](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o automatycznych kopiach zapasowych generowanych przez usługi, zobacz artykuł dotyczący [automatycznych kopii zapasowych](sql-database-automated-backups.md)
- Aby dowiedzieć się więcej o długoterminowym przechowywaniu kopii zapasowych, zobacz temat dotyczący [długoterminowego przechowywania kopii zapasowych](sql-database-long-term-retention.md)
