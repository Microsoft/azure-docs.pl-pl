---
title: Skrypt programu PowerShell służący do uzyskiwania operacji na parametrach klucza i połączenia dla konta Azure Cosmos DB
description: Przykładowy skrypt Azure PowerShell — operacje na klucz konta i parametry połączenia dla konta Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: eb84684201ddf79f2a73fd1bcba1a0f69899bea8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98684322"
---
# <a name="connection-string-and-account-key-operations-for-an-azure-cosmos-db-account-using-powershell"></a>Operacje dotyczące parametrów połączenia i klucza konta dla konta Azure Cosmos DB przy użyciu programu PowerShell
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Ten przykład wymaga Azure PowerShell az 5.4.0 lub nowszy. Uruchom `Get-Module -ListAvailable Az` , aby zobaczyć, które wersje są zainstalowane.
Jeśli musisz zainstalować program, zobacz [install Azure PowerShell module](/powershell/azure/install-az-ps).

Uruchom [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) , aby zalogować się do platformy Azure.

## <a name="sample-script"></a>Przykładowy skrypt

> [!NOTE]
> Ten przykład demonstruje użycie konta interfejsu API SQL. Aby użyć tego przykładu dla innych interfejsów API, skopiuj powiązane właściwości i Zastosuj do skryptu specyficznego dla interfejsu API

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-keys-connection-strings.ps1 "Connection strings and account keys for Azure Cosmos account")]

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
| [Get-AzCosmosDBAccountKey](/powershell/module/az.cosmosdb/get-azcosmosdbaccountkey) | Pobiera parametry połączenia lub klucz (tylko do odczytu i zapisu lub tylko do odczytu) dla konta Cosmos DB. |
| [New-AzCosmosDBAccountKey](/powershell/module/az.cosmosdb/new-azcosmosdbaccountkey) | Wygeneruj ponownie określony klucz dla konta Cosmos DB. |
|**Grupy zasobów platformy Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/).