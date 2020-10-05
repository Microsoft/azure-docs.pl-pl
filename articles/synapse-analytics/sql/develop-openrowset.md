---
title: Jak używać funkcji OPENROWSET w programie SQL na żądanie (wersja zapoznawcza)
description: W tym artykule opisano składnię funkcji OPENROWSET w SQL na żądanie (wersja zapoznawcza) i wyjaśniono, jak używać argumentów.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: e541a5620d4f263e5e1379b364d7c7dd9a97a331
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91289025"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Jak używać funkcji OPENROWSET z SQL na żądanie (wersja zapoznawcza)

`OPENROWSET(BULK...)`Funkcja umożliwia dostęp do plików w usłudze Azure Storage. `OPENROWSET` Funkcja odczytuje zawartość zdalnego źródła danych (na przykład pliku) i zwraca zawartość jako zestaw wierszy. W ramach zasobu SQL na żądanie (wersja zapoznawcza) dostawca zestawu wierszy zbiorczych OPENROWSET jest dostępny przez wywołanie funkcji OPENROWSET i określenie opcji BULK.  

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
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
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
| Azure Data Lake Store Gen2 | Aufs [s]  | [\<file_system>@\<account_name>. dfs.core.windows.net/path/file](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md#uri-syntax)              |
||||

'\<storage_path>'

 Określa ścieżkę w magazynie, która wskazuje folder lub plik, który ma zostać odczytany. Jeśli ścieżka wskazuje kontener lub folder, wszystkie pliki zostaną odczytane z danego kontenera lub folderu. Pliki w podfolderach nie będą uwzględniane. 

 Możesz użyć symboli wieloznacznych, aby docelowa była wiele plików lub folderów. Dozwolone jest użycie wielu niesąsiadujących symboli wieloznacznych.
Poniżej znajduje się przykład, który odczytuje wszystkie pliki *CSV* zaczynające się od *populacji* ze wszystkich folderów zaczynających się od */CSV/Population*:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Jeśli określisz unstructured_data_path jako folder, zapytanie SQL na żądanie pobierze pliki z tego folderu. 

> [!NOTE]
> W przeciwieństwie do usługi Hadoop i bazy danych SQL na żądanie nie zwraca podfolderów. Ponadto, w przeciwieństwie do platformy Hadoop i bazy danych SQL na żądanie, zwraca pliki, dla których nazwa pliku zaczyna się od podkreślenia (_) lub kropki (.).

W poniższym przykładzie, jeśli unstructured_data_path = `https://mystorageaccount.dfs.core.windows.net/webdata/` , zapytanie SQL na żądanie zwróci wiersze z mydata.txt i _hidden.txt. Nie zwróci mydata2.txt i mydata3.txt, ponieważ znajdują się w podfolderze.

![Dane cykliczne dla tabel zewnętrznych](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

Klauzula WITH umożliwia określenie kolumn, które mają być odczytywane z plików.

- W przypadku plików danych CSV odczytywanie wszystkich kolumn, dostarczanie nazw kolumn i ich typów danych. Jeśli chcesz podzbiór kolumn, Użyj numerów porządkowych, aby wybrać kolumny z plików danych źródłowych według liczby porządkowej. Kolumny będą powiązane z oznaczeniem porządkowym. 

    > [!IMPORTANT]
    > Klauzula WITH jest wymagana dla plików CSV.
    >
    
- W przypadku plików danych Parquet Podaj nazwy kolumn, które pasują do nazw kolumn w źródłowych plikach danych. Kolumny będą powiązane według nazwy. Jeśli klauzula WITH zostanie pominięta, zostaną zwrócone wszystkie kolumny z plików Parquet.

column_name = nazwa kolumny wyjściowej. W przypadku podanej nazwy zastępuje nazwę kolumny w pliku źródłowym.

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

Określa metodę kompresji. Obsługiwana jest następująca metoda kompresji:

- org. Apache. Hadoop. IO. Kompresuj. GzipCodec

PARSER_VERSION = "parser_version"

Określa wersję parsera, która ma być używana podczas odczytywania plików. Obecnie obsługiwane wersje analizatora woluminów CSV to 1,0 i 2,0:

- PARSER_VERSION = "1,0"
- PARSER_VERSION = "2,0"

Analizator CSV w wersji 1,0 jest domyślny i bogaty w funkcję. Wersja 2,0 została skompilowana z myślą o wydajności i nie obsługuje wszystkich opcji i kodowań. 

Specyficzne dla analizatora CSV wersja 2,0:

- Nie wszystkie typy danych są obsługiwane.
- Maksymalny rozmiar wiersza to 8 MB.
- Następujące opcje nie są obsługiwane: DATA_COMPRESSION.
- Pusty ciąg w cudzysłowie ("") jest interpretowany jako pusty ciąg.

## <a name="examples"></a>Przykłady

Poniższy przykład zwraca tylko dwie kolumny z numerami porządkowymi 1 i 4 z plików Population*. csv. Ponieważ w plikach nie ma wiersza nagłówka, rozpocznie się odczytywanie z pierwszego wiersza:

```sql
SELECT * 
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

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów, zobacz [Przewodnik Szybki Start dotyczący usługi Query Data Storage](query-data-storage.md) , aby dowiedzieć się, jak używać `OPENROWSET` programu do odczytywania formatów plików [CSV](query-single-csv-file.md), [PARQUET](query-parquet-files.md)i [JSON](query-json-files.md) . Możesz również dowiedzieć się, jak zapisać wyniki zapytania w usłudze Azure Storage przy użyciu [CETAS](develop-tables-cetas.md).
