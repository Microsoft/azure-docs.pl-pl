---
title: Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta przechowywanych w zarządzanym Azure Key Vault HSM (wersja zapoznawcza)
titleSuffix: Azure Storage
description: Dowiedz się, jak skonfigurować szyfrowanie usługi Azure Storage przy użyciu kluczy zarządzanych przez klienta przechowywanych w zarządzanym przez klienta Azure Key Vault HSM (wersja zapoznawcza) przy użyciu interfejsu wiersza polecenia platformy Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: f9b40c934cb428a31a3feb77195518d5351818d7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785365"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta przechowywanych w zarządzanym Azure Key Vault HSM (wersja zapoznawcza)

Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w spoczynku. Domyślnie dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, możesz zarządzać własnymi kluczami. Klucze zarządzane przez klienta muszą być przechowywane w programie Azure Key Vault lub Key Vault Zarządzany sprzętowy model zabezpieczeń (HSM) (wersja zapoznawcza). Zarządzany Azure Key Vault HSM to moduł HSM zweryfikowany w programie FIPS 140-2 poziom 3.

W tym artykule pokazano, jak skonfigurować szyfrowanie przy użyciu kluczy zarządzanych przez klienta przechowywanych w zarządzanym modułze HSM przy użyciu interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się, jak skonfigurować szyfrowanie przy użyciu kluczy zarządzanych przez klienta przechowywanych w magazynie kluczy, zobacz Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta przechowywanych w [Azure Key Vault](customer-managed-keys-configure-key-vault.md).

> [!IMPORTANT]
>
> Szyfrowanie kluczami zarządzanymi przez klienta przechowywanymi w zarządzanym Azure Key Vault HSM jest obecnie dostępne w wersji **ZAPOZNAWCZEJ.** Dodatkowe [warunki użytkowania wersji zapoznawczych](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) usługi Microsoft Azure dotyczą postanowień prawnych, które dotyczą funkcji platformy Azure, które są w wersji beta, wersji zapoznawczej lub w inny sposób nie zostały jeszcze wydane jako ogólnie dostępne.
>
> Azure Key Vault i Azure Key Vault HSM obsługują te same interfejsy API i interfejsy zarządzania dla konfiguracji.

## <a name="assign-an-identity-to-the-storage-account"></a>Przypisywanie tożsamości do konta magazynu

Najpierw przypisz przypisaną przez system tożsamość zarządzaną do konta magazynu. Użyjesz tej tożsamości zarządzanej, aby udzielić kontu magazynu uprawnień dostępu do zarządzanego modułu HSM. Aby uzyskać więcej informacji na temat tożsamości zarządzanych przypisanych przez system, zobacz [Czym są tożsamości zarządzane dla zasobów platformy Azure?](../../active-directory/managed-identities-azure-resources/overview.md).

Aby przypisać tożsamość zarządzaną przy użyciu interfejsu wiersza polecenia platformy Azure, [wywołaj az storage account update](/cli/azure/storage/account#az_storage_account_update). Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>Przypisywanie roli do konta magazynu w celu uzyskania dostępu do zarządzanego modułu HSM

Następnie przypisz rolę Szyfrowanie usługi kryptograficznej zarządzanego modułu **HSM** do tożsamości zarządzanej konta magazynu, aby konto magazynu ma uprawnienia do zarządzanego modułu HSM. Firma Microsoft zaleca, aby zawęzimy zakres przypisania roli do poziomu pojedynczego klucza, aby udzielić tożsamości zarządzanej jak najmniej uprawnień.

Aby utworzyć przypisanie roli dla konta magazynu, wywołaj funkcję [az key vault role assignment create.](/cli/azure/role/assignment#az_role_assignment_create) Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami.
  
```azurecli
storage_account_principal = $(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault role assignment create \
    --hsm-name <hsm-name> \
    --role "Managed HSM Crypto Service Encryption" \
    --assignee $storage_account_principal \
    --scope /keys/<key-name>
```

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>Konfigurowanie szyfrowania za pomocą klucza w zarządzanym hsm

Na koniec skonfiguruj szyfrowanie usługi Azure Storage przy użyciu kluczy zarządzanych przez klienta, aby użyć klucza przechowywanego w zarządzanym hsm. Obsługiwane typy kluczy obejmują klucze RSA-HSM o rozmiarach 2048, 3072 i 4096. Aby dowiedzieć się, jak utworzyć klucz w zarządzanym hsm, zobacz [Tworzenie klucza HSM.](../../key-vault/managed-hsm/key-management.md#create-an-hsm-key)

Zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.12.0 lub nowszej, aby skonfigurować szyfrowanie do używania klucza zarządzanego przez klienta w zarządzanym hsm. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

Aby automatycznie zaktualizować wersję klucza zarządzanego przez klienta, należy pominąć wersję klucza podczas konfigurowania szyfrowania przy użyciu kluczy zarządzanych przez klienta dla konta magazynu. Wywołaj [pozycję az storage account update,](/cli/azure/storage/account#az_storage_account_update) aby zaktualizować ustawienia szyfrowania konta magazynu, jak pokazano w poniższym przykładzie. Uwzględnij `--encryption-key-source parameter` wartość i ustaw ją na , aby włączyć klucze zarządzane przez klienta dla `Microsoft.Keyvault` konta. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami.

```azurecli
hsmurl = $(az keyvault show \
    --hsm-name <hsm-name> \
    --query properties.hsmUri \
    --output tsv)

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Aby ręcznie zaktualizować wersję klucza zarządzanego przez klienta, uwzględnij wersję klucza podczas konfigurowania szyfrowania dla konta magazynu:

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

W przypadku ręcznej aktualizacji wersji klucza należy zaktualizować ustawienia szyfrowania konta magazynu, aby korzystać z nowej wersji. Najpierw przeszukaj zapytanie o numer URI magazynu kluczy, wywołując [element az keyvault show](/cli/azure/keyvault#az_keyvault_show)i dla wersji klucza, wywołując element az [keyvault key list-versions](/cli/azure/keyvault/key#az_keyvault_key_list_versions). Następnie [wywołaj element az storage account update,](/cli/azure/storage/account#az_storage_account_update) aby zaktualizować ustawienia szyfrowania konta magazynu w celu użycia nowej wersji klucza, jak pokazano w poprzednim przykładzie.

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](storage-service-encryption.md)
- [Klucze zarządzane przez klienta na potrzeby szyfrowania w usłudze Azure Storage](customer-managed-keys-overview.md)
