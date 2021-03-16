---
title: Ładowanie danych z pliku CSV do bazy danych (bcp)
description: Dane niewielkich rozmiarów można importować do usługi Azure SQL Database za pomocą narzędzia bcp.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 216410d423a70037588a6fb596771a702a13c003
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472242"
---
# <a name="load-data-from-csv-into-azure-sql-database-or-sql-managed-instance-flat-files"></a>Ładowanie danych z pliku CSV do wystąpienia zarządzanego Azure SQL Database lub SQL (pliki proste)
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Można użyć narzędzia wiersza polecenia bcp do zaimportowania danych z pliku CSV do Azure SQL Database lub wystąpienia zarządzanego Azure SQL.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

### <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebne są:

* Baza danych w Azure SQL Database
* Zainstalowane narzędzie wiersza polecenia bcp
* Zainstalowane narzędzie wiersza polecenia sqlcmd

Narzędzia bcp i sqlcmd można pobrać z [dokumentacji Microsoft sqlcmd] [ https://docs.microsoft.com/sql/tools/sqlcmd-utility?view=sql-server-ver15 ].

### <a name="data-in-ascii-or-utf-16-format"></a>Dane w formacie ASCII lub UTF-16

Jeśli próbujesz wykonać kroki tego samouczka z użyciem własnych danych, musisz zastosować do danych kodowanie ASCII lub UTF-16, ponieważ narzędzie bcp nie obsługuje formatu UTF-8.

## <a name="1-create-a-destination-table"></a>1. Tworzenie tabeli docelowej

Zdefiniuj tabelę w usłudze SQL Database jako tabelę docelową. Kolumny w tabeli muszą odpowiadać danym w poszczególnych wierszach pliku danych.

Aby utworzyć tabelę, otwórz wiersz polecenia i użyj programu sqlcmd.exe, aby uruchomić następujące polecenie:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```

## <a name="2-create-a-source-data-file"></a>2. Utwórz plik danych źródłowych

Otwórz program Notatnik i skopiuj następujące wiersze danych do nowego pliku tekstowego, a następnie zapisz ten plik w lokalnym katalogu tymczasowym: C:\Temp\DimDate2.txt. Te dane są w formacie ASCII.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

(Opcjonalnie) Aby wyeksportować własne dane z bazy danych programu SQL Server, otwórz wiersz polecenia i uruchom następujące polecenie. Zastąp parametry TableName, ServerName, DatabaseName, Username i Password odpowiednimi informacjami.

```bcp
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ,
```

## <a name="3-load-the-data"></a>3. Załaduj dane

Aby załadować dane, otwórz wiersz polecenia i uruchom następujące polecenie, zastępując najpierw wartości parametrów nazwą serwera, nazwą bazy danych, nazwą użytkownika i hasłem.

```bcp
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

Użyj tego polecenia, aby sprawdzić, czy dane zostały załadowane poprawnie:

```bcp
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Wyniki powinny wyglądać następująco:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

## <a name="next-steps"></a>Następne kroki

Aby przeprowadzić migrację bazy danych programu SQL Server, zobacz [SQL Server database migration](database/migrate-to-database-from-sql-server.md) (Migracja bazy danych programu SQL Server).

<!--MSDN references-->
[bcp]: /sql/tools/bcp-utility
[CREATE TABLE syntax]: /sql/t-sql/statements/create-table-azure-sql-data-warehouse

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
