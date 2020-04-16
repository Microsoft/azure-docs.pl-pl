---
title: Korzystanie z języka SQL na żądanie (wersja zapoznawcza)
description: W tym przewodniku Szybki start zobaczysz i dowiesz się, jak łatwo jest wysyłać zapytania do różnych typów plików za pomocą sql na żądanie (wersja zapoznawcza).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 7ab61b6b0243abea0f5e618c9e83d770f7eaeac7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424384"
---
# <a name="quickstart-using-sql-on-demand"></a>Szybki start: korzystanie z języka SQL na żądanie

Synapse SQL on-demand (preview) to usługa zapytań bezserwerowych, która umożliwia uruchamianie zapytań SQL na plikach umieszczonych w usłudze Azure Storage. W tym przewodniku Szybki start dowiesz się, jak wysyłać zapytania do różnych typów plików przy użyciu języka SQL na żądanie.

Obsługiwane są następujące typy plików: JSON, CSV, Apache Parquet

## <a name="prerequisites"></a>Wymagania wstępne

Wybierz klienta SQL do wystawiania zapytań:

- [Usługa Azure Synapse Studio](quickstart-synapse-studio.md) to narzędzie sieci Web, którego można używać do przeglądania plików w magazynie i tworzenia zapytań SQL.
- [Usługa Azure Data Studio](sql/get-started-azure-data-studio.md) to narzędzie klienckie, które umożliwia uruchamianie zapytań SQL i notesów w bazie danych na żądanie.
- [SQL Server Management Studio](sql/get-started-ssms.md) to narzędzie klienckie, które umożliwia uruchamianie zapytań SQL w bazie danych na żądanie.

Parametry szybkiego startu:

| Parametr                                 | Opis                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adres punktu końcowego usługi SQL na żądanie    | Używany jako nazwa serwera                                   |
| Region punktu końcowego usługi SQL na żądanie     | Służy do określania, jakiego przechowywania będziemy używać w próbkach |
| Nazwa użytkownika i hasło dostępu do punktu końcowego | Służy do uzyskiwania dostępu do punktu końcowego                               |
| Baza danych używana do tworzenia widoków         | Baza danych używana jako punkt wyjścia w przykładach       |

## <a name="first-time-setup"></a>Konfiguracja po raz pierwszy

Przed użyciem próbek:

- Tworzenie bazy danych dla widoków (w przypadku, gdy chcesz użyć widoków)
- Tworzenie poświadczeń używanych przez sql na żądanie w celu uzyskania dostępu do plików w magazynie

### <a name="create-database"></a>Tworzenie bazy danych

Stwórz własną bazę danych do celów demonstracyjnych. Jest to baza danych, w której można utworzyć widoki. Użyj tej bazy danych w przykładowych kwerend w tym artykule.

> [!NOTE]
> Bazy danych są używane tylko do wyświetlania metadanych, a nie dla rzeczywistych danych.
>
> Zapisz nazwę bazy danych używasz do użycia w dalszej części przewodnika Szybki start.

Użyj następującej kwerendy, zmieniając `mydbname` nazwę na wybraną:

```sql
CREATE DATABASE mydbname
```

### <a name="create-credentials"></a>Tworzenie poświadczeń

Aby uruchamiać kwerendy przy użyciu języka SQL na żądanie, należy utworzyć poświadczenia dla języka SQL na żądanie, aby uzyskać dostęp do plików w magazynie.

> [!NOTE]
> Należy zauważyć, że należy utworzyć poświadczenia dostępu do konta magazynu. Chociaż SQL na żądanie może uzyskać dostęp do magazynów z różnych regionów, posiadanie magazynu i obszaru roboczego Usługi Azure Synapse w tym samym regionie zapewni lepszą wydajność.

Zmodyfikuj następujący fragment kodu, aby utworzyć poświadczenia dla kontenerów CSV, JSON i Parquet:

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

## <a name="querying-csv-files"></a>Wykonywanie zapytań o pliki CSV

Poniższy obraz jest podglądem pliku, który ma zostać zapytany:

![Pierwsze 10 wierszy pliku CSV bez nagłówka, nowy wiersz w stylu Windows.](./sql/media/query-single-csv-file/population.png)

W poniższej kwerendzie pokazano sposób odczytywania pliku CSV, który nie zawiera wiersza nagłówka, z nowym wierszem w stylu systemu Windows i kolumnami rozdzielanych przecinkami:

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/*.csv'
    , FORMAT = 'CSV'
    , FIELDTERMINATOR =','
    , ROWTERMINATOR = '\n'
  )
WITH
  (
      [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2
    , [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2
    , [year] smallint
    , [population] bigint
  ) AS [r]
WHERE
  country_name = 'Luxembourg' AND year = 2017
```

Schemat można określić w czasie kompilacji kwerendy.
Aby uzyskać więcej przykładów, zobacz jak [kwerendy pliku CSV](sql/query-single-csv-file.md).

## <a name="querying-parquet-files"></a>Kwerenda plików parkietu

W poniższym przykładzie przedstawiono możliwości wnioskowania schematu automatycznego do wykonywania zapytań o pliki parkietu. Zwraca liczbę wierszy we wrześniu 2017 r. bez określania schematu.

> [!NOTE]
> Nie trzeba określać kolumny `OPENROWSET WITH` w klauzuli podczas odczytywania plików parkietu. W takim przypadku SQL na żądanie wykorzystuje metadane w pliku Parkiet i kolumny powiązania według nazwy.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet'
    , FORMAT='PARQUET'
  ) AS nyc
```

Znajdź więcej informacji na temat wykonywania zapytań o [pliki parkietu](sql/query-parquet-files.md)].

## <a name="querying-json-files"></a>Wykonywanie zapytań o pliki JSON

### <a name="json-sample-file"></a>Przykładowy plik JSON

Pliki są przechowywane w kontenerze *json,* *portfelach folderów*i zawierają pojedynczy wpis książki o następującej strukturze:

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

### <a name="querying-json-files"></a>Wykonywanie zapytań o pliki JSON

Poniższa kwerenda pokazuje, jak używać [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) do pobierania wartości skalarnych (tytuł, wydawca) z książki o tytule *Probabilistic i metody statystyczne w kryptologii, Wprowadzenie przez wybrane artykuły:*

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
> Czytamy cały plik JSON jako pojedynczy wiersz /kolumna, więc FIELDTERMINATOR, FIELDQUOTE i ROWTERMINATOR są ustawione na 0x0b, ponieważ nie spodziewamy się go znaleźć w pliku.

## <a name="next-steps"></a>Następne kroki

Teraz możesz zacząć od następujących artykułów Szybki start:

- [Zapytanie o pojedynczy plik CSV](sql/query-single-csv-file.md)
- [Foldery kwerend i wiele plików CSV](sql/query-folders-multiple-csv-files.md)
- [Kwerenda określonych plików](sql/query-specific-files.md)
- [Pliki parkietu kwerendy](sql/query-parquet-files.md)
- [Kwerenda Parkiet zagnieżdżone typy](sql/query-parquet-nested-types.md)
- [Kwerenda plików JSON](sql/query-json-files.md)
- [Tworzenie i używanie widoków](sql/create-use-views.md)
- [Tworzenie i używanie tabel zewnętrznych](sql/create-use-external-tables.md)
- [Utrwalanie wyniku kwerendy w magazynie platformy Azure](sql/create-external-table-as-select.md)

Przejdź do następnego artykułu, aby dowiedzieć się, jak zbadać pojedynczy plik CSV.
> [!div class="nextstepaction"]
> [Zapytanie o pojedynczy plik CSV](sql/query-single-csv-file.md)
