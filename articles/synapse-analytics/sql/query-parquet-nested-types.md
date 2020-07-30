---
title: Parquet typów zagnieżdżonych zapytania przy użyciu języka SQL na żądanie (wersja zapoznawcza)
description: W tym artykule dowiesz się, jak wykonywać zapytania dotyczące zagnieżdżonych typów Parquet.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 60f2a1992750950b50995fcf36513e44e377004d
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386609"
---
# <a name="query-nested-types-in-parquet-and-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Zapytania zagnieżdżonych typów w plikach Parquet i JSON przy użyciu funkcji SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics

W tym artykule dowiesz się, jak napisać zapytanie przy użyciu języka SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics. To zapytanie będzie odczytywać Parquet typy zagnieżdżone.
Zagnieżdżone typy to złożone struktury, które reprezentują obiekty lub tablice. Zagnieżdżone typy mogą być przechowywane w: 
- [PARQUET](query-parquet-files.md) , w którym można mieć wiele kolumn złożonych zawierających tablice i obiekty.
- Hierarchiczne [pliki JSON](query-json-files.md) , które umożliwiają odczytywanie złożonych dokumentów JSON jako pojedynczej kolumny.
- Kolekcja CosmosDB, w której każdy dokument może zawierać złożone właściwości zagnieżdżone (obecnie w ramach warunkowej publicznej wersji zapoznawczej).

Synapse SQL na żądanie umożliwia formatowanie wszystkich zagnieżdżonych typów jako obiektów JSON i tablic, dzięki czemu można [wyodrębnić lub zmodyfikować obiekty złożone przy użyciu funkcji JSON](https://docs.microsoft.com/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) lub [przeanalizować dane JSON przy użyciu funkcji OPENJSON](https://docs.microsoft.com/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server). 

Przykładem zapytania, w którym są wyodrębniane wartości skalarne i obiekty z pliku JSON programu [Research DataSet typu COVID-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) z obiektami zagnieżdżonymi poniżej. 

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
> Ten przykład używa pliku z [COVID-19 Otwórz zestaw danych badań](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). Zobacz licencję tego i strukturę danych na tej stronie.

## <a name="prerequisites"></a>Wymagania wstępne

Pierwszym krokiem jest **utworzenie bazy danych** ze źródłem danych. Następnie zainicjuj obiekty, wykonując [skrypt Instalatora](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) w tej bazie danych. Ten skrypt instalacyjny spowoduje utworzenie źródeł danych, poświadczeń z zakresem bazy danych i zewnętrznych formatów plików, które są używane w tych przykładach.

## <a name="project-nested-or-repeated-data"></a>Dane zagnieżdżone lub powtórzone projektu

Plik PARQUET może zawierać wiele kolumn o typach złożonych. Wartości z tych kolumn są sformatowane jako tekst JSON i zwracane jako VARCHAR kolumny. Następujące zapytanie odczytuje plik *structExample. Parquet* i pokazuje, jak odczytać wartości kolumn zagnieżdżonych: 

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

To zapytanie zwróci następujący wynik, w przypadku którego zawartość każdego zagnieżdżonego obiektu jest zwracana jako tekst JSON:

| DateStruct    | TimeStruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Date": "2009-04-25"}| {"Time": "20:51:54.3598000"}|    {"Timestamp": "5501-04-08 12:13:57.4821000"}|    {"Decimal": 11143412.25350}| {"Float": 0,5}|
|{"Date": "1916-04-29"}| {"Time": "00:16:04.6778000"}|    {"Timestamp": "1990-06-30 20:50:52.6828000"}|    {"Decimal": 1963545.62800}|  {"Float":-2,125}|

Następujące zapytanie odczytuje plik *justSimpleArray. Parquet* . Projektuje wszystkie kolumny z pliku Parquet, w tym zagnieżdżone lub powtórzone dane.

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

`JSON_VALUE`funkcja umożliwia zwracanie wartości z kolumny sformatowanej jako tekst JSON:

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

W przeciwieństwie do plików JSON, które w większości przypadków zwracają pojedyncze kolumny zawierające złożony obiekt JSON. Pliki PARQUET mogą mieć wiele złożonych. Właściwości zagnieżdżonej kolumny można odczytać przy użyciu `JSON_VALUE` funkcji dla każdej kolumny. `OPENROWSET`umożliwia bezpośrednie Określanie ścieżek zagnieżdżonych właściwości w `WITH` klauzuli. Ścieżki można ustawić jako nazwę kolumny lub dodać [wyrażenie ścieżki JSON](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server) po typie kolumny.

Następujące zapytanie odczytuje plik *structExample. Parquet* i pokazuje, jak przedstawić elementy na wykresie zagnieżdżonej kolumny. Istnieją dwa sposoby odwoływania się do wartości zagnieżdżonej:
- Określanie wyrażenia zagnieżdżonej ścieżki wartości po specyfikacji typu.
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

Następujące zapytanie odczytuje plik *justSimpleArray. Parquet* i używa [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) do pobierania elementu **skalarnego** z wewnątrz powtórzonej kolumny, takiej jak tablica lub mapa:

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

Wynik jest przedstawiony w poniższej tabeli:

|SimpleArray    | Pierwszyelement  | Drugielement | Trzecielement |
| --- | --- | --- | --- |
| [11, 12, 13] | 11   | 12 | 13 |
| [21, 22, 23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>Dostęp do obiektów podrzędnych z kolumn złożonych

Następujące zapytanie odczytuje plik *mapExample. Parquet* i używa [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) do pobierania **nieskalarnego** elementu z wewnątrz powtórzonej kolumny, takiej jak tablica lub mapa:

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

Można również jawnie odwoływać się do kolumn, które mają być zwracane w `WITH` klauzuli:

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

Struktura `MapOfPersons` jest zwracana jako `VARCHAR` kolumna i sformatowana jako ciąg JSON.

## <a name="projecting-values-from-repeated-columns"></a>Projekcja wartości z powtórzonych kolumn

Jeśli masz tablicę wartości skalarnych (na przykład `[1,2,3]` ) w niektórych kolumnach, możesz łatwo ją rozwinąć i dołączyć do nich przy użyciu następującego skryptu:

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
