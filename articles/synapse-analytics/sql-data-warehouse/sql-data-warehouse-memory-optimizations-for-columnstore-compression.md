---
title: Poprawa wydajności indeksu magazynu kolumn dla dedykowanej puli SQL
description: Zmniejsz wymagania dotyczące pamięci lub Zwiększ ilość dostępnej pamięci, aby zmaksymalizować liczbę wierszy w ramach poszczególnych grupy wierszy w dedykowanej puli SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 6984ad41c07f7790a746dbd197c18dce2aa83e2f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453724"
---
# <a name="maximizing-rowgroup-quality-for-columnstore-indexes-in-dedicated-sql-pool"></a>Maksymalizowanie jakości grupy wierszy dla indeksów magazynu kolumn w dedykowanej puli SQL 

Jakość grupy wierszy jest określana na podstawie liczby wierszy w grupy wierszy. Zwiększenie dostępnej pamięci może zmaksymalizować liczbę wierszy, które są kompresowane przez indeks magazynu kolumn do poszczególnych grupy wierszy.  Użyj tych metod, aby zwiększyć szybkość kompresji i wydajność zapytań dla indeksów magazynu kolumn.

## <a name="why-the-rowgroup-size-matters"></a>Dlaczego grupy wierszy wielkość

Ponieważ indeks magazynu kolumn skanuje tabelę przez skanowanie segmentów kolumn poszczególnych RowGroups, maksymalizacja liczby wierszy w każdej grupy wierszy zwiększa wydajność zapytań.

Gdy RowGroups ma dużą liczbę wierszy, kompresja danych zwiększa się, co oznacza, że do odczytu z dysku są mniejsze dane.

Aby uzyskać więcej informacji na temat RowGroups, zobacz [Przewodnik po indeksach magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="target-size-for-rowgroups"></a>Rozmiar docelowy dla RowGroups

W celu uzyskania najlepszej wydajności zapytań celem jest maksymalizacja liczby wierszy na grupy wierszy w indeksie magazynu kolumn. Grupy wierszy może zawierać maksymalnie 1 048 576 wierszy.

Nie istnieje maksymalna liczba wierszy na grupy wierszy. Indeksy magazynu kolumn osiągają dobrą wydajność, gdy RowGroups mają co najmniej 100 000 wierszy.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>RowGroups może zostać przycięty podczas kompresji

Podczas ponownej kompilacji lub ponownego kompilowania indeksu magazynu kolumn czasami brak wystarczającej ilości pamięci do skompresowania wszystkich wierszy wyznaczono dla każdego grupy wierszyu. Gdy jest obecne wykorzystanie pamięci, w indeksach magazynu kolumn są przycinane rozmiary grupy wierszy, dzięki czemu kompresja do magazynu kolumn może się powieść.

Gdy jest za mało pamięci, aby skompresować co najmniej 10 000 wierszy w każdym grupy wierszy, zostanie wygenerowany błąd.

Aby uzyskać więcej informacji na temat ładowania zbiorczego, zobacz [ładowanie zbiorcze do klastrowanego indeksu magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="how-to-monitor-rowgroup-quality"></a>Jak monitorować jakość grupy wierszy

DMV sys.dm_pdw_nodes_db_column_store_row_group_physical_stats ([sys.dm_db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) zawiera definicję widoku zgodną z bazą danych SQL), która udostępnia przydatne informacje, takie jak liczba wierszy w rowgroups i powód przycinania, jeśli zostały przycinania.

Aby uzyskać informacje na temat przycinania grupy wierszy, można utworzyć następujący widok jako wygodny sposób wykonywania zapytania dotyczącego tego DMV.

```sql
create view dbo.vCS_rg_physical_stats
as
with cte
as
(
select   tb.[name]                    AS [logical_table_name]
,        rg.[row_group_id]            AS [row_group_id]
,        rg.[state]                   AS [state]
,        rg.[state_desc]              AS [state_desc]
,        rg.[total_rows]              AS [total_rows]
,        rg.[trim_reason_desc]        AS trim_reason_desc
,        mp.[physical_name]           AS physical_name
FROM    sys.[schemas] sm
JOIN    sys.[tables] tb               ON  sm.[schema_id]          = tb.[schema_id]
JOIN    sys.[pdw_table_mappings] mp   ON  tb.[object_id]          = mp.[object_id]
JOIN    sys.[pdw_nodes_tables] nt     ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[dm_pdw_nodes_db_column_store_row_group_physical_stats] rg      ON  rg.[object_id]     = nt.[object_id]
                                                                            AND rg.[pdw_node_id]   = nt.[pdw_node_id]
                                        AND rg.[distribution_id]    = nt.[distribution_id]
)
select *
from cte;
```

Trim_reason_desc informuje o tym, czy grupy wierszy został przycięty (trim_reason_desc = NO_TRIM oznacza, że nie ma żadnej przycinania, a grupa wierszy jest optymalną jakością). Następujące przyczyny przycinania wskazują przedwcześnie przycinanie grupy wierszy:

- BULKLOAD: Ten powód przycinania jest używany, gdy przychodząca partia wierszy dla ładowania była mniejsza niż 1 000 000 wierszy. Aparat utworzy skompresowane grupy wierszy, jeśli są wstawiane ponad 100 000 wierszy (w przeciwieństwie do wstawiania do magazynu różnicowego), ale ustawia przyczynę przycięcia do BULKLOAD. W tym scenariuszu należy rozważyć zwiększenie obciążenia partii, aby uwzględnić więcej wierszy. Ponadto należy ponownie oszacować schemat partycjonowania, aby upewnić się, że nie jest on zbyt szczegółowy, ponieważ grupy wierszy nie mogą obejmować granic partycji.
- MEMORY_LIMITATION: Aby utworzyć grupy wierszy z 1 000 000 wierszy, aparat musi mieć określoną ilość pamięci roboczej. Gdy dostępna pamięć sesji ładowania jest mniejsza niż wymagana pamięć robocza, grupy wierszy zostaną przedwcześnie przycięte. W poniższych sekcjach wyjaśniono, jak oszacować wymaganą pamięć i przydzielić więcej pamięci.
- DICTIONARY_SIZE: Ten powód przycinania wskazuje, że przycinanie grupy wierszy wystąpiło z powodu co najmniej jednej kolumny typu String o szerokim i/lub wysokim ciągu kardynalności. Rozmiar słownika jest ograniczony do 16 MB w pamięci, a po osiągnięciu tego limitu zostanie skompresowana grupa wierszy. Jeśli uruchamiasz w tej sytuacji, rozważ odizolowanie problematycznej kolumny do oddzielnej tabeli.

## <a name="how-to-estimate-memory-requirements"></a>Jak oszacować wymagania dotyczące pamięci

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

Maksymalna wymagana ilość pamięci do skompresowania jednego grupy wierszy jest około

- 72 MB +
- \#wiersze \* \# kolumn \* 8 bajtów +
- \#wiersze \* \# krótkie-String — kolumny \* 32 bajtów +
- \#długi ciąg — kolumny \* 16 MB dla słownika kompresji

> [!NOTE]
> Kolumny krótkie-String używają typów danych typu String o wartościach <= 32 bajtów i kolumn długich-String, które używają typów danych String > 32 bajtów.

Długie ciągi są kompresowane przy użyciu metody kompresji zaprojektowanej do kompresowania tekstu. Ta metoda kompresji używa *słownika* do przechowywania wzorców tekstu. Maksymalny rozmiar słownika wynosi 16 MB. Istnieje tylko jeden słownik dla każdej długiej kolumny ciągu w grupy wierszy.

Szczegółowe omówienie wymagań dotyczących pamięci magazynu kolumn można znaleźć w temacie [skalowanie dedykowanej puli SQL w postaci wideo: Konfiguracja i wskazówki](https://channel9.msdn.com/Events/Ignite/2016/BRK3291).

## <a name="ways-to-reduce-memory-requirements"></a>Sposoby zmniejszenia wymagań dotyczących pamięci

Użyj następujących technik, aby zmniejszyć wymagania dotyczące pamięci na potrzeby kompresowania RowGroups w indeksach magazynu kolumn.

### <a name="use-fewer-columns"></a>Użyj mniejszej liczby kolumn

Jeśli to możliwe, Zaprojektuj tabelę z mniejszą liczbą kolumn. Gdy grupy wierszy jest skompresowany do magazynu kolumn, indeks magazynu kolumn kompresuje poszczególne segmenty kolumn osobno.

W związku z tym wymagania dotyczące pamięci do kompresowania grupy wierszy zwiększają się wraz ze wzrostem liczby kolumn.

### <a name="use-fewer-string-columns"></a>Użyj mniej kolumn ciągów

Kolumny typów danych ciągu wymagają większej ilości pamięci niż typy danych liczbowych i dat. Aby zmniejszyć wymagania dotyczące pamięci, należy rozważyć usunięcie kolumn ciągów z tabel faktów i umieszczenie ich w mniejszych tabelach wymiarów.

Dodatkowe wymagania dotyczące pamięci dla kompresji ciągów:

- Typy danych ciągu o długości do 32 znaków mogą wymagać 32 dodatkowych bajtów na wartość.
- Typy danych ciągu z więcej niż 32 znaków są kompresowane przy użyciu metod słownika.  Każda kolumna w grupy wierszy może wymagać do dodatkowego 16 MB do skompilowania słownika.

### <a name="avoid-over-partitioning"></a>Unikaj nadmiernego partycjonowania

Indeksy magazynu kolumn tworzą co najmniej jedną RowGroups na partycję. W przypadku dedykowanej puli SQL w usłudze Azure Synapse Analytics liczba partycji rośnie szybko, ponieważ dane są dystrybuowane i każda z nich jest podzielona na partycje.

Jeśli tabela zawiera zbyt wiele partycji, może być za mało wierszy, aby wypełnić RowGroups. Brak wierszy nie powoduje utworzenia ciśnienia pamięci podczas kompresji. Jednak prowadzi do RowGroups, które nie osiągną najlepszej wydajności zapytania magazynu kolumn.

Innym powodem, aby uniknąć nadmiernego partycjonowania, jest obciążenie pamięci na potrzeby ładowania wierszy do indeksu magazynu kolumn na partycjonowanej tabeli.

Podczas ładowania wiele partycji może odbierać przychodzące wiersze, które są przechowywane w pamięci, dopóki każda partycja ma wystarczającą ilość wierszy do skompresowania. Zbyt wiele partycji tworzy dodatkowe wykorzystanie pamięci.

### <a name="simplify-the-load-query"></a>Uprość kwerendę ładowania

Baza danych udostępnia przydzielenie pamięci dla zapytania między wszystkimi operatorami w zapytaniu. Gdy zapytanie ładowania ma złożone sortowania i sprzężeń, pamięć dostępna do kompresji jest zmniejszana.

Zaprojektuj zapytanie obciążeniowe, aby skoncentrować się tylko na ładowaniu zapytania. Jeśli musisz uruchomić przekształcenia danych, uruchom je niezależnie od zapytania ładowania. Na przykład możesz przygotować dane w tabeli sterty, uruchomić przekształcenia, a następnie załadować tabelę przejściową do indeksu magazynu kolumn.

> [!TIP]
> Możesz również załadować dane w pierwszej kolejności, a następnie przekształcić dane przy użyciu systemu MPP.

### <a name="adjust-maxdop"></a>Dostosuj MAXDOP

Każda dystrybucja kompresuje RowGroups do magazynu kolumn równolegle, gdy istnieje więcej niż jeden rdzeń procesora CPU na dystrybucję.

Równoległość wymaga dodatkowych zasobów pamięci, co może prowadzić do wykorzystania pamięci i przycinania grupy wierszy.

Aby zmniejszyć wykorzystanie pamięci, można użyć wskazówki zapytania MAXDOP, aby wymusić uruchomienie operacji ładowania w trybie szeregowym w ramach każdej dystrybucji.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Sposoby przydzielenia większej ilości pamięci

Rozmiar jednostek dwu i Klasa zasobów użytkownika wspólnie określają ilość pamięci dostępną dla kwerendy użytkownika.

Aby zwiększyć przydział pamięci dla zapytania ładowania, można zwiększyć liczbę jednostek dwu lub zwiększyć klasę zasobów.

- Aby zwiększyć jednostek dwu, zobacz [Jak mogę skalowanie wydajności?](quickstart-scale-compute-portal.md)
- Aby zmienić klasę zasobów dla zapytania, zobacz [Zmienianie przykładu klasy zasobów użytkownika](resource-classes-for-workload-management.md#change-a-users-resource-class).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o sposobach poprawy wydajności dla dedykowanej puli SQL, zobacz [Omówienie wydajności](cheat-sheet.md).
