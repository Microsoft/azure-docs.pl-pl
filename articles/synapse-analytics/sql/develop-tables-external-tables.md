---
title: Używanie tabel zewnętrznych z programem Synapse SQL
description: Odczytywanie lub zapisywanie plików danych za pomocą synapse SQL
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 4d13d15fe950c89687acfca355d4ed183756536a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423978"
---
# <a name="use-external-tables-with-synapse-sql"></a>Używanie tabel zewnętrznych z programem Synapse SQL

Tabela zewnętrzna wskazuje dane znajdujące się w witrynie Hadoop, Azure Storage lub Azure Data Lake Storage. Tabele zewnętrzne są używane do odczytywania danych z plików lub zapisywania danych do plików w usłudze Azure Storage. Za pomocą synapse SQL można używać tabel zewnętrznych do odczytu i zapisu danych do puli SQL lub SQL na żądanie (wersja zapoznawcza).

## <a name="external-tables-in-sql-pool"></a>Tabele zewnętrzne w puli SQL

W puli SQL można użyć tabeli zewnętrznej, aby:

- Zapytanie usługi Azure Blob Storage i usługi Azure Data Lake Gen2 za pomocą instrukcji Transact-SQL.
- Importowanie i przechowywanie danych z usługi Azure Blob Storage i usługi Azure Data Lake Storage do puli SQL.

W połączeniu z instrukcją [CREATE TABLE AS SELECT](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) wybranie z tabeli zewnętrznej powoduje zaimportowanie danych do tabeli w puli SQL. Oprócz instrukcji [COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)tabele zewnętrzne są przydatne do ładowania danych. Aby zapoznać się z samouczkiem ładowania, zobacz [PolyBase do ładowania danych z usługi Azure Blob Storage](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="external-tables-in-sql-on-demand-preview"></a>Tabele zewnętrzne w języku SQL na żądanie (wersja zapoznawcza)

W przypadku programu SQL na żądanie użyjesz tabeli zewnętrznej do:

- Zapytanie o dane w usłudze Azure Blob Storage lub usłudze Azure Data Lake Storage za pomocą instrukcji Transact-SQL
- Przechowuj wyniki zapytań SQL na żądanie w plikach w usłudze Azure Blob Storage lub usłudze Azure Data Lake Storage przy użyciu [programu CETAS.](develop-tables-cetas.md)

Tabele zewnętrzne można tworzyć przy użyciu języka SQL na żądanie, wykonując następujące kroki:

1. TWORZENIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH
2. CREATE EXTERNAL FILE FORMAT
3. TWORZENIE TABELI ZEWNĘTRZNEJ

## <a name="create-external-data-source"></a>TWORZENIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH

Zewnętrzne źródła danych są używane do łączenia się z kontami magazynu. Pełna dokumentacja jest opisana [tutaj](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="syntax-for-create-external-data-source"></a>Składnia tworzenia zewnętrznego źródła danych

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>' )
[;]
```

## <a name="arguments-for-create-external-data-source"></a>Argumenty utworzenia zewnętrznego źródła danych

data_source_name -Określa nazwę zdefiniowaną przez użytkownika dla źródła danych. Nazwa musi być unikatowa w bazie danych.

LOKALIZACJA `'<prefix>://<path>'` = — udostępnia protokół łączności i ścieżkę do zewnętrznego źródła danych. Ścieżka może zawierać kontener w `'<prefix>://<path>/container'`postaci , i folder `'<prefix>://<path>/container/folder'`w postaci .

| Zewnętrzne źródło danych        | Prefiks lokalizacji | Ścieżka lokalizacji                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Blob Storage          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Usługa Azure Data Lake Store Gen 1 | `adl`           | `<storage_account>.azuredatalake.net`                 |
| Usługa Azure Data Lake Store Gen 2 | `abfs[s]`       | `<container>@<storage_account>.dfs.core.windows.net`  |

## <a name="example-for-create-external-data-source"></a>Przykład tworzenia zewnętrznego źródła danych

Poniższy przykład tworzy zewnętrzne źródło danych dla usługi Azure Data Lake Gen2 wskazując zestaw danych w Nowym Jorku:

```sql
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://newyorktaxidataset.azuredatalakestore.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Tworzy obiekt w formacie pliku zewnętrznego, który definiuje dane zewnętrzne przechowywane w usłudze Azure Blob Storage lub usłudze Azure Data Lake Storage. Utworzenie formatu pliku zewnętrznego jest warunkiem wstępnym utworzenia tabeli zewnętrznej. Pełna dokumentacja jest [tutaj](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Tworząc format pliku zewnętrznego, należy określić rzeczywisty układ danych, do których odwołuje się tabela zewnętrzna.

## <a name="syntax-for-create-external-file-format"></a>Składnia tworzenia zewnętrznego formatu pliku

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
}
```

## <a name="arguments-for-create-external-file-format"></a>Argumenty dotyczące tworzenia zewnętrznego formatu pliku

file_format_name- Określa nazwę formatu pliku zewnętrznego.

FORMAT_TYPE = [ PARKIET | DELIMITEDTEXT]- Określa format danych zewnętrznych.

- PARKIET - Określa format parkietu.
- DELIMITEDTEXT - Określa format tekstu z ogranicznikami kolumn, nazywanymi również terminatorami pól.

FIELD_TERMINATOR = *field_terminator* — dotyczy tylko rozdzielanych plików tekstowych. Terminator pól określa jeden lub więcej znaków oznaczania końca każdego pola (kolumny) w pliku rozdzielanym tekstem. Domyślnie jest to znak potoku (|- ).

Przykłady:

- FIELD_TERMINATOR = '|'
- FIELD_TERMINATOR = '
- FIELD_TERMINATOR = \t

STRING_DELIMITER = *string_delimiter* — określa terminator pól dla danych ciągu tekstowego w pliku rozdzielanym tekstem. Ogranicznik ciągu ma jedną lub więcej znaków długości i jest łączony z pojedynczymi cudzysłowami. Domyślnie jest to pusty ciąg ("").

Przykłady:

- STRING_DELIMITER = '"'
- STRING_DELIMITER = '*'
- STRING_DELIMITER =

FIRST_ROW = *First_row_int* — określa numer wiersza, który jest odczytywany jako pierwszy i dotyczy wszystkich plików. Ustawienie wartości na dwie powoduje, że pierwszy wiersz w każdym pliku (wierszu nagłówka) ma zostać pominięty po załadowaniu danych. Wiersze są pomijane na podstawie istnienia terminatorów wierszy (/r/n, /r, /n).

USE_TYPE_DEFAULT = { PRAWDA | **FAŁSZ** } - Określa sposób obsługi brakujących wartości w rozdzielonych plikach tekstowych podczas pobierania danych z pliku tekstowego.

PRAWDA - Jeśli pobierasz dane z pliku tekstowego, przechowuj każdą brakującą wartość przy użyciu domyślnego typu danych wartości dla odpowiedniej kolumny w definicji tabeli zewnętrznej. Na przykład zastąp brakującą wartość:

- 0, jeśli kolumna jest zdefiniowana jako kolumna liczbowa. Kolumny dziesiętne nie są obsługiwane i powodują błąd.
- Pusty ciąg (""), jeśli kolumna jest kolumną ciągu.
- 1900-01-01, jeśli kolumna jest kolumną daty.

FAŁSZ — przechowuj wszystkie brakujące wartości jako NULL. Wszystkie wartości NULL, które są przechowywane przy użyciu słowa NULL w rozdzielanym pliku tekstowym są importowane jako ciąg "NULL".

Kodowanie = {'UTF8' | 'UTF16'} — SQL on-demand może odczytywać rozdzielane pliki tekstowe UTF8 i UTF16.

DATA_COMPRESSION = *data_compression_method* — ten argument określa metodę kompresji danych dla danych zewnętrznych. Podczas odczytu z tabel zewnętrznych jest ignorowany. Jest on używany tylko podczas pisania do tabel zewnętrznych przy użyciu [CETAS](develop-tables-cetas.md).

Typ formatu pliku PARKIET OBSŁUGUJE NASTĘPUJĄCE metody kompresji:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

## <a name="example-for-create-external-file-format"></a>Przykład tworzenia zewnętrznego formatu pliku

Poniższy przykład tworzy format pliku zewnętrznego dla plików spisowych:

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>TWORZENIE TABELI ZEWNĘTRZNEJ

Polecenie UTWÓRZ TABELĘ ZEWNĘTRZNĄ tworzy tabelę zewnętrzną dla synapse SQL, aby uzyskać dostęp do danych przechowywanych w usłudze Azure Blob Storage lub usłudze Azure Data Lake Storage. 

## <a name="syntax-for-create-external-table"></a>Składnia TWORZENIA TABELI ZEWNĘTRZNEJ

```syntaxsql
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )  
    WITH (
        LOCATION = 'folder_or_filepath',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name
    )  
[;]  

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]
```

## <a name="arguments-create-external-table"></a>Argumenty TWORZENIE TABELI ZEWNĘTRZNEJ

*{ database_name.schema_name.table_name | schema_name.table_name | table_name }*

Od jednej do trzech części nazwa tabeli do utworzenia. W przypadku tabeli zewnętrznej sql na żądanie przechowuje tylko metadane tabeli. Żadne rzeczywiste dane nie są przenoszone ani przechowywane w języku SQL na żądanie.

<column_definition>, ... *n* ]

TWORZENIE TABELI ZEWNĘTRZNEJ obsługuje możliwość konfigurowania nazwy kolumny, typu danych, możliwości zerowej i sortowania. Nie można użyć OGRANICZENIA DOMYŚLNEGO w tabelach zewnętrznych.

>[!IMPORTANT]
>Definicje kolumn, w tym typy danych i liczby kolumn, muszą być zgodne z danymi w plikach zewnętrznych. W przypadku niezgodności wiersze pliku zostaną odrzucone podczas wykonywania zapytania na danych rzeczywistych.

Podczas odczytywania z plików parkietu można określić tylko kolumny, które mają być odczytywane, a resztę pominąć.

LOKALIZACJA = '*folder_or_filepath*'

Określa folder lub ścieżkę pliku i nazwę pliku dla rzeczywistych danych w usłudze Azure Blob Storage. Lokalizacja rozpoczyna się od folderu głównego. Folder główny to lokalizacja danych określona w zewnętrznym źródle danych.

Jeśli określisz folder LOKALIZACJA, kwerenda SQL na żądanie wybierze z tabeli zewnętrznej i pobierze pliki z folderu.

> [!NOTE]
> W przeciwieństwie do Hadoop i PolyBase, SQL na żądanie nie zwraca podfolderów. Zwraca pliki, dla których nazwa pliku zaczyna się od podkreślenia (_) lub kropki (.).

W tym przykładzie, jeśli LOCATION='/webdata/', kwerenda SQL na żądanie zwróci wiersze z pliku mydata.txt i _hidden.txt. Nie zwróci mydata2.txt i mydata3.txt, ponieważ znajdują się w podfolderze.

![Dane cykliczne dla tabel zewnętrznych](./media/develop-tables-external-tables/folder-traversal.png)

DATA_SOURCE = *external_data_source_name* — określa nazwę zewnętrznego źródła danych zawierającego lokalizację danych zewnętrznych. Aby utworzyć zewnętrzne źródło danych, użyj funkcji [UTWÓRZ ZEWNĘTRZNE ŹRÓDŁO DANYCH](#create-external-data-source).

FILE_FORMAT = *external_file_format_name* — określa nazwę obiektu formatu pliku zewnętrznego, który przechowuje typ pliku i metodę kompresji danych zewnętrznych. Aby utworzyć format pliku zewnętrznego, użyj funkcji [CREATE EXTERNAL FILE FORMAT](#create-external-file-format).

## <a name="permissions-create-external-table"></a>Uprawnienia TWORZENIE TABELI ZEWNĘTRZNEJ

Aby wybrać z tabeli zewnętrznej, należy odpowiednie poświadczenia z listy i uprawnienia do odczytu.

## <a name="example-create-external-table"></a>Przykład TWORZENIE TABELI ZEWNĘTRZNEJ

Poniższy przykład tworzy tabelę zewnętrzną. Zwraca pierwszy wiersz:

```sql
CREATE EXTERNAL TABLE census_external_table
(
    decennialTime varchar(20),
    stateName varchar(100),
    countyName varchar(100),
    population int,
    race varchar(50),
    sex    varchar(10),
    minAge int,
    maxAge int
)  
WITH (
    LOCATION = '/parquet/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)
GO

SELECT TOP 1 * FROM census_external_table
```

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Tworzenie i wykonywanie zapytań tabel zewnętrznych z pliku w usłudze Azure Data Lake

Za pomocą funkcji eksploracji usługi Data Lake można teraz tworzyć i wysyłać zapytania do tabeli zewnętrznej przy użyciu puli SQL lub SQL na żądanie za pomocą prostego kliknięcia prawym przyciskiem myszy pliku.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć dostęp do obszaru roboczego z co najmniej roli ARM Access współautora danych magazynu do konta ADLS Gen2

- Musisz mieć co najmniej [uprawnienia do tworzenia](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#permissions-2) i wykonywania zapytań tabel zewnętrznych w puli SQL lub OD SQL

- Usługa połączona skojarzona z kontem ADLS Gen2 **musi mieć dostęp do pliku**. Na przykład jeśli mechanizm uwierzytelniania usługi połączonej jest tożsamość zarządzana, tożsamość zarządzana obszaru roboczego musi mieć co najmniej uprawnienie czytnika obiektów blob magazynu na koncie magazynu

W panelu Dane wybierz plik, z którego chcesz utworzyć tabelę zewnętrzną:
> [!div class="mx-imgBorder"]
>![zewnętrzną1](./media/develop-tables-external-tables/external-table-1.png)

Otworzy się okno dialogowe. Wybierz pulę SQL lub SQL na żądanie, nadaj nazwę tabeli i wybierz otwarty skrypt:

> [!div class="mx-imgBorder"]
>![zdatna do zewnętrznej 2](./media/develop-tables-external-tables/external-table-2.png)

Skrypt SQL jest generowany automatycznie, wywnioskowanie schematu z pliku:
> [!div class="mx-imgBorder"]
>![zdatna do zewnętrznej 3](./media/develop-tables-external-tables/external-table-3.png)

Uruchom skrypt. Skrypt automatycznie uruchomi wybierz top 100 *.:
> [!div class="mx-imgBorder"]
>![zewnętrzną zdatną do pracy4](./media/develop-tables-external-tables/external-table-4.png)

Tabela zewnętrzna jest teraz tworzony, dla przyszłej eksploracji zawartości tej tabeli zewnętrznej użytkownik może zbadać go bezpośrednio z okienka dane:
> [!div class="mx-imgBorder"]
>![zewnętrzną5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem [CETAS,](develop-tables-cetas.md) jak zapisać wyniki kwerendy w tabeli zewnętrznej w usłudze Azure Storage. Możesz też rozpocząć wykonywanie zapytań o [tabele Platformy Spark](develop-storage-files-spark-tables.md).
