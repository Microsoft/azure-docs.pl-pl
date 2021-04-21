---
title: Tworzenie konta magazynu z włączonym szyfrowaniem infrastruktury w celu podwójnego szyfrowania danych
titleSuffix: Azure Storage
description: Klienci, którzy wymagają wyższego poziomu pewności, że ich dane są bezpieczne, mogą również włączyć 256-bitowe szyfrowanie AES na poziomie infrastruktury usługi Azure Storage. Po włączeniu szyfrowania infrastruktury dane na koncie magazynu są dwukrotnie szyfrowane przy użyciu dwóch różnych algorytmów szyfrowania i dwóch różnych kluczy.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 23b3ca919be030490cca06f31dac623d7f80be44
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790387"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>Tworzenie konta magazynu z włączonym szyfrowaniem infrastruktury w celu podwójnego szyfrowania danych

Usługa Azure Storage automatycznie szyfruje wszystkie dane na koncie magazynu na poziomie usługi przy użyciu 256-bitowego szyfrowania AES, jednego z najsilniejszych dostępnych szyfrów blokowych, i jest zgodna ze standardem FIPS 140-2. Klienci, którzy wymagają wyższego poziomu pewności, że ich dane są bezpieczne, mogą również włączyć 256-bitowe szyfrowanie AES na poziomie infrastruktury usługi Azure Storage. Po włączeniu szyfrowania infrastruktury dane na koncie magazynu są szyfrowane dwa razy na poziomie usługi i raz na poziomie infrastruktury z dwoma różnymi algorytmami szyfrowania i &mdash; &mdash; dwoma różnymi kluczami. Podwójne szyfrowanie danych usługi Azure Storage chroni przed scenariuszem, w którym jeden z algorytmów lub kluczy szyfrowania może zostać naruszony. W tym scenariuszu dodatkowa warstwa szyfrowania nadal chroni dane.

Szyfrowanie na poziomie usługi obsługuje korzystanie z kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta z usługą Azure Key Vault lub Key Vault Hardware Security Model (HSM) (wersja zapoznawcza). Szyfrowanie na poziomie infrastruktury opiera się na kluczach zarządzanych przez firmę Microsoft i zawsze używa oddzielnego klucza. Aby uzyskać więcej informacji na temat zarządzania kluczami za pomocą szyfrowania usługi Azure Storage, zobacz About encryption key management (Informacje [o zarządzaniu kluczami szyfrowania).](storage-service-encryption.md#about-encryption-key-management)

Aby podwójnie zaszyfrować dane, należy najpierw utworzyć konto magazynu skonfigurowane do szyfrowania infrastruktury. W tym artykule opisano sposób tworzenia konta magazynu, które umożliwia szyfrowanie infrastruktury.

## <a name="register-to-use-infrastructure-encryption"></a>Rejestrowanie w celu korzystania z szyfrowania infrastruktury

Aby utworzyć konto magazynu z włączonym szyfrowaniem infrastruktury, należy najpierw zarejestrować się, aby korzystać z tej funkcji na platformie Azure przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Nie dotyczy

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby zarejestrować się przy użyciu programu PowerShell, [wywołaj polecenie Register-AzProviderFeature.](/powershell/module/az.resources/register-azproviderfeature)

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Aby sprawdzić stan rejestracji w programie PowerShell, wywołaj polecenie [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Po zatwierdzeniu rejestracji należy ponownie zarejestrować dostawcę zasobów usługi Azure Storage. Aby ponownie zarejestrować dostawcę zasobów przy użyciu programu PowerShell, wywołaj [polecenie Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider)

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zarejestrować się za pomocą interfejsu wiersza polecenia platformy Azure, [wywołaj polecenie az feature register.](/cli/azure/feature#az_feature_register)

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Aby sprawdzić stan rejestracji w interfejsie wiersza polecenia platformy Azure, wywołaj [polecenie az feature.](/cli/azure/feature#az_feature_show)

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Po zatwierdzeniu rejestracji należy ponownie zarejestrować dostawcę zasobów usługi Azure Storage. Aby ponownie zarejestrować dostawcę zasobów za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj [polecenie az provider register.](/cli/azure/provider#az_provider_register)

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Szablon](#tab/template)

Nie dotyczy

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>Tworzenie konta z włączonym szyfrowaniem infrastruktury

Konto magazynu należy skonfigurować do używania szyfrowania infrastruktury podczas jego tworzenia. Konto magazynu musi być typu ogólnego przeznaczenia w wersji 2.

Nie można włączyć ani wyłączyć szyfrowania infrastruktury po utworzeniu konta.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby utworzyć konto magazynu z włączonym szyfrowaniem infrastruktury przy użyciu programu PowerShell, wykonaj następujące kroki:

1. W Azure Portal przejdź do strony **Konta** magazynu.
1. Wybierz przycisk **Dodaj,** aby dodać nowe konto magazynu ogólnego przeznaczenia w wersji 2.
1. Na karcie **Zaawansowane** znajdź pozycję **Szyfrowanie infrastruktury,** a następnie wybierz pozycję **Włączone.**
1. Wybierz **pozycję Przejrzyj i utwórz,** aby zakończyć tworzenie konta magazynu.

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="Zrzut ekranu przedstawiający sposób włączania szyfrowania infrastruktury podczas tworzenia konta":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby utworzyć konto magazynu z włączonym szyfrowaniem infrastruktury przy użyciu programu PowerShell, upewnij się, że zainstalowano moduł [Az.Storage programu PowerShell](https://www.powershellgallery.com/packages/Az.Storage)w wersji 2.2.0 lub nowszej. Aby uzyskać więcej informacji, zobacz [Instalowanie Azure PowerShell](/powershell/azure/install-az-ps).

Następnie utwórz konto magazynu ogólnego przeznaczenia w wersji 2, wywołując [polecenie New-AzStorageAccount.](/powershell/module/az.storage/new-azstorageaccount) Dołącz opcję `-RequireInfrastructureEncryption` włączenia szyfrowania infrastruktury.

W poniższym przykładzie pokazano, jak utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 skonfigurowane na potrzeby magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS) i z włączonym szyfrowaniem infrastruktury na potrzeby podwójnego szyfrowania danych. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby utworzyć konto magazynu z włączonym szyfrowaniem infrastruktury przy użyciu interfejsu wiersza polecenia platformy Azure, upewnij się, że zainstalowano interfejs wiersza polecenia platformy Azure w wersji 2.8.0 lub nowszej. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

Następnie utwórz konto magazynu ogólnego przeznaczenia w wersji 2, wywołując [polecenie az storage account create](/cli/azure/storage/account#az_storage_account_create) i dołączając polecenie , aby włączyć szyfrowanie `--require-infrastructure-encryption option` infrastruktury.

W poniższym przykładzie pokazano, jak utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 skonfigurowane na potrzeby magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS) i z włączonym szyfrowaniem infrastruktury na potrzeby podwójnego szyfrowania danych. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

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

Poniższy przykład JSON tworzy konto magazynu ogólnego przeznaczenia w wersji 2 skonfigurowane na potrzeby magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS) i z włączonym szyfrowaniem infrastruktury na potrzeby podwójnego szyfrowania danych. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

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

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>Sprawdzanie, czy szyfrowanie infrastruktury jest włączone

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby sprawdzić, czy szyfrowanie infrastruktury jest włączone dla konta magazynu z Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. W **obszarze Ustawienia** wybierz pozycję **Szyfrowanie.**

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="Zrzut ekranu przedstawiający sposób sprawdzania, czy szyfrowanie infrastruktury jest włączone dla konta":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby sprawdzić, czy dla konta magazynu włączono szyfrowanie infrastruktury przy użyciu programu PowerShell, wywołaj polecenie [Get-AzStorageAccount.](/powershell/module/az.storage/get-azstorageaccount) To polecenie zwraca zestaw właściwości konta magazynu i ich wartości. Pobierz pole `RequireInfrastructureEncryption` we właściwości `Encryption` i sprawdź, czy jest ono ustawione na `True` wartość .

Poniższy przykład pobiera wartość `RequireInfrastructureEncryption` właściwości . Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby sprawdzić, czy szyfrowanie infrastruktury jest włączone dla konta magazynu za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj [polecenie az storage account show.](/cli/azure/storage/account#az_storage_account_show) To polecenie zwraca zestaw właściwości konta magazynu i ich wartości. Poszukaj pola `requireInfrastructureEncryption` we właściwości i `encryption` sprawdź, czy jest ono ustawione na wartość `true` .

Poniższy przykład pobiera wartość `requireInfrastructureEncryption` właściwości . Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

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
- [Klucze zarządzane przez klienta na potrzeby szyfrowania w usłudze Azure Storage](customer-managed-keys-overview.md)
