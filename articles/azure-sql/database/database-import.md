---
title: Importowanie pliku BACPAC w celu utworzenia bazy danych w Azure SQL Database
description: Utwórz nową bazę danych w Azure SQL Database lub Azure SQL Managed Instance pliku BACPAC.
services: sql-database
ms.service: sql-db-mi
ms.subservice: migrate
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/29/2020
ms.openlocfilehash: eddcab2c0a34ef437e4f2f1e2203fee9065133a4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781887"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Szybki start: importowanie pliku BACPAC do bazy danych w Azure SQL Database lub Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Bazę danych można zaimportować SQL Server do Azure SQL Database lub SQL Managed Instance przy użyciu [pliku BACPAC.](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) Dane można zaimportować z pliku BACPAC przechowywanego w usłudze Azure Blob Storage (tylko w przypadku magazynu w warstwie Standardowa) lub z magazynu lokalnego. Aby maksymalnie zwiększyć szybkość importowania przez zapewnienie większej ilości szybszych zasobów, przeprowadź skalowanie bazy danych do wyższej warstwy usługi i rozmiaru obliczeniowego na czas trwania procesu importowania. Po pomyślnym zakończeniu importowania możesz przeprowadzić skalowanie w dół.

> [!NOTE]
> Poziom zgodności zaimportowanych baz danych zależy od poziomu zgodności źródłowej bazy danych.

> [!IMPORTANT]
> Po zaimportowaniu bazy danych można ją obsługiwać na bieżącym poziomie zgodności (poziom 100 dla bazy danych AdventureWorks2008R2) lub na wyższym poziomie. Aby uzyskać więcej informacji o implikacjach i opcjach związanych z używaniem bazy danych na określonym poziomie zgodności, zobacz [ALTER DATABASE Compatibility Level](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Instrukcja ALTER DATABASE — poziom zgodności). Zapoznaj się też z tematem [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), aby uzyskać informacje o dodatkowych ustawieniach na poziomie bazy danych związanych z poziomem zgodności.

## <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal

Obejrzyj to wideo, aby zobaczyć, jak zaimportować dane z pliku BACPAC w Azure Portal lub kontynuować czytanie poniżej:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

Baza [Azure Portal](https://portal.azure.com) *tylko tworzenie* pojedynczej bazy danych w  usłudze Azure SQL Database i tylko z pliku BACPAC przechowywanego w usłudze Azure Blob Storage.

Aby przeprowadzić migrację [](../managed-instance/sql-managed-instance-paas-overview.md) bazy danych do Azure SQL Managed Instance z pliku BACPAC, należy użyć narzędzia SQL Server Management Studio lub SQLPackage, jeśli Azure Portal lub Azure PowerShell nie jest obecnie obsługiwane.

> [!NOTE]
> Maszyny przetwarzają żądania importu/eksportu przesłane za pośrednictwem programu Azure Portal lub PowerShell muszą przechowywać plik BACPAC, a także pliki tymczasowe generowane przez platformę Data-Tier Application Framework (DacFX). Wymagane miejsce na dysku różni się znacznie w zależności od bazy danych o takim samym rozmiarze i może wymagać miejsca na dysku nawet trzykrotnie znacznego rozmiaru bazy danych. Maszyny z uruchomionym żądaniem importu/eksportu mają tylko 450 GB miejsca na dysku lokalnym. W związku z tym niektóre żądania mogą się nie powieść z powodu błędu `There is not enough space on the disk` . W takim przypadku obejściem jest uruchomienie sqlpackage.exe na maszynie z wystarczającą ilość miejsca na dysku lokalnym. Aby uniknąć tego problemu, zachęcamy do importowania/eksportowania baz danych większych niż 150 GB przy użyciu narzędzia SqlPackage.

1. Aby zaimportować dane z pliku BACPAC do nowej pojedynczej bazy danych przy użyciu Azure Portal, otwórz odpowiednią stronę serwera, a następnie na pasku narzędzi wybierz pozycję **Importuj bazę danych**.  

   ![Importowanie bazy danych1](./media/database-import/sql-server-import-database.png)

1. Wybierz konto magazynu i kontener dla pliku BACPAC, a następnie wybierz plik BACPAC, z którego chcesz zaimportować.

1. Określ nowy rozmiar bazy danych (zazwyczaj taki sam jak początkowy) i podaj miejsce docelowe, SQL Server poświadczenia. Aby uzyskać listę możliwych wartości dla nowej bazy danych w programie Azure SQL Database, [zobacz Tworzenie bazy danych](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true).

   ![Importowanie bazy danych2](./media/database-import/sql-server-import-database-settings.png)

1. Kliknij przycisk **OK**.

1. Aby monitorować postęp importowania, otwórz stronę serwera bazy danych i w obszarze Ustawienia wybierz pozycję **Historia importu/eksportu.** Po pomyślnym zakończeniu importowanie ma **stan Ukończono.**

   ![Stan importu bazy danych](./media/database-import/sql-server-import-database-history.png)

1. Aby sprawdzić, czy baza danych znajduje się na serwerze, wybierz pozycję Bazy danych **SQL** i sprawdź, czy nowa baza danych jest w **trybie online.**

## <a name="using-sqlpackage"></a>Korzystanie z narzędzia SqlPackage

Aby zaimportować bazę SQL Server za pomocą narzędzia wiersza polecenia [SqlPackage,](/sql/tools/sqlpackage) zobacz [parametry i właściwości importu](/sql/tools/sqlpackage#import-parameters-and-properties). [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) i [SQL Server Data Tools for Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt) obejmują pakiet SqlPackage. Najnowszą wersję pakietu [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) można również pobrać z Centrum pobierania Microsoft. 

W celu skalowania i wydajności zalecamy używanie narzędzia SqlPackage w większości środowisk produkcyjnych, a nie Azure Portal. Aby uzyskać blog SQL Server zespołu doradczego klientów na temat migrowania przy użyciu plików, zobacz migrowanie z usługi SQL Server do Azure SQL Database przy użyciu plików `BACPAC` [BACPAC.](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files)

Model aprowizowania oparty na jednostkach DTU obsługuje wybrane wartości maksymalnego rozmiaru bazy danych dla każdej warstwy. Podczas importowania bazy danych [użyj jednej z tych obsługiwanych wartości](/sql/t-sql/statements/create-database-transact-sql). 

Następujące polecenie SqlPackage importuje bazę danych **AdventureWorks2008R2** z magazynu lokalnego do logicznego serwera SQL o nazwie **mynewserver20170403.** Tworzy nową bazę danych **o nazwie myMigratedDatabase** z warstwą **usługi Premium** i celem **usługi P6.** Zmień te wartości zgodnie z potrzebami środowiska.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Aby nawiązać połączenie Azure SQL Database za firmową zaporą, zapora musi mieć otwarty port 1433. Aby nawiązać SQL Managed Instance, musisz mieć połączenie [punkt-lokacja](../managed-instance/point-to-site-p2s-configure.md) lub połączenie express route.

W tym przykładzie pokazano, jak zaimportować bazę danych przy użyciu narzędzia SqlPackage z uniwersalnym uwierzytelnianiem usługi Active Directory.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>Korzystanie z programu PowerShell

> [!NOTE]
> [Zespół SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) obecnie nie obsługuje migracji bazy danych do bazy danych wystąpienia z pliku BACPAC przy użyciu Azure PowerShell. Aby zaimportować do SQL Managed Instance, użyj SQL Server Management Studio SQLPackage.

> [!NOTE]
> Maszyny przetwarzają żądania importu/eksportu przesłane za pośrednictwem portalu lub programu PowerShell muszą przechowywać plik bacpac, a także pliki tymczasowe generowane przez program Data-Tier Application Framework (DacFX). Wymagane miejsce na dysku różni się znacznie w zależności od bazy danych o takim samym rozmiarze i może potrwać do 3 razy więcej niż rozmiar bazy danych. Maszyny z uruchomionym żądaniem importu/eksportu mają tylko 450 GB miejsca na dysku lokalnym. W związku z tym niektóre żądania mogą się nie powieść z powodu błędu "Brak wystarczającej ilości miejsca na dysku". W takim przypadku obejściem jest uruchomienie sqlpackage.exe na maszynie z wystarczającą ilość miejsca na dysku lokalnym. W przypadku importowania/eksportowania baz danych większych niż 150 GB użyj narzędzia SqlPackage, aby uniknąć tego problemu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> Moduł PowerShell Azure Resource Manager (RM) jest nadal obsługiwany, ale cały przyszły rozwój jest dla modułu Az.Sql. Moduł AzureRM będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r.  Argumenty poleceń w module Az i modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji na temat ich zgodności, [zobacz Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az)(Wprowadzenie do nowego modułu Az Azure PowerShell Az).

Użyj polecenia cmdlet [New-AzSqlDatabaseImport,](/powershell/module/az.sql/new-azsqldatabaseimport) aby przesłać żądanie importowania bazy danych na platformę Azure. W zależności od rozmiaru bazy danych importowanie może zająć trochę czasu. Model aprowizowania oparty na jednostkach DTU obsługuje wybrane wartości maksymalnego rozmiaru bazy danych dla każdej warstwy. Podczas importowania bazy danych [użyj jednej z tych obsługiwanych wartości](/sql/t-sql/statements/create-database-transact-sql). 

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

Aby sprawdzić postęp importowania, możesz użyć polecenia cmdlet [Get-AzSqlDatabaseImportExportStatus.](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) Uruchomienie polecenia cmdlet natychmiast po żądaniu zwykle zwraca wartość `Status: InProgress` . Importowanie jest ukończone, gdy zostanie wyświetlony obraz `Status: Succeeded` .

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

Użyj polecenia [az-sql-db-import,](/cli/azure/sql/db#az_sql_db_import) aby przesłać żądanie importowania bazy danych na platformę Azure. W zależności od rozmiaru bazy danych importowanie może trochę potrwać. Model aprowizowania oparty na jednostkach DTU obsługuje wybrane wartości maksymalnego rozmiaru bazy danych dla każdej warstwy. Podczas importowania bazy danych [użyj jednej z tych obsługiwanych wartości](/sql/t-sql/statements/create-database-transact-sql). 

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
> Aby uzyskać inny przykładowy skrypt, zobacz [Importowanie bazy danych z pliku BACPAC.](scripts/import-from-bacpac-powershell.md)

## <a name="cancel-the-import-request"></a>Anulowanie żądania importu

Użyj polecenia [Operacje bazy danych — Anulowanie interfejsu API](/rest/api/sql/databaseoperations/cancel) lub polecenia [PowerShell Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/Stop-AzSqlDatabaseActivity). Tutaj znajduje się przykład polecenia programu PowerShell.

```cmd
Stop-AzSqlDatabaseActivity -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -OperationId $Operation.OperationId
```


## <a name="limitations"></a>Ograniczenia

- Importowanie do bazy danych w elastycznej puli nie jest obsługiwane. Dane można importować do pojedynczej bazy danych, a następnie przenieść ją do elastycznej puli.
- Usługa importu eksportu nie działa, gdy ustawienie Zezwalaj na dostęp do usług platformy Azure jest wyłączone. Problem można jednak rozwiązać, ręcznie uruchamiając narzędzie sqlpackage.exe z maszyny wirtualnej platformy Azure lub wykonując eksportowanie bezpośrednio w kodzie przy użyciu interfejsu API DACFx.
- Import nie obsługuje określania nadmiarowości magazynu kopii zapasowych podczas tworzenia nowej bazy danych i tworzy z domyślną nadmiarowością magazynu kopii zapasowych geograficznie nadmiarowych. Aby obejść ten problem, najpierw utwórz pustą bazę danych z żądaną nadmiarowością magazynu kopii zapasowych przy użyciu programu Azure Portal lub PowerShell, a następnie zaimportuj bazę danych BACPAC do tej pustej bazy danych. 

> [!NOTE]
> Azure SQL Database nadmiarowości magazynu kopii zapasowych z możliwością konfiguracji jest obecnie dostępna w publicznej wersji zapoznawczej tylko w regionie świadczenia usługi Azure w regionie Azja Południowo-Wschodnia.

## <a name="import-using-wizards"></a>Importowanie przy użyciu kreatorów

Można również użyć tych kreatorów.

- [Kreator importu aplikacji warstwy danych w programie SQL Server Management Studio](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server importu i eksportu.](/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard)

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak nawiązać połączenie z bazą danych i odpytać bazę danych w programie Azure SQL Database, zobacz Szybki [start: Azure SQL Database:](connect-query-ssms.md)używanie programu SQL Server Management Studio do nawiązywania połączeń z danymi i wykonywania na nich zapytań.
- Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
- Aby uzyskać omówienie całego procesu migracji bazy danych SQL Server, w tym zalecenia dotyczące wydajności, zobacz SQL Server database migration to Azure SQL Database (Migracja bazy danych do [usługi Azure SQL Database).](migrate-to-database-from-sql-server.md)
- Aby dowiedzieć się, jak bezpiecznie udostępniać klucze magazynu i sygnatury dostępu współdzielone, zobacz Przewodnik po zabezpieczeniach [usługi Azure Storage.](../../storage/blobs/security-recommendations.md)