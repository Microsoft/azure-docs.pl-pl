---
title: CETAS w synapse SQL
description: Korzystanie z programu CETAS z programem Synapse SQL
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 71bc20680467d270436e28190bb49db5b9313ca0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424027"
---
# <a name="cetas-with-synapse-sql"></a>CETAS z synapsem SQL

W puli SQL lub SQL na żądanie (wersja zapoznawcza) można użyć create external table as select (CETAS) do wykonywania następujących zadań:  

- Tworzenie tabeli zewnętrznej
- Eksportuj równolegle wyniki instrukcji Transact-SQL SELECT do

  - Hadoop
  - Azure Storage Blob
  - Usługa Azure Data Lake Storage 2. generacji

## <a name="cetas-in-sql-pool"></a>CETAS w puli SQL

W przypadku puli SQL, użycia i składni CETAS, sprawdź artykuł [CREATE EXTERNAL TABLE AS SELECT.](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Ponadto wskazówki dotyczące CTAS przy użyciu puli SQL, zobacz [TWORZENIE TABELI JAKO WYBIERZ](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) artykułu.

## <a name="cetas-in-sql-on-demand"></a>CETAS w języku SQL na żądanie

Podczas korzystania z zasobu SQL na żądanie, CETAS jest używany do tworzenia tabeli zewnętrznej i eksportowania wyników kwerend do obiektu Blob usługi Azure Storage lub usługi Azure Data Lake Storage Gen2.

## <a name="syntax"></a>Składnia

```syntaxsql
CREATE EXTERNAL TABLE [ [database_name  . [ schema_name ] . ] | schema_name . ] table_name
    WITH (
        LOCATION = 'path_to_folder',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
)
    AS <select_statement>  
[;]

<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>
```

## <a name="arguments"></a>Argumenty

*[ *[ database_name* . [ *schema_name* ] . ] | *schema_name* . ] *table_name**

Od jednej do trzech części nazwa tabeli do utworzenia. W przypadku tabeli zewnętrznej sql na żądanie przechowuje tylko metadane tabeli. Żadne rzeczywiste dane nie są przenoszone ani przechowywane w języku SQL na żądanie.

LOKALIZACJA = *'path_to_folder'*

Określa, gdzie należy zapisać wyniki instrukcji SELECT w zewnętrznym źródle danych. Folder główny to lokalizacja danych określona w zewnętrznym źródle danych. LOKALIZACJA musi wskazywać folder i mieć końcowe /. Przykład: aggregated_data/

DATA_SOURCE = *external_data_source_name*

Określa nazwę zewnętrznego obiektu źródła danych zawierającego lokalizację, w której będą przechowywane dane zewnętrzne. Aby utworzyć zewnętrzne źródło danych, użyj [programu CREATE EXTERNAL DATA SOURCE (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source).

FILE_FORMAT = *external_file_format_name*

Określa nazwę obiektu formatu pliku zewnętrznego zawierającego format pliku danych zewnętrznych. Aby utworzyć format pliku zewnętrznego, użyj programu [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format). Obecnie obsługiwane są tylko zewnętrzne formaty plików z formatem FORMAT='PARKIET".

Z *>common_table_expression<*

Określa tymczasowy nazwany zestaw wyników, znany jako wyrażenie wspólnej tabeli (CTE). Aby uzyskać więcej informacji, zobacz [with common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

WYBIERZ select_criteria <>

Wypełnia nową tabelę wynikami z instrukcji SELECT. *select_criteria* jest treść select instrukcji, która określa, które dane do skopiowania do nowej tabeli. Aby uzyskać informacje o instrukcjach SELECT, zobacz [SELECT (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="permissions"></a>Uprawnienia

Musisz mieć uprawnienia do wyświetlania zawartości folderu i zapisu w folderze LOKALIZACJA, aby CETAS działał.

## <a name="examples"></a>Przykłady

W tych przykładach użyto CETAS do zapisania całkowitej liczby ludności zagregowanej według roku i stanu w folderze aggregated_data, który znajduje się w population_ds źródle danych.

Ten przykład opiera się na poświadczeń, źródło danych i format pliku zewnętrznego utworzone wcześniej. Zapoznaj się z dokumentem [tabel zewnętrznych.](develop-tables-external-tables.md) Aby zapisać wyniki kwerendy w innym folderze w tym samym źródle danych, zmień argument LOKALIZACJA. Aby zapisać wyniki na innym koncie magazynu, utwórz i użyj innego źródła danych dla DATA_SOURCE argumentu.

> [!NOTE]
> Przykłady, które należy wykonać użyć publicznego konta magazynu usługi Azure Open Data. Jest tylko do odczytu. Aby wykonać te kwerendy, należy podać źródło danych, dla których masz uprawnienia do zapisu.

```sql
-- use CETAS to export select statement with OPENROWSET result to  storage
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM
    OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=*/*.parquet',
    FORMAT='PARQUET') AS [r]
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

Poniższa próbka używa tabeli zewnętrznej jako źródła CETAS. Opiera się na poświadczenia, źródło danych, format pliku zewnętrznego i tabeli zewnętrznej utworzone wcześniej. Zapoznaj się z dokumentem [tabel zewnętrznych.](develop-tables-external-tables.md)

```sql
-- use CETAS with select from external table
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM census_external_table
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>Obsługiwane typy danych

CETAS może służyć do przechowywania zestawów wyników z następującymi typami danych SQL:

- binarny
- varbinary
- char
- varchar
- date
- time
- datetime2
- decimal
- numeryczne
- float
- rzeczywiste
- bigint
- int
- smallint
- tinyint
- bit

LoBs nie mogą być używane z CETAS.

Następujące typy danych nie mogą być używane w select części CETAS:

- nchar
- nvarchar
- datetime
- smalldatetime
- datetimeoffset
- pieniędzy
- smallmoney
- uniqueidentifier

## <a name="next-steps"></a>Następne kroki

Możesz spróbować wykonać kwerendę w [tabelach platformy Spark](develop-storage-files-spark-tables.md).
