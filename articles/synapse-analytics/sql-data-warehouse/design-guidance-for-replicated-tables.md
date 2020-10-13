---
title: Wskazówki dotyczące projektowania zreplikowanych tabel
description: Zalecenia dotyczące projektowania zreplikowanych tabel w puli SQL Synapse
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/19/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 036cb15cf16b5f90dc17ccdce378a073a398d403
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86181339"
---
# <a name="design-guidance-for-using-replicated-tables-in-synapse-sql-pool"></a>Wskazówki dotyczące projektowania na potrzeby używania zreplikowanych tabel w puli SQL Synapse

W tym artykule przedstawiono zalecenia dotyczące projektowania replikowanych tabel w schemacie puli SQL Synapse. Te zalecenia umożliwiają zwiększenie wydajności zapytań, zmniejszając jednocześnie przemieszczenie danych i złożoność zapytań.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz koncepcje dystrybucji i przenoszenia danych w puli SQL.Aby uzyskać więcej informacji, zobacz artykuł dotyczący [architektury](massively-parallel-processing-mpp-architecture.md) .

W ramach projektu tabeli należy zrozumieć możliwie jak najwięcej danych i jak są wykonywane zapytania dotyczące danych.Rozważmy na przykład następujące pytania:

- Jak duży jest tabela?
- Jak często jest odświeżana tabela?
- Czy istnieją tabele faktów i wymiarów w bazie danych puli SQL?

## <a name="what-is-a-replicated-table"></a>Co to jest zreplikowana tabela?

Replikowana tabela ma pełną kopię tabeli dostępną w każdym węźle obliczeniowym. Replikowanie tabeli eliminuje konieczność przesyłania danych między węzłami obliczeniowymi przed operacją sprzężenia lub agregacji. Ponieważ tabela ma wiele kopii, zreplikowane tabele działają najlepiej, gdy rozmiar tabeli jest mniejszy niż 2 GB skompresowane.  2 GB nie jest sztywnym limitem.  Jeśli dane są statyczne i nie zmieniają się, można replikować większe tabele.

Na poniższym diagramie przedstawiono zreplikowane tabele, które są dostępne w każdym węźle obliczeniowym. W puli SQL replikowana tabela jest w pełni kopiowana do bazy danych dystrybucji w każdym węźle obliczeniowym.

![Tabela replikowana](./media/design-guidance-for-replicated-tables/replicated-table.png "Tabela replikowana")  

Zreplikowane tabele działają dobrze w przypadku tabel wymiarów w schemacie gwiazdy. Tabele wymiarów są zwykle przyłączone do tabel faktów dystrybuowanych inaczej niż tabela wymiarów.  Wymiary mają zwykle rozmiar, który umożliwia przechowywanie i konserwowanie wielu kopii. Wymiary opisują dane, które zmieniają się powoli, takie jak nazwa i adres klienta oraz szczegóły produktu. Wolno zmieniający się charakter danych prowadzi do mniejszej konserwacji zreplikowanej tabeli.

Rozważ użycie zreplikowanej tabeli, gdy:

- Rozmiar tabeli na dysku jest mniejszy niż 2 GB, niezależnie od liczby wierszy. Aby znaleźć rozmiar tabeli, można użyć polecenia [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) : `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')` .
- Tabela jest używana w sprzężeniach, które w przeciwnym razie wymagają przeniesienia danych. Podczas sprzęgania tabel, które nie są dystrybuowane w tej samej kolumnie, takich jak tabela dystrybuowana z mieszaniem do tabeli okrężnej, przenoszenie danych jest wymagane do ukończenia zapytania.  Jeśli jedna z tabel jest mała, weź pod uwagę zreplikowaną tabelę. W większości przypadków zalecamy używanie zreplikowanych tabel zamiast tabel z działaniem okrężnym. Aby wyświetlić operacje przenoszenia danych w planach zapytań, użyj [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  BroadcastMoveOperation to typowa Operacja przenoszenia danych, którą można wyeliminować przy użyciu zreplikowanej tabeli.  

Zreplikowane tabele mogą nie dać najlepszej wydajności zapytania, gdy:

- W tabeli występują częste operacje wstawiania, aktualizowania i usuwania.Operacje języka danych (DML) wymagają odbudowania zreplikowanej tabeli.Ponowne kompilowanie często może spowodować wolniejszą wydajność.
- Baza danych puli SQL jest często skalowana. Skalowanie bazy danych puli SQL zmienia liczbę węzłów obliczeniowych, co powoduje ponowne skompilowanie zreplikowanej tabeli.
- Tabela zawiera dużą liczbę kolumn, ale operacje na danych zazwyczaj uzyskują dostęp tylko do niewielkiej liczby kolumn. W tym scenariuszu zamiast replikowania całej tabeli może być bardziej efektywne dystrybuowanie tabeli, a następnie utworzenie indeksu na często używanych kolumnach. Gdy zapytanie wymaga przenoszenia danych, Pula SQL przenosi tylko dane dla żądanych kolumn.

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Korzystanie z zreplikowanych tabel z prostymi predykatami zapytań

Przed wybraniem opcji dystrybucji lub replikowania tabeli należy wziąć pod uwagę typy zapytań, które mają być uruchamiane względem tabeli. Zawsze, gdy jest to możliwe,

- Używaj zreplikowanych tabel dla zapytań z prostymi predykatami zapytań, takimi jak równość lub nierówności.
- Używaj tabel rozproszonych dla zapytań ze złożonymi predykatami zapytań, takimi jak takie jak lub niepodobne.

Zapytania intensywnie korzystające z procesora CPU działają najlepiej, gdy prace są dystrybuowane we wszystkich węzłach obliczeniowych. Na przykład zapytania, które uruchamiają obliczenia dla każdego wiersza tabeli, działają lepiej w tabelach rozproszonych niż zreplikowane tabele. Ponieważ zreplikowana tabela jest przechowywana w całości na każdym węźle obliczeniowym, zapytanie intensywnie korzystające z procesora CPU względem zreplikowanej tabeli działa w całej tabeli w każdym węźle obliczeniowym. Dodatkowe obliczenie może spowolnić wykonywanie zapytań.

Na przykład to zapytanie ma złożony predykat.  Działa szybciej, gdy dane są w rozproszonej tabeli zamiast zreplikowanej tabeli. W tym przykładzie dane mogą być dystrybuowane do działania okrężnego.

```sql

SELECT EnglishProductName
FROM DimProduct
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Konwertuj istniejące tabele działania okrężnego na zreplikowane tabele

Jeśli masz już tabele działające w trybie okrężnym, zalecamy przekonwertowanie ich na zreplikowane tabele, jeśli spełniają kryteria opisane w tym artykule. Zreplikowane tabele zwiększają wydajność w przypadku tabel z działaniem okrężnym, ponieważ eliminują potrzebę przenoszenia danych.  Tabela działania okrężnego zawsze wymaga przenoszenia danych dla sprzężeń.

W tym przykładzie zastosowano [CTAs](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) do zmiany tabeli dimSalesTerritory na zreplikowaną tabelę. Ten przykład działa niezależnie od tego, czy DimSalesTerritory jest oparta na skrócie lub działa w trybie okrężnym.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]
WITH
  (
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE')

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Przykład wydajności zapytań dla działania okrężnego i zreplikowanego

Replikowana tabela nie wymaga przenoszenia danych dla sprzężeń, ponieważ cała tabela jest już obecna w każdym węźle obliczeniowym. Jeśli tabele wymiarów są dystrybuowane w sposób okrężny, sprzężenie kopiuje tabelę wymiarów w całości do każdego węzła obliczeniowego. Aby przenieść dane, plan zapytania zawiera operację o nazwie BroadcastMoveOperation. Ten typ operacji przenoszenia danych spowalnia wydajność zapytań i jest eliminowany przy użyciu zreplikowanych tabel. Aby wyświetlić kroki planu zapytania, użyj widoku wykazu systemu [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .  

Na przykład w poniższym zapytaniu względem schematu AdventureWorks `FactInternetSales` tabela jest dystrybuowana z mieszaniem. `DimDate`Tabele i `DimSalesTerritory` są mniejszymi tabelami wymiarów. To zapytanie zwraca łączną wartość sprzedaży w Ameryka Północna dla roku obrachunkowego 2004:

```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```

Tworzenie `DimDate` i `DimSalesTerritory` jak tabele działające w trybie okrężnym. W efekcie zapytanie wykazało następujący plan zapytania, który ma wiele operacji przenoszenia emisji:

![Plan zapytania okrężnego](./media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg)

Ponownie utworzono `DimDate` i `DimSalesTerritory` jako zreplikowane tabele i ponownie uruchomiono zapytanie. Wynikający z tego plan zapytania jest znacznie krótszy i nie są przenoszone żadne emisje.

![Zreplikowany plan zapytania](./media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg)

## <a name="performance-considerations-for-modifying-replicated-tables"></a>Zagadnienia dotyczące wydajności związane z modyfikowaniem zreplikowanych tabel

Pula SQL implementuje zreplikowaną tabelę przez utrzymywanie głównej wersji tabeli. Kopiuje wersję główną do pierwszej bazy danych dystrybucji w każdym węźle obliczeniowym. W przypadku zmiany należy najpierw zaktualizować wersję główną, a następnie tabele w każdym węźle obliczeniowym są ponownie skompilowane. Odbudowa zreplikowanej tabeli obejmuje skopiowanie tabeli do każdego węzła obliczeniowego, a następnie utworzenie indeksów.  Na przykład zreplikowana tabela na DW2000c ma 5 kopii danych.  Kopia główna i pełna kopia na każdym węźle obliczeniowym.  Wszystkie dane są przechowywane w bazach danych dystrybucji. Pula SQL używa tego modelu do obsługi szybszych instrukcji modyfikacji danych i elastycznych operacji skalowania.

Ponowne kompilacje asynchroniczne są wyzwalane przez pierwsze zapytanie względem zreplikowanej tabeli po:

- Dane są ładowane lub modyfikowane
- Wystąpienie SQL Synapse jest skalowane do innego poziomu
- Definicja tabeli została zaktualizowana

Ponowne kompilacje nie są wymagane po:

- Wstrzymaj operację
- Wznów operację

Ponowne kompilowanie nie następuje natychmiast po zmodyfikowaniu danych. Zamiast tego, ponowne kompilowanie jest wyzwalane podczas pierwszego wybierania zapytania z tabeli.  Zapytanie, które wyzwoliło odbudowę natychmiast, od wersji głównej tabeli, gdy dane są asynchronicznie kopiowane do każdego węzła obliczeniowego. Do momentu ukończenia kopiowania danych kolejne zapytania będą nadal używać wersji głównej tabeli.  Jeśli dowolne działanie dotyczy zreplikowanej tabeli, która wymusza kolejną ponowną kompilację, kopiowanie danych jest unieważnione i kolejna instrukcja SELECT wyzwoli dane, które zostaną ponownie skopiowane.

### <a name="use-indexes-conservatively"></a>Używaj indeksów w sposób ostrożny

Standardowe rozwiązania indeksowania mają zastosowanie do zreplikowanych tabel. Pula SQL odbudowuje każdy replikowany indeks tabeli w ramach odbudowy. Indeksy należy stosować tylko wtedy, gdy wydajność zwiększa koszt odbudowy indeksów.

### <a name="batch-data-load"></a>Ładowanie danych wsadowych

Podczas ładowania danych do zreplikowanych tabel, spróbuj zminimalizować kompilacje w partiach zbiorczo. Wykonaj wszystkie obciążenia wsadowe przed uruchomieniem instrukcji SELECT.

Na przykład ten wzorzec ładowania ładuje dane z czterech źródeł i wywołuje cztery ponowne kompilacje.

- Załaduj ze źródła 1.
- Wybierz wyzwalacz instrukcji Kompiluj ponownie 1.
- Załaduj ze źródła 2.
- Wybierz wyzwalacz instrukcji Kompiluj ponownie 2.
- Załaduj ze źródła 3.
- SELECT Statement wyzwala kompilację ponownie 3.
- Załaduj ze źródła 4.
- Select instrukcji Trigger Kompiluj ponownie 4.

Na przykład ten wzorzec ładowania ładuje dane z czterech źródeł, ale wywołuje tylko jedną rekompilację.

- Załaduj ze źródła 1.
- Załaduj ze źródła 2.
- Załaduj ze źródła 3.
- Załaduj ze źródła 4.
- Wybierz opcję Kompiluj ponownie wyzwalacze instrukcji.

### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Kompiluj zreplikowaną tabelę po załadowaniu partii

Aby zapewnić spójne czasy wykonywania zapytań, rozważ wymuszenie skompilowania zreplikowanych tabel po załadowaniu partii. W przeciwnym razie pierwsze zapytanie będzie nadal używać przenoszenia danych do ukończenia zapytania.

To zapytanie używa DMV [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) , aby wyświetlić zreplikowane tabele, które zostały zmodyfikowane, ale nie zostały odbudowane.

```sql
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id
  JOIN sys.pdw_table_distribution_properties p
    ON p.object_id = t.object_id
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```

Aby wyzwolić ponowną kompilację, uruchom następującą instrukcję dla każdej tabeli w poprzednich danych wyjściowych.

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
```

## <a name="next-steps"></a>Następne kroki

Aby utworzyć zreplikowaną tabelę, należy użyć jednej z następujących instrukcji:

- [CREATE TABLE (Pula SQL)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE jako SELECT (Pula SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Aby zapoznać się z omówieniem tabel rozproszonych, zobacz [tabele rozproszone](sql-data-warehouse-tables-distribute.md).
