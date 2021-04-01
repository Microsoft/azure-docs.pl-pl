---
title: Skrypt programu PowerShell służący do tworzenia bazy danych i kontenera Azure Cosmos DB SQL API
description: Skrypt Azure PowerShell — Azure Cosmos DB tworzenia bazy danych i kontenera interfejsu SQL API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 2a098d3eff7bc4a8a78a880386145457b80b42d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98675566"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-api"></a>Tworzenie bazy danych i kontenera dla interfejsu API Azure Cosmos DB-SQL
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Ten przykład wymaga Azure PowerShell az 5.4.0 lub nowszy. Uruchom `Get-Module -ListAvailable Az` , aby zobaczyć, które wersje są zainstalowane.
Jeśli musisz zainstalować program, zobacz [install Azure PowerShell module](/powershell/azure/install-az-ps).

Uruchom [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) , aby zalogować się do platformy Azure.

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt tworzy konto Cosmos dla interfejsu API języka SQL (rdzeń) w dwóch regionach z spójnością poziomu sesji, bazą danych i kontenerem z kluczem partycji, niestandardowymi zasadami indeksowania, unikatowymi zasadami kluczy, czasem wygaśnięcia, dedykowaną przepływność i ostatnim modułem wypełniania konfliktów WINS przy użyciu niestandardowej ścieżki rozwiązywania konfliktów, która będzie używana w przypadku programu `multipleWriteLocations=true` .

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-create.ps1 "Create an account, database, and container for SQL API")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
|**Azure Cosmos DB**| |
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Tworzy konto Cosmos DB. |
| [New-AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbsqldatabase) | Tworzy SQL Database Cosmos DB. |
| [New-AzCosmosDBSqlUniqueKey](/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekey) | Tworzy obiekt PSSqlUniqueKey używany jako parametr dla elementu New-AzCosmosDBSqlUniqueKeyPolicy. |
| [New-AzCosmosDBSqlUniqueKeyPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekeypolicy) | Tworzy obiekt PSSqlUniqueKeyPolicy używany jako parametr dla elementu New-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlCompositePath](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcompositepath) | Tworzy obiekt PSCompositePath używany jako parametr dla elementu New-AzCosmosDBSqlIndexingPolicy. |
| [New-AzCosmosDBSqlIncludedPathIndex](/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpathindex) | Tworzy obiekt PSIndexes używany jako parametr dla elementu New-AzCosmosDBSqlIncludedPath. |
| [New-AzCosmosDBSqlIncludedPath](/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpath) | Tworzy obiekt PSIncludedPath używany jako parametr dla elementu New-AzCosmosDBSqlIndexingPolicy. |
| [New-AzCosmosDBSqlIndexingPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | Tworzy obiekt PSSqlIndexingPolicy używany jako parametr dla elementu New-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlConflictResolutionPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqlconflictresolutionpolicy) | Tworzy obiekt PSSqlConflictResolutionPolicy używany jako parametr dla elementu New-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer) | Tworzy nowy kontener SQL Cosmos DB. |
|**Grupy zasobów platformy Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/).