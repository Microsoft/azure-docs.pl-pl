---
title: Skrypt programu PowerShell służący do tworzenia blokady zasobów dla usługi Azure Cosmos interfejs API tabel tabeli
description: Tworzenie blokady zasobów dla usługi Azure Cosmos interfejs API tabel tabeli
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: a56846e3bfbc9e5392c509ff1f484b8109ce9eac
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098794"
---
# <a name="create-a-resource-lock-for-azure-cosmos-table-api-table-using-azure-powershell"></a>Tworzenie blokady zasobów dla usługi Azure Cosmos interfejs API tabel tabeli przy użyciu Azure PowerShell
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-PowerShell-install](../../../../../includes/sample-PowerShell-install-no-ssh.md)]

> [!IMPORTANT]
> Blokady zasobów nie działają w przypadku zmian wprowadzonych przez użytkowników łączących się przy użyciu dowolnego Cosmos DB zestawu SDK, wszelkich narzędzi łączących się za pośrednictwem kluczy konta lub witryny Azure Portal, chyba że konto Cosmos DB jest najpierw zablokowane przy `disableKeyBasedMetadataWriteAccess` włączonej właściwości. Aby dowiedzieć się więcej o tym, jak włączyć tę właściwość, zobacz, [uniemożliwiając zmiany z zestawów SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-PowerShell[main](../../../../../PowerShell_scripts/cosmosdb/table/ps-table-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```PowerShell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
|**Zasób platformy Azure**| |
| [New-AzResourceLock](/PowerShell/module/az.resources/new-azresourcelock) | Tworzy blokadę zasobu. |
| [Get-AzResourceLock](/PowerShell/module/az.resources/get-azresourcelock) | Pobiera blokadę zasobu lub wyświetla listę blokad zasobów. |
| [Remove-AzResourceLock](/PowerShell/module/az.resources/remove-azresourcelock) | Usuwa blokadę zasobu. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure PowerShell, zobacz [dokumentację dotyczącą Azure PowerShell](/PowerShell/).