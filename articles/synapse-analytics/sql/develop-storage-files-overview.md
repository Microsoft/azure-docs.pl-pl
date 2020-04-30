---
title: Wykonywanie zapytań dotyczących plików magazynu przy użyciu funkcji SQL na żądanie (wersja zapoznawcza) w programie Synapse SQL
description: Opisuje wykonywanie zapytań dotyczących plików magazynu za pomocą zasobów SQL na żądanie (wersja zapoznawcza) w programie Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 2126996620d6f891dde4e7530c057d2c7f31a996
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81676670"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Wykonywanie zapytań dotyczących plików magazynu za pomocą zasobów SQL na żądanie (wersja zapoznawcza) w programie Synapse SQL

SQL na żądanie (wersja zapoznawcza) umożliwia wykonywanie zapytań dotyczących danych w usłudze Data Lake. Oferuje obszar powierzchni zapytania T-SQL, który służy do obsługi zapytań o dane z częściową strukturą i bez struktury.

Do wykonywania zapytań są obsługiwane następujące aspekty języka T-SQL:

- Pełny [wybór](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) obszaru powierzchni, w tym większość funkcji SQL, operatorów i tak dalej.
- Utwórz tabelę ZEWNĘTRZną jako SELECT ([CETAS](develop-tables-cetas.md)) tworzy [tabelę zewnętrzną](develop-tables-external-tables.md) , a następnie eksportuje, równolegle, wyniki instrukcji SELECT języka Transact-SQL do usługi Azure Storage.

Aby uzyskać więcej informacji na temat tego, co to jest program vs. co nie jest obecnie obsługiwane, przeczytaj artykuł [Omówienie usługi SQL na żądanie](on-demand-workspace-overview.md) .

Gdy użytkownicy usługi Azure AD uruchamiają zapytania, wartością domyślną jest dostęp do kont magazynu przy użyciu protokołu uwierzytelniania przekazującego usługi Azure AD. W związku z tym użytkownicy będą personifikowani, a uprawnienia są sprawdzane na poziomie magazynu. Dostęp do [magazynu można kontrolować](develop-storage-files-storage-access-control.md) odpowiednio do własnych potrzeb.

## <a name="extensions"></a>Rozszerzenia

Aby zapewnić bezproblemowe środowisko wykonywania zapytań dotyczących danych znajdujących się w plikach usługi Azure Storage, usługa SQL na żądanie używa funkcji [OPENROWSET](develop-openrowset.md) z dodatkowymi możliwościami:

- [Kwerenda wielu plików lub folderów](#query-multiple-files-or-folders)
- [Format pliku PARQUET](#parquet-file-format)
- [Dodatkowe opcje pracy z rozdzielonym tekstem (terminator pola, terminator wiersza, znak ucieczki)](#additional-options-for-working-with-delimited-text)
- [Odczytaj wybrany podzestaw kolumn](#read-a-chosen-subset-of-columns)
- [Wnioskowanie schematu](#schema-inference)
- [Funkcja filename](#filename-function)
- [FilePath — funkcja](#filepath-function)
- [Pracuj z typami złożonymi i zagnieżdżonymi lub powtarzanymi strukturami danych](#work-with-complex-types-and-nested-or-repeated-data-structures)

### <a name="query-multiple-files-or-folders"></a>Kwerenda wielu plików lub folderów

Aby uruchomić zapytanie T-SQL dotyczące zestawu plików w folderze lub w zestawie folderów podczas traktowania ich jako pojedynczej jednostki lub zestawu wierszy, podaj ścieżkę do folderu lub wzorca (przy użyciu symboli wieloznacznych) w zestawie plików lub folderów.

Mają zastosowanie następujące zasady:

- Wzorce mogą pojawić się w części ścieżki katalogu lub pliku.
- Kilka wzorców może pojawić się w tym samym kroku katalogu lub nazwie pliku.
- Jeśli istnieje wiele symboli wieloznacznych, pliki we wszystkich zgodnych ścieżkach zostaną uwzględnione w wyznaczonym zestawie plików.

```
N'https://myaccount.blob.core.windows.net/myroot/*/mysubfolder/*.csv'
```

Przykłady użycia znajdują się w [folderach zapytań i wielu plikach](query-folders-multiple-csv-files.md) .

### <a name="parquet-file-format"></a>Format pliku PARQUET

Aby wykonać zapytanie dotyczące danych źródłowych Parquet, użyj formatu = "PARQUET"

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

Zapoznaj się z artykułem [pliki Parquet zapytania](query-parquet-files.md) , aby zapoznać się z przykładami użycia.

### <a name="additional-options-for-working-with-delimited-text"></a>Dodatkowe opcje pracy z rozdzielonym tekstem

Te dodatkowe parametry są wprowadzane do pracy z plikami CSV (rozdzielanymi tekstem):

```syntaxsql
<bulk_options> ::=
...
[ , FIELDTERMINATOR = 'char' ]
[ , ROWTERMINATOR = 'char' ]
[ , ESCAPE_CHAR = 'char' ]
...
```

- ESCAPE_CHAR = "char" określa znak w pliku, który jest używany do wyprowadzania siebie i wszystkich wartości ograniczników w pliku. Jeśli po znaku ucieczki następuje wartość inna niż sama lub jakakolwiek z wartości ogranicznika, znak ucieczki jest usuwany podczas odczytywania wartości.
Parametr ESCAPE_CHAR zostanie zastosowany niezależnie od tego, czy FIELDQUOTE jest czy nie jest włączony. Nie będzie on używany do ucieczki znaku cudzysłowu. Znak cudzysłowu jest wyprowadzany z podwójnym cudzysłówem w równaniu z zachowaniem CSV programu Excel.
- FIELDTERMINATOR = "field_terminator" Określa terminator pola, które ma być używane. Domyślny terminator pola jest przecinkiem ("**,**")
- ROWTERMINATOR = "row_terminator" Określa terminator wiersza, który ma być używany. Domyślnym terminatorem wiersza jest znak nowego wiersza: **\r\n**.

### <a name="read-a-chosen-subset-of-columns"></a>Odczytaj wybrany podzestaw kolumn

Aby określić kolumny, które mają zostać odczytane, możesz podać opcjonalną klauzulę WITH w instrukcji OPENROWSET.

- Jeśli istnieją pliki danych CSV, odczytywanie wszystkich kolumn, podawanie nazw kolumn i ich typów danych. Jeśli chcesz podzbiór kolumn, Użyj numerów porządkowych, aby wybrać kolumny z plików danych źródłowych według liczby porządkowej. Kolumny będą powiązane z oznaczeniem porządkowym.
- Jeśli istnieją pliki danych Parquet, Podaj nazwy kolumn, które pasują do nazw kolumn w źródłowych plikach danych. Kolumny będą powiązane według nazwy.

```syntaxsql
OPENROWSET
...
| BULK 'data_file',
{ FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
) AS table_alias(column_alias,...n) | WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })
```

Aby zapoznać się z przykładami, zobacz [odczytywanie plików CSV bez określania wszystkich kolumn](query-single-csv-file.md#returning-subset-of-columns).

### <a name="schema-inference"></a>Wnioskowanie schematu

Pomijając klauzulę WITH z instrukcji OPENROWSET, można nakazać usłudze automatyczne wykrywanie (wnioskowanie) schematu z plików źródłowych.

> [!NOTE]
> Obecnie działa tylko w przypadku formatu pliku PARQUET.

```sql
OPENROWSET(
BULK N'path_to_file(s)', FORMAT='PARQUET');
```

### <a name="filename-function"></a>Funkcja filename

Ta funkcja zwraca nazwę pliku, z którego pochodzi wiersz.

Aby wykonać zapytanie dotyczące określonych plików, przeczytaj sekcję filename w artykule [dotyczącej określonych plików](query-specific-files.md#filename) .

### <a name="filepath-function"></a>FilePath — funkcja

Ta funkcja zwraca pełną ścieżkę lub część ścieżki:

- Gdy wywoływana bez parametru zwraca pełną ścieżkę do pliku, z którego pochodzi wiersz.
- Gdy jest wywoływana z parametrem, zwraca część ścieżki, która pasuje do symbolu wieloznacznego na pozycji określonej w parametrze. Na przykład wartość parametru 1 zwróci część ścieżki, która pasuje do pierwszego symbolu wieloznacznego.

Aby uzyskać dodatkowe informacje, zapoznaj się z sekcją FilePath artykułu dotyczącego [określonych plików zapytania](query-specific-files.md#filepath) .

### <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Pracuj z typami złożonymi i zagnieżdżonymi lub powtarzanymi strukturami danych

Aby zapewnić bezproblemowe środowisko podczas pracy z danymi przechowywanymi w zagnieżdżonych lub powtarzanych typach danych, na przykład w plikach [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) , w usłudze SQL na żądanie dodano poniższe rozszerzenia.

#### <a name="project-nested-or-repeated-data"></a>Dane zagnieżdżone lub powtórzone projektu

Aby uzyskać dane projektu, uruchom instrukcję SELECT na pliku Parquet, który zawiera kolumny zagnieżdżonych typów danych. W danych wyjściowych zagnieżdżone wartości zostaną zserializowane do formatu JSON i zwracane jako typ danych SQL varchar (8000).

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z sekcją dane zagnieżdżonych lub powtarzanych danych w artykule " [typy zagnieżdżone zapytania Parquet](query-parquet-nested-types.md#project-nested-or-repeated-data) ".

#### <a name="access-elements-from-nested-columns"></a>Dostęp do elementów z zagnieżdżonych kolumn

Aby uzyskać dostęp do zagnieżdżonych elementów z zagnieżdżonej kolumny, takiej jak struktura, użyj "notacji kropkowej" do łączenia nazw pól ze ścieżką. Podaj ścieżkę jako column_name w klauzuli WITH funkcji OPENROWSET.

Przykład fragmentu składni jest następujący:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Domyślnie funkcja OPENROWSET jest zgodna z nazwą pola źródłowego i ścieżką z nazwami kolumn podanymi w klauzuli WITH. Do elementów zawartych na różnych poziomach zagnieżdżenia w tym samym pliku źródłowym Parquet można uzyskać dostęp za pośrednictwem klauzuli WITH.

**Zwracane wartości**

- Funkcja zwraca wartość skalarną, taką jak int, Decimal i varchar, z określonego elementu i w określonej ścieżce dla wszystkich typów Parquet, które nie należą do grupy typów zagnieżdżonych.
- Jeśli ścieżka wskazuje element, który jest typu zagnieżdżonego, funkcja zwraca fragment JSON, zaczynając od górnego elementu w określonej ścieżce. Fragment JSON jest typu varchar (8000).
- Jeśli nie można odnaleźć właściwości w określonym column_name, funkcja zwróci błąd.
- Jeśli nie można odnaleźć właściwości w określonym column_path, w zależności od [trybu ścieżki](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE)funkcja zwraca błąd w trybie Strict lub null w trybie swobodny.

Aby zapoznać się z przykładami zapytań, zapoznaj się z sekcją elementy dostępu z zagnieżdżonych kolumn w artykule [Parquet typów zagnieżdżonych](query-parquet-nested-types.md#access-elements-from-nested-columns) .

#### <a name="access-elements-from-repeated-columns"></a>Dostęp do elementów z powtórzonych kolumn

Aby uzyskać dostęp do elementów z powtórzonej kolumny, takich jak element tablicy lub mapy, użyj funkcji [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) dla każdego elementu skalarnego, który jest potrzebny do projektu i zapewnia:

- Kolumna zagnieżdżona lub powtarzana, jako pierwszy parametr
- [Ścieżka JSON](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) określająca element lub właściwość do uzyskania dostępu, jako drugi parametr

Aby uzyskać dostęp do nieskalarnych elementów z powtórzonej kolumny, użyj funkcji [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) dla każdego nieskalarnego elementu, który jest potrzebny do projektu i zapewnia:

- Kolumna zagnieżdżona lub powtarzana, jako pierwszy parametr
- [Ścieżka JSON](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) określająca element lub właściwość do uzyskania dostępu, jako drugi parametr

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

Przykłady zapytań umożliwiające uzyskiwanie dostępu do elementów z powtórzonych kolumn w artykule [Parquet typów zagnieżdżonych zapytania](query-parquet-nested-types.md#access-elements-from-repeated-columns) .

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wykonywania zapytań dotyczących różnych typów plików i tworzenia i używania widoków, zobacz następujące artykuły:

- [Kwerenda pojedynczego pliku CSV](query-single-csv-file.md)
- [Wykonywanie zapytań względem plików Parquet](query-parquet-files.md)
- [Wykonywanie zapytań względem plików JSON](query-json-files.md)
- [Wykonywanie zapytań względem typów zagnieżdżonych Parquet](query-parquet-nested-types.md)
- [Foldery zapytań i wiele plików CSV](query-folders-multiple-csv-files.md)
- [Korzystanie z metadanych plików w zapytaniach](query-specific-files.md)
- [Tworzenie widoków i korzystanie z nich](create-use-views.md)
