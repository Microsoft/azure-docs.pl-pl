---
title: Skrypt programu PowerShell umożliwiający zmianę priorytetu trybu failover dla konta usługi Azure Cosmos z jednym regionem zapisu
description: Przykładowy skrypt Azure PowerShell — zmiana priorytetu trybu failover lub wyzwolenie trybu failover dla konta usługi Azure Cosmos z jednym regionem zapisu
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 05770ba1d2562ec94d8771495aa66ea69523fd06
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488252"
---
# <a name="change-failover-priority-or-trigger-failover-for-an-azure-cosmos-account-with-single-write-region-by-using-powershell"></a>Zmiana priorytetu trybu failover lub wyzwolenie trybu failover dla konta usługi Azure Cosmos z jednym regionem zapisu przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Przykładowy skrypt

> [!NOTE]
> Każda zmiana w regionie z `failoverPriority=0` wyzwala ręczną pracę awaryjną i można ją wykonać tylko na koncie skonfigurowanym do ręcznego przełączania do trybu failover. Zmiany we wszystkich pozostałych regionach po prostu zmieniają priorytet trybu failover dla konta Cosmos.
> [!NOTE]
> Ten przykład pokazuje użycie konta interfejsu API SQL (rdzeń). Aby użyć tego przykładu dla innych interfejsów API, skopiuj powiązane właściwości i Zastosuj do skryptu określonego przez interfejs API

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-failover-priority-update.ps1 "Update failover priority for an Azure Cosmos account or trigger a manual failover")]

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
| [Update-AzCosmosDBAccountFailoverPriority](/powershell/module/az.cosmosdb/update-azcosmosdbaccountfailoverpriority) | Aktualizowanie kolejności priorytetu trybu failover regionów konta Cosmos DB. |
|**Grupy zasobów platformy Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/).