---
title: Wskazówki dotyczące projektowania tabel rozproszonych
description: Zalecenia dotyczące projektowania tabel rozproszonych rozproszonych i rozmieszczonych w trybie okrężnym przy użyciu dedykowanej puli SQL w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3b61df954e913671eafff4b739e0f53a4d420c28
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117303"
---
# <a name="guidance-for-designing-distributed-tables-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Wskazówki dotyczące projektowania tabel rozproszonych przy użyciu dedykowanej puli SQL w usłudze Azure Synapse Analytics

Zalecenia dotyczące projektowania tabel rozproszonych rozproszonych i rozmieszczonych w trybie okrężnym w dedykowanych pulach SQL.

W tym artykule założono, że znasz koncepcje dystrybucji i przenoszenia danych w dedykowanej puli SQL.  Aby uzyskać więcej informacji, zobacz [Architektura usługi Azure Synapse Analytics](massively-parallel-processing-mpp-architecture.md).

## <a name="what-is-a-distributed-table"></a>Co to jest tabela rozproszona?

Rozproszona tabela jest wyświetlana w postaci pojedynczej tabeli, ale wiersze są faktycznie przechowywane w dystrybucji 60. Wiersze są dystrybuowane z algorytmem skrótu lub działania okrężnego.  

**Tabele rozproszone** przez funkcję mieszania zwiększają wydajność zapytań w przypadku dużych tabel faktów i są skoncentrowane na tym artykule. **Tabele** działające w trybie okrężnym są przydatne do poprawy szybkości ładowania. Te opcje projektowe mają znaczny wpływ na poprawę wydajności zapytań i ładowania.

Inną opcją magazynu tabel jest replikacja małej tabeli we wszystkich węzłach obliczeniowych. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące projektowania zreplikowanych tabel](design-guidance-for-replicated-tables.md). Aby szybko wybierać spośród trzech opcji, Zobacz tabele rozproszone w [tabelach przegląd](sql-data-warehouse-tables-overview.md).

W ramach projektu tabeli należy zrozumieć możliwie jak najwięcej danych i jak są wykonywane zapytania dotyczące danych.  Rozważmy na przykład następujące pytania:

- Jak duży jest tabela?
- Jak często jest odświeżana tabela?
- Czy istnieją tabele faktów i wymiarów w dedykowanej puli SQL?

### <a name="hash-distributed"></a>Wartość skrótu dystrybuowana

W tabeli rozproszonej przez funkcję mieszania wiersze tabeli są dystrybuowane w węzłach obliczeniowych przy użyciu funkcji deterministycznej mieszania, która umożliwia przypisanie każdego wiersza do jednej [dystrybucji](massively-parallel-processing-mpp-architecture.md#distributions).

![Tabela rozproszona](./media/sql-data-warehouse-tables-distribute/hash-distributed-table.png "Tabela rozproszona")  

Ponieważ identyczne wartości zawsze są skrótami do tej samej dystrybucji, analiza SQL ma wbudowaną wiedzę o lokalizacjach wierszy. W dedykowanej puli SQL ta wiedza służy do minimalizowania przenoszenia danych podczas wykonywania zapytań, co zwiększa wydajność zapytań.

Tabele rozproszone przez funkcję mieszania dobrze sprawdzają się w przypadku dużych tabel faktów w schemacie gwiazdy. Mogą mieć bardzo dużą liczbę wierszy i nadal osiągać wysoką wydajność. Istnieją oczywiście zagadnienia dotyczące projektowania, które pomagają w uzyskaniu wydajności systemu rozproszonego do zapewnienia. Wybór odpowiedniej kolumny dystrybucji jest taki, jak opisano w tym artykule.

Rozważ użycie tabeli rozproszonej przez funkcję tworzenia skrótów, gdy:

- Rozmiar tabeli na dysku jest większy niż 2 GB.
- W tabeli występują częste operacje wstawiania, aktualizowania i usuwania.

### <a name="round-robin-distributed"></a>Rozdystrybuowane działania okrężne

Rozproszona tabela w trybie okrężnym dystrybuuje wiersze tabeli równomiernie między wszystkimi dystrybucjami. Przypisanie wierszy do dystrybucji jest losowe. W przeciwieństwie do tabel rozproszonych za pomocą skrótów, wiersze z równymi wartościami nie są gwarantowane do przypisania do tej samej dystrybucji.

W związku z tym system czasami musi wywołać operację przenoszenia danych, aby lepiej organizować dane, zanim będzie możliwe rozpoznanie zapytania.  Ten dodatkowy krok może spowolnić zapytania. Na przykład dołączenie tabeli okrężnej wymaga zwykle reshuffling wierszy, które są trafień wydajności.

Należy rozważyć użycie rozkładu okrężnego dla tabeli w następujących scenariuszach:

- Gdy rozpoczynasz pracę jako prosty punkt początkowy, ponieważ jest to wartość domyślna
- Jeśli nie istnieje oczywisty sprzężenie klucza
- Jeśli nie ma odpowiedniej kolumny kandydatów do dystrybucji dla tabeli
- Jeśli tabela nie udostępnia wspólnego klucza sprzężenia z innymi tabelami
- Jeśli sprzężenie jest mniej znaczące niż inne sprzężenia w zapytaniu
- Gdy tabela jest tymczasową tabelą tymczasową

Samouczek [Załaduj Nowy Jork Taxicab danych](./load-data-from-azure-blob-storage-using-copy.md#load-the-data-into-your-data-warehouse) zawiera przykład ładowania danych do tabeli przejściowej z działaniem okrężnym.

## <a name="choosing-a-distribution-column"></a>Wybieranie kolumny dystrybucji

Tabela dystrybuowana z mieszaniem ma kolumnę dystrybucji, która jest kluczem skrótu. Na przykład poniższy kod tworzy tabelę rozproszoną z użyciem skrótów z ProductKey jako kolumna dystrybucji.

```sql
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

Dane przechowywane w kolumnie dystrybucja można aktualizować. Aktualizacje danych w kolumnie dystrybucja mogą skutkować operacją losową danych.

Wybór kolumny dystrybucji jest ważną decyzją projektową, ponieważ wartości w tej kolumnie określają sposób dystrybuowania wierszy. Najlepszy wybór zależy od kilku czynników i zazwyczaj wymaga kompromisów. Po wybraniu kolumny dystrybucji nie można jej zmienić.  

Jeśli nie wybrano najlepszej kolumny po raz pierwszy, można użyć [CREATE TABLE jako Select (CTAs)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) , aby ponownie utworzyć tabelę z inną kolumną dystrybucji.

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Wybierz kolumnę dystrybucji z danymi, które dystrybuują równomiernie

W celu uzyskania najlepszej wydajności wszystkie dystrybucje powinny mieć mniej więcej tej samej liczby wierszy. Gdy co najmniej jedna dystrybucja ma nieproporcjonalną liczbę wierszy, Niektóre dystrybucje kończą swoją część zapytania równoległego przed innymi. Ponieważ nie można ukończyć zapytania, dopóki wszystkie dystrybucje nie zakończą przetwarzania, każde zapytanie będzie tak szybko, jak najwolniejsza dystrybucja.

- Pochylenie danych oznacza, że dane nie są rozkładane równomiernie między dystrybucjami
- Skośność przetwarzania oznacza, że niektóre rozkłady są wykonywane dłużej niż inne podczas wykonywania zapytań równoległych. Może się tak zdarzyć, gdy dane są skośne.
  
Aby zrównoważyć przetwarzanie równoległe, wybierz kolumnę dystrybucji, która:

- **Ma wiele unikatowych wartości.** Kolumna może zawierać kilka zduplikowanych wartości. Jednak wszystkie wiersze o tej samej wartości są przypisywane do tej samej dystrybucji. Ponieważ istnieją dystrybucje 60, kolumna powinna mieć co najmniej 60 unikatowych wartości.  Zwykle liczba unikatowych wartości jest znacznie większa.
- **Nie ma wartości NULL lub ma tylko kilka wartości NULL.** W przypadku skrajnego przykładu, jeśli wszystkie wartości w kolumnie mają wartość NULL, wszystkie wiersze są przypisywane do tej samej dystrybucji. W związku z tym przetwarzanie zapytań jest skośne w jednej dystrybucji i nie korzysta z przetwarzania równoległego.
- **Nie jest kolumną daty**. Wszystkie dane w tej samej dacie są gruntowe w tej samej dystrybucji. Jeśli kilku użytkowników ma wszystkie filtrowanie dla tego samego dnia, tylko 1 z dystrybucji 60 wszystkie operacje przetwarzania.

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Wybierz kolumnę dystrybucji, która minimalizuje przenoszenie danych

W celu uzyskania poprawnych kwerend wyników zapytania mogą przenosić dane z jednego węzła obliczeniowego do innego. Przenoszenie danych odbywa się często, gdy zapytania mają sprzężenia i agregacje w tabelach rozproszonych. Wybór kolumny dystrybucji, która pomaga zminimalizować przenoszenie danych, jest jednym z najważniejszych strategii optymalizacji wydajności dedykowanej puli SQL.

Aby zminimalizować przenoszenie danych, wybierz kolumnę dystrybucji, która:

- Jest używana w `JOIN` `GROUP BY` klauzulach,,, `DISTINCT` `OVER` i `HAVING` . Gdy dwa duże tabele faktów mają częste sprzężenia, wydajność zapytań zwiększa się, gdy obie tabele są dystrybuowane w jednej z kolumn sprzężenia.  Gdy tabela nie jest używana w sprzężeniach, rozważ rozłożenie tabeli w kolumnie, która jest często w `GROUP BY` klauzuli.
- *Nie* jest używany w `WHERE` klauzulach. Może to spowodować zawężenie zapytania, aby nie było uruchamiane na wszystkich dystrybucjach.
- *Nie* jest kolumną daty. Klauzule WHERE często filtrują według daty.  W takim przypadku przetwarzanie może być wykonywane tylko w kilku dystrybucjach.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Co zrobić, gdy żadna z kolumn nie jest dobrą kolumną dystrybucji

Jeśli żadna z kolumn nie ma wystarczającej liczby odrębnych wartości dla kolumny dystrybucji, można utworzyć nową kolumnę jako element złożony jednej lub więcej wartości. Aby uniknąć przenoszenia danych podczas wykonywania zapytania, użyj kolumny dystrybucji złożonej jako kolumny sprzężenia w zapytaniach.

Po zaprojektowaniu tabeli rozproszonej przez funkcję mieszania następnym krokiem jest załadowanie danych do tabeli.  Aby uzyskać wskazówki dotyczące ładowania, zobacz [Omówienie ładowania](design-elt-data-loading.md).

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Jak stwierdzić, czy Twoja kolumna dystrybucji jest dobrym wyborem

Po załadowaniu danych do tabeli rozproszonej przez funkcję tworzenia skrótów Sprawdź, czy wiersze są dystrybuowane w ramach dystrybucji 60. Wiersze na dystrybucję mogą się różnić do 10% bez zauważalnego wpływu na wydajność.

### <a name="determine-if-the-table-has-data-skew"></a>Ustal, czy tabela ma skośne dane

Aby szybko sprawdzić pochylenie danych, należy użyć [polecenia DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Poniższy kod SQL zwraca liczbę wierszy tabeli, które są przechowywane w każdej z dystrybucji 60. W celu zapewnienia zrównoważonej wydajności wiersze w tabeli rozproszonej powinny być równomiernie rozłożone na wszystkie dystrybucje.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Aby określić, które tabele mają więcej niż 10% pochylenia danych:

1. Utwórz widok dbo. vTableSizes, który jest widoczny w artykule [Przegląd tabel](sql-data-warehouse-tables-overview.md#table-size-queries) .  
2. Uruchom poniższe zapytanie:

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having (max(row_count * 1.000) - min(row_count * 1.000))/max(row_count * 1.000) >= .10
    )
order by two_part_name, row_count
;
```

### <a name="check-query-plans-for-data-movement"></a>Sprawdź plany zapytania dotyczące przenoszenia danych

Dobra kolumna dystrybucji umożliwia łączenie i agregacje w celu uzyskania minimalnych ruchów danych. Ma to wpływ na sposób zapisywania sprzężeń. Aby uzyskać minimalny ruch danych dla sprzężenia w dwóch tabelach rozproszonych z mieszaniem, jedną z kolumn sprzężeń musi być kolumna rozkład.  Gdy dwie tabele rozproszone przez mieszanie są przyłączone do kolumny dystrybucji tego samego typu danych, sprzężenie nie wymaga przenoszenia danych. Sprzężenia mogą używać dodatkowych kolumn bez ponoszenia przenoszenia danych.

Aby uniknąć przenoszenia danych podczas przyłączania:

- Tabele biorące udział w sprzężeniu muszą być dystrybuowane jako skrót na **jednej** z kolumn należących do sprzężenia.
- Typy danych kolumn sprzężenia muszą być zgodne między obiema tabelami.
- Kolumny muszą być sprzężone z operatorem Equals.
- Typ sprzężenia nie może być `CROSS JOIN` .

Aby sprawdzić, czy w kwerendach występują przemieszczenie danych, możesz przyjrzeć się planie zapytania.  

## <a name="resolve-a-distribution-column-problem"></a>Rozwiązywanie problemu z kolumną dystrybucji

Nie jest konieczne rozwiązywanie wszystkich przypadków pochylenia danych. Dystrybucja danych polega na znalezieniu równowagi między zmniejszaniem i przenoszeniem danych. Nie zawsze jest możliwe minimalizowanie pochylenia i przenoszenia danych. Czasami korzyści wynikające z minimalnego przenoszenia danych mogą wznieść wpływ na pochylenie danych.

Aby zdecydować, czy należy rozwiązać pochylenie danych w tabeli, należy zrozumieć możliwie jak najwięcej ilości danych i zapytań w obciążeniu. Aby monitorować wpływ pochylenia wydajności zapytań, można użyć kroków opisanych w artykule [monitorowanie zapytań](sql-data-warehouse-manage-monitor.md) . Sprawdź, jak długo trwa wykonywanie dużych zapytań dotyczących poszczególnych dystrybucji.

Ponieważ nie można zmienić kolumny dystrybucji w istniejącej tabeli, typowym sposobem rozwiązania pochylenia danych jest ponowne utworzenie tabeli z inną kolumną dystrybucji.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Utwórz ponownie tabelę z nową kolumną dystrybucji

W tym przykładzie używa się [CREATE TABLE jako zaznaczone,](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) aby ponownie utworzyć tabelę z inną kolumną dystrybucji skrótów.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

## <a name="next-steps"></a>Następne kroki

Aby utworzyć tabelę rozproszoną, należy użyć jednej z następujących instrukcji:

- [CREATE TABLE (dedykowana Pula SQL)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE jako wybór (dedykowana Pula SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)