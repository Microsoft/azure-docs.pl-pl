---
title: Skrypt programu PowerShell służący do tworzenia blokady zasobów dla bazy danych i grafu interfejsu API usługi Azure Cosmos Gremlin
description: Utwórz blokadę zasobów dla bazy danych i grafu interfejsu API usługi Azure Cosmos Gremlin
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: bb09f196e8625a484694e78cd7d5332a52d1179c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98675650"
---
# <a name="create-a-resource-lock-for-azure-cosmos-gremlin-api-database-and-graph-using-azure-powershell"></a>Utwórz blokadę zasobów dla bazy danych i grafu interfejsu API usługi Azure Cosmos Gremlin przy użyciu Azure PowerShell
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Ten przykład wymaga Azure PowerShell az 5.4.0 lub nowszy. Uruchom `Get-Module -ListAvailable Az` , aby zobaczyć, które wersje są zainstalowane.
Jeśli musisz zainstalować program, zobacz [install Azure PowerShell module](/powershell/azure/install-az-ps).

Uruchom [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) , aby zalogować się do platformy Azure.

> [!IMPORTANT]
> Blokady zasobów nie działają w przypadku zmian wprowadzonych przez użytkowników łączących się przy użyciu dowolnego zestawu Gremlin SDK lub witryny Azure Portal, chyba że konto Cosmos DB jest najpierw zablokowane przy `disableKeyBasedMetadataWriteAccess` włączonej właściwości. Aby dowiedzieć się więcej o tym, jak włączyć tę właściwość, zobacz, [uniemożliwiając zmiany z zestawów SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
|**Zasób platformy Azure**| |
| [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) | Tworzy blokadę zasobu. |
| [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock) | Pobiera blokadę zasobu lub wyświetla listę blokad zasobów. |
| [Remove-AzResourceLock](/powershell/module/az.resources/remove-azresourcelock) | Usuwa blokadę zasobu. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure PowerShell, zobacz [dokumentację dotyczącą Azure PowerShell](/powershell/).