---
title: Przywracanie usuniętej dedykowanej puli SQL
description: Przewodnik dotyczący przywracania usuniętej puli SQL w usłudze Azure Synapse Analytics.
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
ms.openlocfilehash: 29edf6ebd451bf05fe24249eeacb416a70001d56
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313573"
---
# <a name="restore-a-deleted-dedicated-sql-pool-in-azure-synapse-analytics"></a>Przywracanie usuniętej dedykowanej puli SQL w usłudze Azure Synapse Analytics

W tym artykule dowiesz się, jak przywrócić dedykowaną pulę SQL przy użyciu Azure Portal lub programu PowerShell.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Sprawdź pojemność jednostek DTU.** Każda dedykowana Pula SQL jest hostowana przez [logiczny serwer SQL](../../azure-sql/database/logical-servers.md) (na przykład MyServer.Database.Windows.NET), który ma domyślny limit przydziału jednostek DTU.  Sprawdź, czy serwer ma wystarczającą liczbę pozostałych limitów przydziału jednostek DTU dla przywracanej bazy danych. Aby dowiedzieć się, jak obliczyć liczbę jednostek DTU potrzebnych lub aby zażądać większej liczby jednostek DTU, zobacz [żądanie zmiany limitu przydziału jednostek DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Przywracanie usuniętego magazynu danych za pomocą programu PowerShell

Aby przywrócić usuniętą dedykowaną pulę SQL, użyj polecenia cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Jeśli odpowiedni serwer został również usunięty, nie można przywrócić tego magazynu danych.

1. Przed rozpoczęciem upewnij się, że [zainstalowano Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Otwórz program PowerShell.
3. Połącz się z kontem platformy Azure i Wyświetl listę wszystkich subskrypcji skojarzonych z Twoim kontem.
4. Wybierz subskrypcję zawierającą usuniętą dedykowaną pulę SQL, która ma zostać przywrócona.
5. Pobierz konkretny usunięty magazyn danych.
6. Przywracanie usuniętej dedykowanej puli SQL
    1. Aby przywrócić usuniętą dedykowaną pulę SQL na innym serwerze, należy określić inną nazwę serwera.  Ten serwer może również znajdować się w innej grupie zasobów i regionie.
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

1. Zaloguj się do [portalu Azure](https://portal.azure.com/).
2. Przejdź do serwera, na którym jest hostowany usunięty magazyn danych.
3. Wybierz ikonę **usunięte bazy danych** w spisie treści.

    ![Usunięte bazy danych](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Wybierz usuniętą usługę Azure Synapse Analytics, którą chcesz przywrócić.

    ![Wybieranie pozycji Usunięte bazy danych](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Określ nową **nazwę bazy danych** i kliknij przycisk **OK** .

    ![Określ nazwę bazy danych](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Następne kroki

- [Przywracanie istniejącej dedykowanej puli SQL](sql-data-warehouse-restore-active-paused-dw.md)
- [Przywracanie z dedykowanej puli SQL z geograficzną kopią zapasową](sql-data-warehouse-restore-from-geo-backup.md)
