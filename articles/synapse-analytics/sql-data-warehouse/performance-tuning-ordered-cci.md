---
title: Strojenie wydajności za pomocą uporządkowanego klastrowanego indeksu magazynu kolumn
description: Zalecenia i zagadnienia, które należy znać w przypadku używania uporządkowanego klastrowanego indeksu magazynu kolumn w celu zwiększenia wydajności zapytań.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 48db8541ebad19e3b22b737f7e92dcc980708ef6
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91841598"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Strojenie wydajności za pomocą uporządkowanego klastrowanego indeksu magazynu kolumn  

Gdy użytkownicy wysyłają zapytanie do tabeli magazynu kolumn w puli SQL Synapse, optymalizator sprawdza wartości minimalne i maksymalne przechowywane w każdym segmencie.  Segmenty, które znajdują się poza granicami predykatu zapytania, nie są odczytywane z dysku do pamięci.  Zapytanie może zwiększyć wydajność, jeśli liczba segmentów do odczytu i ich łączny rozmiar jest mały.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Uporządkowany a nieuporządkowany klastrowany indeks magazynu kolumn

Domyślnie dla każdej tabeli utworzonej bez opcji index składnik wewnętrzny (Konstruktor indeksowania) tworzy na nim nieuporządkowany klastrowany indeks magazynu kolumn (WIK).  Dane w każdej kolumnie są kompresowane do oddzielnego segmentu grupy wierszy WIK.  Istnieją metadane dla każdego zakresu wartości segmentu, dlatego segmenty, które znajdują się poza granicami predykatu zapytania, nie są odczytywane z dysku podczas wykonywania zapytania.  WIK oferuje najwyższy poziom kompresji danych i zmniejsza rozmiar segmentów do odczytu, dzięki czemu zapytania mogą działać szybciej. Jednak ponieważ Konstruktor indeksu nie sortuje danych przed ich kompresowaniem do segmentów, mogą wystąpić segmenty z nakładającymi się zakresami wartości, co sprawia, że zapytania odczytują więcej segmentów z dysku i trwają dłużej.  

Podczas tworzenia uporządkowanej WIK aparat SQL Synapse sortuje istniejące dane w pamięci przez klucze kolejności, zanim Konstruktor index kompresuje je do segmentów indeksu.  Posortowane dane, nakładające się segmenty, zmniejszają się, umożliwiając wykonywanie zapytań o bardziej wydajny sposób eliminacji segmentów, co zwiększa wydajność, ponieważ liczba segmentów odczytywanych z dysku jest mniejsza.  Jeśli wszystkie dane można sortować jednocześnie w pamięci, można uniknąć nakładania się segmentów.  Ze względu na duże tabele w magazynach danych ten scenariusz nie jest często wykonywany.  

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
> W tabeli uporządkowanej WIK nowe dane, które wynikają z tej samej partii operacji ładowania DML lub danych, są sortowane w tej partii, nie istnieje sortowanie globalne dla wszystkich danych w tabeli.  Użytkownicy mogą odbudować uporządkowaną WIK, aby posortować wszystkie dane w tabeli.  W Synapse SQL, ponowne KOMPILOWAnie indeksu magazynu kolumn jest operacją offline.  W przypadku partycjonowanej tabeli ponowne KOMPILOWAnie wykonuje jedną partycję w danym momencie.  Dane w partycji, która jest ponownie skompilowana, są w trybie offline i niedostępne do momentu ukończenia odbudowy dla tej partycji. 

## <a name="query-performance"></a>Wydajność zapytań

Wzrost wydajności zapytania z uporządkowanej WIK zależy od wzorców zapytania, rozmiaru danych, sposobu sortowania danych, fizycznej struktury segmentów oraz klasy jednostek dwu i zasobów wybranej do wykonania zapytania.  Użytkownicy powinni przejrzeć wszystkie te czynniki przed wybraniem kolejności kolumn podczas projektowania uporządkowanej tabeli WIK.

Zapytania ze wszystkimi tymi wzorcami zazwyczaj działają szybciej przy użyciu uporządkowanej WIK.  
1. Zapytania mają równość, nierówność lub predykaty zakresu
1. Kolumny predykatu i uporządkowane kolumny WIK są takie same.  
1. Kolumny predykatu są używane w tej samej kolejności co numer kolumny uporządkowanej kolumny WIK.  
 
W tym przykładzie tabela T1 ma klastrowany indeks magazynu kolumn uporządkowany w sekwencji Col_C, Col_B i Col_A.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

Wydajność zapytania 1 może przynieść więcej korzyści od uporządkowanej WIK niż pozostałe trzy zapytania. 

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

Wydajność ładowania danych do uporządkowanej tabeli WIK jest podobna do tabeli partycjonowanej.  Ładowanie danych do uporządkowanej tabeli WIK może trwać dłużej niż nieuporządkowana tabela WIK z powodu operacji sortowania danych, jednak kwerendy mogą działać szybciej, a następnie z uporządkowaną WIK.  

Poniżej przedstawiono przykładowe porównanie wydajności ładowania danych do tabel z różnymi schematami.

![Wykres słupkowy przedstawiający porównanie wydajności ładowania danych do tabel z różnymi schematami.](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Oto przykładowe porównanie wydajności zapytań między WIK i uporządkowaną WIK.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Zmniejsz nakładające się segmenty

Liczba nakładających się segmentów zależy od rozmiaru danych do sortowania, dostępnej pamięci oraz ustawienia maksymalnego stopnia równoległości (MAXDOP) podczas tworzenia uporządkowanej WIK. Poniżej znajdują się opcje zmniejszania nakładania się segmentów podczas tworzenia uporządkowanej WIK.

- Użyj klasy zasobów xlargerc na wyższym jednostek dwu, aby umożliwić większą ilość pamięci na potrzeby sortowania danych, zanim Konstruktor indeksów kompresuje dane do segmentów.  Raz w segmencie indeksu nie można zmienić fizycznej lokalizacji danych.  Nie ma sortowania danych w ramach segmentu ani między segmentami.  

- Utwórz uporządkowaną WIK z MAXDOP = 1.  Każdy wątek używany do uporządkowanego tworzenia WIK działa w ramach podzestawu danych i sortuje go lokalnie.  Nie ma sortowania globalnego dla danych posortowanych według różnych wątków.  Użycie równoległych wątków może skrócić czas tworzenia uporządkowanej WIK, ale generuje więcej nakładających się segmentów niż przy użyciu jednego wątku.  Obecnie opcja MAXDOP jest obsługiwana tylko w przypadku tworzenia tabeli uporządkowanej WIK przy użyciu CREATE TABLE jako polecenia SELECT.  Tworzenie uporządkowanej WIK za pomocą poleceń CREATE INDEX lub CREATE TABLE nie obsługuje opcji MAXDOP. Przykład:

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- Przed załadowaniem danych do tabel należy je wstępnie sortować według kluczy sortowania.

Oto przykład uporządkowanej dystrybucji tabel WIK, która ma zerowy segment nakładający się na poniższe zalecenia. Uporządkowana tabela WIK jest tworzona w bazie danych DWU1000c za pośrednictwem CTAS z tabeli sterty 20 GB z użyciem MAXDOP 1 i xlargerc.  WIK jest uporządkowana w kolumnie BIGINT bez duplikatów.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Tworzenie uporządkowanej WIK w dużych tabelach

Tworzenie uporządkowanej WIK jest operacją offline.  W przypadku tabel bez partycji dane nie będą dostępne dla użytkowników, dopóki nie zostanie ukończony uporządkowany proces tworzenia WIK.   W przypadku partycjonowanych tabel, ponieważ aparat tworzy uporządkowaną partycję WIK według partycji, użytkownicy mogą nadal uzyskiwać dostęp do danych w partycjach, w których uporządkowane tworzenie WIK nie jest w toku.   Za pomocą tej opcji można zminimalizować przestoje podczas tworzenia uporządkowanej WIK w dużych tabelach: 

1.    Utwórz partycje w docelowej dużej tabeli (o nazwie Table_A).
2.    Utwórz pustą tabelę z uporządkowaną WIK (o nazwie Table_B) z tą samą tabelą i schematem partycji co tabela A.
3.    Przełącz jedną partycję z tabeli A na tabelę B.
4.    Uruchom polecenie ALTER INDEX <Ordered_CCI_Index> na <Table_B> Rebuild PARTITION = <Partition_ID> w tabeli B w celu odbudowania partycji przełączonej.  
5.    Powtórz kroki 3 i 4 dla każdej partycji w Table_A.
6.    Po przełączeniu wszystkich partycji z Table_A do Table_B i zostały one ponownie skompilowane, Porzuć Table_A i Zmień nazwę Table_B na Table_A. 

## <a name="examples"></a>Przykłady

**Z. Aby sprawdzić uporządkowane kolumny i numer porządkowy zamówienia:**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Aby zmienić numer porządkowy kolumny, dodać lub usunąć kolumny z listy Order lub zmienić z WIK na uporządkowaną WIK:**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](sql-data-warehouse-overview-develop.md).
