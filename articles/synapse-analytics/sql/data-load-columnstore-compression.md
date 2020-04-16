---
title: Poprawa wydajności indeksu magazynu kolumn
description: Zmniejsz wymagania dotyczące pamięci lub zwiększ dostępną pamięć, aby zmaksymalizować liczbę wierszy, które indeks magazynu kolumn kompresuje do każdej grupy wierszy.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: f1f3667c088c5f7300317ea02ca19a72e4e62905
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431035"
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>Maksymalizacja jakości grupy wierszy dla magazynu kolumn

Jakość grupy wierszy zależy od liczby wierszy w grupie wierszy. Zwiększenie dostępnej pamięci można zmaksymalizować liczbę wierszy indeksu magazynu kolumn kompresuje do każdej grupy wierszy.  Użyj tych metod, aby poprawić szybkość kompresji i wydajność zapytań dla indeksów magazynu kolumn.

## <a name="why-the-rowgroup-size-matters"></a>Dlaczego rozmiar grupy wierszy ma znaczenie

Ponieważ indeks magazynu kolumn skanuje tabelę, skanując segmenty kolumn poszczególnych grup wierszy, maksymalizacja liczby wierszy w każdej grupie wierszy zwiększa wydajność kwerendy. Gdy grupy wierszy mają dużą liczbę wierszy, kompresja danych poprawia się, co oznacza, że jest mniej danych do odczytu z dysku.

Aby uzyskać więcej informacji na temat grup wierszy, zobacz [Przewodnik indeksów magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="target-size-for-rowgroups"></a>Rozmiar docelowy dla grup wierszy

Aby uzyskać najlepszą wydajność kwerendy, celem jest zmaksymalizowanie liczby wierszy na grupę wierszy w indeksie magazynu kolumn. Grupa wierszy może mieć maksymalnie 1 048 576 wierszy. Nie ma maksymalnej liczby wierszy na grupę wierszy. Indeksy magazynu kolumn osiągają dobrą wydajność, gdy grupy wierszy mają co najmniej 100 000 wierszy.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Grupy wierszy mogą zostać przycięte podczas kompresji

Podczas zbiorczego ładowania lub przebudowy indeksu magazynu kolumn czasami nie ma wystarczającej ilości pamięci, aby skompresować wszystkie wiersze wyznaczone dla każdej grupy wierszy. Gdy występuje ciśnienie pamięci, indeksy magazynu kolumn przyciąć rozmiary grupy wierszy, więc kompresji do magazynu kolumn może zakończyć się pomyślnie.

Jeśli nie ma wystarczającej ilości pamięci, aby skompresować co najmniej 10 000 wierszy do każdej grupy wierszy, zostanie wygenerowany błąd.

Aby uzyskać więcej informacji na temat ładowania [zbiorczego, zobacz Ładowanie zbiorcze do indeksu klastrowanego magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#Bulk ).

## <a name="how-to-monitor-rowgroup-quality"></a>Jak monitorować jakość grupy wierszy

DmV sys.dm_pdw_nodes_db_column_store_row_group_physical_stats ([sys.dm_db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) zawiera definicję widoku pasujące SQL DB), który udostępnia przydatne informacje, takie jak liczba wierszy w grupach wierszy i powodem przycinania, jeśli nie było przycinanie. Można utworzyć następujący widok jako przydatny sposób na zapytanie tego DMV, aby uzyskać informacje na temat przycinania grup wierszy.

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

trim_reason_desc informuje, czy grupa wierszy została przycięta(trim_reason_desc = NO_TRIM oznacza, że nie było przycinania, a grupa wierszy jest optymalnej jakości). Następujące przyczyny przycinania wskazują na przedwczesne przycinanie grupy wierszy:

- BULKLOAD: Ta przyczyna przycinania jest używana, gdy przychodząca partia wierszy dla obciążenia miała mniej niż 1 milion wierszy. Aparat utworzy skompresowane grupy wierszy, jeśli są wstawiane więcej niż 100 000 wierszy (w przeciwieństwie do wstawiania do magazynu delta), ale ustawia przyczynę przycinania na BULKLOAD. W tym scenariuszu należy rozważyć zwiększenie obciążenia wsadowego, aby uwzględnić więcej wierszy. Ponadto ponownie ocenić schemat partycjonowania, aby upewnić się, że nie jest zbyt szczegółowe, jak grupy wierszy nie może obejmować granice partycji.
- MEMORY_LIMITATION: Aby utworzyć grupy wierszy z 1 milionem wierszy, aparat wymaga określonej ilości pamięci roboczej. Gdy dostępna pamięć sesji ładowania jest mniejsza niż wymagana pamięć robocza, grupy wierszy są przedwcześnie przycinane. W poniższych sekcjach wyjaśniono, jak oszacować wymaganą pamięć i przydzielić więcej pamięci.
- DICTIONARY_SIZE: Ten powód przycinania wskazuje, że wystąpiło przycinanie grup wierszy, ponieważ istniała co najmniej jedna kolumna ciągu z ciągami szerokiej i/lub wysokiej kardynalności. Rozmiar słownika jest ograniczony do 16 MB w pamięci i po osiągnięciu tego limitu grupa wierszy jest skompresowana. Jeśli napotkasz tę sytuację, należy rozważyć izolowanie problematycznej kolumny do osobnej tabeli.

## <a name="how-to-estimate-memory-requirements"></a>Jak oszacować wymagania dotyczące pamięci

Maksymalna wymagana pamięć do skompresu jednej grupy wierszy wynosi około

- 72 MB +
- \#kolumny \* \#wierszy \* 8 bajtów +
- \#wiersze \* \#kolumny krótkie \* 32 bajtów +
- \#długie kolumny ciągów \* 16 MB do kompresji słownika

gdzie kolumny ciągów krótkich używają typów danych ciągów <= 32 bajtów i długich kolumn ciągów używają typów danych ciągów > 32 bajtów.

Długie ciągi są kompresowane za pomocą metody kompresji przeznaczonej do kompresji tekstu. Ta metoda kompresji używa *słownika* do przechowywania wzorców tekstu. Maksymalny rozmiar słownika to 16 MB. Istnieje tylko jeden słownik dla każdej długiej kolumny ciągu w grupie wierszy.

Aby zapoznać się z dogłębną omówieniem wymagań dotyczących pamięci magazynu kolumn, zobacz klip wideo [Skalowanie SQL Synapse: konfiguracja i wskazówki](https://channel9.msdn.com/Events/Ignite/2016/BRK3291).

## <a name="ways-to-reduce-memory-requirements"></a>Sposoby zmniejszania wymagań dotyczących pamięci

Poniższe techniki można zmniejszyć wymagania dotyczące pamięci dotyczące kompresji grup wierszy do indeksów magazynu kolumn.

### <a name="use-fewer-columns"></a>Użyj mniejszej liczby kolumn

Jeśli to możliwe, zaprojektuj tabelę z mniejszą liczbą kolumn. Gdy grupa wierszy jest skompresowana do magazynu kolumn, indeks magazynu kolumn kompresuje każdy segment kolumny oddzielnie. W związku z tym wymagania dotyczące pamięci do kompresji rowgroup zwiększyć wraz ze wzrostem liczby kolumn.

### <a name="use-fewer-string-columns"></a>Użyj mniejszej liczby kolumn ciągów

Kolumny typów danych ciągów wymagają więcej pamięci niż typy danych liczbowych i dat. Aby zmniejszyć wymagania dotyczące pamięci, należy rozważyć usunięcie kolumn ciągów z tabel faktów i umieszczenie ich w mniejszych tabelach wymiarów.

Dodatkowe wymagania dotyczące pamięci dla kompresji ciągów:

- Typy danych ciągów do 32 znaków może wymagać 32 dodatkowych bajtów na wartość.
- Typy danych ciągów z więcej niż 32 znakami są kompresowane przy użyciu metod słownika.  Każda kolumna w grupie wierszy może wymagać maksymalnie 16 MB dodatkowych do utworzenia słownika.

### <a name="avoid-over-partitioning"></a>Unikaj nadmiernej partycjonowania

Indeksy magazynu kolumn tworzą co najmniej jedną grupę wierszy na partycję. W przypadku hurtowni danych w usłudze Azure Synapse Analytics liczba partycji szybko rośnie, ponieważ dane są dystrybuowane, a każda dystrybucja jest podzielona na partycje. Jeśli tabela ma zbyt wiele partycji, może nie być wystarczającej liczby wierszy, aby wypełnić rowgroups. Brak wierszy nie tworzy ciśnienia pamięci podczas kompresji, ale prowadzi do rowgroups, które nie osiągają najlepszą wydajność kwerendy magazynu kolumn.

Innym powodem, aby uniknąć nadmiernej partycjonowania jest istnieje obciążenie pamięci do ładowania wierszy do indeksu magazynu kolumn w tabeli podzielonej na partycje. Podczas ładowania wiele partycji może odbierać przychodzące wiersze, które są przechowywane w pamięci, dopóki każda partycja ma wystarczająco dużo wierszy do skompresowania. Posiadanie zbyt wielu partycji tworzy dodatkowe ciśnienie pamięci.

### <a name="simplify-the-load-query"></a>Uproszczenie kwerendy wczytywowej

Baza danych udostępnia grant pamięci dla kwerendy między wszystkimi operatorami w kwerendzie. Gdy kwerenda obciążenia ma złożone sortuje i sprzężenia, pamięć dostępna do kompresji jest zmniejszona.

Zaprojektuj kwerendę ładowania, aby skupić się tylko na załadowaniu kwerendy. Jeśli chcesz uruchomić przekształcenia na danych, uruchom je oddzielnie od kwerendy obciążenia. Na przykład etap danych w tabeli sterty, uruchom przekształcenia, a następnie załadować tabelę przemieszczania do indeksu magazynu kolumn. 

### <a name="adjust-maxdop"></a>Dopasowywanie MAXDOP

Każda dystrybucja kompresuje rowgroups do magazynu kolumn równolegle, gdy istnieje więcej niż jeden rdzeń procesora CPU dostępne dla dystrybucji. Równoległość wymaga dodatkowych zasobów pamięci, co może prowadzić do ciśnienia pamięci i przycinania grupy wierszy.

Aby zmniejszyć ciśnienie w pamięci, można użyć wskazówki kwerendy MAXDOP, aby wymusić operację ładowania do uruchomienia w trybie szeregowym w ramach każdej dystrybucji.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Sposoby przydzielania większej ilości pamięci

Dwu rozmiar i klasa zasobów użytkownika razem określić, ile pamięci jest dostępna dla kwerendy użytkownika. Aby zwiększyć przyznanie pamięci dla kwerendy obciążenia, można zwiększyć liczbę jednostek DU lub zwiększyć klasę zasobów.

- Aby zwiększyć jednostu DWU, zobacz [Jak skalować wydajność?](../sql-data-warehouse/quickstart-scale-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- Aby zmienić klasę zasobów kwerendy, zobacz [Zmienianie przykładu klasy zasobów użytkownika](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#change-a-users-resource-class).

## <a name="next-steps"></a>Następne kroki

Aby znaleźć więcej sposobów na zwiększenie wydajności w języku Synapse SQL, zobacz [omówienie wydajności](../overview-cheat-sheet.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

 
