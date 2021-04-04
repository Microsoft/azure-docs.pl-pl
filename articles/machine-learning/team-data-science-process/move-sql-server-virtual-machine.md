---
title: Przenoszenie danych do maszyny wirtualnej SQL Server — proces nauki o danych zespołu
description: Przenoszenie danych z plików prostych lub SQL Server lokalnych do SQL Server na maszynie wirtualnej platformy Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c80a90b07e25942e751d52cafa47f6e3e94852ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93320338"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Przenoszenie danych do programu SQL Server na maszynie wirtualnej platformy Azure

W tym artykule opisano opcje przeniesienia danych z plików prostych (format CSV lub TSV) lub z SQL Server lokalnego do SQL Server na maszynie wirtualnej platformy Azure. Te zadania dotyczące przeniesienia danych do chmury są częścią procesu nauki o danych zespołowych.

W przypadku tematu, który zawiera opcje przenoszenia danych do Azure SQL Database Machine Learning, zobacz [przenoszenie danych do Azure SQL Database Azure Machine Learning](move-sql-azure.md).

Poniższa tabela zawiera podsumowanie opcji przeniesienia danych do SQL Server na maszynie wirtualnej platformy Azure.

| <b>ZEWNĘTRZ</b> | <b>MIEJSCE docelowe: SQL Server na maszynie wirtualnej platformy Azure</b> |
| --- | --- |
| <b>Plik prosty</b> |1. <a href="#insert-tables-bcp">Narzędzie do kopiowania zbiorczego wiersza polecenia (bcp) </a><br> 2. <a href="#insert-tables-bulkquery">zbiorczo Wstaw zapytanie SQL </a><br> 3. <a href="#sql-builtin-utilities">graficzne wbudowane narzędzia w SQL Server</a> |
| <b>SQL Server lokalne</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Wdróż bazę danych SQL Server w kreatorze Microsoft Azure VM</a><br> 2. <a href="#export-flat-file">wyeksportuj do pliku prostego </a><br> 3. <a href="#sql-migration">Kreator migracji SQL Database </a> <br> 4. <a href="#sql-backup">Tworzenie kopii zapasowej i przywracanie bazy danych </a><br> |

W tym dokumencie przyjęto założenie, że polecenia SQL są wykonywane z programu SQL Server Management Studio lub Eksplorator bazy danych programu Visual Studio.

> [!TIP]
> Alternatywnie możesz użyć [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) , aby utworzyć i zaplanować potok, który będzie przenosić dane do SQL Server maszyny wirtualnej na platformie Azure. Aby uzyskać więcej informacji, zobacz [Kopiowanie danych za pomocą Azure Data Factory (działanie kopiowania)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Wymagania wstępne
W tym samouczku założono, że masz:

* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Storage**. Do przechowywania danych w tym samouczku będzie używane konto usługi Azure Storage. Jeśli nie masz konta usługi Azure Storage, zobacz artykuł [Tworzenie konta magazynu](../../storage/common/storage-account-create.md) . Po utworzeniu konta magazynu należy uzyskać klucz konta używany do uzyskiwania dostępu do magazynu. Zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).
* Zainicjowano **SQL Server na maszynie wirtualnej platformy Azure**. Aby uzyskać instrukcje, zobacz [Konfigurowanie maszyny wirtualnej platformy Azure SQL Server jako serwera notesu IPython na potrzeby zaawansowanej analizy](../data-science-virtual-machine/overview.md).
* Zainstalowano i skonfigurowano **Azure PowerShell** lokalnie. Aby uzyskać instrukcje, zobacz [How to Install and configure Azure PowerShell](/powershell/azure/).

## <a name="moving-data-from-a-flat-file-source-to-sql-server-on-an-azure-vm"></a><a name="filesource_to_sqlonazurevm"></a> Przeniesienie danych z prostego źródła pliku do SQL Server na maszynie wirtualnej platformy Azure
Jeśli dane znajdują się w pliku prostym (ułożone w formacie wiersza/kolumny), można je przenieść do SQL Server maszyny wirtualnej na platformie Azure za pomocą następujących metod:

1. [Narzędzie do kopiowania zbiorczego wiersza polecenia (BCP)](#insert-tables-bcp)
2. [Wstaw zbiorczo zapytanie SQL](#insert-tables-bulkquery)
3. [Graficzne wbudowane narzędzia w SQL Server (Import/Export, SSIS)](#sql-builtin-utilities)

### <a name="command-line-bulk-copy-utility-bcp"></a><a name="insert-tables-bcp"></a>Narzędzie do kopiowania zbiorczego wiersza polecenia (BCP)
BCP to narzędzie wiersza polecenia, które jest instalowane z SQL Server i jest jednym z najszybszych sposobów przenoszenia danych. Działa on w ramach wszystkich trzech SQL Server wariantów (lokalnych SQL Server, SQL Azure i SQL Server maszyny wirtualnej na platformie Azure).

> [!NOTE]
> **Gdzie mają być moje dane dla narzędzia BCP?**  
> Chociaż nie jest to wymagane, posiadanie plików zawierających dane źródłowe znajdujących się na tym samym komputerze co docelowy SQL Server umożliwia szybsze transfery (prędkość sieci a szybkość operacji we/wy dysku lokalnego). Pliki proste zawierające dane można przenieść na maszynę, na której zainstalowano SQL Server przy użyciu różnych narzędzi do kopiowania plików, takich jak [AzCopy](../../storage/common/storage-use-azcopy-v10.md), [Eksplorator usługi Azure Storage](https://storageexplorer.com/) lub kopiowania/wklejania systemu windows za pośrednictwem Remote Desktop Protocol (RDP).
>
>

1. Upewnij się, że baza danych i tabele są tworzone w docelowej bazie danych SQL Server. Oto przykład, jak to zrobić za pomocą `Create Database` `Create Table` poleceń i:

    ```sql
    CREATE DATABASE <database_name>
    
    CREATE TABLE <tablename>
    (
        <columnname1> <datatype> <constraint>,
        <columnname2> <datatype> <constraint>,
        <columnname3> <datatype> <constraint>
    )
    ```

1. Wygeneruj plik formatu, który opisuje schemat tabeli, wydając następujące polecenie z wiersza polecenia na komputerze, na którym zainstalowano Narzędzie bcp.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Wstaw dane do bazy danych przy użyciu polecenia BCP, które powinny być wykonywane z poziomu wiersza polecenia, gdy SQL Server jest zainstalowana na tym samym komputerze:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **Optymalizacja wstawek narzędzia bcp** Zapoznaj się z poniższym artykułem [wskazówki dotyczące optymalizacji importu zbiorczego "](/previous-versions/sql/sql-server-2008-r2/ms177445(v=sql.105)) aby zoptymalizować takie wstawki.
>
>

### <a name="parallelizing-inserts-for-faster-data-movement"></a><a name="insert-tables-bulkquery-parallel"></a>Przekształcają wstawia do szybszego przenoszenia danych
Jeśli przenoszone dane są duże, można przyspieszyć wykonywanie wielu poleceń BCP równolegle w skrypcie programu PowerShell.

> [!NOTE]
> Pozyskiwanie **danych Big Data** Aby zoptymalizować ładowanie danych pod kątem dużych i bardzo dużych zestawów danych, Podziel swoje logiczne i fizyczne tabele bazy z wieloma grupami plików i tabelami partycji. Aby uzyskać więcej informacji na temat tworzenia i ładowania danych do tabel partycji, zobacz sekcję [ładowanie równoległe tabel partycji SQL](parallel-load-sql-partitioned-tables.md).
>
>

Poniższy przykładowy skrypt programu PowerShell demonstruje równoległe wstawienia przy użyciu narzędzia bcp:

```powershell
$NO_OF_PARALLEL_JOBS=2

Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
# Define what each job does
$ScriptBlock = {
    param($partitionnumber)

    #Explicitly using SQL username password
    bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

    #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
    #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
}


# Background processing of all partitions
for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
{
    Write-Debug "Submit loading partition # $i"
    Start-Job $ScriptBlock -Arg $i      
}


# Wait for it all to complete
While (Get-Job -State "Running")
{
    Start-Sleep 10
    Get-Job
}

# Getting the information back from the jobs
Get-Job | Receive-Job
Set-ExecutionPolicy Restricted #reset the execution policy
```

### <a name="bulk-insert-sql-query"></a><a name="insert-tables-bulkquery"></a>Wstaw zbiorczo zapytanie SQL
[Zbiorcze zapytanie SQL](/sql/t-sql/statements/bulk-insert-transact-sql) można użyć do zaimportowania danych do bazy danych z plików opartych na wierszach/kolumnach (obsługiwane typy są omówione w temacie[przygotowanie danych do eksportu zbiorczego lub importu (SQL Server)](/sql/relational-databases/import-export/prepare-data-for-bulk-export-or-import-sql-server)).

Poniżej przedstawiono kilka przykładowych poleceń wstawiania zbiorczego, które są następujące:  

1. Analizuj dane i Ustawiaj opcje niestandardowe przed zaimportowaniem, aby upewnić się, że baza danych SQL Server ma ten sam format dla dowolnych pól specjalnych, takich jak daty. Oto przykład sposobu ustawiania formatu daty jako rok-miesiąc-dzień (Jeśli dane zawierają datę w formacie roku miesiąc-dzień):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. Importuj dane przy użyciu instrukcji importu zbiorczego:

    ```sql
    BULK INSERT <tablename>
    FROM
    '<datafilename>'
    WITH
    (
        FirstRow = 2,
        FIELDTERMINATOR = ',', --this should be column separator in your data
        ROWTERMINATOR = '\n'   --this should be the row separator in your data
    )
    ```

### <a name="built-in-utilities-in-sql-server"></a><a name="sql-builtin-utilities"></a>Wbudowane narzędzia w SQL Server
Do zaimportowania danych do maszyny wirtualnej SQL Server na platformie Azure z poziomu prostego pliku można użyć SQL Server Integration Services (SSIS).
SSIS jest dostępny w dwóch środowiskach programu Studio. Aby uzyskać szczegółowe informacje, zobacz [środowiska usług Integration Services (SSIS) i Studio](/sql/integration-services/integration-services-ssis-development-and-management-tools):

* Aby uzyskać szczegółowe informacje na temat SQL Server narzędzi danych, zobacz [Microsoft SQL Server narzędzia danych](/sql/ssdt/download-sql-server-data-tools-ssdt)  
* Aby uzyskać szczegółowe informacje na temat Kreatora importu/eksportu, zobacz [SQL Server Kreatora importu i eksportu](/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard)

## <a name="moving-data-from-on-premises-sql-server-to-sql-server-on-an-azure-vm"></a><a name="sqlonprem_to_sqlonazurevm"></a>Przeniesienie danych z SQL Server lokalnych do SQL Server na maszynie wirtualnej platformy Azure
Można również użyć następujących strategii migracji:

1. [Wdrażanie bazy danych SQL Server w Kreatorze Microsoft Azure VM](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Eksportuj do pliku prostego](#export-flat-file)
3. [Kreator migracji SQL Database](#sql-migration)
4. [Tworzenie kopii zapasowej i przywracanie bazy danych](#sql-backup)

Opiszemy każdą z poniższych opcji poniżej:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Wdrażanie bazy danych SQL Server w Kreatorze Microsoft Azure VM
**Kreator wdrażania bazy danych SQL Server do Microsoft Azure maszyny wirtualnej** jest prostym i zalecanym sposobem przenoszenia danych z lokalnego wystąpienia SQL Server do SQL Server na maszynie wirtualnej platformy Azure. Aby zapoznać się ze szczegółowymi krokami, a także w omówieniu innych alternatyw, zobacz [Migrowanie bazy danych do SQL Server na maszynie wirtualnej platformy Azure](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md).

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Eksportuj do pliku prostego
Różne metody mogą służyć do zbiorczego eksportowania danych z lokalnego SQL Server, zgodnie z opisem w temacie [import zbiorczy i eksport danych (SQL Server)](/sql/relational-databases/import-export/bulk-import-and-export-of-data-sql-server) . Ten dokument obejmuje przykład programu do kopiowania zbiorczego (BCP). Gdy dane zostaną wyeksportowane do pliku prostego, można je zaimportować do innego serwera SQL przy użyciu importu zbiorczego.

1. Wyeksportuj dane z lokalnego SQL Server do pliku przy użyciu narzędzia bcp w następujący sposób:

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Utwórz bazę danych i tabelę na SQL Server maszynie wirtualnej na platformie Azure przy użyciu `create database` i `create table` dla schematu tabeli wyeksportowanego w kroku 1.
3. Utwórz plik formatu do opisywania schematu tabeli eksportowanych/importowanych danych. Szczegóły pliku formatu opisano w temacie [Tworzenie pliku formatu (SQL Server)](/sql/relational-databases/import-export/create-a-format-file-sql-server).

    Formatuj generowanie plików podczas uruchamiania narzędzia BCP z komputera SQL Server

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`

    Sformatuj generowanie plików podczas zdalnego uruchamiania narzędzia BCP na SQL Server

    `bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n`
4. Użyj dowolnej z metod opisanych w sekcji [przenoszenie danych ze źródła plików](#filesource_to_sqlonazurevm) , aby przenieść dane do SQL Server.

### <a name="sql-database-migration-wizard"></a><a name="sql-migration"></a>Kreator migracji SQL Database
[Kreator migracji bazy danych SQL Server](https://sqlazuremw.codeplex.com/) zapewnia przyjazny sposób przenoszenia danych między dwoma WYSTĄPIENIAMI programu SQL Server. Umożliwia użytkownikowi mapowanie schematu danych między źródłami i tabelami docelowymi, Wybieranie typów kolumn i różnych innych funkcji. Używa kopiowania zbiorczego (BCP) w ramach okładek. Poniżej przedstawiono zrzut ekranu przedstawiający ekran powitalny Kreatora migracji SQL Database.  

![Kreator migracji SQL Server][2]

### <a name="database-back-up-and-restore"></a><a name="sql-backup"></a>Tworzenie kopii zapasowej i przywracanie bazy danych
SQL Server obsługuje:

1. [Tworzenie kopii zapasowej i przywracanie bazy danych](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) (zarówno do pliku lokalnego, jak i do BACPAC eksportu do obiektów BLOB) oraz [aplikacji warstwy danych](/sql/relational-databases/data-tier-applications/data-tier-applications) (przy użyciu BACPAC).
2. Możliwość bezpośredniego tworzenia SQL Server maszyn wirtualnych na platformie Azure przy użyciu skopiowanej bazy danych lub kopiowania do istniejącej bazy danych w programie SQL Database. Aby uzyskać więcej informacji, zobacz [Korzystanie z Kreatora kopiowania bazy danych](/sql/relational-databases/databases/use-the-copy-database-wizard).

Poniżej przedstawiono zrzut ekranu przedstawiający opcje tworzenia kopii zapasowej/przywracania bazy danych z SQL Server Management Studio.

![Narzędzie SQL Server import][1]

## <a name="resources"></a>Zasoby
[Migrowanie bazy danych do SQL Server na maszynie wirtualnej platformy Azure](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md)

[Omówienie programu SQL Server w usłudze Azure Virtual Machines](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png