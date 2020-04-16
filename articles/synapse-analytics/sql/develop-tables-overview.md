---
title: Projektowanie tabel przy użyciu synapse SQL
description: Wprowadzenie do projektowania tabel w synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: f1eec76d92edc97f7e4058d3afe813f0bb2aae47
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431880"
---
# <a name="design-tables-using-synapse-sql"></a>Projektowanie tabel przy użyciu synapse SQL

Ten dokument zawiera kluczowe pojęcia dotyczące projektowania tabel z pulą SQL i SQL na żądanie (wersja zapoznawcza).  

[SQL na żądanie (wersja zapoznawcza)](on-demand-workspace-overview.md) to usługa zapytań dotyczących danych w usłudze data lake. Nie ma magazynu lokalnego do pozyskiwania danych. [Pula SQL](best-practices-sql-pool.md) reprezentuje zbiór zasobów analitycznych, które są aprowizacji podczas korzystania z Synapse SQL. Rozmiar puli SQL jest określany przez jednostki magazynowania danych (DWU).

W poniższej tabeli wymieniono tematy, które są istotne dla puli SQL vs SQL na żądanie:

| Temat                                                        | Pula SQL | SQL na żądanie |
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
| [Stoły okrężne](#round-robin-tables)                    | Tak                | Nie                      |
| [Typowe metody dystrybucji dla tabel](#common-distribution-methods-for-tables) | Tak                | Nie                      |
| [Partycje](#partitions)                                    | Tak                | Tak                     |
| [Indeksy magazynu kolumn](#columnstore-indexes)                  | Tak                | Nie                      |
| [Statystyki](#statistics)                                    | Tak                | Tak                     |
| [Klucz podstawowy i klucz unikatowy](#primary-key-and-unique-key)    | Tak                | Nie                      |
| [Polecenia do tworzenia tabel](#commands-for-creating-tables) | Tak                | Nie                      |
| [Wyrównywanie danych źródłowych do magazynu danych](#aligning-source-data-with-the-data-warehouse) | Tak                | Nie                      |
| [Nieobsługiwały funkcje tabeli](#unsupported-table-features)    | Tak                | Nie                      |
| [Kwerendy rozmiaru tabeli](#table-size-queries)                    | Tak                | Nie                      |

## <a name="determine-table-category"></a>Określanie kategorii tabeli

[Schemat gwiazdy](https://en.wikipedia.org/wiki/Star_schema) organizuje dane w tabelach faktów i wymiarów. Niektóre tabele są używane do integracji lub przemieszczania danych przed przejściem do tabeli faktów lub wymiarów. Podczas projektowania tabeli zdecyduj, czy dane tabeli należą do tabeli faktów, wymiarów czy integracji. Niniejsza decyzja informuje o odpowiedniej strukturze i dystrybucji tabeli.

- **Tabele faktów** zawierają dane ilościowe, które są zwykle generowane w systemie transakcyjnym, a następnie ładowane do magazynu danych. Na przykład firma detaliczna generuje transakcje sprzedaży codziennie, a następnie ładuje dane do tabeli faktów magazynu danych do analizy.

- **Tabele wymiarów** zawierają dane atrybutów, które mogą ulec zmianie, ale zwykle zmieniają się rzadko. Na przykład nazwa i adres klienta są przechowywane w tabeli wymiarów i aktualizowane tylko wtedy, gdy profil klienta się zmieni. Aby zminimalizować rozmiar dużej tabeli faktów, nazwa i adres odbiorcy nie muszą znajdować się w każdym wierszu tabeli faktów. Zamiast tego tabela faktów i tabela wymiarów mogą współużytkować identyfikator klienta. Kwerenda może dołączyć do dwóch tabel, aby skojarzyć profil klienta i transakcje.

- **Tabele integracji** zapewniają miejsce do integracji lub przemieszczania danych. Tabelę integracji można utworzyć jako zwykłą tabelę, tabelę zewnętrzną lub tabelę tymczasową. Na przykład można załadować dane do tabeli przemieszczania, wykonać przekształcenia na danych w przemieszczania, a następnie wstawić dane do tabeli produkcyjnej.

## <a name="schema-names"></a>Nazwy schematów

Schematy są dobrym sposobem na grupowanie obiektów, które są używane w podobny sposób. Poniższy kod tworzy [schemat zdefiniowany przez użytkownika](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) o nazwie wwi.

```sql
CREATE SCHEMA wwi;
```

## <a name="table-names"></a>Nazwy tabel

Jeśli migrujesz wiele baz danych z rozwiązania prem do puli SQL, najlepszym rozwiązaniem jest migracja wszystkich tabel faktów, wymiarów i integracji do jednego schematu puli SQL. Na przykład można przechowywać wszystkie tabele w hurtowni danych próbki [WideWorldImportersDW](/sql/samples/wide-world-importers-dw-database-catalog?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) w jednym schemacie o nazwie wwi.

Aby wyświetlić organizację tabel w puli SQL, można użyć fakt, dim i int jako prefiksy do nazw tabel. W poniższej tabeli przedstawiono niektóre nazwy schematów i tabel dla programu WideWorldImportersDW.  

| WideWorldImportersDW tabela  | Typ tabeli | Pula SQL |
|:-----|:-----|:------|:-----|
| Miasto | Wymiar | Wwi. DimCity (DimCity) |
| Zamówienie | Fact | Wwi. FactOrder (FactOrder) |

## <a name="table-persistence"></a>Trwałość tabeli

Tabele przechowują dane na stałe w usłudze Azure Storage, tymczasowo w usłudze Azure Storage lub w magazynie danych zewnętrznym do magazynu danych.

### <a name="regular-table"></a>Zwykła tabela

Zwykła tabela przechowuje dane w usłudze Azure Storage jako część magazynu danych. Tabela i dane utrzymują się niezależnie od tego, czy sesja jest otwarta.  Poniższy przykład tworzy zwykłą tabelę z dwiema kolumnami.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Tabela tymczasowa

Tabela tymczasowa istnieje tylko na czas trwania sesji. Tabela tymczasowa umożliwia wyświetlanie tymczasowych wyników przez innych użytkowników. Korzystanie z tabel tymczasowych zmniejsza również potrzebę oczyszczania.  Tabele tymczasowe wykorzystują magazyn lokalny i w puli SQL mogą oferować szybszą wydajność.  

SQL na żądanie obsługuje tabele tymczasowe. Ale jego użycie jest ograniczone, ponieważ można wybrać z tabeli tymczasowej, ale nie można dołączyć go z plikami w magazynie.

Aby uzyskać więcej informacji, zobacz [Tabele tymczasowe](develop-tables-temporary.md).

### <a name="external-table"></a>Tabela zewnętrzna

[Tabele zewnętrzne](develop-tables-external-tables.md) wskazują dane znajdujące się w obiekcie blob usługi Azure Storage lub usłudze Azure Data Lake Storage.

Importowanie danych z tabel zewnętrznych do puli SQL przy użyciu instrukcji [CREATE TABLE AS SELECT.](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) Aby zapoznać się z samouczkiem ładowania, zobacz [PolyBase do ładowania danych z magazynu obiektów blob platformy Azure](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

W przypadku języka SQL na żądanie można użyć [cetas,](develop-tables-cetas.md) aby zapisać wynik kwerendy w tabeli zewnętrznej w usłudze Azure Storage.

## <a name="data-types"></a>Typy danych

Pula SQL obsługuje najczęściej używane typy danych. Aby uzyskać listę obsługiwanych typów danych, zobacz [typy danych w odwołaniu DO TWORZENIA TABELI](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes) w instrukcji CREATE TABLE. Aby uzyskać więcej informacji na temat używania typów danych, zobacz [Typy danych](../sql/develop-tables-data-types.md).

## <a name="distributed-tables"></a>Rozproszone tabele

Podstawową cechą puli SQL jest sposób, w jaki może przechowywać i działać na tabelach w [różnych dystrybucjach.](../sql-data-warehouse/massively-parallel-processing-mpp-architecture.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#distributions)  Pula SQL obsługuje trzy metody dystrybucji danych:

- Działanie okrężne (ustawienie domyślne)
- Skrót
- Replikowane

### <a name="hash-distributed-tables"></a>Tabele dystrybuowane przy użyciu skrótu

Tabela rozproszona mieszania dystrybuuje wiersze na podstawie wartości w kolumnie dystrybucji. Tabela rozproszona mieszania jest przeznaczony do osiągnięcia wysokiej wydajności dla zapytań w dużych tabelach. Istnieje kilka czynników, które należy wziąć pod uwagę przy wyborze kolumny dystrybucji.

Aby uzyskać więcej informacji, zobacz [Wskazówki dotyczące projektowania tabel rozproszonych](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="replicated-tables"></a>Zreplikowane tabele

Replikowana tabela ma pełną kopię tabeli dostępnej w każdym węźle obliczeniowym. Kwerendy są uruchamiane szybko w tabelach replikowanych, ponieważ sprzężenia w tabelach replikowanych nie wymagają przenoszenia danych. Replikacja wymaga dodatkowego miejsca do magazynowania i nie jest praktyczna w przypadku dużych tabel.

Aby uzyskać więcej informacji, zobacz [Wskazówki dotyczące projektowania tabel replikowanych](../sql-data-warehouse/design-guidance-for-replicated-tables.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="round-robin-tables"></a>Stoły okrężne

Tabela okrężna rozmieszcza wiersze tabeli równomiernie we wszystkich dystrybucjach. Wiersze są rozdzielane losowo. Ładowanie danych do tabeli okrężne jest szybkie.  Jednak kwerendy mogą wymagać więcej przenoszenia danych niż inne metody dystrybucji.

Aby uzyskać więcej informacji, zobacz [Wskazówki dotyczące projektowania tabel rozproszonych](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="common-distribution-methods-for-tables"></a>Typowe metody dystrybucji dla tabel

Kategoria tabeli często określa optymalną opcję dla dystrybucji tabeli.

| Kategoria tabeli | Zalecana opcja dystrybucji |
|:---------------|:--------------------|
| Fact           | Użyj dystrybucji skrótu z indeksem klastrowanego magazynu kolumn. Zwiększa wydajność, gdy dwie tabele mieszania są połączone w tej samej kolumnie dystrybucji. |
| Wymiar      | Użyj replikowane dla mniejszych tabel. Jeśli tabele są zbyt duże do przechowywania w każdym węźle obliczeniowym, należy użyć mieszania rozproszone. |
| Przygotowanie        | Użyj okrężnego dla tabeli przemieszczania. Obciążenie ctas jest szybkie. Gdy dane są w tabeli przemieszczania, należy użyć INSERT... WYBIERZ, aby przenieść dane do tabel produkcyjnych. |

## <a name="partitions"></a>Partycje

W puli SQL tabela podzielona na partycje przechowuje i wykonuje operacje w wierszach tabeli zgodnie z zakresami danych. Na przykład tabela może być podzielony na partycje według dnia, miesiąca lub roku. Można zwiększyć wydajność kwerendy poprzez eliminację partycji, co ogranicza skanowanie kwerendy do danych w ramach partycji.

Można również zachować dane za pomocą przełączania partycji. Ponieważ dane w puli SQL jest już rozproszone, zbyt wiele partycji może spowolnić wydajność kwerendy. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące partycjonowania](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).  

> [!TIP]
> Podczas przełączania partycji do partycji tabeli, które nie są puste, należy rozważyć użycie opcji TRUNCATE_TARGET w instrukcji [TABELA ALTER,](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) jeśli istniejące dane mają być obcięty.

Poniższy kod przełącza przekształcone dane dzienne do partycji SalesFact i zastępuje wszystkie istniejące dane.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

W języku SQL na żądanie można ograniczyć pliki/foldery (partycje), które będą odczytywane przez kwerendę. Partycjonowanie według ścieżki jest obsługiwane przy użyciu funkcji ścieżki plików i fileinfo opisanych w [querying storage files](develop-storage-files-overview.md). W poniższym przykładzie odczytuje się folder z danymi za rok 2017:

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

Domyślnie pula SQL przechowuje tabelę jako indeks klastrowanego magazynu kolumn. Ta forma magazynu danych osiąga wysoką kompresję danych i wydajność zapytań w dużych tabelach.  Indeks klastrowanego magazynu kolumn jest zwykle najlepszym wyborem, ale w niektórych przypadkach indeks klastrowany lub sterty jest odpowiednią strukturą magazynu.  

> [!TIP]
> Tabela sterty może być szczególnie przydatna do ładowania danych przejściowych, takich jak tabela przejściowa, która jest przekształcana w tabelę końcową.

Aby uzyskać listę funkcji magazynu kolumn, zobacz [Co nowego w indeksach magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Aby poprawić wydajność indeksu magazynu kolumn, zobacz [Maksymalizacja jakości grup wierszy dla indeksów magazynu kolumn](../sql/data-load-columnstore-compression.md).

## <a name="statistics"></a>Statystyki


Optymalizator kwerendy używa statystyk na poziomie kolumny, gdy tworzy plan wykonywania kwerendy. Aby zwiększyć wydajność kwerendy, ważne jest, aby mieć statystyki dla poszczególnych kolumn, zwłaszcza kolumn używanych w sprzężeniach kwerendy. Synapse SQL obsługuje automatyczne tworzenie statystyk. 

Aktualizacja statystyczna nie odbywa się automatycznie. Aktualizuj statystyki po dodaniu lub zmianie znacznej liczby wierszy. Na przykład zaktualizować statystyki po załadowaniu. Dodatkowe informacje znajdują się w [artykule wytycznych dotyczących statystyk.](develop-tables-statistics.md)

## <a name="primary-key-and-unique-key"></a>Klucz podstawowy i klucz unikatowy

Klucz podstawowy jest obsługiwany tylko wtedy, gdy są używane nieklastrowane i niewymnaczone.  Ograniczenie UNIQUE jest obsługiwane tylko wtedy, gdy jest używane NOT ENFORCED.  Aby uzyskać więcej informacji, zobacz artykuł [Ograniczenia tabeli puli SQL.](../sql-data-warehouse/sql-data-warehouse-table-constraints.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="commands-for-creating-tables"></a>Polecenia do tworzenia tabel

Tabelę można utworzyć jako nową pustą tabelę. Można również utworzyć i wypełnić tabelę z wynikami instrukcji select. Poniżej przedstawiono polecenia T-SQL służące do tworzenia tabeli.

| Instrukcja T-SQL | Opis |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tworzy pustą tabelę, definiując wszystkie kolumny i opcje tabeli. |
| [TWORZENIE TABELI ZEWNĘTRZNEJ](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tworzy tabelę zewnętrzną. Definicja tabeli jest przechowywana w puli SQL. Dane tabeli są przechowywane w magazynie obiektów Blob platformy Azure lub usługi Azure Data Lake Storage. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Wypełnia nową tabelę wynikami instrukcji select. Kolumny tabeli i typy danych są oparte na wynikach instrukcji select. Aby zaimportować dane, ta instrukcja można wybrać z tabeli zewnętrznej. |
| [TWORZENIE TABELI ZEWNĘTRZNEJ JAKO WYBIERZ](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tworzy nową tabelę zewnętrzną, eksportując wyniki instrukcji select do lokalizacji zewnętrznej.  Lokalizacja jest magazyn obiektów Blob platformy Azure lub usługi Azure Data Lake Storage. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Wyrównywanie danych źródłowych do magazynu danych

Tabele magazynu danych są wypełniane przez ładowanie danych z innego źródła danych. Aby osiągnąć pomyślne obciążenie, liczba i typy danych kolumn w danych źródłowych muszą być zgodne z definicją tabeli w magazynie danych.

> [!NOTE]
> Wprowadzenie danych do wyrównania może być najtrudniejszą częścią projektowania tabel.

Jeśli dane pochodzą z wielu magazynów danych, można przenieść dane do magazynu danych i przechowywać je w tabeli integracji. Gdy dane znajdują się w tabeli integracji, można użyć mocy puli SQL do zaimplementowania operacji transformacji. Po przygotowaniu danych można je wstawić do tabel produkcyjnych.

## <a name="unsupported-table-features"></a>Nieobsługiwały funkcje tabeli

Pula SQL obsługuje wiele, ale nie wszystkie, funkcji tabeli oferowanych przez inne bazy danych.  Na poniższej liście przedstawiono niektóre funkcje tabeli, które nie są obsługiwane w puli SQL.

- Klucz obcy, sprawdź [ograniczenia tabeli](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Kolumny obliczane](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Widoki indeksowane](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Sequence](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Rozrzedzone kolumny](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- Klucze zastępcze, implementuj z [tożsamością](../sql-data-warehouse/sql-data-warehouse-tables-identity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Synonimy](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Wyzwalacze](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Unikalne indeksy](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Typy zdefiniowane przez użytkownika](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="table-size-queries"></a>Kwerendy rozmiaru tabeli

Jednym z prostych sposobów identyfikowania miejsca i wierszy zużywanych przez tabelę w każdej z 60 dystrybucji jest użycie [PDW_SHOWSPACEUSED DBCC](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Należy pamiętać, że używanie poleceń DBCC może być dość ograniczające.  Dynamiczne widoki zarządzania (DMV) pokazują więcej szczegółów niż polecenia DBCC. Zacznij od utworzenia widoku poniżej.

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

### <a name="table-space-summary"></a>Podsumowanie miejsca w tabeli

Ta kwerenda zwraca wiersze i spację według tabeli.  Podsumowanie obszaru tabeli pozwala sprawdzić, które tabele są twoimi największymi tabelami. Zobaczysz również, czy są one okrężne, replikowane lub rozproszone skrótem.  W przypadku tabel rozproszonych mieszania kwerenda pokazuje kolumnę dystrybucyjną.  

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

### <a name="table-space-by-index-type"></a>Spacja tabeli według typu indeksu

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

### <a name="distribution-space-summary"></a>Podsumowanie przestrzeni dystrybucyjnej

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

Po utworzeniu tabel dla magazynu danych następnym krokiem jest załadowanie danych do tabeli.  Aby zapoznać się z samouczkiem ładowania, zobacz [Ładowanie danych do puli SQL](../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#load-the-data-into-sql-pool).
