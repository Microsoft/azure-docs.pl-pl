---
title: PowerShell — Tworzenie obrazu niestandardowego na podstawie pliku VHD w Azure Lab Services
description: Ten skrypt programu PowerShell tworzy obraz niestandardowy na podstawie pliku VHD w Azure Lab Services.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: d22e6e1d226e826bf114a0fdb378879b828d9b4a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96022238"
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>Tworzenie niestandardowego obrazu z pliku VHD w Azure Lab Services przy użyciu programu PowerShell

Ten przykładowy skrypt programu PowerShell tworzy obraz niestandardowy na podstawie pliku VHD w Azure Lab Services

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
* **Laboratorium**. Skrypt wymaga posiadania istniejącego laboratorium. 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń: 

| Polecenie | Uwagi |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Pobiera zasoby. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Pobiera klucze dostępu dla konta usługi Azure Storage. |
| [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) | Dodaje wdrożenie platformy Azure do grupy zasobów. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/).

Dodatkowe przykłady skryptów programu Azure Lab Services PowerShell można znaleźć w [przykładach Azure Lab Services programu PowerShell](../samples-powershell.md).
