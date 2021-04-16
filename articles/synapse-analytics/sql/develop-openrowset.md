---
title: How to use OPENROWSET in serverless SQL pool (Jak używać funkcji OPENROWSET w bez serwerach puli SQL)
description: W tym artykule opisano składnię funkcji OPENROWSET w bez serwerach puli SQL i wyjaśniono, jak używać argumentów.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 28c54865ab9c2876d998896f5f536a11088962f8
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566430"
---
# <a name="how-to-use-openrowset-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Jak używać zestawu OPENROWSET przy użyciu bez serwera puli SQL w Azure Synapse Analytics

Funkcja `OPENROWSET(BULK...)` umożliwia dostęp do plików w usłudze Azure Storage. `OPENROWSET` Funkcja odczytuje zawartość zdalnego źródła danych (na przykład pliku) i zwraca zawartość jako zestaw wierszy. W ramach bez serwera zasobów puli SQL dostęp do dostawcy zbiorczego zestawu wierszy OPENROWSET uzyskuje się przez wywołanie funkcji OPENROWSET i określenie opcji BULK.  

Do `OPENROWSET` funkcji można odwoływać się w `FROM` klauzuli zapytania tak, jakby była nazwą tabeli `OPENROWSET` . Obsługuje operacje zbiorcze za pośrednictwem wbudowanego dostawcy BULK, który umożliwia odczytywanie i zwracanie danych z pliku jako zestawu wierszy.

## <a name="data-source"></a>Źródło danych

Funkcja OPENROWSET w Synapse SQL odczytuje zawartość plików ze źródła danych. Źródłem danych jest konto usługi Azure Storage i można do niego jawnie odwoływać się w funkcji lub można je dynamicznie wywnioskować z adresu URL plików, które `OPENROWSET` chcesz odczytać.
Funkcja `OPENROWSET` może opcjonalnie zawierać `DATA_SOURCE` parametr w celu określenia źródła danych zawierającego pliki.
- `OPENROWSET` Bez może służyć do bezpośredniego odczytywania zawartości plików z lokalizacji `DATA_SOURCE` adresu URL określonej jako `BULK` opcja:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://<storage account>.dfs.core.windows.net/container/folder/*.parquet',
                    FORMAT = 'PARQUET') AS file
    ```

Jest to szybki i łatwy sposób odczytywania zawartości plików bez wstępnej konfiguracji. Ta opcja umożliwia użycie opcji uwierzytelniania podstawowego w celu uzyskania dostępu do magazynu (przekaż dane logowania usługi Azure AD w usłudze Azure AD i token SAS dla identyfikatorów logowania SQL). 

- `OPENROWSET` Za `DATA_SOURCE` pomocą pliku można uzyskać dostęp do plików na określonym koncie magazynu:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    FORMAT = 'PARQUET') AS file
    ```


    Ta opcja umożliwia skonfigurowanie lokalizacji konta magazynu w źródle danych i określenie metody uwierzytelniania, która ma być używana w celu uzyskania dostępu do magazynu. 
    
    > [!IMPORTANT]
    > `OPENROWSET` Bez `DATA_SOURCE` zapewnia szybki i łatwy sposób uzyskiwania dostępu do plików magazynu, ale oferuje ograniczone opcje uwierzytelniania. Na przykład podmioty zabezpieczeń usługi Azure AD mogą uzyskać dostęp do plików tylko przy użyciu tożsamości usługi [Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity) lub publicznie dostępnych plików. Jeśli potrzebujesz bardziej zaawansowanych opcji uwierzytelniania, użyj opcji i `DATA_SOURCE` zdefiniuj poświadczenia, których chcesz użyć w celu uzyskania dostępu do magazynu.


## <a name="security"></a>Zabezpieczenia

Użytkownik bazy danych musi mieć `ADMINISTER BULK OPERATIONS` uprawnienia do korzystania z funkcji `OPENROWSET` .

Administrator magazynu musi również umożliwić użytkownikowi dostęp do plików przez podanie prawidłowego tokenu SAS lub włączenie podmiotu zabezpieczeń usługi Azure AD w celu uzyskania dostępu do plików magazynu. Dowiedz się więcej o kontroli dostępu do magazynu w [tym artykule.](develop-storage-files-storage-access-control.md)

`OPENROWSET` Użyj następujących reguł, aby określić sposób uwierzytelniania w magazynie:
- W `OPENROWSET` programie `DATA_SOURCE` bez mechanizmu uwierzytelniania zależy od typu obiektu wywołującego.
  - Każdy użytkownik może używać `OPENROWSET` programu bez `DATA_SOURCE` do odczytywania publicznie dostępnych plików w usłudze Azure Storage.
  - Identyfikatory logowania usługi Azure AD mogą uzyskać dostęp do chronionych plików przy użyciu własnej tożsamości usługi [Azure AD,](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) jeśli usługa Azure Storage umożliwia użytkownikowi usługi Azure AD dostęp do podstawowych plików (na przykład jeśli wywołujący ma uprawnienia do usługi `Storage Reader` Azure Storage).
  - Identyfikatory logowania SQL mogą również używać bez uzyskiwania dostępu do publicznie dostępnych plików, plików chronionych przy użyciu tokenu SAS lub tożsamości zarządzanej `OPENROWSET` `DATA_SOURCE` obszaru roboczego usługi Synapse. Należy utworzyć poświadczenia [w zakresie serwera,](develop-storage-files-storage-access-control.md#examples) aby umożliwić dostęp do plików magazynu. 
- W programie z mechanizmem uwierzytelniania jest zdefiniowany w poświadczeń o zakresie bazy danych `OPENROWSET` `DATA_SOURCE` przypisanych do źródła danych, do których istnieje odwołanie. Ta opcja umożliwia dostęp do publicznie dostępnego magazynu lub magazynu przy użyciu tokenu SAS, tożsamości zarządzanej obszaru roboczego lub tożsamości usługi [Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) wywołującego (jeśli wywołujący jest podmiotem zabezpieczeń usługi Azure AD). Jeśli odwołuje się do usługi Azure Storage, która nie jest publiczna, należy utworzyć poświadczenia w zakresie bazy danych i odwoływać się do niego w programie , aby umożliwić dostęp `DATA_SOURCE` do plików [](develop-storage-files-storage-access-control.md#examples) `DATA SOURCE` magazynu.

Wywołujący musi mieć `REFERENCES` uprawnienia do poświadczeń, aby używać go do uwierzytelniania w magazynie.

## <a name="syntax"></a>Składnia

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ]
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ] 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal' | 'json_path'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
[ , HEADER_ROW = { TRUE | FALSE } ]
[ , DATAFILETYPE = { 'char' | 'widechar' } ]
[ , CODEPAGE = { 'ACP' | 'OEM' | 'RAW' | 'code_page' } ]
```

## <a name="arguments"></a>Argumenty

Dostępne są dwie opcje dla plików wejściowych, które zawierają dane docelowe do wykonywania zapytań. Prawidłowe wartości:

- "CSV" — zawiera dowolny rozdzielany plik tekstowy z separatorami wierszy/kolumn. Każdy znak może być używany jako separator pola, taki jak TSV: FIELDTERMINATOR = tab.

- "PARQUET" — plik binarny w formacie Parquet 

**"unstructured_data_path"**

Element unstructured_data_path, który ustanawia ścieżkę do danych, może być ścieżką bezwzględną lub względną:
- Ścieżka bezwzględna w formacie \<prefix> " :// " umożliwia użytkownikowi bezpośrednie \<storage_account_path> / \<storage_path> odczytywanie plików.
- Ścieżka względna w formacie "<storage_path>", która musi być używana z parametrem i opisuje wzorzec pliku w lokalizacji `DATA_SOURCE` <storage_account_path> zdefiniowanej w `EXTERNAL DATA SOURCE` pliku . 

Poniżej znajdziesz odpowiednie wartości, które będą łączyć się z <storage account path> konkretnym zewnętrznym źródłem danych. 

| Zewnętrzne źródło danych       | Prefiks | Ścieżka konta magazynu                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | http[s]  | \<storage_account>.blob.core.windows.net/path/file   |
| Azure Blob Storage         | wasb[s]  | \<container>@\<storage_account>.blob.core.windows.net/path/file |
| Azure Data Lake Store Gen1 | http[s]  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | http[s]  | \<storage_account>.dfs.core.windows.net /path/file   |
| Azure Data Lake Store Gen2 | abfs[s]  | [\<file_system>@\<account_name>.dfs.core.windows.net/path/file](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md#uri-syntax)              |
||||

'\<storage_path>'

Określa ścieżkę w magazynie, która wskazuje folder lub plik, który chcesz odczytać. Jeśli ścieżka wskazuje kontener lub folder, wszystkie pliki zostaną odczytane z tego konkretnego kontenera lub folderu. Pliki w podfolderach nie będą uwzględniane. 

Symboli wieloznacznych można używać do wielu plików lub folderów docelowych. Użycie wielu niezabezpieczonych symboli wieloznacznych jest dozwolone.
Poniżej znajduje się przykład, który odczytuje wszystkie *pliki csv* rozpoczynające się od populacji ze wszystkich folderów rozpoczynających się *od /csv/population*:   
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Jeśli określisz unstructured_data_path jako folder, bez serwera zapytanie puli SQL pobierze pliki z tego folderu. 

Możesz poinstruować bez serwera pulę SQL, aby przechodziła przez foldery, określając /* na końcu ścieżki, jak na przykład: `https://sqlondemandstorage.blob.core.windows.net/csv/population/**`

> [!NOTE]
> W przeciwieństwie do usług Hadoop i PolyBase bez serwera pula SQL nie zwraca podfolderów, chyba że określisz /** na końcu ścieżki. Podobnie jak w przypadku platform Hadoop i PolyBase, nie zwraca ono plików, dla których nazwa pliku zaczyna się od podkreślenia (_) lub okresu (.).

W poniższym przykładzie, jeśli unstructured_data_path = , bez serwera zapytanie puli `https://mystorageaccount.dfs.core.windows.net/webdata/` SQL zwróci wiersze z mydata.txt. Nie zwróci ono mydata2.txt i mydata3.txt ponieważ znajdują się w podfolderze.

![Rekursywne dane dla tabel zewnętrznych](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

Klauzula WITH umożliwia określenie kolumn, które mają być odczytywane z plików.

- Aby odczytać wszystkie kolumny w plikach danych CSV, podaj nazwy kolumn i ich typy danych. Jeśli chcesz użyć podzestawu kolumn, użyj liczb porządkowych, aby wybrać kolumny z plików danych pochodzących według liczby porządkowej. Kolumny będą powiązane przez oznaczenie porządkowe. Jeśli HEADER_ROW = TRUE, powiązanie kolumny jest wykonywane według nazwy kolumny zamiast pozycji porządkowej.
    > [!TIP]
    > Możesz również pominąć klauzulę WITH dla plików CSV. Typy danych będą automatycznie wywnioskowane z zawartości pliku. Możesz użyć argumentu HEADER_ROW, aby określić istnienie wiersza nagłówka, w którym to przypadku nazwy kolumn będą odczytywane z wiersza nagłówka. Aby uzyskać szczegółowe informacje, [sprawdź automatyczne odnajdywanie schematów.](#automatic-schema-discovery)
    
- W przypadku plików danych Parquet podaj nazwy kolumn zgodne z nazwami kolumn w plikach danych pochodzących z programu . Kolumny będą powiązane według nazwy i będzie zróżnicowyana wielkość liter. Jeśli klauzula WITH zostanie pominięta, zostaną zwrócone wszystkie kolumny z plików Parquet.
    > [!IMPORTANT]
    > W nazwach kolumn w plikach Parquet jest wielkość liter. Jeśli określisz nazwę kolumny z wielkością inną niż nazwa kolumny w pliku Parquet, dla tej kolumny zostaną zwrócone wartości NULL.


column_name = nazwa kolumny wyjściowej. Jeśli zostanie podany, ta nazwa zastąpi nazwę kolumny w pliku źródłowym i nazwę kolumny podaną w ścieżce JSON, jeśli istnieje. Jeśli json_path nie zostanie podany, zostanie on automatycznie dodany jako "$.column_name". Sprawdź json_path argumentu zachowania.

column_type = Typ danych dla kolumny wyjściowej. W tym miejscu będzie miała miejsce niejawna konwersja typu danych.

column_ordinal = liczba porządkowa kolumny w plikach źródłowych. Ten argument jest ignorowany w przypadku plików Parquet, ponieważ powiązanie jest wykonywane według nazwy. Poniższy przykład zwróci drugą kolumnę tylko z pliku CSV:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

json_path = [wyrażenie ścieżki JSON do](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) kolumny lub zagnieżdżonych właściwości. Domyślny [tryb ścieżki to](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true#PATHMODE) lax.

> [!NOTE]
> Zapytanie w trybie ścisłym nie powiedzie się z błędem, jeśli podana ścieżka nie istnieje. W trybie lax zapytanie zakończy się powodzeniem, a wyrażenie ścieżki JSON będzie mieć wartość NULL.

**\<bulk_options>**

FIELDTERMINATOR ='field_terminator'

Określa terminator pola, który ma być używany. Domyślnym terminatorem pola jest przecinek ("**,**").

ROWTERMINATOR ='row_terminator''

Określa terminator wiersza, który ma być używany. Jeśli terminator wiersza nie zostanie określony, zostanie użyty jeden z domyślnych terminatorów. Domyślne terminatory dla PARSER_VERSION = "1.0" to \r\n, \n i \r. Domyślne terminatory dla PARSER_VERSION = "2.0" to \r\n i \n.

ESCAPE_CHAR = 'char'

Określa znak w pliku, który jest używany do ucieczki samego siebie i wszystkie wartości ogranicznika w pliku. Jeśli po znaku ucieczki następuje wartość inna niż sama lub dowolna z wartości ogranicznika, znak ucieczki jest porzucany podczas odczytywania wartości. 

Parametr ESCAPE_CHAR zostanie zastosowany niezależnie od tego, czy parametr FIELDQUOTE jest włączony, czy nie. Nie będzie on używany do ucieczki znaku cudzysłowego. Znak cudzysłowy musi być znak ucieczki z innym znakiem cudzysłowy. Znak cudzysłowy może występować w wartości kolumny tylko wtedy, gdy wartość jest hermetyzowana z cudzysłowy.

FIRSTROW = 'first_row' 

Określa liczbę pierwszego wiersza do załadowania. Wartość domyślna to 1 i wskazuje pierwszy wiersz w określonym pliku danych. Numery wierszy są określane przez zliczanie terminatorów wierszy. FirstROW jest oparty na 1.

FIELDQUOTE = 'field_quote' 

Określa znak, który będzie używany jako znak cudzysłowu w pliku CSV. Jeśli nie zostanie określony, zostanie użyty znak cudzysłowu ("). 

DATA_COMPRESSION = "data_compression_method"

Określa metodę kompresji. Obsługiwane tylko PARSER_VERSION ='1.0'. Obsługiwana jest następująca metoda kompresji:

- Gzip

PARSER_VERSION = "parser_version"

Określa wersję parsera, która ma być używana podczas odczytywania plików. Obecnie obsługiwane wersje parsera CSV to 1.0 i 2.0:

- PARSER_VERSION = '1.0'
- PARSER_VERSION = '2.0'

Program CSV Parser w wersji 1.0 jest wersją domyślną i zawiera rozbudowane funkcje. Wersja 2.0 została s zbudowana z myślą o wydajności i nie obsługuje wszystkich opcji i kodowań. 

Specyfika parsera CSV w wersji 1.0:

- Następujące opcje nie są obsługiwane: HEADER_ROW.

Specyfika parsera CSV w wersji 2.0:

- Nie wszystkie typy danych są obsługiwane.
- Maksymalna długość kolumny znaków to 8000.
- Maksymalny limit rozmiaru wiersza to 8 MB.
- Następujące opcje nie są obsługiwane: DATA_COMPRESSION.
- Pusty ciąg w cudzysłowy ("") jest interpretowany jako pusty ciąg.
- Obsługiwany format danych DATE: YYYY-MM-DD
- Obsługiwany format dla typu danych TIME: HH:MM:SS[.fractional seconds]
- Obsługiwany format dla typu danych DATETIME2: YYYY-MM-DD HH:MM:SS[.fractional seconds]

HEADER_ROW = { TRUE | FALSE }

Określa, czy plik CSV zawiera wiersz nagłówka. Wartość domyślna to FALSE. Obsługiwane w PARSER_VERSION='2.0'. W przypadku wartości TRUE nazwy kolumn będą odczytywane z pierwszego wiersza zgodnie z argumentem FIRSTROW. Jeśli wartość TRUE i schemat zostaną określone przy użyciu funkcji WITH, powiązanie nazw kolumn będzie wykonywane według nazwy kolumny, a nie pozycji porządkowych.

DATAFILETYPE = { 'char' | "widechar" }

Określa kodowanie: znak jest używany dla utf8, widechar jest używany dla plików UTF16.

CODEPAGE = { 'ACP' | "OEM" | "NIEPRZETWORZONE" | "code_page" }

Określa stronę kodową danych w pliku danych. Wartość domyślna to 65001 (kodowanie UTF-8). Więcej szczegółów na temat tej opcji można [znaleźć tutaj.](/sql/t-sql/functions/openrowset-transact-sql?view=sql-server-ver15&preserve-view=true#codepage)

## <a name="fast-delimited-text-parsing"></a>Szybkie analizowanie tekstu rozdzielanego

Istnieją dwie wersje rozdzielanego parsera tekstu, których można użyć. Program CSV Parser w wersji 1.0 jest wersją domyślną i zawiera rozbudowane funkcje, podczas gdy parser w wersji 2.0 jest zbudowany z myślą o wydajności. Poprawa wydajności w parserze 2.0 wynika z zaawansowanych technik analizy i wielowątkowych. Różnica w szybkości będzie większa w miarę wzrostu rozmiaru pliku.

## <a name="automatic-schema-discovery"></a>Automatyczne odnajdywanie schematów

Można łatwo odpytować pliki CSV i Parquet bez znajomości ani określania schematu przez pominięcie klauzuli WITH. Nazwy kolumn i typy danych będą wywnioskować z plików.

Pliki Parquet zawierają metadane kolumn, które będą odczytywane. Mapowania typów można znaleźć w mapowaniach typów [dla parquet](#type-mapping-for-parquet). Sprawdź [odczytywanie plików Parquet bez określania schematu przykładów.](#read-parquet-files-without-specifying-schema)

Nazwy kolumn plików CSV można odczytać z wiersza nagłówka. Możesz określić, czy wiersz nagłówka istnieje, używając HEADER_ROW argumentu. Jeśli HEADER_ROW = FALSE, zostaną użyte ogólne nazwy kolumn: C1, C2, ... Cn, gdzie n to liczba kolumn w pliku. Typy danych zostaną wywnioskować z pierwszych 100 wierszy danych. Sprawdź [odczytywanie plików CSV bez określania schematu](#read-csv-files-without-specifying-schema) przykładów.

> [!IMPORTANT]
> Istnieją przypadki, w których nie można wywnioskować odpowiedniego typu danych z powodu braku informacji i zamiast tego zostanie użyty większy typ danych. Prowadzi to do narzutu na wydajność i jest szczególnie ważne w przypadku kolumn znaków, które zostaną wywnioskować jako varchar(8000). Aby uzyskać optymalną wydajność, [sprawdź wywnioskowane typy danych i](best-practices-sql-on-demand.md#check-inferred-data-types) użyj odpowiednich typów [danych.](best-practices-sql-on-demand.md#use-appropriate-data-types)

### <a name="type-mapping-for-parquet"></a>Mapowanie typów dla usługi Parquet

Pliki Parquet zawierają opisy typów dla każdej kolumny. W poniższej tabeli opisano sposób mapowania typów Parquet na typy natywne SQL.

| Typ Parquet | Typ logiczny Parquet (adnotacja) | Typ danych SQL |
| --- | --- | --- |
| Boolean | | bit |
| DANE BINARNE /BYTE_ARRAY | | varbinary |
| Podwójne | | float |
| Float | | liczba rzeczywista |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binarny |
| Binarnym |UTF8 |varchar \* (sortowanie UTF8) |
| Binarnym |Ciąg |varchar \* (sortowanie UTF8) |
| Binarnym |Enum|varchar \* (sortowanie UTF8) |
| FIXED_LEN_BYTE_ARRAY |Uuid |uniqueidentifier |
| Binarnym |Dziesiętnych |decimal |
| Binarnym |JSON |varchar(8000) \* (sortowanie UTF8) |
| Binarnym |Bson | Nieobsługiwane |
| FIXED_LEN_BYTE_ARRAY |Dziesiętnych |decimal |
| BYTE_ARRAY |Interwał | Nieobsługiwane |
| INT32 |INT(8, true) |smallint |
| INT32 |INT(16, true) |smallint |
| INT32 |INT(32, true) |int |
| INT32 |INT(8, false) |tinyint |
| INT32 |INT(16, false) |int |
| INT32 |INT(32, false) |bigint |
| INT32 |DATE |data |
| INT32 |Dziesiętnych |decimal |
| INT32 |TIME (MILLIS)|time |
| INT64 |INT(64, true) |bigint |
| INT64 |INT(64, false) |decimal(20,0) |
| INT64 |Dziesiętnych |decimal |
| INT64 |TIME (MICROS) |time — time (NANOS) nie jest obsługiwany |
|INT64 |SYGNATURA CZASOWA (MILLIS/MICROS) |datetime2 — znacznik czasu (NANOS) nie jest obsługiwany |
|[Typ złożony](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |Listy |varchar(8000), serializowana do JSON |
|[Typ złożony](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|Mapę|varchar(8000), serializowana do JSON |

## <a name="examples"></a>Przykłady

### <a name="read-csv-files-without-specifying-schema"></a>Odczytywanie plików CSV bez określania schematu

Poniższy przykład odczytuje plik CSV zawierający wiersz nagłówka bez określania nazw kolumn i typów danych: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0',
    HEADER_ROW = TRUE) as [r]
```

Poniższy przykład odczytuje plik CSV, który nie zawiera wiersza nagłówka bez określania nazw kolumn i typów danych: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0') as [r]
```

### <a name="read-parquet-files-without-specifying-schema"></a>Odczytywanie plików Parquet bez określania schematu

Poniższy przykład zwraca wszystkie kolumny pierwszego wiersza z zestawu danych spisu, w formacie Parquet i bez określania nazw kolumn i typów danych: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

### <a name="read-specific-columns-from-csv-file"></a>Odczytywanie określonych kolumn z pliku CSV

Poniższy przykład zwraca tylko dwie kolumny z liczbami porządkowymi 1 i 4 z plików population*.csv. Ponieważ w plikach nie ma wiersza nagłówka, rozpoczyna on odczytywanie od pierwszego wiersza:

```sql
SELECT 
    * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```

### <a name="read-specific-columns-from-parquet-file"></a>Odczytywanie określonych kolumn z pliku Parquet

Poniższy przykład zwraca tylko dwie kolumny pierwszego wiersza z zestawu danych spisu w formacie Parquet: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    [stateName] VARCHAR (50),
    [population] bigint
) AS [r]
```

### <a name="specify-columns-using-json-paths"></a>Określanie kolumn przy użyciu ścieżek JSON

W poniższym przykładzie pokazano, jak używać wyrażeń ścieżki [JSON](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) w klauzuli WITH i przedstawiono różnicę między trybami ścieżki ścisłej i lax: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    --lax path mode samples
    [stateName] VARCHAR (50), -- this one works as column name casing is valid - it targets the same column as the next one
    [stateName_explicit_path] VARCHAR (50) '$.stateName', -- this one works as column name casing is valid
    [COUNTYNAME] VARCHAR (50), -- STATEname column will contain NULLs only because of wrong casing - it targets the same column as the next one
    [countyName_explicit_path] VARCHAR (50) '$.COUNTYNAME', -- STATEname column will contain NULLS only because of wrong casing and default path mode being lax

    --strict path mode samples
    [population] bigint 'strict $.population' -- this one works as column name casing is valid
    --,[population2] bigint 'strict $.POPULATION' -- this one fails because of wrong casing and strict path mode
)
AS [r]
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów, zobacz [przewodnik](query-data-storage.md) Szybki start dla magazynu danych zapytań, aby dowiedzieć się, jak odczytywać formaty `OPENROWSET` plików [CSV,](query-single-csv-file.md) [PARQUET](query-parquet-files.md)i [JSON.](query-json-files.md) Sprawdź [najlepsze rozwiązania w celu](best-practices-sql-on-demand.md) osiągnięcia optymalnej wydajności. Możesz również dowiedzieć się, jak zapisać wyniki zapytania w usłudze Azure Storage przy użyciu [funkcji CETAS.](develop-tables-cetas.md)