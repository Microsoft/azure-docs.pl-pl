---
title: Wykonywanie zapytań dotyczących magazynu danych za pomocą puli SQL bezserwerowej (wersja zapoznawcza)
description: W tym artykule opisano sposób tworzenia zapytań do usługi Azure Storage przy użyciu zasobu puli SQL bezserwerowej (wersja zapoznawcza) w ramach usługi Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3fd3a94efd6e7870ae3919a011fc24f66b97c559
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93310955"
---
# <a name="query-storage-files-with-serverless-sql-pool-preview-in-azure-synapse-analytics"></a>Wykonywanie zapytań dotyczących plików magazynu za pomocą puli SQL bezserwerowej (wersja zapoznawcza) w usłudze Azure Synapse Analytics

Pula SQL bezserwerowa (wersja zapoznawcza) umożliwia wykonywanie zapytań dotyczących danych w usłudze Data Lake. Oferuje obszar powierzchni zapytania T-SQL, który służy do obsługi zapytań o dane z częściową strukturą i bez struktury. Do wykonywania zapytań są obsługiwane następujące aspekty języka T-SQL:

- Pełny [wybór](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) obszaru powierzchni, w tym większość [funkcji SQL i operatorów](overview-features.md).
- Utwórz tabelę ZEWNĘTRZną jako SELECT ([CETAS](develop-tables-cetas.md)) tworzy [tabelę zewnętrzną](develop-tables-external-tables.md) , a następnie eksportuje, równolegle, wyniki instrukcji SELECT języka Transact-SQL do usługi Azure Storage.

Aby uzyskać więcej informacji na temat tego, co to jest program vs. co nie jest obecnie obsługiwane, przeczytaj artykuł [Omówienie puli SQL bezserwerowej](on-demand-workspace-overview.md) lub następujące artykuły:
- [Opracowywanie dostępu do magazynu](develop-storage-files-overview.md) , w którym można dowiedzieć się, jak używać funkcji [tabela zewnętrzna](develop-tables-external-tables.md) i funkcja [OPENROWSET](develop-openrowset.md) do odczytywania danych z magazynu.
- [Kontrola dostępu do magazynu](develop-storage-files-storage-access-control.md) , w którym można dowiedzieć się, jak włączyć funkcję SQL Synapse w celu uzyskania dostępu do magazynu przy użyciu uwierzytelniania SAS lub zarządzanej tożsamości obszaru roboczego.

## <a name="overview"></a>Omówienie

Aby zapewnić bezproblemowe środowisko wykonywania zapytań dotyczących danych znajdujących się w plikach usługi Azure Storage, bezserwerowa Pula SQL używa funkcji [OPENROWSET](develop-openrowset.md) z dodatkowymi możliwościami:

- [Kwerenda wielu plików lub folderów](#query-multiple-files-or-folders)
- [Format pliku PARQUET](#query-parquet-files)
- [Wykonywanie zapytania dotyczącego CSV i rozdzielonego tekstu (terminator pola, terminator wiersza, znak ucieczki)](#query-csv-files)
- [Odczytaj wybrany podzestaw kolumn](#read-a-chosen-subset-of-columns)
- [Wnioskowanie schematu](#schema-inference)
- [Funkcja filename](#filename-function)
- [FilePath — funkcja](#filepath-function)
- [Pracuj z typami złożonymi i zagnieżdżonymi lub powtarzanymi strukturami danych](#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="query-parquet-files"></a>Pliki PARQUET zapytania

Aby wykonać zapytanie dotyczące danych źródłowych Parquet, użyj formatu = "PARQUET"

```syntaxsql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net//mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (C1 int, C2 varchar(20), C3 as varchar(max)) as rows
```

Zapoznaj się z artykułem [pliki Parquet zapytania](query-parquet-files.md) , aby zapoznać się z przykładami użycia.

## <a name="query-csv-files"></a>Wykonywanie zapytań o pliki CSV

Aby wykonać zapytanie dotyczące danych źródłowych woluminu CSV, użyj formatu "CSV". Możesz określić schemat pliku CSV jako część `OPENROWSET` funkcji podczas wykonywania zapytania dotyczącego plików CSV:

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.csv', FORMAT = 'CSV', PARSER_VERSION='2.0') 
WITH (C1 int, C2 varchar(20), C3 as varchar(max)) as rows
```

Istnieją pewne dodatkowe opcje, których można użyć do dostosowania reguł analizy do niestandardowego formatu CSv:
- ESCAPE_CHAR = "char" określa znak w pliku, który jest używany do wyprowadzania siebie i wszystkich wartości ograniczników w pliku. Jeśli po znaku ucieczki następuje wartość inna niż sama lub jakakolwiek z wartości ogranicznika, znak ucieczki jest usuwany podczas odczytywania wartości.
ESCAPE_CHAR parametr zostanie zastosowany, niezależnie od tego, czy FIELDQUOTE jest czy nie jest włączony. Nie będzie on używany do ucieczki znaku cudzysłowu. Znak cudzysłowu musi być zmieniony przy użyciu innego znaku cudzysłowu. Znak quota może pojawić się w wartości kolumny tylko wtedy, gdy wartość jest hermetyzowana przy użyciu znaków cudzysłowu.
- FIELDTERMINATOR = "field_terminator" Określa terminator pola, które ma być używane. Domyślny terminator pola jest przecinkiem (" **,** ")
- ROWTERMINATOR = "row_terminator" Określa terminator wiersza, który ma być używany. Domyślnym terminatorem wiersza jest znak nowego wiersza: **\r\n**.

## <a name="file-schema"></a>Schemat pliku

Język SQL w programie SQL Synapse umożliwia definiowanie schematu pliku w ramach `OPENROWSET` funkcji i odczytywanie wszystkich lub podzbioru kolumn albo próbuje automatycznie określić typy kolumn z pliku przy użyciu wnioskowania schematu.

### <a name="read-a-chosen-subset-of-columns"></a>Odczytaj wybrany podzestaw kolumn

Aby określić kolumny, które mają zostać odczytane, możesz podać opcjonalną klauzulę WITH w `OPENROWSET` instrukcji.

- Jeśli istnieją pliki danych CSV, odczytywanie wszystkich kolumn, podawanie nazw kolumn i ich typów danych. Jeśli chcesz podzbiór kolumn, Użyj numerów porządkowych, aby wybrać kolumny z plików danych źródłowych według liczby porządkowej. Kolumny będą powiązane z oznaczeniem porządkowym.
- Jeśli istnieją pliki danych Parquet, Podaj nazwy kolumn, które pasują do nazw kolumn w źródłowych plikach danych. Kolumny będą powiązane według nazwy.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (
      C1 int, 
      C2 varchar(20),
      C3 as varchar(max)
) as rows
```

Dla każdej kolumny należy określić nazwę kolumny i wpisz `WITH` klauzulę.
Aby zapoznać się z przykładami, zobacz [odczytywanie plików CSV bez określania wszystkich kolumn](query-single-csv-file.md#return-a-subset-of-columns).

## <a name="schema-inference"></a>Wnioskowanie schematu

Pomijając klauzulę WITH z `OPENROWSET` instrukcji, można nakazać usłudze automatyczne wykrywanie (wnioskowanie) schematu z plików źródłowych.

> [!NOTE]
> Obecnie działa tylko w przypadku formatu pliku PARQUET.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
```

Upewnij się, że [odpowiednie wywnioskowane typy danych](best-practices-sql-on-demand.md#check-inferred-data-types) są używane w celu uzyskania optymalnej wydajności. 

## <a name="query-multiple-files-or-folders"></a>Kwerenda wielu plików lub folderów

Aby uruchomić zapytanie T-SQL dotyczące zestawu plików w folderze lub w zestawie folderów podczas traktowania ich jako pojedynczej jednostki lub zestawu wierszy, podaj ścieżkę do folderu lub wzorca (przy użyciu symboli wieloznacznych) w zestawie plików lub folderów.

Mają zastosowanie następujące zasady:

- Wzorce mogą pojawić się w części ścieżki katalogu lub pliku.
- Kilka wzorców może pojawić się w tym samym kroku katalogu lub nazwie pliku.
- Jeśli istnieje wiele symboli wieloznacznych, pliki we wszystkich zgodnych ścieżkach zostaną uwzględnione w wyznaczonym zestawie plików.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/myroot/*/mysubfolder/*.parquet', FORMAT = 'PARQUET' ) as rows
```

Przykłady użycia znajdują się w [folderach zapytań i wielu plikach](query-folders-multiple-csv-files.md) .

## <a name="file-metadata-functions"></a>Funkcje metadanych plików

### <a name="filename-function"></a>Funkcja filename

Ta funkcja zwraca nazwę pliku, z którego pochodzi wiersz. 

Aby wykonać zapytanie dotyczące określonych plików, przeczytaj sekcję filename w artykule [dotyczącej określonych plików](query-specific-files.md#filename) .

Zwracany typ danych to nvarchar (1024). W celu uzyskania optymalnej wydajności zawsze należy rzutować wynik funkcji filename na odpowiedni typ danych. Jeśli używasz znaku typu danych, upewnij się, że jest używana odpowiednia długość.

### <a name="filepath-function"></a>FilePath — funkcja

Ta funkcja zwraca pełną ścieżkę lub część ścieżki:

- Gdy wywoływana bez parametru zwraca pełną ścieżkę do pliku, z którego pochodzi wiersz.
- Gdy jest wywoływana z parametrem, zwraca część ścieżki, która pasuje do symbolu wieloznacznego na pozycji określonej w parametrze. Na przykład wartość parametru 1 zwróci część ścieżki, która pasuje do pierwszego symbolu wieloznacznego.

Aby uzyskać dodatkowe informacje, zapoznaj się z sekcją FilePath artykułu dotyczącego [określonych plików zapytania](query-specific-files.md#filepath) .

Zwracany typ danych to nvarchar (1024). W celu uzyskania optymalnej wydajności zawsze należy rzutować wynik funkcji FilePath na odpowiedni typ danych. Jeśli używasz znaku typu danych, upewnij się, że jest używana odpowiednia długość.

## <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Pracuj z typami złożonymi i zagnieżdżonymi lub powtarzanymi strukturami danych

Aby umożliwić bezproblemowe środowisko pracy z danymi przechowywanymi w zagnieżdżonych lub powtarzanych typach danych, na przykład w plikach [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) , bezserwerowa Pula SQL dodała następujące rozszerzenia.

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

Aby uzyskać dostęp do zagnieżdżonych elementów z zagnieżdżonej kolumny, takiej jak struktura, użyj "notacji kropkowej" do łączenia nazw pól ze ścieżką. Podaj ścieżkę jako column_name w klauzuli WITH `OPENROWSET` funkcji.

Przykład fragmentu składni jest następujący:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Domyślnie `OPENROWSET` Funkcja jest zgodna z nazwą pola źródłowego i ścieżką z nazwami kolumn podanymi w klauzuli with. Do elementów zawartych na różnych poziomach zagnieżdżenia w tym samym pliku źródłowym Parquet można uzyskać dostęp za pośrednictwem klauzuli WITH.

**Wartości zwracane**

- Funkcja zwraca wartość skalarną, taką jak int, Decimal i varchar, z określonego elementu i w określonej ścieżce dla wszystkich typów Parquet, które nie należą do grupy typów zagnieżdżonych.
- Jeśli ścieżka wskazuje element, który jest typu zagnieżdżonego, funkcja zwraca fragment JSON, zaczynając od górnego elementu w określonej ścieżce. Fragment JSON jest typu varchar (8000).
- Jeśli nie można odnaleźć właściwości w określonym column_name, funkcja zwróci błąd.
- Jeśli nie można odnaleźć właściwości w określonym column_path, w zależności od [trybu ścieżki](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE)funkcja zwraca błąd w trybie Strict lub null w trybie swobodny.

Aby zapoznać się z przykładami zapytań, zapoznaj się z sekcją elementy dostępu z zagnieżdżonych kolumn w artykule [Parquet typów zagnieżdżonych](query-parquet-nested-types.md#read-properties-from-nested-object-columns) .

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

## <a name="query-samples"></a>Przykłady zapytań

Możesz dowiedzieć się więcej o wysyłaniu zapytań do różnych typów danych przy użyciu przykładowych zapytań.

### <a name="tools"></a>narzędzia

Narzędzia potrzebne do wysyłania zapytań:
    - Azure Synapse Studio (wersja zapoznawcza)
    - Azure Data Studio
    - SQL Server Management Studio

### <a name="demo-setup"></a>Konfiguracja demonstracyjna

Pierwszym krokiem jest **utworzenie bazy danych** , w której będą wykonywane zapytania. Następnie zainicjujesz obiekty, wykonując [skrypt Instalatora](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) w tej bazie danych. 

Ten skrypt instalacyjny utworzy źródła danych, poświadczenia w zakresie bazy danych i zewnętrzne formaty plików, które są używane do odczytywania danych w tych przykładach.

> [!NOTE]
> Bazy danych są używane tylko do wyświetlania metadanych, a nie dla rzeczywistych danych.  Zapisz nazwę używanej bazy danych, która będzie potrzebna później.

```sql
CREATE DATABASE mydbname;
```

### <a name="provided-demo-data"></a>Podane dane demonstracyjne

Dane demonstracyjne zawierają następujące zestawy danych:

- NYC taksówki — w przypadku danych o formacie CSV i Parquet — część publicznego NYC
- Zestaw danych populacji w formacie CSV
- Przykładowe pliki Parquet z kolumnami zagnieżdżonymi
- Książki w formacie JSON

| Ścieżka folderu                                                  | Opis                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| CSV                                                        | Folder nadrzędny dla danych w formacie CSV                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Foldery z plikami danych populacji w różnych formatach CSV. |
| /csv/taxi/                                                   | Folder z plikami danych publicznych NYC w formacie CSV              |
| Parquet                                                    | Folder nadrzędny dla danych w formacie Parquet                     |
| /parquet/taxi                                                | NYC publiczne pliki danych w formacie Parquet, podzielone na partycje według roku i miesiąca przy użyciu schematu partycjonowania Hive/Hadoop. |
| /parquet/nested/                                             | Przykładowe pliki Parquet z kolumnami zagnieżdżonymi                     |
| kodu                                                       | Folder nadrzędny dla danych w formacie JSON                        |
| /json/books/                                                 | Pliki JSON z danymi książek                                   |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wykonywania zapytań dotyczących różnych typów plików i tworzenia widoków i korzystania z nich, zobacz następujące artykuły:

- [Wykonywanie zapytań o pliki CSV](query-single-csv-file.md)
- [Wykonywanie zapytań względem plików Parquet](query-parquet-files.md)
- [Wykonywanie zapytań względem plików JSON](query-json-files.md)
- [Wartości zagnieżdżone zapytania](query-parquet-nested-types.md)
- [Foldery zapytań i wiele plików CSV](query-folders-multiple-csv-files.md)
- [Korzystanie z metadanych plików w zapytaniach](query-specific-files.md)
- [Tworzenie widoków i korzystanie z nich](create-use-views.md)
