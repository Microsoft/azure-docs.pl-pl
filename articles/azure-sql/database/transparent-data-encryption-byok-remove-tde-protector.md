---
title: Usuwanie funkcji ochrony TDE (program PowerShell & interfejsu wiersza polecenia platformy Azure)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Dowiedz się, jak reagować na potencjalnie naruszoną ochrony TDE dla usługi Azure SQL Database lub Azure Synapse Analytics przy użyciu funkcji TDE z obsługą funkcji Bring Your Own Key (BYOK).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: f98dcdd9c1a479703c82c01b4fd240507ea355de
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784470"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Usuwanie ochrony Transparent Data Encryption (TDE) przy użyciu programu PowerShell
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


W tym temacie opisano sposób reagowania na potencjalnie naruszoną ochronę TDE dla usługi Azure SQL Database lub Azure Synapse Analytics, która korzysta z funkcji TDE z kluczami zarządzanymi przez klienta w programie Azure Key Vault — Bring Your Own Key (BYOK). Aby dowiedzieć się więcej na temat obsługi funkcji BYOK dla funkcji TDE, zobacz [stronę przeglądu](transparent-data-encryption-byok-overview.md).

> [!CAUTION]
> Procedury opisane w tym artykule powinny być wykonywane tylko w skrajnych przypadkach lub w środowiskach testowych. Dokładnie przejrzyj te kroki, ponieważ usunięcie aktywnie używanych funkcji ochrony TDE z Azure Key Vault **spowoduje, że baza danych stanie się niedostępna.**

Jeśli kiedykolwiek podejrzewa się, że klucz został naruszony, na przykład usługa lub użytkownik miał nieautoryzowany dostęp do klucza, najlepiej usunąć klucz.

Należy pamiętać, że po usunięciu funkcji ochrony TDE w programie Key Vault w ciągu maksymalnie 10 minut wszystkie zaszyfrowane bazy danych zaczną odrzucać wszystkie połączenia za pomocą odpowiedniego komunikatu o błędzie i zmienią jego stan na [Niedostępny.](./transparent-data-encryption-byok-overview.md#inaccessible-tde-protector)

Ten przewodnik z wytycznymi przechodzi przez dwa podejścia w zależności od żądanego wyniku po złamania zabezpieczeń odpowiedzi na zdarzenie:

- Aby bazy danych w programie Azure SQL Database/Azure Synapse Analytics **niedostępne.**
- Aby uniemożliwić dostęp do baz danych Azure SQL Database /Azure Azure Synapse Analytics **.**

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć subskrypcję platformy Azure i być jej administratorem
- Musisz mieć zainstalowany Azure PowerShell i uruchomiony.
- W tym przewodniku założono, że już używasz klucza z usługi Azure Key Vault jako ochrony TDE dla Azure SQL Database lub Azure Synapse. Zobacz [Transparent Data Encryption z obsługą byOK,](transparent-data-encryption-byok-overview.md) aby dowiedzieć się więcej.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

 Aby uzyskać instrukcje dotyczące instalacji modułu Az, zobacz [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps). Aby uzyskać informacje o konkretnych poleceniach cmdlet, [zobacz AzureRM.Sql](/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Moduł PowerShell Azure Resource Manager (RM) jest nadal obsługiwany, ale cały przyszły rozwój jest dla modułu Az.Sql. Moduł AzureRM będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r.  Argumenty poleceń w module Az i modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji na temat ich zgodności, [zobacz Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az)(Wprowadzenie do nowego modułu Az Azure PowerShell Az).

# <a name="the-azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uzyskać informacje na temat instalacji, [zobacz Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

* * *

## <a name="check-tde-protector-thumbprints"></a>Sprawdzanie odcisków palca funkcji ochrony TDE

W poniższych krokach opisano sposób sprawdzania odcisków palca funkcji ochrony TDE, które są nadal w użyciu przez wirtualne pliki dziennika (VLF) danej bazy danych.
Odcisk palca bieżącej funkcji ochrony TDE bazy danych i identyfikatora bazy danych można znaleźć, uruchamiając:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint]
 FROM [sys].[dm_database_encryption_keys]
```

Następujące zapytanie zwraca VLFs i funkcji ochrony TDE odpowiednie odciski palca w użyciu. Każdy inny odcisk palca odnosi się do innego klucza w Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

Alternatywnie możesz użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure:

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Polecenie programu PowerShell **Get-AzureRmSqlServerKeyVaultKey** zawiera odcisk palca funkcji ochrony TDE używanej w zapytaniu, dzięki czemu możesz zobaczyć, które klucze zachować i które klucze usunąć w usłudze   AKV. Tylko klucze, które nie są już używane przez bazę danych, można bezpiecznie usunąć z Azure Key Vault.

# <a name="the-azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Polecenie programu PowerShell **az sql server key show** zawiera odcisk palca funkcji ochrony TDE używanej w zapytaniu, dzięki czemu możesz zobaczyć, które klucze należy zachować i które klucze usunąć w udatku   AKV. Tylko klucze, które nie są już używane przez bazę danych, można bezpiecznie usunąć z Azure Key Vault.

* * *

## <a name="keep-encrypted-resources-accessible"></a>Zachowanie dostępności zaszyfrowanych zasobów

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Utwórz nowy [klucz w Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Upewnij się, że ten nowy klucz jest tworzony w oddzielnym magazynie kluczy od potencjalnie naruszonej ochrony TDE, ponieważ kontrola dostępu jest aprowizowana na poziomie magazynu.

2. Dodaj nowy klucz do serwera przy użyciu polecenia cmdlet [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) i [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) i zaktualizuj go jako nową ochronę TDE serwera.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Upewnij się, że serwer i wszystkie repliki zostały zaktualizowane do nowej funkcji ochrony TDE przy użyciu polecenia cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector)

   > [!NOTE]
   > Propagowanie nowej funkcji ochrony TDE do wszystkich baz danych i pomocniczych baz danych na serwerze może potrwać kilka minut.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Tworzenie kopii [zapasowej nowego klucza w programie](/powershell/module/az.keyvault/backup-azkeyvaultkey) Key Vault.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Usuń naruszony klucz z Key Vault polecenia cmdlet [Remove-AzKeyVaultKey.](/powershell/module/az.keyvault/remove-azkeyvaultkey)

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Aby przywrócić klucz do Key Vault w przyszłości za pomocą polecenia cmdlet [Restore-AzKeyVaultKey:](/powershell/module/az.keyvault/restore-azkeyvaultkey)

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="the-azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uzyskać informacje o poleceniach, zobacz magazyn [kluczy interfejsu wiersza polecenia platformy Azure.](/cli/azure/keyvault/key)

1. Utwórz nowy [klucz w Key Vault](/cli/azure/keyvault/key#az_keyvault_key_create). Upewnij się, że ten nowy klucz został utworzony w oddzielnym magazynie kluczy od potencjalnie naruszonej ochrony TDE, ponieważ kontrola dostępu jest aprowizowana na poziomie magazynu.

2. Dodaj nowy klucz do serwera i zaktualizuj go jako nową funkcje ochrony TDE serwera.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Upewnij się, że serwer i wszystkie repliki zostały zaktualizowane do nowej ochrony TDE.

   > [!NOTE]
   > Propagowanie nowej funkcji ochrony TDE do wszystkich baz danych i pomocniczych baz danych na serwerze może potrwać kilka minut.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Tworzenie kopii zapasowej nowego klucza w programie Key Vault.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Usuń naruszony klucz z Key Vault.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Aby przywrócić klucz do Key Vault w przyszłości.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="make-encrypted-resources-inaccessible"></a>Uniemożliwić dostęp do zaszyfrowanych zasobów

1. Porzuć bazy danych, które są szyfrowane za pomocą klucza, który potencjalnie został naruszony.

   Automatycznie tworzyć kopię zapasową bazy danych i plików dziennika, więc przywracanie do punktu w czasie bazy danych można wykonać w dowolnym momencie (o ile po poświecie klucza). Bazy danych muszą zostać usunięte przed usunięciem aktywnej ochrony TDE, aby zapobiec potencjalnej utracie danych z maksymalnie 10 minut najnowszych transakcji.

2. Kopię zapasową materiału klucza funkcji ochrony TDE w Key Vault.
3. Usuń potencjalnie naruszony klucz z Key Vault

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak obrócić ochronę TDE serwera w celu zapewnienia zgodności z wymaganiami zabezpieczeń: Obracanie ochrony [Transparent Data Encryption przy użyciu programu PowerShell](transparent-data-encryption-byok-key-rotation.md)
- Wprowadzenie do obsługi Bring Your Own Key TDE: włączanie funkcji TDE przy użyciu własnego klucza z Key Vault [programu PowerShell](transparent-data-encryption-byok-configure.md)