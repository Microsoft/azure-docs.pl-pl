---
title: Skrypt programu PowerShell do wyświetlania i uzyskiwania Azure Cosmos DB operacji interfejs API tabel
description: Azure PowerShell listę Azure Cosmos DB skryptu i Pobierz operacje interfejs API tabel
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 07/31/2020
ms.author: mjbrown
ms.openlocfilehash: 6385a28ff976b206839a86d94f1ea61a9c6f11f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98680152"
---
# <a name="list-and-get-tables-for-azure-cosmos-db---table-api"></a>Wyświetlanie listy i pobieranie tabel dla Azure Cosmos DB-interfejs API tabel
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Ten przykład wymaga Azure PowerShell az 5.4.0 lub nowszy. Uruchom `Get-Module -ListAvailable Az` , aby zobaczyć, które wersje są zainstalowane.
Jeśli musisz zainstalować program, zobacz [install Azure PowerShell module](/powershell/azure/install-az-ps).

Uruchom [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) , aby zalogować się do platformy Azure.

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-list-get.ps1 "List or get tables for Table API")]

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
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Wyświetla listę kont Cosmos DB lub pobiera określone konto Cosmos DB. |
| [Get-AzCosmosDBTable](/powershell/module/az.cosmosdb/get-azcosmosdbtable) | Wyświetla listę tabel interfejs API tabel na koncie lub pobiera określoną tabelę interfejs API tabel na koncie. |
|**Grupy zasobów platformy Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/).