---
title: Włączanie usługi SQL TDE za pomocą usługi Azure Key Vault
description: Dowiedz się, jak skonfigurować bazę danych i magazyn danych SQL platformy Azure, aby rozpocząć korzystanie z przezroczystego szyfrowania danych (TDE) do szyfrowania w spoczynku przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 5b1c985eeec9af25ec576f4e2375c417dc376f95
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452761"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>Program PowerShell i cli: Włączanie przezroczystego szyfrowania danych za pomocą klucza zarządzanego przez klienta z usługi Azure Key Vault

W tym artykule opisano, jak używać klucza z usługi Azure Key Vault dla przezroczystego szyfrowania danych (TDE) w bazie danych SQL lub hurtowni danych. Aby dowiedzieć się więcej o integracji usługi TDE z usługą Azure Key Vault — pomoc techniczna "Bring Your Own Key" (BYOK), odwiedź witrynę [TDE z kluczami zarządzanymi przez klienta w usłudze Azure Key Vault.](transparent-data-encryption-byok-azure-sql.md) 

## <a name="prerequisites-for-powershell"></a>Wymagania wstępne programu PowerShell

- Musisz mieć subskrypcję platformy Azure i być administratorem w tej subskrypcji.
- [Zalecane, ale opcjonalne] Mieć sprzętowy moduł zabezpieczeń (HSM) lub lokalny magazyn kluczy do tworzenia lokalnej kopii materiału klucza TDE Protector.
- Musisz mieć zainstalowany i uruchomiony program Azure PowerShell.
- Utwórz usługę Azure Key Vault i Key do użycia w tde.
  - [Instrukcje dotyczące używania sprzętowego modułu zabezpieczeń (HSM) i usługi Key Vault](../key-vault/keys/hsm-protected-keys.md)
    - Magazyn kluczy musi mieć następującą właściwość, która ma być używana dla TDE:
  - [soft-delete](../key-vault/general/overview-soft-delete.md) i ochrona przed czyszczeniem
- Klucz musi mieć następujące atrybuty, które mają być używane dla TDE:
   - Brak daty ważności
   - Nie wyłączono
   - Możliwość wykonywania *get*, *wrap key*, *rozpakować kluczowych* operacji

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aby uzyskać instrukcje dotyczące instalacji modułu Az, zobacz [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps). Aby zapoznać się z określonymi poleceniami cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

Aby uzyskać szczegółowe dane dotyczące usługi Key Vault, zobacz [Instrukcje programu PowerShell z usługi Key Vault](../key-vault/secrets/quick-create-powershell.md) i Jak używać funkcji [usuwania programów Key Vault w programie PowerShell](../key-vault/general/soft-delete-powershell.md).

> [!IMPORTANT]
> Moduł programu PowerShell Azure Resource Manager (RM) jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Moduł AzureRM będzie nadal otrzymywać poprawki błędów co najmniej do grudnia 2020.  Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji na temat ich zgodności, zobacz [Przedstawianie nowego modułu Az programu Azure PowerShell.](/powershell/azure/new-azureps-module-az)

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Przypisywanie tożsamości usługi Azure AD do serwera

Jeśli masz istniejący serwer, użyj następujących czynności, aby dodać tożsamość usługi Azure AD do serwera:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

Jeśli tworzysz serwer, użyj polecenia cmdlet [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) z tagiem -Identity, aby dodać tożsamość usługi Azure AD podczas tworzenia serwera:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Udzielanie uprawnień usługi Key Vault do serwera

Użyj polecenia cmdlet [Set-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) aby udzielić serwerowi dostępu do magazynu kluczy przed użyciem klucza z niego dla TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Dodaj klucz Przechowalni kluczy do serwera i ustaw ochronę TDE

- Pobieranie identyfikatora klucza z magazynu kluczy za pomocą polecenia cmdlet [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0)
- Użyj polecenia cmdlet [Add-AzSqlServerKeyVaultKey,](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) aby dodać klucz z magazynu kluczy do serwera.
- Polecenie cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) służy do ustawiania klucza jako ochrony TDE dla wszystkich zasobów serwera.
- Użyj polecenia cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector,](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) aby potwierdzić, że ochrona TDE została skonfigurowana zgodnie z przeznaczeniem.

> [!NOTE]
> Łączna długość nazwy magazynu kluczy i nazwy klucza nie może przekraczać 94 znaków.

> [!TIP]
> Przykładowy keyid z przechowalni kluczy:https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>Włączanie TDE

Użyj polecenia cmdlet [Set-AzSqlDatabaseTransparentDataEncryption,](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) aby włączyć TDE.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

Teraz baza danych lub magazyn danych ma TDE włączone z kluczem szyfrowania w Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Sprawdzanie stanu szyfrowania i aktywności szyfrowania

Użyj [Get-AzSqlDatabaseTransparentDataEncryption,](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) aby uzyskać stan szyfrowania i [Get-AzSqlDatabaseTransparentDataEncryptionActivity,](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) aby sprawdzić postęp szyfrowania bazy danych lub magazynu danych.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zainstalować wymagany interfejs wiersza polecenia w wersji 2.0 lub nowszej i połączyć się z subskrypcją platformy Azure, zobacz [Instalowanie i konfigurowanie interfejsu wiersza polecenia azure cross-platform 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

Aby uzyskać szczegółowe dane dotyczące usługi Key Vault, zobacz [Zarządzanie magazynem kluczy przy użyciu interfejsu CLI 2.0](../key-vault/general/manage-with-cli2.md) i Jak używać funkcji [usuwania nietrwałego w programie Key Vault za pomocą interfejsu WIERSZA POLECENIA](../key-vault/general/soft-delete-cli.md).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Przypisywanie tożsamości usługi Azure AD do serwera

```azurecli
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> Zachowaj "principalID" z tworzenia serwera, jest to identyfikator obiektu używany do przypisywania uprawnień magazynu kluczy w następnym kroku

## <a name="grant-key-vault-permissions-to-your-server"></a>Udzielanie uprawnień usługi Key Vault do serwera

```azurecli
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Zachowaj klucz URI lub keyID nowego klucza dla następnego kroku, na przykład:https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Dodaj klucz Przechowalni kluczy do serwera i ustaw ochronę TDE

```azurecli
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> Łączna długość nazwy magazynu kluczy i nazwy klucza nie może przekraczać 94 znaków.

## <a name="turn-on-tde"></a>Włączanie TDE

```azurecli
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

Teraz baza danych lub magazyn danych ma TDE włączone z kluczem szyfrowania zarządzanym przez klienta w usłudze Azure Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Sprawdzanie stanu szyfrowania i aktywności szyfrowania

```azurecli
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Przydatne polecenia cmdlet programu PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Użyj polecenia cmdlet [Set-AzSqlDatabaseTransparentDataEncryption,](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) aby wyłączyć TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled"
   ```

- Użyj polecenia cmdlet [Get-AzSqlServerKeyVaultKey,](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) aby zwrócić listę kluczy usługi Key Vault dodanych do serwera.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Użyj [przycisku Usuń-AzSqlServerKeyVaultKey,](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) aby usunąć klucz magazynu kluczy z serwera.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

- Aby uzyskać ogólne ustawienia bazy danych, zobacz [az sql](/cli/azure/sql).

- Aby zapoznać się z ustawieniami klucza przechowalni, zobacz [az sql server key](/cli/azure/sql/server/key).

- Aby uzyskać informacje o ustawieniach TDE, zobacz [az sql server tde-key](/cli/azure/sql/server/tde-key) i [az sql db tde](/cli/azure/sql/db/tde).

* * *

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Sprawdź, czy wystąpi problem:

- Jeśli nie można odnaleźć magazynu kluczy, upewnij się, że masz odpowiednią subskrypcję.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Jeśli nie można dodać nowego klucza do serwera lub nie można zaktualizować nowego klucza jako ochrony TDE, sprawdź następujące kwestie:
   - Klucz nie powinien mieć daty ważności
   - Klucz musi mieć *przycisk get*, wrap *key*i *odłączyć* operacje klucza włączone.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak obrócić ochronę TDE serwera w celu spełnienia wymagań dotyczących zabezpieczeń: [Obróć ochronę transparentnego szyfrowania danych za pomocą programu PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- W przypadku zagrożenia bezpieczeństwa dowiedz się, jak usunąć potencjalnie naruszoną ochronę TDE: [Usuń potencjalnie naruszony klucz.](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
