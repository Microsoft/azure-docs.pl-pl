---
title: Indeksowanie tabel
description: Zalecenia i przykłady dotyczące indeksowania tabel w dedykowanej puli SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: fea314d595fb39a1e35dec8ab24533ad4b893f98
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448071"
---
# <a name="indexing-dedicated-sql-pool-tables-in-azure-synapse-analytics"></a>Indeksowanie dedykowanych tabel puli SQL w usłudze Azure Synapse Analytics

Zalecenia i przykłady dotyczące indeksowania tabel w dedykowanej puli SQL.

## <a name="index-types"></a>Typy indeksów

Dedykowana Pula SQL oferuje kilka opcji indeksowania, takich jak [klastrowane indeksy magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [indeksy klastrowane i indeksy nieklastrowane](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), a także opcję, która nie jest indeksem, nazywana również [stertą](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  

Aby utworzyć tabelę z indeksem, zapoznaj się z dokumentacją [CREATE TABLE (dedykowana Pula SQL)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .

## <a name="clustered-columnstore-indexes"></a>Klastrowane indeksy magazynu kolumn

Domyślnie dedykowana Pula SQL tworzy klastrowany indeks magazynu kolumn, gdy w tabeli nie określono żadnych opcji indeksu. Klastrowane tabele magazynu kolumn oferują zarówno najwyższy poziom kompresji danych, jak i najlepszą ogólną wydajność zapytań.  Klastrowane tabele magazynu kolumn zwykle outperformją tabele indeksu lub sterty klastrowane i zazwyczaj najlepiej sprawdzają się w przypadku dużych tabel.  Z tego względu klastrowana magazyn kolumn jest najlepszym miejscem do uruchomienia, gdy nie masz pewności, jak indeksować tabelę.  

Aby utworzyć klastrowaną tabelę magazynu kolumn, wystarczy określić KLASTROWANY indeks magazynu kolumn w klauzuli WITH lub pozostawić klauzulę WITH off:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Istnieje kilka scenariuszy, w których klastrowana magazyn kolumn może nie być dobrą opcją:

- Tabele magazynu kolumn nie obsługują varchar (max), nvarchar (max) i varbinary (max). Zamiast tego Rozważ użycie sterty lub indeksu klastrowanego.
- Tabele magazynu kolumn mogą być mniej wydajne w przypadku danych przejściowych. Rozważ użycie sterty, a nawet tabel tymczasowych.
- Małe tabele zawierające mniej niż 60 000 000 wierszy. Uwzględnij tabele sterty.

## <a name="heap-tables"></a>Tabele sterty

Po tymczasowym wypełnieniu danych w dedykowanej puli SQL może się okazać, że korzystanie z tabeli sterty przyspiesza cały proces. Jest to spowodowane tym, że obciążenia na sterty są szybsze niż w przypadku tabel indeksów, a w niektórych przypadkach można wykonać kolejne operacje odczytu z pamięci podręcznej.  W przypadku ładowania danych tylko w celu przygotowania ich przed uruchomieniem większej liczby przekształceń ładowanie tabeli do sterty jest znacznie szybsze niż załadowanie danych do tabeli klastrowanej magazynu kolumn. Ponadto ładowanie danych do [tymczasowej tabeli](sql-data-warehouse-tables-temporary.md) ładuje się szybciej niż ładowanie tabeli do magazynu trwałego.  Po załadowaniu danych można tworzyć indeksy w tabeli w celu przyspieszenia wykonywania zapytań.  

Tabele magazynu kolumn klastra zaczynają optymalną kompresję, gdy istnieje więcej niż 60 000 000 wierszy.  W przypadku małych tabel odnośników, mniej niż 60 000 000 wierszy, rozważ użycie STERTy lub indeksu klastrowanego w celu przyspieszenia wykonywania zapytań. 

Aby utworzyć tabelę sterty, wystarczy określić STERTę w klauzuli WITH:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Indeksy klastrowane i nieklastrowane

Indeksy klastrowane mogą outperform klastrowane tabele magazynu kolumn, gdy trzeba szybko pobrać pojedynczy wiersz. W przypadku zapytań, w których jeden lub bardzo kilka wierszy wyszukiwania jest wymaganych do wykonania z najwyższą szybkością, należy rozważyć indeks klastra lub pomocniczy indeks nieklastrowany. Wadą użycia indeksu klastrowanego jest to, że tylko zapytania, które są korzystne, są tymi, które używają filtru wysoce selektywnego w kolumnie indeks klastrowany. Aby poprawić filtr dla innych kolumn, można dodać indeks nieklastrowany do innych kolumn. Jednak każdy indeks dodawany do tabeli dodaje zarówno miejsce, jak i czas przetwarzania do załadowania.

Aby utworzyć klastrowaną tabelę indeksów, wystarczy określić indeks KLASTROWANY w klauzuli WITH:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Aby dodać indeks nieklastrowany do tabeli, należy użyć następującej składni:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Optymalizowanie klastrowanych indeksów magazynu kolumn

Klastrowane tabele magazynu kolumn są zorganizowane w postaci danych do segmentów.  Wysoka jakość segmentu ma kluczowe znaczenie dla osiągnięcia optymalnej wydajności zapytań w tabeli magazynu kolumn.  Jakość segmentu może być mierzona przez liczbę wierszy w skompresowanej grupie wierszy.  Jakość segmentu jest najbardziej optymalna, gdy istnieją co najmniej 100 000 wiersze dla skompresowanej grupy wierszy i uzyskać wydajność, ponieważ liczba wierszy w grupie wierszy zbliża się do 1 048 576 wierszy, które są najbardziej wierszami grupy wierszy.

Poniższy widok można utworzyć i użyć w systemie w celu obliczenia średniej liczby wierszy na grupę wierszy i zidentyfikowania wszelkich nieoptymalnych indeksów magazynu kolumn klastra.  Ostatnia kolumna w tym widoku generuje instrukcję SQL, której można użyć do odbudowania indeksów.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Teraz, gdy został utworzony widok, Uruchom to zapytanie, aby zidentyfikować tabele z grupami wierszy, które mają mniej niż 100 000 wierszy. Oczywiście możesz chcieć zwiększyć próg 100 000, jeśli szukasz bardziej optymalnej jakości segmentu.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Po uruchomieniu zapytania możesz zacząć przeglądać dane i analizować wyniki. W tej tabeli opisano, co należy szukać w analizie grupy wierszy.

| Kolumna | Jak korzystać z tych danych |
| --- | --- |
| [table_partition_count] |Jeśli tabela jest podzielona na partycje, może być konieczne wyświetlenie większej liczby otwartych grup wierszy. Z każdą partycją w dystrybucji może być skojarzona otwarta grupa wierszy. Przeznaczenie tej operacji do analizy. Niewielką tabelę, która została podzielona na partycje, można zoptymalizować, usuwając w ten sposób partycjonowanie. |
| [row_count_total] |Łączna liczba wierszy w tabeli. Na przykład możesz użyć tej wartości, aby obliczyć procent wierszy w stanie skompresowanym. |
| [row_count_per_distribution_MAX] |Jeśli wszystkie wiersze są równomiernie rozłożone, ta wartość będzie docelową liczbą wierszy na dystrybucję. Porównaj tę wartość z compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Łączna liczba wierszy w formacie magazynu kolumn dla tabeli. |
| [COMPRESSED_rowgroup_rows_AVG] |Jeśli średnia liczba wierszy jest znacznie mniejsza od maksymalnej liczby wierszy dla grupy wierszy, rozważ użycie CTAS lub ALTER INDEX Rebuild w celu ponownego skompresowania danych |
| [COMPRESSED_rowgroup_count] |Liczba grup wierszy w formacie magazynu kolumn. Jeśli ta liczba jest bardzo wysoka w odniesieniu do tabeli, jest wskaźnikiem, że gęstość magazynu kolumn jest niska. |
| [COMPRESSED_rowgroup_rows_DELETED] |Wiersze są logicznie usuwane w formacie magazynu kolumn. Jeśli liczba jest wysoka względem rozmiaru tabeli, Rozważ ponowne utworzenie partycji lub odbudowanie indeksu, ponieważ spowoduje to usunięcie ich fizycznie. |
| [COMPRESSED_rowgroup_rows_MIN] |Użyj tej opcji w połączeniu z kolumnami AVG i MAX, aby zrozumieć zakres wartości dla grup wierszy w magazynie kolumn. Mała liczba powyżej progu obciążenia (102 400 na rozproszenie na partycję) sugeruje, że optymalizacje są dostępne w ładowaniu danych |
| [COMPRESSED_rowgroup_rows_MAX] |Jak powyżej |
| [OPEN_rowgroup_count] |Otwarte grupy wierszy są normalne. Jedna z nich powinna oczekiwać jednej OTWARTEj grupy wierszy na dystrybucję tabeli (60). Nadmierne liczby sugerują ładowanie danych między partycjami. Sprawdź dokładnie strategię partycjonowania, aby upewnić się, że jest to dźwięk |
| [OPEN_rowgroup_rows] |Każda grupa wierszy może zawierać 1 048 576 wierszy. Użyj tej wartości, aby sprawdzić, w jaki sposób są obecnie w pełni otwarte grupy wierszy. |
| [OPEN_rowgroup_rows_MIN] |Otwarte grupy wskazują, że dane są Trickle ładowane do tabeli lub że poprzednie obciążenie zostało rozlane względem pozostałych wierszy w tej grupie wierszy. Użyj wartości minimalna, maksymalna, średnia kolumna, aby zobaczyć, ile danych jest SAT w otwartych grupach wierszy. W przypadku małych tabel może to potrwać do 100% wszystkich danych! W takim przypadku należy ponownie skompilować polecenie ALTER INDEX, aby wymusić dane do magazynu kolumn. |
| [OPEN_rowgroup_rows_MAX] |Jak powyżej |
| [OPEN_rowgroup_rows_AVG] |Jak powyżej |
| [CLOSED_rowgroup_rows] |Zapoznaj się z wierszem zamkniętej grupy wierszy jako Sanity. |
| [CLOSED_rowgroup_count] |Liczba zamkniętych grup wierszy powinna być niska, jeśli którykolwiek z nich jest widoczny. Zamknięte grupy wierszy można przekonwertować na skompresowane grupy wierszy przy użyciu instrukcji ALTER INDEX... Reorganizuj polecenie. Nie jest to jednak zwykle wymagane. Zamknięte grupy są automatycznie konwertowane na grupy wierszy magazynu kolumn za pomocą procesu "proces przenoszenia spójnej kolekcji". |
| [CLOSED_rowgroup_rows_MIN] |Zamknięte grupy wierszy powinny mieć bardzo wysoką szybkość wypełniania. Jeśli stawka wypełniania zamkniętej grupy wierszy jest niska, wymagana jest dodatkowa analiza magazynu kolumn. |
| [CLOSED_rowgroup_rows_MAX] |Jak powyżej |
| [CLOSED_rowgroup_rows_AVG] |Jak powyżej |
| [Rebuild_Index_SQL] |SQL w celu odbudowania indeksu magazynu kolumn dla tabeli |

## <a name="causes-of-poor-columnstore-index-quality"></a>Przyczyny niskiej jakości indeksu magazynu kolumn

Jeśli zidentyfikowano tabele z niską jakością segmentu, chcesz zidentyfikować główną przyczynę.  Poniżej przedstawiono niektóre inne typowe przyczyny niskiej jakości segmentu:

1. Wykorzystanie pamięci po skompilowaniu indeksu
2. Duża liczba operacji DML
3. Operacje ładowania małego lub Trickle
4. Zbyt wiele partycji

Te czynniki mogą spowodować, że indeks magazynu kolumn będzie znacznie mniejszy niż optymalny 1 000 000 wierszy na grupę wierszy. Mogą również spowodować przechodzenie wierszy do grupy wierszy różnic zamiast skompresowanej grupy wierszy.

### <a name="memory-pressure-when-index-was-built"></a>Wykorzystanie pamięci po skompilowaniu indeksu

Liczba wierszy na grupę wierszy skompresowanych jest bezpośrednio odnosząca się do szerokości wiersza i ilości pamięci dostępnej do przetworzenia grupy wierszy.  Jeśli wiersze są zapisywane w tabelach magazynu kolumn przy dużym wykorzystaniu pamięci, może to spowodować obniżenie jakości segmentów w magazynie kolumn.  W związku z tym najlepszym rozwiązaniem jest nadanie sesji zapisu w tabelach indeksów magazynu kolumn dostęp do możliwie największej ilości pamięci.  Ponieważ istnieje kompromis między ilością pamięci i współbieżnością, wskazówki dotyczące odpowiedniej alokacji pamięci są zależne od danych w poszczególnych wierszach tabeli, jednostek magazynów danych przydzielonych do systemu i liczby miejsc współbieżności, które można przypisać do sesji, która zapisuje dane w tabeli.

### <a name="high-volume-of-dml-operations"></a>Duża liczba operacji DML

Duża liczba operacji DML, które aktualizują i usuwają wiersze, mogą wprowadzać nieefektywność w magazynie kolumn. Jest to szczególnie prawdziwe, gdy większość wierszy w grupie wierszy jest modyfikowana.

- Usunięcie wiersza z grupy skompresowanych wierszy tylko logicznie oznacza wiersz jako usunięty. Wiersz pozostanie w grupie skompresowanych wierszy do momentu odbudowania partycji lub tabeli.
- Wstawianie wiersza powoduje dodanie wiersza do wewnętrznej tabeli magazynu wierszy zwanej grupą wierszy różnicowych. Wstawiony wiersz nie jest konwertowany na magazyn kolumn do momentu zapełnienia grupy wierszy różnicowych i jest oznaczony jako zamknięty. Grupy wierszy są zamykane, gdy osiągną maksymalną pojemność wynoszącą 1 048 576 wierszy.
- Aktualizacja wiersza w formacie magazynu kolumn jest przetwarzana jako usuwanie logiczne, a następnie INSERT. Wstawiony wiersz może być przechowywany w magazynie różnicowym.

Wsadowe operacje aktualizacji i wstawiania, które przekraczają próg zbiorczy wynoszący 102 400 wierszy dla dystrybucji wyrównanej do partycji, przechodzą bezpośrednio do formatu magazynu kolumn. Jednak przy założeniu, że rozkład równomierny, należy zmodyfikować więcej niż 6 144 000 wierszy w jednej operacji. Jeśli liczba wierszy dla danej dystrybucji wyrównanej do partycji jest mniejsza niż 102 400, wiersze przechodzą do magazynu różnicowego i pozostają tam do momentu wstawienia lub zmodyfikowania wystarczających wierszy, aby zamknąć grupę wierszy, lub indeks został odbudowany.

### <a name="small-or-trickle-load-operations"></a>Operacje ładowania małego lub Trickle

Małe obciążenia, które są przesyłane do dedykowanej puli SQL, są również czasami znane jako obciążenia Trickle. Zwykle przedstawiają one blisko stałego strumienia danych wprowadzanych przez system. Jednak ponieważ ten strumień zbliża się do ciągłego, ilość wierszy nie jest szczególnie duża. Częściej niż nie są znacznie częściej wartości progowej wymaganej do bezpośredniego ładowania do formatu magazynu kolumn.

W takich sytuacjach często lepiej jest wystawić dane w usłudze Azure Blob Storage i pozwolić na jego gromadzenie przed załadowaniem. Ta technika jest często znana jako *mikropartie*.

### <a name="too-many-partitions"></a>Zbyt wiele partycji

Innym zagadnieniem, które należy wziąć pod uwagę, jest wpływ partycjonowania na klastrowanych tabelach magazynu kolumn.  Przed partycjonowaniem dedykowana Pula SQL dzieli już dane na 60 baz danych.  Partycjonowanie dzieli dane.  W przypadku partycjonowania danych należy wziąć pod uwagę, że **każda** partycja wymaga co najmniej 1 000 000 wierszy do skorzystania z klastrowanego indeksu magazynu kolumn.  W przypadku partycjonowania tabeli do 100 partycji, tabela wymaga co najmniej 6 000 000 000 wierszy do skorzystania z klastrowanego indeksu magazynu kolumn (60 dystrybucje *100 partycje* 1 000 000). Jeśli tabela partycji 100 nie zawiera 6 000 000 000 wierszy, zmniejsz liczbę partycji lub Rozważ użycie tabeli sterty.

Po załadowaniu tabel z danymi wykonaj następujące kroki, aby zidentyfikować i skompilować ponownie tabele z nieoptymalnymi klastrowanymi indeksami magazynu kolumn.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Ponowne kompilowanie indeksów w celu zwiększenia jakości segmentu

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Krok 1. identyfikowanie lub Tworzenie użytkownika, który używa odpowiedniej klasy zasobów

Jednym z szybkim sposobem na natychmiastowe zwiększenie jakości segmentu jest odbudowanie indeksu.  KOD SQL zwrócony przez powyższy widok zwraca instrukcję ALTER INDEX Rebuild, której można użyć do odbudowania indeksów. Podczas ponownego kompilowania indeksów należy przydzielić wystarczającą ilość pamięci do sesji, która odtwarza indeks.  W tym celu należy zwiększyć klasę zasobów użytkownika, który ma uprawnienia do odbudowania indeksu w tej tabeli do zalecanej minimalnej wartości.

Poniżej znajduje się przykład sposobu przydzielania większej ilości pamięci do użytkownika przez zwiększenie ich klasy zasobów. Aby współpracować z klasami zasobów, zobacz [klasy zasobów dla zarządzania obciążeniami](resource-classes-for-workload-management.md).

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Krok 2. ponowne kompilowanie klastrowanych indeksów magazynu kolumn przy użyciu wyższego użytkownika klasy zasobów

Zaloguj się jako użytkownik z kroku 1 (np. LoadUser), który jest teraz używany do wyższego poziomu klasy zasobów i wykonaj instrukcje ALTER INDEX. Upewnij się, że ten użytkownik ma uprawnienie ALTER do tabel, w których trwa ponowne kompilowanie indeksu. W poniższych przykładach pokazano, jak ponownie skompilować cały indeks magazynu kolumn lub jak ponownie skompilować jedną partycję. W przypadku dużych tabel bardziej praktyczna jest możliwość ponownego kompilowania indeksów pojedynczej partycji w danym momencie.

Alternatywnie, zamiast odbudować indeks, można skopiować tabelę do nowej tabeli [przy użyciu CTAs](sql-data-warehouse-develop-ctas.md). Jaki jest najlepszy sposób? W przypadku dużych ilości danych CTAS jest zwykle szybszy niż [ALTER index](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). W przypadku mniejszych ilości danych polecenie ALTER INDEX jest łatwiejsze w użyciu i nie wymaga zamiany tabeli.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

Odbudowanie indeksu w dedykowanej puli SQL jest operacją offline.  Aby uzyskać więcej informacji na temat odbudowywania indeksów, zobacz sekcję ALTER INDEX Rebuild w obszarze [defragmentacja indeksów magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-defragmentation?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)i [Zmień indeks](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Krok 3. ulepszone sprawdzanie jakości klastrowanego segmentu magazynu kolumn

Uruchom ponownie zapytanie, które określiło tabelę z niską jakością segmentu i sprawdź, czy jakość segmentu została ulepszona.  Jeśli jakość segmentu nie poprawi się, może to oznaczać, że wiersze w tabeli są bardzo szerokie.  Podczas ponownego kompilowania indeksów Rozważ użycie wyższej klasy zasobów lub jednostek dwu.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Ponowne kompilowanie indeksów przy użyciu CTAS i przełączania partycji

W tym przykładzie używamy instrukcji [CREATE TABLE as Select (CTAs)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) i przełączania partycji w celu odbudowania partycji tabeli.

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Switch IN the rebuilt data with TRUNCATE_TARGET option
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2 WITH (TRUNCATE_TARGET = ON);
```

Aby uzyskać więcej informacji na temat ponownego tworzenia partycji za pomocą CTAS, zobacz [Używanie partycji w dedykowanej puli SQL](sql-data-warehouse-tables-partition.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia tabel, zobacz [Tworzenie tabel](sql-data-warehouse-tables-overview.md).
