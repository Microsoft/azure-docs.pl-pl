---
title: Jak korzystać z funkcji OPENROWSET w puli SQL bezserwerowej
description: W tym artykule opisano składnię OPENROWSET w puli SQL bezserwerowej i wyjaśniono, jak używać argumentów.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 9fd10d6a4fb748a61b5e1d9e27777c2fa1134039
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2021
ms.locfileid: "99225617"
---
# <a name="how-to-use-openrowset-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Jak używać funkcji OPENROWSET przy użyciu bezserwerowej puli SQL w usłudze Azure Synapse Analytics

`OPENROWSET(BULK...)`Funkcja umożliwia dostęp do plików w usłudze Azure Storage. `OPENROWSET` Funkcja odczytuje zawartość zdalnego źródła danych (na przykład pliku) i zwraca zawartość jako zestaw wierszy. W ramach zasobu puli SQL bezserwerowej można uzyskać dostęp do dostawcy zestawów wierszy zbiorczych OPENROWSET, wywołując funkcję OPENROWSET i określając opcję ZBIORCZą.  

Do `OPENROWSET` funkcji można odwoływać się w `FROM` klauzuli zapytania, tak jakby była nazwą tabeli `OPENROWSET` . Obsługuje operacje zbiorcze za pośrednictwem wbudowanego dostawcy ZBIORCZego, który umożliwia odczytywanie danych z pliku i zwracanie ich jako zestawu wierszy.

## <a name="data-source"></a>Źródło danych

Funkcja OPENROWSET w Synapse SQL odczytuje zawartość plików ze źródła danych. Źródło danych jest kontem usługi Azure Storage i może być jawnie przywoływane w `OPENROWSET` funkcji lub może być dynamicznie wywnioskowane z adresu URL plików, które mają zostać odczytane.
`OPENROWSET`Funkcja może opcjonalnie zawierać parametr, `DATA_SOURCE` Aby określić źródło danych, które zawiera pliki.
- `OPENROWSET` bez `DATA_SOURCE` może służyć do bezpośredniego odczytywania zawartości plików z lokalizacji URL określonej jako `BULK` Opcja:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://<storage account>.dfs.core.windows.net/container/folder/*.parquet',
                    FORMAT = 'PARQUET') AS file
    ```

Jest to szybka i łatwa metoda odczytywania zawartości plików bez wstępnej konfiguracji. Ta opcja umożliwia korzystanie z opcji uwierzytelniania podstawowego w celu uzyskania dostępu do magazynu (usługa Azure AD Passthrough dla nazw logowania usługi Azure AD i token sygnatury dostępu współdzielonego dla logowań SQL). 

- `OPENROWSET` Program with `DATA_SOURCE` umożliwia dostęp do plików na określonym koncie magazynu:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    FORMAT = 'PARQUET') AS file
    ```


    Ta opcja umożliwia skonfigurowanie lokalizacji konta magazynu w źródle danych i określenie metody uwierzytelniania, która ma być używana do uzyskiwania dostępu do magazynu. 
    
    > [!IMPORTANT]
    > `OPENROWSET` bez `DATA_SOURCE` zapewnia szybki i łatwy sposób uzyskiwania dostępu do plików magazynu, ale oferuje ograniczoną liczbę opcji uwierzytelniania. Na przykład podmioty zabezpieczeń usługi Azure AD mogą uzyskiwać dostęp do plików tylko przy użyciu [tożsamości usługi Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity) lub publicznie dostępnych plików. Jeśli potrzebujesz bardziej zaawansowanych opcji uwierzytelniania, użyj `DATA_SOURCE` opcji i zdefiniuj poświadczenia, które mają być używane do uzyskiwania dostępu do magazynu.


## <a name="security"></a>Zabezpieczenia

Użytkownik bazy danych musi mieć `ADMINISTER BULK OPERATIONS` uprawnienia do korzystania z `OPENROWSET` funkcji.

Administrator magazynu musi także umożliwić użytkownikowi dostęp do plików przez udostępnienie prawidłowego tokenu SAS lub włączenie podmiotu zabezpieczeń usługi Azure AD w celu uzyskania dostępu do plików magazynu. Dowiedz się więcej o kontroli dostępu do magazynu w [tym artykule](develop-storage-files-storage-access-control.md).

`OPENROWSET` Użyj następujących reguł, aby określić sposób uwierzytelniania do magazynu:
- W programie `OPENROWSET` bez `DATA_SOURCE` mechanizmu uwierzytelniania zależy od typu obiektu wywołującego.
  - Każdy użytkownik może używać `OPENROWSET` bez `DATA_SOURCE` konieczności odczytywania publicznie dostępnych plików w usłudze Azure Storage.
  - Identyfikatory logowania usługi Azure AD mogą uzyskiwać dostęp do chronionych plików przy użyciu własnej [tożsamości usługi Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) , jeśli usługa Azure Storage umożliwia użytkownikowi usługi Azure AD dostęp do plików źródłowych (na przykład jeśli obiekt wywołujący ma `Storage Reader` uprawnienia do usługi Azure Storage).
  - Identyfikatory logowania SQL mogą być również używane `OPENROWSET` bez `DATA_SOURCE` uzyskiwania dostępu do publicznie dostępnych plików, plików chronionych przy użyciu tokenu SAS lub zarządzanej tożsamości obszaru roboczego Synapse. Należy [utworzyć poświadczenia o zakresie serwera](develop-storage-files-storage-access-control.md#examples) , aby umożliwić dostęp do plików magazynu. 
- W programie `OPENROWSET` z `DATA_SOURCE` mechanizmem uwierzytelniania jest zdefiniowany w poświadczeniach o zakresie bazy danych przypisanych do źródła danych, do którego się odwołuje. Ta opcja umożliwia dostęp do dostępnego publicznie magazynu lub dostęp do magazynu przy użyciu tokenu SAS, zarządzanej tożsamości obszaru roboczego lub [tożsamości obiektu wywołującego usługi Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) (Jeśli obiekt wywołujący jest podmiotem zabezpieczeń usługi Azure AD). Jeśli `DATA_SOURCE` odwołuje się do usługi Azure Storage, która nie jest publiczna, należy [utworzyć poświadczenia w zakresie bazy danych](develop-storage-files-storage-access-control.md#examples) i odwołać się do nich w `DATA SOURCE` celu zezwolenia na dostęp do plików magazynu.

Obiekt wywołujący musi mieć `REFERENCES` uprawnienia do poświadczeń, aby można było go użyć do uwierzytelnienia w magazynie.

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
```

## <a name="arguments"></a>Argumenty

Dostępne są dwie opcje dla plików wejściowych, które zawierają dane docelowe do wykonywania zapytań. Prawidłowe wartości:

- "CSV" — zawiera dowolny rozdzielany plik tekstowy z separatorami wierszy/kolumn. Dowolny znak może być używany jako separator pola, na przykład TSV: FIELDTERMINATOR = Tab.

- "PARQUET" — plik binarny w formacie Parquet 

**"unstructured_data_path"**

Unstructured_data_path, który ustanawia ścieżkę do danych może być ścieżką bezwzględną lub względną:
- Ścieżka bezwzględna w formacie " \<prefix> :// \<storage_account_path> / \<storage_path> " umożliwia użytkownikowi bezpośrednie odczytywanie plików.
- Ścieżka względna w formacie "<storage_path>", która musi być używana z `DATA_SOURCE` parametrem i opisuje wzorzec pliku w lokalizacji <storage_account_path> zdefiniowanej w `EXTERNAL DATA SOURCE` . 

Poniżej znajdziesz odpowiednie <storage account path> wartości, które zostaną połączone z określonym zewnętrznym źródłem danych. 

| Zewnętrzne źródło danych       | Prefiks | Ścieżka konta magazynu                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | http [s]  | \<storage_account>. blob.core.windows.net/path/file   |
| Azure Blob Storage         | wasb [s]  | \<container>@\<storage_account>. blob.core.windows.net/path/file |
| Azure Data Lake Store Gen1 | http [s]  | \<storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | http [s]  | \<storage_account>. dfs.core.windows.net/Path/File   |
| Azure Data Lake Store Gen2 | ABFS [s]  | [\<file_system>@\<account_name>. dfs.core.windows.net/path/file](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md#uri-syntax)              |
||||

'\<storage_path>'

Określa ścieżkę w magazynie, która wskazuje folder lub plik, który ma zostać odczytany. Jeśli ścieżka wskazuje kontener lub folder, wszystkie pliki zostaną odczytane z danego kontenera lub folderu. Pliki w podfolderach nie będą uwzględniane. 

Możesz użyć symboli wieloznacznych, aby docelowa była wiele plików lub folderów. Dozwolone jest użycie wielu niesąsiadujących symboli wieloznacznych.
Poniżej znajduje się przykład, który odczytuje wszystkie pliki *CSV* zaczynające się od *populacji* ze wszystkich folderów zaczynających się od */CSV/Population*:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Jeśli określisz unstructured_data_path jako folder, zapytanie puli SQL bezserwerowe pobierze pliki z tego folderu. 

Można nakazać bezserwerowej puli SQL przechodzenie między folderami, określając/* na końcu ścieżki, jak na przykład: `https://sqlondemandstorage.blob.core.windows.net/csv/population/**`

> [!NOTE]
> W przeciwieństwie do usługi Hadoop i bazy danych, bezserwerowa Pula SQL nie zwraca podfolderów, chyba że na końcu ścieżki nie określono/* *. Ponadto, w przeciwieństwie do usługi Hadoop i Base, bezserwerowa Pula SQL zwraca pliki, dla których nazwa pliku zaczyna się od znaku podkreślenia (_) lub kropki (.).

W poniższym przykładzie, jeśli unstructured_data_path = `https://mystorageaccount.dfs.core.windows.net/webdata/` , bezserwerowe zapytanie puli SQL zwróci wiersze z mydata.txt i _hidden.txt. Nie zwróci mydata2.txt i mydata3.txt, ponieważ znajdują się w podfolderze.

![Dane cykliczne dla tabel zewnętrznych](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

Klauzula WITH umożliwia określenie kolumn, które mają być odczytywane z plików.

- W przypadku plików danych CSV odczytywanie wszystkich kolumn, dostarczanie nazw kolumn i ich typów danych. Jeśli chcesz podzbiór kolumn, Użyj numerów porządkowych, aby wybrać kolumny z plików danych źródłowych według liczby porządkowej. Kolumny będą powiązane z oznaczeniem porządkowym. Jeśli jest używana wartość HEADER_ROW = TRUE, powiązanie kolumny jest wykonywane według nazwy kolumny zamiast pozycji porządkowej.
    > [!TIP]
    > Można również pominąć klauzulę WITH dla plików CSV. Typy danych zostaną automatycznie wywnioskowane z zawartości pliku. Można użyć argumentu HEADER_ROW, aby określić istnienie wiersza nagłówka, w którym nazwy kolumn przypadków będą odczytywane z wiersza nagłówka. Aby uzyskać szczegółowe informacje, sprawdź [automatyczne odnajdowanie schematów](#automatic-schema-discovery).
    
- W przypadku plików danych Parquet Podaj nazwy kolumn, które pasują do nazw kolumn w źródłowych plikach danych. Kolumny będą powiązane według nazwy i uwzględniają wielkość liter. Jeśli klauzula WITH zostanie pominięta, zostaną zwrócone wszystkie kolumny z plików Parquet.
    > [!IMPORTANT]
    > Nazwy kolumn w plikach Parquet uwzględniają wielkość liter. Jeśli określisz nazwę kolumny z wielkością liter inną niż nazwa kolumny w pliku Parquet, wartości NULL zostaną zwrócone dla tej kolumny.


column_name = nazwa kolumny wyjściowej. W przypadku podanej nazwy zastępuje ona nazwę kolumny w pliku źródłowym i w nazwie kolumny podanej w ścieżce JSON, jeśli istnieje. Jeśli nie podano json_path, zostanie ona automatycznie dodana jako "$ .column_name". Sprawdź json_path argument w celu zachowania.

column_type = typ danych dla kolumny wyjściowej. Niejawna konwersja typu danych zostanie przeprowadzona tutaj.

column_ordinal = numer porządkowy kolumny w plikach źródłowych. Ten argument jest ignorowany dla plików Parquet, ponieważ wiązanie jest wykonywane według nazwy. Poniższy przykład zwróci drugą kolumnę tylko z pliku CSV:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

json_path = [wyrażenie ścieżki JSON](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) do kolumny lub właściwości zagnieżdżonej. Domyślny [tryb ścieżki](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true#PATHMODE) to swobodny.

> [!NOTE]
> W zapytaniu w trybie Strict nie powiedzie się z błędem, jeśli podana ścieżka nie istnieje. Zapytanie w trybie swobodny zostanie wykonane pomyślnie, a wyrażenie ścieżki JSON zwróci wartość NULL.

**\<bulk_options>**

FIELDTERMINATOR = "field_terminator"

Określa terminator pola do użycia. Domyślny terminator pola jest przecinkiem ("**,**").

ROWTERMINATOR = "row_terminator" "

Określa terminator wiersza, który ma być używany. Jeśli terminator wiersza nie zostanie określony, zostanie użyty jeden z domyślnych terminatorów. Domyślne terminatory dla PARSER_VERSION = "1,0" to \r\n, \n i \r. Domyślne terminatory dla PARSER_VERSION = "2,0" to \r\n i \n.

ESCAPE_CHAR = "char"

Określa znak w pliku, który jest używany do wyprowadzania samego siebie i wszystkich wartości ogranicznika w pliku. Jeśli po znaku ucieczki następuje wartość inna niż sama lub jakakolwiek z wartości ogranicznika, znak ucieczki jest usuwany podczas odczytywania wartości. 

Parametr ESCAPE_CHAR zostanie zastosowany niezależnie od tego, czy FIELDQUOTE jest czy nie jest włączony. Nie będzie on używany do ucieczki znaku cudzysłowu. Znak cudzysłowu musi być zmieniony przy użyciu innego znaku cudzysłowu. Znak quota może pojawić się w wartości kolumny tylko wtedy, gdy wartość jest hermetyzowana przy użyciu znaków cudzysłowu.

FIRSTROW = "first_row" 

Określa numer pierwszego wiersza do załadowania. Wartość domyślna to 1 i wskazuje pierwszy wiersz w określonym pliku danych. Numery wierszy są określane przez liczenie terminatorów wierszy. FIRSTROW jest oparty na 1.

FIELDQUOTE = "field_quote" 

Określa znak, który będzie używany jako znak cudzysłowu w pliku CSV. Jeśli nie zostanie określony, zostanie użyty znak cudzysłowu ("). 

DATA_COMPRESSION = "data_compression_method"

Określa metodę kompresji. Obsługiwane tylko w PARSER_VERSION = "1.0". Obsługiwana jest następująca metoda kompresji:

- GZIP

PARSER_VERSION = "parser_version"

Określa wersję parsera, która ma być używana podczas odczytywania plików. Obecnie obsługiwane wersje analizatora woluminów CSV to 1,0 i 2,0:

- PARSER_VERSION = "1,0"
- PARSER_VERSION = "2,0"

Analizator CSV w wersji 1,0 jest domyślny i bogaty w funkcję. Wersja 2,0 została skompilowana z myślą o wydajności i nie obsługuje wszystkich opcji i kodowań. 

Specyficzne dla analizatora CSV wersja 1,0:

- Następujące opcje nie są obsługiwane: HEADER_ROW.

Specyficzne dla analizatora CSV wersja 2,0:

- Nie wszystkie typy danych są obsługiwane.
- Maksymalna długość kolumny znaku to 8000.
- Maksymalny rozmiar wiersza to 8 MB.
- Następujące opcje nie są obsługiwane: DATA_COMPRESSION.
- Pusty ciąg w cudzysłowie ("") jest interpretowany jako pusty ciąg.
- Obsługiwany format typu danych Data: RRRR-MM-DD
- Obsługiwany format typu danych TIME: HH: MM: SS [. ułamkowe sekundy]
- Obsługiwany format dla typu danych DATETIME2: RRRR-MM-DD GG: MM: SS [. ułamkowe sekundy]

HEADER_ROW = {TRUE | FALSE

Określa, czy plik CSV zawiera wiersz nagłówka. Wartość domyślna to FALSE. Obsługiwane w PARSER_VERSION = "2.0". Jeśli wartość jest równa TRUE, nazwy kolumn są odczytywane z pierwszego wiersza zgodnie z argumentem FIRSTROW. Jeśli wartość jest równa TRUE i schemat jest określony przy użyciu WITH, powiązanie nazw kolumn będzie odbywać się według nazwy kolumny, a nie pozycji porządkowej.

DataFileType = {"char" | "widechar"}

Określa kodowanie: char jest używany dla UTF8, widechar jest używany dla plików UTF16.

## <a name="fast-delimited-text-parsing"></a>Szybkie rozdzielone analizowanie tekstu

Istnieją dwa rozdzielone wersje analizatora tekstu, których można użyć. Analizator woluminów CSV w wersji 1,0 jest domyślny i jest bogaty, a w przypadku usługi parser w wersji 2,0 jest tworzona dla wydajności. Poprawa wydajności w analizatorze 2,0 pochodzi z zaawansowanych technik analizy i wielowątkowości. Różnica w szybkości będzie większa w miarę zwiększania się rozmiaru pliku.

## <a name="automatic-schema-discovery"></a>Automatyczne odnajdowanie schematów

Można łatwo badać pliki CSV i Parquet bez znajomości lub określania schematu, pomijając klauzulę WITH. Nazwy kolumn i typy danych zostaną wywnioskowane na podstawie plików.

Pliki Parquet zawierają metadane kolumn, które zostaną odczytane, mapowania typów można znaleźć w [mapowaniach typów dla Parquet](#type-mapping-for-parquet). Sprawdź [odczytywanie plików Parquet bez określania schematu](#read-parquet-files-without-specifying-schema) dla przykładów.

Nazwy kolumn dla plików CSV można odczytywać z wiersza nagłówka. Można określić, czy wiersz nagłówka istnieje przy użyciu argumentu HEADER_ROW. Jeśli HEADER_ROW = FALSE, zostaną użyte ogólne nazwy kolumn: C1, C2,... Nazwa pospolita, gdzie n jest liczbą kolumn w pliku. Typy danych zostaną wywnioskowane z pierwszych 100 wierszy danych. Sprawdź [odczytywanie plików CSV bez określania schematu](#read-csv-files-without-specifying-schema) dla przykładów.

> [!IMPORTANT]
> Istnieją przypadki, w których nie można wywnioskować odpowiedniego typu danych z powodu braku informacji, a zamiast tego zostanie użyty większy typ danych. Zapewnia to obciążenie wydajności i jest szczególnie ważne w przypadku kolumn znaków, które zostaną wywnioskowane jako varchar (8000). Aby uzyskać optymalną wydajność, [Sprawdź wywnioskowane typy danych](best-practices-sql-on-demand.md#check-inferred-data-types) i [Używaj odpowiednich typów danych](best-practices-sql-on-demand.md#use-appropriate-data-types).

### <a name="type-mapping-for-parquet"></a>Mapowanie typu dla Parquet

Pliki Parquet zawierają opisy typów dla każdej kolumny. W poniższej tabeli opisano, jak typy Parquet są mapowane na typy natywne języka SQL.

| Typ Parquet | Parquet — typ logiczny (Adnotacja) | Typ danych SQL |
| --- | --- | --- |
| TYPU | | bit |
| DANE BINARNE/BYTE_ARRAY | | varbinary |
| DOUBLE | | float |
| FLOAT | | liczba rzeczywista |
| ELEMENTEM | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binarny |
| BINARNY |KODOWANIA |varchar \* (sortowanie UTF8) |
| BINARNY |PARAMETRY |varchar \* (sortowanie UTF8) |
| BINARNY |PODSTAWOWE|varchar \* (sortowanie UTF8) |
| FIXED_LEN_BYTE_ARRAY |INTERFEJSU |uniqueidentifier |
| BINARNY |DOKŁADNOŚCI |decimal |
| BINARNY |JSON |varchar (8000) \* (sortowanie UTF8) |
| BINARNY |BSON | Nieobsługiwane |
| FIXED_LEN_BYTE_ARRAY |DOKŁADNOŚCI |decimal |
| BYTE_ARRAY |DAT | Nieobsługiwane |
| ELEMENTEM |INT (8, prawda) |smallint |
| ELEMENTEM |INT (16, true) |smallint |
| ELEMENTEM |INT (32, true) |int |
| ELEMENTEM |INT (8, FAŁSZ) |tinyint |
| ELEMENTEM |INT (16, FAŁSZ) |int |
| ELEMENTEM |INT (32, false) |bigint |
| ELEMENTEM |DATE |data |
| ELEMENTEM |DOKŁADNOŚCI |decimal |
| ELEMENTEM |CZAS (MŁYNER)|time |
| INT64 |INT (64, true) |bigint |
| INT64 |INT (64, false) |Liczba dziesiętna (20, 0) |
| INT64 |DOKŁADNOŚCI |decimal |
| INT64 |CZAS (MICROS) |czas czasu (NANOs) nie jest obsługiwany |
|INT64 |SYGNATURA CZASOWA (MILL/MICROS) |datetime2 — SYGNATURa CZASowa (NANOs) nie jest obsługiwana |
|[Typ złożony](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |STAW |varchar (8000), serializacja do formatu JSON |
|[Typ złożony](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|ZMAPOWAĆ|varchar (8000), serializacja do formatu JSON |

## <a name="examples"></a>Przykłady

### <a name="read-csv-files-without-specifying-schema"></a>Odczytaj pliki CSV bez określania schematu

Poniższy przykład odczytuje plik CSV, który zawiera wiersz nagłówka bez określania nazw kolumn i typów danych: 

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

### <a name="read-parquet-files-without-specifying-schema"></a>Odczytaj pliki Parquet bez określania schematu

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

### <a name="read-specific-columns-from-csv-file"></a>Odczytaj określone kolumny z pliku CSV

Poniższy przykład zwraca tylko dwie kolumny z numerami porządkowymi 1 i 4 z plików Population*. csv. Ponieważ w plikach nie ma wiersza nagłówka, rozpocznie się odczytywanie z pierwszego wiersza:

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

### <a name="read-specific-columns-from-parquet-file"></a>Odczytaj określone kolumny z pliku Parquet

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

Poniższy przykład pokazuje, jak można użyć [wyrażeń ścieżki JSON](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) w klauzuli with i ilustruje różnicę między trybami ścieżki ścisłej i swobodny: 

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

Aby uzyskać więcej przykładów, zobacz [Przewodnik Szybki Start dotyczący usługi Query Data Storage](query-data-storage.md) , aby dowiedzieć się, jak używać `OPENROWSET` programu do odczytywania formatów plików [CSV](query-single-csv-file.md), [PARQUET](query-parquet-files.md)i [JSON](query-json-files.md) . Zapoznaj się z [najlepszymi rozwiązaniami](best-practices-sql-on-demand.md) w celu uzyskania optymalnej wydajności. Możesz również dowiedzieć się, jak zapisać wyniki zapytania w usłudze Azure Storage przy użyciu [CETAS](develop-tables-cetas.md).
