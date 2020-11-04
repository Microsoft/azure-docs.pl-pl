---
title: Projektowanie tabel przy użyciu języka SQL Synapse
description: Wprowadzenie do projektowania tabel w programie SQL Synapse.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 1c12727e08c6ec9075aa6c1e256279ab7596417b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324526"
---
# <a name="design-tables-using-synapse-sql-in-azure-synapse-analytics"></a>Projektowanie tabel przy użyciu języka SQL Synapse w usłudze Azure Synapse Analytics

Ten dokument zawiera najważniejsze pojęcia dotyczące projektowania tabel z dedykowaną pulą SQL i bezserwerową pulą SQL (wersja zapoznawcza).  

[Pula SQL Bezserwerowa (wersja zapoznawcza)](on-demand-workspace-overview.md) to usługa zapytań dla danych w usłudze Data Lake. Nie ma magazynu lokalnego na potrzeby pozyskiwania danych. [Dedykowana Pula SQL](best-practices-sql-pool.md) reprezentuje kolekcję zasobów analitycznych, które są obsługiwane podczas korzystania z programu Synapse SQL. Rozmiar dedykowanej puli SQL jest określany przez jednostki magazynowania danych (jednostek dwu).

W poniższej tabeli wymieniono tematy istotne dla dedykowanej puli SQL i bezserwerowej puli SQL:

| Temat                                                        | dedykowana Pula SQL | Pula SQL bezserwerowa |
| ------------------------------------------------------------ | ------------------ | ----------------------- |
| [Określanie kategorii tabeli](#determine-table-category)        | Tak                | Nie                      |
| [Nazwy schematów](#schema-names)                                | Tak                | Tak                     |
| [Nazwy tabel](#table-names)                                  | Tak                | Nie                      |
| [Trwałość tabeli](#table-persistence)                      | Tak                | Nie                      |
| [Zwykła tabela](#regular-table)                              | Tak                | Nie                      |
| [Tabela tymczasowa](#temporary-table)                          | Tak                | Tak                     |
| [Tabela zewnętrzna](#external-table)                            | Tak                | Tak                     |
| [Typy danych](#data-types)                                    | Tak                | Tak                     |
| [Rozproszone tabele](#distributed-tables)                    | Tak                | Nie                      |
| [Tabele dystrybuowane przy użyciu skrótu](#hash-distributed-tables)          | Tak                | Nie                      |
| [Zreplikowane tabele](#replicated-tables)                      | Tak                | Nie                      |
| [Tabele działające w trybie okrężnym](#round-robin-tables)                    | Tak                | Nie                      |
| [Typowe metody dystrybucji dla tabel](#common-distribution-methods-for-tables) | Tak                | Nie                      |
| [Partycje](#partitions)                                    | Tak                | Tak                     |
| [Indeksy magazynu kolumn](#columnstore-indexes)                  | Tak                | Nie                      |
| [Statystyki](#statistics)                                    | Tak                | Tak                     |
| [Klucz podstawowy i unikatowy klucz](#primary-key-and-unique-key)    | Tak                | Nie                      |
| [Polecenia służące do tworzenia tabel](#commands-for-creating-tables) | Tak                | Nie                      |
| [Wyrównywanie danych źródłowych z magazynem danych](#align-source-data-with-the-data-warehouse) | Tak                | Nie                      |
| [Nieobsługiwane funkcje tabeli](#unsupported-table-features)    | Tak                | Nie                      |
| [Zapytania o rozmiar tabeli](#table-size-queries)                    | Tak                | Nie                      |

## <a name="determine-table-category"></a>Określanie kategorii tabeli

[Schemat gwiazdy](https://en.wikipedia.org/wiki/Star_schema) organizuje dane w postaci tabel faktów i wymiarów. Niektóre tabele są używane do integracji lub danych przemieszczania przed przechodzeniem do tabeli faktów lub wymiarów. Podczas projektowania tabeli należy zdecydować, czy dane tabeli należą do tabeli faktów, wymiarów lub integracji. Ta decyzja informuje o odpowiedniej strukturze i dystrybucji tabeli.

- **Tabele faktów** zawierają ilościowe dane, które są często generowane w systemie transakcyjnym, a następnie ładowane do magazynu danych. Na przykład firma detaliczna generuje transakcje sprzedaży codziennie, a następnie ładuje dane do tabeli faktów magazynu danych na potrzeby analizy.

- **Tabele wymiarów** zawierają dane atrybutów, które mogą ulec zmianie, ale zazwyczaj zmieniają się rzadko. Na przykład nazwa i adres klienta są przechowywane w tabeli wymiarów i aktualizowane tylko w przypadku zmiany profilu klienta. Aby zminimalizować rozmiar dużej tabeli faktów, nazwa i adres klienta nie muszą znajdować się w każdym wierszu tabeli faktów. Zamiast tego tabela faktów i tabela wymiarów mogą współdzielić identyfikator klienta. Zapytanie może sprzęgać dwie tabele, aby skojarzyć profil i transakcje klienta.

- **Tabele integracji** zapewniają miejsce do integrowania lub przygotowywania danych. Tabelę integracji można utworzyć jako tabelę regularną, tabelę zewnętrzną lub tabelę tymczasową. Na przykład można załadować dane do tabeli przejściowej, wykonać przekształcenia danych w procesie przejściowym, a następnie wstawić dane do tabeli produkcyjnej.

## <a name="schema-names"></a>Nazwy schematów

Schematy są dobrym sposobem grupowania jednocześnie obiektów, które są używane w podobny sposób. Poniższy kod tworzy [schemat zdefiniowany przez użytkownika](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) o nazwie WWI.

```sql
CREATE SCHEMA wwi;
```

## <a name="table-names"></a>Nazwy tabel

W przypadku migrowania wielu baz danych z rozwiązania Premium do dedykowanej puli SQL najlepszym rozwiązaniem jest Migrowanie wszystkich tabel faktów, wymiarów i integracji do jednego schematu puli SQL. Można na przykład przechowywać wszystkie tabele w [WideWorldImportersDW](/sql/samples/wide-world-importers-dw-database-catalog?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) przykładowego magazynu danych w ramach jednego schematu o nazwie WWI.

Aby wyświetlić organizację tabel w dedykowanej puli SQL, można użyć faktów, Dim i int jako prefiksów nazw tabel. W poniższej tabeli przedstawiono niektóre z nazw schematu i tabeli dla WideWorldImportersDW.  

| Tabela WideWorldImportersDW  | Typ tabeli | dedykowana Pula SQL |
|:-----|:-----|:------|:-----|
| City (Miasto) | Wymiar | WWI. DimCity |
| Zamówienie | Fact | WWI. FactOrder |

## <a name="table-persistence"></a>Trwałość tabeli

Tabele przechowują dane trwale w usłudze Azure Storage, tymczasowo w usłudze Azure Storage lub w magazynie danych zewnętrznym względem magazynu danych.

### <a name="regular-table"></a>Zwykła tabela

Zwykła tabela przechowuje dane w usłudze Azure Storage jako część magazynu danych. Tabela i dane są zachowywane niezależnie od tego, czy sesja jest otwarta.  Poniższy przykład tworzy zwykłą tabelę z dwiema kolumnami.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Tabela tymczasowa

Tabela tymczasowa istnieje tylko na czas trwania sesji. Możesz użyć tabeli tymczasowej, aby uniemożliwić innym użytkownikom wyświetlanie wyników tymczasowych. Korzystanie z tabel tymczasowych również zmniejsza potrzebę czyszczenia.  Tabele tymczasowe wykorzystują magazyn lokalny i w dedykowanych pulach SQL mogą oferować szybszą wydajność.  

Pula SQL bezserwerowa obsługuje tabele tymczasowe. Jednak jego użycie jest ograniczone, ponieważ można wybrać z tabeli tymczasowej, ale nie można dołączyć jej do plików w magazynie.

Aby uzyskać więcej informacji, zobacz  [tabele tymczasowe](develop-tables-temporary.md).

### <a name="external-table"></a>Tabela zewnętrzna

[Zewnętrzne tabele](develop-tables-external-tables.md) wskazują dane znajdujące się w obiekcie blob usługi Azure Storage lub Azure Data Lake Storage.

Importuj dane z tabel zewnętrznych do dedykowanych pul SQL przy użyciu instrukcji [CREATE TABLE as SELECT](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) . Aby zapoznać się z samouczkiem dotyczącym ładowania, zobacz [Korzystanie z bazy danych w celu ładowania dane z usługi Azure Blob Storage](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

W przypadku puli SQL bezserwerowej można użyć [CETAS](develop-tables-cetas.md) do zapisania wyniku zapytania w tabeli zewnętrznej w usłudze Azure Storage.

## <a name="data-types"></a>Typy danych

Dedykowana Pula SQL obsługuje najczęściej używane typy danych. Aby zapoznać się z listą obsługiwanych typów danych, zobacz [typy danych w CREATE TABLE odwołanie](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes&preserve-view=true) w instrukcji CREATE TABLE. Aby uzyskać więcej informacji na temat korzystania z typów danych, zobacz [typy danych](../sql/develop-tables-data-types.md).

## <a name="distributed-tables"></a>Rozproszone tabele

Podstawową funkcją dedykowanej puli SQL jest sposób, w jaki można przechowywać i pracować w tabelach w ramach [dystrybucji](../sql-data-warehouse/massively-parallel-processing-mpp-architecture.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#distributions).  Dedykowana Pula SQL obsługuje trzy metody dystrybucji danych:

- Działanie okrężne (ustawienie domyślne)
- Skrót
- Replikowane

### <a name="hash-distributed-tables"></a>Tabele dystrybuowane przy użyciu skrótu

Rozproszona tabela skrótów dystrybuuje wiersze na podstawie wartości w kolumnie dystrybucja. Rozproszonej tabeli skrótów jest przeznaczony do osiągania wysokiej wydajności dla zapytań w dużych tabelach. Podczas wybierania kolumny dystrybucji należy wziąć pod uwagę kilka czynników.

Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące projektowania tabel rozproszonych](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="replicated-tables"></a>Zreplikowane tabele

Replikowana tabela ma pełną kopię tabeli dostępną w każdym węźle obliczeniowym. Zapytania działają szybko w zreplikowanych tabelach, ponieważ sprzężenia w zreplikowanych tabelach nie wymagają przenoszenia danych. Replikacja wymaga dodatkowego magazynu, ale nie jest praktyczna w przypadku dużych tabel.

Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące projektowania zreplikowanych tabel](../sql-data-warehouse/design-guidance-for-replicated-tables.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="round-robin-tables"></a>Tabele działające w trybie okrężnym

Tabela z działaniem okrężnym dystrybuuje wiersze tabeli równomiernie między wszystkimi dystrybucjami. Wiersze są dystrybuowane losowo. Ładowanie danych do tabeli okrężnej jest szybkie.  Jednak zapytania mogą wymagać więcej ruchów danych niż inne metody dystrybucji.

Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące projektowania tabel rozproszonych](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="common-distribution-methods-for-tables"></a>Typowe metody dystrybucji dla tabel

Kategoria tabeli często określa optymalną opcję dystrybucji tabeli.

| Kategoria tabeli | Zalecana opcja dystrybucji |
|:---------------|:--------------------|
| Fact           | Używaj dystrybucji skrótów z klastrowanym indeksem magazynu kolumn. Zwiększona wydajność, gdy dwie tabele skrótów są przyłączone do tej samej kolumny dystrybucji. |
| Wymiar      | Użyj replikacji dla mniejszych tabel. Jeśli tabele są zbyt duże, aby były przechowywane w poszczególnych węzłach obliczeniowych, użyj rozproszonego tworzenia skrótów. |
| Przygotowanie        | Użyj działania okrężnego dla tabeli przemieszczania. Ładowanie za pomocą CTAS jest szybkie. Gdy dane są w tabeli przemieszczania, użyj instrukcji INSERT... Wybierz, aby przenieść dane do tabel produkcyjnych. |

## <a name="partitions"></a>Partycje

W dedykowanych pulach SQL tabela partycjonowana i wykonuje operacje na wierszach tabeli zgodnie z zakresami danych. Na przykład tabela może być partycjonowana według dnia, miesiąca lub roku. Można poprawić wydajność zapytań za pomocą eliminacji partycji, co ogranicza skanowanie zapytania do danych w ramach partycji.

Możesz również zachować dane za pomocą przełączania partycji. Ponieważ dane w dedykowanej puli SQL są już dystrybuowane, zbyt wiele partycji może powolnej wydajności zapytań. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące partycjonowania](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).  

> [!TIP]
> Gdy partycja przełączy się na partycje tabeli, które nie są puste, rozważ użycie opcji TRUNCATE_TARGET w instrukcji [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) , jeśli istniejące dane mają zostać obcięte.

Kod poniżej przełącza przekształcone dane codziennie na partycję SalesFact i zastępuje wszelkie istniejące dane.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

W puli SQL bezserwerowej można ograniczyć pliki/foldery (partycje), które będą odczytywane przez zapytanie. Partycjonowanie według ścieżki jest obsługiwane przy użyciu funkcji FilePath i FileInfo opisanych w temacie [wykonywanie zapytań dotyczących plików magazynu](develop-storage-files-overview.md). Poniższy przykład odczytuje folder z danymi dla roku 2017:

```sql
SELECT
    nyc.filepath(1) AS [year],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
GROUP BY
    nyc.filepath(1),
    payment_type
ORDER BY
    nyc.filepath(1),
    payment_type
```

## <a name="columnstore-indexes"></a>Indeksy magazynu kolumn

Domyślnie dedykowana Pula SQL przechowuje tabelę jako klastrowany indeks magazynu kolumn. Ta forma magazynu danych zapewnia wysoką kompresję danych i wydajność zapytań w przypadku dużych tabel.  Klastrowany indeks magazynu kolumn jest zazwyczaj najlepszym wyborem, ale w niektórych przypadkach indeks klastrowany lub sterta jest odpowiednią strukturą magazynu.  

> [!TIP]
> Tabela sterty może być szczególnie przydatna do ładowania danych przejściowych, takich jak tabela tymczasowa, która jest przekształcana w ostateczną tabelę.

Aby zapoznać się z listą funkcji magazynu kolumn, zobacz [co nowego w indeksach magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Aby poprawić wydajność indeksu magazynu kolumn, zobacz [maksymalizacja jakości grupy wierszy dla indeksów magazynu kolumn](../sql/data-load-columnstore-compression.md).

## <a name="statistics"></a>Statystyki

Optymalizator zapytań używa statystyk na poziomie kolumny podczas tworzenia planu wykonywania zapytania. Aby poprawić wydajność zapytań, ważne jest posiadanie statystyk dla poszczególnych kolumn, zwłaszcza kolumn używanych w sprzężeniach zapytań. Synapse SQL obsługuje automatyczne tworzenie statystyk. 

Aktualizowanie statystyczne nie odbywa się automatycznie. Aktualizacja statystyk po dodaniu lub zmianie znaczącej liczby wierszy. Na przykład zaktualizuj statystyki po załadowaniu. Dodatkowe informacje znajdują się w artykule [wskazówki dotyczące statystyk](develop-tables-statistics.md) .

## <a name="primary-key-and-unique-key"></a>Klucz podstawowy i unikatowy klucz

W przypadku dedykowanej puli SQL klucz podstawowy jest obsługiwany tylko w przypadku, gdy są używane zarówno nieklastrowane, jak i niewymuszone.  Ograniczenie UNIQUE jest obsługiwane tylko wtedy, gdy nie jest WYMUSZANe.  Aby uzyskać więcej informacji, zobacz artykuł [ograniczenia tabeli puli SQL](../sql-data-warehouse/sql-data-warehouse-table-constraints.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="commands-for-creating-tables"></a>Polecenia służące do tworzenia tabel

W przypadku dedykowanej puli SQL można utworzyć tabelę jako nową pustą tabelę. Możesz również utworzyć i wypełnić tabelę z wynikami instrukcji SELECT. Poniżej przedstawiono polecenia T-SQL służące do tworzenia tabeli.

| Instrukcja T-SQL | Opis |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Tworzy pustą tabelę przez zdefiniowanie wszystkich kolumn i opcji tabeli. |
| [TWORZENIE TABELI ZEWNĘTRZNEJ](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Tworzy tabelę zewnętrzną. Definicja tabeli jest przechowywana w dedykowanej puli SQL. Dane tabeli są przechowywane w usłudze Azure Blob Storage lub Azure Data Lake Storage. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Wypełnia nową tabelę wynikami instrukcji SELECT. Kolumny tabeli i typy danych są oparte na wynikach instrukcji SELECT. Aby zaimportować dane, ta instrukcja może zostać wybrana z tabeli zewnętrznej. |
| [UTWÓRZ TABELĘ ZEWNĘTRZNĄ JAKO WYBRANĄ](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Tworzy nową tabelę zewnętrzną przez wyeksportowanie wyników instrukcji SELECT do lokalizacji zewnętrznej.  Lokalizacją jest usługa Azure Blob Storage lub Azure Data Lake Storage. |

## <a name="align-source-data-with-the-data-warehouse"></a>Wyrównaj dane źródłowe z magazynem danych

Dedykowane tabele puli SQL są wypełniane przez załadowanie danych z innego źródła danych. Aby osiągnąć pomyślne obciążenie, liczba i typy danych kolumn w danych źródłowych muszą być wyrównane z definicją tabeli w magazynie danych.

> [!NOTE]
> Pobieranie danych do wyrównania może być najtrudniejszą częścią projektowania tabel.

Jeśli dane pochodzą z wielu magazynów danych, można przenieść dane do magazynu danych i zapisać je w tabeli integracji. Gdy dane są w tabeli integracji, można użyć możliwości dedykowanej puli SQL do implementowania operacji transformacji. Po przygotowaniu danych można je wstawić do tabel produkcyjnych.

## <a name="unsupported-table-features"></a>Nieobsługiwane funkcje tabeli

Dedykowana Pula SQL obsługuje wiele funkcji tabel oferowanych przez inne bazy danych, ale nie wszystkie.  Na poniższej liście przedstawiono niektóre funkcje tabeli, które nie są obsługiwane w dedykowanej puli SQL.

- Klucz obcy, sprawdzanie [ograniczeń tabeli](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Kolumny obliczane](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Indeksowane widoki](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Sequence](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Kolumny rozrzedzone](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- Klucze zastępcze, implementacja przy użyciu [tożsamości](../sql-data-warehouse/sql-data-warehouse-tables-identity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Synonimy](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Wyzwalacze](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Indeksy unikatowe](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Typy zdefiniowane przez użytkownika](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="table-size-queries"></a>Zapytania o rozmiar tabeli

W dedykowanej puli SQL jeden prosty sposób identyfikowania miejsca i wierszy zużywanych przez tabelę w każdej z dystrybucji 60 to użycie [polecenia DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Należy pamiętać, że użycie poleceń DBCC może być dość ograniczone.  Dynamiczne widoki zarządzania (widoków DMV) pokazują więcej szczegółów niż polecenia DBCC. Zacznij od utworzenia poniższego widoku.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count]
    + nps.[row_overflow_used_page_count]
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count]
 - (nps.[reserved_page_count] - nps.[used_page_count])
 - ([in_row_data_page_count]
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
WHERE pn.[type] = 'COMPUTE'
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT *
FROM size
;
```

### <a name="table-space-summary"></a>Podsumowanie obszaru tabeli

To zapytanie zwraca wiersze i spację według tabeli.  Podsumowanie obszaru tabeli pozwala zobaczyć, które tabele są największą tabelą. Zobaczysz również, czy są to działania okrężne, zreplikowane lub oparte na skrótach.  W przypadku tabel dystrybuowanych przez funkcję mieszania zapytanie pokazuje kolumnę dystrybucji.  

```sql
SELECT
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM
    dbo.vTableSizes
GROUP BY
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Przestrzeń tabeli według typu dystrybucji

```sql
SELECT
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Przestrzeń tabeli według typu indeksu

```sql
SELECT
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Podsumowanie przestrzeni dystrybucji

```sql
SELECT
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Następne kroki

Następnym krokiem po utworzeniu tabel dla hurtowni danych jest załadowanie danych do tabeli.  Aby zapoznać się z samouczkiem ładowania, zobacz [ładowanie danych do dedykowanej puli SQL](../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#load-the-data-into-sql-pool).
