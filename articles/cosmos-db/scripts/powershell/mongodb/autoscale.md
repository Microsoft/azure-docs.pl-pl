---
title: Skrypt programu PowerShell służący do tworzenia bazy danych i kolekcji interfejsu API usługi Azure Cosmos MongoDB z funkcją automatycznego skalowania
description: Skrypt Azure PowerShell — Tworzenie bazy danych i kolekcji interfejsu API usługi Azure Cosmos MongoDB przy użyciu funkcji automatycznego skalowania
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 64f4a4ce0e5ef35a0ba8dc314bbe6fb4f1d1030b
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675458"
---
# <a name="create-a-database-and-collection-with-autoscale-for-azure-cosmos-db---mongodb-api"></a>Tworzenie bazy danych i kolekcji z funkcją automatycznego skalowania dla interfejsu API Azure Cosmos DB MongoDB
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Ten przykład wymaga Azure PowerShell az 5.4.0 lub nowszy. Uruchom `Get-Module -ListAvailable Az` , aby zobaczyć, które wersje są zainstalowane.
Jeśli musisz zainstalować program, zobacz [install Azure PowerShell module](/powershell/azure/install-az-ps).

Uruchom [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) , aby zalogować się do platformy Azure.

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-autoscale.ps1 "Create a database and collection with autoscale for MongoDB API")]

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
| [New-AzCosmosDBMongoDBDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbdatabase) | Tworzy bazę danych interfejsu API MongoDB. |
| [New-AzCosmosDBMongoDBIndex](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbindex) | Tworzy indeks interfejsu API MongoDB. |
| [New-AzCosmosDBMongoDBCollection](/powershell/module/az.cosmosdb/new-azcosmosdbmongodbcollection) | Tworzy kolekcję interfejsów API MongoDB. |
|**Grupy zasobów platformy Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/).
