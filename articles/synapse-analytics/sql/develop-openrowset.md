---
title: Jak używać funkcji OPENROWSET w języku SQL na żądanie (wersja zapoznawcza)
description: Ten artykuł zawiera opis składni programu OPENROWSET w programie SQL on-demand (wersja zapoznawcza) i wyjaśniono, jak używać argumentów.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 25eb93a01c59225b6d9e64db5d08b954adb4f8ab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424069"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Jak używać programu OPENROWSET z programem SQL na żądanie (wersja zapoznawcza)

Funkcja OPENROWSET(BULK...) umożliwia dostęp do plików w usłudze Azure Storage. W zasobie SQL on-demand (wersja zapoznawcza) dostawca zestawu wierszy zbiorczych OPENROWSET jest dostępny, wywołując funkcję OPENROWSET i określając opcję BULK.  

Do funkcji OPENROWSET można odwoływać się w klauzuli FROM kwerendy tak, jakby była to nazwa tabeli OPENROWSET. Obsługuje operacje zbiorcze za pośrednictwem wbudowanego dostawcy bulk, który umożliwia odczyt danych z pliku i zwracane jako zestaw wierszy.

Funkcja OPENROWSET nie jest obecnie obsługiwana w puli SQL.

## <a name="syntax"></a>Składnia

```
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

- "CSV" - Zawiera dowolny rozdzielony plik tekstowy z separatorami wierszy/kolumn. Dowolny znak może być użyty jako separator pól, taki jak TSV: FIELDTERMINATOR = tab.

- 'PARKIET' - Plik binarny w formacie Parkietu 

"unstructured_data_path"

Unstructured_data_path, która ustanawia ścieżkę do danych, jest uporządkowany<prefix>w następujący sposób: " "<storage_account_path>/<storage_path>" 
 
 
 Poniżej znajdziesz odpowiednie ścieżki kont magazynu, które będą zawierać łącze do konkretnego zewnętrznego źródła danych. 

| Zewnętrzne źródło danych       | Prefiks | Ścieżka konta magazynu                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Store Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | https  | <storage_account>.dfs.core.windows.net              |

 "<storage_path>" 

 Określa ścieżkę w magazynie, która wskazuje folder lub plik, który chcesz odczytać. Jeśli ścieżka wskazuje kontener lub folder, wszystkie pliki zostaną odczytane z tego określonego kontenera lub folderu. Pliki w podfolderach nie zostaną uwzględnione. 
 
 Symbole wieloznaczne można używać do kierowania na wiele plików lub folderów. Użycie wielu nieposiących po sobie symboli wieloznacznych jest dozwolone.
Poniżej znajduje się przykład, który odczytuje wszystkie pliki *csv,* zaczynająchttps://sqlondemandstorage.blob.core.windows.net/csv/populationod *populacji* ze wszystkich folderów, zaczynając od */csv/population*: '*/population*.csv'

Jeśli określisz unstructured_data_path do folderu, kwerenda SQL na żądanie pobierze pliki z tego folderu. 

> [!NOTE]
> W przeciwieństwie do Hadoop i PolyBase, SQL na żądanie nie zwraca podfolderów. Ponadto, w przeciwieństwie do Hadoop i PloyBase, SQL na żądanie zwraca pliki, dla których nazwa pliku zaczyna się od podkreślenia (_) lub kropki (.).

W poniższym przykładzie, jeśli unstructured_data_path='https://mystorageaccount.dfs.core.windows.net/webdata/', zapytanie SQL na żądanie zwróci wiersze z pliku mydata.txt i _hidden.txt. Nie zwróci mydata2.txt i mydata3.txt, ponieważ znajdują się one w podfolderze.

![Dane cykliczne dla tabel zewnętrznych](./media/develop-openrowset/folder-traversal.png)

[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]

Klauzula WITH umożliwia określenie kolumn, które mają być odczytywane z plików.

- W przypadku plików danych CSV, aby odczytać wszystkie kolumny, podaj nazwy kolumn i ich typy danych. Jeśli chcesz podzbiór kolumn, użyj liczb porządkowych, aby wybrać kolumny z źródłowych plików danych według liczby porządkowej. Kolumny będą powiązane oznaczeniem porządkowym. 

> [!IMPORTANT]
> Klauzula WITH jest obowiązkowa dla plików CSV.
- W przypadku plików danych parkietu podaj nazwy kolumn, które pasują do nazw kolumn w źródłowych plikach danych. Kolumny będą powiązane według nazwy. Jeśli klauzula WITH zostanie pominięta, zostaną zwrócone wszystkie kolumny z plików Parkietu.

column_name = Nazwa kolumny wyjściowej. Jeśli jest podana, ta nazwa zastępuje nazwę kolumny w pliku źródłowym.

column_type = Typ danych dla kolumny wyjściowej. Konwersja typu danych niejawnych odbędzie się tutaj.

column_ordinal = Liczba porządkowa kolumny w pliku źródłowym(-ach). Ten argument jest ignorowany dla plików Parkietu, ponieważ powiązanie odbywa się według nazwy. Poniższy przykład spowoduje zwrócenie drugiej kolumny tylko z pliku CSV:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```
<bulk_options>

FIELDTERMINATOR ='field_terminator'

Określa terminator pola, który ma być używany. Domyślnym terminatorem pola jest przecinek ("**,**").

ROWTERMINATOR ='row_terminator''

Określa terminator wierszy, który ma być używany. Domyślny terminator wiersza to znak nowego wiersza, taki jak \r\n.

ESCAPE_CHAR = 'char'

Określa znak w pliku, który jest używany do ucieczki się i wszystkie wartości ogranicznika w pliku. Jeśli po znaku ucieczki następuje wartość inna niż ona lub dowolna z wartości ogranicznika, znak ucieczki jest odrzucany podczas odczytywania wartości. 

Parametr ESCAPE_CHAR zostanie zastosowany niezależnie od tego, czy funkcja FIELDQUOTE jest lub nie jest włączona. Nie będzie używany do ucieczki znak cytowanie. Znak cudzysłowu jest zmieniany z cudzysłowami podwójnymi w zgodzie z zachowaniem CSV programu Excel.

FIRSTROW = 'first_row' 

Określa numer pierwszego wiersza do załadowania. Domyślnym ustawieniem jest 1. Oznacza to pierwszy wiersz w określonym pliku danych. Liczby wierszy są określane przez zliczanie terminatorów wierszy. FIRSTROW jest oparty na 1.

CYTAT FIELDQUOTE = 'field_quote' 

Określa znak, który będzie używany jako znak cudzysłowu w pliku CSV. Jeśli nie zostanie określony, zostanie użyty znak cudzysłowu ("). 

## <a name="examples"></a>Przykłady

Poniższy przykład zwraca tylko dwie kolumny z liczbami porządkowymi 1 i 4 z plików population*.csv. Ponieważ w plikach nie ma wiersza nagłówka, rozpoczyna się odczyt od pierwszego wiersza:

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



Poniższy przykład zwraca wszystkie kolumny pierwszego wiersza z zestawu danych spisu w formacie Parkiet bez określania nazw kolumn i typów danych: 

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

Aby uzyskać więcej przykładów, przejdź do [przewodnika Szybki start](query-data-storage.md) lub zapisz wyniki kwerendy w usłudze Azure Storage przy użyciu [programu CETAS.](develop-tables-cetas.md)
