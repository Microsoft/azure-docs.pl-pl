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
ms.openlocfilehash: fb6faec5bcaec3bc4bfb2e15a2a07fdd532e0519
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460787"
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
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) | Tworzy magazyn Azure Key Vault do przechowywania zabezpieczonych danych, takich jak klucze szyfrowania. |
| [Klawisz Add-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey) | Tworzy klucz szyfrowania w magazynie Key Vault. |
| [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) | Tworzy jednostkę usługi Azure Active Directory używaną do bezpiecznego uwierzytelniania i sterowania dostępem do kluczy szyfrowania. |
| [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Ustawia uprawnienia w magazynie Key Vault w celu udzielenia jednostce usługi dostępu do kluczy szyfrowania. |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | Tworzy maszynę wirtualną i łączy ją z kartą sieciową, siecią wirtualną, podsiecią i sieciową grupą zabezpieczeń. To polecenie otwiera również port 80 i ustawia poświadczenia administracyjne. |
| [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault) | Pobiera wymagane informacje dotyczące magazynu Key Vault |
| [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) | Włącza szyfrowanie na maszynie wirtualnej przy użyciu poświadczeń jednostki usługi i klucza szyfrowania. |
| [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus) | Wyświetla stan procesu szyfrowania maszyny wirtualnej. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby w niej zawarte. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell na potrzeby maszyny wirtualnej można znaleźć w [dokumentacji dotyczącej maszyny wirtualnej platformy Azure z systemem Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
