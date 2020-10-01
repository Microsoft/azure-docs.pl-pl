---
title: Zaimportuj plik BACPAC, aby utworzyć bazę danych w Azure SQL Database
description: Utwórz nową bazę danych w Azure SQL Database lub wystąpienia zarządzanego Azure SQL z pliku BACPAC.
services: sql-database
ms.service: sql-db-mi
ms.subservice: migrate
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: ec3da815a9ca3e55fd65f1f0a64a81b74c6d2979
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613751"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Szybki Start: Importowanie pliku BACPAC do bazy danych w Azure SQL Database lub wystąpienia zarządzanego Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Bazę danych SQL Server można zaimportować do wystąpienia zarządzanego Azure SQL Database lub SQL przy użyciu pliku [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) . Dane można zaimportować z pliku BACPAC przechowywanego w usłudze Azure Blob Storage (tylko w przypadku magazynu w warstwie Standardowa) lub z magazynu lokalnego. Aby maksymalnie zwiększyć szybkość importowania przez zapewnienie większej ilości szybszych zasobów, przeprowadź skalowanie bazy danych do wyższej warstwy usługi i rozmiaru obliczeniowego na czas trwania procesu importowania. Po pomyślnym zakończeniu importowania możesz przeprowadzić skalowanie w dół.

> [!NOTE]
> Poziom zgodności zaimportowanej bazy danych jest oparty na poziomie zgodności źródłowej bazy danych.

> [!IMPORTANT]
> Po zaimportowaniu bazy danych możesz wybrać opcję obsługi bazy danych na jej bieżącym poziomie zgodności (poziom 100 dla bazy danych AdventureWorks2008R2) lub na wyższym poziomie. Aby uzyskać więcej informacji o implikacjach i opcjach związanych z używaniem bazy danych na określonym poziomie zgodności, zobacz [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Instrukcja ALTER DATABASE — poziom zgodności). Zapoznaj się też z tematem [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), aby uzyskać informacje o dodatkowych ustawieniach na poziomie bazy danych związanych z poziomem zgodności.

## <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal

Obejrzyj ten film wideo, aby dowiedzieć się, jak importować z pliku BACPAC w Azure Portal lub kontynuować odczytywanie poniżej:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

[Azure Portal](https://portal.azure.com) obsługuje *tylko* Tworzenie pojedynczej bazy danych w programie Azure SQL Database i *tylko* z pliku BACPAC przechowywanego w usłudze Azure Blob Storage.

Aby przeprowadzić migrację bazy danych do [wystąpienia zarządzanego usługi Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) z pliku BACPAC, użyj SQL Server Management Studio lub sqlpackage, używając Azure Portal lub Azure PowerShell nie jest obecnie obsługiwane.

> [!NOTE]
> Komputery przetwarzające żądania importu/eksportu przesłane za pomocą Azure Portal lub PowerShell muszą przechowywać plik BACPAC oraz pliki tymczasowe generowane przez strukturę aplikacji warstwy danych (DacFX). Wymagane miejsce na dysku zmienia się w różnych bazach danych o takim samym rozmiarze i może wymagać wolnego miejsca na dysku do 3 razy większym niż rozmiar bazy danych. Maszyny, na których jest uruchomione żądanie importu/eksportu, mają tylko miejsce na dysku lokalnym 450GB. W rezultacie niektóre żądania mogą zakończyć się niepowodzeniem z powodu błędu `There is not enough space on the disk` . W takim przypadku obejście ma na celu uruchomienie sqlpackage.exe na komputerze z wystarczającą ilością miejsca na dysku lokalnym. Zachęcamy do używania sqlpackage do importowania/eksportowania baz danych większych niż 150 GB, aby uniknąć tego problemu.

1. Aby zaimportować plik BACPAC do nowej pojedynczej bazy danych przy użyciu Azure Portal, Otwórz odpowiednią stronę serwera, a następnie na pasku narzędzi wybierz pozycję **Importuj bazę danych**.  

   ![Import1 bazy danych](./media/database-import/sql-server-import-database.png)

1. Wybierz konto magazynu i kontener dla pliku BACPAC, a następnie wybierz plik BACPAC, z którego ma zostać zaimportowany.

1. Określ nowy rozmiar bazy danych (zwykle taki sam jak pierwotny) i podaj miejsce docelowe SQL Server poświadczenia. Aby uzyskać listę możliwych wartości dla nowej bazy danych w Azure SQL Database, zobacz [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Import2 bazy danych](./media/database-import/sql-server-import-database-settings.png)

1. Kliknij przycisk **OK**.

1. Aby monitorować postęp importowania, Otwórz stronę serwera bazy danych, a następnie w obszarze **Ustawienia**wybierz pozycję **historia importowania/eksportowania**. Po pomyślnym zaimportowaniu stan **zakończył** się.

   ![Stan importowania bazy danych](./media/database-import/sql-server-import-database-history.png)

1. Aby sprawdzić, czy baza danych jest aktywna na serwerze, wybierz opcję **bazy danych SQL** i sprawdź, czy nowa baza danych jest w **trybie online**.

## <a name="using-sqlpackage"></a>Korzystanie z sqlpackage

Aby zaimportować bazę danych SQL Server przy użyciu narzędzia wiersza polecenia [sqlpackage](https://docs.microsoft.com/sql/tools/sqlpackage) , zobacz [Importowanie parametrów i właściwości](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). Narzędzia [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i [SQL Server Data Tools for Visual Studio zawierają element](https://msdn.microsoft.com/library/mt204009.aspx) sqlpackage. Możesz również pobrać najnowszy [pakiet sqlpackage](https://www.microsoft.com/download/details.aspx?id=53876) z centrum pobierania Microsoft.

W przypadku skalowania i wydajności zalecamy użycie elementu sqlpackage w większości środowisk produkcyjnych, a nie przy użyciu Azure Portal. Blog zespołu ds. pomocy technicznej SQL Server o migracji przy użyciu `BACPAC` plików można znaleźć w temacie [Migrowanie z SQL Server do Azure SQL Database przy użyciu plików BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Następujące polecenie sqlpackage importuje bazę danych **AdventureWorks2008R2** z magazynu lokalnego do logicznego serwera SQL o nazwie **mynewserver20170403**. Tworzy nową bazę danych o nazwie **myMigratedDatabase** z warstwą usługi **Premium** i celem usługi **P6** . Zmień te wartości zgodnie z potrzebami w danym środowisku.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Aby nawiązać połączenie z Azure SQL Database za zaporą firmową, zapora musi mieć otwarty port 1433. Aby nawiązać połączenie z wystąpieniem zarządzanym SQL, musisz mieć [połączenie typu punkt-lokacja](../managed-instance/point-to-site-p2s-configure.md) lub połączenie z usługą Express Route.

Ten przykład pokazuje, jak zaimportować bazę danych przy użyciu sqlpackage with Active Directory Universal Authentication.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>Korzystanie z programu PowerShell

> [!NOTE]
> [Wystąpienie zarządzane SQL](../managed-instance/sql-managed-instance-paas-overview.md) nie obsługuje obecnie migrowania bazy danych do bazy danych wystąpienia z pliku BACPAC przy użyciu Azure PowerShell. Aby zaimportować do wystąpienia zarządzanego SQL, użyj SQL Server Management Studio lub sqlpackage.

> [!NOTE]
> Komputery przetwarzające żądania importu/eksportu przesłane za pomocą portalu lub programu PowerShell muszą przechowywać plik BACPAC oraz pliki tymczasowe generowane przez strukturę aplikacji warstwy danych (DacFX). Wymagane miejsce na dysku zmienia się znacznie w baz danych z tym samym rozmiarem i może trwać do 3 razy rozmiaru bazy danych. Maszyny, na których jest uruchomione żądanie importu/eksportu, mają tylko miejsce na dysku lokalnym 450GB. W związku z tym niektóre żądania mogą zakończyć się niepowodzeniem z powodu błędu "nie ma wystarczającej ilości miejsca na dysku". W takim przypadku obejście ma na celu uruchomienie sqlpackage.exe na komputerze z wystarczającą ilością miejsca na dysku lokalnym. Podczas importowania/eksportowania baz danych o rozmiarze większym niż 150 GB należy użyć sqlpackage, aby uniknąć tego problemu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell (RM) jest nadal obsługiwany, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. Moduł AzureRM będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020.  Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji o zgodności, zobacz [wprowadzenie do nowego Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az).

Użyj polecenia cmdlet [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) , aby przesłać żądanie importu bazy danych na platformę Azure. W zależności od rozmiaru bazy danych Importowanie może zająć trochę czasu.

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

Aby sprawdzić postęp importowania, można użyć polecenia cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) . Uruchomienie polecenia cmdlet natychmiast po przejściu żądania zwykle zwraca wartość `Status: InProgress` . Importowanie zostało zakończone, gdy zobaczysz `Status: Succeeded` .

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

Aby przesłać żądanie importu bazy danych do platformy Azure, użyj polecenia [AZ-SQL-DB-import](/cli/azure/sql/db#az-sql-db-import) . W zależności od rozmiaru bazy danych Importowanie może zająć trochę czasu.

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
> Aby zapoznać się z innym przykładem skryptu, zobacz [Importowanie bazy danych z pliku BACPAC](scripts/import-from-bacpac-powershell.md).

## <a name="limitations"></a>Ograniczenia

- Importowanie do bazy danych w elastycznej puli nie jest obsługiwane. Dane można importować do pojedynczej bazy danych, a następnie przenieść ją do elastycznej puli.
- Usługa Import Export nie działa, gdy ustawienie Zezwalaj na dostęp do usług platformy Azure jest wyłączone. Można jednak obejść ten problem, ręcznie uruchamiając sqlpackage.exe z maszyny wirtualnej platformy Azure lub wykonując eksport bezpośrednio w kodzie przy użyciu interfejsu API DACFx.
- Import nie obsługuje określania nadmiarowości magazynu kopii zapasowych podczas tworzenia nowej bazy danych i jest tworzona przy użyciu domyślnej nadmiarowości geograficznie nadmiarowego magazynu kopii zapasowej. Aby obejść ten element, należy najpierw utworzyć pustą bazę danych o nadmiarowości magazynu kopii zapasowej za pomocą Azure Portal lub programu PowerShell, a następnie zaimportować BACPAC do tej pustej bazy danych. 

> [!NOTE]
> Azure SQL Database konfigurowalnej nadmiarowości magazynu kopii zapasowych jest obecnie dostępna w publicznej wersji zapoznawczej tylko w regionie "Południowo-Wschodnia".

## <a name="import-using-wizards"></a>Importuj przy użyciu kreatorów

Możesz również użyć tych kreatorów.

- [Kreator importowania aplikacji warstwy danych w SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server Kreatora importu i eksportu](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak nawiązać połączenie z bazą danych i zbadać ją w Azure SQL Database, zobacz [Szybki Start: Azure SQL Database: użyj SQL Server Management Studio, aby nawiązać połączenie z danymi i wysyłać do nich zapytania](connect-query-ssms.md).
- Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
- Aby zapoznać się z omówieniem całego procesu migracji bazy danych SQL Server, łącznie z zaleceniami dotyczącymi wydajności, zobacz [SQL Server migracji bazy danych do Azure SQL Database](migrate-to-database-from-sql-server.md).
- Aby dowiedzieć się, jak bezpiecznie zarządzać i udostępniać klucze magazynu i sygnatury dostępu współdzielonego, zobacz [Przewodnik po zabezpieczeniach usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
