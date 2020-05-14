---
title: CETAS Synapse SQL
description: Używanie CETAS z Synapse SQL
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 3bf7ff668584a78fea6e2d787e96f36a20f12e37
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197631"
---
# <a name="cetas-with-synapse-sql"></a>CETAS z Synapse SQL

W puli SQL lub SQL na żądanie (wersja zapoznawcza) możesz użyć opcji Utwórz tabelę ZEWNĘTRZną jako SELECT (CETAS), aby wykonać następujące zadania:  

- Tworzenie tabeli zewnętrznej
- Eksportowanie, równolegle, wyniki instrukcji SELECT języka Transact-SQL do

  - Hadoop
  - Azure Storage Blob
  - Usługa Azure Data Lake Storage 2. generacji

## <a name="cetas-in-sql-pool"></a>CETAS w puli SQL

Dla puli SQL CETAS użycie i składnię, zaznacz artykuł [Tworzenie tabeli zewnętrznej jako SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) . Ponadto, aby uzyskać wskazówki dotyczące CTAS przy użyciu puli SQL, zobacz artykuł [CREATE TABLE jako SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) .

## <a name="cetas-in-sql-on-demand"></a>CETAS na żądanie SQL

W przypadku korzystania z zasobu SQL na żądanie CETAS jest używany do tworzenia tabeli zewnętrznej i eksportowania wyników zapytania do Azure Storage Blob lub Azure Data Lake Storage Gen2.

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

*[[ *database_name* . [ *schema_name* ]. ] | *schema_name* . ] *table_name**

Nazwa tabeli, która ma zostać utworzona. W przypadku tabeli zewnętrznej SQL na żądanie przechowuje tylko metadane tabeli. Żadne rzeczywiste dane nie są przenoszone ani przechowywane na żądanie SQL.

LOCATION = *"PATH_TO_FOLDER"*

Określa miejsce zapisania wyników instrukcji SELECT w zewnętrznym źródle danych. Folder główny to lokalizacja danych określona w zewnętrznym źródle danych. Lokalizacja musi wskazywać na folder i mieć końcowe/. Przykład: aggregated_data/

DATA_SOURCE = *external_data_source_name*

Określa nazwę obiektu zewnętrznego źródła danych, który zawiera lokalizację, w której będą przechowywane dane zewnętrzne. Aby utworzyć zewnętrzne źródło danych, użyj [instrukcji CREATE External Data Source (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source).

FILE_FORMAT = *external_file_format_name*

Określa nazwę obiektu zewnętrznego formatu pliku, który zawiera format zewnętrznego pliku danych. Aby utworzyć zewnętrzny format pliku, użyj [formatu Create External File Format (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format). Obecnie obsługiwane są tylko zewnętrzne formaty plików z atrybutem FORMAT = "PARQUET".

Z *<common_table_expression>*

Określa tymczasowy, nazwany zestaw wyników, znany jako wspólne wyrażenie tabeli (CTE). Aby uzyskać więcej informacji, zobacz temat [WITH common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Wybierz <select_criteria>

Wypełnia nową tabelę wynikami instrukcji SELECT. *select_criteria* jest treścią instrukcji SELECT, która określa, które dane mają zostać skopiowane do nowej tabeli. Aby uzyskać informacje na temat instrukcji SELECT, zobacz [SELECT (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

> [!NOTE]
> Klauzula ORDER BY w SELECT Part elementu CETAS nie jest obsługiwana.

## <a name="permissions"></a>Uprawnienia

Musisz mieć uprawnienia do wyświetlania zawartości folderu i zapisywania do folderu lokalizacji, aby CETAS działał.

## <a name="examples"></a>Przykłady

Te przykłady używają CETAS do zapisywania całkowitej populacji zagregowanej według roku i stanu do folderu aggregated_data, który znajduje się w population_ds DataSource.

Ten przykład opiera się na utworzonym wcześniej formacie, źródle danych i pliku zewnętrznym. Zapoznaj się z dokumentem [tabele zewnętrzne](develop-tables-external-tables.md) . Aby zapisać wyniki zapytania do innego folderu w tym samym źródle danych, Zmień argument lokalizacji. Aby zapisać wyniki na innym koncie magazynu, Utwórz i użyj innego źródła danych dla argumentu DATA_SOURCE.

> [!NOTE]
> Poniższe przykłady używają publicznego konta usługi Azure Data Storage. Jest on tylko do odczytu. Aby wykonać te zapytania, należy podać źródło danych, dla którego masz uprawnienia do zapisu.

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

W poniższym przykładzie jest stosowana tabela zewnętrzna jako źródło dla CETAS. Opiera się on na poświadczeniach, źródle danych, zewnętrznym formacie pliku i utworzonej wcześniej tabeli zewnętrznej. Zapoznaj się z dokumentem [tabele zewnętrzne](develop-tables-external-tables.md) .

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

CETAS może służyć do przechowywania zestawów wyników przy użyciu następujących typów danych SQL:

- binarny
- varbinary
- char
- varchar
- data
- time
- datetime2
- decimal
- numeryczne
- float
- liczba rzeczywista
- bigint
- int
- smallint
- tinyint
- bit

LOB nie można używać z CETAS.

Następujące typy danych nie mogą być używane w SELECT część elementu CETAS:

- nchar
- nvarchar
- datetime
- smalldatetime
- datetimeoffset
- pieniędzy
- smallmoney
- uniqueidentifier

## <a name="next-steps"></a>Następne kroki

Można spróbować wykonać zapytanie dotyczące [tabel platformy Spark](develop-storage-files-spark-tables.md).
