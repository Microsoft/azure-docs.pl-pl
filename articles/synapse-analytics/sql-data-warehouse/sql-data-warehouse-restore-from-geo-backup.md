---
title: Przywracanie magazynu danych z geograficznej kopii zapasowej
description: Przewodnik dotyczący przywracania geograficznego dla puli SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7e0980a9142dc966916d5a4df898ea53b0ddeae5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745082"
---
# <a name="geo-restore-for-sql-pool"></a>Przywracanie geograficzne dla puli SQL

W tym artykule dowiesz się, jak przywrócić pulę SQL z geograficznej kopii zapasowej za pośrednictwem Azure Portal i programu PowerShell.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Sprawdź pojemność jednostek DTU.** Każda pula SQL jest hostowana przez program SQL Server (na przykład myserver.database.windows.net), który ma domyślny limit przydziału jednostek DTU. Sprawdź, czy program SQL Server ma wystarczający limit przydziału jednostek DTU dla przywracanej bazy danych. Aby dowiedzieć się, jak obliczyć liczbę jednostek DTU potrzebnych lub aby zażądać większej liczby jednostek DTU, zobacz [żądanie zmiany limitu przydziału jednostek DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Przywracanie z regionu geograficznego platformy Azure za pomocą programu PowerShell

Aby przywrócić z geograficznej kopii zapasowej, należy użyć polecenia cmdlet [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) i [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

> [!NOTE]
> Można wykonać przywracanie geograficzne do Gen2! W tym celu należy określić Gen2 serviceobiektywname (np. wartości DW1000**c**) jako opcjonalny parametr.
>

1. Przed rozpoczęciem upewnij się, że [zainstalowano Azure PowerShell](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
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
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different logical server.
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

Wykonaj kroki opisane poniżej, aby przywrócić pulę SQL z kopii zapasowej geograficznej:

1. Zaloguj się do konta [Azure Portal](https://portal.azure.com/) .
2. Kliknij pozycję **+ Utwórz zasób**.

   ![Nowy magazyn DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Kliknij pozycję **bazy danych** , a następnie * * Azure Synapse Analytics (dawniej SQL DW) * *.

   ![Nowy DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Wprowadź informacje wymagane na karcie **podstawowe** i kliknij przycisk **Dalej: Ustawienia dodatkowe**.

   ![Podstawy](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. W polu **Użyj istniejącego parametru danych** wybierz pozycję **kopia zapasowa** , a następnie wybierz odpowiednią kopię zapasową z opcji przewijania w dół. Kliknij przycisk **Przegląd + Utwórz**.

   ![kopia zapasowa](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. Po przywróceniu magazynu danych sprawdź, czy **stan** jest w trybie online.

## <a name="next-steps"></a>Następne kroki

- [Przywracanie istniejącej puli SQL](sql-data-warehouse-restore-active-paused-dw.md)
- [Przywracanie usuniętej puli SQL](sql-data-warehouse-restore-deleted-dw.md)
