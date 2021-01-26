---
title: Obliczanie rozmiaru kontenera obiektów BLOB za pomocą programu PowerShell
titleSuffix: Azure Storage
description: Oblicz rozmiar kontenera w usłudze Azure Blob Storage, sumując rozmiar każdego z obiektów BLOB.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 87ef18530c549396b7d8fe1ec4ff0e08cb8535e8
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784278"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>Obliczanie rozmiaru kontenera obiektów BLOB za pomocą programu PowerShell

Ten skrypt oblicza rozmiar kontenera w usłudze Azure Blob Storage przez zsumowanie rozmiaru obiektów BLOB w kontenerze.

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
