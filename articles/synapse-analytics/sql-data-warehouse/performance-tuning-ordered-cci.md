---
title: Strojenie wydajności za pomocą uporządkowanego klastrowanego indeksu magazynu kolumn
description: Zalecenia i zagadnienia, które należy znać podczas korzystania z indeksu magazynu kolumn klastrowanych uporządkowanej, aby zwiększyć wydajność kwerend.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 088a0d10b96a30ef830b4e8a8dc12c19127141db
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417040"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Strojenie wydajności za pomocą uporządkowanego klastrowanego indeksu magazynu kolumn  

Gdy użytkownicy kwerendy tabeli magazynu kolumn w puli Synapse SQL, optymalizator sprawdza minimalne i maksymalne wartości przechowywane w każdym segmencie.  Segmenty, które są poza granicami predykatu kwerendy nie są odczytywane z dysku do pamięci.  Kwerenda może uzyskać szybszą wydajność, jeśli liczba segmentów do odczytu i ich całkowity rozmiar są małe.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Zamówiony a niezamówiony indeks klastrowanego magazynu kolumn

Domyślnie dla każdej tabeli utworzonej bez opcji indeksu wewnętrzny składnik (konstruktor indeksów) tworzy na niej nie uporządkowany indeks klastrowanego magazynu kolumn (CCI).  Dane w każdej kolumnie są kompresowane do oddzielnego segmentu grupy wierszy CCI.  Istnieją metadane w zakresie wartości każdego segmentu, więc segmenty, które znajdują się poza granicami predykatu kwerendy, nie są odczytywane z dysku podczas wykonywania kwerendy.  CCI oferuje najwyższy poziom kompresji danych i zmniejsza rozmiar segmentów do odczytu, dzięki czemu kwerendy mogą działać szybciej. Jednak ponieważ konstruktor indeksów nie sortuje danych przed skompresowaniem ich do segmentów, mogą wystąpić segmenty z nakładającymi się zakresami wartości, co powoduje, że kwerendy czytają więcej segmentów z dysku i kończą dłużej.  

Podczas tworzenia uporządkowanej łącze CCI aparat Synapse SQL sortuje istniejące dane w pamięci według klucza(-ów) kolejności, zanim konstruktor indeksów kompresuje je do segmentów indeksu.  W przypadku posortowanych danych zmniejsza się nakładanie się segmentów, co pozwala kwerendom na bardziej wydajną eliminację segmentów, a tym samym na większą wydajność, ponieważ liczba segmentów do odczytu z dysku jest mniejsza.  Jeśli wszystkie dane można sortować w pamięci naraz, można uniknąć nakładania się segmentów.  Ze względu na duże tabele w magazynach danych ten scenariusz nie zdarza się często.  

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
> W uporządkowanej tabeli CCI nowe dane wynikające z tej samej partii DML lub operacji ładowania danych są sortowane w ramach tej partii, nie ma globalnego sortowania wszystkich danych w tabeli.  Użytkownicy mogą odbudować uporządkowane CCI, aby posortować wszystkie dane w tabeli.  W Synapse SQL indeks magazynu kolumn REBUILD jest operacją offline.  Dla tabeli podzielonej na partycje, REBUILD odbywa się po jednej partycji naraz.  Dane w partycji, która jest przebudowywana jest "w trybie offline" i niedostępne, dopóki przebudowa nie zostanie ukończona dla tej partycji. 

## <a name="query-performance"></a>Wydajność zapytań

Przyrost wydajności kwerendy z uporządkowanego CCI zależy od wzorców zapytań, rozmiaru danych, jak dobrze dane są sortowane, fizycznej struktury segmentów i DWU i klasy zasobów wybrane dla wykonywania kwerendy.  Użytkownicy powinni przejrzeć wszystkie te czynniki przed wybraniem kolumn zamawiania podczas projektowania uporządkowanej tabeli CCI.

Zapytania z tych wszystkich wzorców zazwyczaj działają szybciej z uporządkowanych CCI.  
1. Zapytania mają równość, nierówność lub predykaty zakresu
1. Kolumny predykatu i uporządkowane kolumny CCI są takie same.  
1. Kolumny predykatu są używane w tej samej kolejności co kolumna porządkowa uporządkowanych kolumn CCI.  
 
W tym przykładzie tabela T1 ma indeks klastrowanego magazynu kolumn uporządkowany w sekwencji Col_C, Col_B i Col_A.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

Wydajność kwerendy 1 może korzystać więcej z uporządkowanych CCI niż pozostałe trzy kwerendy. 

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

Wydajność ładowania danych do uporządkowanej tabeli CCI jest podobna do tabeli podzielonej na partycje.  Ładowanie danych do uporządkowanej tabeli CCI może trwać dłużej niż niezamówionej tabeli CCI z powodu operacji sortowania danych, jednak kwerendy mogą działać szybciej później z uporządkowanymi CCI.  

Oto przykładowe porównanie wydajności ładowania danych do tabel z różnymi schematami.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Oto przykładowe porównanie wydajności kwerendy między CCI i uporządkowane CCI.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Zmniejszanie nakładania się segmentów

Liczba nakładających się segmentów zależy od rozmiaru danych do sortowania, dostępnej pamięci i maksymalnego stopnia równoległości (MAXDOP) podczas tworzenia uporządkowanego CCI. Poniżej znajdują się opcje zmniejszenia nakładania się segmentów podczas tworzenia uporządkowanych CCI.

- Użyj klasy zasobów xlargerc na wyższym DWU, aby umożliwić więcej pamięci do sortowania danych, zanim konstruktor indeksów kompresuje dane do segmentów.  Raz w segmencie indeksu nie można zmienić fizycznej lokalizacji danych.  Nie ma sortowania danych w segmencie ani w różnych segmentach.  

- Utwórz uporządkowane CCI z MAXDOP = 1.  Każdy wątek używany do tworzenia uporządkowanych CCI działa na podzbiór danych i sortuje go lokalnie.  Nie ma globalnego sortowania danych posortowanych według różnych wątków.  Za pomocą równoległych wątków można skrócić czas tworzenia uporządkowanej CCI, ale wygeneruje więcej nakładających się segmentów niż przy użyciu pojedynczego wątku.  Obecnie opcja MAXDOP jest obsługiwana tylko przy tworzeniu uporządkowanej tabeli CCI za pomocą polecenia CREATE TABLE AS SELECT.  Tworzenie uporządkowanych CCI za pomocą polecenia CREATE INDEX lub CREATE TABLE nie obsługuje opcji MAXDOP. Na przykład:

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- Przed załadowaniem ich do tabel wstępnie posortuj dane według kluczy sortowania.

Oto przykład uporządkowanej dystrybucji tabeli CCI, która ma zero segmentów nakładających się zgodnie z powyższymi zaleceniami. Uporządkowana tabela CCI jest tworzona w bazie danych DWU1000c za pośrednictwem CTAS z tabeli sterty 20 GB przy użyciu MAXDOP 1 i xlargerc.  CCI jest zamawiany na kolumnie BIGINT bez duplikatów.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Tworzenie uporządkowanych CCI w dużych tabelach

Tworzenie uporządkowanej CCI jest operacją w trybie offline.  W przypadku tabel bez partycji dane nie będą dostępne dla użytkowników, dopóki nie zostanie ukończony proces tworzenia uporządkowanej usługi CCI.   W przypadku tabel podzielonych na partycje, ponieważ aparat tworzy uporządkowaną partycję CCI według partycji, użytkownicy nadal mogą uzyskiwać dostęp do danych w partycjach, w których uporządkowane tworzenie CCI nie jest w toku.   Za pomocą tej opcji można zminimalizować przestoje podczas tworzenia zamówionych CCI na dużych tabelach: 

1.    Tworzenie partycji w docelowej tabeli dużej (o nazwie Table_A).
2.    Utwórz pustą uporządkowaną tabelę CCI (zwaną Table_B) z tym samym schematem tabeli i partycji co tabela A.
3.    Przełącz jedną partycję z tabeli A do tabeli B.
4.    Uruchom alter index <Ordered_CCI_Index> on <Table_B> REBUILD PARTITION = <Partition_ID> w tabeli B, aby odbudować partycję przełączaną.  
5.    Powtórz krok 3 i 4 dla każdej partycji w Table_A.
6.    Po przełączeniu wszystkich partycji z Table_A na Table_B i przebudowy, Table_A upuszczania i zmienianie nazwy Table_B na Table_A. 

## <a name="examples"></a>Przykłady

**A. Aby sprawdzić, czy uporządkowane kolumny i porządkowe porządkowe kolejności:**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Aby zmienić porządek kolumnowy, dodać lub usunąć kolumny z listy zamówień lub zmienić z CCI na uporządkowane CCI:**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej wskazówek dotyczących rozwoju, zobacz [omówienie rozwoju](sql-data-warehouse-overview-develop.md).
