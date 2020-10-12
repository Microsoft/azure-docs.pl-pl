---
title: Parquet typów zagnieżdżonych zapytania przy użyciu języka SQL na żądanie (wersja zapoznawcza)
description: W tym artykule dowiesz się, jak wykonywać zapytania dotyczące Parquet typów zagnieżdżonych za pomocą SQL na żądanie (wersja zapoznawcza).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 08502704515c791bf63f4803b7446a0471c0a869
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91288260"
---
# <a name="query-nested-types-in-parquet-and-json-files-by-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Zapytania zagnieżdżonych typów w plikach Parquet i JSON przy użyciu funkcji SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics

W tym artykule dowiesz się, jak napisać zapytanie przy użyciu funkcji SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics. Zapytanie będzie odczytywać Parquet typy zagnieżdżone.
Zagnieżdżone typy to złożone struktury, które reprezentują obiekty lub tablice. Zagnieżdżone typy mogą być przechowywane w: 
- [Parquet](query-parquet-files.md), w którym można mieć wiele kolumn złożonych zawierających tablice i obiekty.
- Hierarchiczne [pliki JSON](query-json-files.md), które umożliwiają odczytywanie złożonego dokumentu JSON jako pojedynczej kolumny.
- Kolekcje Azure Cosmos DB (obecnie w publicznej wersji zapoznawczej), gdzie każdy dokument może zawierać złożone właściwości zagnieżdżone.

Usługa Azure Synapse SQL na żądanie umożliwia formatowanie wszystkich zagnieżdżonych typów jako obiektów JSON i tablic. Dzięki temu można [wyodrębnić lub zmodyfikować obiekty złożone przy użyciu funkcji JSON](https://docs.microsoft.com/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) lub [przeanalizować dane JSON przy użyciu funkcji OPENJSON](https://docs.microsoft.com/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server). 

Oto przykład zapytania, które wyodrębnia wartość skalarną i wartości obiektów z pliku JSON [Open Research DataSet COVID-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) , który zawiera obiekty zagnieżdżone: 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

`JSON_VALUE`Funkcja zwraca wartość skalarną z pola w określonej ścieżce. `JSON_QUERY`Funkcja zwraca obiekt sformatowany w formacie JSON z pola pod określoną ścieżką.

> [!IMPORTANT]
> Ten przykład używa pliku z otwartego zestawu danych badania COVID-19. [Zobacz tutaj licencję i strukturę danych](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/).

## <a name="prerequisites"></a>Wymagania wstępne

Pierwszym krokiem jest utworzenie bazy danych, w której zostanie utworzone źródło danych. Następnie można zainicjować obiekty, uruchamiając [skrypt instalacyjny](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) w bazie danych programu. Skrypt Instalatora utworzy źródła danych, poświadczenia w zakresie bazy danych i zewnętrzne formaty plików, które są używane w przykładach.

## <a name="project-nested-or-repeated-data"></a>Dane zagnieżdżone lub powtórzone projektu

Plik Parquet może mieć wiele kolumn o typach złożonych. Wartości z tych kolumn są sformatowane jako tekst JSON i zwracane jako VARCHAR kolumny. Następujące zapytanie odczytuje plik structExample. Parquet i pokazuje, jak odczytać wartości kolumn zagnieżdżonych: 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

To zapytanie zwraca następujący wynik. Zawartość każdego zagnieżdżonego obiektu jest zwracana jako tekst JSON.

| DateStruct    | TimeStruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Date": "2009-04-25"}| {"Time": "20:51:54.3598000"}|    {"Timestamp": "5501-04-08 12:13:57.4821000"}|    {"Decimal": 11143412.25350}| {"Float": 0,5}|
|{"Date": "1916-04-29"}| {"Time": "00:16:04.6778000"}|    {"Timestamp": "1990-06-30 20:50:52.6828000"}|    {"Decimal": 1963545.62800}|  {"Float":-2,125}|

Następujące zapytanie odczytuje plik justSimpleArray. Parquet. Projektuje wszystkie kolumny z pliku Parquet, w tym zagnieżdżone i powtórzone dane.

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

To zapytanie zwróci następujący wynik:

|SimpleArray|
| --- |
|[11, 12, 13]|
|[21, 22, 23]|

## <a name="read-properties-from-nested-object-columns"></a>Odczytaj właściwości z zagnieżdżonych kolumn obiektów

`JSON_VALUE`Funkcja umożliwia zwracanie wartości z kolumn sformatowanych jako tekst JSON:

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

Wynik jest przedstawiony w poniższej tabeli:

|title  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| Dodatkowe informacje o epidemioloach ekonomicznych... | Julien   | -Ilustracja S1: Phylogeny... | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

W przeciwieństwie do plików JSON, które w większości przypadków zwracają pojedynczą kolumnę zawierającą złożony obiekt JSON, pliki Parquet mogą mieć wiele kolumn złożonych. Właściwości zagnieżdżonych kolumn można odczytać przy użyciu `JSON_VALUE` funkcji dla każdej kolumny. `OPENROWSET` umożliwia bezpośrednie Określanie ścieżek zagnieżdżonych właściwości w `WITH` klauzuli. Można ustawić ścieżki jako nazwę kolumny lub dodać [wyrażenie ścieżki JSON](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server) po typie kolumny.

Następujące zapytanie odczytuje plik structExample. Parquet i pokazuje, jak przedstawić elementy na wykresie zagnieżdżonej kolumny. Istnieją dwa sposoby odwoływania się do zagnieżdżonej wartości:
- Określając wyrażenie zagnieżdżonej ścieżki wartości po specyfikacji typu.
- Formatowanie nazwy kolumny jako ścieżki zagnieżdżonej przy użyciu do ".", aby odwołać się do pól.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Dostęp do elementów z powtórzonych kolumn

Następujące zapytanie odczytuje plik justSimpleArray. Parquet i używa [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) do pobierania elementu skalarnego z wewnątrz powtórzonej kolumny, takiej jak tablica lub mapa:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Oto wynik:

|SimpleArray    | Pierwszyelement  | Drugielement | Trzecielement |
| --- | --- | --- | --- |
| [11, 12, 13] | 11   | 12 | 13 |
| [21, 22, 23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>Dostęp do obiektów podrzędnych z kolumn złożonych

Następujące zapytanie odczytuje plik mapExample. Parquet i używa [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) do pobierania nieskalarnego elementu z wewnątrz powtórzonej kolumny, takiej jak tablica lub mapa:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Można również jawnie odwoływać się do kolumn, które mają zostać zwrócone w `WITH` klauzuli:

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

Struktura `MapOfPersons` jest zwracana jako kolumna varchar i sformatowana jako ciąg JSON.

## <a name="project-values-from-repeated-columns"></a>Wartości projektu z powtórzonych kolumn

Jeśli masz tablicę wartości skalarnych (na przykład `[1,2,3]` ) w niektórych kolumnach, możesz łatwo ją rozwinąć i dołączyć do nich w wierszu głównym przy użyciu tego skryptu:

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>Następne kroki

W następnym artykule przedstawiono sposób [wykonywania zapytań dotyczących plików JSON](query-json-files.md).
