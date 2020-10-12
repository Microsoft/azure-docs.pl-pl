---
title: Obliczanie rozmiaru kontenera obiektów BLOB za pomocą programu PowerShell
titleSuffix: Azure Storage
description: Obliczanie rozmiaru kontenera w usłudze Azure Blob Storage przez zsumowanie rozmiaru wszystkich zawartych w nim obiektów blob.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: de275bcca1644750532809b35ae85d954d3cac6d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89076909"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>Obliczanie rozmiaru kontenera obiektów BLOB za pomocą programu PowerShell

Ten skrypt oblicza rozmiar kontenera w usłudze Azure Blob Storage przez zsumowanie rozmiaru obiektów blob w tym kontenerze.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Ten skrypt programu PowerShell określa szacunkowy rozmiar kontenera i nie należy go używać w obliczeniach na potrzeby rozliczeń. Jeśli potrzebujesz skryptu obliczającego rozmiar kontenera na potrzeby rozliczeń, zobacz [Obliczanie rozmiaru kontenera obiektów blob na potrzeby rozliczeń](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów, kontener i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia, służące do obliczenia rozmiaru kontenera w usłudze Blob Storage. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Pobiera określone konto usługi Storage lub wszystkie konta usługi Storage w grupie zasobów lub subskrypcji. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Zwraca listę obiektów blob w kontenerze. |

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz skryptu obliczającego rozmiar kontenera na potrzeby rozliczeń, zobacz [Obliczanie rozmiaru kontenera obiektów blob na potrzeby rozliczeń](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).

Więcej przykładowych skryptów programu PowerShell dla usługi Storage można znaleźć na stronie [PowerShell samples for Azure Storage (Przykładowe skrypty programu PowerShell dla usługi Azure Storage)](../blobs/storage-samples-blobs-powershell.md).
