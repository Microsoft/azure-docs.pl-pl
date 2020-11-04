---
title: Równoległy import danych zbiorczych w tabelach partycji SQL — proces nauki danych zespołu
description: Kompiluj partycjonowane tabele na potrzeby szybkiego równoległego importowania danych do bazy danych SQL Server.
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
ms.openlocfilehash: 456e881d84697f4542f972ac0798cc95a3455b3c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322410"
---
# <a name="build-and-optimize-tables-for-fast-parallel-import-of-data-into-a-sql-server-on-an-azure-vm"></a>Kompiluj i Optymalizuj tabele umożliwiające szybkie importowanie danych do SQL Server na maszynie wirtualnej platformy Azure

W tym artykule opisano sposób tworzenia tabel partycjonowanych na potrzeby szybkiego równoległego importowania danych do bazy danych SQL Server. W celu ładowania i przesyłania danych Big Data do bazy danych SQL można ulepszyć Importowanie danych do bazy danych SQL i kolejnych zapytań przy użyciu *partycjonowanych tabel i widoków*. 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Tworzenie nowej bazy danych i zestawu grup plików
* [Utwórz nową bazę danych](/sql/t-sql/statements/create-database-transact-sql), jeśli jeszcze nie istnieje.
* Dodawanie grup plików bazy danych do bazy danych, która zawiera partycjonowane pliki fizyczne. 
* Można to zrobić przy użyciu [polecenia](/sql/t-sql/statements/alter-database-transact-sql-set-options) [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql) , jeśli baza danych już istnieje.
* Dodaj co najmniej jeden plik (w razie potrzeby) do każdej grupy plików bazy danych.
  
  > [!NOTE]
  > Określ docelową grupę plików, która przechowuje dane dla tej partycji oraz nazwy plików fizycznych baz danych, w których są przechowywane dane grupy plików.
  > 
  > 

Poniższy przykład tworzy nową bazę danych z trzema grupami plików innymi niż grupy podstawowe i dzienników, zawierające jeden plik fizyczny w każdym z nich. Pliki bazy danych są tworzone w domyślnym folderze danych SQL Server, zgodnie z konfiguracją w wystąpieniu SQL Server. Aby uzyskać więcej informacji na temat domyślnych lokalizacji plików, zapoznaj się z tematem [lokalizacje plików dla domyślnych i nazwanych wystąpień SQL Server](/sql/sql-server/install/file-locations-for-default-and-named-instances-of-sql-server).

```sql
   DECLARE @data_path nvarchar(256);
   SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);

   EXECUTE ('
      CREATE DATABASE <database_name>
         ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup2'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup3'', FILENAME = ''' + @data_path + '<file_name_3>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ) 
         LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')
```

## <a name="create-a-partitioned-table"></a>Tworzenie tabeli partycjonowanej
Aby utworzyć partycjonowane tabele zgodnie ze schematem danych, zamapowane do grup plików bazy danych utworzonych w poprzednim kroku, należy najpierw utworzyć funkcję partycji i schemat. Po zaimportowaniu zbiorczych danych do tabel partycjonowanych rekordy są dystrybuowane między grupami plików zgodnie ze schematem partycji, zgodnie z poniższym opisem.

### <a name="1-create-a-partition-function"></a>1. Utwórz funkcję partycji
[Tworzenie funkcji partycji](/sql/t-sql/statements/create-partition-function-transact-sql) Ta funkcja definiuje zakres wartości/granic do uwzględnienia w każdej tabeli partycji pojedynczej, na przykład w celu ograniczenia partycji według miesiąca (niektóre \_ pola DateTime \_ ) w roku 2013:
  
```sql
   CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
      AS RANGE RIGHT FOR VALUES (
         '20130201', '20130301', '20130401',
         '20130501', '20130601', '20130701', '20130801',
         '20130901', '20131001', '20131101', '20131201' )
```

### <a name="2-create-a-partition-scheme"></a>2. Utwórz schemat partycji
[Utwórz schemat partycji](/sql/t-sql/statements/create-partition-scheme-transact-sql). Ten schemat mapuje każdy zakres partycji w funkcji Partition na fizyczną grupę plików, na przykład:
  
```sql
      CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
```
 
Aby sprawdzić, jakie zakresy są stosowane w każdej partycji zgodnie z funkcją/schemat, uruchom następujące zapytanie:
  
```sql
   SELECT psch.name as PartitionScheme,
            prng.value AS PartitionValue,
            prng.boundary_id AS BoundaryID
   FROM sys.partition_functions AS pfun
   INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
   INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
   WHERE pfun.name = <DatetimeFieldPFN>
```

### <a name="3-create-a-partition-table"></a>3. Tworzenie tabeli partycji
[Utwórz partycjonowane tabele](/sql/t-sql/statements/create-table-transact-sql)zgodnie ze schematem danych i określ schemat partycji oraz pole ograniczenia używane do partycjonowania tabeli, na przykład:
  
```sql
   CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)
```

Aby uzyskać więcej informacji, zobacz [Tworzenie partycjonowanych tabel i indeksów](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes).

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Importuj zbiorczo dane dla każdej tabeli partycji pojedynczej

* Można użyć narzędzia BCP, BULK INSERT lub innych metod, takich jak [SQL Server Kreatora migracji](https://sqlazuremw.codeplex.com/). W podanym przykładzie zastosowano metodę BCP.
* Zmień [bazę danych](/sql/t-sql/statements/alter-database-transact-sql-set-options) , aby zmienić schemat rejestrowania transakcji na BULK_LOGGED, aby zminimalizować obciążenie rejestrowania, na przykład:
  
   ```sql
      ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
   ```
* Aby przyspieszyć ładowanie danych, uruchom równolegle operacje importu zbiorczego. Aby uzyskać porady dotyczące przyspieszania importowania zbiorczego danych Big Data do baz danych SQL Server, zobacz [obciążenie 1 TB w czasie krótszym niż 1 godzina](/archive/blogs/sqlcat/load-1tb-in-less-than-1-hour).

Poniższy skrypt programu PowerShell jest przykładem równoległego ładowania danych przy użyciu narzędzia BCP.

```powershell
    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0

    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>

    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"

    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir

    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }

    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }

    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }

    Get-Job

    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date
```

## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Tworzenie indeksów w celu optymalizowania sprzężeń i wydajności zapytań
* W przypadku wyodrębnienia danych do modelowania z wielu tabel Utwórz indeksy w kluczach sprzężenia, aby zwiększyć wydajność przyłączania.
* [Utwórz indeksy](/sql/t-sql/statements/create-index-transact-sql) (klastrowane lub nieklastrowane) przeznaczone dla tej samej grupy plików dla każdej partycji, na przykład:
  
```sql
   CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
--  or,
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
 ```
 
  > [!NOTE]
  > Przed zbiorczym zaimportowaniem danych można utworzyć indeksy. Tworzenie indeksu przed importem zbiorczym spowalnia ładowanie danych.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Zaawansowany proces i technologia analityczna w przykładowym działaniu
Aby zapoznać się z kompleksowym przykładem, korzystając z procesu nauki o danych zespołowych z publicznym zestawem danych, zobacz [zespołowe przetwarzanie danych w ramach działania: używanie SQL Server](sql-walkthrough.md).