---
title: Konfigurowanie kluczy zarządzanych przez klienta przy użyciu programu PowerShell
titleSuffix: Azure Storage
description: Dowiedz się, jak skonfigurować klucze zarządzane przez klienta do szyfrowania za pomocą usługi Azure Storage przy użyciu programu PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a3fdde755a5e024efead5c8861a1d5cd769b6d23
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87036832"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Konfigurowanie kluczy zarządzanych przez klienta za pomocą Azure Key Vault przy użyciu programu PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

W tym artykule opisano sposób konfigurowania Azure Key Vault za pomocą kluczy zarządzanych przez klienta przy użyciu programu PowerShell. Aby dowiedzieć się, jak utworzyć magazyn kluczy przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [Szybki Start: Ustawianie i pobieranie klucza tajnego z Azure Key Vault przy użyciu programu PowerShell](../../key-vault/secrets/quick-create-powershell.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Przypisywanie tożsamości do konta magazynu

Aby włączyć klucze zarządzane przez klienta dla konta magazynu, należy najpierw przypisać tożsamość zarządzaną przez system do konta magazynu. Ta tożsamość zarządzana zostanie użyta do nadania uprawnień kontu magazynu dostępu do magazynu kluczy.

Aby przypisać tożsamość zarządzaną przy użyciu programu PowerShell, wywołaj polecenie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Aby uzyskać więcej informacji na temat konfigurowania tożsamości zarządzanych przez system przy użyciu programu PowerShell, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu programu PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Utwórz nowy magazyn kluczy

Aby utworzyć nowy magazyn kluczy przy użyciu programu PowerShell, zainstaluj [program PowerShell w](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) wersji 2.0.0 lub nowszej. Następnie Wywołaj polecenie [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) , aby utworzyć nowy magazyn kluczy.

Magazyn kluczy używany do przechowywania kluczy zarządzanych przez klienta do szyfrowania usługi Azure Storage musi mieć włączone dwa ustawienia ochrony klucza, **usuwanie nietrwałe** i **nie przeczyszczanie**. W wersji 2.0.0 i nowszych modułu AZ. Key magazynu usuwanie nietrwałe jest domyślnie włączone podczas tworzenia nowego magazynu kluczy.

W poniższym przykładzie jest tworzony nowy magazyn kluczy z włączonym **niewygładzonym usuwaniem** i **nie przeczyszczam** właściwości. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Aby dowiedzieć się, jak włączyć **usuwanie nietrwałe** i **nie czyścić** w istniejącym magazynie kluczy za pomocą programu PowerShell, zobacz sekcję zatytułowaną **Włączanie usuwania nietrwałego** i **Włączanie ochrony przed przeczyszczaniem** w artykule [jak używać narzędzia unsoft-Delete](../../key-vault/general/soft-delete-powershell.md)w programie PowerShell.

## <a name="configure-the-key-vault-access-policy"></a>Konfigurowanie zasad dostępu magazynu kluczy

Następnie skonfiguruj zasady dostępu dla magazynu kluczy, aby konto magazynu miało uprawnienia dostępu do niego. W tym kroku zostanie użyta zarządzana tożsamość, która została wcześniej przypisana do konta magazynu.

Aby ustawić zasady dostępu dla magazynu kluczy, wywołaj polecenie [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>Utwórz nowy klucz

Następnie utwórz nowy klucz w magazynie kluczy. Aby utworzyć nowy klucz, wywołaj polecenie [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

Szyfrowanie za pomocą usługi Azure Storage obsługuje klucze RSA i RSA-HSM o rozmiarach 2048, 3072 i 4096. Aby uzyskać więcej informacji na temat kluczy, zobacz **Key Vault klucze** w temacie [informacje Azure Key Vault klucze, wpisy tajne i certyfikaty](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta

Domyślnie szyfrowanie usługi Azure Storage używa kluczy zarządzanych przez firmę Microsoft. W tym kroku Skonfiguruj konto usługi Azure Storage do używania kluczy zarządzanych przez klienta z usługą Azure Key Vault, a następnie określ klucz, który ma zostać skojarzony z kontem magazynu.

Podczas konfigurowania szyfrowania przy użyciu kluczy zarządzanych przez klienta można automatycznie obrócić klucz używany do szyfrowania, gdy wersja zostanie zmieniona w skojarzonym magazynie kluczy. Alternatywnie można jawnie określić wersję klucza do użycia podczas szyfrowania, dopóki wersja klucza nie zostanie zaktualizowana ręcznie.

### <a name="configure-encryption-for-automatic-rotation-of-customer-managed-keys"></a>Konfigurowanie szyfrowania na potrzeby automatycznego rotacji kluczy zarządzanych przez klienta

Aby skonfigurować szyfrowanie automatycznego rotacji kluczy zarządzanych przez klienta, zainstaluj moduł [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) w wersji 2.0.0 lub nowszej.

Aby automatycznie obrócić klucze zarządzane przez klienta, należy pominąć wersję klucza podczas konfigurowania kluczy zarządzanych przez klienta dla konta magazynu. Wywołaj polecenie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) , aby zaktualizować ustawienia szyfrowania konta magazynu, jak pokazano w poniższym przykładzie, i Dołącz opcję **-KeyvaultEncryption** , aby włączyć klucze zarządzane przez klienta dla konta magazynu. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

### <a name="configure-encryption-for-manual-rotation-of-key-versions"></a>Konfigurowanie szyfrowania do ręcznego rotacji wersji kluczowych

Aby jawnie określić wersję klucza do użycia na potrzeby szyfrowania, należy podać wersję klucza podczas konfigurowania szyfrowania przy użyciu kluczy zarządzanych przez klienta dla konta magazynu. Wywołaj polecenie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) , aby zaktualizować ustawienia szyfrowania konta magazynu, jak pokazano w poniższym przykładzie, i Dołącz opcję **-KeyvaultEncryption** , aby włączyć klucze zarządzane przez klienta dla konta magazynu. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

Po ręcznym obróceniu wersji klucza należy zaktualizować ustawienia szyfrowania konta magazynu, aby użyć nowej wersji. Najpierw należy wywołać polecenie [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) , aby uzyskać najnowszą wersję klucza. Następnie Wywołaj polecenie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) , aby zaktualizować ustawienia szyfrowania konta magazynu tak, aby korzystały z nowej wersji klucza, jak pokazano w poprzednim przykładzie.

## <a name="use-a-different-key"></a>Użyj innego klucza

Aby zmienić klucz używany do szyfrowania usługi Azure Storage, wywołaj polecenie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) , jak pokazano na stronie [Konfigurowanie szyfrowania z kluczami zarządzanymi przez klienta](#configure-encryption-with-customer-managed-keys) i podaj nową nazwę klucza i wersję. Jeśli nowy klucz znajduje się w innym magazynie kluczy, należy również zaktualizować identyfikator URI magazynu kluczy.

## <a name="revoke-customer-managed-keys"></a>Odwołaj klucze zarządzane przez klienta

Klucze zarządzane przez klienta można odwołać przez usunięcie zasad dostępu magazynu kluczy. Aby odwołać klucz zarządzany przez klienta, wywołaj polecenie [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) , jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Wyłącz klucze zarządzane przez klienta

Po wyłączeniu kluczy zarządzanych przez klienta konto magazynu będzie ponownie szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby wyłączyć klucze zarządzane przez klienta, wywołaj polecenie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) z `-StorageEncryption` opcją, jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](storage-service-encryption.md)
- [Co to jest Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
