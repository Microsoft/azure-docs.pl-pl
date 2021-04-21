---
title: Tworzenie zakresów szyfrowania i zarządzanie nimi
description: Dowiedz się, jak utworzyć zakres szyfrowania, aby odizolować dane obiektów blob na poziomie kontenera lub obiektu blob.
services: storage
author: tamram
ms.service: storage
ms.date: 03/26/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 656443b0bc9d0e45f43634b1b4c21145de7a5bb5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792547"
---
# <a name="create-and-manage-encryption-scopes"></a>Tworzenie zakresów szyfrowania i zarządzanie nimi

Zakresy szyfrowania umożliwiają zarządzanie szyfrowaniem na poziomie pojedynczego obiektu blob lub kontenera. Zakresy szyfrowania mogą być wykorzystywane do tworzenia bezpiecznych granic między danymi, które znajdują się na tym samym koncie magazynu, ale należą do różnych klientów. Aby uzyskać więcej informacji na temat zakresów szyfrowania, zobacz [Zakresy szyfrowania dla usługi Blob Storage.](encryption-scope-overview.md)

W tym artykule pokazano, jak utworzyć zakres szyfrowania. Pokazano również, jak określić zakres szyfrowania podczas tworzenia obiektu blob lub kontenera.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-an-encryption-scope"></a>Tworzenie zakresu szyfrowania

Zakres szyfrowania, który jest chroniony za pomocą klucza zarządzanego przez firmę Microsoft, lub klucza zarządzanego przez klienta, który jest przechowywany w programie Azure Key Vault lub w zarządzanym sprzętowym modelu zabezpieczeń (HSM) usługi Azure Key Vault (wersja zapoznawcza). Aby utworzyć zakres szyfrowania przy użyciu klucza zarządzanego przez klienta, należy najpierw utworzyć magazyn kluczy lub zarządzany moduł HSM i dodać klucz, który będzie dla tego zakresu. Magazyn kluczy lub zarządzany moduł HSM musi mieć włączoną ochronę przed przeczyszczaniem i musi znajdować się w tym samym regionie co konto magazynu.

Zakres szyfrowania jest automatycznie włączany podczas jego tworzenia. Po utworzeniu zakresu szyfrowania można go określić podczas tworzenia obiektu blob. Podczas tworzenia kontenera można również określić domyślny zakres szyfrowania, który jest automatycznie stosowana do wszystkich obiektów blob w kontenerze.

# <a name="portal"></a>[Portal](#tab/portal)

Aby utworzyć zakres szyfrowania w Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. Wybierz ustawienie **Szyfrowanie.**
1. Wybierz **kartę Zakresy szyfrowania.**
1. Kliknij przycisk **Dodaj,** aby dodać nowy zakres szyfrowania.
1. W **okienku Tworzenie zakresu** szyfrowania wprowadź nazwę nowego zakresu.
1. Wybierz żądany typ obsługi kluczy szyfrowania: klucze zarządzane przez firmę **Microsoft** lub **Klucze zarządzane przez klienta.**
    - W przypadku wyboru **kluczy zarządzanych przez firmę Microsoft** kliknij przycisk **Utwórz,** aby utworzyć zakres szyfrowania.
    - W przypadku **wybrania** opcji Klucze zarządzane przez klienta wybierz subskrypcję i określ magazyn kluczy lub zarządzany moduł HSM oraz klucz do użycia dla tego zakresu szyfrowania, jak pokazano na poniższej ilustracji.

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia zakresu szyfrowania w Azure Portal":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby utworzyć zakres szyfrowania przy użyciu programu PowerShell, zainstaluj moduł [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) programu PowerShell w wersji 3.4.0 lub nowszej.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Tworzenie zakresu szyfrowania chronionego przez klucze zarządzane przez firmę Microsoft

Aby utworzyć nowy zakres szyfrowania, który jest chroniony przez klucze zarządzane przez firmę Microsoft, wywołaj polecenie **New-AzStorageEncryptionScope** z `-StorageEncryption` parametrem .

Pamiętaj, aby zastąpić wartości symboli zastępczych w przykładzie własnymi wartościami:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Tworzenie zakresu szyfrowania chronionego przez klucze zarządzane przez klienta

Aby utworzyć nowy zakres szyfrowania, który jest chroniony przez klucze zarządzane przez klienta przechowywane w magazynie kluczy lub zarządzanym modułem HSM, najpierw skonfiguruj klucze zarządzane przez klienta dla konta magazynu. Musisz przypisać tożsamość zarządzaną do konta magazynu, a następnie użyć tożsamości zarządzanej do skonfigurowania zasad dostępu dla magazynu kluczy lub zarządzanego modułu HSM, aby konto magazynu ma uprawnienia dostępu do niego.

Aby skonfigurować klucze zarządzane przez klienta do użycia z zakresem szyfrowania, należy włączyć ochronę przed przeczyszczaniem w magazynie kluczy lub zarządzanym modułem HSM. Magazyn kluczy lub zarządzany moduł HSM musi znajdować się w tym samym regionie co konto magazynu.

Pamiętaj, aby zastąpić wartości symboli zastępczych w przykładzie własnymi wartościami:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri>"
$scopeName2 = "customer2scope"

# Assign a system managed identity to the storage account.
$storageAccount = Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -AssignIdentity

# Configure the access policy for the key vault.
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

Następnie wywołaj **polecenie New-AzStorageEncryptionScope** z parametrem `-KeyvaultEncryption` i określ klucz URI. Do uwzględnienia wersji klucza w kluczu URI jest opcjonalne. W przypadku pominięcia wersji klucza zakres szyfrowania automatycznie użyje najnowszej wersji klucza. Jeśli dołączysz wersję klucza, musisz ręcznie zaktualizować wersję klucza, aby użyć innej wersji.

Pamiętaj, aby zastąpić wartości symboli zastępczych w przykładzie własnymi wartościami:

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Aby utworzyć zakres szyfrowania za pomocą interfejsu wiersza polecenia platformy Azure, najpierw zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.20.0 lub nowszej.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Tworzenie zakresu szyfrowania chronionego przez klucze zarządzane przez firmę Microsoft

Aby utworzyć nowy zakres szyfrowania, który jest chroniony przez klucze zarządzane przez firmę Microsoft, wywołaj polecenie [az storage account encryption-scope create,](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_create) określając `--key-source` parametr jako `Microsoft.Storage` . Pamiętaj, aby zastąpić wartości symboli zastępczych własnymi wartościami:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Tworzenie zakresu szyfrowania chronionego przez klucze zarządzane przez klienta

Aby utworzyć nowy zakres szyfrowania, który jest chroniony przez klucze zarządzane przez firmę Microsoft, wywołaj polecenie [az storage account encryption-scope create,](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_create) określając `--key-source` parametr jako `Microsoft.Storage` . Pamiętaj, aby zastąpić wartości symboli zastępczych własnymi wartościami:

Aby utworzyć nowy zakres szyfrowania, który jest chroniony przez klucze zarządzane przez klienta w magazynie kluczy lub zarządzanym modułem HSM, najpierw skonfiguruj klucze zarządzane przez klienta dla konta magazynu. Musisz przypisać tożsamość zarządzaną do konta magazynu, a następnie użyć tożsamości zarządzanej do skonfigurowania zasad dostępu dla magazynu kluczy, tak aby konto magazynu ma uprawnienia dostępu do niego. Aby uzyskać więcej informacji, zobacz [Klucze zarządzane przez klienta na potrzeby szyfrowania w usłudze Azure Storage.](../common/customer-managed-keys-overview.md)

Aby skonfigurować klucze zarządzane przez klienta do użycia z zakresem szyfrowania, ochrona przed przeczyszczaniem musi być włączona w magazynie kluczy lub zarządzanym hsm. Magazyn kluczy lub zarządzany moduł HSM muszą znajdować się w tym samym regionie co konto magazynu.

Pamiętaj, aby zastąpić wartości symboli zastępczych w przykładzie własnymi wartościami:

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity

storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group> \
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

Następnie wywołaj **polecenie az storage account encryption-scope create** z `--key-uri` parametrem i określ klucz URI. Do uwzględnienia wersji klucza w kluczu URI jest opcjonalne. Jeśli pominięto wersję klucza, zakres szyfrowania będzie automatycznie używać najnowszej wersji klucza. Jeśli dołączysz wersję klucza, musisz ręcznie zaktualizować wersję klucza, aby użyć innej wersji.

Pamiętaj, aby zastąpić wartości symboli zastępczych w przykładzie własnymi wartościami:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

Aby dowiedzieć się, jak skonfigurować szyfrowanie usługi Azure Storage przy użyciu kluczy zarządzanych przez klienta w magazynie kluczy lub zarządzanym modułem HSM, zobacz następujące artykuły:

- [Konfigurowanie szyfrowania za pomocą kluczy zarządzanych przez klienta przechowywanych w usłudze Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md)
- [Skonfiguruj szyfrowanie przy użyciu kluczy zarządzanych przez klienta przechowywanych w zarządzanym Azure Key Vault HSM (wersja zapoznawcza).](../common/customer-managed-keys-configure-key-vault-hsm.md)

## <a name="list-encryption-scopes-for-storage-account"></a>Lista zakresów szyfrowania dla konta magazynu

# <a name="portal"></a>[Portal](#tab/portal)

Aby wyświetlić zakresy szyfrowania dla konta magazynu w Azure Portal,  przejdź do ustawienia Zakresy szyfrowania dla konta magazynu. W tym okienku można włączyć lub wyłączyć zakres szyfrowania albo zmienić klucz zakresu szyfrowania.

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="Zrzut ekranu przedstawiający listę zakresów szyfrowania w Azure Portal":::

Aby wyświetlić szczegóły klucza zarządzanego przez klienta, w tym jego numer URI i wersję oraz informacje o tym, czy wersja klucza jest automatycznie aktualizowana, skorzystaj z linku w **kolumnie Klucz.**

:::image type="content" source="media/encryption-scope-manage/customer-managed-key-details-portal.png" alt-text="Zrzut ekranu przedstawiający szczegóły klucza używanego z zakresem szyfrowania":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby wyświetlić listę zakresów szyfrowania dostępnych dla konta magazynu przy użyciu programu PowerShell, wywołaj polecenie **Get-AzStorageEncryptionScope.** Pamiętaj, aby zastąpić wartości symboli zastępczych w przykładzie własnymi wartościami:

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

Aby wyświetlić listę wszystkich zakresów szyfrowania w grupie zasobów według konta magazynu, użyj składni potoku:

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Aby wyświetlić listę zakresów szyfrowania dostępnych dla konta magazynu za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj [polecenie az storage account encryption-scope list.](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_list) Pamiętaj, aby zastąpić wartości symboli zastępczych w przykładzie własnymi wartościami:

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>Tworzenie kontenera z domyślnym zakresem szyfrowania

Podczas tworzenia kontenera można określić domyślny zakres szyfrowania. Obiekty blob w tym kontenerze będą domyślnie używać tego zakresu.

Pojedynczy obiekt blob można utworzyć z własnym zakresem szyfrowania, chyba że kontener jest skonfigurowany tak, aby wymagać, aby wszystkie obiekty blob używały zakresu domyślnego. Aby uzyskać więcej informacji, zobacz [Zakresy szyfrowania dla kontenerów i obiektów blob.](encryption-scope-overview.md#encryption-scopes-for-containers-and-blobs)

# <a name="portal"></a>[Portal](#tab/portal)

Aby utworzyć kontener z domyślnym zakresem szyfrowania w Azure Portal, najpierw utwórz zakres szyfrowania zgodnie z opisem w tece Create an encryption scope (Tworzenie [zakresu szyfrowania).](#create-an-encryption-scope) Następnie wykonaj następujące kroki, aby utworzyć kontener:

1. Przejdź do listy kontenerów na koncie magazynu i wybierz przycisk **Dodaj,** aby utworzyć nowy kontener.
1. Rozwiń **pozycję Ustawienia** zaawansowane w **okienku Nowy** kontener.
1. Z **listy rozwijanej** Zakres szyfrowania wybierz domyślny zakres szyfrowania dla kontenera.
1. Aby wymagać, aby wszystkie obiekty blob w kontenerze używały domyślnego zakresu szyfrowania, zaznacz pole wyboru Użyj tego zakresu szyfrowania dla wszystkich **obiektów blob w kontenerze**. Jeśli to pole wyboru jest zaznaczone, pojedynczy obiekt blob w kontenerze nie może zastąpić domyślnego zakresu szyfrowania.

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="Zrzut ekranu przedstawiający kontener z domyślnym zakresem szyfrowania":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby utworzyć kontener z domyślnym zakresem szyfrowania za pomocą programu PowerShell, wywołaj polecenie [New-AzStorageContainer,](/powershell/module/az.storage/new-azstoragecontainer) określając zakres `-DefaultEncryptionScope` parametru . Aby wymusić użycie domyślnego zakresu kontenera przez wszystkie obiekty blob w kontenerze, ustaw `-PreventEncryptionScopeOverride` parametr na `true` wartość .

```powershell
$containerName1 = "container1"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a container with a default encryption scope that cannot be overridden.
New-AzStorageContainer -Name $containerName1 `
    -Context $ctx `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Aby utworzyć kontener z domyślnym zakresem szyfrowania za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj [polecenie az storage container create,](/cli/azure/storage/container#az_storage_container_create) określając zakres `--default-encryption-scope` parametru . Aby wymusić użycie domyślnego zakresu kontenera przez wszystkie obiekty blob w kontenerze, ustaw `--prevent-encryption-scope-override` parametr na `true` wartość .

W poniższym przykładzie użyto konta usługi Azure AD do autoryzowania operacji tworzenia kontenera. Możesz również użyć klucza dostępu do konta. Aby uzyskać więcej informacji, zobacz [Autoryzowanie dostępu do danych obiektów blob lub kolejek za pomocą interfejsu wiersza polecenia platformy Azure.](./authorize-data-operations-cli.md)

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <container> \
    --default-encryption-scope <scope> \
    --prevent-encryption-scope-override true \
    --auth-mode login
```

---

Jeśli klient próbuje określić zakres podczas przekazywania obiektu blob do kontenera, który ma domyślny zakres szyfrowania, a kontener jest skonfigurowany tak, aby zapobiegać zastępowaniu zakresu domyślnego przez obiekty blob, operacja kończy się niepowodzeniem z komunikatem informujący, że żądanie jest zabronione przez zasady szyfrowania kontenera.

## <a name="upload-a-blob-with-an-encryption-scope"></a>Przekazywanie obiektu blob z zakresem szyfrowania

Podczas przekazywania obiektu blob można określić zakres szyfrowania dla tego obiektu blob lub użyć domyślnego zakresu szyfrowania dla kontenera, jeśli został określony.

# <a name="portal"></a>[Portal](#tab/portal)

Aby przekazać obiekt blob z zakresem szyfrowania za pośrednictwem usługi Azure Portal, najpierw utwórz zakres szyfrowania zgodnie z opisem w tece [Create an encryption scope (Tworzenie zakresu szyfrowania).](#create-an-encryption-scope) Następnie wykonaj następujące kroki, aby utworzyć obiekt blob:

1. Przejdź do kontenera, do którego chcesz przekazać obiekt blob.
1. Wybierz przycisk **Przekaż** i znajdź obiekt blob do przekazania.
1. Rozwiń **pozycję Ustawienia** zaawansowane w **okienku Przekazywanie obiektu blob.**
1. Znajdź **sekcję rozwijaną** Zakres szyfrowania. Domyślnie obiekt blob jest tworzony z domyślnym zakresem szyfrowania dla kontenera, jeśli został określony. Jeśli kontener wymaga, aby obiekty blob używały domyślnego zakresu szyfrowania, ta sekcja jest wyłączona.
1. Aby określić inny zakres dla obiektu blob, który jest przesyłany, wybierz pozycję Wybierz istniejący **zakres,** a następnie wybierz żądany zakres z listy rozwijanej.

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="Zrzut ekranu przedstawiający sposób przekazywania obiektu blob z zakresem szyfrowania":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby przekazać obiekt blob z zakresem szyfrowania za pośrednictwem programu PowerShell, wywołaj polecenie [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) i podaj zakres szyfrowania obiektu blob.

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx

# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx `
    -Container $containerName2 `
    -File $localSrcFile `
    -Blob "helloworld.txt" `
    -BlobType Block `
    -EncryptionScope $scopeName2
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Aby przekazać obiekt blob z zakresem szyfrowania za pośrednictwem interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) i podaj zakres szyfrowania dla obiektu blob.

Jeśli używasz usługi Azure Cloud Shell, postępuj zgodnie z instrukcjami opisanymi w części Przekazywanie obiektu [blob,](storage-quickstart-blobs-cli.md#upload-a-blob) aby utworzyć plik w katalogu głównym. Następnie możesz przekazać ten plik do obiektu blob, korzystając z poniższego przykładu.

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## <a name="change-the-encryption-key-for-a-scope"></a>Zmienianie klucza szyfrowania dla zakresu

Aby zmienić klucz, który chroni zakres szyfrowania z klucza zarządzanego przez firmę Microsoft na klucz zarządzany przez klienta, najpierw upewnij się, że dla konta magazynu włączono klucze zarządzane przez klienta przy użyciu modułu HSM usługi Azure Key Vault lub Key Vault. Aby uzyskać więcej informacji, zobacz [Configure encryption with customer-managed keys stored in Azure Key Vault (Konfigurowanie](../common/customer-managed-keys-configure-key-vault.md) szyfrowania przy użyciu kluczy zarządzanych przez klienta) lub [Configure encryption with customer-managed keys stored in Azure Key Vault](../common/customer-managed-keys-configure-key-vault.md)(Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta w u Azure Key Vault).

# <a name="portal"></a>[Portal](#tab/portal)

Aby zmienić klucz, który chroni zakres w Azure Portal, wykonaj następujące kroki:

1. Przejdź do **karty Zakresy szyfrowania,** aby wyświetlić listę zakresów szyfrowania dla konta magazynu.
1. Wybierz przycisk **Więcej** obok zakresu, który chcesz zmodyfikować.
1. W **okienku Edytowanie zakresu** szyfrowania można zmienić typ szyfrowania z klucz zarządzany przez firmę Microsoft na klucz zarządzany przez klienta lub na odwrót.
1. Aby wybrać nowy klucz zarządzany  przez klienta, wybierz pozycję Użyj nowego klucza i określ magazyn kluczy, klucz i wersję klucza.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby zmienić klucz, który chroni zakres szyfrowania z klucza zarządzanego przez klienta na klucz zarządzany przez firmę Microsoft przy użyciu programu PowerShell, wywołaj polecenie **Update-AzStorageEncryptionScope** i przekaż parametr `-StorageEncryption` :

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

Następnie wywołaj **polecenie Update-AzStorageEncryptionScope** i przekaż parametry `-KeyUri` i `-KeyvaultEncryption` :

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Aby zmienić klucz, który chroni zakres szyfrowania z klucza zarządzanego przez klienta na klucz zarządzany przez firmę Microsoft za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_update) i przekaż parametr o wartości `--key-source` `Microsoft.Storage` :

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

Następnie wywołaj **polecenie az storage account encryption-scope update,** przekaż parametr i przekaż parametr o wartości `--key-uri` `--key-source` `Microsoft.KeyVault` :

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="disable-an-encryption-scope"></a>Wyłączanie zakresu szyfrowania

Gdy zakres szyfrowania jest wyłączony, nie są już naliczane za niego faktury. Wyłącz wszystkie zakresy szyfrowania, które nie są potrzebne, aby uniknąć niepotrzebnych opłat. Aby uzyskać więcej informacji, zobacz [Szyfrowanie usługi Azure Storage dla danych magazynowych.](../common/storage-service-encryption.md)

# <a name="portal"></a>[Portal](#tab/portal)

Aby wyłączyć zakres szyfrowania w Azure Portal, przejdź  do ustawienia Zakresy szyfrowania dla konta magazynu, wybierz żądany zakres szyfrowania i wybierz **pozycję Wyłącz**.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby wyłączyć zakres szyfrowania za pomocą programu PowerShell, wywołaj polecenie Update-AzStorageEncryptionScope i dołącz parametr o wartości , jak pokazano w `-State` `disabled` poniższym przykładzie. Aby ponownie włączyć zakres szyfrowania, wywołaj to samo polecenie z `-State` parametrem ustawionym na `enabled` . Pamiętaj, aby zastąpić wartości symboli zastępczych w przykładzie własnymi wartościami:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Aby wyłączyć zakres szyfrowania za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj [polecenie az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_update) i dołącz parametr o wartości , jak pokazano w poniższym `--state` `Disabled` przykładzie. Aby ponownie włączyć zakres szyfrowania, wywołaj to samo polecenie z `--state` parametrem ustawionym na `Enabled` . Pamiętaj, aby zastąpić wartości symboli zastępczych w przykładzie własnymi wartościami:

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

> [!IMPORTANT]
> Nie można usunąć zakresu szyfrowania. Aby uniknąć nieoczekiwanych kosztów, należy wyłączyć wszystkie zakresy szyfrowania, które nie są obecnie potrzebne.

---

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](../common/storage-service-encryption.md)
- [Zakresy szyfrowania dla usługi Blob Storage](encryption-scope-overview.md)
- [Klucze zarządzane przez klienta na potrzeby szyfrowania w usłudze Azure Storage](../common/customer-managed-keys-overview.md)