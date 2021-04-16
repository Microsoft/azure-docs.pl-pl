---
title: Program PowerShell & INTERFEJSY API REST dla dedykowanej puli SQL (dawniej SQL DW)
description: Najważniejsze polecenia cmdlet programu PowerShell dla dedykowanej puli SQL (dawniej SQL DW) w programie Azure Synapse Analytics w tym sposób wstrzymywania i wznawiania bazy danych.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: 1f00f470fb0aa8ac98b431c6fc9428f501b553ed
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566447"
---
# <a name="powershell--rest-apis-for-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Interfejsy API REST programu PowerShell & dla dedykowanej puli SQL (dawniej SQL DW) w Azure Synapse Analytics 

Wieloma dedykowanymi zadaniami administracyjnymi puli SQL można zarządzać przy użyciu Azure PowerShell cmdlet lub interfejsów API REST.  Poniżej przedstawiono kilka przykładów użycia poleceń programu PowerShell w celu zautomatyzowania typowych zadań w dedykowanej puli SQL (dawniej SQL DW).  Aby uzyskać kilka dobrych przykładów interfejsu REST, zobacz artykuł [Manage scalability with REST (Zarządzanie skalowalnością za pomocą interfejsu REST).](sql-data-warehouse-manage-compute-rest-api.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Wprowadzenie do Azure PowerShell cmdlet

1. Otwórz program Windows PowerShell.
2. W wierszu polecenia programu PowerShell uruchom te polecenia, aby zalogować się do Azure Resource Manager i wybrać subskrypcję.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Przykład wstrzymywania magazynu danych

Wstrzymaj bazę danych o nazwie "Database02" hostowaną na serwerze o nazwie "Server01".  Serwer znajduje się w grupie zasobów platformy Azure o nazwie "ResourceGroup1".

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

Odmiana , w tym przykładzie, potokuje pobrany obiekt do [suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  W związku z tym baza danych jest wstrzymana. Końcowe polecenie wyświetla wyniki.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Przykład uruchamiania magazynu danych

Wznów działanie bazy danych o nazwie "Database02" hostowanej na serwerze o nazwie "Server01". Serwer znajduje się w grupie zasobów o nazwie "ResourceGroup1".

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Odmiana— w tym przykładzie baza danych o nazwie "Database02" jest pobierana z serwera o nazwie "Server01", który znajduje się w grupie zasobów o nazwie "ResourceGroup1". Potokuje pobrany obiekt do [obiektu Resume-AzSqlDatabase.](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Pamiętaj, że jeśli serwer jest foo.database.windows.net, użyj parametru "foo" jako parametru -ServerName w poleceniach cmdlet programu PowerShell.

## <a name="other-supported-powershell-cmdlets"></a>Inne obsługiwane polecenia cmdlet programu PowerShell

Te polecenia cmdlet programu PowerShell są obsługiwane Azure Synapse Analytics magazynu danych.

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

Aby uzyskać więcej przykładów programu PowerShell, zobacz:

* [Tworzenie magazynu danych przy użyciu programu PowerShell](create-data-warehouse-powershell.md)
* [Przywracanie bazy danych](sql-data-warehouse-restore-points.md)

Aby uzyskać informacje o innych zadaniach, które można zautomatyzować za pomocą programu PowerShell, [zobacz Azure SQL Database cmdlet](/powershell/module/az.sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Nie wszystkie Azure SQL Database cmdlet są obsługiwane Azure Synapse Analytics magazynu danych. Aby uzyskać listę zadań, które można zautomatyzować za pomocą rest, zobacz [Operacje dla Azure SQL Database](/rest/api/sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
