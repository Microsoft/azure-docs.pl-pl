---
title: 'Szybki Start: korzystanie z puli SQL bezserwerowej'
description: W tym przewodniku szybki start zobaczysz i dowiesz się, jak łatwo można wysyłać zapytania o różne typy plików przy użyciu puli SQL bezserwerowej.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 8607355069bbae5983239ddbd3e8752143f31497
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676335"
---
# <a name="quickstart-use-serverless-sql-pool"></a>Szybki Start: korzystanie z puli SQL bezserwerowej

Synapse bezserwerowa Pula SQL jest usługą kwerendy bezserwerowej, która umożliwia uruchamianie zapytań SQL dotyczących plików umieszczonych w usłudze Azure Storage. W tym przewodniku szybki start dowiesz się, jak wykonywać zapytania o różne typy plików przy użyciu puli SQL bezserwerowej. Obsługiwane formaty są wymienione w [OPENROWSET](sql/develop-openrowset.md).

Ten przewodnik Szybki Start przedstawia wykonywanie zapytań: CSV, Apache Parquet i plików JSON.

## <a name="prerequisites"></a>Wymagania wstępne

Wybierz klienta SQL na potrzeby wykonywania zapytań:

- [Azure Synapse Studio](./get-started-create-workspace.md) to narzędzie sieci Web, za pomocą którego można przeglądać pliki w magazynie i tworzyć zapytania SQL.
- [Azure Data Studio](sql/get-started-azure-data-studio.md) to narzędzie klienckie, które umożliwia uruchamianie zapytań SQL i notesów w bazie danych na żądanie.
- [SQL Server Management Studio](sql/get-started-ssms.md) to narzędzie klienta, które umożliwia uruchamianie zapytań SQL w bazie danych na żądanie.

Parametry dla tego przewodnika Szybki Start:

| Parametr                                 | Opis                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| adres punktu końcowego usługi puli SQL bezserwerowej    | Używane jako nazwa serwera                                   |
| Region punktu końcowego usługi puli SQL bezserwerowej     | Służy do określenia magazynu, który będzie używany w przykładach |
| Nazwa użytkownika i hasło dostępu do punktu końcowego | Używane do uzyskiwania dostępu do punktu końcowego                               |
| Baza danych służąca do tworzenia widoków         | Baza danych używana jako punkt początkowy w próbkach       |

## <a name="first-time-setup"></a>Konfiguracja pierwszego czasu

Przed rozpoczęciem korzystania z przykładów:

- Tworzenie bazy danych dla widoków (w przypadku, gdy chcesz używać widoków)
- Utwórz poświadczenia, które mają być używane przez bezserwerową pulę SQL do uzyskiwania dostępu do plików w magazynie

### <a name="create-database"></a>Tworzenie bazy danych

Utwórz własną bazę danych w celach demonstracyjnych. Ta baza danych zostanie użyta do utworzenia widoków i przykładowych zapytań w tym artykule.

> [!NOTE]
> Bazy danych są używane tylko do wyświetlania metadanych, a nie dla rzeczywistych danych.
>Zapisz nazwę bazy danych używaną w dalszej części przewodnika Szybki Start.

Użyj następującego zapytania, aby zmienić `mydbname` nazwę wybranego elementu:

```sql
CREATE DATABASE mydbname
```

### <a name="create-data-source"></a>Utwórz źródło danych

Aby uruchamiać zapytania przy użyciu puli SQL bezserwerowej, należy utworzyć źródło danych, za pomocą którego można uzyskać dostęp do plików w magazynie przy użyciu puli SQL bezserwerowej.
Wykonaj Poniższy fragment kodu, aby utworzyć źródło danych używane w przykładach w tej sekcji:

```sql
-- create master key that will protect the credentials:
CREATE MASTER KEY ENCRYPTION BY PASSWORD = <enter very strong password here>

-- create credentials for containers in our demo storage account
CREATE DATABASE SCOPED CREDENTIAL sqlondemand
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

## <a name="query-csv-files"></a>Wykonywanie zapytań o pliki CSV

Na poniższej ilustracji znajduje się podgląd pliku do zbadania:

![Pierwsze 10 wierszy pliku CSV bez nagłówka, nowy wiersz stylu systemu Windows.](./sql/media/query-single-csv-file/population.png)

Poniższe zapytanie pokazuje, jak odczytać plik CSV, który nie zawiera wiersza nagłówka, z nowym wierszem w stylu systemu Windows i kolumn rozdzielanych przecinkami:

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'csv/population/*.csv',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT = 'CSV', PARSER_VERSION = '2.0'
  )
WITH
  (
      country_code VARCHAR (5)
    , country_name VARCHAR (100)
    , year smallint
    , population bigint
  ) AS r
WHERE
  country_name = 'Luxembourg' AND year = 2017
```

W czasie kompilacji zapytania można określić schemat.
Aby uzyskać więcej przykładów, zobacz jak [zbadać plik CSV](sql/query-single-csv-file.md).

## <a name="query-parquet-files"></a>Wykonywanie zapytań względem plików Parquet

Poniższy przykład pokazuje możliwości automatycznego wnioskowania schematu do wykonywania zapytań dotyczących plików Parquet. Zwraca liczbę wierszy we wrześniu 2017 bez określania schematu.

> [!NOTE]
> Nie ma potrzeby określania kolumn w klauzuli w `OPENROWSET WITH` przypadku odczytywania plików Parquet. W takim przypadku bezserwerowa Pula SQL korzysta z metadanych w pliku Parquet i wiąże kolumny według nazwy.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'parquet/taxi/year=2017/month=9/*.parquet',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT='PARQUET'
  ) AS nyc
```

Znajdź więcej informacji na temat [wykonywania zapytań dotyczących plików Parquet](sql/query-parquet-files.md).

## <a name="query-json-files"></a>Wykonywanie zapytań względem plików JSON

### <a name="json-sample-file"></a>Przykładowy plik JSON

Pliki są przechowywane w kontenerze *JSON* , w *książkach* folderów i zawierają jedną pozycję książki z następującą strukturą:

```json
{  
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[  
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

### <a name="query-json-files"></a>Wykonywanie zapytań względem plików JSON

Poniższe zapytanie pokazuje, w jaki sposób używać [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) do pobierania wartości skalarnych (tytuł, wydawca) z książki z tytułem *probabilistyczne i metodami statystycznymi w Cryptology, wprowadzeniem do wybranych artykułów*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'json/books/*.json',
      DATA_SOURCE = 'SqlOnDemandDemo'
    , FORMAT='CSV'
    , FIELDTERMINATOR ='0x0b'
    , FIELDQUOTE = '0x0b'
    , ROWTERMINATOR = '0x0b'
  )
WITH
  ( jsonContent varchar(8000) ) AS [r]
WHERE
  JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics'
```

> [!IMPORTANT]
> Odczytujemy cały plik JSON jako pojedynczy wiersz/kolumnę. Tak więc FIELDTERMINATOR, FIELDQUOTE i ROWTERMINATOR są ustawione na 0x0B, ponieważ nie oczekujemy, że znajdą się w pliku.

## <a name="next-steps"></a>Następne kroki

Teraz możesz przystąpić do kontynuowania pracy z następującymi artykułami:

- [Kwerenda pojedynczego pliku CSV](sql/query-single-csv-file.md)
- [Foldery zapytań i wiele plików CSV](sql/query-folders-multiple-csv-files.md)
- [Kwerenda określonych plików](sql/query-specific-files.md)
- [Wykonywanie zapytań względem plików Parquet](sql/query-parquet-files.md)
- [Wykonywanie zapytań względem typów zagnieżdżonych Parquet](sql/query-parquet-nested-types.md)
- [Wykonywanie zapytań względem plików JSON](sql/query-json-files.md)
- [Tworzenie widoków i korzystanie z nich](sql/create-use-views.md)
- [Tworzenie i używanie tabel zewnętrznych](sql/create-use-external-tables.md)
- [Utrwalanie wyniku zapytania w usłudze Azure Storage](sql/create-external-table-as-select.md)
- [Kwerenda pojedynczego pliku CSV](sql/query-single-csv-file.md)