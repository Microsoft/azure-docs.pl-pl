---
title: Rotacja funkcji ochrony TDE (program PowerShell & interfejsu wiersza polecenia platformy Azure)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Dowiedz się, jak obrócić Transparent Data Encryption (TDE) dla serwera na platformie Azure używanego przez usługę Azure SQL Database i Azure Synapse Analytics przy użyciu programu PowerShell i interfejsu wiersza polecenia platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 67bcd8597314530f26481ef840644ffbc056b033
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777567"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>Obracanie funkcji ochrony Transparent Data Encryption (TDE)
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


W tym artykule opisano rotację kluczy [dla serwera przy](logical-servers.md) użyciu funkcji ochrony TDE z Azure Key Vault. Obracanie logicznej funkcji ochrony TDE dla serwera oznacza przełączenie na nowy klucz asymetryczny, który chroni bazy danych na serwerze. Rotacja kluczy jest operacją online i powinna potrwać tylko kilka sekund, ponieważ tylko odszyfrowuje i szyfruje ponownie klucz szyfrowania danych bazy danych, a nie całą bazę danych.

W tym przewodniku omówiono dwie opcje rotacji funkcji ochrony TDE na serwerze.

> [!NOTE]
> Wstrzymana dedykowana pula SQL w Azure Synapse Analytics musi zostać wznowiona przed rotacją kluczy.

> [!IMPORTANT]
> Nie usuwaj poprzednich wersji klucza po przewłaczaniu. Podczas przewłacania kluczy niektóre dane są nadal szyfrowane przy użyciu poprzednich kluczy, takich jak starsze kopie zapasowe bazy danych.

## <a name="prerequisites"></a>Wymagania wstępne

- W tym przewodniku założono, że już używasz klucza z usługi Azure Key Vault jako funkcji ochrony TDE dla Azure SQL Database lub Azure Synapse Analytics. Zobacz [Transparent Data Encryption z obsługą byOK.](transparent-data-encryption-byok-overview.md)
- Musisz mieć zainstalowane Azure PowerShell i uruchomione.
- [Zalecane, ale opcjonalne] Najpierw utwórz materiał klucza dla funkcji ochrony TDE w sprzętowym module zabezpieczeń (HSM) lub lokalnym magazynie kluczy, a następnie zaimportuj materiał klucza do Azure Key Vault. Postępuj zgodnie z instrukcjami dotyczącymi korzystania ze sprzętowego modułu zabezpieczeń [(HSM) i](../../key-vault/general/overview.md) Key Vault, aby dowiedzieć się więcej.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uzyskać instrukcje dotyczące instalacji modułu Az, zobacz [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps). Aby uzyskać informacje o konkretnych poleceniach cmdlet, [zobacz AzureRM.Sql](/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Moduł PowerShell Azure Resource Manager (RM) jest nadal obsługiwany, ale cały przyszły rozwój jest dla modułu Az.Sql. Moduł AzureRM będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r.  Argumenty poleceń w module Az i modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji na temat ich zgodności, zobacz [Wprowadzenie do nowego modułu Azure PowerShell Az.](/powershell/azure/new-azureps-module-az)

# <a name="the-azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uzyskać informacje na temat instalacji, [zobacz Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

* * *

## <a name="manual-key-rotation"></a>Ręczna rotacja kluczy

Ręczna rotacja kluczy używa następujących poleceń, aby dodać zupełnie nowy klucz, który może być pod nową nazwą klucza, a nawet innym magazynem kluczy. Takie podejście obsługuje dodawanie tego samego klucza do różnych magazynów kluczy w celu obsługi scenariuszy wysokiej dostępności i geograficznego odzyskiwania po awarii.

> [!NOTE]
> Łączna długość nazwy magazynu kluczy i nazwy klucza nie może przekraczać 94 znaków.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Użyj polecenia cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)i [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

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

Użyj poleceń [az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create), az sql server key [create](/cli/azure/sql/server/key#az_sql_server_key_create)i az sql [server tde-key set.](/cli/azure/sql/server/tde-key#az_sql_server_tde_key_set)

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>Przełączanie trybu ochrony TDE

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

- Aby przełączyć ochronę TDE z zarządzanej przez firmę Microsoft na tryb BYOK, użyj polecenia cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Aby przełączyć ochronę TDE z trybu BYOK na zarządzany przez firmę Microsoft, użyj polecenia cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

W poniższych przykładach użyj [polecenia az sql server tde-key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- Aby przełączyć ochrony TDE z zarządzanych przez firmę Microsoft do trybu BYOK,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Aby przełączyć ochrony TDE z byOK tryb zarządzany przez firmę Microsoft,

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Następne kroki

- W przypadku zagrożenia bezpieczeństwa dowiedz się, jak usunąć potencjalnie naruszoną ochronę TDE: Usuń potencjalnie [naruszony klucz.](transparent-data-encryption-byok-remove-tde-protector.md)

- Wprowadzenie do integracji Azure Key Vault i obsługi Bring Your Own Key TDE: włączanie funkcji TDE przy użyciu własnego klucza z Key Vault [programu PowerShell.](transparent-data-encryption-byok-configure.md)