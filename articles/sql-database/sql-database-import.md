---
title: Importowanie pliku BACPAC w celu utworzenia bazy danych
description: Utwórz bazę danych SQL newAzure, importując plik BACPAC.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: 4fe8eb0b363fc95ac5913e66884a2ed8f3d225a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086491"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Szybki Start: Importowanie pliku BACPAC do bazy danych w Azure SQL Database

Bazę danych SQL Server można zaimportować do bazy danych w Azure SQL Database przy użyciu pliku [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) . Dane można zaimportować z `BACPAC` pliku przechowywanego w usłudze Azure Blob Storage (tylko w przypadku magazynu w warstwie Standardowa) lub z magazynu lokalnego w lokalizacji lokalnej. Aby zmaksymalizować szybkość importowania, zapewniając większe i szybsze zasoby, Skaluj bazę danych do wyższej warstwy usług i rozmiaru obliczeń podczas procesu importowania. Po pomyślnym zakończeniu importowania można skalować w dół.

> [!NOTE]
> Poziom zgodności zaimportowanej bazy danych jest oparty na poziomie zgodności źródłowej bazy danych.

> [!IMPORTANT]
> Po zaimportowaniu bazy danych możesz wybrać opcję obsługi bazy danych na jej bieżącym poziomie zgodności (poziom 100 dla bazy danych AdventureWorks2008R2) lub na wyższym poziomie. Aby uzyskać więcej informacji o implikacjach i opcjach związanych z używaniem bazy danych na określonym poziomie zgodności, zobacz [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Instrukcja ALTER DATABASE — poziom zgodności). Zapoznaj się też z tematem [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), aby uzyskać informacje o dodatkowych ustawieniach na poziomie bazy danych związanych z poziomem zgodności.

## <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal

Obejrzyj ten film wideo, aby dowiedzieć się, jak importować z pliku BACPAC w Azure Portal lub kontynuować odczytywanie poniżej:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

[Azure Portal](https://portal.azure.com) obsługuje *tylko* Tworzenie pojedynczej bazy danych w programie Azure SQL Database i *tylko* z pliku BACPAC przechowywanego w usłudze Azure Blob Storage.

Migrowanie bazy danych do [wystąpienia zarządzanego](sql-database-managed-instance.md) z pliku BACPAC przy użyciu Azure PowerShell nie jest obecnie obsługiwane. Zamiast tego użyj SQL Server Management Studio lub sqlpackage.

> [!NOTE]
> Komputery przetwarzające żądania importu/eksportu przesłane za pomocą Azure Portal lub PowerShell muszą przechowywać plik BACPAC oraz pliki tymczasowe generowane przez strukturę aplikacji warstwy danych (DacFX). Wymagane miejsce na dysku zmienia się w różnych bazach danych o takim samym rozmiarze i może wymagać wolnego miejsca na dysku do 3 razy większym niż rozmiar bazy danych. Maszyny, na których jest uruchomione żądanie importu/eksportu, mają tylko miejsce na dysku lokalnym 450GB. W rezultacie niektóre żądania mogą zakończyć się niepowodzeniem z powodu `There is not enough space on the disk`błędu. W takim przypadku obejście ma na celu uruchomienie programu sqlpackage. exe na komputerze z wystarczającą ilością miejsca na dysku lokalnym. Zachęcamy do używania sqlpackage do importowania/eksportowania baz danych większych niż 150 GB, aby uniknąć tego problemu.

1. Aby zaimportować plik BACPAC do nowej pojedynczej bazy danych przy użyciu Azure Portal, Otwórz odpowiednią stronę serwera bazy danych, a następnie na pasku narzędzi wybierz pozycję **Importuj bazę danych**.  

   ![Import1 bazy danych](./media/sql-database-import/sql-server-import-database.png)

1. Wybierz konto magazynu i kontener dla pliku BACPAC, a następnie wybierz plik BACPAC, z którego ma zostać zaimportowany.

1. Określ nowy rozmiar bazy danych (zwykle taki sam jak pierwotny) i podaj miejsce docelowe SQL Server poświadczenia. Aby uzyskać listę możliwych wartości dla nowej bazy danych Azure SQL, zobacz [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Import2 bazy danych](./media/sql-database-import/sql-server-import-database-settings.png)

1. Kliknij przycisk **OK**.

1. Aby monitorować postęp importowania, Otwórz stronę serwera bazy danych, a następnie w obszarze **Ustawienia**wybierz pozycję **historia importowania/eksportowania**. Po pomyślnym zaimportowaniu stan **zakończył** się.

   ![Stan importowania bazy danych](./media/sql-database-import/sql-server-import-database-history.png)

1. Aby sprawdzić, czy baza danych jest aktywna na serwerze bazy danych, wybierz opcję **bazy danych SQL** i sprawdź, czy nowa baza danych jest w **trybie online**.

## <a name="using-sqlpackage"></a>Korzystanie z sqlpackage

Aby zaimportować bazę danych SQL Server przy użyciu narzędzia wiersza polecenia [sqlpackage](https://docs.microsoft.com/sql/tools/sqlpackage) , zobacz [Importowanie parametrów i właściwości](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). Narzędzia [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i [SQL Server Data Tools for Visual Studio zawierają element](https://msdn.microsoft.com/library/mt204009.aspx) sqlpackage. Możesz również pobrać najnowszy [pakiet sqlpackage](https://www.microsoft.com/download/details.aspx?id=53876) z centrum pobierania Microsoft.

W przypadku skalowania i wydajności zalecamy użycie elementu sqlpackage w większości środowisk produkcyjnych, a nie przy użyciu Azure Portal. Blog zespołu ds. pomocy technicznej SQL Server o migracji przy `BACPAC` użyciu plików można znaleźć w temacie [migrowanie z SQL Server do Azure SQL Database przy użyciu plików BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Następujące polecenie sqlpackage importuje bazę danych **AdventureWorks2008R2** z magazynu lokalnego do serwera Azure SQL Database o nazwie **mynewserver20170403**. Tworzy nową bazę danych o nazwie **myMigratedDatabase** z warstwą usługi **Premium** i celem usługi **P6** . Zmień te wartości zgodnie z potrzebami w danym środowisku.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Aby nawiązać połączenie z serwerem SQL Database zarządzania pojedynczą bazą danych za zaporą firmową, zapora musi mieć otwarty port 1433. Aby nawiązać połączenie z wystąpieniem zarządzanym, musisz mieć [połączenie typu punkt-lokacja](sql-database-managed-instance-configure-p2s.md) lub połączenie z usługą Express Route.

Ten przykład pokazuje, jak zaimportować bazę danych przy użyciu sqlpackage with Active Directory Universal Authentication.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>Korzystanie z programu PowerShell

> [!NOTE]
> [Wystąpienie zarządzane](sql-database-managed-instance.md) nie obsługuje obecnie migrowania bazy danych do bazy danych wystąpienia z pliku BACPAC przy użyciu Azure PowerShell. Aby zaimportować do wystąpienia zarządzanego, użyj SQL Server Management Studio lub sqlpackage.

> [!NOTE]
> Komputery przetwarzające żądania importu/eksportu przesłane za pomocą portalu lub programu PowerShell muszą przechowywać plik BACPAC oraz pliki tymczasowe generowane przez strukturę aplikacji warstwy danych (DacFX). Wymagane miejsce na dysku zmienia się znacznie w baz danych z tym samym rozmiarem i może trwać do 3 razy rozmiaru bazy danych. Maszyny, na których jest uruchomione żądanie importu/eksportu, mają tylko miejsce na dysku lokalnym 450GB. W związku z tym niektóre żądania mogą zakończyć się niepowodzeniem z powodu błędu "nie ma wystarczającej ilości miejsca na dysku". W takim przypadku obejście ma na celu uruchomienie programu sqlpackage. exe na komputerze z wystarczającą ilością miejsca na dysku lokalnym. Podczas importowania/eksportowania baz danych o rozmiarze większym niż 150 GB należy użyć sqlpackage, aby uniknąć tego problemu.

# <a name="powershell"></a>[Narzędzia](#tab/azure-powershell)

> [!IMPORTANT]
> Moduł programu PowerShell Azure Resource Manager (RM) jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. Moduł AzureRM będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020.  Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji o zgodności, zobacz [wprowadzenie do nowego Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az).

Użyj polecenia cmdlet [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) , aby przesłać żądanie importu bazy danych do usługi Azure SQL Database. W zależności od rozmiaru bazy danych Importowanie może zająć trochę czasu.

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

Aby sprawdzić postęp importowania, można użyć polecenia cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) . Uruchomienie polecenia cmdlet natychmiast po przejściu żądania zwykle `Status: InProgress`zwraca wartość. Importowanie zostało zakończone, gdy zobaczysz `Status: Succeeded`.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj polecenia [AZ-SQL-DB-import](/cli/azure/sql/db#az-sql-db-import) , aby przesłać żądanie importu bazy danych do usługi Azure SQL Database. W zależności od rozmiaru bazy danych Importowanie może zająć trochę czasu.

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> Aby zapoznać się z innym przykładem skryptu, zobacz [Importowanie bazy danych z pliku BACPAC](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Ograniczenia

- Importowanie do bazy danych w puli elastycznej nie jest obsługiwane. Dane można importować do pojedynczej bazy danych, a następnie przenieść ją do puli elastycznej.
- Usługa Import Export nie działa, gdy ustawienie Zezwalaj na dostęp do usług platformy Azure jest wyłączone. Można jednak obejść ten problem, ręcznie uruchamiając program sqlpackage. exe z maszyny wirtualnej platformy Azure lub wykonując eksport bezpośrednio w kodzie przy użyciu interfejsu API DACFx.

## <a name="import-using-wizards"></a>Importuj przy użyciu kreatorów

Możesz również użyć tych kreatorów.

- [Kreator importowania aplikacji warstwy danych w SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server Kreatora importu i eksportu](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak nawiązać połączenie z zaimportowanym SQL Database i wykonać zapytania, zobacz [Szybki Start: Azure SQL Database: użyj SQL Server Management Studio do nawiązywania połączeń i wykonywania zapytań dotyczących danych](sql-database-connect-query-ssms.md).
- Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
- Aby zapoznać się z omówieniem całego procesu migracji bazy danych SQL Server, łącznie z zaleceniami dotyczącymi wydajności, zobacz [SQL Server migracji bazy danych do Azure SQL Database](sql-database-single-database-migrate.md).
- Aby dowiedzieć się, jak bezpiecznie zarządzać i udostępniać klucze magazynu i sygnatury dostępu współdzielonego, zobacz [Przewodnik po zabezpieczeniach usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
