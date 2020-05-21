---
title: Korzystanie z SQL na żądanie (wersja zapoznawcza)
description: W tym przewodniku szybki start zobaczysz, jak łatwo można wysyłać zapytania o różne typy plików przy użyciu funkcji SQL na żądanie (wersja zapoznawcza).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 8c87b059d94d6b3be1a4b5cf2f83007b746f4156
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658594"
---
# <a name="quickstart-use-sql-on-demand"></a>Szybki Start: korzystanie z SQL na żądanie

Synapse SQL na żądanie (wersja zapoznawcza) to usługa zapytań bezserwerowych, która umożliwia uruchamianie zapytań SQL dotyczących plików umieszczonych w usłudze Azure Storage. W tym przewodniku szybki start dowiesz się, jak wysyłać zapytania o różne typy plików przy użyciu programu SQL na żądanie. Obsługiwane formaty są wymienione w [OPENROWSET](sql/develop-openrowset.md).

Ten przewodnik Szybki Start przedstawia wykonywanie zapytań: CSV, Apache Parquet i plików JSON.

## <a name="prerequisites"></a>Wymagania wstępne

Wybierz klienta SQL, aby wystawić zapytania:

- [Azure Synapse Studio](quickstart-synapse-studio.md) to narzędzie sieci Web, za pomocą którego można przeglądać pliki w magazynie i tworzyć zapytania SQL.
- [Azure Data Studio](sql/get-started-azure-data-studio.md) to narzędzie klienckie, które umożliwia uruchamianie zapytań SQL i notesów w bazie danych na żądanie.
- [SQL Server Management Studio](sql/get-started-ssms.md) to narzędzie klienta, które umożliwia uruchamianie zapytań SQL w bazie danych na żądanie.

Parametry dla tego przewodnika Szybki Start:

| Parametr                                 | Opis                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adres punktu końcowego usługi SQL na żądanie    | Używane jako nazwa serwera                                   |
| Region punktu końcowego usługi SQL na żądanie     | Służy do określenia magazynu, który będzie używany w przykładach |
| Nazwa użytkownika i hasło dostępu do punktu końcowego | Używane do uzyskiwania dostępu do punktu końcowego                               |
| Baza danych służąca do tworzenia widoków         | Baza danych używana jako punkt początkowy w próbkach       |

## <a name="first-time-setup"></a>Konfiguracja pierwszego czasu

Przed rozpoczęciem korzystania z przykładów:

- Tworzenie bazy danych dla widoków (w przypadku, gdy chcesz używać widoków)
- Utwórz poświadczenia, które będą używane przez SQL na żądanie do uzyskiwania dostępu do plików w magazynie

### <a name="create-database"></a>Tworzenie bazy danych

Utwórz własną bazę danych w celach demonstracyjnych. Ta baza danych zostanie użyta do utworzenia widoków i przykładowych zapytań w tym artykule.

> [!NOTE]
> Bazy danych są używane tylko do wyświetlania metadanych, a nie dla rzeczywistych danych.
>Zapisz nazwę bazy danych używaną w dalszej części przewodnika Szybki Start.

Użyj następującego zapytania, aby zmienić `mydbname` nazwę wybranego elementu:

```sql
CREATE DATABASE mydbname
```

### <a name="create-credentials"></a>Utwórz poświadczenia

Aby uruchamiać zapytania przy użyciu programu SQL na żądanie, Utwórz poświadczenia dla programu SQL na żądanie, aby używać go do uzyskiwania dostępu do plików w magazynie.

> [!NOTE]
> Aby można było pomyślnie uruchomić przykłady w tej sekcji, należy użyć tokenu SAS.
>
> Aby rozpocząć korzystanie z tokenów SAS, należy porzucić tożsamość użytkownika, który został wyjaśniony w następującym [artykule](sql/develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through).
>
> Funkcja SQL on-Demand domyślnie zawsze używa przekazywania w usłudze AAD.

Aby uzyskać więcej informacji na temat zarządzania kontrolą dostępu do magazynu, zobacz artykuł[Kontrola dostępu do konta magazynu dla usługi SQL na żądanie](sql/develop-storage-files-storage-access-control.md) .

Wykonaj Poniższy fragment kodu, aby utworzyć poświadczenia używane w przykładach w tej sekcji:

```sql
-- create credentials for containers in our demo storage account
IF EXISTS
   (SELECT * FROM sys.credentials
   WHERE name = 'https://sqlondemandstorage.blob.core.windows.net')
   DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
```

## <a name="query-csv-files"></a>Pliki CSV zapytania

Na poniższej ilustracji znajduje się podgląd pliku do zbadania:

![Pierwsze 10 wierszy pliku CSV bez nagłówka, nowy wiersz stylu systemu Windows.](./sql/media/query-single-csv-file/population.png)

Poniższe zapytanie pokazuje, jak odczytać plik CSV, który nie zawiera wiersza nagłówka, z nowym wierszem w stylu systemu Windows i kolumn rozdzielanych przecinkami:

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/*.csv'
    , FORMAT = 'CSV'
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
> Nie ma potrzeby określania kolumn w klauzuli w `OPENROWSET WITH` przypadku odczytywania plików Parquet. W takim przypadku SQL na żądanie wykorzystuje metadane w pliku Parquet i wiąże kolumny według nazwy.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet'
    , FORMAT='PARQUET'
  ) AS nyc
```

Znajdź więcej informacji na temat [wykonywania zapytań dotyczących plików Parquet](sql/query-parquet-files.md).

## <a name="query-json-files"></a>Wykonywanie zapytań względem plików JSON

### <a name="json-sample-file"></a>Przykładowy plik JSON

Pliki są przechowywane w kontenerze *JSON* , w *książkach*folderów i zawierają jedną pozycję książki z następującą strukturą:

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

Poniższe zapytanie pokazuje, w jaki sposób używać [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) do pobierania wartości skalarnych (tytuł, wydawca) z książki z tytułem *probabilistyczne i metodami statystycznymi w Cryptology, wprowadzeniem do wybranych artykułów*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json'
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
