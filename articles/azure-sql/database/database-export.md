---
title: Eksportowanie Azure SQL Database do pliku BACPAC (Azure Portal)
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Wyeksportuj bazę danych do pliku BACPAC przy użyciu Azure Portal.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
author: stevestein
ms.custom: sqldbrb=2
ms.author: sstein
ms.reviewer: ''
ms.date: 07/16/2019
ms.topic: how-to
ms.openlocfilehash: c0e62a7d9b9beb8ecdfaabdd44fdd547dd78d38f
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92328195"
---
# <a name="export-to-a-bacpac-file---azure-sql-database-and-azure-sql-managed-instance"></a>Eksportowanie do Azure SQL Database pliku BACPAC i wystąpienia zarządzanego usługi Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

W przypadku konieczności eksportowania bazy danych na potrzeby archiwizacji lub przechodzenia do innej platformy można wyeksportować schemat bazy danych i dane do pliku [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) . Plik BACPAC jest plikiem ZIP z rozszerzeniem BACPAC zawierającym metadane i dane z bazy danych. Plik BACPAC może być przechowywany w usłudze Azure Blob Storage lub w magazynie lokalnym w lokalizacji lokalnej, a później zaimportowany z powrotem do Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL lub wystąpienia SQL Server.

## <a name="considerations"></a>Zagadnienia do rozważenia

- Aby eksport był spójnie sprzeczny, należy upewnić się, że nie ma żadnych działań zapisu w trakcie eksportowania lub że eksportuje się z [sprzecznej z transakcyjną kopią](database-copy.md) bazy danych.
- Jeśli eksportujesz do magazynu obiektów blob, maksymalny rozmiar pliku BACPAC wynosi 200 GB. Aby zarchiwizować większy plik BACPAC, należy wyeksportować do magazynu lokalnego.
- Eksportowanie pliku BACPAC do usługi Azure Premium Storage przy użyciu metod omówionych w tym artykule nie jest obsługiwane.
- Magazyn za zaporą nie jest obecnie obsługiwany.
- Jeśli operacja eksportu przekroczy 20 godzin, może zostać anulowana. Aby zwiększyć wydajność podczas eksportowania, możesz:

  - Tymczasowe zwiększenie rozmiaru obliczeń.
  - Zastąp wszystkie działania odczytu i zapisu podczas eksportowania.
  - Użyj [indeksu klastrowanego](https://msdn.microsoft.com/library/ms190457.aspx) z wartościami innymi niż null dla wszystkich dużych tabel. Bez indeksów klastrowanych eksport może zakończyć się niepowodzeniem, jeśli trwa dłużej niż 6-12 godzin. Wynika to z faktu, że usługa Export musi ukończyć skanowanie tabeli, aby spróbować wyeksportować całą tabelę. Dobrym sposobem na ustalenie, czy tabele są zoptymalizowane pod kątem eksportu, jest uruchomienie **polecenia DBCC SHOW_STATISTICS** i upewnienie się, że *RANGE_HI_KEY* nie ma wartości null i jej wartość ma dobry rozkład. Aby uzyskać szczegółowe informacje, zobacz [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs nie są przeznaczone do użycia na potrzeby operacji tworzenia kopii zapasowych i przywracania. Platforma Azure automatycznie tworzy kopie zapasowe dla każdej bazy danych użytkownika. Aby uzyskać szczegółowe informacje, zobacz [Omówienie ciągłości](business-continuity-high-availability-disaster-recover-hadr-overview.md) działania i [SQL Database kopii zapasowych](automated-backups-overview.md).

## <a name="the-azure-portal"></a>Witryna Azure Portal

Eksportowanie BACPAC bazy danych z [wystąpienia zarządzanego Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) przy użyciu Azure Portal nie jest obecnie obsługiwane. Zamiast tego użyj SQL Server Management Studio lub sqlpackage.

> [!NOTE]
> Komputery przetwarzające żądania importu/eksportu przesłane za pomocą Azure Portal lub PowerShell muszą przechowywać plik BACPAC oraz pliki tymczasowe generowane przez Data-Tier Application Framework (DacFX). Wymagane miejsce na dysku zmienia się w różnych bazach danych o takim samym rozmiarze i może wymagać wolnego miejsca na dysku do 3 razy większym niż rozmiar bazy danych. Maszyny, na których jest uruchomione żądanie importu/eksportu, mają tylko miejsce na dysku lokalnym 450GB. W rezultacie niektóre żądania mogą zakończyć się niepowodzeniem z powodu błędu `There is not enough space on the disk` . W takim przypadku obejście ma na celu uruchomienie sqlpackage.exe na komputerze z wystarczającą ilością miejsca na dysku lokalnym. Zachęcamy do używania [sqlpackage](#sqlpackage-utility) do importowania/eksportowania baz danych większych niż 150 GB, aby uniknąć tego problemu.

1. Aby wyeksportować bazę danych przy użyciu [Azure Portal](https://portal.azure.com), Otwórz stronę bazy danych i kliknij przycisk **Eksportuj** na pasku narzędzi.

   ![Zrzut ekranu, który podświetla przycisk Eksportuj.](./media/database-export/database-export1.png)

2. Określ nazwę pliku BACPAC, wybierz istniejące konto usługi Azure Storage i kontener do wyeksportowania, a następnie podaj odpowiednie poświadczenia, aby uzyskać dostęp do źródłowej bazy danych. W tym miejscu jest wymagana **Nazwa logowania administratora** programu SQL Server, nawet jeśli jesteś administratorem platformy Azure, ponieważ administrator platformy Azure nie ma uprawnień administratora w Azure SQL Database lub wystąpieniu zarządzanym Azure SQL.

    ![Eksport bazy danych](./media/database-export/database-export2.png)

3. Kliknij przycisk **OK**.

4. Aby monitorować postęp operacji eksportowania, Otwórz stronę dla serwera zawierającego wyeksportowaną bazę danych. W obszarze do **Ustawienia** , a następnie kliknij pozycję **Importuj/Eksportuj historię**.

   ![Eksportuj historię](./media/database-export/export-history.png)

## <a name="sqlpackage-utility"></a>Narzędzie sqlpackage

Aby wyeksportować bazę danych w SQL Database przy użyciu narzędzia wiersza polecenia [sqlpackage](https://docs.microsoft.com/sql/tools/sqlpackage) , zobacz [Eksportowanie parametrów i właściwości](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). Narzędzie sqlpackage jest dostarczane z najnowszymi wersjami [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) i [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Możesz też pobrać najnowszą wersję elementu [sqlpackage](https://www.microsoft.com/download/details.aspx?id=53876) bezpośrednio z centrum pobierania Microsoft.

Zalecamy używanie narzędzia sqlpackage do skalowania i wydajności w większości środowisk produkcyjnych. Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).

W tym przykładzie przedstawiono sposób eksportowania bazy danych przy użyciu SqlPackage.exe z Active Directory uniwersalnym uwierzytelnianiem:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Najnowsze wersje SQL Server Management Studio udostępniają kreatora do eksportowania bazy danych w Azure SQL Database lub bazy danych wystąpienia zarządzanego SQL do pliku BACPAC. Zapoznaj się z tematem [Eksportowanie aplikacji warstwy danych](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="powershell"></a>PowerShell

> [!NOTE]
> [Wystąpienie zarządzane Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) nie obsługuje obecnie eksportowania bazy danych do pliku BACPAC przy użyciu Azure PowerShell. Aby wyeksportować wystąpienie zarządzane do pliku BACPAC, użyj SQL Server Management Studio lub sqlpackage.

Użyj polecenia cmdlet [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) , aby przesłać żądanie eksportu bazy danych do usługi Azure SQL Database. W zależności od rozmiaru bazy danych operacja eksportowania może zająć trochę czasu.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Aby sprawdzić stan żądania eksportu, należy użyć polecenia cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) . Uruchomienie to natychmiast po żądaniu zwykle zwraca **stan: w toku**. Gdy widzisz **stan: powodzenie** eksportu zostało zakończone.

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat długoterminowego przechowywania kopii zapasowych pojedynczej bazy danych i baz danych w puli jako alternatywy dla eksportowania bazy danych na potrzeby archiwum, zobacz [długoterminowe przechowywanie kopii zapasowych](long-term-retention-overview.md). Zadań programu SQL Agent można użyć do zaplanowania kopii [zapasowych bazy danych tylko do kopiowania](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) jako alternatywy dla długoterminowego przechowywania kopii zapasowych.
- Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
- Aby dowiedzieć się więcej o importowaniu BACPAC do bazy danych SQL Server, zobacz [Importowanie BACPAC do bazy danych SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
- Aby dowiedzieć się więcej na temat eksportowania BACPAC z bazy danych SQL Server, zobacz [Eksportowanie aplikacji warstwy danych](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Aby dowiedzieć się więcej o używaniu usługi migracji danych do migracji bazy danych, zobacz [Migrowanie z SQL Server do Azure SQL Database offline przy użyciu platformy DMS](../../dms/tutorial-sql-server-to-azure-sql.md).
- Jeśli eksportujesz z SQL Server jako preludium do migracji do Azure SQL Database, zobacz [Migrowanie bazy danych SQL Server do Azure SQL Database](migrate-to-database-from-sql-server.md).
- Aby dowiedzieć się, jak bezpiecznie zarządzać i udostępniać klucze magazynu i sygnatury dostępu współdzielonego, zobacz [Przewodnik po zabezpieczeniach usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
