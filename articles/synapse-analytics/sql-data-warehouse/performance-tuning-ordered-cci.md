---
title: Strojenie wydajności za pomocą uporządkowanego klastrowanego indeksu magazynu kolumn
description: Zalecenia i zagadnienia, które należy znać podczas używania uporządkowanego klastrowanego indeksu magazynu kolumn w celu zwiększenia wydajności zapytań w dedykowanych pulach SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/13/2021
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3245f560d9a5afb1f9cf8824eeaa3bc681706794
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389676"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Strojenie wydajności za pomocą uporządkowanego klastrowanego indeksu magazynu kolumn  

Gdy użytkownicy odpytuje tabelę magazynu kolumn w dedykowanej puli SQL, optymalizator sprawdza minimalne i maksymalne wartości przechowywane w każdym segmencie.  Segmenty spoza granic predykatu zapytania nie są odczytywane z dysku do pamięci.  Zapytanie może uzyskać większą wydajność, jeśli liczba segmentów do odczytania i ich całkowity rozmiar są małe.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Uporządkowany a nieu ordered clustered columnstore index

Domyślnie dla każdej tabeli utworzonej bez opcji indeksu składnik wewnętrzny (konstruktor indeksu) tworzy na nim nieu uporządkowany indeks klastrowanego magazynu kolumn (CCI).  Dane w każdej kolumnie są kompresowane do oddzielnego segmentu grupy wierszy CCI.  Zakres wartości każdego segmentu zawiera metadane, więc segmenty spoza granic predykatu zapytania nie są odczytywane z dysku podczas wykonywania zapytania.  CcI oferuje najwyższy poziom kompresji danych i zmniejsza rozmiar segmentów do odczytu, dzięki czemu zapytania mogą działać szybciej. Jednak ponieważ konstruktor indeksu nie sortuje danych przed skompresowaniem ich do segmentów, mogą wystąpić segmenty z nakładającymi się zakresami wartości, co powoduje, że zapytania odczytują więcej segmentów z dysku i trwa dłużej.  

Podczas tworzenia uporządkowanego pliku CCI dedykowany aparat puli SQL sortuje istniejące dane w pamięci według kluczy kolejności, zanim konstruktor indeksu skompresuje je w segmenty indeksu.  W przypadku posortowanych danych nakładanie się segmentów jest reduowane, dzięki czemu zapytania mają bardziej wydajną eliminację segmentów, a tym samym większą wydajność, ponieważ liczba segmentów do odczytania z dysku jest mniejsza.  Jeśli wszystkie dane można sortować w pamięci jednocześnie, można uniknąć nakładania się segmentów.  Ze względu na duże tabele w magazynach danych ten scenariusz nie występuje często.  

Aby sprawdzić zakresy segmentów dla kolumny, uruchom następujące polecenie z nazwą tabeli i nazwą kolumny:

```sql
SELECT o.name, pnp.index_id, 
cls.row_count, pnp.data_compression_desc, 
pnp.pdw_node_id, pnp.distribution_id, cls.segment_id, 
cls.column_id, 
cls.min_data_id, cls.max_data_id, 
cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table Name>' and cols.name = '<Column Name>'  and TMap.physical_name  not like '%HdTable%'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id 


```

> [!NOTE] 
> W uporządkowanej tabeli CCI nowe dane wynikające z tej samej partii operacji DML lub ładowania danych są sortowane w ramach tej partii, nie ma globalnego sortowania wszystkich danych w tabeli.  Użytkownicy mogą ponownie skompilować uporządkowany indeks CCI, aby posortować wszystkie dane w tabeli.  W dedykowanej puli SQL indeks magazynu kolumn REBUILD jest operacją w trybie offline.  W przypadku tabeli partycjonowanych kompilowanie odbywa się po jednej partycji na raz.  Dane w partycji, która jest kompilowana jest "offline" i niedostępne do czasu ukończenia ponownej kompilowania dla tej partycji. 

## <a name="query-performance"></a>Wydajność zapytań

Wzrost wydajności zapytania na podstawie uporządkowanej liczby jednostek CCI zależy od wzorców zapytań, rozmiaru danych, jakości sortowania danych, fizycznej struktury segmentów oraz jednostek DWU i klasy zasobów wybranych do wykonania zapytania.  Użytkownicy powinni przejrzeć wszystkie te czynniki przed wybraniem kolumn zamawiania podczas projektowania uporządkowanej tabeli CCI.

Zapytania ze wszystkimi tymi wzorcami zwykle działają szybciej dzięki uporządkowanej cci.  
1. Zapytania mają predykaty równości, nierówności lub zakresu
1. Kolumny predykatu i uporządkowane kolumny CCI są takie same.  
1. Kolumny predykatu są używane w tej samej kolejności co liczba porządkowa kolumn uporządkowanej liczby cci.  
 
W tym przykładzie tabela T1 zawiera indeks klastrowanego magazynu kolumn uporządkowany w kolejności Col_C, Col_B i Col_A.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

Wydajność zapytania 1 może być bardziej wyższa niż w przypadku trzech pozostałych zapytań. 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>Wydajność ładowania danych

Wydajność ładowania danych do uporządkowanej tabeli CCI jest podobna do tabeli podzielonej na partycje.  Ładowanie danych do uporządkowanej tabeli CCI może trwać dłużej niż nieu uporządkowana tabela CCI ze względu na operację sortowania danych, jednak zapytania mogą być uruchamiane szybciej później za pomocą uporządkowanego cci.  

Oto przykładowe porównanie wydajności ładowania danych do tabel z różnymi schematami.

![Wykres słupkowy przedstawiający porównanie wydajności ładowania danych do tabel z różnymi schematami.](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Poniżej znajduje się przykładowe porównanie wydajności zapytań między cci i uporządkowaną cci.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Zmniejszanie nakładających się segmentów

Liczba nakładających się segmentów zależy od rozmiaru danych do sortowania, dostępnej pamięci i ustawienia maksymalnego stopnia równoległości (MAXDOP) podczas tworzenia uporządkowanej liczby cci. Poniżej przedstawiono opcje zmniejszenia nakładających się segmentów podczas tworzenia uporządkowanych obrazów CCI.

- Użyj klasy zasobów xlargerc na wyższej jednostce DWU, aby umożliwić więcej pamięci na sortowanie danych, zanim konstruktor indeksu skompresuje dane w segmenty.  W segmencie indeksu nie można zmienić fizycznej lokalizacji danych.  Nie ma sortowania danych w obrębie segmentu ani między segmentami.  

- Utwórz uporządkowany cci z maxdop = 1.  Każdy wątek używany do tworzenia uporządkowanej cci działa na podzestawie danych i sortuje je lokalnie.  Nie ma globalnego sortowania danych posortowanych według różnych wątków.  Użycie równoległych wątków może skrócić czas tworzenia uporządkowanego cci, ale wygeneruje więcej nakładających się segmentów niż użycie pojedynczego wątku.  Obecnie opcja MAXDOP jest obsługiwana tylko podczas tworzenia uporządkowanej tabeli CCI przy użyciu CREATE TABLE AS SELECT.  Tworzenie uporządkowanej cci za pomocą polecenia CREATE INDEX lub CREATE TABLE nie obsługuje opcji MAXDOP. Na przykład

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- Przed załadowaniem danych do tabel posortuj je wstępnie według kluczy sortowania.

Oto przykład uporządkowanej dystrybucji tabel CCI, która nie nakłada się na segmenty zgodnie z powyższymi zaleceniami. Uporządkowana tabela CCI jest tworzona w bazie danych DWU1000c za pośrednictwem ctaS z tabeli sterty 20 GB przy użyciu maxdop 1 i xlargerc.  Identyfikator CCI jest uporządkowany w kolumnie BIGINT bez duplikatów.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Tworzenie uporządkowanego cci w dużych tabelach

Tworzenie uporządkowanego pliku CCI jest operacją w trybie offline.  W przypadku tabel bez partycji dane nie będą dostępne dla użytkowników, dopóki nie zakończy się uporządkowany proces tworzenia cci.   W przypadku tabel partycjonowanych, ponieważ aparat tworzy uporządkowaną partycję CCI według partycji, użytkownicy nadal mogą uzyskać dostęp do danych w partycjach, w których nie jest przetwarzane uporządkowane tworzenie cci.   Możesz użyć tej opcji, aby zminimalizować przestoje podczas zamawiania tworzenia cci w dużych tabelach: 

1.    Utwórz partycje w docelowej dużej tabeli (nazywanej Table_A).
2.    Utwórz pustą uporządkowaną tabelę CCI (nazywaną Table_B) z tym samym schematem tabeli i partycji co tabela A.
3.    Przełącz jedną partycję z tabeli A do tabeli B.
4.    Uruchom program ALTER INDEX <Ordered_CCI_Index> ON <Table_B> REBUILD PARTITION = <Partition_ID> tabeli B, aby ponownie skompilować partycję w trybie switched-in.  
5.    Powtórz kroki 3 i 4 dla każdej partycji w Table_A.
6.    Po przełączeniu wszystkich partycji z Table_A na Table_B i odbudowie należy usunąć Table_A i zmienić nazwę Table_B na Table_A. 

>[!TIP]
> W przypadku dedykowanej tabeli puli SQL z uporządkowaną cci, instrukcja ALTER INDEX REBUILD ponownie posortowa dane przy użyciu bazy danych tempdb. Monitor tempdb during rebuild operations (Monitorowanie bazy danych tempdb podczas operacji ponownego kompilowania). Jeśli potrzebujesz więcej miejsca w bazie danych tempdb, przeskaluj pulę w górę. Skaluj z powrotem w dół po zakończeniu ponownego kompilowania indeksu.
>
> W przypadku tabeli dedykowanej puli SQL z uporządkowaną cci reorganize alter INDEX nie sortuje ponownie danych. Aby skorzystać z danych, użyj funkcji ALTER INDEX REBUILD.

## <a name="examples"></a>Przykłady

**A. Aby sprawdzić kolumny uporządkowane i porządkowe:**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Aby zmienić liczbę porządkową kolumn, dodać lub usunąć kolumny z listy zamówień lub zmienić kolejność z CCI na uporządkowaną cci:**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej porad programistów, zobacz [Omówienie projektowania.](sql-data-warehouse-overview-develop.md)
