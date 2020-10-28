---
title: Włącz program SQL TDE w Azure Key Vault
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Dowiedz się, jak skonfigurować Azure SQL Database i usługę Azure Synapse Analytics, aby rozpocząć korzystanie z funkcji Transparent Data Encryption (TDE) do szyfrowania w środowisku REST przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 38be8b97b3255e4e63301e693d2a5f295e8d801b
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779972"
---
# <a name="powershell-and-the-azure-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell i interfejs wiersza polecenia platformy Azure: Włącz Transparent Data Encryption z kluczem zarządzanym przez klienta w programie Azure Key Vault
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

W tym artykule przedstawiono sposób użycia klucza z Azure Key Vault dla Transparent Data Encryption (TDE) na platformie Azure SQL Database lub Azure Synapse Analytics (dawniej SQL Data Warehouse). Aby dowiedzieć się więcej na temat obsługi TDE Azure Key Vault z integracją Bring Your Own Key (BYOK), odwiedź [TDE z kluczami zarządzanymi przez klienta w Azure Key Vault](transparent-data-encryption-byok-overview.md).

## <a name="prerequisites-for-powershell"></a>Wymagania wstępne dotyczące programu PowerShell

- Musisz mieć subskrypcję platformy Azure i być administratorem tej subskrypcji.
- [Zalecane, ale opcjonalne] Posiadanie sprzętowego modułu zabezpieczeń (HSM) lub lokalnego magazynu kluczy na potrzeby tworzenia lokalnej kopii materiału klucza ochrony TDE.
- Musisz mieć Azure PowerShell zainstalowane i uruchomione.
- Utwórz Azure Key Vault i klucz do użycia dla TDE.
  - [Instrukcje dotyczące korzystania z sprzętowego modułu zabezpieczeń (HSM) i Key Vault](../../key-vault/keys/hsm-protected-keys.md)
    - Magazyn kluczy musi mieć następującą właściwość, która ma być używana dla TDE:
  - Ochrona przed [usuwaniem](../../key-vault/general/soft-delete-overview.md) i przeczyszczaniem
- Klucz musi mieć następujące atrybuty, aby można było używać go dla TDE:
  - Brak daty wygaśnięcia
  - Niewyłączone
  - Możliwość wykonania operacji *Get* , *zawijania klucza* , *odpakowania klucza*

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aby uzyskać instrukcje dotyczące instalacji modułu Az, zobacz [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps). Aby poznać konkretne polecenia cmdlet, zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/).

Aby uzyskać szczegółowe informacje na temat Key Vault, zobacz [instrukcje programu PowerShell w artykule Key Vault](../../key-vault/secrets/quick-create-powershell.md) i [sposób korzystania z Key Vault nietrwałego usuwania przy użyciu programu PowerShell](../../key-vault/general/soft-delete-powershell.md).

> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell (RM) jest nadal obsługiwany, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. Moduł AzureRM będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020.  Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji o zgodności, zobacz [wprowadzenie do nowego Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az).

## <a name="assign-an-azure-active-directory-azure-ad-identity-to-your-server"></a>Przypisywanie tożsamości usługi Azure Active Directory (Azure AD) do serwera

Jeśli masz istniejący [serwer](logical-servers.md), użyj poniższego, aby dodać tożsamość usługi Azure Active Directory (Azure AD) do serwera:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

W przypadku tworzenia serwera należy użyć polecenia cmdlet [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) ze znacznikiem Identity, aby dodać tożsamość usługi Azure AD podczas tworzenia serwera:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Przyznaj Key Vault uprawnienia do serwera

Użyj polecenia cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) , aby udzielić serwerowi dostępu do magazynu kluczy przed użyciem klucza z niego dla TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Dodaj klucz Key Vault do serwera i ustaw funkcję ochrony TDE

- Użyj polecenia cmdlet [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) , aby pobrać identyfikator klucza z magazynu kluczy
- Użyj polecenia cmdlet [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) , aby dodać klucz z Key Vault do serwera programu.
- Użyj polecenia cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) , aby ustawić klucz jako funkcję ochrony TDE dla wszystkich zasobów serwera.
- Użyj polecenia cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) , aby upewnić się, że funkcja ochrony TDE została skonfigurowana zgodnie z oczekiwaniami.

> [!NOTE]
> Łączna długość nazwy magazynu kluczy i nazwy klucza nie może przekraczać 94 znaków.

> [!TIP]
> Przykład KeyId z Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>Włącz TDE

Aby włączyć TDE, należy użyć polecenia cmdlet [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) .

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

Teraz baza danych lub magazyn danych ma TDE z kluczem szyfrowania w Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Sprawdź stan szyfrowania i aktywność szyfrowania

Użyj elementu [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) , aby pobrać stan szyfrowania i [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) w celu sprawdzenia postępu szyfrowania bazy danych lub magazynu danych.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="the-azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zainstalować wymaganą wersję interfejsu wiersza polecenia platformy Azure (w wersji 2,0 lub nowszej) i nawiązać połączenie z subskrypcją platformy Azure, zobacz [Instalowanie i Konfigurowanie międzyplatformowego interfejsu Command-Line platformy azure 2,0](/cli/azure/install-azure-cli).

Aby uzyskać szczegółowe informacje na temat Key Vault, zobacz [zarządzanie Key Vault przy użyciu interfejsu wiersza polecenia 2,0](../../key-vault/general/manage-with-cli2.md) i [sposobu używania Key Vault nietrwałego usuwania przy użyciu interfejsu wiersza polecenia](../../key-vault/general/soft-delete-cli.md).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Przypisywanie tożsamości usługi Azure AD do serwera

```azurecli
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> Nie można utworzyć serwera "principalID", który jest identyfikatorem obiektu używanym do przypisywania uprawnień magazynu kluczy w następnym kroku

## <a name="grant-key-vault-permissions-to-your-server"></a>Przyznaj Key Vault uprawnienia do serwera

```azurecli
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Zachowaj identyfikator URI klucza lub keyID nowego klucza dla następnego kroku, na przykład: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Dodaj klucz Key Vault do serwera i ustaw funkcję ochrony TDE

```azurecli
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> Łączna długość nazwy magazynu kluczy i nazwy klucza nie może przekraczać 94 znaków.

## <a name="turn-on-tde"></a>Włącz TDE

```azurecli
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

Teraz baza danych lub magazyn danych ma TDE włączony przy użyciu klucza szyfrowania zarządzanego przez klienta w Azure Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Sprawdź stan szyfrowania i aktywność szyfrowania

```azurecli
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Przydatne polecenia cmdlet programu PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Aby wyłączyć TDE, należy użyć polecenia cmdlet [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) .

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled"
   ```

- Użyj polecenia cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) , aby zwrócić listę kluczy Key Vault dodanych do serwera.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Aby usunąć klucz Key Vault z serwera, użyj elementu [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) .

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

- Ogólne ustawienia bazy danych można znaleźć w temacie [AZ SQL](/cli/azure/sql).

- Aby uzyskać ustawienia klucza magazynu, zobacz [AZ SQL Server Key](/cli/azure/sql/server/key).

- W przypadku ustawień TDE zobacz [AZ SQL Server TDE-Key](/cli/azure/sql/server/tde-key) i [AZ SQL DB TDE](/cli/azure/sql/db/tde).

* * *

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wystąpi problem, sprawdź następujące kwestie:

- Jeśli nie można znaleźć magazynu kluczy, upewnij się, że jesteś w odpowiedniej subskrypcji.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="the-azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Jeśli nie można dodać nowego klucza do serwera lub nie można zaktualizować nowego klucza jako funkcji ochrony TDE, sprawdź następujące kwestie:
   - Klucz nie powinien mieć daty wygaśnięcia
   - Klucz musi mieć włączone operacje *Get* , *zawijania* klucza i *dezawijania kluczy* .

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak obrócić funkcję ochrony TDE na serwerze, aby zachować zgodność z wymaganiami dotyczącymi zabezpieczeń: [obróć transparent Data Encryption funkcję ochrony przy użyciu programu PowerShell](transparent-data-encryption-byok-key-rotation.md).
- W przypadku zagrożenia bezpieczeństwa należy dowiedzieć się, jak usunąć potencjalnie naruszoną ochronę TDE: [Usuń potencjalnie złamany klucz](transparent-data-encryption-byok-remove-tde-protector.md).