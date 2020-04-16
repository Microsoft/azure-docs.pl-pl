---
title: Optymalizacja transakcji pod kątem puli SQL
description: Dowiedz się, jak zoptymalizować wydajność kodu transakcyjnego w puli SQL (magazyn danych), minimalizując ryzyko długich wycofywania.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: d6902b2b076df86012cec6941be417ad0f0c7660
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428734"
---
# <a name="optimizing-transactions-in-sql-pool"></a>Optymalizacja transakcji w puli SQL

Dowiedz się, jak zoptymalizować wydajność kodu transakcyjnego w puli SQL, minimalizując ryzyko długich wycofywania.

## <a name="transactions-and-logging"></a>Transakcje i rejestrowanie

Transakcje są ważnym składnikiem aparatu relacyjnej bazy danych. Pula SQL używa transakcji podczas modyfikacji danych. Te transakcje mogą być jawne lub niejawne. Instrukcje POJEDYNCZE INSERT, UPDATE i DELETE są przykładami niejawnych transakcji. Jawne transakcje używają BEGIN TRAN, COMMIT TRAN lub ROLLBACK TRAN. Jawne transakcje są zazwyczaj używane, gdy wiele instrukcji modyfikacji muszą być powiązane ze sobą w jednej jednostce atomowej.

Pula SQL zatwierdza zmiany w bazie danych przy użyciu dzienników transakcji. Każda dystrybucja ma swój własny dziennik transakcji. Zapisy dziennika transakcji są automatyczne. Nie jest wymagana konfiguracja. Jednak podczas gdy ten proces gwarantuje zapis wprowadza obciążenie w systemie. Można zminimalizować ten wpływ, pisząc transakcyjnie wydajny kod. Transakcyjnie skuteczny kod zasadniczo dzieli się na dwie kategorie.

* W miarę możliwości używaj minimalnych konstrukcji rejestrowania
* Przetwarzaj dane przy użyciu partii o określonym zakresie, aby uniknąć pojedynczych długotrwałych transakcji
* Przyjęcie wzorca przełączania partycji dla dużych modyfikacji danej partycji

## <a name="minimal-vs-full-logging"></a>Minimalne a pełne rejestrowanie

W przeciwieństwie do w pełni zalogowanych operacji, które używają dziennika transakcji do śledzenia każdej zmiany wiersza, minimalnie rejestrowane operacje śledzą tylko alokacje zasięgu i zmiany meta-danych. W związku z tym minimalne rejestrowanie obejmuje rejestrowanie tylko informacje, które są wymagane do wycofania transakcji po awarii lub jawnego żądania (ROLLBACK TRAN). Ponieważ znacznie mniej informacji jest śledzonych w dzienniku transakcji, minimalnie zarejestrowana operacja działa lepiej niż operacja o podobnej wielkości w pełni rejestrowane. Ponadto, ponieważ mniej zapisów przejść do dziennika transakcji, znacznie mniejsza ilość danych dziennika jest generowany i tak jest bardziej we/wy wydajne.

Limity bezpieczeństwa transakcji dotyczą tylko w pełni zarejestrowanych operacji.

> [!NOTE]
> Minimalnie rejestrowane operacje mogą uczestniczyć w jawnych transakcjach. Ponieważ wszystkie zmiany w strukturach alokacji są śledzone, możliwe jest wycofanie minimalnie zarejestrowanych operacji.

## <a name="minimally-logged-operations"></a>Minimalnie rejestrowane operacje

Następujące operacje mogą być rejestrowane minimalnie:

* UTWÓRZ TABELĘ JAKO WYBIERZ ([CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* Wstawić.. Wybierz
* CREATE INDEX
* ODBUDUJ INDEKS ZMIAN
* DROP INDEX
* OBCINANIE TABELI
* TABELA UPUSZCZANIA
* ZMIEŃ PARTYCJĘ PRZEŁĄCZNIKA TABELI

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> Limit bezpieczeństwa transakcji nie ma wpływu na wewnętrzne operacje przenoszenia danych (takie jak BROADCAST i SHUFFLE).

## <a name="minimal-logging-with-bulk-load"></a>Minimalne rejestrowanie przy obciążeniu masowym

CTAS i INSERT... SELECT są zarówno operacje obciążenia zbiorczego. Jednak oba są pod wpływem definicji tabeli docelowej i zależą od scenariusza obciążenia. W poniższej tabeli wyjaśniono, kiedy operacje masowe są rejestrowane w całości lub minimalnie:  

| Indeks podstawowy | Scenariusz obciążenia | Tryb rejestrowania |
| --- | --- | --- |
| Sterta |Dowolne |**Minimalny** |
| Indeks klastrowany |Pusta tabela docelowa |**Minimalny** |
| Indeks klastrowany |Załadowane wiersze nie nakładają się na istniejące strony w docelowych |**Minimalny** |
| Indeks klastrowany |Załadowane wiersze nakładają się na istniejące strony w docelowych |Pełne |
| Indeks klastrowanego magazynu kolumn |Rozmiar partii >= 102 400 na partycję wyrównaną dystrybucją |**Minimalny** |
| Indeks klastrowanego magazynu kolumn |Rozmiar partii < 102 400 na partycję wyrównaną dystrybucją |Pełne |

Warto zauważyć, że wszelkie zapisy do aktualizacji indeksów pomocniczych lub nieklastrowanych zawsze będą w pełni rejestrowane operacje.

> [!IMPORTANT]
> Pula SQL ma 60 dystrybucji. W związku z tym przy założeniu, że wszystkie wiersze są równomiernie rozłożone i lądowania w jednej partycji, partia będzie musiał zawierać 6,144,000 wierszy lub większe, aby być minimalnie rejestrowane podczas zapisywania do indeksu klastrowanego magazynu kolumn. Jeśli tabela jest podzielony na partycje i wiersze są wstawiane zakres granic partycji, a następnie trzeba będzie 6,144,000 wierszy na granicę partycji przy założeniu, że nawet dystrybucji danych. Każda partycja w każdej dystrybucji musi niezależnie przekraczać próg wiersza 102 400, aby wstawić było minimalnie zalogowane do dystrybucji.

Ładowanie danych do tabeli niepuste z indeksem klastrowanym często może zawierać mieszaninę wierszy w pełni rejestrowane i minimalnie rejestrowane. Indeks klastrowany jest zbilansowanym drzewem (b-drzewo) stron. Jeśli strona zapisywana jest już zawiera wiersze z innej transakcji, a następnie te zapisy będą w pełni rejestrowane. Jeśli jednak strona jest pusta, zapis na tej stronie będzie minimalnie rejestrowany.

## <a name="optimizing-deletes"></a>Optymalizacja usuwania

DELETE jest w pełni zarejestrowaną operacją.  Jeśli chcesz usunąć dużą ilość danych w tabeli lub partycji, `SELECT` często ma większy sens do danych, które chcesz zachować, które mogą być uruchamiane jako minimalnie rejestrowane operacji.  Aby wybrać dane, utwórz nową tabelę z [programem CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).  Po utworzeniu użyj [funkcji ZMIEŃ nazwę,](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) aby zamienić starą tabelę na nowo utworzoną tabelę.

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

## <a name="optimizing-updates"></a>Optymalizacja aktualizacji

UPDATE jest w pełni zarejestrowaną operacją.  Jeśli trzeba zaktualizować dużą liczbę wierszy w tabeli lub partycji, często może być znacznie bardziej wydajne użycie minimalnie rejestrowane operacji, takich jak [CTAS,](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) aby to zrobić.

W poniższym przykładzie pełna aktualizacja tabeli została przekonwertowana na CTAS, dzięki czemu możliwe jest minimalne rejestrowanie.

W takim przypadku retrospektywnie dodajemy kwotę rabatu do sprzedaży w tabeli:

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
> Ponowne tworzenie dużych tabel może korzystać z funkcji zarządzania obciążeniem puli SQL. Aby uzyskać więcej informacji, zobacz [Klasy zasobów do zarządzania obciążeniem](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="optimizing-with-partition-switching"></a>Optymalizacja za pomocą przełączania partycji

W przypadku modyfikacji na dużą skalę wewnątrz [partycji tabeli,](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)to wzorzec przełączania partycji ma sens. Jeśli modyfikacja danych jest znacząca i obejmuje wiele partycji, iteracja za pośrednictwem partycji osiąga ten sam wynik.

Kroki wykonywania przełącznika partycji są następujące:

1. Tworzenie pustej partycji
2. Wykonaj "update" jako CTAS
3. Przełączanie istniejących danych do tabeli out
4. Przełączanie nowych danych
5. Czyszczenie danych

Jednak aby ułatwić identyfikację partycji do przełączania, należy utworzyć następującą procedurę pomocnika.  

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

Ta procedura maksymalizuje ponowne użycie kodu i utrzymuje przykład przełączania partycji bardziej kompaktowy.

Poniższy kod demonstruje kroki wymienione wcześniej, aby osiągnąć pełną procedurę przełączania partycji.

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

## <a name="minimize-logging-with-small-batches"></a>Minimalizowanie rejestrowania za pomocą małych partii

W przypadku operacji modyfikacji dużych danych może mieć sens dzielenie operacji na fragmenty lub partie w celu zakresu jednostki pracy.

Poniższy kod jest przykładem roboczym. Rozmiar partii został ustawiony na trywialną liczbę, aby wyróżnić technikę. W rzeczywistości wielkość partii byłaby znacznie większa.

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

Usługa Azure Synapse Analytics umożliwia [wstrzymanie, wznowienie i skalowanie](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) puli SQL na żądanie. 

Po wstrzymaniu lub skalowaniu puli SQL ważne jest, aby zrozumieć, że wszelkie transakcje w locie są natychmiast zakończone; powoduje, że wszelkie otwarte transakcje mają zostać wycofane. 

Jeśli obciążenie zostało wystawione długo działa i niekompletne modyfikacji danych przed wstrzymanie lub skalowania operacji, a następnie tej pracy należy cofnąć. To cofanie może mieć wpływ na czas potrzebny do wstrzymania lub skalowania puli SQL. 

> [!IMPORTANT]
> Zarówno `UPDATE` `DELETE` i są w pełni rejestrowane operacje, a więc te operacje cofania/ponawiania może trwać znacznie dłużej niż równoważne minimalnie rejestrowane operacje.

Najlepszym scenariuszem jest umożliwienie transakcji modyfikacji danych lotu przed wstrzymaniem lub skalowaniem puli SQL. Jednak ten scenariusz nie zawsze może być praktyczne. Aby zmniejszyć ryzyko długotrwałego wycofywania, należy wziąć pod uwagę jedną z następujących opcji:

* Przepisz długotrwałe operacje przy użyciu [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)
* Podziel operację na kawałki; pracy na podzbiorze wierszy

## <a name="next-steps"></a>Następne kroki

Zobacz [Transakcje w puli SQL,](develop-transactions.md) aby dowiedzieć się więcej o poziomach izolacji i limitach transakcyjnych.  Aby zapoznać się z innymi najlepszymi rozwiązaniami, zobacz [Najważniejsze wskazówki dotyczące puli SQL](best-practices-sql-pool.md).
