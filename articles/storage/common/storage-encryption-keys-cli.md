---
title: Konfigurowanie kluczy zarządzanych przez klienta przy użyciu interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Storage
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure skonfigurować klucze zarządzane przez klienta za pomocą Azure Key Vault na potrzeby szyfrowania usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 893c953562e0d150bd5e8110e5473fd24a2aff83
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176349"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Konfigurowanie kluczy zarządzanych przez klienta za pomocą Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

W tym artykule opisano sposób konfigurowania Azure Key Vault za pomocą kluczy zarządzanych przez klienta przy użyciu interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się, jak utworzyć magazyn kluczy za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Szybki Start: Ustawianie i pobieranie klucza tajnego z Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure](../../key-vault/secrets/quick-create-cli.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Przypisywanie tożsamości do konta magazynu

Aby włączyć klucze zarządzane przez klienta dla konta magazynu, należy najpierw przypisać tożsamość zarządzaną przez system do konta magazynu. Ta tożsamość zarządzana zostanie użyta do nadania uprawnień kontu magazynu dostępu do magazynu kluczy.

Aby przypisać tożsamość zarządzaną za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage account Update](/cli/azure/storage/account#az-storage-account-update). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Aby uzyskać więcej informacji na temat konfigurowania tożsamości zarządzanych przez system za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Utwórz nowy magazyn kluczy

Magazyn kluczy używany do przechowywania kluczy zarządzanych przez klienta do szyfrowania usługi Azure Storage musi mieć włączone dwa ustawienia ochrony klucza, **usuwanie nietrwałe** i **nie przeczyszczanie**. Aby utworzyć nowy magazyn kluczy przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure z włączonymi tymi ustawieniami, uruchom następujące polecenia. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

Aby utworzyć nowy magazyn kluczy przy użyciu interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Key webcreate](/cli/azure/keyvault#az-keyvault-create). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

Aby dowiedzieć się, jak włączyć **usuwanie nietrwałe** i **nie czyścić** w istniejącym magazynie kluczy za pomocą interfejsu wiersza polecenia platformy Azure, zapoznaj się z sekcjami zatytułowanymi **Włączanie usuwania nietrwałego** i **Włączanie ochrony przed przeczyszczaniem** w artykule [jak używać programu unsoft-Delete with CLI](../../key-vault/general/soft-delete-cli.md).

## <a name="configure-the-key-vault-access-policy"></a>Konfigurowanie zasad dostępu magazynu kluczy

Następnie skonfiguruj zasady dostępu dla magazynu kluczy, aby konto magazynu miało uprawnienia dostępu do niego. W tym kroku zostanie użyta zarządzana tożsamość, która została wcześniej przypisana do konta magazynu.

Aby ustawić zasady dostępu dla magazynu kluczy, wywołaj polecenie [AZ KeyBinding Set-Policy](/cli/azure/keyvault#az-keyvault-set-policy). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Utwórz nowy klucz

Następnie Utwórz klucz w magazynie kluczy. Aby utworzyć klucz, wywołaj [AZ Key magazynu Create](/cli/azure/keyvault/key#az-keyvault-key-create). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

Szyfrowanie za pomocą usługi Azure Storage jest obsługiwane tylko przez 2048-bitowe klucze RSA i RSA-HSM. Aby uzyskać więcej informacji na temat kluczy, zobacz **Key Vault klucze** w temacie [informacje Azure Key Vault klucze, wpisy tajne i certyfikaty](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta

Domyślnie szyfrowanie usługi Azure Storage używa kluczy zarządzanych przez firmę Microsoft. Skonfiguruj konto usługi Azure Storage dla kluczy zarządzanych przez klienta i określ klucz, który ma zostać skojarzony z kontem magazynu.

Aby zaktualizować ustawienia szyfrowania konta magazynu, wywołaj polecenie [AZ Storage account Update](/cli/azure/storage/account#az-storage-account-update), jak pokazano w poniższym przykładzie. Uwzględnij `--encryption-key-source` parametr i ustaw go na `Microsoft.Keyvault` , aby włączyć klucze zarządzane przez klienta dla konta magazynu. Przykładem jest również zapytanie dotyczące identyfikatora URI magazynu kluczy i najnowszej wersji klucza, obie wartości są konieczne do skojarzenia klucza z kontem magazynu. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>Zaktualizuj wersję klucza

Podczas tworzenia nowej wersji klucza należy zaktualizować konto magazynu, aby używało nowej wersji. Najpierw należy wykonać zapytanie dotyczące identyfikatora URI magazynu kluczy przez wywołanie [AZ Key magazyn show](/cli/azure/keyvault#az-keyvault-show)i dla wersji klucza przez wywołanie [AZ Key magazynu list-Versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Następnie Wywołaj [AZ Storage account Update](/cli/azure/storage/account#az-storage-account-update) , aby zaktualizować ustawienia szyfrowania konta magazynu, aby użyć nowej wersji klucza, jak pokazano w poprzedniej sekcji.

## <a name="use-a-different-key"></a>Użyj innego klucza

Aby zmienić klucz używany do szyfrowania usługi Azure Storage, wywołaj polecenie [AZ Storage account Update](/cli/azure/storage/account#az-storage-account-update) , jak pokazano w temacie [Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta](#configure-encryption-with-customer-managed-keys) i podaj nową nazwę klucza i wersję. Jeśli nowy klucz znajduje się w innym magazynie kluczy, należy również zaktualizować identyfikator URI magazynu kluczy.

## <a name="revoke-customer-managed-keys"></a>Odwołaj klucze zarządzane przez klienta

Jeśli uważasz, że klucz mógł zostać naruszony, możesz odwołać klucze zarządzane przez klienta, usuwając zasady dostępu do magazynu kluczy. Aby odwołać klucz zarządzany przez klienta, wywołaj polecenie [AZ Key Delete-Policy](/cli/azure/keyvault#az-keyvault-delete-policy) , jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>Wyłącz klucze zarządzane przez klienta

Po wyłączeniu kluczy zarządzanych przez klienta konto magazynu będzie ponownie szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby wyłączyć klucze zarządzane przez klienta, wywołaj polecenie [AZ Storage account Update](/cli/azure/storage/account#az-storage-account-update) i `--encryption-key-source parameter` Ustaw `Microsoft.Storage`polecenie na, jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie usługi Azure Storage dla danych magazynowanych](storage-service-encryption.md) 
- [Co to jest Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
