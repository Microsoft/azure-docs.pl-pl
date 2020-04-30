---
title: Jak używać funkcji OPENROWSET w programie SQL na żądanie (wersja zapoznawcza)
description: W tym artykule opisano składnię funkcji OPENROWSET w SQL na żądanie (wersja zapoznawcza) i wyjaśniono, jak używać argumentów.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 6325d5555b01373b148dce69731ec64896d6e1fd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81680488"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Jak używać funkcji OPENROWSET z SQL na żądanie (wersja zapoznawcza)

Funkcja OPENROWSET (BULK...) umożliwia dostęp do plików w usłudze Azure Storage. W ramach zasobu SQL na żądanie (wersja zapoznawcza) dostawca zestawu wierszy zbiorczych OPENROWSET jest dostępny przez wywołanie funkcji OPENROWSET i określenie opcji BULK.  

Do funkcji OPENROWSET można odwoływać się w klauzuli FROM zapytania, tak jakby była to nazwa tabeli OPENROWSET. Obsługuje operacje zbiorcze za pośrednictwem wbudowanego dostawcy ZBIORCZego, który umożliwia odczytywanie danych z pliku i zwracanie ich jako zestawu wierszy.

Funkcja OPENROWSET nie jest obecnie obsługiwana w puli SQL.

## <a name="syntax"></a>Składnia

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row'  ]     
[ , FIELDQUOTE = 'quote_characters']
```

## <a name="arguments"></a>Argumenty

Dostępne są dwie opcje dla plików wejściowych, które zawierają dane docelowe do wykonywania zapytań. Prawidłowe wartości:

- "CSV" — zawiera dowolny rozdzielany plik tekstowy z separatorami wierszy/kolumn. Dowolny znak może być używany jako separator pola, na przykład TSV: FIELDTERMINATOR = Tab.

- "PARQUET" — plik binarny w formacie Parquet 

**"unstructured_data_path"**

Unstructured_data_path, które ustalają ścieżkę do danych, są uporządkowane w następujący sposób:  
"\<prefix>://\<storage_account_path>/\<storage_path>"
 
 
 Poniżej znajdziesz odpowiednie ścieżki kont magazynu, które zostaną połączone z określonym zewnętrznym źródłem danych. 

| Zewnętrzne źródło danych       | Prefiks | Ścieżka konta magazynu                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | https  | \<storage_account>. blob.core.windows.net             |
| Azure Data Lake Store Gen1 | https  | \<storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | https  | \<storage_account>. dfs.core.windows.net              |
||||

"\<storage_path>"

 Określa ścieżkę w magazynie, która wskazuje folder lub plik, który ma zostać odczytany. Jeśli ścieżka wskazuje kontener lub folder, wszystkie pliki zostaną odczytane z danego kontenera lub folderu. Pliki w podfolderach nie będą uwzględniane. 
 
 Możesz użyć symboli wieloznacznych, aby docelowa była wiele plików lub folderów. Dozwolone jest użycie wielu niesąsiadujących symboli wieloznacznych.
Poniżej znajduje się przykład, który odczytuje wszystkie pliki *CSV* zaczynające się od *populacji* ze wszystkich folderów zaczynających się od */CSV/Population*:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Jeśli określisz unstructured_data_path jako folder, zapytanie SQL na żądanie pobierze pliki z tego folderu. 

> [!NOTE]
> W przeciwieństwie do usługi Hadoop i bazy danych SQL na żądanie nie zwraca podfolderów. Ponadto, w przeciwieństwie do usługi Hadoop i PloyBase, SQL na żądanie zwraca pliki, dla których nazwa pliku zaczyna się od podkreślenia (_) lub kropki (.).

W poniższym przykładzie, jeśli unstructured_data_path =`https://mystorageaccount.dfs.core.windows.net/webdata/`, zapytanie SQL na żądanie zwróci wiersze z elementu webdata. txt i _hidden. txt. Nie zwróci mydata2. txt i mydata3. txt, ponieważ znajdują się one w podfolderze.

![Dane cykliczne dla tabel zewnętrznych](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

Klauzula WITH umożliwia określenie kolumn, które mają być odczytywane z plików.

- W przypadku plików danych CSV odczytywanie wszystkich kolumn, dostarczanie nazw kolumn i ich typów danych. Jeśli chcesz podzbiór kolumn, Użyj numerów porządkowych, aby wybrać kolumny z plików danych źródłowych według liczby porządkowej. Kolumny będą powiązane z oznaczeniem porządkowym. 

> [!IMPORTANT]
> Klauzula WITH jest wymagana dla plików CSV.
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

Określa terminator wiersza, który ma być używany. Domyślny terminator wiersza to znak nowego wiersza, taki jak \r\n.

ESCAPE_CHAR = "char"

Określa znak w pliku, który jest używany do wyprowadzania samego siebie i wszystkich wartości ogranicznika w pliku. Jeśli po znaku ucieczki następuje wartość inna niż sama lub jakakolwiek z wartości ogranicznika, znak ucieczki jest usuwany podczas odczytywania wartości. 

Parametr ESCAPE_CHAR zostanie zastosowany niezależnie od tego, czy FIELDQUOTE jest czy nie jest włączony. Nie będzie on używany do ucieczki znaku cudzysłowu. Znak cudzysłowu jest wyprowadzany z podwójnym cudzysłówem w równaniu z zachowaniem CSV programu Excel.

FIRSTROW = "first_row" 

Określa numer pierwszego wiersza do załadowania. Domyślnym ustawieniem jest 1. Wskazuje pierwszy wiersz w określonym pliku danych. Numery wierszy są określane przez liczenie terminatorów wierszy. FIRSTROW jest oparty na 1.

FIELDQUOTE = "field_quote" 

Określa znak, który będzie używany jako znak cudzysłowu w pliku CSV. Jeśli nie zostanie określony, zostanie użyty znak cudzysłowu ("). 

## <a name="examples"></a>Przykłady

Poniższy przykład zwraca tylko dwie kolumny z numerami porządkowymi 1 i 4 z plików Population*. csv. Ponieważ w plikach nie ma wiersza nagłówka, rozpocznie się odczytywanie z pierwszego wiersza:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

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



Poniższy przykład zwraca wszystkie kolumny pierwszego wiersza z zestawu danych spisu w formacie Parquet bez określania nazw kolumn i typów danych: 

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```



## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów, przejdź do [przewodników szybki start](query-data-storage.md) lub Zapisz wyniki zapytania w usłudze Azure Storage przy użyciu [CETAS](develop-tables-cetas.md).
