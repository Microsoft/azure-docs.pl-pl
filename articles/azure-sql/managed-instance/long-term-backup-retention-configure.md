---
title: 'Wystąpienie zarządzane Azure SQL: długoterminowe przechowywanie kopii zapasowych (PowerShell)'
description: Dowiedz się, jak przechowywać i przywracać automatyczne kopie zapasowe w oddzielnych kontenerach usługi Azure Blob Storage dla wystąpienia zarządzanego Azure SQL przy użyciu programu PowerShell.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 04/29/2020
ms.openlocfilehash: 4249e7df61c7f1f090b6377114bcab6ce1d40b2a
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619087"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>Zarządzanie długoterminowym przechowywaniem kopii zapasowych wystąpienia zarządzanego usługi Azure SQL (program PowerShell)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

W wystąpieniu zarządzanym usługi Azure SQL można skonfigurować [długoterminowe zasady przechowywania kopii zapasowych](../database/long-term-retention-overview.md#sql-managed-instance-support) (LTR) jako ograniczoną publiczną funkcję w wersji zapoznawczej. Pozwala to na automatyczne zachowywanie kopii zapasowych bazy danych w oddzielnych kontenerach usługi Azure Blob Storage przez maksymalnie 10 lat. Następnie można odzyskać bazę danych przy użyciu tych kopii zapasowych za pomocą programu PowerShell.

   > [!IMPORTANT]
   > Wartość LTR dla wystąpień zarządzanych jest obecnie w ograniczonej wersji zapoznawczej i jest dostępna dla subskrypcji umów EA i CSP w przypadku wystąpienia w zależności od wielkości liter. Aby zażądać rejestracji, Utwórz [bilet pomocy technicznej systemu Azure](https://azure.microsoft.com/support/create-ticket/). W polu Typ problemu wybierz pozycję problem techniczny, dla opcji usługa wybierz SQL Database wystąpienie zarządzane, a w polu Typ problemu wybierz pozycję **kopia zapasowa, przywracanie i ciągłość działania oraz przechowywanie długoterminowych kopii zapasowych**. W żądaniu Określ, czy chcesz zarejestrować się w ograniczonej publicznej wersji zapoznawczej LTR dla wystąpienia zarządzanego.

W poniższych sekcjach pokazano, jak używać programu PowerShell do konfigurowania długoterminowego przechowywania kopii zapasowych, wyświetlania kopii zapasowych w usłudze Azure SQL Storage oraz przywracania danych z kopii zapasowej w usłudze Azure SQL Storage.

## <a name="azure-roles-to-manage-long-term-retention"></a>Role platformy Azure do zarządzania długoterminowym przechowywaniem

W przypadku funkcji **Get-AzSqlInstanceDatabaseLongTermRetentionBackup** i **Restore-AzSqlInstanceDatabase**konieczne będzie posiadanie jednej z następujących ról:

- Rola właściciela subskrypcji lub
- Rola współautor wystąpienia zarządzanego lub
- Rola niestandardowa z następującymi uprawnieniami:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

W przypadku **usunięcia-AzSqlInstanceDatabaseLongTermRetentionBackup**należy mieć jedną z następujących ról:

- Rola właściciela subskrypcji lub
- Rola niestandardowa z następującymi uprawnieniami:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> Rola współautor wystąpienia zarządzanego nie ma uprawnień do usuwania kopii zapasowych LTR.

Uprawnienia RBAC można udzielić w zakresie *subskrypcji* lub *grupy zasobów* . Aby jednak uzyskać dostęp do kopii zapasowych LTR należących do porzuconego wystąpienia, należy przyznać uprawnienie w zakresie *subskrypcji* tego wystąpienia.

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

## <a name="create-an-ltr-policy"></a>Tworzenie zasad odltr

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

## <a name="view-ltr-policies"></a>Wyświetl zasady LTR

Ten przykład pokazuje, jak wyświetlić listę zasad LTR w wystąpieniu

```powershell
# gets the current version of LTR policy for the database
$ltrPolicies = Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup
```

## <a name="clear-an-ltr-policy"></a>Wyczyść zasady odltr

Ten przykład pokazuje, jak wyczyścić zasady LTR z bazy danych

```powershell
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

## <a name="view-ltr-backups"></a>Wyświetl kopie zapasowe LTR

Ten przykład pokazuje, jak wyświetlić listę kopii zapasowych LTR w ramach wystąpienia.

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

Ten przykład pokazuje, jak usunąć kopię zapasową LTR z listy kopii zapasowych.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Usuwanie kopii zapasowej LTR jest nieodwracalne. Aby usunąć kopię zapasową LTR po usunięciu wystąpienia, musisz mieć uprawnienia do zakresu subskrypcji. Można skonfigurować powiadomienia dotyczące każdego usunięcia w Azure Monitor przez filtrowanie operacji "usuwa długoterminową kopię zapasową przechowywania". Dziennik aktywności zawiera informacje o tym, kto i kiedy żądanie zostało zgłoszone. Szczegółowe instrukcje znajdują się w temacie [tworzenie alertów dziennika aktywności](../../azure-monitor/platform/alerts-activity-log.md) .

## <a name="restore-from-ltr-backups"></a>Przywracanie z kopii zapasowych LTR

Ten przykład pokazuje, jak przywrócić z kopii zapasowej LTR. Zwróć uwagę, że ten interfejs nie został zmieniony, ale parametr identyfikatora zasobu wymaga teraz identyfikatora zasobu kopii zapasowej LTR.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
Restore-AzSqlInstanceDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId `
   -TargetInstanceName $instanceName -TargetResourceGroupName $resourceGroup -TargetInstanceDatabaseName $dbName
```

> [!IMPORTANT]
> Aby przywrócić dane z kopii zapasowej LTR po usunięciu wystąpienia, musisz mieć uprawnienia do zakresu subskrypcji wystąpienia, a subskrypcja musi być aktywna. Należy również pominąć opcjonalny parametr-ResourceGroupName.

> [!NOTE]
> W tym miejscu możesz połączyć się z przywróconą bazą danych przy użyciu programu SQL Server Management Studio, aby wykonać niezbędne zadania, takie jak wyodrębnienie bitu danych z przywróconej bazy danych w celu skopiowania do istniejącej bazy danych lub usunięcie istniejącej bazę danych i zmiana nazwy przywróconej bazy danych na istniejącą nazwę bazy danych. Zobacz [przywracanie do punktu w czasie](../database/recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o automatycznych kopiach zapasowych generowanych przez usługi, zobacz artykuł dotyczący [automatycznych kopii zapasowych](../database/automated-backups-overview.md)
- Aby dowiedzieć się więcej o długoterminowym przechowywaniu kopii zapasowych, zobacz temat dotyczący [długoterminowego przechowywania kopii zapasowych](../database/long-term-retention-overview.md)
