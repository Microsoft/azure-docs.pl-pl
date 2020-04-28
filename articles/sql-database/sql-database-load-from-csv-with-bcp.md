---
title: Ładowanie danych z pliku CSV do bazy danych (bcp)
description: Dane niewielkich rozmiarów można importować do usługi Azure SQL Database za pomocą narzędzia bcp.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: b0df3d588f1d9b0a50c3ea7a583b0704e7e85c39
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73827488"
---
# <a name="load-data-from-csv-into-azure-sql-database-flat-files"></a>Ładowanie danych z pliku CSV do usługi Azure SQL Database (pliki proste)

Dane z pliku CSV można importować do usługi Azure SQL Database za pomocą narzędzia wiersza polecenia bcp.

## <a name="before-you-begin"></a>Przed rozpoczęciem

### <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebne są:

* Serwer Azure SQL Database i baza danych
* Zainstalowane narzędzie wiersza polecenia bcp
* Zainstalowane narzędzie wiersza polecenia sqlcmd

Narzędzia bcp i sqlcmd można pobrać z [Centrum pobierania Microsoft][Microsoft Download Center].

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

Aby przeprowadzić migrację bazy danych programu SQL Server, zobacz [SQL Server database migration](sql-database-single-database-migrate.md) (Migracja bazy danych programu SQL Server).

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
