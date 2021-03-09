---
title: Optymalizowanie transakcji dla dedykowanej puli SQL
description: Dowiedz się, jak zoptymalizować wydajność kodu transakcyjnego w dedykowanej puli SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 8b1f820cfca0e352b49d815e2b99d407ccc8ce43
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505702"
---
# <a name="optimize-transactions-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Optymalizowanie transakcji za pomocą dedykowanej puli SQL w usłudze Azure Synapse Analytics 

Dowiedz się, jak zoptymalizować wydajność kodu transakcyjnego w dedykowanej puli SQL, jednocześnie minimalizując ryzyko długotrwałego wycofywania.

## <a name="transactions-and-logging"></a>Transakcje i rejestrowanie

Transakcje są ważnym składnikiem aparatu relacyjnej bazy danych. Dedykowana Pula SQL używa transakcji podczas modyfikacji danych. Te transakcje mogą być jawne lub niejawne. Pojedyncze instrukcje INSERT, UPDATE i DELETE to wszystkie przykłady niejawnych transakcji. Transakcje jawne używają instrukcji BEGIN przeładunek, Zatwierdź transakcję lub Wycofaj ładunek. Jawne transakcje są zwykle używane, gdy wielokrotne instrukcje modyfikacji muszą być powiązane z pojedynczą jednostką niepodzielną.

Dedykowana Pula SQL zatwierdza zmiany w bazie danych przy użyciu dzienników transakcji. Każda dystrybucja ma swój własny dziennik transakcji. Zapisy w dzienniku transakcji są automatyczne. Nie jest wymagana żadna konfiguracja. Jednak proces ten gwarantuje, że zapis wprowadza obciążenie systemu. Ten wpływ można zminimalizować przez zapisanie wydajnego kodu transactionowego. W szerokim stopniu funkcjonalny kod jest podzielony na dwie kategorie.

* Używaj minimalnych konstrukcji rejestrowania wszędzie tam, gdzie to możliwe
* Przetwarzaj dane przy użyciu partii o określonym zakresie, aby uniknąć długotrwałych transakcji długoterminowych
* Przyjęcie wzorca przełączania partycji dla dużych modyfikacji w danej partycji

## <a name="minimal-vs-full-logging"></a>Rejestrowanie minimalne i pełne

W przeciwieństwie do w pełni zarejestrowanej operacji, która korzysta z dziennika transakcji do śledzenia każdej zmiany w wierszu, co najmniej zarejestrowane operacje śledzą tylko zmiany w zakresie alokacji i metadanych. W związku z tym minimalne rejestrowanie polega na rejestrowaniu tylko informacji wymaganych do wycofania transakcji po awarii lub w przypadku jawnego żądania (Wycofaj wyjątek). Gdy mniej informacji jest śledzonych w dzienniku transakcji, minimalna zarejestrowana operacja jest lepsza niż w pełni zarejestrowana operacja. Ponadto, ponieważ mniejsza liczba zapisów to dziennik transakcji, jest generowanych dużo mniejszych ilości danych dziennika i dlatego jest to większa wydajność operacji we/wy.

Limity bezpieczeństwa transakcji mają zastosowanie tylko do w pełni zarejestrowanych operacji.

> [!NOTE]
> Operacje o minimalnym zarejestrowaniu mogą uczestniczyć w jawnych transakcjach. Ponieważ wszystkie zmiany struktur alokacji są śledzone, możliwe jest wycofanie operacji o minimalnym zarejestrowaniu.

## <a name="minimally-logged-operations"></a>Operacje o minimalnym rejestrowaniu

Następujące operacje mogą być w sposób minimalny zarejestrowane:

* CREATE TABLE jako SELECT ([CTAs](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json))
* Wstaw.. ZAZNACZENIA
* CREATE INDEX
* ALTER INDEX REBUILD
* DROP INDEX
* TRUNCATE TABLE
* DROP TABLE
* ALTER TABLE SWITCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> Nie ma to wpływ na operacje wewnętrznego przenoszenia danych (takie jak EMISJa i losowo).

## <a name="minimal-logging-with-bulk-load"></a>Minimalne rejestrowanie przy ładowaniu zbiorczym

CTAS i Wstaw... SELECT to operacje ładowania zbiorczego. Jednak oba mają wpływ na definicję tabeli docelowej i zależą od scenariusza ładowania. W poniższej tabeli wyjaśniono, kiedy operacje zbiorcze są w pełni lub co najmniej rejestrowane:  

| Indeks podstawowy | Scenariusz ładowania | Tryb rejestrowania |
| --- | --- | --- |
| Sterta |Dowolne |**Minimalny** |
| Klastrowany indeks |Pusta tabela docelowa |**Minimalny** |
| Klastrowany indeks |Załadowane wiersze nie nakładają się na istniejące strony w miejscu docelowym |**Minimalny** |
| Klastrowany indeks |Załadowane wiersze nakładają się na istniejące strony w miejscu docelowym |Pełne |
| Klastrowany indeks magazynu kolumn |Rozmiar wsadu >= 102 400 na wyrównanej dystrybucji partycji |**Minimalny** |
| Klastrowany indeks magazynu kolumn |Rozmiar wsadu < 102 400 na rozkład wyrównany do partycji |Pełne |

Warto zauważyć, że wszystkie zapisy do aktualizacji pomocniczych lub nieklastrowanych indeksów będą zawsze w pełni zarejestrowane.

> [!IMPORTANT]
> Dedykowana Pula SQL ma dystrybucje 60. W związku z tym, przy założeniu, że wszystkie wiersze są równomiernie dystrybuowane i wypełnianie w pojedynczej partycji, partia będzie musiała zawierać 6 144 000 wierszy lub większa do minimalnej rejestracji podczas zapisywania w klastrowanym indeksie magazynu kolumn. Jeśli tabela jest podzielona na partycje i wstawiane wiersze rozciągają się na granice partycji, w granicach partycji będą potrzebne 6 144 000 wierszy, przy założeniu, że nawet dystrybucji danych. Każda partycja w każdej dystrybucji musi niezależnie przekroczyć próg wiersza 102 400 dla operacji INSERT, który ma być minimalnym zalogowaniem do dystrybucji.

Ładowanie danych do niepustej tabeli z indeksem klastrowanym może często zawierać kombinację całkowicie zarejestrowanych i minimalnych zarejestrowanych wierszy. Klastrowany indeks to zrównoważone drzewo (b-Tree) stron. Jeśli strona, w której jest zapisywana, zawiera już wiersze z innej transakcji, te zapisy zostaną w pełni zarejestrowane. Jeśli jednak strona jest pusta, zapis na tej stronie zostanie zapisany w sposób minimalny.

## <a name="optimize-deletes"></a>Optymalizuj usunięcia

Usuwanie jest operacją w pełni zarejestrowana.  Jeśli konieczne jest usunięcie dużej ilości danych w tabeli lub partycji, często jest to bardziej zrozumiałe dla danych, które mają `SELECT` być przechowywane, które mogą być uruchamiane jako zarejestrowane minimalnie.  Aby wybrać dane, Utwórz nową tabelę z [CTAs](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).  Po utworzeniu Użyj [nazwy](/sql/t-sql/statements/rename-transact-sql?view=azure-sqldw-latest&preserve-view=true) , aby zamienić starą tabelę na nowo utworzoną tabelę.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimize-updates"></a>Optymalizowanie aktualizacji

Aktualizacja jest w pełni zarejestrowanej operacji.  Jeśli trzeba zaktualizować dużą liczbę wierszy w tabeli lub partycji, często może być dużo bardziej wydajna, aby użyć operacji zarejestrowanej minimalnie, takiej jak [CTAs](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) .

W poniższym przykładzie aktualizacja pełnej tabeli została przekonwertowane na CTAS, aby możliwe było minimalne rejestrowanie.

W takim przypadku do sprzedaży w tabeli należy ponownie dodać kwotę rabatu:

```sql
--Step 01. Create a new table containing the "Update".
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS
SELECT
    [ProductKey]  
,    [OrderDateKey]
,    [DueDateKey]  
,    [ShipDateKey]
,    [CustomerKey]
,    [PromotionKey]
,    [CurrencyKey]
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber]
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> Ponowne utworzenie dużych tabel może skorzystać z używania dedykowanych funkcji zarządzania obciążeniami puli SQL. Aby uzyskać więcej informacji, zobacz [klasy zasobów dla zarządzania obciążeniami](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="optimize-with-partition-switching"></a>Optymalizuj z przełączaniem do partycji

Jeśli jest to na przykład w przypadku modyfikacji o dużej skali wewnątrz [partycji tabeli](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), to zrozumiały jest wzorzec przełączania partycji. Jeśli modyfikacja danych jest istotna i obejmuje wiele partycji, a następnie iteracja na partycjach uzyskuje ten sam wynik.

Kroki prowadzące do przełączenia partycji są następujące:

1. Tworzenie pustej partycji out
2. Wykonaj operację "Update" jako CTAS
3. Przełącz istniejące dane do tabeli out
4. Przełącz nowe dane
5. Wyczyść dane

Aby jednak ułatwić zidentyfikowanie partycji do przełączenia, Utwórz następującą procedurę pomocnika.  

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id]
                                                AND i.[index_id]        = p.[index_id]
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id]
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Ta procedura maksymalizuje ponowne użycie kodu i utrzymuje przykład większej kompaktowania na dysku.

Poniższy kod ilustruje powyższe kroki, aby uzyskać procedurę przełączania pełnej partycji.

```sql
--Create a partitioned aligned empty table to switch out the data
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT
    [ProductKey]  
,    [OrderDateKey]
,    [DueDateKey]  
,    [ShipDateKey]
,    [CustomerKey]
,    [PromotionKey]
,    [CurrencyKey]
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber]
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Minimalizacja rejestrowania z małymi partiami

W przypadku dużych operacji modyfikacji danych warto podzielić operację na fragmenty lub partie, aby określić zakres jednostki pracy.

Poniższy kod jest przykładem roboczym. Rozmiar wsadu został ustawiony na wartość typu prostego, aby wyróżnić technikę. W rzeczywistości rozmiar wsadu byłby znacznie większy.

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Wskazówki dotyczące wstrzymywania i skalowania

Usługa Azure Synapse Analytics umożliwia [wstrzymywanie, wznawianie i skalowanie](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) dedykowanej puli SQL na żądanie. 

W przypadku wstrzymania lub skalowania dedykowanej puli SQL należy zrozumieć, że wszystkie transakcje związane z lotem są kończone natychmiast; powoduje wycofywanie wszelkich otwartych transakcji. 

Jeśli obciążenie wystawiło długotrwałą i niekompletną modyfikację danych przed operacją wstrzymania lub skalowania, należy to zrobić. To cofnięcie może mieć wpływ na czas oczekiwania na wstrzymanie lub skalowanie dedykowanej puli SQL. 

> [!IMPORTANT]
> Zarówno `UPDATE` , jak i `DELETE` są w pełni zarejestrowane operacje, więc operacje cofania/ponawiania mogą trwać znacznie dłużej niż równoważne operacje, które nie zostały zarejestrowane w sposób minimalny.

Najlepszym scenariuszem jest umożliwienie przeprowadzenia transakcji modyfikacji danych lotu przed zawstrzymywaniem lub skalowaniem dedykowanej puli SQL. Jednak ten scenariusz może nie zawsze być praktyczny. Aby zmniejszyć ryzyko długotrwałego wycofywania, należy wziąć pod uwagę jedną z następujących opcji:

* Ponownie Napisz długotrwałe operacje przy użyciu [CTAs](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)
* Przerwij operację do fragmentów; działanie na podzestawie wierszy

## <a name="next-steps"></a>Następne kroki

Zobacz [transakcje w dedykowanej puli SQL](develop-transactions.md) , aby dowiedzieć się więcej na temat poziomów izolacji i limitów transakcyjnych.  Aby zapoznać się z innymi najlepszymi rozwiązaniami, zobacz [najlepsze rozwiązania dotyczące puli SQL](best-practices-sql-pool.md).
