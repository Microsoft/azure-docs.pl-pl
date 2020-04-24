---
title: Korzystanie z SQL na żądanie (wersja zapoznawcza)
description: W tym przewodniku szybki start zobaczysz, jak łatwo można wykonywać zapytania o różne typy plików przy użyciu funkcji SQL na żądanie (wersja zapoznawcza).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: d49918fc67a45419e5c7ca123642c48e689a1496
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113786"
---
# <a name="quickstart-using-sql-on-demand"></a>Szybki Start: korzystanie z SQL na żądanie

Synapse SQL na żądanie (wersja zapoznawcza) to bezserwerowa usługa zapytań, która umożliwia uruchamianie zapytań SQL dotyczących plików umieszczonych w usłudze Azure Storage. W tym przewodniku szybki start dowiesz się, jak wysyłać zapytania o różne typy plików przy użyciu programu SQL na żądanie.

Obsługiwane są następujące typy plików: JSON, CSV, Apache Parquet

## <a name="prerequisites"></a>Wymagania wstępne

Wybierz klienta SQL, aby wystawić zapytania:

- [Azure Synapse Studio](quickstart-synapse-studio.md) to narzędzie sieci Web, za pomocą którego można przeglądać pliki w magazynie i tworzyć zapytania SQL.
- [Azure Data Studio](sql/get-started-azure-data-studio.md) to narzędzie klienckie, które umożliwia uruchamianie zapytań SQL i notesów w bazie danych na żądanie.
- [SQL Server Management Studio](sql/get-started-ssms.md) to narzędzie klienta, które umożliwia uruchamianie zapytań SQL w bazie danych na żądanie.

Parametry szybkiego startu:

| Parametr                                 | Opis                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adres punktu końcowego usługi SQL na żądanie    | Używane jako nazwa serwera                                   |
| Region punktu końcowego usługi SQL na żądanie     | Służy do określenia magazynu, który będzie używany w przykładach |
| Nazwa użytkownika i hasło dostępu do punktu końcowego | Używane do uzyskiwania dostępu do punktu końcowego                               |
| Baza danych służąca do tworzenia widoków         | Baza danych używana jako punkt początkowy w próbkach       |

## <a name="first-time-setup"></a>Konfiguracja pierwszego czasu

Przed użyciem przykładów:

- Tworzenie bazy danych dla widoków (w przypadku, gdy chcesz używać widoków)
- Utwórz poświadczenia, które będą używane przez SQL na żądanie do uzyskiwania dostępu do plików w magazynie

### <a name="create-database"></a>Tworzenie bazy danych

Utwórz własną bazę danych w celach demonstracyjnych. To jest baza danych, w której tworzysz widoki. Użyj tej bazy danych w przykładowych zapytaniach w tym artykule.

> [!NOTE]
> Bazy danych są używane tylko do wyświetlania metadanych, a nie dla rzeczywistych danych.
>
> Zapisz nazwę bazy danych używaną w dalszej części przewodnika Szybki Start.

Użyj następującego zapytania, aby zmienić `mydbname` nazwę wybranego elementu:

```sql
CREATE DATABASE mydbname
```

### <a name="create-credentials"></a>Utwórz poświadczenia

Aby uruchamiać zapytania przy użyciu programu SQL na żądanie, Utwórz poświadczenia dla programu SQL na żądanie, aby używać go do uzyskiwania dostępu do plików w magazynie.

> [!NOTE]
> Aby pomyślnie uruchomić przykłady w tej sekcji, musisz użyć tokenu SAS.
>
> Aby rozpocząć korzystanie z tokenów SAS, należy porzucić tożsamość użytkownika, który został wyjaśniony w następującym [artykule](sql/develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through).
>
> Funkcja SQL on-Demand domyślnie zawsze używa przekazywania w usłudze AAD.

Aby uzyskać więcej informacji na temat zarządzania kontrolą dostępu do magazynu, zaznacz ten [link](sql/develop-storage-files-storage-access-control.md).

Wykonaj Poniższy fragment kodu, aby utworzyć poświadczenie używane w przykładach w tej sekcji:

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

## <a name="querying-csv-files"></a>Wykonywanie zapytania dotyczącego plików CSV

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

## <a name="querying-parquet-files"></a>Wykonywanie zapytania dotyczącego plików Parquet

Poniższy przykład pokazuje możliwości automatycznego wnioskowania schematu do wykonywania zapytań dotyczących plików Parquet. Zwraca liczbę wierszy we wrześniu 2017 bez określania schematu.

> [!NOTE]
> Nie ma potrzeby określania kolumn w klauzuli w `OPENROWSET WITH` przypadku odczytywania plików Parquet. W takim przypadku SQL na żądanie wykorzystuje metadane w pliku Parquet i tworzy powiązanie kolumn według nazwy.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet'
    , FORMAT='PARQUET'
  ) AS nyc
```

Więcej informacji na temat [wykonywania zapytań dotyczących plików Parquet](sql/query-parquet-files.md)].

## <a name="querying-json-files"></a>Wykonywanie zapytania dotyczącego plików JSON

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

### <a name="querying-json-files"></a>Wykonywanie zapytania dotyczącego plików JSON

Poniższe zapytanie pokazuje, jak używać [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) do pobierania wartości skalarnych (tytuł, wydawca) z książki z tytułem *probabilistyczne i metodami statystycznymi w Cryptology, wprowadzeniem do wybranych artykułów*:

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
> Odczytujemy cały plik JSON jako pojedynczy wiersz/kolumnę, więc FIELDTERMINATOR, FIELDQUOTE i ROWTERMINATOR są ustawione na 0x0B, ponieważ nie oczekujemy, że znajdą się w pliku.

## <a name="next-steps"></a>Następne kroki

Teraz możesz zacząć od następujących artykułów szybkiego startu:

- [Kwerenda pojedynczego pliku CSV](sql/query-single-csv-file.md)
- [Foldery zapytań i wiele plików CSV](sql/query-folders-multiple-csv-files.md)
- [Kwerenda określonych plików](sql/query-specific-files.md)
- [Wykonywanie zapytań względem plików Parquet](sql/query-parquet-files.md)
- [Wykonywanie zapytań względem typów zagnieżdżonych Parquet](sql/query-parquet-nested-types.md)
- [Wykonywanie zapytań względem plików JSON](sql/query-json-files.md)
- [Tworzenie widoków i korzystanie z nich](sql/create-use-views.md)
- [Tworzenie i używanie tabel zewnętrznych](sql/create-use-external-tables.md)
- [Utrwalanie wyniku zapytania w usłudze Azure Storage](sql/create-external-table-as-select.md)

Przejdź do następnego artykułu, aby dowiedzieć się, jak wykonać zapytanie dotyczące pojedynczego pliku CSV.
> [!div class="nextstepaction"]
> [Kwerenda pojedynczego pliku CSV](sql/query-single-csv-file.md)
