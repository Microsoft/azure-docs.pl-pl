---
title: Przywracanie usuniętej puli SQL
description: Przewodnik dotyczący przywracania usuniętej puli SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 879844efdc5c2b40f69ee5f79305d4dfa596fd27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89460732"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Przywracanie usuniętej puli SQL przy użyciu usługi Azure Synapse Analytics

Ten artykuł zawiera informacje na temat przywracania bazy danych SQL przy użyciu Azure Portal lub programu PowerShell.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Sprawdź pojemność jednostek DTU.** Każda pula SQL jest hostowana przez [logiczny serwer SQL](../../azure-sql/database/logical-servers.md) (na przykład MyServer.Database.Windows.NET), który ma domyślny limit przydziału jednostek DTU.  Sprawdź, czy serwer ma wystarczającą liczbę pozostałych limitów przydziału jednostek DTU dla przywracanej bazy danych. Aby dowiedzieć się, jak obliczyć liczbę jednostek DTU potrzebnych lub aby zażądać większej liczby jednostek DTU, zobacz [żądanie zmiany limitu przydziału jednostek DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Przywracanie usuniętego magazynu danych za pomocą programu PowerShell

Aby przywrócić usuniętą pulę SQL, użyj polecenia cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Jeśli odpowiedni serwer został również usunięty, nie można przywrócić tego magazynu danych.

1. Przed rozpoczęciem upewnij się, że [zainstalowano Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Otwórz program PowerShell.
3. Połącz się z kontem platformy Azure i Wyświetl listę wszystkich subskrypcji skojarzonych z Twoim kontem.
4. Wybierz subskrypcję zawierającą usuniętą pulę SQL, która ma zostać przywrócona.
5. Pobierz konkretny usunięty magazyn danych.
6. Przywracanie usuniętej puli SQL
    1. Aby przywrócić usuniętą pulę SQL na innym serwerze, należy określić inną nazwę serwera.  Ten serwer może również znajdować się w innej grupie zasobów i regionie.
    1. Aby przywrócić do innej subskrypcji, użyj przycisku [Przenieś](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) , aby przenieść serwer do innej subskrypcji.
7. Sprawdź, czy przywrócony magazyn danych jest w trybie online.
8. Po zakończeniu przywracania można skonfigurować odzyskany magazyn danych, wykonując następujące czynności: [Skonfiguruj bazę danych po odzyskaniu](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Przywracanie usuniętej bazy danych przy użyciu Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do serwera, na którym jest hostowany usunięty magazyn danych.
3. Wybierz ikonę **usunięte bazy danych** w spisie treści.

    ![Usunięte bazy danych](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Wybierz usuniętą usługę Azure Synapse Analytics, którą chcesz przywrócić.

    ![Wybieranie pozycji Usunięte bazy danych](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Określ nową **nazwę bazy danych** i kliknij przycisk **OK** .

    ![Określ nazwę bazy danych](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Następne kroki

- [Przywracanie istniejącej puli SQL](sql-data-warehouse-restore-active-paused-dw.md)
- [Przywracanie z puli SQL geograficznej kopii zapasowej](sql-data-warehouse-restore-from-geo-backup.md)
