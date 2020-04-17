---
title: Konfigurowanie kluczy zarządzanych przez klienta za pomocą interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Storage
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do konfigurowania kluczy zarządzanych przez klienta za pomocą usługi Azure Key Vault dla szyfrowania usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 796e3b3f46bc83b776826baf6e078c696eda543b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456775"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Konfigurowanie kluczy zarządzanych przez klienta za pomocą usługi Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

W tym artykule pokazano, jak skonfigurować usługę Azure Key Vault przy użyciu kluczy zarządzanych przez klienta przy użyciu interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się, jak utworzyć magazyn kluczy przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [Szybki start: Ustawianie i pobieranie klucza tajnego z usługi Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure](../../key-vault/secrets/quick-create-cli.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Przypisywanie tożsamości do konta magazynu

Aby włączyć klucze zarządzane przez klienta dla konta magazynu, należy najpierw przypisać do konta magazynu tożsamość zarządzaną przypisaną przez system. Użyjesz tej tożsamości zarządzanej, aby udzielić uprawnień konta magazynu, aby uzyskać dostęp do magazynu kluczy.

Aby przypisać tożsamość zarządzaną przy użyciu interfejsu wiersza polecenia platformy Azure, należy [wywołać aktualizację konta magazynu az](/cli/azure/storage/account#az-storage-account-update). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Aby uzyskać więcej informacji na temat konfigurowania tożsamości zarządzanych przypisanych do systemu za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)

## <a name="create-a-new-key-vault"></a>Tworzenie nowego magazynu kluczy

Magazyn kluczy używany do przechowywania kluczy zarządzanych przez klienta do szyfrowania usługi Azure Storage musi mieć włączone dwa ustawienia ochrony kluczy: **Usuwanie nietrwałe** i **Nie czyścić**. Aby utworzyć nowy magazyn kluczy przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure z włączonymi tymi ustawieniami, wykonaj następujące polecenia. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

Aby utworzyć nowy magazyn kluczy przy użyciu interfejsu wiersza polecenia platformy Azure, należy wywołać [az keyvault create](/cli/azure/keyvault#az-keyvault-create). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

Aby dowiedzieć się, jak włączyć **usuwanie programów trwałych** i **nie czyścić** w istniejącym magazynie kluczy za pomocą interfejsu wiersza polecenia platformy Azure, zobacz sekcje **"Włączanie usuwania nietrwałego** i **włączanie ochrony przed przeczyszczaniem** w [sekcji Jak używać usuwania nietrwałego za pomocą interfejsu wiersza polecenia.](../../key-vault/general/soft-delete-cli.md)

## <a name="configure-the-key-vault-access-policy"></a>Konfigurowanie zasad dostępu do magazynu kluczy

Następnie skonfiguruj zasady dostępu dla magazynu kluczy, tak aby konto magazynu miało uprawnienia dostępu do niego. W tym kroku użyjesz tożsamości zarządzanej, która została wcześniej przypisana do konta magazynu.

Aby ustawić zasady dostępu dla magazynu kluczy, [wywołanie az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

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

## <a name="create-a-new-key"></a>Tworzenie nowego klucza

Następnie utwórz klucz w magazynie kluczy. Aby utworzyć klucz, należy wywołać [utworzenie klawisza az keyvault](/cli/azure/keyvault/key#az-keyvault-key-create). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

Tylko 2048-bitowe klucze RSA i RSA-HSM są obsługiwane za pomocą szyfrowania usługi Azure Storage. Aby uzyskać więcej informacji o kluczach, zobacz **Klucze magazynu kluczy,** wpisy tajne i certyfikaty usługi Azure Key Vault — informacje o [kluczach, wpisach tajnych i certyfikatach](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)usługi Azure Key Vault .

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurowanie szyfrowania za pomocą kluczy zarządzanych przez klienta

Domyślnie szyfrowanie usługi Azure Storage używa kluczy zarządzanych przez firmę Microsoft. Skonfiguruj konto usługi Azure Storage dla kluczy zarządzanych przez klienta i określ klucz do skojarzenia z kontem magazynu.

Aby zaktualizować ustawienia szyfrowania konta magazynu, zadzwoń do [aktualizacji konta magazynu az,](/cli/azure/storage/account#az-storage-account-update)jak pokazano w poniższym przykładzie. Dołącz `--encryption-key-source` parametr i ustaw `Microsoft.Keyvault` go, aby włączyć klucze zarządzane przez klienta dla konta magazynu. W przykładzie również kwerendy dla identyfikatora URI magazynu kluczy i najnowszej wersji klucza, które wartości są potrzebne do skojarzenia klucza z kontem magazynu. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

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

## <a name="update-the-key-version"></a>Aktualizowanie wersji klucza

Podczas tworzenia nowej wersji klucza należy zaktualizować konto magazynu, aby użyć nowej wersji. Najpierw kwerenda dla identyfikatora URI magazynu kluczy, wywołując [program az keyvault show](/cli/azure/keyvault#az-keyvault-show), a dla wersji klucza, wywołując [az keyvault key list-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Następnie zadzwoń do [aktualizacji konta magazynu az,](/cli/azure/storage/account#az-storage-account-update) aby zaktualizować ustawienia szyfrowania konta magazynu, aby użyć nowej wersji klucza, jak pokazano w poprzedniej sekcji.

## <a name="use-a-different-key"></a>Używanie innego klucza

Aby zmienić klucz używany do szyfrowania usługi Azure Storage, wywołaj [aktualizację konta magazynu AZ,](/cli/azure/storage/account#az-storage-account-update) jak pokazano w [temacie Konfigurowanie szyfrowania za pomocą kluczy zarządzanych przez klienta](#configure-encryption-with-customer-managed-keys) i podaj nową nazwę i wersję klucza. Jeśli nowy klucz znajduje się w innym magazynie kluczy, należy również zaktualizować identyfikator URI magazynu kluczy.

## <a name="revoke-customer-managed-keys"></a>Odwoływanie kluczy zarządzanych przez klienta

Jeśli uważasz, że klucz mógł zostać naruszony, możesz odwołać klucze zarządzane przez klienta, usuwając zasady dostępu do magazynu kluczy. Aby odwołać klucz zarządzany przez klienta, należy wywołać polecenie [az keyvault delete-policy,](/cli/azure/keyvault#az-keyvault-delete-policy) jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>Wyłączanie kluczy zarządzanych przez klienta

Po wyłączeniu kluczy zarządzanych przez klienta konto magazynu jest ponownie szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Aby wyłączyć klucze zarządzane przez klienta, należy `--encryption-key-source parameter` wywołać [aktualizację konta magazynu az](/cli/azure/storage/account#az-storage-account-update) i ustawić na `Microsoft.Storage`, jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie usługi Azure Storage dla danych w stanie spoczynku](storage-service-encryption.md) 
- [Co to jest usługa Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
