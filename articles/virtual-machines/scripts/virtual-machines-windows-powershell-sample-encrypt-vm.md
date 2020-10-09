---
title: Przykładowy skrypt programu Azure PowerShell — szyfrowanie maszyny wirtualnej z systemem Windows
description: Przykładowy skrypt programu Azure PowerShell — szyfrowanie maszyny wirtualnej z systemem Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 102187d52ec86a7a87223975ae3d1f6bbe9097d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89078779"
---
# <a name="encrypt-a-windows-virtual-machine-with-azure-powershell"></a>Szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu programu Azure PowerShell

Ten skrypt tworzy bezpieczny magazyn Azure Key Vault, klucze szyfrowania, jednostkę usługi Azure Active Directory i maszynę wirtualną z systemem Windows. Maszyna wirtualna jest następnie szyfrowana przy użyciu klucza szyfrowania z magazynu Key Vault i poświadczeń jednostki usługi.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/virtual-machine/encrypt-vm/encrypt-windows-vm.ps1 "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia wdrożenia. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) | Tworzy magazyn Azure Key Vault do przechowywania zabezpieczonych danych, takich jak klucze szyfrowania. |
| [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) | Tworzy klucz szyfrowania w magazynie Key Vault. |
| [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) | Tworzy jednostkę usługi Azure Active Directory używaną do bezpiecznego uwierzytelniania i sterowania dostępem do kluczy szyfrowania. |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Ustawia uprawnienia w magazynie Key Vault w celu udzielenia jednostce usługi dostępu do kluczy szyfrowania. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Tworzy maszynę wirtualną i łączy ją z kartą sieciową, siecią wirtualną, podsiecią i sieciową grupą zabezpieczeń. To polecenie otwiera również port 80 i ustawia poświadczenia administracyjne. |
| [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) | Pobiera wymagane informacje dotyczące magazynu Key Vault |
| [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) | Włącza szyfrowanie na maszynie wirtualnej przy użyciu poświadczeń jednostki usługi i klucza szyfrowania. |
| [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) | Wyświetla stan procesu szyfrowania maszyny wirtualnej. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby w niej zawarte. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).

Więcej przykładowych skryptów programu PowerShell na potrzeby maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
