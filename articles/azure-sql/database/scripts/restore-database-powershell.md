---
title: 'PowerShell: Przywracanie kopii zapasowej bazy danych w SQL Database'
description: Azure PowerShell przykładowy skrypt służący do przywracania bazy danych w SQL Database do wcześniejszego punktu w czasie z automatycznych kopii zapasowych
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/27/2019
ms.openlocfilehash: 52398fa083765acf70b11602e939fbcd2b1f6da6
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054053"
---
# <a name="use-powershell-to-restore-an-azure-sql-database-to-an-earlier-point-in-time"></a>Przywracanie Azure SQL Database do wcześniejszego punktu w czasie za pomocą programu PowerShell
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ten przykładowy skrypt programu PowerShell umożliwia przywrócenie bazy danych w SQL Database do określonego punktu w czasie.  

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga AZ PowerShell 1.4.0 lub nowszego. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tworzy serwer, który hostuje bazy danych i pule elastyczne. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Tworzy bazę danych na serwerze. |
| [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) | Pobiera geograficznie nadmiarową kopię zapasową bazy danej, autonomicznej lub w puli. |
| [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) | Przywraca bazę danych. |
| [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) | Usuwa bazę danych. |
| [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Pobiera usuniętą bazę danych, którą można przywrócić. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../powershell-script-content-guide.md).
