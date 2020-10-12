---
title: Utwórz konto, które obsługuje klucze zarządzane przez klienta dla tabel i kolejek
titleSuffix: Azure Storage
description: Dowiedz się, jak utworzyć konto magazynu, które obsługuje Konfigurowanie kluczy zarządzanych przez klienta dla tabel i kolejek. Użyj interfejsu wiersza polecenia platformy Azure lub szablonu Azure Resource Manager, aby utworzyć konto magazynu, które opiera się na kluczu szyfrowania konta na potrzeby szyfrowania usługi Azure Storage. Następnie można skonfigurować dla konta klucze zarządzane przez klienta.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 726be3f0f8402404d0154336aaf7d5f09fefec10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90967470"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Utwórz konto, które obsługuje klucze zarządzane przez klienta dla tabel i kolejek

Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w stanie spoczynku. Domyślnie usługa queue storage i Table Storage używają klucza, który jest objęty zakresem usługi i jest zarządzany przez firmę Microsoft. Możesz również wybrać użycie kluczy zarządzanych przez klienta do szyfrowania danych z kolejki lub tabeli. Aby używać kluczy zarządzanych przez klienta z kolejkami i tabelami, należy najpierw utworzyć konto magazynu używające klucza szyfrowania, który jest objęty zakresem konta, a nie z usługą. Po utworzeniu konta, które korzysta z klucza szyfrowania konta dla danych z kolejki i tabeli, można skonfigurować klucze zarządzane przez klienta dla tego konta magazynu.

W tym artykule opisano sposób tworzenia konta magazynu, które opiera się na kluczu, który jest objęty zakresem konta. Po pierwszym utworzeniu konta firma Microsoft używa klucza konta do szyfrowania danych na koncie, a firma Microsoft zarządza kluczem. Następnie można skonfigurować klucze zarządzane przez klienta dla konta, aby skorzystać z tych korzyści, w tym z możliwością udostępnienia własnych kluczy, zaktualizowania wersji klucza, obrócenia kluczy i odwołaniu kontroli dostępu.

## <a name="about-the-feature"></a>Informacje o funkcji

Aby utworzyć konto magazynu, które opiera się na kluczu szyfrowania konta dla usługi Queue i Table Storage, musisz najpierw zarejestrować się, aby korzystać z tej funkcji na platformie Azure. Ze względu na ograniczoną pojemność należy pamiętać, że może upłynąć kilka miesięcy przed zatwierdzeniem żądań dostępu.

Można utworzyć konto magazynu, które opiera się na kluczu szyfrowania konta dla usługi Queue i Table Storage w następujących regionach:

- East US
- South Central US
- Zachodnie stany USA 2  

### <a name="register-to-use-the-account-encryption-key"></a>Zarejestruj się, aby użyć klucza szyfrowania konta

Aby zarejestrować się w celu korzystania z klucza szyfrowania konta z usługą Queue lub Table Storage, użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby zarejestrować się w programie PowerShell, wywołaj polecenie [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zarejestrować się w interfejsie wiersza polecenia platformy Azure, wywołaj polecenie [AZ Feature Register](/cli/azure/feature#az-feature-register) .

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Szablon](#tab/template)

Nie dotyczy

---

### <a name="check-the-status-of-your-registration"></a>Sprawdź stan rejestracji

Aby sprawdzić stan rejestracji dla magazynu kolejki lub tabeli, użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby sprawdzić stan rejestracji w programie PowerShell, wywołaj polecenie [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby sprawdzić stan rejestracji w interfejsie wiersza polecenia platformy Azure, wywołaj polecenie [AZ Feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Szablon](#tab/template)

Nie dotyczy

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Zarejestruj ponownie dostawcę zasobów usługi Azure Storage

Po zatwierdzeniu rejestracji należy ponownie zarejestrować dostawcę zasobów usługi Azure Storage. Użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby ponownie zarejestrować dostawcę zasobów.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby ponownie zarejestrować dostawcę zasobów przy użyciu programu PowerShell, wywołaj polecenie [register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) .

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby ponownie zarejestrować dostawcę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Provider Register](/cli/azure/provider#az-provider-register) .

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Szablon](#tab/template)

Nie dotyczy

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Tworzenie konta korzystającego z klucza szyfrowania konta

Należy skonfigurować nowe konto magazynu do korzystania z klucza szyfrowania konta dla kolejek i tabel w momencie utworzenia konta magazynu. Nie można zmienić zakresu klucza szyfrowania po utworzeniu konta.

Konto magazynu musi być kontem ogólnego przeznaczenia w wersji 2. Można utworzyć konto magazynu i skonfigurować je w taki sposób, aby korzystało z klucza szyfrowania konta przy użyciu interfejsu wiersza polecenia platformy Azure lub szablonu Azure Resource Manager.

> [!NOTE]
> Opcjonalnie można skonfigurować tylko magazyn kolejek i tabel, aby szyfrować dane przy użyciu klucza szyfrowania konta podczas tworzenia konta magazynu. Magazyn obiektów blob i Azure Files zawsze używają klucza szyfrowania konta do szyfrowania danych.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby użyć programu PowerShell do utworzenia konta magazynu, które opiera się na kluczu szyfrowania konta, upewnij się, że zainstalowano moduł Azure PowerShell w wersji 3.4.0 lub nowszej. Aby uzyskać więcej informacji, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).

Następnie utwórz konto magazynu ogólnego przeznaczenia w wersji 2, wywołując polecenie [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) z odpowiednimi parametrami:

- Uwzględnij `-EncryptionKeyTypeForQueue` opcję i ustaw jej wartość na, aby `Account` użyć klucza szyfrowania konta do szyfrowania danych w usłudze queue storage.
- Uwzględnij `-EncryptionKeyTypeForTable` opcję i ustaw jej wartość na, aby `Account` użyć klucza szyfrowania konta do szyfrowania danych w usłudze Table Storage.

Poniższy przykład pokazuje, jak utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 skonfigurowane dla magazynu geograficznie nadmiarowego do odczytu (RA-GRS), które korzysta z klucza szyfrowania konta do szyfrowania danych zarówno dla magazynu kolejek, jak i tabeli. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

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

Aby użyć interfejsu wiersza polecenia platformy Azure do utworzenia konta magazynu, które opiera się na kluczu szyfrowania konta, upewnij się, że zainstalowano interfejs wiersza polecenia platformy Azure w wersji 2.0.80 lub nowszej. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Następnie utwórz konto magazynu ogólnego przeznaczenia w wersji 2, wywołując polecenie [AZ Storage account Create](/cli/azure/storage/account#az-storage-account-create) z odpowiednimi parametrami:

- Uwzględnij `--encryption-key-type-for-queue` opcję i ustaw jej wartość na, aby `Account` użyć klucza szyfrowania konta do szyfrowania danych w usłudze queue storage.
- Uwzględnij `--encryption-key-type-for-table` opcję i ustaw jej wartość na, aby `Account` użyć klucza szyfrowania konta do szyfrowania danych w usłudze Table Storage.

Poniższy przykład pokazuje, jak utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 skonfigurowane dla magazynu geograficznie nadmiarowego do odczytu (RA-GRS), które korzysta z klucza szyfrowania konta do szyfrowania danych zarówno dla magazynu kolejek, jak i tabeli. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

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

Poniższy przykład JSON tworzy konto magazynu ogólnego przeznaczenia w wersji 2, które jest skonfigurowane na potrzeby magazynu geograficznie nadmiarowego do odczytu (RA-GRS) i używa klucza szyfrowania konta do szyfrowania danych zarówno dla magazynu kolejek, jak i tabeli. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach ostrych własnymi wartościami:

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

Po utworzeniu konta, które opiera się na kluczu szyfrowania konta, można skonfigurować klucze zarządzane przez klienta, które są przechowywane w Azure Key Vault lub w Key Vault zarządzanym modelu zabezpieczeń sprzętu (HSM) (wersja zapoznawcza). Aby dowiedzieć się, jak przechowywać klucze zarządzane przez klienta w magazynie kluczy, zobacz [Konfigurowanie szyfrowania z kluczami zarządzanymi przez klienta, które są przechowywane w Azure Key Vault](customer-managed-keys-configure-key-vault.md). Aby dowiedzieć się, jak przechowywać klucze zarządzane przez klienta w zarządzanym module HSM, zobacz [Konfigurowanie szyfrowania z kluczami zarządzanymi przez klienta przechowywanych w Azure Key Vault zarządzanym module HSM (wersja zapoznawcza)](customer-managed-keys-configure-key-vault-hsm.md).

## <a name="verify-the-account-encryption-key"></a>Weryfikowanie klucza szyfrowania konta

Aby sprawdzić, czy usługa na koncie magazynu korzysta z klucza szyfrowania konta, wywołaj polecenie [AZ Storage account](/cli/azure/storage/account#az-storage-account-show) dla interfejsu wiersza polecenia platformy Azure. To polecenie zwraca zestaw właściwości konta magazynu i ich wartości. Wyszukaj `keyType` pole dla każdej usługi we właściwości szyfrowania i sprawdź, czy jest ono ustawione na wartość `Account` .

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby sprawdzić, czy usługa na koncie magazynu korzysta z klucza szyfrowania konta, wywołaj polecenie [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) . To polecenie zwraca zestaw właściwości konta magazynu i ich wartości. Wyszukaj `KeyType` pole dla każdej usługi we `Encryption` właściwości i sprawdź, czy jest ono ustawione na wartość `Account` .

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby sprawdzić, czy usługa na koncie magazynu korzysta z klucza szyfrowania konta, wywołaj polecenie [AZ Storage account show](/cli/azure/storage/account#az-storage-account-show) . To polecenie zwraca zestaw właściwości konta magazynu i ich wartości. Wyszukaj `keyType` pole dla każdej usługi we właściwości szyfrowania i sprawdź, czy jest ono ustawione na wartość `Account` .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Szablon](#tab/template)

Nie dotyczy

---

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](storage-service-encryption.md)
- [Klucze zarządzane przez klienta dla szyfrowania usługi Azure Storage](customer-managed-keys-overview.md)
- [Co to jest Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
