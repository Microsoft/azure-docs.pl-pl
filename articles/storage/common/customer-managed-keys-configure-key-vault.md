---
title: Konfigurowanie szyfrowania za pomocą kluczy zarządzanych przez klienta przechowywanych w usłudze Azure Key Vault
titleSuffix: Azure Storage
description: Dowiedz się, jak skonfigurować szyfrowanie usługi Azure Storage przy użyciu kluczy zarządzanych przez klienta przechowywanych w usłudze Azure Key Vault przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/16/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 77a01a270f47ddacb71962188e7fedd0a0a9f6d0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790441"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>Konfigurowanie szyfrowania za pomocą kluczy zarządzanych przez klienta przechowywanych w usłudze Azure Key Vault

Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w spoczynku. Domyślnie dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, możesz zarządzać własnymi kluczami. Klucze zarządzane przez klienta muszą być przechowywane w programie Azure Key Vault lub Key Vault Zarządzany sprzętowy model zabezpieczeń (HSM) (wersja zapoznawcza).

W tym artykule pokazano, jak skonfigurować szyfrowanie przy użyciu kluczy zarządzanych przez klienta przechowywanych w magazynie kluczy przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się, jak skonfigurować szyfrowanie przy użyciu kluczy zarządzanych przez klienta przechowywanych w zarządzanym modułze HSM, zobacz Configure [encryption with customer-managed keys stored in Azure Key Vault Managed HSM (preview) (Konfigurowanie](customer-managed-keys-configure-key-vault-hsm.md)szyfrowania przy użyciu kluczy zarządzanych przez klienta przechowywanych w zarządzanym modułem HSM (wersja zapoznawcza) ).

> [!NOTE]
> Azure Key Vault i Azure Key Vault HSM obsługują te same interfejsy API i interfejsy zarządzania dla konfiguracji.

## <a name="configure-a-key-vault"></a>Konfigurowanie magazynu kluczy

Do przechowywania kluczy zarządzanych przez klienta można użyć nowego lub istniejącego magazynu kluczy. Konto magazynu i magazyn kluczy muszą znajdować się w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach.

Używanie kluczy zarządzanych przez klienta z szyfrowaniem usługi Azure Storage wymaga, aby dla magazynu kluczy włączono zarówno ochronę usuwania nie soft, jak i ochrony przed przeczyszczaniami. Usuwanie nieuprawniane jest domyślnie włączone podczas tworzenia nowego magazynu kluczy i nie można go wyłączyć. Ochronę przed przeczyszczaniem można włączyć podczas tworzenia magazynu kluczy lub po jego utworzeniu.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby dowiedzieć się, jak utworzyć magazyn kluczy za pomocą Azure Portal, zobacz Szybki [start:](../../key-vault/general/quick-create-portal.md)tworzenie magazynu kluczy przy użyciu Azure Portal . Podczas tworzenia magazynu kluczy wybierz pozycję Włącz ochronę przed **przeczyszczaniem,** jak pokazano na poniższej ilustracji.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="Zrzut ekranu przedstawiający sposób włączania ochrony przed przeczyszczaniem podczas tworzenia magazynu kluczy":::

Aby włączyć ochronę przed przeczyszczaniem w istniejącym magazynie kluczy, wykonaj następujące kroki:

1. Przejdź do magazynu kluczy w Azure Portal.
1. W **obszarze Ustawienia** wybierz pozycję **Właściwości.**
1. W sekcji **Ochrona przed przeczyszczaniem** wybierz pozycję **Włącz ochronę przed przeczyszczaniem.**

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby utworzyć nowy magazyn kluczy przy użyciu programu PowerShell, zainstaluj wersję 2.0.0 lub nowszą modułu [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) programu PowerShell. Następnie [wywołaj pozycję New-AzKeyVault,](/powershell/module/az.keyvault/new-azkeyvault) aby utworzyć nowy magazyn kluczy. W wersji 2.0.0 lub nowszej modułu Az.KeyVault usuwanie nie softowe jest domyślnie włączone podczas tworzenia nowego magazynu kluczy.

Poniższy przykład tworzy nowy magazyn kluczy z włączoną ochroną przed usuwaniem nieukreślonym i ochroną przed przeczyszczaniem. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Aby dowiedzieć się, jak włączyć ochronę przed przeczyszczaniem w istniejącym magazynie kluczy przy użyciu programu PowerShell, zobacz Jak używać usuwania nie [soft-delete w programie PowerShell.](../../key-vault/general/key-vault-recovery.md)

Następnie przypisz przypisaną przez system tożsamość zarządzaną do konta magazynu. Użyjesz tej tożsamości zarządzanej, aby udzielić kontu magazynu uprawnień dostępu do magazynu kluczy. Aby uzyskać więcej informacji na temat tożsamości zarządzanych przypisanych przez system, zobacz [Co to są tożsamości zarządzane dla zasobów platformy Azure?](../../active-directory/managed-identities-azure-resources/overview.md).

Aby przypisać tożsamość zarządzaną przy użyciu programu PowerShell, [wywołaj polecenia Set-AzStorageAccount:](/powershell/module/az.storage/set-azstorageaccount)

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Na koniec skonfiguruj zasady dostępu dla magazynu kluczy, aby konto magazynu ma uprawnienia dostępu do niego. W tym kroku użyjesz tożsamości zarządzanej, która została wcześniej przypisana do konta magazynu.

Aby ustawić zasady dostępu dla magazynu kluczy, wywołaj element [Set-AzKeyVaultAccessPolicy:](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby utworzyć nowy magazyn kluczy przy użyciu interfejsu wiersza polecenia platformy Azure, [wywołaj element az keyvault create](/cli/azure/keyvault#az_keyvault_create). Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

Aby dowiedzieć się, jak włączyć ochronę przed przeczyszczaniem w istniejącym magazynie kluczy przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz Jak używać usuwania nie [soft-delete za pomocą interfejsu wiersza polecenia](../../key-vault/general/key-vault-recovery.md).

Następnie przypisz przypisaną przez system tożsamość zarządzaną do konta magazynu. Użyjesz tej tożsamości zarządzanej, aby udzielić kontu magazynu uprawnień dostępu do magazynu kluczy. Aby uzyskać więcej informacji na temat tożsamości zarządzanych przypisanych przez system, zobacz [Co to są tożsamości zarządzane dla zasobów platformy Azure?](../../active-directory/managed-identities-azure-resources/overview.md).

Aby przypisać tożsamość zarządzaną przy użyciu interfejsu wiersza polecenia platformy Azure, [wywołaj az storage account update](/cli/azure/storage/account#az_storage_account_update):

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Na koniec skonfiguruj zasady dostępu dla magazynu kluczy, aby konto magazynu było w stanie uzyskać do niego dostęp. W tym kroku użyjesz tożsamości zarządzanej, która została wcześniej przypisana do konta magazynu.

Aby ustawić zasady dostępu dla magazynu kluczy, wywołaj [element az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy):

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

## <a name="add-a-key"></a>Dodawanie klucza

Następnie dodaj klucz w magazynie kluczy.

Szyfrowanie usługi Azure Storage obsługuje klucze RSA i RSA-HSM o rozmiarach 2048, 3072 i 4096. Aby uzyskać więcej informacji na temat kluczy, zobacz [About keys (Informacje o kluczach).](../../key-vault/keys/about-keys.md)

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby dowiedzieć się, jak dodać klucz za pomocą Azure Portal, zobacz Szybki [start:](../../key-vault/keys/quick-create-portal.md)ustawianie i pobieranie klucza z usługi Azure Key Vault pomocą Azure Portal .

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby dodać klucz przy użyciu programu PowerShell, [wywołaj polecenia Add-AzKeyVaultKey.](/powershell/module/az.keyvault/add-azkeyvaultkey) Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby dodać klucz za pomocą interfejsu wiersza polecenia platformy Azure, [wywołaj element az keyvault key create.](/cli/azure/keyvault/key#az_keyvault_key_create) Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta

Następnie skonfiguruj konto usługi Azure Storage do używania kluczy zarządzanych przez klienta z usługą Azure Key Vault, a następnie określ klucz do skojarzenia z kontem magazynu.

Podczas konfigurowania szyfrowania przy użyciu kluczy zarządzanych przez klienta możesz wybrać opcję automatycznego aktualizowania wersji klucza używanej do szyfrowania usługi Azure Storage zawsze wtedy, gdy nowa wersja jest dostępna w skojarzonym magazynie kluczy. Alternatywnie można jawnie określić wersję klucza, która ma być używana do szyfrowania, dopóki wersja klucza nie zostanie zaktualizowana ręcznie.

> [!NOTE]
> Aby obrócić klucz, utwórz nową wersję klucza w Azure Key Vault. Usługa Azure Storage nie obsługuje rotacji klucza w Azure Key Vault, dlatego należy obrócić klucz ręcznie lub utworzyć funkcję, aby obrócić go zgodnie z harmonogramem.

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>Konfigurowanie szyfrowania w celu automatycznego aktualizowania wersji kluczy

Usługa Azure Storage może automatycznie aktualizować klucz zarządzany przez klienta, który jest używany do szyfrowania, aby używać najnowszej wersji klucza. Gdy klucz zarządzany przez klienta zostanie obrócony w Azure Key Vault, usługa Azure Storage automatycznie rozpocznie korzystanie z najnowszej wersji klucza do szyfrowania.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby skonfigurować klucze zarządzane przez klienta przy użyciu automatycznego aktualizowania wersji klucza w Azure Portal, wykonaj następujące kroki:

1. Przejdź do konta magazynu.
1. W bloku **Ustawienia** dla konta magazynu kliknij pozycję **Szyfrowanie**. Domyślnie zarządzanie kluczami jest ustawione na wartość **Klucze zarządzane przez firmę Microsoft,** jak pokazano na poniższej ilustracji.

    ![Zrzut ekranu portalu przedstawiający opcję szyfrowania](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. Wybierz opcję **Klucze zarządzane przez klienta.**
1. Wybierz **opcję Wybierz Key Vault** wybierz pozycję.
1. Wybierz **pozycję Wybierz magazyn kluczy i klucz**.
1. Wybierz magazyn kluczy zawierający klucz, którego chcesz użyć.
1. Wybierz klucz z magazynu kluczy.

   ![Zrzut ekranu przedstawiający sposób wybierania magazynu kluczy i klucza](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. Zapisz zmiany.

Po podano klucz, klucz Azure Portal, że automatyczne aktualizowanie wersji klucza jest włączona i wyświetla wersję klucza aktualnie używaną do szyfrowania.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="Zrzut ekranu przedstawiający automatyczne aktualizowanie włączonej wersji klucza":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby skonfigurować klucze zarządzane przez klienta przy użyciu automatycznego aktualizowania wersji klucza przy użyciu programu PowerShell, zainstaluj moduł [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) w wersji 2.0.0 lub nowszej.

Aby automatycznie zaktualizować wersję klucza zarządzanego przez klienta, należy pominąć wersję klucza podczas konfigurowania szyfrowania przy użyciu kluczy zarządzanych przez klienta dla konta magazynu. Wywołaj funkcję [Set-AzStorageAccount,](/powershell/module/az.storage/set-azstorageaccount) aby zaktualizować ustawienia szyfrowania konta magazynu, jak pokazano w poniższym przykładzie, i dołącz opcję **-KeyvaultEncryption,** aby włączyć klucze zarządzane przez klienta dla konta magazynu.

Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby skonfigurować klucze zarządzane przez klienta przy użyciu automatycznego aktualizowania wersji klucza za pomocą interfejsu wiersza polecenia platformy Azure, zainstaluj interfejs wiersza polecenia platformy Azure w wersji [2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) lub nowszej. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

Aby automatycznie zaktualizować wersję klucza zarządzanego przez klienta, należy pominąć wersję klucza podczas konfigurowania szyfrowania za pomocą kluczy zarządzanych przez klienta dla konta magazynu. Wywołaj [pozycję az storage account update,](/cli/azure/storage/account#az_storage_account_update) aby zaktualizować ustawienia szyfrowania konta magazynu, jak pokazano w poniższym przykładzie. Dołącz parametr `--encryption-key-source` i ustaw go na wartość , aby `Microsoft.Keyvault` włączyć klucze zarządzane przez klienta dla konta.

Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami.

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

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Konfigurowanie szyfrowania w celu ręcznego aktualizowania wersji kluczy

Jeśli wolisz ręcznie zaktualizować wersję klucza, jawnie określ wersję w czasie konfigurowania szyfrowania przy użyciu kluczy zarządzanych przez klienta. W takim przypadku usługa Azure Storage nie zaktualizuje automatycznie wersji klucza po utworzeniu nowej wersji w magazynie kluczy. Aby użyć nowej wersji klucza, należy ręcznie zaktualizować wersję używaną do szyfrowania usługi Azure Storage.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby skonfigurować klucze zarządzane przez klienta przy użyciu ręcznego aktualizowania wersji klucza w Azure Portal, określ klucz URI, w tym wersję. Aby określić klucz jako wartość URI, wykonaj następujące kroki:

1. Aby zlokalizować klucz URI w Azure Portal, przejdź do magazynu kluczy i wybierz **ustawienie** Klucze. Wybierz żądany klucz, a następnie kliknij go, aby wyświetlić jego wersje. Wybierz wersję klucza, aby wyświetlić ustawienia dla tej wersji.
1. Skopiuj wartość pola **Identyfikator klucza,** która zawiera identyfikator URI.

    ![Zrzut ekranu przedstawiający URI klucza magazynu kluczy](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. W **ustawieniach Klucz** szyfrowania dla konta magazynu wybierz opcję **Wprowadź klucz URI.**
1. Wklej skopiowany adres URI w polu **Key URI (Klucz URI).** Pomiń wersję klucza z URI, aby umożliwić automatyczne aktualizowanie wersji klucza.

   ![Zrzut ekranu przedstawiający sposób wprowadzania klucza URI](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. Określ subskrypcję zawierającą magazyn kluczy.
1. Zapisz zmiany.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby skonfigurować klucze zarządzane przez klienta przy użyciu ręcznego aktualizowania wersji klucza, należy jawnie podać wersję klucza podczas konfigurowania szyfrowania dla konta magazynu. Wywołaj funkcję [Set-AzStorageAccount,](/powershell/module/az.storage/set-azstorageaccount) aby zaktualizować ustawienia szyfrowania konta magazynu, jak pokazano w poniższym przykładzie, i dołącz opcję **-KeyvaultEncryption,** aby włączyć klucze zarządzane przez klienta dla konta magazynu.

Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

W przypadku ręcznej aktualizacji wersji klucza należy zaktualizować ustawienia szyfrowania konta magazynu, aby korzystać z nowej wersji. Najpierw wywołaj [element Get-AzKeyVaultKey,](/powershell/module/az.keyvault/get-azkeyvaultkey) aby uzyskać najnowszą wersję klucza. Następnie [wywołaj pozycję Set-AzStorageAccount,](/powershell/module/az.storage/set-azstorageaccount) aby zaktualizować ustawienia szyfrowania konta magazynu w celu użycia nowej wersji klucza, jak pokazano w poprzednim przykładzie.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby skonfigurować klucze zarządzane przez klienta przy użyciu ręcznego aktualizowania wersji klucza, należy jawnie podać wersję klucza podczas konfigurowania szyfrowania dla konta magazynu. Wywołaj [pozycję az storage account update,](/cli/azure/storage/account#az_storage_account_update) aby zaktualizować ustawienia szyfrowania konta magazynu, jak pokazano w poniższym przykładzie. Uwzględnij parametr i ustaw go na wartość , `--encryption-key-source` `Microsoft.Keyvault` aby włączyć klucze zarządzane przez klienta dla konta.

Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami.

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

W przypadku ręcznej aktualizacji wersji klucza należy zaktualizować ustawienia szyfrowania konta magazynu, aby korzystać z nowej wersji. Najpierw przeszukaj zapytanie o numer URI magazynu kluczy, wywołując [element az keyvault show](/cli/azure/keyvault#az_keyvault_show)i dla wersji klucza, wywołując element az [keyvault key list-versions](/cli/azure/keyvault/key#az_keyvault_key_list-versions). Następnie [wywołaj element az storage account update,](/cli/azure/storage/account#az_storage_account_update) aby zaktualizować ustawienia szyfrowania konta magazynu w celu użycia nowej wersji klucza, jak pokazano w poprzednim przykładzie.

---

## <a name="change-the-key"></a>Zmienianie klucza

W dowolnym momencie możesz zmienić klucz, który jest ci przy użyciu szyfrowania usługi Azure Storage.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby zmienić klucz za pomocą Azure Portal, wykonaj następujące kroki:

1. Przejdź do konta magazynu i wyświetl **ustawienia szyfrowania.**
1. Wybierz magazyn kluczy i wybierz nowy klucz.
1. Zapisz zmiany.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby zmienić klucz przy użyciu programu PowerShell, wywołaj polecenia [Set-AzStorageAccount,](/powershell/module/az.storage/set-azstorageaccount) jak pokazano w tece Configure [encryption with customer-managed keys](#configure-encryption-with-customer-managed-keys) (Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta) i podaj nową nazwę i wersję klucza. Jeśli nowy klucz znajduje się w innym magazynie kluczy, należy również zaktualizować jego URI.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zmienić klucz za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj element [az storage account update,](/cli/azure/storage/account#az_storage_account_update) jak pokazano w tece [Configure encryption with customer-managed keys](#configure-encryption-with-customer-managed-keys) (Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta) i podaj nową nazwę i wersję klucza. Jeśli nowy klucz znajduje się w innym magazynie kluczy, należy również zaktualizować jego URI.

---

## <a name="revoke-customer-managed-keys"></a>Odwoływanie kluczy zarządzanych przez klienta

Odwołanie klucza zarządzanego przez klienta powoduje usunięcie skojarzenia między kontem magazynu i magazynem kluczy.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby odwołać klucze zarządzane przez klienta za pomocą Azure Portal, wyłącz klucz zgodnie z opisem w tece [Wyłączanie kluczy zarządzanych przez klienta.](#disable-customer-managed-keys)

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Klucze zarządzane przez klienta można odwołać, usuwając zasady dostępu magazynu kluczy. Aby odwołać klucz zarządzany przez klienta przy użyciu programu PowerShell, wywołaj polecenie [Remove-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Klucze zarządzane przez klienta można odwołać, usuwając zasady dostępu magazynu kluczy. Aby odwołać klucz zarządzany przez klienta za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj [polecenie az keyvault delete-policy,](/cli/azure/keyvault#az_keyvault_delete_policy) jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>Wyłączanie kluczy zarządzanych przez klienta

Po wyłączeniu kluczy zarządzanych przez klienta twoje konto magazynu zostanie ponownie zaszyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby wyłączyć klucze zarządzane przez klienta w Azure Portal, wykonaj następujące kroki:

1. Przejdź do konta magazynu i wyświetl **ustawienia** szyfrowania.
1. Usuń zaznaczenie pola wyboru obok ustawienia **Użyj własnego** klucza.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby wyłączyć klucze zarządzane przez klienta przy użyciu programu PowerShell, wywołaj funkcję [Set-AzStorageAccount z](/powershell/module/az.storage/set-azstorageaccount) opcją , jak pokazano `-StorageEncryption` w poniższym przykładzie. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyłączyć klucze zarządzane przez klienta za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj funkcję [az storage account update](/cli/azure/storage/account#az_storage_account_update) i ustaw wartość , jak `--encryption-key-source parameter` `Microsoft.Storage` pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami i użyć zmiennych zdefiniowanych w poprzednich przykładach.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](storage-service-encryption.md)
- [Klucze zarządzane przez klienta na potrzeby szyfrowania w usłudze Azure Storage](customer-managed-keys-overview.md)
- [Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta przechowywanych w zarządzanym Azure Key Vault HSM (wersja zapoznawcza)](customer-managed-keys-configure-key-vault-hsm.md)
