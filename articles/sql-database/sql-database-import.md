---
title: Importowanie pliku BACPAC w celu utworzenia bazy danych
description: Utwórz nową bazę danych SQL, importując plik BACPAC.
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
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086491"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Szybki start: importowanie pliku BACPAC do bazy danych w bazie danych SQL usługi Azure

Bazę danych programu SQL Server można zaimportować do bazy danych w bazie danych SQL usługi Azure przy użyciu pliku [BACPAC.](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) Dane można zaimportować z `BACPAC` pliku przechowywanego w magazynie obiektów Blob platformy Azure (tylko do magazynu standardowego) lub z magazynu lokalnego w lokalizacji lokalnej. Aby zmaksymalizować szybkość importu, zapewniając więcej i szybsze zasoby, skaluj bazę danych do wyższej warstwy usług i rozmiaru obliczeń podczas procesu importowania. Następnie można skalować w dół po pomyślnym zakończeniu importowania.

> [!NOTE]
> Poziom zgodności importowanej bazy danych jest oparty na poziomie zgodności źródłowej bazy danych.

> [!IMPORTANT]
> Po zaimportowaniu bazy danych można obsługiwać bazę danych na jej bieżącym poziomie zgodności (poziom 100 dla bazy danych AdventureWorks2008R2) lub na wyższym poziomie. Aby uzyskać więcej informacji o implikacjach i opcjach związanych z używaniem bazy danych na określonym poziomie zgodności, zobacz [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Instrukcja ALTER DATABASE — poziom zgodności). Zapoznaj się też z tematem [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), aby uzyskać informacje o dodatkowych ustawieniach na poziomie bazy danych związanych z poziomem zgodności.

## <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal

Obejrzyj ten klip wideo, aby zobaczyć, jak importować z pliku BACPAC w witrynie Azure portal lub kontynuować czytanie poniżej:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

Portal [Azure obsługuje](https://portal.azure.com) *tylko* tworzenie pojedynczej bazy danych w usłudze Azure SQL Database i *tylko* z pliku BACPAC przechowywanego w magazynie obiektów Blob platformy Azure.

Migracja bazy danych do [wystąpienia zarządzanego](sql-database-managed-instance.md) z pliku BACPAC przy użyciu programu Azure PowerShell nie jest obecnie obsługiwana. Zamiast tego należy użyć programu SQL Server Management Studio lub SQLPackage.

> [!NOTE]
> Maszyny przetwarzające żądania importu/eksportu przesłane za pośrednictwem portalu Azure lub programu PowerShell muszą przechowywać plik BACPAC, a także pliki tymczasowe generowane przez platformę aplikacji warstwy danych (DacFX). Wymagane miejsce na dysku różni się znacznie w poszczególnych bazach danych o tym samym rozmiarze i może wymagać miejsca na dysku do 3 razy większej niż rozmiar bazy danych. Komputery z żądaniem importu/eksportu mają tylko 450 GB miejsca na dysku lokalnym. W rezultacie niektóre żądania mogą zakończyć się niepowodzeniem z błędem `There is not enough space on the disk`. W takim przypadku obejście jest uruchomienie sqlpackage.exe na komputerze z wystarczającą ilością miejsca na dysku lokalnym. Zachęcamy za pomocą SqlPackage do importowania/eksportowania baz danych większych niż 150 GB, aby uniknąć tego problemu.

1. Aby zaimportować z pliku BACPAC do nowej pojedynczej bazy danych za pomocą witryny Azure Portal, otwórz odpowiednią stronę serwera bazy danych, a następnie na pasku narzędzi wybierz **pozycję Importuj bazę danych**.  

   ![Import bazy danych1](./media/sql-database-import/sql-server-import-database.png)

1. Wybierz konto magazynu i kontener dla pliku BACPAC, a następnie wybierz plik BACPAC, z którego ma być importowany.

1. Określ nowy rozmiar bazy danych (zwykle taki sam jak pochodzenie) i podaj docelowe poświadczenia programu SQL Server. Aby uzyskać listę możliwych wartości dla nowej bazy danych SQL platformy Azure, zobacz [Tworzenie bazy danych](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Import bazy danych2](./media/sql-database-import/sql-server-import-database-settings.png)

1. Kliknij przycisk **OK**.

1. Aby monitorować postęp importu, otwórz stronę serwera bazy danych i w obszarze **Ustawienia**wybierz pozycję **Importuj/Eksportuj historię**. Po pomyślnym zaimportowaniu ma stan **Zakończone.**

   ![Stan importu bazy danych](./media/sql-database-import/sql-server-import-database-history.png)

1. Aby sprawdzić, czy baza danych jest dostępna na serwerze bazy danych, wybierz **bazy danych SQL** i sprawdź, czy nowa baza danych jest w **trybie online**.

## <a name="using-sqlpackage"></a>Korzystanie z pakietu SqlPackage

Aby zaimportować bazę danych programu SQL Server przy użyciu narzędzia wiersza polecenia [SqlPackage,](https://docs.microsoft.com/sql/tools/sqlpackage) zobacz [parametry i właściwości importu](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) obejmują SqlPackage. Możesz również pobrać najnowszy [pakiet SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) z centrum pobierania firmy Microsoft.

Aby skalować i wydajność, zaleca się przy użyciu SqlPackage w większości środowisk produkcyjnych, a nie przy użyciu witryny Azure portal. Aby zapoznać się z blogiem zespołu `BACPAC` doradczego klienta programu SQL Server na temat migracji przy użyciu plików, zobacz [migracja z programu SQL Server do usługi Azure SQL Database przy użyciu plików BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Następujące polecenie SqlPackage importuje bazę danych **AdventureWorks2008R2** z magazynu lokalnego do serwera bazy danych SQL Database platformy Azure o nazwie **mynewserver20170403**. Tworzy nową bazę danych o nazwie **myMigratedDatabase** z warstwą usługi **Premium** i celem usługi **P6.** Zmień te wartości odpowiednio dla danego środowiska.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Aby połączyć się z serwerem bazy danych SQL, który zarządza pojedynczą bazą danych zza zapory firmowej, zapora musi być otwarta. Aby połączyć się z wystąpieniem zarządzanym, musisz mieć [połączenie typu "punkt-lokacja"](sql-database-managed-instance-configure-p2s.md) lub połączenie trasy ekspresowej.

W tym przykładzie pokazano, jak zaimportować bazę danych przy użyciu programu SqlPackage z powszechnym uwierzytelnianiem usługi Active Directory.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>Korzystanie z programu PowerShell

> [!NOTE]
> [Wystąpienie zarządzane](sql-database-managed-instance.md) nie obsługuje obecnie migracji bazy danych do bazy danych wystąpienia z pliku BACPAC przy użyciu programu Azure PowerShell. Aby zaimportować do wystąpienia zarządzanego, użyj programu SQL Server Management Studio lub SQLPackage.

> [!NOTE]
> Maszyny przetwarzające żądania importu/eksportu przesłane za pośrednictwem portalu lub programu Powershell muszą przechowywać plik bacpac, a także pliki tymczasowe generowane przez platformę aplikacji warstwy danych (DacFX). Wymagane miejsce na dysku różni się znacznie w zależności od wielkości i może trwać do 3 razy więcej niż rozmiar bazy danych. Komputery z żądaniem importu/eksportu mają tylko 450 GB miejsca na dysku lokalnym. W rezultacie niektóre żądania mogą zakończyć się niepowodzeniem z błędem "Za mało miejsca na dysku". W takim przypadku obejście jest uruchomienie sqlpackage.exe na komputerze z wystarczającą ilością miejsca na dysku lokalnym. Podczas importowania/eksportowania baz danych większych niż 150 GB należy użyć programu SqlPackage, aby uniknąć tego problemu.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> Moduł programu PowerShell Azure Resource Manager (RM) jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Moduł AzureRM będzie nadal otrzymywać poprawki błędów co najmniej do grudnia 2020.  Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji na temat ich zgodności, zobacz [Przedstawianie nowego modułu Az programu Azure PowerShell.](/powershell/azure/new-azureps-module-az)

Polecenie cmdlet [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) służy do przesyłania żądania importu bazy danych do usługi Azure SQL Database. W zależności od rozmiaru bazy danych importowanie może zająć trochę czasu.

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

Polecenia cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) służy do sprawdzania postępu importu. Uruchamianie polecenia cmdlet natychmiast `Status: InProgress`po żądaniu zwykle zwraca . Importowanie zostanie zakończone `Status: Succeeded`po wyświetleniu pliku .

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

Polecenie [az-sql-db-import](/cli/azure/sql/db#az-sql-db-import) służy do przesyłania żądania importu bazy danych do usługi Azure SQL Database. W zależności od rozmiaru bazy danych importowanie może zająć trochę czasu.

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
> W innym przykładzie skryptu zobacz [Importowanie bazy danych z pliku BACPAC](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Ograniczenia

- Importowanie do bazy danych w puli elastycznej nie jest obsługiwane. Można zaimportować dane do pojedynczej bazy danych, a następnie przenieść bazę danych do puli elastycznej.
- Importuj usługę eksportu nie działa, gdy zezwalaj na dostęp do usług platformy Azure jest ustawiona na OFF. Jednak można obejść ten problem, ręcznie uruchamiając sqlpackage.exe z maszyny wirtualnej platformy Azure lub wykonywania eksportu bezpośrednio w kodzie przy użyciu interfejsu API DACFx.

## <a name="import-using-wizards"></a>Importowanie przy użyciu kreatorów

Można również użyć tych kreatorów.

- [Kreator aplikacji warstwy danych w programie SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Kreator importu i eksportu programu SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak połączyć się z zaimportowanymi bazami danych SQL i wysyłać [kwerendy, zobacz Szybki start: Baza danych SQL: Łączenie i wykonywanie zapytań o dane](sql-database-connect-query-ssms.md)programu SQL Server Management Studio za pomocą programu SQL Server Management Studio.
- Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
- Aby zapoznać się z omówienia całego procesu migracji bazy danych programu SQL Server, w tym zaleceniami dotyczącymi wydajności, zobacz [Migracja bazy danych programu SQL Server do bazy danych SQL Sql Database](sql-database-single-database-migrate.md).
- Aby dowiedzieć się, jak bezpiecznie zarządzać kluczami magazynu i sygnaturami dostępu współdzielonego oraz udostępniać je, zobacz [Przewodnik po zabezpieczeniach usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
