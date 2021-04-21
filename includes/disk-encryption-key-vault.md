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
ms.openlocfilehash: 98922829e83f84078c3d8cadae15844dba194c93
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799782"
---
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

*Jeśli masz już grupę zasobów, możesz od razu przejść do tworzenia [magazynu kluczy.](#create-a-key-vault)*

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

Utwórz grupę zasobów za pomocą [polecenia az group create](/cli/azure/group#az_group_create) interfejsu wiersza polecenia platformy Azure, polecenia Azure PowerShell [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) lub polecenia [Azure Portal](https://portal.azure.com).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

*Jeśli masz już magazyn kluczy, możesz od razu przejść do opcji Ustaw zaawansowane [zasady dostępu magazynu kluczy.](#set-key-vault-advanced-access-policies)*

Utwórz magazyn kluczy przy użyciu polecenia [az keyvault create](/cli/azure/keyvault#az_keyvault_create) interfejsu wiersza polecenia platformy Azure, polecenia [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) programu Azure PowerShell, polecenia [Azure Portal](https://portal.azure.com)lub Resource Manager [szablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> Magazyn kluczy i maszyny wirtualne muszą znajdować się w tej samej subskrypcji. Ponadto w celu zapewnienia, że wpisy tajne szyfrowania nie przekroczy granic regionalnych, Azure Disk Encryption wymaga, aby Key Vault i maszyny wirtualne znajdowały się w tym samym regionie. Utwórz maszynę Key Vault, która znajduje się w tej samej subskrypcji i tym samym regionie co maszyny wirtualne do zaszyfrowania. 

Każda Key Vault musi mieć unikatową nazwę. Zastąp <your-unique-keyvault-name> nazwą magazynu kluczy w poniższych przykładach.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Podczas tworzenia magazynu kluczy przy użyciu interfejsu wiersza polecenia platformy Azure dodaj flagę "--enabled-for-disk-encryption".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Podczas tworzenia magazynu kluczy przy użyciu Azure PowerShell dodaj flagę "-EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Magazyn kluczy można również utworzyć przy użyciu szablonu [Resource Manager .](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)

1. W szablonie szybkiego startu platformy Azure kliknij pozycję **Deploy to Azure (Wd wdrażaj na platformie Azure).**
2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, nazwę Key Vault, identyfikator obiektu, postanowienia prawne i umowę, a następnie kliknij pozycję **Kup.** 


##  <a name="set-key-vault-advanced-access-policies"></a>Ustawianie zaawansowanych zasad dostępu magazynu kluczy

Platforma Azure musi mieć dostęp do kluczy szyfrowania lub wpisów tajnych w magazynie kluczy, aby udostępnić je maszynie wirtualnej na potrzeby rozruchu i odszyfrowywania woluminów. 

Jeśli magazyn kluczy szyfrowania dysków, wdrażania lub wdrażania szablonu nie został włączyć w czasie tworzenia (jak pokazano w poprzednim kroku), należy zaktualizować jego zaawansowane zasady dostępu.  

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj [narzędzia az keyvault update,](/cli/azure/keyvault#az_keyvault_update) aby włączyć szyfrowanie dysków dla magazynu kluczy. 

 - **Włącz Key Vault szyfrowania dysków:** Wymagana jest opcja włączonego szyfrowania dysków. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Włącz Key Vault wdrożenia, jeśli to konieczne:** Umożliwia dostawcy zasobów Microsoft.Compute pobieranie wpisów tajnych z tego magazynu kluczy, gdy ten magazyn kluczy jest przywołyny podczas tworzenia zasobów, na przykład podczas tworzenia maszyny wirtualnej.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Włącz Key Vault wdrażania szablonu, jeśli to konieczne:** Zezwalaj Resource Manager na pobieranie wpisów tajnych z magazynu.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Użyj polecenia cmdlet programu PowerShell dla magazynu kluczy [Set-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) aby włączyć szyfrowanie dysków dla magazynu kluczy.

  - **Włącz Key Vault szyfrowania dysków:** Funkcja EnabledForDiskEncryption jest wymagana do szyfrowania dysków platformy Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Włącz Key Vault wdrożenia, jeśli to konieczne:** Umożliwia dostawcy zasobów Microsoft.Compute pobieranie wpisów tajnych z tego magazynu kluczy, gdy ten magazyn kluczy jest przywołyny podczas tworzenia zasobów, na przykład podczas tworzenia maszyny wirtualnej.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Włącz Key Vault wdrażania szablonu, jeśli to konieczne:** Umożliwia Azure Resource Manager wpisów tajnych z tego magazynu kluczy, gdy ten magazyn kluczy jest przywołyny we wdrożeniu szablonu.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure Portal

1. Wybierz magazyn kluczy, przejdź do opcji **Zasady dostępu** i Kliknij, aby wyświetlić zaawansowane **zasady dostępu.**
2. Zaznacz pole z etykietą **Włącz dostęp do Azure Disk Encryption szyfrowania woluminu.**
3. Wybierz **pozycję Włącz dostęp do usługi Azure Virtual Machines na** potrzeby wdrażania i/lub Włącz dostęp do Azure Resource Manager na potrzeby wdrożenia **szablonu,** jeśli jest to konieczne. 
4. Kliknij pozycję **Zapisz**.

    ![Zaawansowane zasady dostępu usługi Azure Key Vault](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Konfigurowanie klucza szyfrowania klucza (KEK)

Jeśli chcesz użyć klucza szyfrowania klucza (KEK) dla dodatkowej warstwy zabezpieczeń kluczy szyfrowania, dodaj klucz szyfrowania kluczy do magazynu kluczy. Po podano klucz szyfrowania klucza, Azure Disk Encryption używa tego klucza do opakowywania wpisów tajnych szyfrowania przed zapisem w Key Vault.

Nowy klucz szyfrowania kluczy można wygenerować za pomocą polecenia [az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create) interfejsu wiersza polecenia platformy Azure, polecenia cmdlet Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) lub [polecenia](https://portal.azure.com/)Azure Portal . Należy wygenerować typ klucza RSA. Azure Disk Encryption nie obsługuje jeszcze używania kluczy krzywej wielokropka.

Zamiast tego można zaimportować klucz szyfrowania kluczy z lokalnego modułu HSM do zarządzania kluczami. Aby uzyskać więcej informacji, [zobacz dokumentację Key Vault .](../articles/key-vault/keys/hsm-protected-keys.md)

Adresy URL klucza szyfrowania kluczy magazynu kluczy muszą być wersjonarowane. Platforma Azure wymusza to ograniczenie wersji. Aby uzyskać prawidłowy klucz tajny i adresy URL klucz-klucz-klucz, zobacz następujące przykłady:

* Przykład prawidłowego adresu URL tajnego: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Przykład prawidłowego adresu URL KEK: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure Disk Encryption nie obsługuje określania numerów portów w ramach wpisów tajnych magazynu kluczy i adresów URL klucza szyfrowania kluczy. Przykłady nie obsługiwanych adresów URL magazynu kluczy można znaleźć w następujących przykładach:

  * Dopuszczalny adres URL magazynu kluczy: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Nieakceptowalny adres URL magazynu kluczy: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj polecenia [az keyvault key create interfejsu wiersza](/cli/azure/keyvault/key#az_keyvault_key_create) polecenia platformy Azure, aby wygenerować nowy klucz szyfrowania kluczy i zapisać go w magazynie kluczy.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA
```

Zamiast tego możesz zaimportować klucz prywatny przy użyciu polecenia [az keyvault key import](/cli/azure/keyvault/key#az_keyvault_key_import) interfejsu wiersza polecenia platformy Azure:

W obu przypadkach należy podać nazwę klucza szyfrowania kluczy w parametrze [az vm encryption enable](/cli/azure/vm/encryption#az_vm_encryption_enable) interfejsu wiersza polecenia platformy Azure --key-encryption-key. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Użyj Azure PowerShell cmdlet [Add-AzKeyVaultKey,](/powershell/module/az.keyvault/add-azkeyvaultkey) aby wygenerować nowy klucz KEK i zapisać go w magazynie kluczy.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Zamiast tego możesz zaimportować klucz prywatny przy użyciu Azure PowerShell [az keyvault key import.](/cli/azure/keyvault/key#az_keyvault_key_import)

W obu przypadkach należy podać identyfikator magazynu kluczy kluczy KEK i adres URL klucza szyfrowania kluczy do parametrów Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) -KeyEncryptionKeyVaultId i -KeyEncryptionKeyUrl. Należy pamiętać, że w tym przykładzie założono, że używasz tego samego magazynu kluczy zarówno dla klucza szyfrowania dysku, jak i klucza KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```