---
title: Szyfrowanie dysków dla zestawów skalowania platformy Azure za pomocą Azure PowerShell
description: Dowiedz się, jak używać Azure PowerShell do szyfrowania wystąpień maszyn wirtualnych i dołączonych dysków w zestawie skalowania maszyn wirtualnych z systemem Windows
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 59b015232fcfe2fd98e1b9f28c2eb4fa86606049
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519575"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Szyfruj system operacyjny i dołączone dyski danych w zestawie skalowania maszyn wirtualnych za pomocą Azure PowerShell

Moduł Azure PowerShell umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi za pomocą wiersza polecenia programu PowerShell lub skryptów.  W tym artykule przedstawiono sposób użycia Azure PowerShell do tworzenia i szyfrowania zestawu skalowania maszyn wirtualnych. Aby uzyskać więcej informacji na temat stosowania Azure Disk Encryption do zestawu skalowania maszyn wirtualnych, zobacz [Azure Disk Encryption for Virtual Machine Scale Sets](disk-encryption-overview.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Tworzenie Azure Key Vault z włączonym szyfrowaniem dysków

Azure Key Vault mogą przechowywać klucze, wpisy tajne lub hasła, które umożliwiają bezpieczne wdrażanie ich w aplikacjach i usługach. Klucze kryptograficzne są przechowywane w Azure Key Vault przy użyciu ochrony oprogramowania lub można importować lub generować klucze w sprzętowych modułach zabezpieczeń (sprzętowych modułów zabezpieczeń) z certyfikatem standardu FIPS 140-2 Level 2. Te klucze kryptograficzne są używane do szyfrowania i odszyfrowywania dysków wirtualnych dołączonych do maszyny wirtualnej. Zachowujesz kontrolę nad tymi kluczami kryptograficznymi i można przeprowadzić inspekcję ich użycia.

Utwórz Key Vault za pomocą elementu [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Aby zezwolić Key Vault na używanie do szyfrowania dysków, ustaw parametr *EnabledForDiskEncryption* . W poniższym przykładzie zdefiniowano również zmienne dla nazwy grupy zasobów, nazwy Key Vault i lokalizacji. Podaj własną unikatową nazwę Key Vault:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Użyj istniejącego Key Vault

Ten krok jest wymagany tylko w przypadku istniejących Key Vault, które mają być używane z szyfrowaniem dysków. Pomiń ten krok, jeśli utworzono Key Vault w poprzedniej sekcji.

Istniejące Key Vault w tej samej subskrypcji i regionie można włączyć jako zestaw skalowania na potrzeby szyfrowania dysku przy użyciu [opcji Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy). Zdefiniuj nazwę istniejącego Key Vault w zmiennej *$vaultName* w następujący sposób:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania

Najpierw ustaw nazwę użytkownika i hasło administratora wystąpień maszyn wirtualnych przy użyciu polecenia [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Teraz utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [New-AzVmss](/powershell/module/az.compute/new-azvmss). Musisz również utworzyć moduł równoważenia obciążenia, który umożliwia kierowanie ruchu do poszczególnych wystąpień maszyn wirtualnych. Moduł równoważenia obciążenia zawiera reguły, które pozwalają kierować ruchem na porcie TCP 80 oraz korzystać z ruchu pulpitu zdalnego na porcie TCP 3389 i komunikacji zdalnej programu PowerShell na porcie TCP 5985:

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>Włącz szyfrowanie

Aby zaszyfrować wystąpienia maszyn wirtualnych w zestawie skalowania, najpierw Uzyskaj pewne informacje dotyczące identyfikatora URI Key Vault i identyfikatora zasobu z poleceniem [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault). Te zmienne są używane do uruchamiania procesu szyfrowania przy użyciu [opcji Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension):


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

Po wyświetleniu monitu wpisz *y* , aby kontynuować proces szyfrowania dysku dla wystąpień maszyn wirtualnych zestawu skalowania.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Włącz szyfrowanie przy użyciu KEK, aby zawijać klucz

Klucz szyfrowania klucza można także użyć, aby zwiększyć bezpieczeństwo podczas szyfrowania zestawu skalowania maszyn wirtualnych.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

> [!NOTE]
>  Składnia wartości parametru dysk-szyfrowanie-kluczy jest pełnym ciągiem identyfikatora:</br>
/subscriptions/[Identyfikator subskrypcji — identyfikator GUID]/resourceGroups/[Resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy]</br></br>
> Składnia wartości parametru klucz-szyfrowanie-klucz jest pełnym identyfikatorem URI KEK w programie:</br>
https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Keys/[kekname]/[KEK-Unique-ID]

## <a name="check-encryption-progress"></a>Sprawdź postęp szyfrowania

Aby sprawdzić stan szyfrowania dysku, użyj polecenie [Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption):


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Gdy wystąpienia maszyn wirtualnych są szyfrowane, kod *EncryptionSummary* zgłasza *ProvisioningState/zakończony powodzeniem* , jak pokazano w następujących przykładowych danych wyjściowych:

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>Wyłącz szyfrowanie

Jeśli nie chcesz już używać szyfrowanych wystąpień maszyn wirtualnych, możesz wyłączyć szyfrowanie za pomocą [disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) w następujący sposób:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Następne kroki

- W tym artykule użyto Azure PowerShell do szyfrowania zestawu skalowania maszyn wirtualnych. Możesz również użyć szablonów [interfejsu wiersza polecenia platformy Azure](disk-encryption-cli.md) lub [Azure Resource Manager](disk-encryption-azure-resource-manager.md).
- Jeśli chcesz, aby Azure Disk Encryption zastosowały się po zainicjowaniu innego rozszerzenia, możesz użyć [sekwencjonowania rozszerzeń](virtual-machine-scale-sets-extension-sequencing.md).
