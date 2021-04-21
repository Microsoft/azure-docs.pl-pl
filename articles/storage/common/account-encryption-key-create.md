---
title: Tworzenie konta obsługującego klucze zarządzane przez klienta dla tabel i kolejek
titleSuffix: Azure Storage
description: Dowiedz się, jak utworzyć konto magazynu, które obsługuje konfigurowanie kluczy zarządzanych przez klienta dla tabel i kolejek. Użyj interfejsu wiersza polecenia platformy Azure Azure Resource Manager szablonu magazynu, aby utworzyć konto magazynu, które korzysta z klucza szyfrowania konta na użytek szyfrowania usługi Azure Storage. Następnie można skonfigurować klucze zarządzane przez klienta dla konta.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 4c86811ee72d2713fced6320a17d1ccde1866d99
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769953"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Tworzenie konta obsługującego klucze zarządzane przez klienta dla tabel i kolejek

Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w spoczynku. Domyślnie usługi Queue Storage i Table Storage używają klucza, który jest ograniczony do usługi i zarządzany przez firmę Microsoft. Możesz również użyć kluczy zarządzanych przez klienta do szyfrowania danych kolejki lub tabeli. Aby używać kluczy zarządzanych przez klienta z kolejkami i tabelami, należy najpierw utworzyć konto magazynu, które używa klucza szyfrowania, który jest ograniczony do konta, a nie do usługi. Po utworzeniu konta, które używa klucza szyfrowania konta dla danych kolejek i tabel, można skonfigurować klucze zarządzane przez klienta dla tego konta magazynu.

W tym artykule opisano sposób tworzenia konta magazynu, które opiera się na kluczu o zakresie konta. Po pierwszym utworzeniu konta firma Microsoft używa klucza konta do szyfrowania danych na koncie, a firma Microsoft zarządza kluczem. Następnie można skonfigurować klucze zarządzane przez klienta dla konta, aby korzystać z tych korzyści, w tym zapewniać własne klucze, aktualizować wersję klucza, obracać klucze i odwoływał mechanizmy kontroli dostępu.

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Tworzenie konta, które używa klucza szyfrowania konta

Należy skonfigurować nowe konto magazynu do używania klucza szyfrowania konta dla kolejek i tabel w momencie tworzenia konta magazynu. Nie można zmienić zakresu klucza szyfrowania po utworzeniu konta.

Konto magazynu musi być typu ogólnego przeznaczenia w wersji 2. Konto magazynu można utworzyć i skonfigurować tak, aby polegało na kluczu szyfrowania konta przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure Resource Manager szablonu.

> [!NOTE]
> Opcjonalnie można skonfigurować tylko magazyn kolejek i tabel do szyfrowania danych przy użyciu klucza szyfrowania konta podczas tworzenia konta magazynu. Magazyn obiektów blob i Azure Files zawsze używają klucza szyfrowania konta do szyfrowania danych.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby użyć programu PowerShell do utworzenia konta magazynu, które opiera się na kluczu szyfrowania konta, upewnij się, że zainstalowano moduł Azure PowerShell w wersji 3.4.0 lub nowszej. Aby uzyskać więcej informacji, zobacz [Instalowanie Azure PowerShell modułu](/powershell/azure/install-az-ps).

Następnie utwórz konto magazynu ogólnego przeznaczenia w wersji 2, wywołując polecenie [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) z odpowiednimi parametrami:

- Uwzględnij opcję i ustaw jej wartość na , aby używać klucza szyfrowania konta `-EncryptionKeyTypeForQueue` do szyfrowania danych w magazynie `Account` kolejek.
- Uwzględnij opcję i ustaw jej wartość na , aby używać klucza `-EncryptionKeyTypeForTable` szyfrowania konta do szyfrowania danych w magazynie `Account` tabel.

W poniższym przykładzie pokazano, jak utworzyć konto magazynu ogólnego przeznaczenia w wersji 2, które jest skonfigurowane na potrzeby magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS) i który używa klucza szyfrowania konta do szyfrowania danych zarówno dla magazynu kolejek, jak i magazynu tabel. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby użyć interfejsu wiersza polecenia platformy Azure do utworzenia konta magazynu, które opiera się na kluczu szyfrowania konta, upewnij się, że zainstalowano interfejs wiersza polecenia platformy Azure w wersji 2.0.80 lub nowszej. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

Następnie utwórz konto magazynu ogólnego przeznaczenia w wersji 2, wywołując [polecenie az storage account create](/cli/azure/storage/account#az_storage_account_create) z odpowiednimi parametrami:

- Uwzględnij opcję i ustaw jej wartość na , aby używać klucza szyfrowania konta `--encryption-key-type-for-queue` do szyfrowania danych w magazynie `Account` kolejek.
- Uwzględnij opcję i ustaw jej wartość na , aby używać klucza `--encryption-key-type-for-table` szyfrowania konta do szyfrowania danych w magazynie `Account` tabel.

W poniższym przykładzie pokazano, jak utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 skonfigurowane na potrzeby magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS), które używa klucza szyfrowania konta do szyfrowania danych zarówno dla magazynu kolejek, jak i tabel. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Szablon](#tab/template)

Poniższy przykład JSON tworzy konto magazynu ogólnego przeznaczenia w wersji 2, które jest skonfigurowane na potrzeby magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS) i które używa klucza szyfrowania konta do szyfrowania danych zarówno dla magazynu kolejek, jak i tabel. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

Po utworzeniu konta, które opiera się na kluczu szyfrowania konta, można skonfigurować klucze zarządzane przez klienta, które są przechowywane w programie Azure Key Vault lub w zarządzanym sprzętowym modelu zabezpieczeń (HSM) usługi Key Vault (wersja zapoznawcza). Aby dowiedzieć się, jak przechowywać klucze zarządzane przez klienta w magazynie kluczy, zobacz Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta przechowywanych w [Azure Key Vault](customer-managed-keys-configure-key-vault.md). Aby dowiedzieć się, jak przechowywać klucze zarządzane przez klienta w zarządzanym hsm, zobacz Configure [encryption with customer-managed keys stored in Azure Key Vault Managed HSM (preview) (Konfigurowanie](customer-managed-keys-configure-key-vault-hsm.md)szyfrowania przy użyciu kluczy zarządzanych przez klienta w zarządzanym Azure Key Vault HSM (wersja zapoznawcza).

## <a name="verify-the-account-encryption-key"></a>Weryfikowanie klucza szyfrowania konta

Aby sprawdzić, czy usługa na koncie magazynu używa klucza szyfrowania konta, wywołaj polecenie [az storage account](/cli/azure/storage/account#az_storage_account_show) interfejsu wiersza polecenia platformy Azure. To polecenie zwraca zestaw właściwości konta magazynu i ich wartości. Poszukaj pola dla każdej usługi we właściwości encryption i `keyType` sprawdź, czy jest ono ustawione na `Account` wartość .

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby sprawdzić, czy usługa na koncie magazynu używa klucza szyfrowania konta, wywołaj polecenie [Get-AzStorageAccount.](/powershell/module/az.storage/get-azstorageaccount) To polecenie zwraca zestaw właściwości konta magazynu i ich wartości. Poszukaj pola `KeyType` dla każdej usługi we właściwości i `Encryption` sprawdź, czy jest ono ustawione na wartość `Account` .

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby sprawdzić, czy usługa na koncie magazynu używa klucza szyfrowania konta, wywołaj [polecenie az storage account show.](/cli/azure/storage/account#az_storage_account_show) To polecenie zwraca zestaw właściwości konta magazynu i ich wartości. Poszukaj pola dla każdej usługi we właściwości encryption i `keyType` sprawdź, czy jest ustawiona na wartość `Account` .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Szablon](#tab/template)

Nie dotyczy

---

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Konto magazynu utworzone w celu używania klucza szyfrowania o zakresie konta jest rozliczane za pojemność magazynu tabel i transakcje z inną stawką niż konto, które używa domyślnego klucza o zakresie usługi. Aby uzyskać szczegółowe informacje, [zobacz Azure Table Storage pricing (Cennik](https://azure.microsoft.com/pricing/details/storage/tables/)usługi Azure Table Storage).

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](storage-service-encryption.md)
- [Klucze zarządzane przez klienta na potrzeby szyfrowania w usłudze Azure Storage](customer-managed-keys-overview.md)
- [Co to jest Azure Key Vault?](../../key-vault/general/overview.md)