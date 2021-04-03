---
title: Przywracanie istniejącej dedykowanej puli SQL (dawniej SQL DW)
description: Przewodnik dotyczący przywracania istniejącej dedykowanej puli SQL w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2ce552a13592c9d26ef70575f98b0b76ecc454ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97591995"
---
# <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Przywracanie istniejącej dedykowanej puli SQL (dawniej SQL DW)

Ten artykuł zawiera informacje na temat przywracania istniejącej dedykowanej puli SQL (dawniej SQL DW) przy użyciu Azure Portal i programu PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Sprawdź pojemność jednostek DTU.** Każda pula jest hostowana przez [logiczny serwer SQL](../../azure-sql/database/logical-servers.md) (na przykład MyServer.Database.Windows.NET), który ma domyślny limit przydziału jednostek DTU. Sprawdź, czy serwer ma wystarczającą liczbę pozostałych limitów przydziału jednostek DTU dla przywracanej bazy danych. Aby dowiedzieć się, jak obliczyć liczbę jednostek DTU potrzebnych lub aby zażądać większej liczby jednostek DTU, zobacz [żądanie zmiany limitu przydziału jednostek DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

1. Upewnij się, że [zainstalowano Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Mieć istniejący punkt przywracania, z którego chcesz przeprowadzić przywracanie. Jeśli chcesz utworzyć nowe przywracanie, zapoznaj się z [samouczkiem, aby utworzyć nowy punkt przywracania zdefiniowany przez użytkownika](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-powershell"></a>Przywracanie istniejącej dedykowanej puli SQL (dawniej SQL DW) przy użyciu programu PowerShell

Aby przywrócić istniejącą dedykowaną pulę SQL (dawniej SQL DW) z punktu przywracania, użyj polecenia cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) programu PowerShell.

1. Otwórz program PowerShell.

2. Połącz się z kontem platformy Azure i Wyświetl listę wszystkich subskrypcji skojarzonych z Twoim kontem.

3. Wybierz subskrypcję zawierającą bazę danych, która ma zostać przywrócona.

4. Wyświetl listę punktów przywracania dla dedykowanej puli SQL (dawniej SQL DW).

5. Wybierz żądany punkt przywracania za pomocą RestorePointCreationDate.

6. Przywróć dedykowaną pulę SQL (wcześniej SQL DW) do żądanego punktu przywracania za pomocą polecenia cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) programu PowerShell.

    1. Aby przywrócić dedykowaną pulę SQL (dawniej SQL DW) na innym serwerze, należy określić inną nazwę serwera.  Ten serwer może również znajdować się w innej grupie zasobów i regionie.
    2. Aby przywrócić do innej subskrypcji, użyj przycisku "Przenieś", aby przenieść serwer do innej subskrypcji.

7. Sprawdź, czy przywrócona dedykowana Pula SQL (wcześniej SQL DW) jest w trybie online.

8. Po zakończeniu przywracania można skonfigurować odzyskaną dedykowaną pulę SQL (dawniej SQL DW), wykonując następujące czynności: [Konfigurowanie bazy danych po odzyskaniu](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

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

# Or list all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different server
#$TargetResourceGroupName = $Database.ResourceGroupName # for restoring to different server in same resourcegroup 
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-the-azure-portal"></a>Przywracanie istniejącej dedykowanej puli SQL (dawniej SQL DW) za pomocą Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do dedykowanej, z której chcesz przywrócić.
3. W górnej części bloku przegląd wybierz pozycję **Przywróć**.

    ![ Omówienie przywracania kopii zapasowych](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Wybierz **punkty przywracania automatycznego** lub **punkty przywracania zdefiniowane przez użytkownika**. Jeśli dedykowana Pula SQL (wcześniej SQL DW) nie ma żadnych automatycznych punktów przywracania, poczekaj kilka godzin lub przed przywróceniem Utwórz punkt przywracania zdefiniowany przez użytkownika. W przypadku punktów przywracania User-Defined wybierz istniejącą lub Utwórz nową. W przypadku **serwera** można wybrać serwer w innej grupie zasobów i regionie lub utworzyć nowy. Po podania wszystkich parametrów kliknij przycisk **Przegląd + Przywróć**.

    ![Automatyczne punkty przywracania](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Następne kroki

- [Przywracanie usuniętej dedykowanej puli SQL (dawniej SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
- [Przywracanie z dedykowanej puli SQL z geograficzną kopią zapasową (dawniej SQL DW)](sql-data-warehouse-restore-from-geo-backup.md)
