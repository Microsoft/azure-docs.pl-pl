---
title: Obróć funkcję ochrony TDE (PowerShell & interfejs wiersza polecenia platformy Azure)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Dowiedz się, jak obrócić funkcję ochrony Transparent Data Encryption (TDE) dla serwera na platformie Azure używanej przez Azure SQL Database i usługę Azure Synapse Analytics przy użyciu programu PowerShell i interfejsu wiersza polecenia platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 699434e89872b9525ea99883bc4fcbc86757ab8e
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619852"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>Obróć funkcję ochrony Transparent Data Encryption (TDE)
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


W tym artykule opisano rotację kluczy dla [serwera](logical-servers.md) przy użyciu funkcji ochrony TDE z Azure Key Vault. Obrócenie funkcji ochrony logicznej TDE dla serwera oznacza przełączenie na nowy klucz asymetryczny chroniący bazy danych na serwerze. Rotacja kluczy jest operacją online i należy wykonać zaledwie kilka sekund, ponieważ spowoduje to odszyfrowanie i ponowne zaszyfrowanie klucza szyfrowania danych bazy danych, a nie całej bazy danych.

W tym przewodniku omówiono dwie opcje rotacji funkcji ochrony TDE na serwerze.

> [!NOTE]
> Należy wznowić wstrzymaną pulę SQL usługi Azure Synapse Analytics przed rozpoczęciem rotacji kluczy.

> [!IMPORTANT]
> Nie usuwaj poprzednich wersji klucza po przejściu. Gdy klucze są zestawiane, niektóre dane są nadal szyfrowane przy użyciu poprzednich kluczy, takich jak starsze kopie zapasowe bazy danych.

## <a name="prerequisites"></a>Wymagania wstępne

- W tym przewodniku założono, że korzystasz już z klucza z Azure Key Vault jako funkcji ochrony TDE dla Azure SQL Database lub analizy Synapse Azure. Zobacz [transparent Data Encryption z obsługą BYOK](transparent-data-encryption-byok-overview.md).
- Musisz mieć Azure PowerShell zainstalowane i uruchomione.
- [Zalecane, ale opcjonalne] Najpierw Utwórz materiał klucza dla ochrony TDE w sprzętowym module zabezpieczeń (HSM) lub lokalnym magazynie kluczy, a następnie zaimportuj kluczowy materiał do Azure Key Vault. Postępuj zgodnie z [instrukcjami dotyczącymi używania sprzętowego modułu zabezpieczeń (HSM) i Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) , aby dowiedzieć się więcej.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uzyskać instrukcje dotyczące instalacji modułu Az, zobacz [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps). Aby poznać konkretne polecenia cmdlet, zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell (RM) jest nadal obsługiwany, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. Moduł AzureRM będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020.  Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji o zgodności, zobacz [wprowadzenie do nowego Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az).

# <a name="the-azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uzyskać więcej instalacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

* * *

## <a name="manual-key-rotation"></a>Ręczny obrót klucza

Ręczne rotacja kluczy używa następujących poleceń w celu dodania zupełnie nowego klucza, który może znajdować się w nowej nazwie klucza lub nawet w innym magazynie kluczy. Użycie tej metody umożliwia dodanie tego samego klucza do różnych magazynów kluczy w celu obsługi scenariuszy wysokiej dostępności i odzyskiwania geograficznego.

> [!NOTE]
> Łączna długość nazwy magazynu kluczy i nazwy klucza nie może przekraczać 94 znaków.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Użyj poleceń cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)i [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="the-azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj polecenia [AZ Key magazynu Create](/cli/azure/keyvault/key#az-keyvault-key-create), [AZ SQL Server Key Create](/cli/azure/sql/server/key#az-sql-server-key-create)i [AZ SQL Server TDE-Key Set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) .

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>Przełącz tryb ochrony TDE

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

- Aby przełączyć funkcję ochrony TDE z trybu zarządzanego przez firmę Microsoft do BYOK, należy użyć polecenia cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Aby przełączyć funkcję ochrony TDE z trybu BYOK do zarządzanego przez firmę Microsoft, należy użyć polecenia cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

W poniższych przykładach użyto [AZ SQL Server TDE-Key Set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- Aby przełączyć funkcję ochrony TDE z trybu zarządzanego przez firmę Microsoft do BYOK,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Aby przełączyć funkcję ochrony TDE z trybu BYOK do zarządzanego przez firmę Microsoft,

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Następne kroki

- W przypadku zagrożenia bezpieczeństwa należy dowiedzieć się, jak usunąć potencjalnie naruszoną ochronę TDE: [Usuń potencjalnie złamany klucz](transparent-data-encryption-byok-remove-tde-protector.md).

- Rozpocznij pracę z integracją Azure Key Vault i wsparcie Bring Your Own Key dla TDE: [Włącz TDE przy użyciu własnego klucza z Key Vault przy użyciu programu PowerShell](transparent-data-encryption-byok-configure.md).
