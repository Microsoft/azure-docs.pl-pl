---
title: Przywracanie do punktu w czasie (PITR)
titleSuffix: Azure SQL Managed Instance
description: Przywróć bazę danych Azure SQL Managed Instance do wcześniejszego punktu w czasie.
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
ms.openlocfilehash: 4c116b378c72d87641157fc453d65e46be9f43ec
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787171"
---
# <a name="restore-a-database-in-azure-sql-managed-instance-to-a-previous-point-in-time"></a>Przywracanie bazy danych w Azure SQL Managed Instance do wcześniejszego punktu w czasie
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Użyj przywracania do punktu w czasie (PITR, point-in-time restore), aby utworzyć bazę danych jako kopię innej bazy danych z przeszłości. W tym artykule opisano sposób przywracania bazy danych do punktu w czasie w Azure SQL Managed Instance.

Przywracanie do punktu w czasie jest przydatne w scenariuszach odzyskiwania, takich jak zdarzenia spowodowane błędami, niepoprawnie załadowane dane lub usuwanie kluczowych danych. Można go również użyć po prostu do testowania lub inspekcji. Pliki kopii zapasowej są przechowywane przez 7–35 dni, w zależności od ustawień bazy danych.

Przywracanie do punktu w czasie może przywrócić bazę danych:

- z istniejącej bazy danych.
- z usuniętej bazy danych.
- do tego samego SQL Managed Instance lub do innej SQL Managed Instance. 

## <a name="limitations"></a>Ograniczenia

Przywracanie do punktu w czasie do SQL Managed Instance ma następujące ograniczenia:

- W przypadku przywracania z jednego wystąpienia usługi SQL Managed Instance do innego oba wystąpienia muszą znajdować się w tej samej subskrypcji i regionie. Przywracanie między regionami i subskrypcjami nie jest obecnie obsługiwane.
- Przywracanie do punktu w czasie całej SQL Managed Instance jest niemożliwe. W tym artykule wyjaśniono tylko, co jest możliwe: przywracanie do punktu w czasie bazy danych hostowanej w SQL Managed Instance.

> [!WARNING]
> Należy pamiętać o rozmiarze magazynu SQL Managed Instance. W zależności od rozmiaru danych do przywrócenia może zabraknie miejsca w magazynie wystąpienia. Jeśli nie ma wystarczającej ilości miejsca dla przywróconych danych, użyj innego podejścia.

W poniższej tabeli przedstawiono scenariusze przywracania do punktu w czasie dla SQL Managed Instance:

|           |Przywracanie istniejącej bazy danych do tego samego wystąpienia SQL Managed Instance| Przywracanie istniejącej bazy danych do innej SQL Managed Instance|Przywracanie usuniętej bazy danych do tej samej SQL Managed Instance|Przywracanie usuniętej bazy danych do innej SQL Managed Instance|
|:----------|:----------|:----------|:----------|:----------|
|**Witryna Azure Portal**| Tak|Nie |Tak|Nie|
|**Interfejs wiersza polecenia platformy Azure**|Tak |Tak |Nie|Nie|
|**Program PowerShell**| Tak|Tak |Tak|Tak|

## <a name="restore-an-existing-database"></a>Przywracanie istniejącej bazy danych

Przywróć istniejącą bazę danych do tej samej SQL Managed Instance przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby przywrócić bazę danych do innej SQL Managed Instance, użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby określić właściwości dla docelowej SQL Managed Instance i grupy zasobów. Jeśli te parametry nie zostaną określone, baza danych zostanie domyślnie przywrócona do istniejącej SQL Managed Instance danych. Azure Portal obecnie nie obsługuje przywracania do innej SQL Managed Instance.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Przejdź do swojego SQL Managed Instance i wybierz bazę danych, którą chcesz przywrócić.
3. Wybierz **pozycję Przywróć** na stronie bazy danych:

    ![Przywracanie bazy danych przy użyciu Azure Portal](./media/point-in-time-restore/restore-database-to-mi.png)

4. Na **stronie Przywracanie** wybierz punkt dla daty i czasu, do którego chcesz przywrócić bazę danych.
5. Wybierz **pozycję Potwierdź,** aby przywrócić bazę danych. Ta akcja rozpoczyna proces przywracania, który tworzy nową bazę danych i wypełnia ją danymi z oryginalnej bazy danych w określonym punkcie w czasie. Aby uzyskać więcej informacji na temat procesu odzyskiwania, zobacz [Czas odzyskiwania](../database/recovery-using-backups.md#recovery-time).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Jeśli nie masz jeszcze zainstalowanego Azure PowerShell, zobacz [Instalowanie Azure PowerShell modułu](/powershell/azure/install-az-ps).

Aby przywrócić bazę danych przy użyciu programu PowerShell, określ wartości parametrów w poniższym poleceniu. Następnie uruchom polecenie :

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

Aby przywrócić bazę danych do SQL Managed Instance, określ również nazwy docelowej grupy zasobów i grupy SQL Managed Instance:  

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

Aby uzyskać szczegółowe informacje, [zobacz Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Jeśli nie masz jeszcze zainstalowanego interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

Aby przywrócić bazę danych przy użyciu interfejsu wiersza polecenia platformy Azure, określ wartości parametrów w poniższym poleceniu. Następnie uruchom polecenie :

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Aby przywrócić bazę danych do innej SQL Managed Instance, określ również nazwy docelowej grupy zasobów i SQL Managed Instance:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Aby uzyskać szczegółowy opis dostępnych parametrów, zobacz dokumentację interfejsu wiersza polecenia dotyczącą przywracania bazy danych w [SQL Managed Instance](/cli/azure/sql/midb#az_sql_midb_restore).

---

## <a name="restore-a-deleted-database"></a>Przywracanie usuniętej bazy danych

Przywracanie usuniętej bazy danych można wykonać przy użyciu programu PowerShell lub Azure Portal. Aby przywrócić usuniętą bazę danych do tego samego wystąpienia, użyj polecenia Azure Portal programu PowerShell. Aby przywrócić usuniętą bazę danych do innego wystąpienia, użyj programu PowerShell. 

### <a name="portal"></a>Portal 


Aby odzyskać zarządzaną bazę danych przy użyciu Azure Portal, otwórz stronę przeglądu SQL Managed Instance i wybierz pozycję **Usunięte bazy danych.** Wybierz usuniętą bazę danych, którą chcesz przywrócić, i wpisz nazwę nowej bazy danych, która zostanie utworzona przy użyciu danych przywróconych z kopii zapasowej.

  ![Zrzut ekranu przedstawiający przywracanie usuniętej Azure SQL bazy danych wystąpienia](./media/point-in-time-restore/restore-deleted-sql-managed-instance-annotated.png)

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

Aby przywrócić bazę danych do SQL Managed Instance, określ również nazwy docelowej grupy zasobów i grupy SQL Managed Instance:

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

## <a name="overwrite-an-existing-database"></a>Zastępowanie istniejącej bazy danych

Aby zastąpić istniejącą bazę danych, należy:

1. Upuść istniejącą bazę danych, którą chcesz zastąpić.
2. Zmień nazwę przywróconej bazy danych do punktu w czasie na nazwę usuniętej bazy danych.

### <a name="drop-the-original-database"></a>Upuść oryginalną bazę danych

Bazę danych można usunąć przy użyciu interfejsu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Możesz również usunąć bazę danych, łącząc się bezpośrednio z SQL Managed Instance, uruchamiając program SQL Server Management Studio (SSMS), a następnie uruchamiając następujące polecenie Języka Transact-SQL (T-SQL):

```sql
DROP DATABASE WorldWideImporters;
```

Użyj jednej z następujących metod, aby nawiązać połączenie z bazą danych w SQL Managed Instance:

- [SSMS/Azure Data Studio za pośrednictwem maszyny wirtualnej platformy Azure](./connect-vm-instance-configure.md)
- [Punkt-lokacja](./point-to-site-p2s-configure.md)
- [Publiczny punkt końcowy](./public-endpoint-configure.md)

# <a name="portal"></a>[Portal](#tab/azure-portal)

W Azure Portal wybierz bazę danych z SQL Managed Instance, a następnie wybierz pozycję **Usuń**.

   ![Usuwanie bazy danych przy użyciu Azure Portal](./media/point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Użyj następującego polecenia programu PowerShell, aby usunąć istniejącą bazę danych z SQL Managed Instance:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj następującego polecenia interfejsu wiersza polecenia platformy Azure, aby usunąć istniejącą bazę danych z SQL Managed Instance:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Zmienianie nazwy nowej bazy danych w celu dopasowania jej do oryginalnej nazwy bazy danych

Połącz się bezpośrednio z SQL Managed Instance i uruchom SQL Server Management Studio. Następnie uruchom następujące zapytanie Języka Transact-SQL (T-SQL). Zapytanie spowoduje zmianę nazwy przywróconej bazy danych na nazwę usuniętej bazy danych, która ma zostać nadpisyowana.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Użyj jednej z następujących metod, aby nawiązać połączenie z bazą danych w SQL Managed Instance:

- [Maszyna wirtualna platformy Azure](./connect-vm-instance-configure.md)
- [Punkt-lokacja](./point-to-site-p2s-configure.md)
- [Publiczny punkt końcowy](./public-endpoint-configure.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [automatycznych kopiach zapasowych.](../database/automated-backups-overview.md)
