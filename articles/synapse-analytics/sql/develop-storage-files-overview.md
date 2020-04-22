---
title: Pliki magazynu zapytań przy użyciu SQL na żądanie (wersja zapoznawcza) w synapse SQL
description: W tym artykule opisano wykonywanie zapytań dotyczących plików magazynu przy użyciu zasobów SQL on-demand (preview) w ramach programu Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 2126996620d6f891dde4e7530c057d2c7f31a996
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676670"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Pliki magazynu zapytań przy użyciu zasobów SQL on-demand (preview) w synapse SQL

SQL na żądanie (wersja zapoznawcza) umożliwia wykonywanie zapytań o dane w aplikacji data lake. Oferuje powierzchni zapytania T-SQL, który obsługuje półstrukturalnych i niestrukturalnych zapytań danych.

W przypadku wykonywania zapytań obsługiwane są następujące aspekty T-SQL:

- Pełna [powierzchnia SELECT,](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) w tym większość funkcji SQL, operatorów i tak dalej.
- UTWÓRZ TABELĘ ZEWNĘTRZNĄ JAKO SELECT[(CETAS)](develop-tables-cetas.md)tworzy [tabelę zewnętrzną,](develop-tables-external-tables.md) a następnie eksportuje równolegle wyniki instrukcji Transact-SQL SELECT do usługi Azure Storage.

Aby uzyskać więcej informacji na temat tego, co jest w porównaniu z tym, co nie jest obecnie obsługiwane, przeczytaj artykuł [omówienie sql na żądanie.](on-demand-workspace-overview.md)

Gdy użytkownicy usługi Azure AD uruchamiają kwerendy, domyślnie jest to, aby konta magazynu były dostępne przy użyciu protokołu uwierzytelniania przekazywania usługi Azure AD. W związku z tym użytkownicy będą personifikowani i uprawnienia sprawdzane na poziomie magazynu. Możesz kontrolować dostęp do [pamięci masowej](develop-storage-files-storage-access-control.md) zgodnie z twoimi potrzebami.

## <a name="extensions"></a>Rozszerzenia

Aby zapewnić płynne środowisko dla wykonywania zapytań o dane znajdujące się w plikach usługi Azure Storage, sql na żądanie używa funkcji [OPENROWSET](develop-openrowset.md) z dodatkowymi możliwościami:

- [Kwerenda z wieloma plikami lub folderami](#query-multiple-files-or-folders)
- [Format pliku PARKIET](#parquet-file-format)
- [Dodatkowe opcje pracy z tekstem rozdzielanym (terminator pól, terminator wierszy, znak ucieczki)](#additional-options-for-working-with-delimited-text)
- [Odczytywanie wybranego podzbioru kolumn](#read-a-chosen-subset-of-columns)
- [Wnioskowanie ze schematu](#schema-inference)
- [nazwa pliku, funkcja](#filename-function)
- [filepath, funkcja](#filepath-function)
- [Praca ze złożonymi typami i zagnieżdżonymi lub powtarzającymi się strukturami danych](#work-with-complex-types-and-nested-or-repeated-data-structures)

### <a name="query-multiple-files-or-folders"></a>Kwerenda z wieloma plikami lub folderami

Aby uruchomić kwerendę T-SQL nad zestawem plików w folderze lub zestawie folderów, traktując je jako pojedynczą jednostkę lub zestaw wierszy, podaj ścieżkę do folderu lub wzorca (przy użyciu symboli wieloznacznych) nad zestawem plików lub folderów.

Mają zastosowanie następujące zasady:

- Wzorce mogą pojawić się w części ścieżki katalogu lub w nazwach plików.
- W tym samym kroku lub nazwie pliku może pojawić się kilka wzorców.
- Jeśli istnieje wiele symboli wieloznacznych, pliki we wszystkich pasujących ścieżkach zostaną uwzględnione w wynikowym zestawie plików.

```
N'https://myaccount.blob.core.windows.net/myroot/*/mysubfolder/*.csv'
```

Zapoznaj się [z folderami kwerend i wieloma plikami,](query-folders-multiple-csv-files.md) aby zapoznać się z przykładami użycia.

### <a name="parquet-file-format"></a>Format pliku PARKIET

Aby zbadać dane źródłowe parkietu, użyj format = 'PARKIET'

```syntaxsql
OPENROWSET
(
    { BULK 'data_file' ,
    { FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
)
AS table_alias(column_alias,...n)
<bulk_options> ::=
...
[ , FORMAT = {'CSV' | 'PARQUET'} ]
```

Przejrzyj artykuł [o plikach parkietu kwerendy,](query-parquet-files.md) aby zapoznać się z przykładami użycia.

### <a name="additional-options-for-working-with-delimited-text"></a>Dodatkowe opcje pracy z tekstem rozdzielanym

Te dodatkowe parametry są wprowadzane do pracy z plikami CSV (tekst rozdzielany):

```syntaxsql
<bulk_options> ::=
...
[ , FIELDTERMINATOR = 'char' ]
[ , ROWTERMINATOR = 'char' ]
[ , ESCAPE_CHAR = 'char' ]
...
```

- ESCAPE_CHAR = 'char' Określa znak w pliku, który jest używany do ucieczki się i wszystkie wartości ogranicznika w pliku. Jeśli po znaku ucieczki następuje wartość inna niż ona lub którakolwiek z wartości ogranicznika, znak ucieczki jest odrzucany podczas odczytywania wartości.
Parametr ESCAPE_CHAR zostanie zastosowany niezależnie od tego, czy funkcja FIELDQUOTE jest lub nie jest włączona. Nie będzie on używany do ucieczki znak cytowanie. Znak cudzysłowu jest zmieniany z cudzysłowami podwójnymi w zgodzie z zachowaniem CSV programu Excel.
- FIELDTERMINATOR ='field_terminator' Określa terminator pola, który ma być używany. Domyślnym terminatorem pola jest przecinek ("**,**")
- ROWTERMINATOR ='row_terminator' Określa terminator wiersza, który ma być używany. Domyślnym terminatorem wiersza jest znak nowego wiersza: **\r\n**.

### <a name="read-a-chosen-subset-of-columns"></a>Odczytywanie wybranego podzbioru kolumn

Aby określić kolumny, które chcesz odczytać, można podać opcjonalną klauzulę WITH w instrukcji OPENROWSET.

- Jeśli istnieją pliki danych CSV, aby odczytać wszystkie kolumny, podaj nazwy kolumn i ich typy danych. Jeśli chcesz podzbiór kolumn, użyj liczb porządkowych, aby wybrać kolumny z źródłowych plików danych według liczby porządkowej. Kolumny będą powiązane oznaczeniem porządkowym.
- Jeśli istnieją pliki danych parkietu, podaj nazwy kolumn, które pasują do nazw kolumn w źródłowych plikach danych. Kolumny będą powiązane według nazwy.

```syntaxsql
OPENROWSET
...
| BULK 'data_file',
{ FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
) AS table_alias(column_alias,...n) | WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })
```

W przypadku przykładów można znaleźć w [obszarze Odczyt plików CSV bez określania wszystkich kolumn](query-single-csv-file.md#returning-subset-of-columns).

### <a name="schema-inference"></a>Wnioskowanie ze schematu

Pomijając klauzulę WITH z instrukcji OPENROWSET, można poinstruować usługę, aby automatycznie wykrywała (wywnioskować) schemat z plików źródłowych.

> [!NOTE]
> Obecnie działa to tylko w formacie pliku PARKIET.

```sql
OPENROWSET(
BULK N'path_to_file(s)', FORMAT='PARQUET');
```

### <a name="filename-function"></a>Nazwa pliku, funkcja

Ta funkcja zwraca nazwę pliku, z którego pochodzi wiersz.

Aby zbadać określone pliki, przeczytaj sekcję Nazwa pliku w artykule [Zapytanie o określone pliki.](query-specific-files.md#filename)

### <a name="filepath-function"></a>Ścieżka pliku, funkcja

Ta funkcja zwraca pełną ścieżkę lub część ścieżki:

- Po wywołaniu bez parametru zwraca pełną ścieżkę pliku, z której pochodzi wiersz.
- Po wywołaniu z parametrem zwraca część ścieżki, która pasuje do symbolu wieloznacznego na pozycji określonej w parametrze. Na przykład wartość parametru 1 zwróci część ścieżki, która pasuje do pierwszego symbolu wieloznacznego.

Aby uzyskać dodatkowe informacje, przeczytaj sekcję Ścieżka plików w artykule [Kwerenda określonych plików.](query-specific-files.md#filepath)

### <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Praca ze złożonymi typami i zagnieżdżonymi lub powtarzającymi się strukturami danych

Aby włączyć płynne środowisko podczas pracy z danymi przechowywanymi w zagnieżdżonych lub powtarzających się typach danych, takich jak pliki [parkietu,](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) sql na żądanie dodał rozszerzenia poniżej.

#### <a name="project-nested-or-repeated-data"></a>Zagnieżdżone lub powtarzane dane projektu

Aby projektować dane, uruchom instrukcję SELECT za plikiem Parkietu zawierającą kolumny zagnieżdżonych typów danych. Na wyjściu wartości zagnieżdżone będą serializowane w języku JSON i zwracane jako typ danych SQL varchar(8000).

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z sekcji Zagnieżdżone lub powtarzane dane projektu w klasie [kwerendy zagnieżdżonych typów](query-parquet-nested-types.md#project-nested-or-repeated-data) artykułu.

#### <a name="access-elements-from-nested-columns"></a>Dostęp do elementów z kolumn zagnieżdżonych

Aby uzyskać dostęp do elementów zagnieżdżonych z kolumny zagnieżdżonej, takich jak Struktura, użyj "notacji punktowej", aby łączyć nazwy pól ze ścieżką. Podaj ścieżkę jako column_name w klauzuli WITH funkcji OPENROWSET.

Przykład fragmentu składni jest następujący:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Domyślnie funkcja OPENROWSET jest zgodna z nazwą pola źródłowego i ścieżką z nazwami kolumn podanymi w klauzuli WITH. Elementy zawarte na różnych poziomach zagnieżdżania w tym samym źródłowym pliku Parkietu są dostępne za pośrednictwem klauzuli WITH.

**Zwracane wartości**

- Funkcja zwraca wartość skalarną, taką jak int, decimal i varchar, z określonego elementu i na określonej ścieżce dla wszystkich typów parkietu, które nie znajdują się w grupie Typ zagnieżdżony.
- Jeśli ścieżka wskazuje na element, który jest typu zagnieżdżonego, funkcja zwraca fragment JSON, począwszy od górnego elementu na określonej ścieżce. Fragment JSON jest typu varchar(8000).
- Jeśli właściwości nie można znaleźć w określonym column_name, funkcja zwraca błąd.
- Jeśli właściwości nie można znaleźć w określonym column_path, w zależności od [trybu path,](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE)funkcja zwraca błąd, gdy w trybie ścisłym lub null, gdy w trybie lax.

W przypadku przykładów kwerend przejrzyj sekcję Dostęp z zagnieżdżonych kolumn w artykule [Typy zagnieżdżone Parkiet kwerendy.](query-parquet-nested-types.md#access-elements-from-nested-columns)

#### <a name="access-elements-from-repeated-columns"></a>Dostęp do elementów z powtarzających się kolumn

Aby uzyskać dostęp do elementów z powtarzanej kolumny, takich jak element Array lub Map, użyj funkcji [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) dla każdego elementu skalarnego, który należy projektować i dostarczać:

- Zagnieżdżona lub powtarzana kolumna jako pierwszy parametr
- [Ścieżka JSON](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) określająca element lub właściwość dostępu jako drugi parametr

Aby uzyskać dostęp do elementów nieskarzalnych z powtarzanej kolumny, użyj funkcji [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) dla każdego elementu nieskawarowego, który należy projektować i dostarczać:

- Zagnieżdżona lub powtarzana kolumna jako pierwszy parametr
- [Ścieżka JSON](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) określająca element lub właściwość dostępu jako drugi parametr

Zobacz fragment składni poniżej:

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Przykłady kwerend dotyczące uzyskiwania dostępu do elementów z powtarzających się kolumn można znaleźć w artykule [Typy zagnieżdżone parkietu kwerendy.](query-parquet-nested-types.md#access-elements-from-repeated-columns)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wykonywania zapytań o różne typy plików oraz tworzenia i używania widoków, zobacz następujące artykuły:

- [Zapytanie o pojedynczy plik CSV](query-single-csv-file.md)
- [Wykonywanie zapytań względem plików Parquet](query-parquet-files.md)
- [Wykonywanie zapytań względem plików JSON](query-json-files.md)
- [Wykonywanie zapytań względem typów zagnieżdżonych Parquet](query-parquet-nested-types.md)
- [Foldery kwerend i wiele plików CSV](query-folders-multiple-csv-files.md)
- [Używanie metadanych plików w kwerendach](query-specific-files.md)
- [Tworzenie widoków i korzystanie z nich](create-use-views.md)
