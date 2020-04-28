---
title: Migrowanie wystąpienia zarządzanego przez certyfikat TDE
description: Migruj certyfikat ochrony klucza szyfrowania bazy danych z użyciem przezroczystego szyfrowania danych do Azure SQL Database wystąpienia zarządzanego
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
ms.date: 04/25/2019
ms.openlocfilehash: 0f6e379287323d9353acd887cf30d5c9c0065959
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "74555381"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-database-managed-instance"></a>Migrowanie certyfikatu bazy danych chronionej przez funkcję TDE do wystąpienia zarządzanego usługi Azure SQL Database

Podczas migrowania bazy danych chronionej przez [transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) do Azure SQL Database wystąpienia zarządzanego przy użyciu opcji przywracania natywnego należy zmigrować odpowiedni certyfikat z SQL Server lokalnego lub IaaS przed przywróceniem bazy danych. Ten artykuł przeprowadzi Cię przez proces ręcznej migracji certyfikatu do wystąpienia zarządzanego usługi Azure SQL Database:

> [!div class="checklist"]
> * Eksportowanie certyfikatu do pliku wymiany informacji osobistych (pfx)
> * Wyodrębnianie certyfikatu z pliku do ciągu Base-64
> * Przekazywanie go przy użyciu polecenia cmdlet programu PowerShell

Alternatywną możliwość bezproblemowej migracji zarówno bazy danych chronionej przez funkcję TDE, jak i odpowiedniego certyfikatu, korzystającą z w pełni zarządzanej usługi opisano w artykule [Jak przeprowadzić migrację lokalnej bazy danych do wystąpienia zarządzanego przy użyciu usługi Azure Database Migration Service](../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> Migrowany certyfikat jest używany tylko do przywracania bazy danych chronionej przez funkcję TDE. Wkrótce po zakończeniu przywracania migrowany certyfikat zostanie zastąpiony przez inną funkcję ochrony, certyfikat zarządzany przez usługę lub klucz asymetryczny z magazynu kluczy, w zależności od typu przezroczystego szyfrowania danych ustawionego w wystąpieniu.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym artykule potrzebne jest spełnienie następujących wymagań wstępnych:

- Narzędzie wiersza polecenia [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) zainstalowane na serwerze lokalnym lub innym komputerze z dostępem do certyfikatu wyeksportowanego jako plik. Narzędzie Pvk2Pfx stanowi część [Zestawu sterowników systemu Windows dla przedsiębiorstw](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk) — autonomicznego, samodzielnego środowiska wiersza polecenia.
- Zainstalowany program [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell) w wersji 5.0 lub nowszej.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Upewnij się, że masz:

- Moduł Azure PowerShell [zainstalowany i zaktualizowany](https://docs.microsoft.com/powershell/azure/install-az-ps).
- [AZ. SQL module](https://www.powershellgallery.com/packages/Az.Sql).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

Uruchom następujące polecenia w programie PowerShell, aby zainstalować/zaktualizować moduł:

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

* * *

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>Eksportowanie certyfikatu TDE do pliku wymiany informacji osobistych (pfx)

Certyfikat można wyeksportować bezpośrednio ze źródłowego programu SQL Server lub z magazynu certyfikatów, jeśli jest tam przechowywany.

### <a name="export-certificate-from-the-source-sql-server"></a>Eksportowanie certyfikatu ze źródłowego programu SQL Server

Wykonaj następujące kroki, aby wyeksportować certyfikat przy użyciu programu SQL Server Management Studio i przekonwertować go w format pfx. W tych krokach dla nazw plików i certyfikatów oraz ścieżek są używane ogólne nazwy *TDE_Cert* i *full_path*. Należy je zastąpić rzeczywistymi nazwami.

1. W programie SSMS otwórz nowe okno zapytania i połącz się ze źródłowym programem SQL Server.

1. Za pomocą następującego skryptu wyświetl listę baz danych chronionych przez funkcję TDE i pobierz nazwę certyfikatu chroniącego szyfrowanie bazy danych, który wymaga migracji:

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![Lista certyfikatów TDE](./media/sql-database-managed-instance-migrate-tde-certificate/onprem-certificate-list.png)

1. Wykonaj następujący skrypt, aby wyeksportować certyfikat do pary plików (cer i pvk), w których znajdują się informacje o kluczu publicznym i prywatnym:

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![Tworzenie kopii zapasowej certyfikatu TDE](./media/sql-database-managed-instance-migrate-tde-certificate/backup-onprem-certificate.png)

1. Korzystając z konsoli programu PowerShell, skopiuj informacje o certyfikacie z pary nowo utworzonych plików do pliku wymiany informacji osobistych (pfx) za pomocą narzędzia Pvk2Pfx:

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>Eksportowanie certyfikatu z magazynu certyfikatów

Jeśli certyfikat jest przechowywany w magazynie certyfikatów komputera lokalnego programu SQL Server, można go wyeksportować, wykonując następujące kroki:

1. Otwórz konsolę programu PowerShell i uruchom następujące polecenie, aby otworzyć przystawkę Certyfikaty programu Microsoft Management Console:

   ```cmd
   certlm
   ```

2. W przystawce Certyfikaty programu MMC rozwiń ścieżkę Osobiste -> Certyfikaty, aby wyświetlić listę certyfikatów

3. Kliknij prawym przyciskiem myszy certyfikat, a następnie kliknij polecenie Eksportuj…

4. Postępuj zgodnie z poleceniami kreatora, aby wyeksportować certyfikat i klucz prywatny do formatu wymiany informacji osobistych

## <a name="upload-certificate-to-azure-sql-database-managed-instance-using-azure-powershell-cmdlet"></a>Przekaż certyfikat do Azure SQL Database wystąpienia zarządzanego przy użyciu polecenia cmdlet Azure PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Rozpocznij od kroków przygotowawczych w programie PowerShell:

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. Po ukończeniu wszystkich kroków przygotowawczych uruchom następujące polecenia, aby przekazać certyfikat zakodowany w formacie Base-64 do docelowego wystąpienia zarządzanego:

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Musisz najpierw [skonfigurować Azure Key Vault](/azure/key-vault/key-vault-manage-with-cli2) przy użyciu pliku *PFX* .

1. Rozpocznij od kroków przygotowawczych w programie PowerShell:

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target Managed Instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. Po ukończeniu wszystkich kroków przygotowawczych uruchom następujące polecenia, aby przekazać certyfikat zakodowany w formacie Base-64 do docelowego wystąpienia zarządzanego:

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

Certyfikat jest teraz dostępny dla określonego wystąpienia zarządzanego i można pomyślnie przywrócić kopię zapasową odpowiedniej bazy danych chronionej przez funkcję TDE.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób migrowania klucza szyfrowania ochrony certyfikatu bazy danych Transparent Data Encryption z SQL Server lokalnego lub IaaS do Azure SQL Database wystąpienia zarządzanego.

Zobacz [Przywracanie kopii zapasowej bazy danych do wystąpienia zarządzanego usługi Azure SQL Database](sql-database-managed-instance-get-started-restore.md), aby dowiedzieć się, jak przywrócić kopię zapasową bazy danych do wystąpienia zarządzanego usługi Azure SQL Database.
