---
title: Wykonywanie zapytań dotyczących plików JSON przy użyciu puli SQL bezserwerowej
description: W tej sekcji opisano sposób odczytywania plików JSON przy użyciu bezserwerowej puli SQL w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 5fcf688bbe8a5be2fc10b70950990b7b6ca71df8
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225595"
---
# <a name="query-json-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Wykonywanie zapytań dotyczących plików JSON przy użyciu bezserwerowej puli SQL w usłudze Azure Synapse Analytics

W tym artykule dowiesz się, jak napisać zapytanie przy użyciu bezserwerowej puli SQL w usłudze Azure Synapse Analytics. Celem zapytania jest odczytywanie plików JSON przy użyciu funkcji [OPENROWSET](develop-openrowset.md). 
- Standardowe pliki JSON, w których wiele dokumentów JSON jest przechowywanych jako tablica JSON.
- Rozdzielane wierszami pliki JSON, w których dokumenty JSON są rozdzielone znakami nowego wiersza. Typowymi rozszerzeniami tych typów plików są `jsonl` , `ldjson` , i `ndjson` .

## <a name="read-json-documents"></a>Odczytaj dokumenty JSON

Najprostszym sposobem, aby zobaczyć zawartość pliku JSON, jest podanie adresu URL pliku do `OPENROWSET` funkcji, określenie woluminu CSV `FORMAT` i ustawienie wartości `0x0b` dla `fieldterminator` i `fieldquote` . Jeśli musisz odczytywać pliki JSON rozdzielane wierszami, to jest to wystarczające. Jeśli masz klasyczny plik JSON, musisz ustawić wartości `0x0b` dla `rowterminator` . `OPENROWSET` funkcja będzie analizować kod JSON i zwracać każdy dokument w następującym formacie:

| usługę |
| --- |
|{"date_rep": "2020-07-24", "Day": 24, "Month": 7, "Year": 2020, "Cases": 3, "śmierć": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-25", "Day": 25, "Month": 7, "Year": 2020, "Cases": 7, "śmierć": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-26", "Day": 26, "Month": 7, "Year": 2020, "Cases": 4, "śmierć": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-27", "Day": 27, "Month": 7, "Year": 2020, "Cases": 8, "śmierć": 0, "geo_id": "AF"}|

Jeśli plik jest publicznie dostępny lub tożsamość usługi Azure AD może uzyskać do niego dostęp, zawartość pliku powinna zostać wyświetlona przy użyciu zapytania, jak pokazano w poniższych przykładach.

### <a name="read-json-files"></a>Odczytaj pliki JSON

Następujące przykładowe zapytanie odczytuje pliki JSON i rozdzielone wierszami, a także zwraca wszystkie dokumenty jako osobny wiersz.

```sql
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

Dokument JSON w poprzednim przykładzie zapytania zawiera tablicę obiektów. Zapytanie zwraca każdy obiekt jako osobny wiersz w zestawie wyników. Upewnij się, że możesz uzyskać dostęp do tego pliku. Jeśli plik jest chroniony za pomocą klucza SAS lub tożsamości niestandardowej, należy skonfigurować [poświadczenia na poziomie serwera dla logowania SQL](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential). 

### <a name="data-source-usage"></a>Użycie źródła danych

Poprzedni przykład używa pełnej ścieżki do pliku. Alternatywnie można utworzyć zewnętrzne źródło danych z lokalizacją wskazującą folder główny magazynu, a następnie użyć tego źródła danych i ścieżki względnej do pliku w `OPENROWSET` funkcji:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.json',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

Jeśli źródło danych jest chronione za pomocą klucza SAS lub tożsamości niestandardowej, można skonfigurować [Źródło danych z poświadczeniami o zakresie bazy danych](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

W poniższych sekcjach można zobaczyć, jak wykonywać zapytania dotyczące różnych typów plików JSON.

## <a name="parse-json-documents"></a>Analizowanie dokumentów JSON

Zapytania w poprzednich przykładach zwracają każdy dokument JSON jako pojedynczy ciąg w osobnym wierszu zestawu wyników. Możesz użyć funkcji `JSON_VALUE` i, `OPENJSON` Aby przeanalizować wartości w dokumentach JSON i zwrócić je jako wartości relacyjne, tak jak pokazano w następującym przykładzie:

| Data \_ przedstawiciela | padkach | \_Identyfikator Geo |
| --- | --- | --- |
| 2020-07-24 | 3 | AF |
| 2020-07-25 | 7 | AF |
| 2020-07-26 | 4 | AF |
| 2020-07-27 | 8| AF |

### <a name="sample-json-document"></a>Przykładowy dokument JSON

Przykłady zapytań odczytują pliki *JSON* zawierające dokumenty o następującej strukturze:

```json
{
    "date_rep":"2020-07-24",
    "day":24,"month":7,"year":2020,
    "cases":13,"deaths":0,
    "countries_and_territories":"Afghanistan",
    "geo_id":"AF",
    "country_territory_code":"AFG",
    "continent_exp":"Asia",
    "load_date":"2020-07-25 00:05:14",
    "iso_country":"AF"
}
```

> [!NOTE]
> Jeśli te dokumenty są przechowywane jako dane JSON rozdzielane wierszami, należy ustawić `FIELDTERMINATOR` i `FIELDQUOTE` 0x0B. Jeśli masz standardowy format JSON, musisz ustawić `ROWTERMINATOR` 0x0B.

### <a name="query-json-files-using-json_value"></a>Wykonywanie zapytań dotyczących plików JSON przy użyciu JSON_VALUE

Poniższe zapytanie pokazuje, jak używać [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) do pobierania wartości skalarnych ( `date_rep` , `countries_and_territories` , `cases` ) z dokumentów JSON:

```sql
select
    JSON_VALUE(doc, '$.date_rep') AS date_reported,
    JSON_VALUE(doc, '$.countries_and_territories') AS country,
    CAST(JSON_VALUE(doc, '$.deaths') AS INT) as fatal,
    JSON_VALUE(doc, '$.cases') as cases,
    doc
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
order by JSON_VALUE(doc, '$.geo_id') desc
```

Po wyodrębnieniu właściwości JSON z dokumentu JSON można zdefiniować aliasy kolumn i opcjonalnie rzutować wartość tekstową na typ.

### <a name="query-json-files-using-openjson"></a>Wykonywanie zapytań dotyczących plików JSON przy użyciu OPENJSON

Następujące zapytanie używa [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?view=azure-sqldw-latest&preserve-view=true). Spowoduje to pobranie statystyk COVID zgłoszonych w Serbii:

```sql
select
    *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
    cross apply openjson (doc)
        with (  date_rep datetime2,
                cases int,
                fatal int '$.deaths',
                country varchar(100) '$.countries_and_territories')
where country = 'Serbia'
order by country, date_rep desc;
```
Wyniki są funkcjonalnie takie same, jak wyniki zwrócone przy użyciu `JSON_VALUE` funkcji. W niektórych przypadkach `OPENJSON` może to mieć zalety `JSON_VALUE` :
- W `WITH` klauzuli można jawnie ustawić aliasy kolumn i typy dla każdej właściwości. Nie trzeba umieszczać `CAST` funkcji w każdej kolumnie na `SELECT` liście.
- `OPENJSON` może być szybsze, jeśli zwracasz dużą liczbę właściwości. Jeśli zwracasz tylko 1-2 właściwości, `OPENJSON` Funkcja może być narzutem.
- Należy użyć funkcji, `OPENJSON` Jeśli konieczne jest przeanalizowanie tablicy z każdego dokumentu i dołączenie go z wierszem nadrzędnym.

## <a name="next-steps"></a>Następne kroki

W następnych artykułach w tej serii pokazano, jak:

- [Wykonywanie zapytań dotyczących folderów i wielu plików](query-folders-multiple-csv-files.md)
- [Tworzenie widoków i korzystanie z nich](create-use-views.md)
