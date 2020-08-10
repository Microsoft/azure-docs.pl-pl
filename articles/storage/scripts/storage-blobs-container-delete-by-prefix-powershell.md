---
title: Przykładowy skrypt programu Azure PowerShell — usuwanie kontenerów na podstawie prefiksu | Microsoft Docs
description: Przeczytaj przykład, który pokazuje, jak usunąć usługę Azure Blob Storage na podstawie prefiksu nazwy kontenera przy użyciu Azure PowerShell.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: 18827beeb606694e2c9089f27570216d413aabd9
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88033561"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Usuwanie kontenerów na podstawie prefiksu nazwy kontenera

Ten skrypt usuwa kontenery z usługi Azure Blob Storage na podstawie prefiksu nazwy kontenera.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów, pozostałe kontenery i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do usunięcia kontenerów na podstawie prefiksu nazwy kontenera. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Pobiera określone konto usługi Storage lub wszystkie konta usługi Storage w grupie zasobów lub subskrypcji. |
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | Zwraca listę kontenerów magazynu skojarzonych z kontem magazynu. |
| [Remove-AzStorageContainer](/powershell/module/az.storage/Remove-AzStorageContainer) | Usuwa określony kontener magazynu. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).

Więcej przykładowych skryptów programu PowerShell dla usługi Storage można znaleźć na stronie [PowerShell samples for Azure Blob storage (Przykładowe skrypty programu PowerShell dla usługi Azure Blob Storage)](../blobs/storage-samples-blobs-powershell.md).
