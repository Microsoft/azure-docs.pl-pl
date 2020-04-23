---
title: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu programu Azure PowerShell
description: W tym przewodniku Szybki start dowiesz się, jak tworzyć i szyfrować maszynę wirtualną systemu Windows za pomocą programu Azure PowerShell
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 6f564a9a4f13136bbe7e28a3600ca71892c82439
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081595"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-in-azure-with-powershell"></a>Szybki start: tworzenie i szyfrowanie maszyny wirtualnej systemu Windows na platformie Azure za pomocą programu PowerShell

Moduł Azure PowerShell umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą wiersza polecenia programu PowerShell lub skryptów. Ten przewodnik Szybki start pokazuje, jak używać modułu programu Azure PowerShell do tworzenia maszyny wirtualnej systemu Windows (VM), tworzenia magazynu kluczy do przechowywania kluczy szyfrowania i szyfrowania maszyny wirtualnej. 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną platformy Azure za pomocą [programu New-AzVM](/powershell/module/az.compute/new-azvm). Poświadczenia należy podać do polecenia cmdlet. 

```powershell
$cred = Get-Credential 

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image win2016datacenter -Size Standard_D2S_V3
```

Wdrożenie maszyny wirtualnej potrwa kilka minut. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Tworzenie magazynu kluczy skonfigurowanych dla kluczy szyfrowania

Szyfrowanie dysków platformy Azure przechowuje swój klucz szyfrowania w magazynie azure key vault. Utwórz magazyn kluczy za pomocą [programu New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Aby włączyć magazyn kluczy do przechowywania kluczy szyfrowania, należy użyć parametru -EnabledForDiskEncryption.

> [!Important]
> Każda przechowalnia kluczy musi mieć unikatową nazwę. Poniższy przykład tworzy magazyn kluczy o nazwie *myKV*, ale należy nazwać coś innego.

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Szyfruj maszynę wirtualną

Zaszyfruj maszynę wirtualną za pomocą [funkcji Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension wymaga pewnych wartości z obiektu Przechowalnia kluczy. Wartości te można uzyskać, przekazując unikatową nazwę magazynu kluczy do [programu Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```powershell
$KeyVault = Get-AzKeyVault -VaultName MyKV -ResourceGroupName MyResourceGroup

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId
```

Po kilku minutach proces zwróci następujące elementy:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

Proces szyfrowania można zweryfikować, uruchamiając [get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

```powershell
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Gdy szyfrowanie jest włączone, na zwróconym wyjściu zostaną wyświetlone następujące informacje:

```
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NoDiskFound
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono maszynę wirtualną, utworzono magazyn kluczy, który był włączony dla kluczy szyfrowania, i zaszyfrowano maszynę wirtualną.  Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat wymagań wstępnych usługi Azure Disk Encryption dotyczących maszyn wirtualnych IaaS.

> [!div class="nextstepaction"]
> [Omówienie szyfrowania dysków platformy Azure](disk-encryption-overview.md)