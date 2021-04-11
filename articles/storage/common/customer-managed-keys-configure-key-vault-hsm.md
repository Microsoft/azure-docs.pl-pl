---
title: Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta, przechowywanych w Azure Key Vault zarządzanym module HSM (wersja zapoznawcza)
titleSuffix: Azure Storage
description: Dowiedz się, jak skonfigurować szyfrowanie usługi Azure Storage za pomocą kluczy zarządzanych przez klienta w Azure Key Vault zarządzanym module HSM (wersja zapoznawcza) przy użyciu interfejsu wiersza polecenia platformy Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: ea51c1f7fcfce5b795965eab2f9c03a820a6ab03
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059364"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta, przechowywanych w Azure Key Vault zarządzanym module HSM (wersja zapoznawcza)

Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w stanie spoczynku. Domyślnie dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, możesz zarządzać własnymi kluczami. Klucze zarządzane przez klienta muszą być przechowywane w Azure Key Vault lub Key Vault zarządzanym sprzętowym modelem zabezpieczeń (HSM) (wersja zapoznawcza). Azure Key Vault zarządzanym modułem HSM jest sprawdzony 140-2 Level 3 w trybie FIPS.

W tym artykule pokazano, jak skonfigurować szyfrowanie za pomocą kluczy zarządzanych przez klienta przechowywanych w zarządzanym module HSM przy użyciu interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się, jak skonfigurować szyfrowanie za pomocą kluczy zarządzanych przez klienta przechowywanych w magazynie kluczy, zobacz [Konfigurowanie szyfrowania z kluczami zarządzanymi przez klienta, które są przechowywane w Azure Key Vault](customer-managed-keys-configure-key-vault.md).

> [!IMPORTANT]
>
> Szyfrowanie z kluczami zarządzanymi przez klienta przechowywanych w Azure Key Vault zarządzanym module HSM jest obecnie w **wersji zapoznawczej**. Zapoznaj się z [dodatkowymi postanowieniami dotyczącymi](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych w Microsoft Azure wersjach zapoznawczych, które mają zastosowanie do funkcji platformy Azure w wersjach beta, Preview lub innych, które nie zostały jeszcze ogólnie udostępnione.
>
> Azure Key Vault i Azure Key Vault zarządzanym modułem HSM obsługują te same interfejsy API i interfejsów zarządzania na potrzeby konfiguracji.

## <a name="assign-an-identity-to-the-storage-account"></a>Przypisywanie tożsamości do konta magazynu

Najpierw Przypisz tożsamość zarządzaną przypisaną przez system do konta magazynu. Ta tożsamość zarządzana zostanie użyta do nadania uprawnień kontu magazynu dostępu do zarządzanego modułu HSM. Aby uzyskać więcej informacji na temat tożsamości zarządzanych przypisanych do systemu, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure?](../../active-directory/managed-identities-azure-resources/overview.md).

Aby przypisać tożsamość zarządzaną za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage account Update](/cli/azure/storage/account#az-storage-account-update). Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>Przypisywanie roli do konta magazynu w celu uzyskania dostępu do zarządzanego modułu HSM

Następnie przypisz rolę **szyfrowania zarządzanej usługi kryptograficznej modułu HSM** do tożsamości zarządzanej konta magazynu, aby konto magazynu miało uprawnienia do zarządzanego modułu HSM. Firma Microsoft zaleca, aby ograniczyć przypisanie roli do poziomu pojedynczego klucza w celu udzielenia najmniejszego możliwego uprawnienia do zarządzanej tożsamości.

Aby utworzyć przypisanie roli dla konta magazynu, wywołaj [AZ Key Storage role Create](/cli/azure/role/assignment#az_role_assignment_create). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.
  
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

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>Skonfiguruj szyfrowanie za pomocą klucza w zarządzanym module HSM

Na koniec Skonfiguruj szyfrowanie usługi Azure Storage za pomocą kluczy zarządzanych przez klienta, aby użyć klucza przechowywanego w zarządzanym module HSM. Obsługiwane typy kluczy obejmują klucze HSM modułu RSA o rozmiarach 2048, 3072 i 4096. Aby dowiedzieć się, jak utworzyć klucz w zarządzanym module HSM, zobacz [Tworzenie klucza HSM](../../key-vault/managed-hsm/key-management.md#create-an-hsm-key).

Zainstaluj interfejs wiersza polecenia platformy Azure 2.12.0 lub nowszy, aby skonfigurować szyfrowanie do korzystania z klucza zarządzanego przez klienta w zarządzanym module HSM. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Aby automatycznie zaktualizować wersję klucza dla klucza zarządzanego przez klienta, należy pominąć wersję klucza podczas konfigurowania szyfrowania z kluczami zarządzanymi przez klienta dla konta magazynu. Wywołaj [AZ Storage account Update](/cli/azure/storage/account#az_storage_account_update) , aby zaktualizować ustawienia szyfrowania konta magazynu, jak pokazano w poniższym przykładzie. Dołącz i ustaw tę opcję, `--encryption-key-source parameter` Aby `Microsoft.Keyvault` włączyć klucze zarządzane przez klienta dla konta. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

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

Aby ręcznie zaktualizować wersję klucza zarządzanego przez klienta, należy uwzględnić wersję klucza podczas konfigurowania szyfrowania dla konta magazynu:

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

W przypadku ręcznej aktualizacji wersji klucza należy zaktualizować ustawienia szyfrowania konta magazynu, aby użyć nowej wersji. Najpierw należy wykonać zapytanie dotyczące identyfikatora URI magazynu kluczy przez wywołanie [AZ Key magazyn show](/cli/azure/keyvault#az-keyvault-show)i dla wersji klucza przez wywołanie [AZ Key magazynu list-Versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Następnie Wywołaj [AZ Storage account Update](/cli/azure/storage/account#az-storage-account-update) , aby zaktualizować ustawienia szyfrowania konta magazynu, aby użyć nowej wersji klucza, jak pokazano w poprzednim przykładzie.

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](storage-service-encryption.md)
- [Klucze zarządzane przez klienta dla szyfrowania usługi Azure Storage](customer-managed-keys-overview.md)
