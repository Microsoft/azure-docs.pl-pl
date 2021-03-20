---
title: Przywracanie dedykowanej puli SQL z geograficznej kopii zapasowej
description: Przewodnik dotyczący przywracania geograficznego dedykowanej puli SQL w usłudze Azure Synapse Analytics
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
ms.openlocfilehash: 4683bd84873506483209f4a0eb3751a1b163ed48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96449856"
---
# <a name="geo-restore-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Geograficzna przywracanie dedykowanej puli SQL w usłudze Azure Synapse Analytics

W tym artykule dowiesz się, jak przywrócić dedykowaną pulę SQL (dawniej SQL DW) z geograficznej kopii zapasowej za pośrednictwem Azure Portal i programu PowerShell.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Sprawdź pojemność jednostek DTU.** Każda dedykowana Pula SQL (wcześniej SQL DW) jest hostowana przez [logiczny serwer SQL](../../azure-sql/database/logical-servers.md) (na przykład MyServer.Database.Windows.NET), który ma domyślny limit przydziału jednostek DTU. Sprawdź, czy program SQL Server ma wystarczający limit przydziału jednostek DTU dla przywracanej bazy danych. Aby dowiedzieć się, jak obliczyć liczbę jednostek DTU potrzebnych lub aby zażądać większej liczby jednostek DTU, zobacz [żądanie zmiany limitu przydziału jednostek DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Przywracanie z regionu geograficznego platformy Azure za pomocą programu PowerShell

Aby przywrócić z geograficznej kopii zapasowej, należy użyć polecenia cmdlet [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) i [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

> [!NOTE]
> Można wykonać przywracanie geograficzne do Gen2! W tym celu należy określić Gen2 serviceobiektywname (np. wartości DW1000 **c**) jako opcjonalny parametr.
>

1. Przed rozpoczęciem upewnij się, że [zainstalowano Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Otwórz program PowerShell.
3. Połącz się z kontem platformy Azure i Wyświetl listę wszystkich subskrypcji skojarzonych z Twoim kontem.
4. Wybierz subskrypcję zawierającą magazyn danych, który ma zostać przywrócony.
5. Pobierz magazyn danych, który chcesz odzyskać.
6. Utwórz żądanie odzyskiwania dla hurtowni danych.
7. Sprawdź stan magazynu danych, który został przywrócony do lokalizacji geograficznej.
8. Aby skonfigurować magazyn danych po zakończeniu przywracania, zobacz [Konfigurowanie bazy danych po odzyskaniu]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

Odzyskana baza danych będzie TDE, jeśli źródłowa baza danych jest włączona.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Przywracanie z regionu geograficznego platformy Azure za pośrednictwem Azure Portal

Wykonaj kroki opisane poniżej, aby przywrócić dedykowaną pulę SQL (dawniej SQL DW) z kopii zapasowej geograficznej:

1. Zaloguj się do konta [Azure Portal](https://portal.azure.com/) .
1. Wyszukaj **dedykowane pule SQL (dawniej SQL DW)**.

   ![Nowy DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

1. Kliknij przycisk Dodaj i Wypełnij informacje wymagane na karcie **podstawowe** i kliknij przycisk **Dalej: Ustawienia dodatkowe**.

   ![Podstawy](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

1. W polu **Użyj istniejącego parametru danych** wybierz pozycję **kopia zapasowa** , a następnie wybierz odpowiednią kopię zapasową z opcji przewijania w dół. Kliknij pozycję **Przejrzyj i utwórz**.

   ![kopia zapasowa](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

1. Po przywróceniu magazynu danych sprawdź, czy **stan** jest w trybie online.

## <a name="next-steps"></a>Następne kroki

- [Przywracanie istniejącej dedykowanej puli SQL (dawniej SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Przywracanie usuniętej dedykowanej puli SQL (dawniej SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
