---
title: Poprawianie wydajności indeksu magazynu kolumn dla dedykowanej puli SQL
description: Zmniejsz wymagania dotyczące pamięci lub zwiększ ilość dostępnej pamięci, aby zmaksymalizować liczbę wierszy w każdej grupie wierszy w dedykowanej puli SQL.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/22/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 1336359bdd0768ba1d1554554d266cacfb483a43
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566515"
---
# <a name="maximizing-rowgroup-quality-for-columnstore-indexes-in-dedicated-sql-pool"></a>Maksymalizowanie jakości grupy wierszy dla indeksów magazynu kolumn w dedykowanej puli SQL 

Jakość grupy wierszy zależy od liczby wierszy w grupie wierszy. Zwiększenie dostępnej pamięci może zmaksymalizować liczbę wierszy skompresowanych przez indeks magazynu kolumn do każdej grupy wierszy.  Użyj tych metod, aby zwiększyć współczynnik kompresji i wydajność zapytań dla indeksów magazynu kolumn.

## <a name="why-the-rowgroup-size-matters"></a>Dlaczego rozmiar grupy wierszy ma znaczenie

Ponieważ indeks magazynu kolumn skanuje tabelę, skanując segmenty kolumn poszczególnych grup wierszy, maksymalizowanie liczby wierszy w każdej grupie wierszy zwiększa wydajność zapytań.

Gdy grupy wierszy mają dużą liczbę wierszy, zwiększa się kompresja danych, co oznacza, że jest mniej danych do odczytania z dysku.

Aby uzyskać więcej informacji na temat grup wierszy, zobacz [Columnstore Indexes Guide (Przewodnik po indeksach magazynu kolumn).](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="target-size-for-rowgroups"></a>Rozmiar docelowy dla grup wierszy

Aby uzyskać najlepszą wydajność zapytań, celem jest zmaksymalizowanie liczby wierszy na grupę wierszy w indeksie magazynu kolumn. Grupa wierszy może mieć maksymalnie 1 048 576 wierszy.

Nie można mieć maksymalnej liczby wierszy na grupę wierszy. Indeksy magazynu kolumn osiągają dobrą wydajność, gdy grupy wierszy mają co najmniej 100 000 wierszy.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Grupy wierszy mogą zostać przycięte podczas kompresji

Podczas ładowania zbiorczego lub ponownego kompilowania indeksu magazynu kolumn czasami nie ma wystarczającej ilości pamięci, aby skompresować wszystkie wiersze wyznaczone dla każdej grupy wierszy. Gdy występuje użycie pamięci, indeksy magazynu kolumn przycinają rozmiary grup wierszy, aby kompresja do magazynu kolumn zakończyła się powodzeniem.

Jeśli za mało pamięci do skompresowania co najmniej 10 000 wierszy do każdej grupy wierszy, zostanie wygenerowany błąd.

Aby uzyskać więcej informacji na temat ładowania zbiorczego, zobacz [Ładowanie zbiorcze do klastrowanego indeksu magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="how-to-monitor-rowgroup-quality"></a>Jak monitorować jakość grupy wierszy

Widok DMV sys.dm_pdw_nodes_db_column_store_row_group_physical_stats ([sys.dm_db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) zawiera definicję widoku pasującą do bazy danych SQL), która uwidacznia przydatne informacje, takie jak liczba wierszy w grupach wierszy i przyczyna przycinania, jeśli było przycinanie.

Poniższy widok można utworzyć jako wygodny sposób wykonywania zapytań dotyczących tego widoku DMV w celu uzyskania informacji na temat przycinania grupy wierszy.

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

Ten trim_reason_desc, czy grupa wierszy została przycinana(trim_reason_desc = NO_TRIM oznacza, że nie było przycinania, a grupa wierszy jest o optymalnej jakości). Następujące przyczyny przycinania wskazują na przedwczesne przycinanie grupy wierszy:

- BULKLOAD: ta przyczyna przycinania jest używana, gdy przychodząca partia wierszy dla obciążenia miała mniej niż 1 milion wierszy. Aparat utworzy skompresowane grupy wierszy, jeśli liczba wstawionych wierszy jest większa niż 100 000 (zamiast wstawiania do magazynu różnicowego), ale ustawia przyczynę przycinania na BULKLOAD. W tym scenariuszu rozważ zwiększenie obciążenia partii w celu uwzględnienia większej liczby wierszy. Ponadto ponownie przeceń schemat partycjonowania, aby upewnić się, że nie jest on zbyt szczegółowy, ponieważ grupy wierszy nie mogą obejmować granic partycji.
- MEMORY_LIMITATION: aby utworzyć grupy wierszy z 1 milionem wierszy, aparat wymaga pewnej ilości pamięci roboczej. Jeśli dostępna pamięć sesji ładowania jest mniejsza niż wymagana pamięć robocza, grupy wierszy są przedwcześnie przycinane. W poniższych sekcjach opisano sposób szacowania wymaganej pamięci i przydzielenia większej ilości pamięci.
- DICTIONARY_SIZE: Ta przyczyna przycinania wskazuje, że nastąpiło przycinanie grupy wierszy, ponieważ istnieje co najmniej jedna kolumna ciągów z ciągami szerokich i/lub o wysokiej kardynalności. Rozmiar słownika jest ograniczony do 16 MB w pamięci, a po osiągnięciu tego limitu grupa wierszy jest kompresowana. W takiej sytuacji rozważ odizolowanie problematycznej kolumny do oddzielnej tabeli.

## <a name="how-to-estimate-memory-requirements"></a>Jak oszacować wymagania dotyczące pamięci

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

Maksymalna ilość pamięci wymaganej do skompresowania jednej grupy wierszy wynosi około

- 72 MB +
- \#wiersze \* \# \* kolumny 8 bajtów +
- \#wiersze \* \# z krótkimi kolumnami-ciągami \* 32 bajty +
- \#długie kolumny ciągów \* 16 MB dla słownika kompresji

> [!NOTE]
> Kolumny z krótkimi ciągami używają ciągowych typów danych <= 32 bajtów, a kolumny ciągów używają ciągowych typów danych o rozmiarze > 32 bajtów.

Długie ciągi są kompresowane przy użyciu metody kompresji przeznaczonej do kompresowania tekstu. Ta metoda kompresji używa *słownika do* przechowywania wzorców tekstu. Maksymalny rozmiar słownika to 16 MB. Dla każdej długiej kolumny ciągu w grupie wierszy istnieje tylko jeden słownik.

Aby uzyskać szczegółowe omówienie wymagań dotyczących pamięci magazynu kolumn, zobacz wideo [Dedicated SQL pool scaling: configuration and guidance](https://channel9.msdn.com/Events/Ignite/2016/BRK3291)(Dedykowane skalowanie puli SQL: konfiguracja i wskazówki).

## <a name="ways-to-reduce-memory-requirements"></a>Sposoby zmniejszenia wymagań dotyczących pamięci

Użyj następujących technik, aby zmniejszyć wymagania dotyczące pamięci w przypadku kompresowania grup wierszy do indeksów magazynu kolumn.

### <a name="use-fewer-columns"></a>Używanie mniejszej liczby kolumn

Jeśli to możliwe, zaprojektuj tabelę z mniejszą liczbą kolumn. Gdy grupa wierszy jest skompresowana do magazynu kolumn, indeks magazynu kolumn kompresuje każdy segment kolumn oddzielnie.

W związku z tym wymagania dotyczące pamięci w celu skompresowania grupy wierszy zwiększają się wraz ze wzrostem liczby kolumn.

### <a name="use-fewer-string-columns"></a>Użyj mniejszej liczby kolumn ciągów

Kolumny typów danych ciągów wymagają większej ilości pamięci niż typy danych liczbowych i dat. Aby zmniejszyć wymagania dotyczące pamięci, rozważ usunięcie kolumn ciągów z tabel faktów i umieszczenie ich w mniejszych tabelach wymiarów.

Dodatkowe wymagania dotyczące pamięci na potrzeby kompresji ciągów:

- Ciągowe typy danych do 32 znaków mogą wymagać 32 dodatkowych bajtów na wartość.
- Ciągowe typy danych z więcej niż 32 znakami są kompresowane przy użyciu metod słownika.  Do skompilowania słownika każda kolumna w grupie wierszy może wymagać do 16 MB dodatkowych danych.

### <a name="avoid-over-partitioning"></a>Unikaj zbytniego partycjonowania

Indeksy magazynu kolumn tworzą co najmniej jedną grupę wierszy na partycję. W przypadku dedykowanej puli SQL Azure Synapse Analytics liczba partycji szybko rośnie, ponieważ dane są dystrybuowane, a każda dystrybucja jest partycjonowana.

Jeśli tabela zawiera zbyt wiele partycji, może być za mało wierszy do wypełnienia grup wierszy. Brak wierszy nie powoduje użycia pamięci podczas kompresji. Jednak prowadzi to do grup wierszy, które nie osiągają najlepszej wydajności zapytań magazynu kolumn.

Innym powodem, aby uniknąć zbyt wielu partycjonowania, jest obciążenie pamięci związane z ładowaniem wierszy do indeksu magazynu kolumn w tabeli podzielonej na partycje.

Podczas ładowania wiele partycji może odbierać przychodzące wiersze, które są przechowywane w pamięci, dopóki każda partycja nie będzie mieć wystarczającej liczby wierszy do skompresowania. Zbyt duża ilość partycji powoduje dodatkowe użycie pamięci.

### <a name="simplify-the-load-query"></a>Upraszczanie zapytania ładowania

Baza danych udostępnia przyznawanie pamięci dla zapytania wszystkim operatorom w zapytaniu. Gdy zapytanie obciążenia ma złożone sortowania i sprzężenia, ilość pamięci dostępnej do kompresji jest mniejsza.

Zaprojektuj zapytanie obciążeniowe, aby skoncentrować się tylko na ładowaniu zapytania. Jeśli musisz uruchomić przekształcenia danych, uruchom je oddzielnie od zapytania ładowania. Na przykład przetowalij dane w tabeli sterty, uruchom przekształcenia, a następnie załaduj tabelę przemieszczania do indeksu magazynu kolumn.

> [!TIP]
> Możesz również najpierw załadować dane, a następnie przekształcić je za pomocą systemu MPP.

### <a name="adjust-maxdop"></a>Dostosowywanie ustawienia MAXDOP

Każda dystrybucja kompresuje grupy wierszy do magazynu kolumn równolegle, gdy dostępnych jest więcej niż jeden rdzeń procesora CPU na dystrybucję.

Równoległość wymaga dodatkowych zasobów pamięci, co może prowadzić do użycia pamięci i przycinania grupy wierszy.

Aby zmniejszyć wykorzystanie pamięci, można użyć wskazówki zapytania MAXDOP, aby wymusić uruchomienie operacji ładowania w trybie szeregowym w każdej dystrybucji.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Sposoby przydzielania większej ilości pamięci

Rozmiar jednostek DWU i klasa zasobów użytkownika razem określają ilość pamięci dostępnej dla zapytania użytkownika.

Aby zwiększyć przydział pamięci dla zapytania obciążeniowego, można zwiększyć liczbę liczby liczby pamięci RAM lub zwiększyć klasę zasobów.

- Aby zwiększyć liczbę 2200, [zobacz Jak mogę wydajność skalowania?](quickstart-scale-compute-portal.md)
- Aby zmienić klasę zasobów dla zapytania, zobacz [Przykład zmiany klasy zasobów użytkownika](resource-classes-for-workload-management.md#change-a-users-resource-class).

## <a name="next-steps"></a>Następne kroki

Aby znaleźć więcej sposobów poprawy wydajności dedykowanej puli SQL, zobacz [Performance overview (Omówienie wydajności).](cheat-sheet.md)
