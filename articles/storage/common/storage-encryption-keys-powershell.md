---
title: Konfigurowanie kluczy zarządzanych przez klienta za pomocą programu PowerShell
titleSuffix: Azure Storage
description: Dowiedz się, jak skonfigurować klucze zarządzane przez klienta do szyfrowania usługi Azure Storage za pomocą programu PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bfc2e256396904456a7ee0fd8b6173c00a5f53d7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456400"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Konfigurowanie kluczy zarządzanych przez klienta za pomocą usługi Azure Key Vault przy użyciu programu PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

W tym artykule pokazano, jak skonfigurować usługę Azure Key Vault przy użyciu kluczy zarządzanych przez klienta przy użyciu programu PowerShell. Aby dowiedzieć się, jak utworzyć magazyn kluczy przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [Szybki start: Ustawianie i pobieranie klucza tajnego z usługi Azure Key Vault przy użyciu programu PowerShell](../../key-vault/secrets/quick-create-powershell.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Przypisywanie tożsamości do konta magazynu

Aby włączyć klucze zarządzane przez klienta dla konta magazynu, należy najpierw przypisać do konta magazynu tożsamość zarządzaną przypisaną przez system. Użyjesz tej tożsamości zarządzanej, aby udzielić uprawnień konta magazynu, aby uzyskać dostęp do magazynu kluczy.

Aby przypisać tożsamość zarządzaną za pomocą programu PowerShell, zadzwoń do [programu Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Aby uzyskać więcej informacji na temat konfigurowania tożsamości zarządzanych przypisanych do systemu za pomocą programu PowerShell, zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure przy użyciu programu PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Tworzenie nowego magazynu kluczy

Aby utworzyć nowy magazyn kluczy za pomocą programu PowerShell, należy wywołać [new-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Magazyn kluczy używany do przechowywania kluczy zarządzanych przez klienta do szyfrowania usługi Azure Storage musi mieć włączone dwa ustawienia ochrony kluczy: **Usuwanie nietrwałe** i **Nie czyścić**.

Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

Aby dowiedzieć się, jak włączyć **usuwanie programów I** usuwanie programem Wyczyszczono w istniejącym magazynie kluczy za pomocą programu PowerShell, zobacz sekcje **"Włączanie usuwania nietrwale** i włączanie ochrony przed **przeczyszczaniem** w [sekcji Jak używać usuwania programowego w programie PowerShell](../../key-vault/general/soft-delete-powershell.md). **Do Not Purge**

## <a name="configure-the-key-vault-access-policy"></a>Konfigurowanie zasad dostępu do magazynu kluczy

Następnie skonfiguruj zasady dostępu dla magazynu kluczy, tak aby konto magazynu miało uprawnienia dostępu do niego. W tym kroku użyjesz tożsamości zarządzanej, która została wcześniej przypisana do konta magazynu.

Aby ustawić zasady dostępu dla magazynu kluczy, zadzwoń [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>Tworzenie nowego klucza

Następnie utwórz nowy klucz w magazynie kluczy. Aby utworzyć nowy klucz, zadzwoń do [add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

Tylko 2048-bitowe klucze RSA i RSA-HSM są obsługiwane za pomocą szyfrowania usługi Azure Storage. Aby uzyskać więcej informacji o kluczach, zobacz **Klucze magazynu kluczy,** wpisy tajne i certyfikaty usługi Azure Key Vault — informacje o [kluczach, wpisach tajnych i certyfikatach](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)usługi Azure Key Vault .

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurowanie szyfrowania za pomocą kluczy zarządzanych przez klienta

Domyślnie szyfrowanie usługi Azure Storage używa kluczy zarządzanych przez firmę Microsoft. W tym kroku skonfiguruj konto usługi Azure Storage, aby używało kluczy zarządzanych przez klienta i określ klucz do skojarzenia z kontem magazynu.

Zadzwoń [Set-AzStorageAccount,](/powershell/module/az.storage/set-azstorageaccount) aby zaktualizować ustawienia szyfrowania konta magazynu, jak pokazano w poniższym przykładzie. Dołącz opcję **-KeyvaultEncryption,** aby włączyć klucze zarządzane przez klienta dla konta magazynu. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Aktualizowanie wersji klucza

Podczas tworzenia nowej wersji klucza należy zaktualizować konto magazynu, aby użyć nowej wersji. Najpierw zadzwoń [do get-AzKeyVaultKey,](/powershell/module/az.keyvault/get-azkeyvaultkey) aby uzyskać najnowszą wersję klucza. Następnie zadzwoń [set-AzStorageAccount,](/powershell/module/az.storage/set-azstorageaccount) aby zaktualizować ustawienia szyfrowania konta magazynu, aby użyć nowej wersji klucza, jak pokazano w poprzedniej sekcji.

## <a name="use-a-different-key"></a>Używanie innego klucza

Aby zmienić klucz używany do szyfrowania usługi Azure Storage, zadzwoń [set-AzStorageAccount,](/powershell/module/az.storage/set-azstorageaccount) jak pokazano w [Konfigurowanie szyfrowania za pomocą kluczy zarządzanych przez klienta](#configure-encryption-with-customer-managed-keys) i podaj nową nazwę i wersję klucza. Jeśli nowy klucz znajduje się w innym magazynie kluczy, należy również zaktualizować identyfikator URI magazynu kluczy.

## <a name="revoke-customer-managed-keys"></a>Odwoływanie kluczy zarządzanych przez klienta

Jeśli uważasz, że klucz mógł zostać naruszony, możesz odwołać klucze zarządzane przez klienta, usuwając zasady dostępu do magazynu kluczy. Aby odwołać klucz zarządzany przez klienta, należy wywołać polecenie [Usuń-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Wyłączanie kluczy zarządzanych przez klienta

Po wyłączeniu kluczy zarządzanych przez klienta konto magazynu jest ponownie szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Aby wyłączyć klucze zarządzane przez klienta, zadzwoń [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) z `-StorageEncryption` opcją, jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie usługi Azure Storage dla danych w stanie spoczynku](storage-service-encryption.md)
- [Co to jest usługa Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
