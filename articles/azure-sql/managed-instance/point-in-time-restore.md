---
title: Przywracanie do punktu w czasie (kopie)
titleSuffix: Azure SQL Managed Instance
description: Przywracanie bazy danych w wystąpieniu zarządzanym Azure SQL Server do wcześniejszego punktu w czasie.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 0a56cfc147d4fb5cbdccf13363ad28bc602d8216
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182761"
---
# <a name="restore-a-database-in-azure-sql-managed-instance-to-a-previous-point-in-time"></a>Przywracanie bazy danych w wystąpieniu zarządzanym Azure SQL do wcześniejszego punktu w czasie
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Użyj funkcji przywracania do określonego momentu (kopie), aby utworzyć bazę danych jako kopię innej bazy danych od pewnego czasu w przeszłości. W tym artykule opisano, jak wykonać przywracanie do punktu w czasie dla bazy danych w wystąpieniu zarządzanym usługi Azure SQL.

Przywracanie do punktu w czasie jest przydatne w scenariuszach odzyskiwania, takich jak zdarzenia spowodowane błędami, nieprawidłowo załadowane dane lub usuwanie kluczowych danych. Można go również użyć po prostu do testowania lub inspekcji. Pliki kopii zapasowej są przechowywane przez 7 – 35 dni, w zależności od ustawień bazy danych.

Przywracanie do punktu w czasie umożliwia przywrócenie bazy danych:

- z istniejącej bazy danych.
- z usuniętej bazy danych.
- z tym samym wystąpieniem zarządzanym SQL lub innym wystąpieniem zarządzanym SQL. 

## <a name="limitations"></a>Ograniczenia

Przywracanie do punktu w czasie do wystąpienia zarządzanego SQL ma następujące ograniczenia:

- Gdy przywracasz z jednego wystąpienia wystąpienia zarządzanego SQL na inny, oba wystąpienia muszą znajdować się w tej samej subskrypcji i regionie. Przywracanie między regionami i subskrypcjami nie jest obecnie obsługiwane.
- Przywracanie do punktu w czasie całego wystąpienia zarządzanego SQL nie jest możliwe. W tym artykule wyjaśniono, co jest możliwe: Przywracanie do punktu w czasie dla bazy danych hostowanej w wystąpieniu zarządzanym SQL.

> [!WARNING]
> Zapoznaj się z rozmiarem magazynu zarządzanego wystąpienia SQL. W zależności od rozmiaru danych do przywrócenia można wypróbować magazyn z wystąpieniami. Jeśli nie ma wystarczającej ilości miejsca na przywrócone dane, użyj innego podejścia.

W poniższej tabeli przedstawiono scenariusze przywracania do punktu w czasie dla wystąpienia zarządzanego SQL:

|           |Przywróć istniejącą bazę danych do tego samego wystąpienia wystąpienia zarządzanego SQL| Przywracanie istniejącej bazy danych do innego wystąpienia zarządzanego SQL|Przywracanie usuniętej bazy danych do tego samego wystąpienia zarządzanego SQL|Przywracanie usuniętej bazy danych do innego wystąpienia zarządzanego SQL|
|:----------|:----------|:----------|:----------|:----------|
|**Witryna Azure Portal**| Tak|Nie |Tak|Nie|
|**Interfejs wiersza polecenia platformy Azure**|Tak |Tak |Nie|Nie|
|**Program PowerShell**| Tak|Tak |Tak|Tak|

## <a name="restore-an-existing-database"></a>Przywracanie istniejącej bazy danych

Przywróć istniejącą bazę danych do tego samego wystąpienia zarządzanego SQL przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby przywrócić bazę danych do innego wystąpienia zarządzanego SQL, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby można było określić właściwości docelowego wystąpienia zarządzanego SQL i grupy zasobów. Jeśli nie określisz tych parametrów, baza danych zostanie domyślnie przywrócona do istniejącego wystąpienia zarządzanego SQL. Azure Portal nie obsługuje obecnie przywracania do innego wystąpienia zarządzanego SQL.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Przejdź do wystąpienia zarządzanego SQL i wybierz bazę danych, którą chcesz przywrócić.
3. Na stronie Baza danych wybierz pozycję **Przywróć** :

    ![Przywracanie bazy danych przy użyciu Azure Portal](./media/point-in-time-restore/restore-database-to-mi.png)

4. Na stronie **przywracanie** wybierz punkt dla daty i godziny, do której chcesz przywrócić bazę danych.
5. Wybierz pozycję **Potwierdź** , aby przywrócić bazę danych. Ta akcja uruchamia proces przywracania, który tworzy nową bazę danych i wypełnia ją danymi z oryginalnej bazy danych w określonym momencie. Aby uzyskać więcej informacji na temat procesu odzyskiwania, zobacz [czas odzyskiwania](../database/recovery-using-backups.md#recovery-time).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Jeśli nie masz jeszcze zainstalowanego Azure PowerShell, zobacz [Install the Azure PowerShell module](/powershell/azure/install-az-ps).

Aby przywrócić bazę danych przy użyciu programu PowerShell, określ wartości parametrów w poniższym poleceniu. Następnie uruchom polecenie:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

Aby przywrócić bazę danych do innego wystąpienia zarządzanego SQL, należy również określić nazwy docelowej grupy zasobów i docelowego wystąpienia zarządzanego SQL:  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Aby uzyskać szczegółowe informacje, zobacz [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Jeśli nie masz jeszcze zainstalowanego interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Aby przywrócić bazę danych przy użyciu interfejsu wiersza polecenia platformy Azure, określ wartości parametrów w następującym poleceniu. Następnie uruchom polecenie:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Aby przywrócić bazę danych do innego wystąpienia zarządzanego SQL, należy również określić nazwy docelowej grupy zasobów i wystąpienia zarządzanego SQL:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Szczegółowe wyjaśnienie dostępnych parametrów można znaleźć w [dokumentacji interfejsu wiersza polecenia w celu przywrócenia bazy danych w wystąpieniu zarządzanym SQL](/cli/azure/sql/midb#az-sql-midb-restore).

---

## <a name="restore-a-deleted-database"></a>Przywracanie usuniętej bazy danych

Przywracanie usuniętej bazy danych można wykonać przy użyciu programu PowerShell lub Azure Portal. Aby przywrócić usuniętą bazę danych do tego samego wystąpienia, użyj Azure Portal lub programu PowerShell. Aby przywrócić usuniętą bazę danych do innego wystąpienia, użyj programu PowerShell. 

### <a name="portal"></a>Portal 


Aby odzyskać zarządzaną bazę danych przy użyciu Azure Portal, Otwórz stronę Omówienie wystąpienia zarządzanego SQL i wybierz pozycję **usunięte bazy danych**. Wybierz usuniętą bazę danych, którą chcesz przywrócić, a następnie wpisz nazwę nowej bazy danych, która zostanie utworzona z przywróconymi danymi z kopii zapasowej.

  ![Zrzut ekranu przedstawiający przywracanie usuniętej bazy danych wystąpienia usługi Azure SQL](./media/point-in-time-restore/restore-deleted-sql-managed-instance-annotated.png)

.. /.. /sql-database

### <a name="powershell"></a>PowerShell

Aby przywrócić bazę danych do tego samego wystąpienia, zaktualizuj wartości parametrów, a następnie uruchom następujące polecenie programu PowerShell: 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -FromPointInTimeBackup -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

Aby przywrócić bazę danych do innego wystąpienia zarządzanego SQL, należy również określić nazwy docelowej grupy zasobów i docelowego wystąpienia zarządzanego SQL:

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlinstanceDatabase -FromPointInTimeBackup -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>Zastąp istniejącą bazę danych

Aby zastąpić istniejącą bazę danych, musisz:

1. Usuń istniejącą bazę danych, która ma zostać zastąpiona.
2. Zmień nazwę bazy danych "punkt w czasie" na nazwę usuniętej bazy danych.

### <a name="drop-the-original-database"></a>Usuń oryginalną bazę danych

Bazę danych można usunąć przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Możesz również porzucić bazę danych, łącząc się bezpośrednio z wystąpieniem zarządzanym SQL, rozpoczynając SQL Server Management Studio (SSMS), a następnie uruchamiając następujące polecenie Transact-SQL (T-SQL):

```sql
DROP DATABASE WorldWideImporters;
```

Użyj jednej z następujących metod, aby nawiązać połączenie z bazą danych w wystąpieniu zarządzanym SQL:

- [Narzędzie SSMS/Azure Data Studio za pośrednictwem maszyny wirtualnej platformy Azure](./connect-vm-instance-configure.md)
- [Punkt-lokacja](./point-to-site-p2s-configure.md)
- [Publiczny punkt końcowy](./public-endpoint-configure.md)

# <a name="portal"></a>[Portal](#tab/azure-portal)

W Azure Portal wybierz bazę danych z wystąpienia zarządzanego SQL, a następnie wybierz pozycję **Usuń**.

   ![Usuwanie bazy danych przy użyciu Azure Portal](./media/point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Użyj następującego polecenia programu PowerShell, aby usunąć istniejącą bazę danych z wystąpienia zarządzanego SQL:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj następującego polecenia platformy Azure CLI, aby usunąć istniejącą bazę danych z wystąpienia zarządzanego SQL:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Zmień nazwę nowej bazy danych tak, aby była zgodna z oryginalną nazwą bazy danych

Połącz się bezpośrednio z wystąpieniem zarządzanym SQL i uruchom SQL Server Management Studio. Następnie uruchom następujące zapytanie Transact-SQL (T-SQL). Zapytanie zmieni nazwę przywróconej bazy danych na wartość porzuconej bazy danych, która ma zostać zastąpiona.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Użyj jednej z następujących metod, aby nawiązać połączenie z bazą danych w wystąpieniu zarządzanym SQL:

- [Maszyna wirtualna platformy Azure](./connect-vm-instance-configure.md)
- [Punkt-lokacja](./point-to-site-p2s-configure.md)
- [Publiczny punkt końcowy](./public-endpoint-configure.md)

## <a name="next-steps"></a>Następne kroki

Uzyskaj informacje na temat [zautomatyzowanych kopii zapasowych](../database/automated-backups-overview.md).
