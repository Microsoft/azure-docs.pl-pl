---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 6f8cfc314c75221a88b58095cc71ea685280ac49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501021"
---
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

*Jeśli masz już grupę zasobów, możesz pominąć, aby [utworzyć magazyn kluczy](#create-a-key-vault).*

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Utwórz grupę zasobów za pomocą polecenia [AZ Group Create](/cli/azure/group#az-group-create) Azure CLI, polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell lub z [Azure Portal](https://portal.azure.com).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

*Jeśli masz już Magazyn kluczy, możesz pominąć, aby [ustawić zaawansowane zasady dostępu magazynu kluczy](#set-key-vault-advanced-access-policies).*

Utwórz magazyn kluczy za pomocą polecenia [AZ Key magazyn Create](/cli/azure/keyvault#az-keyvault-create) Azure CLI, polecenia [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) programu Azure PowerShell, [Azure Portal](https://portal.azure.com)lub [szablonu Menedżer zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> Magazyn kluczy i maszyny wirtualne muszą znajdować się w tej samej subskrypcji. Ponadto, aby zapewnić, że wpisy tajne szyfrowania nie przekraczają granic regionalnych, Azure Disk Encryption wymaga, aby Key Vault i maszyny wirtualne znajdowały się w tym samym regionie. Utwórz i użyj Key Vault, który znajduje się w tej samej subskrypcji i regionie co maszyny wirtualne do zaszyfrowania. 

Każdy Key Vault musi mieć unikatową nazwę. Zastąp <unikatowym identyfikatorem magazynu kluczy> nazwą magazynu klucza w poniższych przykładach.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Podczas tworzenia magazynu kluczy przy użyciu interfejsu wiersza polecenia platformy Azure Dodaj flagę "--Enabled-for-Disk-Encryption".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Podczas tworzenia magazynu kluczy przy użyciu Azure PowerShell należy dodać flagę "-EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Możesz również utworzyć magazyn kluczy przy użyciu [szablonu Menedżer zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. W szablonie szybkiego startu platformy Azure kliknij pozycję **Wdróż na platformie Azure**.
2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, nazwę Key Vault, identyfikator obiektu, postanowienia prawne i umowę, a następnie kliknij przycisk **Kup**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Ustawianie zaawansowanych zasad dostępu magazynu kluczy

Platforma Azure wymaga dostępu do kluczy szyfrowania lub wpisów tajnych w magazynie kluczy, aby udostępnić je na maszynie wirtualnej w celu rozruchu i odszyfrowywania woluminów. 

Jeśli nie włączono magazynu kluczy na potrzeby wdrożenia szyfrowania dysku, wdrożenia lub szablonu w momencie tworzenia (jak pokazano w poprzednim kroku), należy zaktualizować zaawansowane zasady dostępu.  

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj [AZ Key Update](/cli/azure/keyvault#az-keyvault-update) , aby włączyć szyfrowanie dysków dla magazynu kluczy. 

 - **Włącz Key Vault szyfrowania dysku:** Szyfrowanie włączone dla dysku jest wymagane. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **W razie potrzeby włącz Key Vault wdrożenia:** Umożliwia dostawcy zasobów Microsoft. COMPUTE pobieranie wpisów tajnych z tego magazynu kluczy, gdy ten magazyn kluczy jest przywoływany podczas tworzenia zasobu, na przykład podczas tworzenia maszyny wirtualnej.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **W razie potrzeby włącz Key Vault wdrożenia szablonów:** Zezwól Menedżer zasobów na pobieranie wpisów tajnych z magazynu.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Aby włączyć szyfrowanie dysków dla magazynu kluczy, należy użyć polecenia cmdlet programu PowerShell magazynu kluczy [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) .

  - **Włącz Key Vault szyfrowania dysku:** Usługa EnabledForDiskEncryption jest wymagana do szyfrowania dysków Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **W razie potrzeby włącz Key Vault wdrożenia:** Umożliwia dostawcy zasobów Microsoft. COMPUTE pobieranie wpisów tajnych z tego magazynu kluczy, gdy ten magazyn kluczy jest przywoływany podczas tworzenia zasobu, na przykład podczas tworzenia maszyny wirtualnej.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **W razie potrzeby włącz Key Vault wdrożenia szablonów:** Umożliwia Azure Resource Manager pobierania wpisów tajnych z tego magazynu kluczy, gdy do tego magazynu kluczy odwołuje się wdrożenie szablonu.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure Portal

1. Wybierz magazyn kluczy, przejdź do pozycji **zasady dostępu**, a **następnie kliknij, aby wyświetlić zaawansowane zasady dostępu**.
2. Zaznacz pole o nazwie **Włącz dostęp do Azure Disk Encryption na potrzeby szyfrowania woluminów**.
3. Wybierz pozycję **Włącz dostęp do usługi Azure Virtual Machines w celu wdrożenia** i/lub **włącz dostęp do Azure Resource Manager na potrzeby wdrożenia szablonu**, jeśli jest to konieczne. 
4. Kliknij pozycję **Zapisz**.

    ![Zaawansowane zasady dostępu magazynu kluczy platformy Azure](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Skonfiguruj klucz szyfrowania klucza (KEK)

Jeśli chcesz użyć klucza szyfrowania klucza (KEK) w celu uzyskania dodatkowej warstwy zabezpieczeń dla kluczy szyfrowania, Dodaj KEK do magazynu kluczy. W przypadku określenia klucza szyfrowania klucza Azure Disk Encryption używa tego klucza do zawijania wpisów tajnych szyfrowania przed zapisem w Key Vault.

Nowy KEK można wygenerować przy użyciu interfejsu wiersza polecenia platformy Azure [AZ Key magazynu create](/cli/azure/keyvault/key#az-keyvault-key-create) Azure PowerShell, polecenie cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) lub [Azure Portal](https://portal.azure.com/). Musisz wygenerować typ klucza RSA; Azure Disk Encryption nie obsługuje jeszcze kluczy krzywej eliptycznej.

Zamiast tego możesz zaimportować KEK z lokalnego modułu HSM zarządzania kluczami. Aby uzyskać więcej informacji, zobacz [dokumentację Key Vault](../articles/key-vault/keys/hsm-protected-keys.md).

Adresy URL KEK magazynu kluczy muszą być w wersji. System Azure wymusza to ograniczenie wersji. Aby uzyskać prawidłowe tajne i KEK adresy URL, zobacz następujące przykłady:

* Przykładowy prawidłowy tajny adres URL: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Przykład prawidłowego adresu URL KEK: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure Disk Encryption nie obsługuje określania numerów portów jako części wpisów tajnych magazynu kluczy i adresów URL KEK. Przykłady nieobsługiwanych i obsługiwanych adresów URL magazynu kluczy można znaleźć w następujących przykładach:

  * Akceptowalny adres URL magazynu kluczy: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Nieakceptowalny adres URL magazynu kluczy: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj interfejsu wiersza polecenia platformy Azure [AZ Key magazynu Create](/cli/azure/keyvault/key#az-keyvault-key-create) , aby wygenerować nowy KEK i zapisać go w magazynie kluczy.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA
```

Zamiast tego możesz zaimportować klucz prywatny przy użyciu interfejsu wiersza polecenia platformy Azure [AZ Key magazynu klucz import](/cli/azure/keyvault/key#az-keyvault-key-import) :

W obu przypadkach należy podać nazwę KEK w interfejsie wiersza polecenia platformy Azure [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) --Key-Encryption-Key. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Użyj polecenia cmdlet Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) , aby wygenerować nowy KEK i zapisać go w magazynie kluczy.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Zamiast tego możesz zaimportować klucz prywatny przy użyciu polecenia Azure PowerShell [AZ Key magazynu klucz import](/cli/azure/keyvault/key#az-keyvault-key-import) .

W obu przypadkach należy podać identyfikator magazynu kluczy KEK oraz adres URL KEK do parametrów Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) -KeyEncryptionKeyVaultId i-KeyEncryptionKeyUrl. Należy pamiętać, że w tym przykładzie założono, że używasz tego samego magazynu kluczy zarówno dla klucza szyfrowania dysku, jak i dla KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```