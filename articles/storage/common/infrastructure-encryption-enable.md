---
title: Utwórz konto magazynu z włączoną funkcją szyfrowania infrastruktury umożliwiającą podwójne szyfrowanie danych
titleSuffix: Azure Storage
description: Klienci, którzy wymagają wyższego poziomu pewności, że ich dane są bezpieczne, mogą również włączyć 256-bitowe szyfrowanie AES na poziomie infrastruktury usługi Azure Storage. Po włączeniu szyfrowania infrastruktury dane na koncie magazynu są szyfrowane dwa razy z dwoma różnymi algorytmami szyfrowania i dwoma różnymi kluczami.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 3164de9c3e44001d58d46eab9f823041b440960b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984138"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>Utwórz konto magazynu z włączoną funkcją szyfrowania infrastruktury umożliwiającą podwójne szyfrowanie danych

Usługa Azure Storage automatycznie szyfruje wszystkie dane na koncie magazynu na poziomie usługi przy użyciu 256-bitowego szyfrowania AES, jednego z najsilniejszych szyfrów blokowych i jest zgodnych ze standardem FIPS 140-2. Klienci, którzy wymagają wyższego poziomu pewności, że ich dane są bezpieczne, mogą również włączyć 256-bitowe szyfrowanie AES na poziomie infrastruktury usługi Azure Storage. Po włączeniu szyfrowania infrastruktury dane na koncie magazynu są szyfrowane dwa razy &mdash; na poziomie usługi i raz na poziomie infrastruktury &mdash; z dwoma różnymi algorytmami szyfrowania i dwoma różnymi kluczami. Podwójne szyfrowanie danych usługi Azure Storage chroni przed scenariuszem, w którym można złamać jeden z algorytmów szyfrowania lub kluczy. W tym scenariuszu dodatkowa warstwa szyfrowania nadal chroni dane.

Szyfrowanie na poziomie usługi obsługuje użycie kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta z użyciem Azure Key Vault lub Key Vault zarządzanego modelu zabezpieczeń sprzętu (HSM) (wersja zapoznawcza). Szyfrowanie na poziomie infrastruktury korzysta z kluczy zarządzanych przez firmę Microsoft i zawsze korzysta z osobnego klucza. Aby uzyskać więcej informacji na temat zarządzania kluczami przy użyciu szyfrowania usługi Azure Storage, zobacz [Informacje o zarządzaniu kluczami szyfrowania](storage-service-encryption.md#about-encryption-key-management).

Aby podwójnie zaszyfrować dane, należy najpierw utworzyć konto magazynu skonfigurowane do szyfrowania infrastruktury. W tym artykule opisano sposób tworzenia konta magazynu, które umożliwia szyfrowanie infrastruktury.

## <a name="register-to-use-infrastructure-encryption"></a>Zarejestruj się, aby korzystać z szyfrowania infrastruktury

Aby utworzyć konto magazynu z włączonym szyfrowaniem infrastruktury, musisz najpierw zarejestrować się, aby korzystać z tej funkcji na platformie Azure przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Nie dotyczy

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby zarejestrować się w programie PowerShell, wywołaj polecenie [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Aby sprawdzić stan rejestracji w programie PowerShell, wywołaj polecenie [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Po zatwierdzeniu rejestracji należy ponownie zarejestrować dostawcę zasobów usługi Azure Storage. Aby ponownie zarejestrować dostawcę zasobów przy użyciu programu PowerShell, wywołaj polecenie [register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) .

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zarejestrować się w interfejsie wiersza polecenia platformy Azure, wywołaj polecenie [AZ Feature Register](/cli/azure/feature#az-feature-register) .

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Aby sprawdzić stan rejestracji w interfejsie wiersza polecenia platformy Azure, wywołaj polecenie [AZ Feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Po zatwierdzeniu rejestracji należy ponownie zarejestrować dostawcę zasobów usługi Azure Storage. Aby ponownie zarejestrować dostawcę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Provider Register](/cli/azure/provider#az-provider-register) .

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Szablon](#tab/template)

Nie dotyczy

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>Utwórz konto z włączonym szyfrowaniem infrastruktury

Należy skonfigurować konto magazynu do korzystania z szyfrowania infrastruktury w momencie tworzenia konta. Konto magazynu musi być kontem ogólnego przeznaczenia w wersji 2.

Nie można włączyć lub wyłączyć szyfrowania infrastruktury po utworzeniu konta.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Aby użyć programu PowerShell do utworzenia konta magazynu z włączonym szyfrowaniem infrastruktury, wykonaj następujące czynności:

1. W Azure Portal przejdź do strony **konta magazynu** .
1. Wybierz przycisk **Dodaj** , aby dodać nowe konto magazynu ogólnego przeznaczenia w wersji 2.
1. Na karcie **Zaawansowane** Znajdź pozycję Szyfrowanie **infrastruktury** i wybierz pozycję **włączone**.
1. Wybierz pozycję **Przegląd + Utwórz** , aby zakończyć tworzenie konta magazynu.

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="Zrzut ekranu przedstawiający sposób włączania szyfrowania infrastruktury podczas tworzenia konta":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby użyć programu PowerShell do utworzenia konta magazynu z włączonym szyfrowaniem infrastruktury, upewnij się, że zainstalowano [moduł programu PowerShell AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage)w wersji 2.2.0 lub nowszej. Aby uzyskać więcej informacji, zobacz [Install Azure PowerShell](/powershell/azure/install-az-ps).

Następnie utwórz konto magazynu ogólnego przeznaczenia w wersji 2, wywołując polecenie [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) . Uwzględnij `-RequireInfrastructureEncryption` opcję włączania szyfrowania infrastruktury.

Poniższy przykład pokazuje, jak utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 skonfigurowane dla magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS) i ma włączone szyfrowanie infrastruktury umożliwiające podwójne szyfrowanie danych. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby utworzyć konto magazynu z włączonym szyfrowaniem infrastruktury przy użyciu interfejsu wiersza polecenia platformy Azure, upewnij się, że zainstalowano interfejs wiersza polecenia platformy Azure w wersji 2.8.0 lub nowszej. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Następnie utwórz konto magazynu ogólnego przeznaczenia w wersji 2, wywołując polecenie [AZ Storage account Create](/cli/azure/storage/account#az-storage-account-create) i Dołącz `--require-infrastructure-encryption option` do włączenia szyfrowania infrastruktury.

Poniższy przykład pokazuje, jak utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 skonfigurowane dla magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS) i ma włączone szyfrowanie infrastruktury umożliwiające podwójne szyfrowanie danych. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

# <a name="template"></a>[Szablon](#tab/template)

Poniższy przykład JSON tworzy konto magazynu ogólnego przeznaczenia w wersji 2 skonfigurowane dla magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS) i ma włączone szyfrowanie infrastruktury umożliwiające podwójne szyfrowanie danych. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

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
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>Sprawdź, czy szyfrowanie infrastruktury jest włączone

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Aby sprawdzić, czy szyfrowanie infrastruktury zostało włączone dla konta magazynu z Azure Portal, wykonaj następujące czynności:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. W obszarze **Ustawienia**wybierz pozycję **szyfrowanie**.

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="Zrzut ekranu przedstawiający sposób włączania szyfrowania infrastruktury podczas tworzenia konta":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby sprawdzić, czy szyfrowanie infrastruktury jest włączone dla konta magazynu w programie PowerShell, wywołaj polecenie [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) . To polecenie zwraca zestaw właściwości konta magazynu i ich wartości. Pobierz `RequireInfrastructureEncryption` pole we `Encryption` właściwości i sprawdź, czy jest ustawione na `True` .

Poniższy przykład pobiera wartość `RequireInfrastructureEncryption` właściwości. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach ostrych własnymi wartościami:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby sprawdzić, czy szyfrowanie infrastruktury zostało włączone dla konta magazynu za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage account show](/cli/azure/storage/account#az-storage-account-show) . To polecenie zwraca zestaw właściwości konta magazynu i ich wartości. Wyszukaj `requireInfrastructureEncryption` pole we `encryption` właściwości i sprawdź, czy jest ono ustawione na wartość `true` .

Poniższy przykład pobiera wartość `requireInfrastructureEncryption` właściwości. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach ostrych własnymi wartościami:

```azurecli-interactive
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