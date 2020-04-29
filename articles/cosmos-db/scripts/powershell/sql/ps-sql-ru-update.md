---
title: Skrypt programu PowerShell do aktualizowania przepływności (RU/s) dla bazy danych lub kontenera Azure Cosmos DB SQL API
description: Skrypt programu PowerShell do aktualizowania przepływności (RU/s) dla bazy danych lub kontenera Azure Cosmos DB SQL API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: 950a76c2caaffe6eef578d5614e9469c5942e08c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80365976"
---
# <a name="update-throughput-rus-for-an-azure-cosmos-db-sql-api-database-or-container"></a>Aktualizowanie przepływności (RU/s) dla bazy danych lub kontenera Azure Cosmos DB SQL API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-update.ps1 "Update throughput (RU/s) for an Azure Cosmos DB SQL API Database or Container")]

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
| [Set-AzCosmosDBSqlDatabase](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqldatabase) | Tworzy nowy lub aktualizuje istniejący SQL Database Cosmos DB. |
| [Get-AzCosmosDBSqlContainer](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainer) | Pobiera kontener SQL Cosmos DB. |
| [Set-AzCosmosDBSqlContainer](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqlcontainer) | Tworzy nowy lub aktualizuje istniejący kontener Cosmos DB SQL. |
|**Grupy zasobów platformy Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Więcej przykładowych skryptów programu PowerShell dla usługi Azure Cosmos DB można znaleźć w artykule [Azure Cosmos DB PowerShell scripts](../../../powershell-samples.md) (Skrypty programu PowerShell dla usługi Azure Cosmos DB).