---
title: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu programu Azure PowerShell
description: W tym przewodniku szybki start dowiesz się, jak utworzyć i zaszyfrować maszynę wirtualną z systemem Linux przy użyciu programu Azure PowerShell
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 32387ed6c124798155b912bc88cd717a30cb264c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89074325"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-in-azure-with-azure-powershell"></a>Szybki Start: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu Azure PowerShell

Moduł Azure PowerShell umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą wiersza polecenia programu PowerShell lub skryptów. W tym przewodniku szybki start pokazano, jak za pomocą modułu Azure PowerShell utworzyć maszynę wirtualną z systemem Linux, utworzyć Key Vault do przechowywania kluczy szyfrowania i zaszyfrować maszynę wirtualną. Ten przewodnik Szybki Start używa obrazu Ubuntu 16,04 LTS Marketplace z kanonicznej i Standard_D2S_V3 rozmiaru maszyny wirtualnej. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną platformy Azure z poleceniem [New-AzVM](/powershell/module/az.compute/new-azvm), przekazując do niej utworzony wcześniej obiekt konfiguracji maszyny wirtualnej.

```powershell-interactive
$cred = Get-Credential

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image Canonical:UbuntuServer:18.04-LTS:latest -Size Standard_D2S_V3
```

Wdrożenie maszyny wirtualnej potrwa kilka minut. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Utwórz Key Vault skonfigurowany pod kątem kluczy szyfrowania

Klucz szyfrowania w usłudze Azure Disk Encryption jest przechowywany w Azure Key Vault. Utwórz Key Vault za pomocą elementu [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Aby włączyć Key Vault do przechowywania kluczy szyfrowania, użyj parametru-EnabledForDiskEncryption.

> [!Important]
> Każdy Magazyn kluczy musi mieć unikatową nazwę na platformie Azure. W poniższych przykładach Zastąp <unikatową nazwą magazynu kluczy> z wybraną nazwą.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Szyfruj maszynę wirtualną

Zaszyfruj maszynę wirtualną za pomocą [opcji Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension wymaga pewnych wartości z obiektu Key Vault. Możesz uzyskać te wartości, przekazując unikatową nazwę magazynu kluczy do [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```azurepowershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -SkipVmBackup -VolumeType All
```

Po kilku minutach proces zwróci następujące elementy:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

Proces szyfrowania można sprawdzić, uruchamiając [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Po włączeniu szyfrowania w zwróconych danych wyjściowych zostaną wyświetlone następujące elementy:

```
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono maszynę wirtualną, która utworzyła Key Vault, w której włączono obsługę kluczy szyfrowania, oraz zaszyfrowaną MASZYNę wirtualną.  Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat Azure Disk Encryption dla maszyn wirtualnych z systemem Linux.

> [!div class="nextstepaction"]
> [Przegląd Azure Disk Encryption](disk-encryption-overview.md)
