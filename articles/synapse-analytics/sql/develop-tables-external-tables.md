---
title: Używanie tabel zewnętrznych z Synapse SQL
description: Odczytywanie lub zapisywanie plików danych za pomocą Synapse SQL
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 0986a1d6a75f0d464eb405841af821c606c68200
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565325"
---
# <a name="use-external-tables-with-synapse-sql"></a>Używanie tabel zewnętrznych z Synapse SQL

Zewnętrzna tabela wskazuje dane znajdujące się w usłudze Hadoop, obiekcie blob usługi Azure Storage lub Azure Data Lake Storage. Tabele zewnętrzne są używane do odczytywania danych z plików lub do zapisu danych w plikach w usłudze Azure Storage. Dzięki Synapse SQL można używać tabel zewnętrznych do odczytywania i zapisu danych w dedykowanej puli SQL lub bez użycia serwera puli SQL.

## <a name="external-tables-in-dedicated-sql-pool-and-serverless-sql-pool"></a>Tabele zewnętrzne w dedykowanej puli SQL i bez serwera puli SQL

### <a name="dedicated-sql-pool"></a>[Dedykowana pula SQL](#tab/sql-pool) 

W dedykowanej puli SQL można użyć tabeli zewnętrznej, aby:

- Wykonywanie Azure Blob Storage i usługi Azure Data Lake Gen2 za pomocą instrukcji języka Transact-SQL.
- Importowanie i przechowywanie danych z Azure Blob Storage i Azure Data Lake Storage do dedykowanej puli SQL.

Gdy jest używana w połączeniu z CREATE TABLE [AS SELECT,](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) wybranie z tabeli zewnętrznej importuje dane do tabeli w puli SQL. Oprócz instrukcji [COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)tabele zewnętrzne są przydatne do ładowania danych. 

Aby uzyskać samouczek dotyczący ładowania, zobacz Ładowanie danych z programu [przy użyciu programu PolyBase Azure Blob Storage](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

### <a name="serverless-sql-pool"></a>[Bez serwera pula SQL](#tab/sql-on-demand)

W przypadku bez serwera puli SQL użyjesz tabeli zewnętrznej, aby:

- Wykonywanie zapytań o dane w Azure Blob Storage lub Azure Data Lake Storage za pomocą instrukcji języka Transact-SQL
- Przechowywanie wyników zapytania puli SQL bez serwera w plikach w Azure Blob Storage lub Azure Data Lake Storage użyciu [instrukcji CETAS](develop-tables-cetas.md)

Tabele zewnętrzne można tworzyć przy użyciu bez serwera puli SQL, korzystając z następujących kroków:

1. TWORZENIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH
2. CREATE EXTERNAL FILE FORMAT
3. TWORZENIE TABELI ZEWNĘTRZNEJ

---

### <a name="security"></a>Zabezpieczenia

Użytkownik musi mieć `SELECT` uprawnienia do zewnętrznej tabeli, aby odczytać dane.
Dostęp do tabeli zewnętrznej w usłudze Azure Storage przy użyciu poświadczeń o zakresie bazy danych zdefiniowanych w źródle danych przy użyciu następujących reguł:
- Źródło danych bez poświadczeń umożliwia tabelom zewnętrznym dostęp do publicznie dostępnych plików w usłudze Azure Storage.
- Źródło danych może mieć poświadczenia umożliwiające tabelom zewnętrznym dostęp tylko do plików w usłudze Azure Storage przy użyciu tokenu SAS lub tożsamości zarządzanej obszaru roboczego — przykłady można znaleźć w artykule [Develop storage files access control](develop-storage-files-storage-access-control.md#examples) (Opracowywanie kontroli dostępu do magazynu plików magazynu).

> [!IMPORTANT]
> W dedykowanej puli SQL źródło danych utworzone bez poświadczeń umożliwia użytkownikom usługi Azure AD dostęp do plików magazynu przy użyciu ich tożsamości usługi Azure AD. W bezużytecznej puli SQL należy utworzyć źródło danych z poświadczeniami w zakresie bazy danych, które mają właściwość `IDENTITY='User Identity'` — zobacz [przykłady tutaj.](develop-storage-files-storage-access-control.md#examples)

## <a name="create-external-data-source"></a>TWORZENIE ZEWNĘTRZNEGO ŹRÓDŁA DANYCH

Zewnętrzne źródła danych są używane do łączenia się z kontami magazynu. Pełną dokumentację opisano [tutaj.](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="syntax-for-create-external-data-source"></a>Składnia polecenia CREATE EXTERNAL DATA SOURCE

#### <a name="dedicated-sql-pool"></a>[Dedykowana pula SQL](#tab/sql-pool)

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
     , TYPE = HADOOP
)
[;]
```

#### <a name="serverless-sql-pool"></a>[Bez serwera pula SQL](#tab/sql-on-demand)

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
)
[;]
```

---

### <a name="arguments-for-create-external-data-source"></a>Argumenty dla polecenia CREATE EXTERNAL DATA SOURCE

data_source_name

Określa zdefiniowaną przez użytkownika nazwę źródła danych. Nazwa musi być unikatowa w obrębie bazy danych.

#### <a name="location"></a>Lokalizacja
LOCATION = `'<prefix>://<path>'`   — udostępnia protokół łączności i ścieżkę do zewnętrznego źródła danych. Następujące wzorce mogą być używane w lokalizacji:

| Zewnętrzne źródło danych        | Prefiks lokalizacji | Ścieżka lokalizacji                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Blob Storage          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Azure Blob Storage          | `http[s]`       | `<storage_account>.blob.core.windows.net/<container>/subfolders` |
| Azure Data Lake Store Gen 1 | `http[s]`       | `<storage_account>.azuredatalakestore.net/webhdfs/v1` |
| Azure Data Lake Store Gen 2 | `http[s]`       | `<storage_account>.dfs.core.windows.net/<container>/subfolders`  |

`https:` Prefiks umożliwia użycie podfolderu w ścieżce.

#### <a name="credential"></a>Poświadczenie
CREDENTIAL = `<database scoped credential>` to opcjonalne poświadczenie, które będzie używane do uwierzytelniania w usłudze Azure Storage. Zewnętrzne źródło danych bez poświadczeń może uzyskać dostęp do konta magazynu publicznego. 

Zewnętrzne źródła danych bez poświadczeń w dedykowanej puli SQL będą używać tożsamości usługi Azure AD wywołującej do uzyskiwania dostępu do plików w magazynie. Zewnętrzne źródło danych dla bez serwera puli SQL z poświadczeniami będzie używać tożsamości usługi Azure AD wywołującej do  `IDENTITY='User Identity'` uzyskiwania dostępu do plików.
- W dedykowanej puli SQL poświadczenia w zakresie bazy danych mogą określać niestandardową tożsamość aplikacji, tożsamość zarządzaną obszaru roboczego lub klucz SAK. 
- W bezserowej puli SQL poświadczenia w zakresie bazy danych mogą określać tożsamość usługi Azure AD wywołującego, tożsamość zarządzaną obszaru roboczego lub klucz sygnatury dostępu współdzielonego. 

#### <a name="type"></a>TYP
TYPE = jest obowiązkową opcją w dedykowanej puli SQL i określa, że technologia Polybase jest używana do uzyskiwania `HADOOP` dostępu do plików bazowych. Tego parametru nie można używać w bezużytecznej puli SQL, która używa wbudowanego czytnika natywnego.

### <a name="example-for-create-external-data-source"></a>Przykład tworzenia ZEWNĘTRZNEGO ŹRÓDŁA DANYCH

#### <a name="dedicated-sql-pool"></a>[Dedykowana pula SQL](#tab/sql-pool)

Poniższy przykład tworzy zewnętrzne źródło danych dla usługi Azure Data Lake Gen2, które wskaże zestaw danych w Nowym Jorku:

```sql
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://data@newyorktaxidataset.dfs.core.windows.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

#### <a name="serverless-sql-pool"></a>[Bez serwera pula SQL](#tab/sql-on-demand)

Poniższy przykład tworzy zewnętrzne źródło danych dla usługi Azure Data Lake Gen2, do którego można uzyskać dostęp przy użyciu poświadczeń SAS:

```sql
CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO

CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

Poniższy przykład tworzy zewnętrzne źródło danych dla usługi Azure Data Lake Gen2, które wskaże publicznie dostępny zestaw danych w Nowym Jorku:

```sql
CREATE EXTERNAL DATA SOURCE YellowTaxi
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
```
---

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Tworzy obiekt formatu pliku zewnętrznego, który definiuje dane zewnętrzne przechowywane w Azure Blob Storage lub Azure Data Lake Storage. Tworzenie formatu pliku zewnętrznego jest warunkiem wstępnym tworzenia tabeli zewnętrznej. Pełna dokumentacja znajduje się [tutaj.](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)

Tworząc format pliku zewnętrznego, określasz rzeczywisty układ danych, do których odwołuje się tabela zewnętrzna.

### <a name="syntax-for-create-external-file-format"></a>Składnia dla CREATE EXTERNAL FILE FORMAT

#### <a name="sql-pool"></a>[Pula SQL](#tab/sql-pool)

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
    [ , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' ]
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

#### <a name="serverless-sql-pool"></a>[Bez serwera pula SQL](#tab/sql-on-demand)

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
    [ , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' ]
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
    | PARSER_VERSION = {'parser_version'}
}
```

---


### <a name="arguments-for-create-external-file-format"></a>Argumenty dla polecenia CREATE EXTERNAL FILE FORMAT

file_format_name - Określa nazwę formatu pliku zewnętrznego.

FORMAT_TYPE = [ PARQUET | DELIMITEDTEXT]- Określa format danych zewnętrznych.

- PARQUET — określa format Parquet.
- DELIMITEDTEXT — określa format tekstu z ogranicznikami kolumn, nazywanymi również terminatorami pól.

FIELD_TERMINATOR = *field_terminator* — dotyczy tylko rozdzielanych plików tekstowych. Terminator pola określa co najmniej jeden znak, który oznacza koniec każdego pola (kolumny) w pliku tekstowym z ogranicznikami. Wartość domyślna to znak potoku (ꞌ|ꞌ).

Przykłady:

- FIELD_TERMINATOR = "|"
- FIELD_TERMINATOR = ' '
- FIELD_TERMINATOR = ꞌ\tꞌ

STRING_DELIMITER = *string_delimiter* - Określa terminator pola dla danych typu ciąg w pliku tekstowym. Ogranicznik ciągu ma długość co najmniej jednego znaków i jest ujęty w a cudzysłów pojedynczy. Wartością domyślną jest pusty ciąg ("").

Przykłady:

- STRING_DELIMITER = """
- STRING_DELIMITER = '*'
- STRING_DELIMITER = ꞌ,ꞌ

FIRST_ROW = *First_row_int* — określa numer wiersza, który jest odczytywany jako pierwszy i ma zastosowanie do wszystkich plików. Ustawienie wartości na dwa powoduje pominięcie pierwszego wiersza w każdym pliku (wierszu nagłówka) podczas ładowania danych. Wiersze są pomijane na podstawie istnienia terminatorów wierszy (/r/n, /r, /n).

USE_TYPE_DEFAULT = { TRUE | **FALSE** } - Określa sposób obsługi brakujących wartości w rozdzielanych plikach tekstowych podczas pobierania danych z pliku tekstowego.

TRUE — w przypadku pobierania danych z pliku tekstowego przechowuj każdą brakującą wartość przy użyciu typu danych wartości domyślnej dla odpowiedniej kolumny w definicji tabeli zewnętrznej. Na przykład zastąp brakującą wartość:

- 0, jeśli kolumna jest zdefiniowana jako kolumna liczbowa. Kolumny dziesiętne nie są obsługiwane i powodują błąd.
- Pusty ciąg (""), jeśli kolumna jest kolumną ciągu.
- 1900-01-01, jeśli kolumna jest kolumną dat.

FALSE — wszystkie brakujące wartości należy przechowywać jako wartości NULL. Wszystkie wartości NULL przechowywane przy użyciu słowa NULL w rozdzielanych plikach tekstowych są importowane jako ciąg "NULL".

Kodowanie = {'UTF8' | 'UTF16'} — bez serwera pula SQL może odczytywać rozdzielane pliki tekstowe zakodowane w formacie UTF8 i UTF16.

DATA_COMPRESSION = *data_compression_method* — ten argument określa metodę kompresji danych dla danych zewnętrznych. 

Typ formatu pliku PARQUET obsługuje następujące metody kompresji:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

Podczas odczytywania z tabel zewnętrznych PARQUET ten argument jest ignorowany, ale jest używany podczas zapisywania w tabelach zewnętrznych przy użyciu [cetas](develop-tables-cetas.md).

Typ formatu pliku DELIMITEDTEXT obsługuje następującą metodę kompresji:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'

PARSER_VERSION = "parser_version" Określa wersję parsera, która ma być używana podczas odczytywania plików. Sprawdź PARSER_VERSION argument w [argumentach OPENROWSET,](develop-openrowset.md#arguments) aby uzyskać szczegółowe informacje.

### <a name="example-for-create-external-file-format"></a>Przykład dla formatu CREATE EXTERNAL FILE

Poniższy przykład tworzy format pliku zewnętrznego dla plików spisu:

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>CREATE EXTERNAL TABLE

Polecenie CREATE EXTERNAL TABLE tworzy tabelę zewnętrzną dla Synapse SQL dostępu do danych przechowywanych w Azure Blob Storage lub Azure Data Lake Storage. 

### <a name="syntax-for-create-external-table"></a>Składnia polecenia CREATE EXTERNAL TABLE

```sql
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

### <a name="arguments-create-external-table"></a>Argumenty CREATE EXTERNAL TABLE

*{ database_name.schema_name.table_name | schema_name.table_name | table_name }*

Nazwa tabeli do utworzenia (od jednej do trzech części). W przypadku tabeli zewnętrznej bez serwera pula SQL przechowuje tylko metadane tabeli. Żadne rzeczywiste dane nie są przenoszone ani przechowywane w bez serwera puli SQL.

<column_definition>, ... *n* ]

Create EXTERNAL TABLE obsługuje możliwość konfigurowania nazwy kolumny, typu danych i sortowanie. W tabelach zewnętrznych nie można używać ograniczenia DEFAULT.

>[!IMPORTANT]
>Definicje kolumn, w tym typy danych i liczby kolumn, muszą być zgodne z danymi w plikach zewnętrznych. W przypadku niezgodności wiersze pliku zostaną odrzucone podczas wykonywania zapytania na danych rzeczywistych.

Podczas odczytywania z plików Parquet można określić tylko te kolumny, które mają zostać odczytane, i pominąć pozostałe.

LOCATION = '*folder_or_filepath*'

Określa folder lub ścieżkę pliku i nazwę pliku dla rzeczywistych danych w Azure Blob Storage. Lokalizacja rozpoczyna się od folderu głównego. Folder główny to lokalizacja danych określona w zewnętrznym źródle danych.

Jeśli określisz lokalizację folderu, bez serwera kwerendy puli SQL wybierze z tabeli zewnętrznej i pobierze pliki z folderu.

> [!NOTE]
> W przeciwieństwie do usług Hadoop i PolyBase bez serwera bez serwera pula SQL nie zwraca podfolderów, chyba że określisz /** na końcu ścieżki. Podobnie jak w przypadku platform Hadoop i PolyBase, nie zwraca ono plików, dla których nazwa pliku zaczyna się od podkreślenia (_) lub okresu (.).

W tym przykładzie, jeśli LOCATION='/webdata/', bez serwera zapytanie puli SQL, zwróci wiersze z mydata.txt. Nie zwróci ono mydata2.txt i mydata3.txt ponieważ znajdują się w podfolderze.

![Rekursywne dane dla tabel zewnętrznych](./media/develop-tables-external-tables/folder-traversal.png)

DATA_SOURCE = *external_data_source_name* — określa nazwę zewnętrznego źródła danych, które zawiera lokalizację danych zewnętrznych. Aby utworzyć zewnętrzne źródło danych, użyj [funkcji CREATE EXTERNAL DATA SOURCE.](#create-external-data-source)

FILE_FORMAT = *external_file_format_name* — określa nazwę obiektu formatu pliku zewnętrznego, który przechowuje typ pliku i metodę kompresji dla danych zewnętrznych. Aby utworzyć format pliku zewnętrznego, użyj [formatu CREATE EXTERNAL FILE FORMAT](#create-external-file-format).

### <a name="permissions-create-external-table"></a>Uprawnienia CREATE EXTERNAL TABLE

Do wybrania z tabeli zewnętrznej potrzebne są odpowiednie poświadczenia z listą i uprawnieniami do odczytu.

### <a name="example-create-external-table"></a>Przykład: CREATE EXTERNAL TABLE

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

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Tworzenie tabel zewnętrznych i wykonywanie zapytań dotyczących ich z pliku w usłudze Azure Data Lake

Za pomocą funkcji eksploracji usługi Data Lake można teraz tworzyć tabelę zewnętrzną i tworzyć zapytania dotyczące jej przy użyciu dedykowanej puli SQL lub bez serwera puli SQL za pomocą prostego kliknięcia pliku prawym przyciskiem myszy. Gesty jednym kliknięciem służące do tworzenia tabel zewnętrznych na podstawie ADLS Gen2 magazynu są obsługiwane tylko w przypadku plików Parquet. 

### <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć dostęp do obszaru roboczego z rolą dostępu ARM Współautor danych obiektu blob usługi Storage do konta ADLS Gen2 magazynu

- Musisz mieć co najmniej uprawnienia [do tworzenia tabel](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#permissions-2&preserve-view=true) zewnętrznych w puli SQL lub SQL OD i wykonywania zapytań o nie

Na panelu Dane wybierz plik, z których chcesz utworzyć tabelę zewnętrzną:
> [!div class="mx-imgBorder"]
>![externaltable1](./media/develop-tables-external-tables/external-table-1.png)

Zostanie otwarte okno dialogowe. Wybierz dedykowaną pulę SQL lub bez serwera pulę SQL, nadaj nazwę tabeli i wybierz otwarty skrypt:

> [!div class="mx-imgBorder"]
>![externaltable2](./media/develop-tables-external-tables/external-table-2.png)

Skrypt SQL jest automatycznie generowany wywnioskując schemat z pliku :
> [!div class="mx-imgBorder"]
>![externaltable3](./media/develop-tables-external-tables/external-table-3.png)

Uruchom skrypt. Skrypt automatycznie uruchamia polecenie Select Top 100 *.:
> [!div class="mx-imgBorder"]
>![externaltable4](./media/develop-tables-external-tables/external-table-4.png)

Tabela zewnętrzna jest teraz tworzona. W celu przyszłej eksploracji zawartości tej tabeli zewnętrznej użytkownik może tworzyć zapytania bezpośrednio z okienka Dane:
> [!div class="mx-imgBorder"]
>![externaltable5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [artykułem CETAS,](develop-tables-cetas.md) aby dowiedzieć się, jak zapisać wyniki zapytania w tabeli zewnętrznej w usłudze Azure Storage. Możesz też rozpocząć wykonywanie zapytań [Apache Spark dla Azure Synapse tabel zewnętrznych.](develop-storage-files-spark-tables.md)
