---
title: Używanie tabel zewnętrznych z Synapse SQL
description: Odczytywanie i zapisywanie plików danych za pomocą języka SQL Synapse
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 6c76fcc0fefdf8aa3ae97a4c131481f7ea6ada81
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91288855"
---
# <a name="use-external-tables-with-synapse-sql"></a>Używanie tabel zewnętrznych z Synapse SQL

Zewnętrzna tabela wskazuje dane znajdujące się w usłudze Hadoop, w usłudze Azure Storage BLOB lub Azure Data Lake Storage. Tabele zewnętrzne są używane do odczytywania danych z plików lub zapisywania danych w plikach w usłudze Azure Storage. Za pomocą języka SQL Synapse można używać tabel zewnętrznych do odczytywania i zapisywania danych w puli SQL lub SQL na żądanie (wersja zapoznawcza).

## <a name="external-tables-in-synapse-sql-pool-and-on-demand"></a>Tabele zewnętrzne w puli Synapse SQL i na żądanie

### <a name="sql-pool"></a>[Pula SQL](#tab/sql-pool) 

W puli SQL można użyć tabeli zewnętrznej do:

- Wykonaj zapytania dotyczące usługi Azure Blob Storage i Azure Data Lake Gen2 przy użyciu instrukcji języka Transact-SQL.
- Importuj i przechowuj dane z usługi Azure Blob Storage i Azure Data Lake Storage do puli SQL.

Gdy jest używany w połączeniu z instrukcją [CREATE TABLE jako SELECT](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) , wybranie z tabeli zewnętrznej importuje dane do tabeli w puli SQL. Oprócz [instrukcji Copy](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)tabele zewnętrzne są przydatne do ładowania danych. 

Aby zapoznać się z samouczkiem ładowania, zobacz Tworzenie [danych z usługi Azure Blob Storage przy użyciu sieci podstawowej](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="sql-on-demand"></a>[SQL na żądanie](#tab/sql-on-demand)

W przypadku usługi SQL na żądanie będziesz używać tabeli zewnętrznej do:

- Wykonywanie zapytań dotyczących danych w usłudze Azure Blob Storage lub Azure Data Lake Storage przy użyciu instrukcji języka Transact-SQL
- Przechowywanie wyników zapytania na żądanie SQL w plikach na platformie Azure Blob Storage lub Azure Data Lake Storage przy użyciu [CETAS](develop-tables-cetas.md)

Tabele zewnętrzne można utworzyć przy użyciu programu SQL na żądanie, wykonując następujące czynności:

1. UTWÓRZ ZEWNĘTRZNE ŹRÓDŁO DANYCH
2. CREATE EXTERNAL FILE FORMAT
3. TWORZENIE TABELI ZEWNĘTRZNEJ

---

### <a name="security"></a>Zabezpieczenia

Użytkownik musi mieć `SELECT` uprawnienia do tabeli zewnętrznej, aby odczytywać dane.
Zewnętrzna tabela dostępu do magazynu platformy Azure przy użyciu podanego w zakresie bazy danych poświadczeń zdefiniowanej w źródle danych przy użyciu następujących reguł:
- Źródło danych bez poświadczeń umożliwia tabelom zewnętrznym dostęp do publicznie dostępnych plików w usłudze Azure Storage.
- Źródło danych może mieć poświadczenia umożliwiające zewnętrznym tabelom dostęp do plików w usłudze Azure Storage przy użyciu tokenu SAS lub tożsamości zarządzanej przez obszar roboczy — aby poznać przykłady, zobacz artykuł dotyczący [tworzenia plików magazynu w ramach kontroli dostępu do magazynu](develop-storage-files-storage-access-control.md#examples) .

> [!IMPORTANT]
> W puli SQL źródło danych bez creadential umożliwia użytkownikowi usługi Azure AD dostęp do plików magazynu przy użyciu tożsamości usługi Azure AD. W programie SQL na żądanie należy utworzyć źródło danych z poświadczeniami z zakresem bazy danych zawierającymi `IDENTITY='User Identity'` właściwości — Zobacz [przykłady tutaj](develop-storage-files-storage-access-control.md#examples).

## <a name="create-external-data-source"></a>UTWÓRZ ZEWNĘTRZNE ŹRÓDŁO DANYCH

Zewnętrzne źródła danych są używane do łączenia się z kontami magazynu. Kompletna dokumentacja została [zamieszczona tutaj](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="syntax-for-create-external-data-source"></a>Składnia dla tworzenia zewnętrznego źródła danych

#### <a name="sql-pool"></a>[Pula SQL](#tab/sql-pool)

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
     , TYPE = HADOOP
)
[;]
```

#### <a name="sql-on-demand"></a>[SQL na żądanie](#tab/sql-on-demand)

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
)
[;]
```

---

### <a name="arguments-for-create-external-data-source"></a>Argumenty tworzenia zewnętrznego źródła danych

data_source_name

Określa zdefiniowaną przez użytkownika nazwę źródła danych. Nazwa musi być unikatowa w obrębie bazy danych.

#### <a name="location"></a>Lokalizacja
LOCATION = `'<prefix>://<path>'`   — udostępnia protokół połączenia i ścieżkę do zewnętrznego źródła danych. W lokalizacji można używać następujących wzorców:

| Zewnętrzne źródło danych        | Prefiks lokalizacji | Ścieżka lokalizacji                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Blob Storage          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Azure Blob Storage          | `http[s]`       | `<storage_account>.blob.core.windows.net/<container>/subfolders` |
| Azure Data Lake Store Gen 1 | `http[s]`       | `<storage_account>.azuredatalakestore.net/webhdfs/v1` |
| Azure Data Lake Store Gen 2 | `http[s]`       | `<storage_account>.dfs.core.windows.net/<container>/subfolders`  |

`https:` prefiks umożliwia użycie podfolderu w ścieżce.

#### <a name="credential"></a>Poświadczenie
CREDENTIAL = `<database scoped credential>` to opcjonalne poświadczenie, które będzie używane do uwierzytelniania w usłudze Azure Storage. Zewnętrzne źródło danych bez poświadczeń może uzyskać dostęp do konta magazynu publicznego. 

Zewnętrzne źródła danych bez poświadczeń w puli SQL mogą również korzystać z tożsamości usługi Azure AD wywołujących w celu uzyskiwania dostępu do plików w magazynie. Zewnętrzne źródło danych z poświadczeniami użycia określonej w poświadczeniu do uzyskiwania dostępu do plików.
- W puli SQL poświadczenia w zakresie bazy danych mogą określać niestandardową tożsamość aplikacji, tożsamość zarządzaną w obszarze roboczym lub klucz SAK. 
- W usłudze SQL na żądanie poświadczenia bazy danych mogą określać tożsamość usługi Azure AD, tożsamość zarządzaną w obszarze roboczym lub klucz sygnatury dostępu współdzielonego. 

#### <a name="type"></a>TYP
Typ = `HADOOP` jest obowiązkowy opcja w puli SQL i określa, że technologia podstawowa jest używana do uzyskiwania dostępu do plików źródłowych. Ten parametr nie może być używany w usłudze SQL na żądanie, która używa wbudowanego czytnika natywnego.

### <a name="example-for-create-external-data-source"></a>Przykład tworzenia zewnętrznego źródła danych

#### <a name="sql-pool"></a>[Pula SQL](#tab/sql-pool)

Poniższy przykład tworzy zewnętrzne źródło danych dla Azure Data Lake Gen2 wskazujące zestaw danych New York:

```sql
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://data@newyorktaxidataset.dfs.core.windows.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

#### <a name="sql-on-demand"></a>[SQL na żądanie](#tab/sql-on-demand)

Poniższy przykład tworzy zewnętrzne źródło danych dla Azure Data Lake Gen2, do którego można uzyskać dostęp przy użyciu poświadczeń sygnatury dostępu współdzielonego:

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

Poniższy przykład tworzy zewnętrzne źródło danych dla Azure Data Lake Gen2 wskazujące publicznie dostępny zestaw danych Nowego Jorku:

```sql
CREATE EXTERNAL DATA SOURCE YellowTaxi
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
```
---

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Tworzy obiekt zewnętrznego formatu pliku, który definiuje dane zewnętrzne przechowywane w usłudze Azure Blob Storage lub Azure Data Lake Storage. Tworzenie zewnętrznego formatu pliku jest wymaganiem wstępnym dla tworzenia tabeli zewnętrznej. Kompletna dokumentacja jest dostępna [tutaj](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Tworząc zewnętrzny format pliku, należy określić rzeczywisty układ danych, do których odwołuje się tabela zewnętrzna.

### <a name="syntax-for-create-external-file-format"></a>Składnia polecenia CREATE EXTERNAL FILE FORMAT

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

### <a name="arguments-for-create-external-file-format"></a>Argumenty dla tworzenia formatu zewnętrznego pliku

file_format_name — określa nazwę formatu pliku zewnętrznego.

FORMAT_TYPE = [PARQUET | DELIMITEDTEXT] — określa format danych zewnętrznych.

- PARQUET — określa format Parquet.
- DELIMITEDTEXT — określa format tekstu z ogranicznikami kolumn, nazywanymi również terminatorami pól.

FIELD_TERMINATOR = *field_terminator* — dotyczy tylko rozdzielanych plików tekstowych. Terminator pola określa jeden lub więcej znaków, które oznaczają koniec każdego pola (kolumny) w pliku rozdzielonym tekstem. Wartość domyślna to znak potoku (ꞌ | ꞌ).

Przykłady:

- FIELD_TERMINATOR = "|"
- FIELD_TERMINATOR = ""
- FIELD_TERMINATOR = ꞌ \ t ꞌ

STRING_DELIMITER = *string_delimiter* — Określa terminator pola dla danych ciągu typu w pliku rozdzielonym tekstem. Ogranicznik ciągu ma długość co najmniej jednego znaku i jest ujęty w apostrofy. Wartość domyślna to ciąg pusty ("").

Przykłady:

- STRING_DELIMITER = "" "
- STRING_DELIMITER = "*"
- STRING_DELIMITER = ꞌ, ꞌ

FIRST_ROW = *First_row_int* — określa numer wiersza, który jest odczytywany jako pierwszy i ma zastosowanie do wszystkich plików. Ustawienie wartości na dwa powoduje pominięcie pierwszego wiersza w każdym pliku (wierszu nagłówka) podczas ładowania danych. Wiersze są pomijane na podstawie istnienia terminatorów wierszy (/r/n,/r,/n).

USE_TYPE_DEFAULT = {TRUE | **Wartość false** } -Określa, jak obsługiwać brakujące wartości w rozdzielanych plikach tekstowych podczas pobierania danych z pliku tekstowego.

Prawda — jeśli pobierasz dane z pliku tekstowego, przechowuj każdą brakującą wartość za pomocą typu danych wartość domyślna dla odpowiadającej kolumny w definicji tabeli zewnętrznej. Na przykład Zastąp brakującą wartość wartością:

- 0, jeśli kolumna jest zdefiniowana jako kolumna numeryczna. Kolumny dziesiętne nie są obsługiwane i spowodują wystąpienie błędu.
- Pusty ciąg (""), jeśli kolumna jest kolumną ciągu.
- 1900-01-01, jeśli kolumna jest kolumną daty.

FALSE — Przechowuj wszystkie brakujące wartości jako wartości NULL. Wszystkie wartości NULL, które są przechowywane przy użyciu wyrazu NULL w rozdzielonym pliku tekstowym, są importowane jako ciąg "NULL".

Kodowanie = {"UTF8" | "UTF16"} — SQL na żądanie może odczytywać zakodowane pliki tekstowe UTF8 i UTF16.

DATA_COMPRESSION = *data_compression_method* — ten argument określa metodę kompresji danych zewnętrznych. 

Typ formatu pliku PARQUET obsługuje następujące metody kompresji:

- DATA_COMPRESSION = "org. Apache. Hadoop. IO. Kompresuj. GzipCodec"
- DATA_COMPRESSION = "org. Apache. Hadoop. IO. Kompresuj. SnappyCodec"

Podczas odczytywania z PARQUET zewnętrznych tabel ten argument jest ignorowany, ale jest używany podczas zapisywania w tabelach zewnętrznych przy użyciu [CETAS](develop-tables-cetas.md).

Typ formatu pliku DELIMITEDTEXT obsługuje następującą metodę kompresji:

- DATA_COMPRESSION = "org. Apache. Hadoop. IO. Kompresuj. GzipCodec"

### <a name="example-for-create-external-file-format"></a>Przykład tworzenia zewnętrznego formatu pliku

Poniższy przykład tworzy zewnętrzny format pliku dla plików spisu:

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>TWORZENIE TABELI ZEWNĘTRZNEJ

Polecenie Utwórz tabelę ZEWNĘTRZną tworzy tabelę zewnętrzną dla Synapse SQL, aby uzyskać dostęp do danych przechowywanych w usłudze Azure Blob Storage lub Azure Data Lake Storage. 

### <a name="syntax-for-create-external-table"></a>Składnia dla tworzenia tabeli zewnętrznej

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

### <a name="arguments-create-external-table"></a>Tworzenie tabeli zewnętrznej przez argumenty

*{database_name. schema_name. table_name | schema_name. table_name | table_name}*

Nazwa tabeli, która ma zostać utworzona. W przypadku tabeli zewnętrznej SQL na żądanie przechowuje tylko metadane tabeli. Żadne rzeczywiste dane nie są przenoszone ani przechowywane na żądanie SQL.

<column_definition>,... *n* ]

Tworzenie tabeli zewnętrznej obsługuje możliwość konfigurowania nazwy kolumny, typu danych, wartości null i sortowania. Nie można użyć ograniczenia domyślnego dla tabel zewnętrznych.

>[!IMPORTANT]
>Definicje kolumn, w tym typy danych i liczby kolumn, muszą być zgodne z danymi w plikach zewnętrznych. W przypadku niezgodności wiersze pliku zostaną odrzucone podczas wykonywania zapytania na danych rzeczywistych.

Podczas odczytu z plików Parquet można określić tylko kolumny, które mają zostać odczytane, i pominąć resztę.

LOCATION = "*folder_or_filepath*"

Określa folder lub ścieżkę pliku i nazwę pliku dla rzeczywistych danych w usłudze Azure Blob Storage. Lokalizacja jest uruchamiana z folderu głównego. Folder główny to lokalizacja danych określona w zewnętrznym źródle danych.

W przypadku określenia lokalizacji folderu zapytanie na żądanie SQL zostanie wybrane z tabeli zewnętrznej i pobrane pliki z folderu.

> [!NOTE]
> W przeciwieństwie do usługi Hadoop i bazy danych SQL na żądanie nie zwraca podfolderów. Zwraca pliki, dla których nazwa pliku zaczyna się od podkreślenia (_) lub kropki (.).

W tym przykładzie, jeśli LOCATION = "/webdata/", zapytanie na żądanie SQL zwróci wiersze z mydata.txt i _hidden.txt. Nie zwróci mydata2.txt i mydata3.txt, ponieważ znajdują się w podfolderze.

![Dane cykliczne dla tabel zewnętrznych](./media/develop-tables-external-tables/folder-traversal.png)

DATA_SOURCE = *external_data_source_name* — określa nazwę zewnętrznego źródła danych, które zawiera lokalizację danych zewnętrznych. Aby utworzyć zewnętrzne źródło danych, użyj polecenie [Utwórz zewnętrzne źródło danych](#create-external-data-source).

FILE_FORMAT = *external_file_format_name* — określa nazwę obiektu zewnętrznego formatu pliku, który przechowuje typ pliku i metodę kompresji danych zewnętrznych. Aby utworzyć zewnętrzny format pliku, użyj [formatu Utwórz zewnętrzny plik](#create-external-file-format).

### <a name="permissions-create-external-table"></a>Uprawnienia Tworzenie tabeli zewnętrznej

Aby można było wybrać tabelę zewnętrzną, potrzebne są odpowiednie poświadczenia z uprawnieniami list i odczytu.

### <a name="example-create-external-table"></a>Przykład tworzenia tabeli zewnętrznej

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

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Tworzenie i wykonywanie zapytań dotyczących tabel zewnętrznych z pliku w Azure Data Lake

Korzystając z funkcji Data Lake eksploracji, możesz teraz tworzyć i wysyłać zapytania do tabeli zewnętrznej przy użyciu puli SQL lub SQL na żądanie z prostym kliknięciem pliku.

### <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć dostęp do obszaru roboczego z co najmniej rolą dostępu ARM współautora danych obiektów blob magazynu do konta ADLS Gen2

- Musisz mieć co najmniej [uprawnienia do tworzenia](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#permissions-2&preserve-view=true) i wysyłania zapytań dotyczących tabel zewnętrznych w puli SQL lub SQL od

- Połączona usługa skojarzona z kontem ADLS Gen2 **musi mieć dostęp do pliku**. Na przykład jeśli mechanizm uwierzytelniania połączonej usługi jest tożsamością zarządzaną, tożsamość zarządzana w obszarze roboczym musi mieć co najmniej uprawnienie czytelnik magazynu obiektów BLOB na koncie magazynu

Z panelu dane wybierz plik, dla którego chcesz utworzyć zewnętrzną tabelę:
> [!div class="mx-imgBorder"]
>![externaltable1](./media/develop-tables-external-tables/external-table-1.png)

Zostanie otwarte okno dialogowe. Wybierz pozycję Pula SQL lub SQL na żądanie, nadaj jej nazwę tabeli i wybierz pozycję Otwórz skrypt:

> [!div class="mx-imgBorder"]
>![externaltable2](./media/develop-tables-external-tables/external-table-2.png)

Skrypt SQL został automatycznie wygenerowany w celu wywnioskowania schematu z pliku:
> [!div class="mx-imgBorder"]
>![externaltable3](./media/develop-tables-external-tables/external-table-3.png)

Uruchom skrypt. Skrypt automatycznie uruchomi wybierz górną 100 *.:
> [!div class="mx-imgBorder"]
>![externaltable4](./media/develop-tables-external-tables/external-table-4.png)

Tabela zewnętrzna została utworzona, w celu przeprowadzenia dalszej eksploracji zawartości tej tabeli zewnętrznej użytkownik może zbadać ją bezpośrednio w okienku dane:
> [!div class="mx-imgBorder"]
>![externaltable5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem [CETAS](develop-tables-cetas.md) , jak zapisać wyniki zapytania w tabeli zewnętrznej w usłudze Azure Storage. Można też rozpocząć wykonywanie zapytań [dotyczących Apache Spark w przypadku tabel zewnętrznych usługi Azure Synapse](develop-storage-files-spark-tables.md).
