---
title: Skonfiguruj szyfrowanie za pomocą kluczy zarządzanych przez klienta, przechowywanych w Azure Key Vault
titleSuffix: Azure Storage
description: Dowiedz się, jak skonfigurować szyfrowanie usługi Azure Storage za pomocą kluczy zarządzanych przez klienta w Azure Key Vault przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0991992a6138d263dfb4d200c9555a8d53366d70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90995988"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>Skonfiguruj szyfrowanie za pomocą kluczy zarządzanych przez klienta, przechowywanych w Azure Key Vault

Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w stanie spoczynku. Domyślnie dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, możesz zarządzać własnymi kluczami. Klucze zarządzane przez klienta muszą być przechowywane w Azure Key Vault lub Key Vault zarządzanym sprzętowym modelem zabezpieczeń (HSM) (wersja zapoznawcza).

W tym artykule pokazano, jak skonfigurować szyfrowanie za pomocą kluczy zarządzanych przez klienta przechowywanych w magazynie kluczy przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się, jak skonfigurować szyfrowanie za pomocą kluczy zarządzanych przez klienta przechowywanych w zarządzanym module HSM, zobacz [Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta, przechowywanych w Azure Key Vault zarządzanym module HSM (wersja zapoznawcza)](customer-managed-keys-configure-key-vault-hsm.md).

> [!NOTE]
> Azure Key Vault i Azure Key Vault zarządzanym modułem HSM obsługują te same interfejsy API i interfejsów zarządzania na potrzeby konfiguracji.

## <a name="configure-a-key-vault"></a>Konfigurowanie magazynu kluczy

Za pomocą nowego lub istniejącego magazynu kluczy można przechowywać klucze zarządzane przez klienta. Konto magazynu i Magazyn kluczy muszą znajdować się w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach.

W przypadku korzystania z kluczy zarządzanych przez klienta z szyfrowaniem usługi Azure Storage wymagane jest włączenie zarówno nietrwałego usuwania, jak i przeczyszczania dla magazynu kluczy. Usuwanie nietrwałe jest domyślnie włączone podczas tworzenia nowego magazynu kluczy i nie można go wyłączyć. Ochronę przed przeczyszczeniem można włączyć podczas tworzenia magazynu kluczy lub po jego utworzeniu.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Aby dowiedzieć się, jak utworzyć magazyn kluczy za pomocą Azure Portal, zobacz [Szybki Start: Tworzenie magazynu kluczy przy użyciu Azure Portal](../../key-vault/general/quick-create-portal.md). Podczas tworzenia magazynu kluczy wybierz opcję **Włącz ochronę przeczyszczania**, jak pokazano na poniższej ilustracji.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="Zrzut ekranu przedstawiający sposób włączania ochrony przed czyszczeniem podczas tworzenia magazynu kluczy":::

Aby włączyć ochronę przed przeczyszczaniem w istniejącym magazynie kluczy, wykonaj następujące kroki:

1. Przejdź do magazynu kluczy w Azure Portal.
1. W obszarze **Ustawienia**wybierz pozycję **Właściwości**.
1. W sekcji **przeczyszczanie ochrony** wybierz opcję **Włącz ochronę przeczyszczania**.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby utworzyć nowy magazyn kluczy przy użyciu programu PowerShell, zainstaluj [program PowerShell w](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) wersji 2.0.0 lub nowszej. Następnie Wywołaj polecenie [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) , aby utworzyć nowy magazyn kluczy. W przypadku wersji 2.0.0 i nowszych modułu AZ. Key magazynu usuwanie nietrwałe jest domyślnie włączone podczas tworzenia nowego magazynu kluczy.

W poniższym przykładzie jest tworzony nowy magazyn kluczy z włączonym usuwaniem nietrwałego i przeczyszczaniem. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Aby dowiedzieć się, jak włączyć ochronę przed przeczyszczaniem w istniejącym magazynie kluczy przy użyciu programu PowerShell, zobacz [jak używać nietrwałego usuwania przy użyciu programu PowerShell](../../key-vault/general/soft-delete-powershell.md).

Następnie przypisz tożsamość zarządzaną przypisaną przez system do konta magazynu. Ta tożsamość zarządzana zostanie użyta do nadania uprawnień kontu magazynu dostępu do magazynu kluczy. Aby uzyskać więcej informacji na temat tożsamości zarządzanych przypisanych do systemu, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure?](../../active-directory/managed-identities-azure-resources/overview.md).

Aby przypisać tożsamość zarządzaną przy użyciu programu PowerShell, wywołaj polecenie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount):

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Na koniec Skonfiguruj zasady dostępu dla magazynu kluczy, aby konto magazynu miało uprawnienia dostępu do niego. W tym kroku zostanie użyta zarządzana tożsamość, która została wcześniej przypisana do konta magazynu.

Aby ustawić zasady dostępu dla magazynu kluczy, wywołaj polecenie [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy):

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby utworzyć nowy magazyn kluczy przy użyciu interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Key webcreate](/cli/azure/keyvault#az-keyvault-create). Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

Aby dowiedzieć się, jak włączyć ochronę przed przeczyszczaniem w istniejącym magazynie kluczy za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [jak używać nietrwałego usuwania przy użyciu interfejsu wiersza polecenia](../../key-vault/general/soft-delete-cli.md).

Następnie przypisz tożsamość zarządzaną przypisaną przez system do konta magazynu. Ta tożsamość zarządzana zostanie użyta do nadania uprawnień kontu magazynu dostępu do magazynu kluczy. Aby uzyskać więcej informacji na temat tożsamości zarządzanych przypisanych do systemu, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure?](../../active-directory/managed-identities-azure-resources/overview.md).

Aby przypisać tożsamość zarządzaną za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage account Update](/cli/azure/storage/account#az-storage-account-update):

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Na koniec Skonfiguruj zasady dostępu dla magazynu kluczy, aby konto magazynu miało uprawnienia dostępu do niego. W tym kroku zostanie użyta zarządzana tożsamość, która została wcześniej przypisana do konta magazynu.

Aby ustawić zasady dostępu dla magazynu kluczy, wywołaj polecenie [AZ KeyBinding Set-Policy](/cli/azure/keyvault#az-keyvault-set-policy):

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

---

## <a name="add-a-key"></a>Dodaj klucz

Następnie Dodaj klucz w magazynie kluczy.

Szyfrowanie za pomocą usługi Azure Storage obsługuje klucze RSA i RSA-HSM o rozmiarach 2048, 3072 i 4096. Aby uzyskać więcej informacji na temat kluczy, zobacz **Key Vault klucze** w temacie [informacje Azure Key Vault klucze, wpisy tajne i certyfikaty](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Aby dowiedzieć się, jak dodać klucz z Azure Portal, zobacz [Szybki Start: Ustawianie i pobieranie klucza z Azure Key Vault przy użyciu Azure Portal](../../key-vault/keys/quick-create-portal.md).

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby dodać klucz przy użyciu programu PowerShell, wywołaj polecenie [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby dodać klucz za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj [AZ Key magazynu Create](/cli/azure/keyvault/key#az-keyvault-key-create). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta

Następnie skonfiguruj konto usługi Azure Storage tak, aby korzystało z kluczy zarządzanych przez klienta z Azure Key Vault, a następnie określ klucz skojarzony z kontem magazynu.

Podczas konfigurowania szyfrowania przy użyciu kluczy zarządzanych przez klienta można wybrać opcję automatycznej aktualizacji wersji klucza używanej do szyfrowania usługi Azure Storage, gdy nowa wersja jest dostępna w skojarzonym magazynie kluczy. Alternatywnie można jawnie określić wersję klucza do użycia podczas szyfrowania, dopóki wersja klucza nie zostanie zaktualizowana ręcznie.

> [!NOTE]
> Aby obrócić klucz, Utwórz nową wersję klucza w Azure Key Vault. Usługa Azure Storage nie obsługuje rotacji klucza w Azure Key Vault, więc musisz ręcznie obrócić klucz lub utworzyć funkcję, aby obrócić ją zgodnie z harmonogramem.

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>Skonfiguruj szyfrowanie automatycznej aktualizacji wersji kluczy

Usługa Azure Storage może automatycznie zaktualizować klucz zarządzany przez klienta używany do szyfrowania, aby użyć najnowszej wersji klucza. Gdy klucz zarządzany przez klienta zostanie obrócony w Azure Key Vault, usługa Azure Storage automatycznie zacznie używać najnowszej wersji klucza do szyfrowania.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Aby skonfigurować klucze zarządzane przez klienta z automatyczną aktualizacją wersji klucza w Azure Portal, wykonaj następujące kroki:

1. Przejdź do konta magazynu.
1. W bloku **Ustawienia** dla konta magazynu kliknij pozycję **szyfrowanie**. Wybierz opcję **klucze zarządzane przez klienta** , jak pokazano na poniższej ilustracji.

    ![Zrzut ekranu portalu przedstawiający opcję szyfrowania](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. Wybierz opcję **Wybierz z Key Vault** .
1. Wybierz pozycję **Wybierz magazyn kluczy i klucz**.
1. Wybierz magazyn kluczy zawierający klucz, którego chcesz użyć.
1. Wybierz klucz z magazynu kluczy.

   ![Zrzut ekranu przedstawiający sposób wybierania magazynu kluczy i klucza](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. Zapisz zmiany.

Po określeniu klucza Azure Portal wskazuje, że automatyczna aktualizacja wersji klucza jest włączona i wyświetla aktualnie używaną wersję klucza do szyfrowania.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="Zrzut ekranu przedstawiający sposób włączania ochrony przed czyszczeniem podczas tworzenia magazynu kluczy":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby skonfigurować klucze zarządzane przez klienta z automatyczną aktualizacją wersji klucza przy użyciu programu PowerShell, zainstaluj moduł [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) w wersji 2.0.0 lub nowszej.

Aby automatycznie zaktualizować wersję klucza dla klucza zarządzanego przez klienta, należy pominąć wersję klucza podczas konfigurowania szyfrowania z kluczami zarządzanymi przez klienta dla konta magazynu. Wywołaj polecenie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) , aby zaktualizować ustawienia szyfrowania konta magazynu, jak pokazano w poniższym przykładzie, i Dołącz opcję **-KeyvaultEncryption** , aby włączyć klucze zarządzane przez klienta dla konta magazynu.

Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby skonfigurować klucze zarządzane przez klienta z automatyczną aktualizacją wersji klucza przy użyciu interfejsu wiersza polecenia platformy Azure, zainstaluj [interfejs wiersza polecenia platformy Azure w wersji 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) lub nowszej. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Aby automatycznie zaktualizować wersję klucza dla klucza zarządzanego przez klienta, należy pominąć wersję klucza podczas konfigurowania szyfrowania z kluczami zarządzanymi przez klienta dla konta magazynu. Wywołaj [AZ Storage account Update](/cli/azure/storage/account#az-storage-account-update) , aby zaktualizować ustawienia szyfrowania konta magazynu, jak pokazano w poniższym przykładzie. Dołącz `--encryption-key-source` parametr i ustaw go, aby `Microsoft.Keyvault` włączyć klucze zarządzane przez klienta dla konta.

Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

---

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Skonfiguruj szyfrowanie dla ręcznej aktualizacji wersji kluczy

Jeśli wolisz ręcznie zaktualizować wersję klucza, jawnie określ wersję w momencie konfigurowania szyfrowania przy użyciu kluczy zarządzanych przez klienta. W takim przypadku usługa Azure Storage nie będzie automatycznie aktualizować wersji klucza, gdy nowa wersja zostanie utworzona w magazynie kluczy. Aby użyć nowej wersji klucza, należy ręcznie zaktualizować wersję używaną do szyfrowania usługi Azure Storage.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Aby skonfigurować klucze zarządzane przez klienta z ręczną aktualizacją wersji klucza w Azure Portal, określ identyfikator URI klucza, łącznie z wersją. Aby określić klucz jako identyfikator URI, wykonaj następujące kroki:

1. Aby zlokalizować identyfikator URI klucza w Azure Portal, przejdź do magazynu kluczy, a następnie wybierz ustawienie **klucze** . Wybierz odpowiedni klucz, a następnie kliknij klucz, aby wyświetlić jego wersje. Wybierz wersję klucza, aby wyświetlić ustawienia dla tej wersji.
1. Skopiuj wartość pola **Identyfikator klucza** , która zapewnia identyfikator URI.

    ![Zrzut ekranu przedstawiający identyfikator URI klucza magazynu kluczy](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. W ustawieniach **klucza szyfrowania** dla konta magazynu wybierz opcję **Wprowadź identyfikator URI klucza** .
1. Wklej identyfikator URI, który został skopiowany do pola **klucza URI** . Pomiń wersję klucza z identyfikatora URI, aby włączyć automatyczną aktualizację wersji klucza.

   ![Zrzut ekranu przedstawiający sposób wprowadzania identyfikatora URI klucza](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. Określ subskrypcję zawierającą magazyn kluczy.
1. Zapisz zmiany.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby skonfigurować klucze zarządzane przez klienta z ręczną aktualizacją wersji klucza, należy jawnie podać wersję klucza podczas konfigurowania szyfrowania dla konta magazynu. Wywołaj polecenie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) , aby zaktualizować ustawienia szyfrowania konta magazynu, jak pokazano w poniższym przykładzie, i Dołącz opcję **-KeyvaultEncryption** , aby włączyć klucze zarządzane przez klienta dla konta magazynu.

Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

W przypadku ręcznej aktualizacji wersji klucza należy zaktualizować ustawienia szyfrowania konta magazynu, aby użyć nowej wersji. Najpierw należy wywołać polecenie [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) , aby uzyskać najnowszą wersję klucza. Następnie Wywołaj polecenie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) , aby zaktualizować ustawienia szyfrowania konta magazynu tak, aby korzystały z nowej wersji klucza, jak pokazano w poprzednim przykładzie.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby skonfigurować klucze zarządzane przez klienta z ręczną aktualizacją wersji klucza, należy jawnie podać wersję klucza podczas konfigurowania szyfrowania dla konta magazynu. Wywołaj [AZ Storage account Update](/cli/azure/storage/account#az-storage-account-update) , aby zaktualizować ustawienia szyfrowania konta magazynu, jak pokazano w poniższym przykładzie. Dołącz `--encryption-key-source` parametr i ustaw go, aby `Microsoft.Keyvault` włączyć klucze zarządzane przez klienta dla konta.

Pamiętaj, aby zastąpić wartości zastępcze w nawiasach własnymi wartościami.

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

W przypadku ręcznej aktualizacji wersji klucza należy zaktualizować ustawienia szyfrowania konta magazynu, aby użyć nowej wersji. Najpierw należy wykonać zapytanie dotyczące identyfikatora URI magazynu kluczy przez wywołanie [AZ Key magazyn show](/cli/azure/keyvault#az-keyvault-show)i dla wersji klucza przez wywołanie [AZ Key magazynu list-Versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Następnie Wywołaj [AZ Storage account Update](/cli/azure/storage/account#az-storage-account-update) , aby zaktualizować ustawienia szyfrowania konta magazynu, aby użyć nowej wersji klucza, jak pokazano w poprzednim przykładzie.

---

## <a name="change-the-key"></a>Zmień klucz

Klucz używany do szyfrowania usługi Azure Storage można zmienić w dowolnym momencie.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Aby zmienić klucz za pomocą Azure Portal, wykonaj następujące kroki:

1. Przejdź do konta magazynu i Wyświetl ustawienia **szyfrowania** .
1. Wybierz magazyn kluczy i wybierz nowy klucz.
1. Zapisz zmiany.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby zmienić klucz w programie PowerShell, wywołaj polecenie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) , jak pokazano w [Konfiguruj szyfrowanie z kluczami zarządzanymi przez klienta](#configure-encryption-with-customer-managed-keys) i podaj nową nazwę klucza i wersję. Jeśli nowy klucz znajduje się w innym magazynie kluczy, należy również zaktualizować identyfikator URI magazynu kluczy.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zmienić klucz za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage account Update](/cli/azure/storage/account#az-storage-account-update) , jak pokazano na stronie [Konfigurowanie szyfrowania z kluczami zarządzanymi przez klienta](#configure-encryption-with-customer-managed-keys) i podaj nową nazwę klucza i wersję. Jeśli nowy klucz znajduje się w innym magazynie kluczy, należy również zaktualizować identyfikator URI magazynu kluczy.

---

## <a name="revoke-customer-managed-keys"></a>Odwołaj klucze zarządzane przez klienta

Odwołanie klucza zarządzanego przez klienta powoduje usunięcie skojarzenia między kontem magazynu i magazynem kluczy.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Aby odwołać klucze zarządzane przez klienta przy użyciu Azure Portal, wyłącz klucz zgodnie z opisem w temacie [wyłączanie kluczy zarządzanych przez klienta](#disable-customer-managed-keys).

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Klucze zarządzane przez klienta można odwołać przez usunięcie zasad dostępu magazynu kluczy. Aby odwołać klucz zarządzany przez klienta za pomocą programu PowerShell, wywołaj polecenie [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) , jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Klucze zarządzane przez klienta można odwołać przez usunięcie zasad dostępu magazynu kluczy. Aby odwołać klucz zarządzany przez klienta za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Key Delete-Policy](/cli/azure/keyvault#az-keyvault-delete-policy) , jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>Wyłącz klucze zarządzane przez klienta

Po wyłączeniu kluczy zarządzanych przez klienta konto magazynu będzie ponownie szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Aby wyłączyć klucze zarządzane przez klienta w Azure Portal, wykonaj następujące kroki:

1. Przejdź do konta magazynu i Wyświetl ustawienia **szyfrowania** .
1. Usuń zaznaczenie pola wyboru obok ustawienia **Użyj własnego klucza** .

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby wyłączyć klucze zarządzane przez klienta przy użyciu programu PowerShell, wywołaj polecenie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) z `-StorageEncryption` opcją, jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyłączyć klucze zarządzane przez klienta za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage account Update](/cli/azure/storage/account#az-storage-account-update) i ustaw wartość `--encryption-key-source parameter` na `Microsoft.Storage` , jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](storage-service-encryption.md)
- [Klucze zarządzane przez klienta dla szyfrowania usługi Azure Storage](customer-managed-keys-overview.md)
- [Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta, przechowywanych w Azure Key Vault zarządzanym module HSM (wersja zapoznawcza)](customer-managed-keys-configure-key-vault-hsm.md)
