---
title: Włączanie i wyłączanie zasad przechowywania danych — Azure SQL Edge
description: Informacje o włączaniu i wyłączaniu zasad przechowywania danych w usłudze Azure SQL Edge
keywords: SQL Edge, przechowywanie danych
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: 5b8dd911952a63ba8775f27a6128ff61e849e823
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861429"
---
# <a name="enable-and-disable-data-retention-policies"></a>Włączanie i wyłączanie zasad przechowywania danych

W tym temacie opisano sposób włączania i wyłączania zasad przechowywania danych dla bazy danych i tabeli. 

## <a name="enable-data-retention-for-a-database"></a>Włączanie przechowywania danych dla bazy danych

Poniższy przykład pokazuje, jak włączyć przechowywanie danych przy użyciu [polecenia ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-set-options).

```sql
ALTER DATABASE [<DatabaseName>] SET DATA_RETENTION  ON;
```

## <a name="check-if-data-retention-is-enabled-for-a-database"></a>Sprawdź, czy dla bazy danych jest włączone przechowywanie danych

Następujące polecenie służy do sprawdzania, czy dla bazy danych jest włączone przechowywanie danych
```sql
SELECT is_data_retention_enabled, name
FROM sys.databases;
```

## <a name="enable-data-retention-for-a-table"></a>Włączanie przechowywania danych w tabeli

Przechowywanie danych musi być włączone dla każdej tabeli, dla której dane mają być automatycznie przeczyszczane. Po włączeniu przechowywania danych w bazie danych i tabeli zadanie systemu w tle okresowo skanuje tabelę w celu zidentyfikowania i usunięcia wszelkich przestarzałych (przestarzałych) wierszy. Przechowywanie danych można włączyć w tabeli podczas tworzenia tabeli za pomocą instrukcji [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) lub using [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

Poniższy przykład pokazuje, jak włączyć przechowywanie danych dla tabeli przy użyciu polecenia [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql). 

```sql
CREATE TABLE [dbo].[data_retention_table] 
(
[dbdatetime2] datetime2(7), 
[product_code] int, 
[value] char(10),  
CONSTRAINT [pk_current_data_retention_table] PRIMARY KEY CLUSTERED ([product_code])
) WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )
```

`WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )`Część polecenia CREATE TABLE umożliwia ustawienie przechowywania danych w tabeli. Polecenie używa następujących wymaganych parametrów 

- DATA_DELETION — wskazuje, czy przechowywanie danych jest włączone czy wyłączone.
- FILTER_COLUMN-Name w kolumnie w tabeli, która zostanie użyta do sprawdzenia, czy wiersze są przestarzałe. Kolumna filtru może być tylko kolumną z następującymi typami danych 
    - Data
    - SmallDateTime
    - DateTime
    - DateTime2
    - DateTimeOffset
- RETENTION_PERIOD — wartość całkowita, a po niej deskryptor jednostki. Dozwolone jednostki to dzień, dni, tydzień, tygodnie, miesiąc, miesiące, rok i lata.

Poniższy przykład pokazuje, jak włączyć przechowywanie danych dla tabeli przy użyciu [instrukcji ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).  

```sql
Alter Table [dbo].[data_retention_table]
SET (DATA_DELETION = On (FILTER_COLUMN = [timestamp], RETENTION_PERIOD = 1 day))
```

## <a name="check-if-data-retention-is-enabled-for-a-table"></a>Sprawdź, czy w tabeli jest włączone przechowywanie danych

Następujące polecenie służy do sprawdzania tabel, dla których jest włączone przechowywanie danych

```sql
select name, data_retention_period, data_retention_period_unit from sys.tables
```

Wartość data_retention_period =-1 i data_retention_period_unit jako NIESKOŃCZONość wskazuje, że przechowywanie danych nie jest ustawione w tabeli.

Poniższe zapytanie może służyć do identyfikowania kolumny używanej jako filter_column do przechowywania danych. 

```sql
Select name from sys.columns
where is_data_deletion_filter_column =1 
and object_id = object_id(N'dbo.data_retention_table', N'U')
```

## <a name="correlating-db-and-table-data-retention-settings"></a>Skorelowanie ustawień przechowywania danych bazy danych i tabeli

Ustawienie przechowywania danych w bazie danych i tabeli służy do określenia, czy autoczyszczenie dla przestarzałych wierszy zostanie uruchomione w tabelach, czy nie. 

|Opcja bazy danych | Opcja tabeli | Zachowanie |
|----------------|--------------|----------|
| WYŁ. | WYŁ. | Zasady przechowywania danych są wyłączone, a autoczyszczenie przestarzałych rekordów jest wyłączone.|
| WYŁ. | ON  | Zasady przechowywania danych są włączone dla tabeli. Autooczyszczanie przestarzałych rekordów jest wyłączone, natomiast metody ręcznego czyszczenia mogą służyć do czyszczenia przestarzałych rekordów. |
| ON | WYŁ. | Zasady przechowywania danych są włączone na poziomie bazy danych. Jednak ta opcja jest wyłączona na poziomie tabeli, ale nie jest wykonywane czyszczenie przestarzałych wierszy.|
| ON | ON | Zasady przechowywania danych są włączone zarówno dla bazy danych, jak i dla tabel. Automatyczne czyszczenie przestarzałych rekordów jest włączone. |

## <a name="disable-data-retention-on-a-table"></a>Wyłączanie przechowywania danych w tabeli 

Przechowywanie danych można wyłączyć w tabeli przy użyciu [instrukcji ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql). Następujące polecenie służy do wyłączania przechowywania danych w tabeli.

```sql
Alter Table [dbo].[data_retention_table]
Set (DATA_DELETION = OFF)
```

## <a name="disable-data-retention-on-a-database"></a>Wyłączanie przechowywania danych w bazie danych

Przechowywanie danych można wyłączyć w tabeli przy użyciu [polecenia ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-set-options). Następujące polecenie służy do wyłączania przechowywania danych w bazie danych.

```sql
ALTER DATABASE <DatabaseName> SET DATA_RETENTION  OFF;
```

## <a name="next-steps"></a>Następne kroki
- [Przechowywanie danych i automatyczne czyszczenie danych](data-retention-overview.md)
- [Zarządzanie danymi historycznymi przy użyciu zasad przechowywania](data-retention-cleanup.md)
