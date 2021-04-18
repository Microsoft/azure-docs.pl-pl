---
title: Indeksowanie tabel
description: Zalecenia i przykłady dotyczące indeksowania tabel w dedykowanej puli SQL.
services: synapse-analytics
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/16/2021
author: XiaoyuMSFT
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 58f3eed8b16ff3ed02c6dfac6dc7d72ebb4ca374
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599982"
---
# <a name="indexing-dedicated-sql-pool-tables-in-azure-synapse-analytics"></a>Indeksowanie dedykowanych tabel puli SQL w Azure Synapse Analytics

Zalecenia i przykłady dotyczące indeksowania tabel w dedykowanej puli SQL.

## <a name="index-types"></a>Typy indeksów

Dedykowana pula SQL oferuje kilka opcji indeksowania, w tym klastrowane indeksy magazynu [kolumn,](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)indeksy klastrowane i indeksy [nieklastrowane](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)oraz opcję bez indeksu znaną również jako [sterta.](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  

Aby utworzyć tabelę z indeksem, zobacz [dokumentację CREATE TABLE (dedykowana pula SQL).](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="clustered-columnstore-indexes"></a>Klastrowane indeksy magazynu kolumn

Domyślnie dedykowana pula SQL tworzy klastrowany indeks magazynu kolumn, gdy w tabeli nie określono żadnych opcji indeksu. Klastrowane tabele magazynu kolumn oferują najwyższy poziom kompresji danych i najlepszą ogólną wydajność zapytań.  Klastrowane tabele magazynu kolumn zwykle mają niższą niż klastrowane tabele indeksów lub sterty i zazwyczaj są najlepszym wyborem w przypadku dużych tabel.  Z tego powodu klastrowany magazyn kolumn jest najlepszym miejscem do rozpoczęcia pracy, gdy nie masz pewności, jak indeksować tabelę.  

Aby utworzyć tabelę klastrowanego magazynu kolumn, wystarczy określić CLUSTERED COLUMNSTORE INDEX w klauzuli WITH lub pozostawić klauzulę WITH wyłączona:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Istnieje kilka scenariuszy, w których klastrowany magazyn kolumn może nie być dobrą opcją:

- Tabele magazynu kolumn nie obsługują funkcji varchar(max), nvarchar(max) i varbinary(max). Zamiast tego rozważ stertę lub indeks klastrowany.
- Tabele magazynu kolumn mogą być mniej wydajne w przypadku danych przejściowych. Rozważ stertę, a może nawet tabele tymczasowe.
- Małe tabele z mniej niż 60 milionami wierszy. Rozważmy tabele sterty.

## <a name="heap-tables"></a>Tabele sterty

W przypadku tymczasowego docelowych danych w dedykowanej puli SQL może się okazać, że użycie tabeli sterty przyspiesza cały proces. Wynika to z tego, że obciążenia na heapsy są szybsze niż indeksowanie tabel, a w niektórych przypadkach kolejne odczyty można wykonać z pamięci podręcznej.  Jeśli dane są ładowane tylko w celu ich etapu przed uruchomieniem większej liczby przekształceń, ładowanie tabeli do tabeli sterty jest znacznie szybsze niż ładowanie danych do tabeli klastrowanych magazynów kolumn. Ponadto ładowanie danych do tabeli tymczasowej [jest](sql-data-warehouse-tables-temporary.md) ładowane szybciej niż ładowanie tabeli do magazynu trwałego.  Po załadowaniu danych możesz utworzyć indeksy w tabeli, aby uzyskać większą wydajność zapytań.  

Tabele magazynu kolumn klastra zaczynają osiągać optymalną kompresję, gdy liczba wierszy wynosi ponad 60 milionów.  W przypadku małych tabel odnośników( mniej niż 60 milionów wierszy) rozważ użycie protokołu HEAP lub indeksu klastrowanego, aby uzyskać większą wydajność zapytań. 

Aby utworzyć tabelę sterty, wystarczy określić heap w klauzuli WITH:

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

Indeksy klastrowane mogą różnić się od tabel klastrowanego magazynu kolumn, gdy trzeba szybko pobrać jeden wiersz. W przypadku zapytań, w których do wykonania z bardzo dużą szybkością jest wymagane pojedyncze lub bardzo mało wyszukiwania wierszy, należy wziąć pod uwagę indeks klastrowany lub indeks pomocniczy nieklastrowany. Wadą korzystania z indeksu klastrowanego jest to, że korzystne są tylko zapytania korzystające z wysoce selektywnego filtru w kolumnie indeksu klastrowanego. Aby ulepszyć filtrowanie innych kolumn, do innych kolumn można dodać indeks nieklastrowany. Jednak każdy indeks dodawany do tabeli dodaje do ładowania zarówno miejsce, jak i czas przetwarzania.

Aby utworzyć tabelę indeksów klastrowanych, wystarczy określić INDEKS KLASTROWANY w klauzuli WITH:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Aby dodać indeks nie klastrowany do tabeli, użyj następującej składni:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Optymalizacja klastrowanych indeksów magazynu kolumn

Klastrowane tabele magazynu kolumn są zorganizowane w dane w segmenty.  Wysoka jakość segmentu ma kluczowe znaczenie dla osiągnięcia optymalnej wydajności zapytań w tabeli magazynu kolumn.  Jakość segmentu można mierzyć na podstawie liczby wierszy w skompresowanych grupach wierszy.  Jakość segmentu jest najbardziej optymalna, gdy istnieje co najmniej 100 000 wierszy na skompresowaną grupę wierszy i zyskują wydajność, ponieważ liczba wierszy na grupę wierszy zbliża się do 1 048 576 wierszy, czyli najwięcej wierszy, które może zawierać grupa wierszy.

Poniższy widok można utworzyć i użyć w systemie do obliczenia średniej liczby wierszy na grupę wierszy i zidentyfikowania dowolnych indeksów magazynu kolumn klastra nie optymalnego.  Ostatnia kolumna w tym widoku generuje instrukcję SQL, która może służyć do ponownego kompilowania indeksów.

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

Teraz, po utworzeniu widoku, uruchom to zapytanie, aby zidentyfikować tabele z grupami wierszy o mniej niż 100 000 wierszy. Oczywiście możesz zwiększyć próg 100 000, jeśli szukasz bardziej optymalnej jakości segmentu.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Po uruchomieniu zapytania możesz rozpocząć analizowanie danych i analizowanie wyników. W tej tabeli wyjaśniono, czego szukać w analizie grup wierszy.

| Kolumna | Jak używać tych danych |
| --- | --- |
| [table_partition_count] |Jeśli tabela jest partycjonowana, można oczekiwać większej liczby otwartych grup wierszy. Teoretycznie z każdą partycją w rozkładie może być skojarzona otwarta grupa wierszy. Należy to podsumować w analizie. Małą tabelę, która została partycjonowana, można zoptymalizować przez całkowite usunięcie partycjonowania, ponieważ poprawi to kompresję. |
| [row_count_total] |Łączna liczba wierszy dla tabeli. Na przykład można użyć tej wartości do obliczenia procentu wierszy w stanie skompresowanym. |
| [row_count_per_distribution_MAX] |Jeśli wszystkie wiersze są dystrybuowane równomiernie, ta wartość będzie docelową liczbą wierszy na dystrybucję. Porównaj tę wartość z wartością compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Łączna liczba wierszy w formacie magazynu kolumn dla tabeli. |
| [COMPRESSED_rowgroup_rows_AVG] |Jeśli średnia liczba wierszy jest znacznie mniejsza niż maksymalna liczba wierszy dla grupy wierszy, rozważ ponowne skompilowanie danych przy użyciu funkcji CTAS lub ALTER INDEX REBUILD |
| [COMPRESSED_rowgroup_count] |Liczba grup wierszy w formacie magazynu kolumn. Jeśli ta liczba jest bardzo wysoka w stosunku do tabeli, jest to wskaźnik niskiej gęstości magazynu kolumn. |
| [COMPRESSED_rowgroup_rows_DELETED] |Wiersze są logicznie usuwane w formacie magazynu kolumn. Jeśli liczba jest wysoka względem rozmiaru tabeli, rozważ ponowne skompilowanie partycji lub ponowne skompilowanie indeksu, ponieważ spowoduje to ich fizyczne usunięcie. |
| [COMPRESSED_rowgroup_rows_MIN] |Użyj tego w połączeniu z kolumnami AVG i MAX, aby zrozumieć zakres wartości dla grup wierszy w twoim sklepie kolumn. Niska liczba powyżej progu obciążenia (102 400 na dystrybucję dostosowaną do partycji) sugeruje, że optymalizacje są dostępne w obciążeniu danych |
| [COMPRESSED_rowgroup_rows_MAX] |Tak jak powyżej |
| [OPEN_rowgroup_count] |Otwarte grupy wierszy są normalne. Można by oczekiwać, że jedna grupa wierszy OPEN na dystrybucję tabeli (60). Nadmierne liczby sugerują ładowanie danych między partycjami. Sprawdź strategię partycjonowania, aby upewnić się, że jest ona brzmiała |
| [OPEN_rowgroup_rows] |Każda grupa wierszy może mieć maksymalnie 1 048 576 wierszy. Użyj tej wartości, aby sprawdzić, jak pełne są obecnie otwarte grupy wierszy |
| [OPEN_rowgroup_rows_MIN] |Otwarte grupy wskazują, że dane są ładowane do tabeli lub poprzednie obciążenie rozlało się na pozostałe wiersze do tej grupy wierszy. Użyj kolumn MIN, MAX i AVG, aby zobaczyć, ile danych znajduje się w grupach wierszy OTWARTYCH. W przypadku małych tabel może to być 100% wszystkich danych. W takim przypadku należy ponownie skompilować indeks alter, aby wymusić dane do magazynu kolumn. |
| [OPEN_rowgroup_rows_MAX] |Tak jak powyżej |
| [OPEN_rowgroup_rows_AVG] |Tak jak powyżej |
| [CLOSED_rowgroup_rows] |Przyjrzyj się wierszom zamkniętej grupy wierszy jako kontroli sanity. |
| [CLOSED_rowgroup_count] |Liczba zamkniętych grup wierszy powinna być niska, jeśli w ogóle są widoczne. Zamknięte grupy wierszy można przekonwertować na skompresowane grupy wierszy przy użyciu funkcji ALTER INDEX... REORGANIZE , polecenie. Nie jest to jednak zwykle wymagane. Zamknięte grupy są automatycznie konwertowane na grupy wierszy magazynu kolumn według procesu "przenoszenia krotki" w tle. |
| [CLOSED_rowgroup_rows_MIN] |Zamknięte grupy wierszy powinny mieć bardzo wysoki współczynnik wypełniania. Jeśli współczynnik wypełnienia zamkniętej grupy wierszy jest niski, wymagana jest dalsza analiza magazynu kolumn. |
| [CLOSED_rowgroup_rows_MAX] |Tak jak powyżej |
| [CLOSED_rowgroup_rows_AVG] |Tak jak powyżej |
| [Rebuild_Index_SQL] |SQL do ponownego kompilowania indeksu magazynu kolumn dla tabeli |

## <a name="impact-of-index-maintenance"></a>Wpływ konserwacji indeksu

Kolumna w `Rebuild_Index_SQL` widoku `vColumnstoreDensity` zawiera instrukcje, których można użyć do `ALTER INDEX REBUILD` ponownego skompilowania indeksów. Podczas ponownego kompilowania indeksów należy przydzielić wystarczającą ilość pamięci do sesji, która ponownie skompilowała indeks. W tym celu zwiększ klasę [zasobów użytkownika,](resource-classes-for-workload-management.md) który ma uprawnienia do ponownego kompilowania indeksu w tej tabeli do zalecanego minimum. Aby uzyskać przykład, zobacz [Rebuilding indexes to improve segment quality (Odbudowywanie indeksów w celu poprawy jakości segmentu)](#rebuilding-indexes-to-improve-segment-quality) w dalszej części tego artykułu.

W przypadku tabeli z uporządkowaną klastrowym indeksem magazynu kolumn program ponownie posortowa dane `ALTER INDEX REBUILD` przy użyciu bazy danych tempdb. Monitor tempdb during rebuild operations (Monitorowanie bazy danych tempdb podczas operacji ponownego kompilowania). Jeśli potrzebujesz więcej miejsca na bazę danych tempdb, przeskaluj pulę baz danych w górę. Skaluj z powrotem w dół po zakończeniu ponownego kompilowania indeksu.

W przypadku tabeli z uporządkowaną klastrowym indeksem magazynu kolumn nie powoduje `ALTER INDEX REORGANIZE` ponownego sortowania danych. Aby ponownie posortować dane, `ALTER INDEX REBUILD` użyj .

Aby uzyskać więcej informacji na temat uporządkowanych klastrowanych indeksów magazynu kolumn, zobacz [Performance tuning with ordered clustered columnstore index](performance-tuning-ordered-cci.md)(Dostrajanie wydajności za pomocą uporządkowanego klastrowanego indeksu magazynu kolumn).

## <a name="causes-of-poor-columnstore-index-quality"></a>Przyczyny niskiej jakości indeksu magazynu kolumn

Jeśli zidentyfikowano tabele o niskiej jakości segmentu, chcesz zidentyfikować główną przyczynę.  Poniżej przedstawiono kilka innych typowych przyczyn niskiej jakości segmentu:

1. Użycie pamięci podczas budowanych indeksów
2. Duża liczba operacji DML
3. Operacje ładowania małych lub zrębowych
4. Zbyt wiele partycji

Te czynniki mogą spowodować, że indeks magazynu kolumn będzie mieć znacznie mniej niż optymalny 1 milion wierszy na grupę wierszy. Mogą one również powodować, że wiersze będą trafiać do grupy wierszy różnicowych zamiast do skompresowanych grup wierszy.

### <a name="memory-pressure-when-index-was-built"></a>Użycie pamięci podczas budowanych indeksów

Liczba wierszy na skompresowaną grupę wierszy jest bezpośrednio powiązana z szerokością wiersza i ilością pamięci dostępnej do przetwarzania grupy wierszy.  Jeśli wiersze są zapisywane w tabelach magazynu kolumn przy dużym wykorzystaniu pamięci, może to spowodować obniżenie jakości segmentów w magazynie kolumn.  W związku z tym najlepszym rozwiązaniem jest nadanie sesji zapisu w tabelach indeksu magazynu kolumn możliwie jak najwięcej pamięci.  Ponieważ istnieje związek między pamięcią i współbieżnością, wskazówki dotyczące właściwej alokacji pamięci zależą od danych w każdym wierszu tabeli, jednostek magazynu danych przydzielonych do systemu oraz liczby gniazd współbieżności, które można przekazać do sesji, która zapisuje dane w tabeli.

### <a name="high-volume-of-dml-operations"></a>Duża liczba operacji DML

Duża liczba operacji DML, które aktualizują i usuwają wiersze, może wprowadzać nieefektywność do magazynu kolumn. Jest to szczególnie istotne, gdy większość wierszy w grupie wierszy jest modyfikowana.

- Usunięcie wiersza ze skompresowanego grupy wierszy tylko logicznie oznacza wiersz jako usunięty. Wiersz pozostaje w skompresowanej grupie wierszy, dopóki partycja lub tabela nie zostanie ponownie skomponowana.
- Wstawienie wiersza powoduje dodanie wiersza do wewnętrznej tabeli magazynu wierszy nazywanej grupą wierszy różnicowych. Wstawiony wiersz nie jest konwertowany na magazyn kolumn, dopóki grupa wierszy różnicowych nie zostanie zapełniona i nie zostanie oznaczona jako zamknięta. Grupy wierszy są zamykane po osiągnięciu maksymalnej pojemności 1 048 576 wierszy.
- Aktualizowanie wiersza w formacie magazynu kolumn jest przetwarzane jako usuwanie logiczne, a następnie wstawianie. Wstawiony wiersz może być przechowywany w magazynie różnicowym.

Operacje aktualizacji wsadowej i wstawiania, które przekraczają próg zbiorczy 102 400 wierszy na dystrybucję wyrównaną do partycji, są bezpośrednio zgodne z formatem magazynu kolumn. Jednak przy założeniu rozkładu równomiernego należy zmodyfikować więcej niż 6,144 miliona wierszy w ramach jednej operacji, aby miało to miejsce. Jeśli liczba wierszy dla danej dystrybucji wyrównanej do partycji jest mniejsza niż 102 400, wiersze trafiają do magazynu różnicowego i pozostają tam do momentu wstawienia lub zmodyfikowania wystarczającej liczby wierszy do zamknięcia grupy wierszy lub przebudowania indeksu.

### <a name="small-or-trickle-load-operations"></a>Operacje ładowania małych lub zrębowych

Małe obciążenia, które przepływają do dedykowanej puli SQL, są również czasami nazywane obciążeniami skomplikowanymi. Zazwyczaj reprezentują one niemal stały strumień danych pozyskanych przez system. Jednak ze względu na to, że ten strumień jest niemal ciągły, liczba wierszy nie jest szczególnie duża. Dane są znacznie poniżej progu wymaganego do bezpośredniego ładowania do formatu magazynu kolumn.

W takich sytuacjach często lepiej jest najpierw trafić dane do usługi Azure Blob Storage i pozwolić na ich gromadzenie przed załadowaniem. Ta technika jest często znana jako *mikrosadowe*.

### <a name="too-many-partitions"></a>Zbyt wiele partycji

Kolejną rzeczą, którą należy wziąć pod uwagę, jest wpływ partycjonowania na klastrowane tabele magazynu kolumn.  Przed partycjonowaniem dedykowana pula SQL już dzieli dane na 60 baz danych.  Partycjonowanie dodatkowo dzieli dane.  W przypadku partycjonowania danych  należy wziąć pod uwagę, że każda partycja potrzebuje co najmniej 1 miliona wierszy, aby skorzystać z klastrowanego indeksu magazynu kolumn.  Jeśli podzielisz tabelę na 100 partycji, tabela potrzebuje co najmniej 6 miliardów wierszy, aby skorzystać z klastrowanego indeksu magazynu kolumn (60 dystrybucji *100* partycji 1 miliona wierszy). Jeśli tabela 100-partycji nie zawiera 6 miliardów wierszy, zmniejsz liczbę partycji lub zamiast tego rozważ użycie tabeli sterty.

Po załadowaniu tabel z niektórymi danymi wykonaj poniższe kroki, aby zidentyfikować i ponownie skompilować tabele z nie optymalnie klastrowanych indeksów magazynu kolumn.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Ponowne kompilowanie indeksów w celu poprawy jakości segmentu

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Krok 1. Identyfikowanie lub tworzenie użytkownika, który używa odpowiedniej klasy zasobów

Jednym z szybkich sposobów natychmiastowego poprawienia jakości segmentu jest ponowne skompilowanie indeksu.  Język SQL zwrócony przez powyższy widok zwraca instrukcję ALTER INDEX REBUILD, której można użyć do ponownego skompilowania indeksów. Podczas odbudowywania indeksów upewnij się, że przydzielisz wystarczającą ilość pamięci do sesji, która ponownie skompilowała indeks. W tym celu należy zwiększyć klasę zasobów użytkownika, który ma uprawnienia do ponownego kompilowania indeksu w tej tabeli do zalecanego minimum.

Poniżej przedstawiono przykład przydzielania użytkownikowi większej ilości pamięci przez zwiększenie klasy zasobów. Aby pracować z klasami zasobów, zobacz [Resource classes for workload management (Klasy zasobów do zarządzania obciążeniami).](resource-classes-for-workload-management.md)

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser';
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Krok 2. Ponowne kompilowanie klastrowanych indeksów magazynu kolumn z użytkownikiem wyższej klasy zasobów

Zaloguj się jako użytkownik z kroku 1 (LoadUser), który teraz używa wyższej klasy zasobów, i wykonaj instrukcje ALTER INDEX. Upewnij się, że ten użytkownik ma uprawnienia ALTER do tabel, w których indeks jest ponownie budowany. Te przykłady pokazują, jak ponownie skompilować cały indeks magazynu kolumn lub jak ponownie skompilować pojedynczą partycję. W przypadku dużych tabel bardziej praktyczne jest ponowne kompilowanie indeksów dla jednej partycji na raz.

Zamiast ponownie kompilować indeks, możesz też skopiować tabelę do nowej tabeli przy [użyciu metody CTAS](sql-data-warehouse-develop-ctas.md). Jaki sposób jest najlepszy? W przypadku dużych ilości danych ctAS jest zazwyczaj szybszy niż [alter INDEX](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). W przypadku mniejszych ilości danych użycie funkcji ALTER INDEX jest łatwiejsze i nie wymaga wymiany tabeli.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5;
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE);
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE);
```

Ponowne kompilowanie indeksu w dedykowanej puli SQL jest operacją w trybie offline.  Aby uzyskać więcej informacji na temat odbudowywania indeksów, zobacz sekcję ALTER INDEX REBUILD w tematach [Defragmentacja](/sql/relational-databases/indexes/columnstore-indexes-defragmentation?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)indeksów magazynu kolumn i [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Krok 3. Sprawdzanie, czy jakość segmentu klastrowanych magazynów kolumn uległa poprawie

Ponownie uruchomić zapytanie, które zidentyfikował tabelę o niskiej jakości segmentu i sprawdzić, czy jakość segmentu uległa poprawie.  Jeśli jakość segmentu nie poprawi się, może to oznaczać, że wiersze w tabeli są bardzo szerokie.  Podczas ponownego kompilowania indeksów rozważ użycie wyższej klasy zasobów lub jednostek DWU.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Ponowne kompilowanie indeksów za pomocą ctaS i przełączania partycji

W tym przykładzie [użyto CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) i przełączania partycji w celu ponownego skompilowania partycji tabeli.

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

Aby uzyskać więcej informacji na temat ponownego tworzenia partycji przy użyciu instrukcji CTAS, zobacz Using partitions in dedicated SQL pool (Używanie [partycji w dedykowanej puli SQL).](sql-data-warehouse-tables-partition.md)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia tabel, zobacz [Tworzenie tabel](sql-data-warehouse-tables-overview.md).
