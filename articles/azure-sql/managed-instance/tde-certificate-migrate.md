---
title: Migrowanie wystąpienia zarządzanego przez certyfikat TDE
description: Migrowanie certyfikatu ochrona klucza szyfrowania bazy danych za pomocą Transparent Data Encryption do wystąpienia zarządzanego usługi Azure SQL
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein, jovanpop
ms.date: 07/21/2020
ms.openlocfilehash: 08adfd7b69d580f6a231f13f9fb2793d828e16a3
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91618169"
---
# <a name="migrate-a-certificate-of-a-tde-protected-database-to-azure-sql-managed-instance"></a>Migrowanie certyfikatu chronionej bazy danych TDE do wystąpienia zarządzanego usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

W przypadku migrowania bazy danych chronionej przez program [transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) do wystąpienia zarządzanego usługi Azure SQL przy użyciu opcji przywracania natywnego należy zmigrować odpowiedni certyfikat z wystąpienia SQL Server przed przywróceniem bazy danych. Ten artykuł przeprowadzi Cię przez proces ręcznej migracji certyfikatu do wystąpienia zarządzanego Azure SQL:

> [!div class="checklist"]
>
> * Eksportowanie certyfikatu do pliku wymiany informacji osobistych (pfx)
> * Wyodrębnij certyfikat z pliku do ciągu Base-64
> * Przekaż za pomocą polecenia cmdlet programu PowerShell

Aby uzyskać alternatywną opcję przy użyciu w pełni zarządzanej usługi do bezproblemowej migracji bazy danych chronionej przez TDE i odpowiedniego certyfikatu, zobacz [jak migrować lokalną bazę danych do wystąpienia zarządzanego usługi Azure SQL przy użyciu Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> Migrowany certyfikat jest używany tylko do przywracania bazy danych chronionej przez TDE. Wkrótce po zakończeniu przywracania migrowany certyfikat zostanie zastąpiony przez inną ochronę, certyfikat zarządzany przez usługę lub klucz asymetryczny z magazynu kluczy, w zależności od typu TDE ustawionego w wystąpieniu.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym artykule potrzebne jest spełnienie następujących wymagań wstępnych:

* Narzędzie wiersza polecenia [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) zainstalowane na serwerze lokalnym lub innym komputerze z dostępem do certyfikatu wyeksportowanego jako plik. Narzędzie Pvk2Pfx jest częścią [zestawu sterowników przedsiębiorstwa systemu Windows](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk), czyli środowiska wiersza polecenia.
* Zainstalowany program [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell) w wersji 5.0 lub nowszej.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Upewnij się, że masz:

* Moduł Azure PowerShell [zainstalowany i zaktualizowany](https://docs.microsoft.com/powershell/azure/install-az-ps).
* [AZ. SQL module](https://www.powershellgallery.com/packages/Az.Sql).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez wystąpienie zarządzane usługi Azure SQL, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRM są zasadniczo identyczne.

Uruchom następujące polecenia w programie PowerShell, aby zainstalować/zaktualizować moduł:

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

* * *

## <a name="export-the-tde-certificate-to-a-pfx-file"></a>Eksportowanie certyfikatu TDE do pliku PFX

Certyfikat można wyeksportować bezpośrednio z wystąpienia źródłowego SQL Server lub z magazynu certyfikatów, jeśli jest tam przechowywany.

### <a name="export-the-certificate-from-the-source-sql-server-instance"></a>Wyeksportuj certyfikat z wystąpienia SQL Server źródłowego

Wykonaj następujące kroki, aby wyeksportować certyfikat z SQL Server Management Studio i przekonwertować go na format PFX. Nazwy ogólne *TDE_Cert* i *full_path* są używane dla nazw certyfikatów i plików oraz ścieżek w ramach kroków. Należy je zastąpić rzeczywistymi nazwami.

1. W programie SSMS Otwórz nowe okno zapytania i Połącz się z wystąpieniem źródła SQL Server.

1. Użyj następującego skryptu, aby wyświetlić listę baz danych chronionych przez TDE i uzyskać nazwę certyfikatu chroniącego szyfrowanie bazy danych do migracji:

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

   ![Lista certyfikatów TDE](./media/tde-certificate-migrate/onprem-certificate-list.png)

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

   ![Tworzenie kopii zapasowej certyfikatu TDE](./media/tde-certificate-migrate/backup-onprem-certificate.png)

1. Użyj konsoli programu PowerShell do kopiowania informacji o certyfikacie z pary nowo utworzonych plików do pliku PFX przy użyciu narzędzia Pvk2Pfx:

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-the-certificate-from-a-certificate-store"></a>Eksportowanie certyfikatu z magazynu certyfikatów

Jeśli certyfikat jest przechowywany w magazynie certyfikatów na komputerze lokalnym SQL Server, można go wyeksportować, wykonując następujące czynności:

1. Otwórz konsolę programu PowerShell i uruchom następujące polecenie, aby otworzyć przystawkę Certyfikaty w programie Microsoft Management Console:

   ```cmd
   certlm
   ```

2. W przystawce MMC Certyfikaty rozwiń węzeł osobiste > certyfikaty, aby wyświetlić listę certyfikatów.

3. Kliknij prawym przyciskiem myszy certyfikat, a następnie kliknij pozycję **Eksportuj**.

4. Postępuj zgodnie z instrukcjami kreatora, aby wyeksportować certyfikat i klucz prywatny do formatu PFX.

## <a name="upload-the-certificate-to-azure-sql-managed-instance-using-an-azure-powershell-cmdlet"></a>Przekaż certyfikat do wystąpienia zarządzanego usługi Azure SQL za pomocą polecenia cmdlet Azure PowerShell

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Rozpocznij od kroków przygotowawczych w programie PowerShell:

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. Po wykonaniu wszystkich kroków przygotowania Uruchom następujące polecenia, aby przekazać zakodowany certyfikat Base-64 do docelowego wystąpienia zarządzanego:

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -AsByteStream
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Najpierw należy [skonfigurować magazyn kluczy platformy Azure](/azure/key-vault/key-vault-manage-with-cli2) z plikiem *PFX* .

1. Rozpocznij od kroków przygotowawczych w programie PowerShell:

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. Po wykonaniu wszystkich kroków przygotowania Uruchom następujące polecenia, aby przekazać zakodowany certyfikat Base-64 do docelowego wystąpienia zarządzanego:

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

Certyfikat jest teraz dostępny dla określonego wystąpienia zarządzanego, a kopia zapasowa odpowiedniej bazy danych chronionej przez TDE można przywrócić pomyślnie.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób migracji certyfikatu chroniącego klucz szyfrowania bazy danych z Transparent Data Encryption z wystąpienia SQL Server lokalnego lub IaaS do wystąpienia zarządzanego usługi Azure SQL.

Zobacz [przywracanie kopii zapasowej bazy danych do wystąpienia zarządzanego usługi Azure SQL](restore-sample-database-quickstart.md) , aby dowiedzieć się, jak przywrócić kopię zapasową bazy danych do wystąpienia zarządzanego Azure SQL.
