---
title: Skrypt programu PowerShell służący do aktualizowania regionów konta usługi Azure Cosmos
description: Przykładowy skrypt Azure PowerShell — aktualizowanie regionów konta usługi Azure Cosmos
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/21/2020
ms.author: mjbrown
ms.openlocfilehash: 4a8e24b4445e2bf29b35e87600de260c523d4376
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80367574"
---
# <a name="update-an-azure-cosmos-accounts-regions-using-powershell"></a>Aktualizowanie regionów konta usługi Azure Cosmos przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Przykładowy skrypt

> [!NOTE]
> Nie można modyfikować regionów i zmieniać innych właściwości konta Cosmos w tej samej operacji. Muszą one być takie same jak dwie osobne operacje.
> [!NOTE]
> Ten przykład pokazuje użycie konta interfejsu API SQL (rdzeń). Aby użyć tego przykładu dla innych interfejsów API, skopiuj powiązane właściwości i Zastosuj do skryptu określonego przez interfejs API.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-update-region.ps1 "Update Azure Cosmos account regions")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
|**Zasoby platformy Azure**| |
| [Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource) | Zaktualizuj zasób. |
|**Grupy zasobów platformy Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Więcej przykładowych skryptów programu PowerShell dla usługi Azure Cosmos DB można znaleźć w artykule [Azure Cosmos DB PowerShell scripts](../../../powershell-samples.md) (Skrypty programu PowerShell dla usługi Azure Cosmos DB).
