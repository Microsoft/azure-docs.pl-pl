---
title: 'Interfejsy API REST programu PowerShell & dla dedykowanej puli SQL (dawniej: SQL DW)'
description: Najważniejsze polecenia cmdlet programu PowerShell dla dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics, w tym informacje na temat wstrzymania i wznowienia bazy danych.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: 83e6082025f068e91a3d531f052b746870ffd57a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584112"
---
# <a name="powershell--rest-apis-for-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Interfejsy API REST programu PowerShell & dla dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics 

Wiele dedykowanych zadań administracyjnych puli SQL można zarządzać przy użyciu poleceń cmdlet Azure PowerShell lub interfejsów API REST.  Poniżej przedstawiono kilka przykładów użycia poleceń programu PowerShell do automatyzowania typowych zadań w dedykowanej puli SQL (dawniej SQL DW).  Aby zapoznać się z niektórymi dobrymi przykładami REST, zobacz artykuł [Zarządzanie skalowalnością przy użyciu interfejsu REST](sql-data-warehouse-manage-compute-rest-api.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Wprowadzenie do Azure PowerShell poleceń cmdlet

1. Otwórz program Windows PowerShell.
2. W wierszu polecenia programu PowerShell uruchom następujące polecenie, aby zalogować się do Azure Resource Manager i wybrać subskrypcję.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Przykład wstrzymywania magazynu danych

Wstrzymywanie bazy danych o nazwie "Database02" hostowanej na serwerze o nazwie "Serwer01".  Serwer należy do grupy zasobów platformy Azure o nazwie "ResourceGroup1".

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

Odmiana, w tym przykładzie potok pobrano do [zawieszania-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  W związku z tym baza danych jest wstrzymana. Końcowe polecenie wyświetla wyniki.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Uruchom przykład magazynu danych

Wznów działanie bazy danych o nazwie "Database02" hostowanej na serwerze o nazwie "Serwer01". Serwer jest zawarty w grupie zasobów o nazwie "ResourceGroup1".

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Odmiana, w tym przykładzie pobiera bazę danych o nazwie "Database02" z serwera o nazwie "Serwer01", który znajduje się w grupie zasobów o nazwie "ResourceGroup1". Pobrano potok w celu [wznowienia-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Należy pamiętać, że jeśli serwer jest foo.database.windows.net, użyj "foo" jako-ServerName w poleceniach cmdlet programu PowerShell.

## <a name="other-supported-powershell-cmdlets"></a>Inne obsługiwane polecenia cmdlet programu PowerShell

Te polecenia cmdlet programu PowerShell są obsługiwane przez usługę Azure Synapse Analytics Data Warehouse.

* [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/get-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów dla programu PowerShell, zobacz:

* [Tworzenie magazynu danych przy użyciu programu PowerShell](create-data-warehouse-powershell.md)
* [Przywracanie bazy danych](sql-data-warehouse-restore-points.md)

Aby zapoznać się z innymi zadaniami, które można zautomatyzować za pomocą programu PowerShell, zobacz [Azure SQL Database polecenia cmdlet](/powershell/module/az.sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Nie wszystkie polecenia cmdlet Azure SQL Database są obsługiwane przez usługę Azure Synapse Analytics Data Warehouse. Aby zapoznać się z listą zadań, które można zautomatyzować za pomocą usługi REST, zobacz [operacje dla Azure SQL Database](/rest/api/sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
