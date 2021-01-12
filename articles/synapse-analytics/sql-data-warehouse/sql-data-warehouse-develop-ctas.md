---
title: CREATE TABLE JAKO SELECT (CTAS)
description: Wyjaśnienie i przykłady CREATE TABLE jako instrukcji SELECT (CTAS) w Synapse SQL do tworzenia rozwiązań.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019, azure-synapse
ms.openlocfilehash: 95158193a978702392106c8aa3347de211fd1a3e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115450"
---
# <a name="create-table-as-select-ctas"></a>CREATE TABLE JAKO SELECT (CTAS)

W tym artykule wyjaśniono, CREATE TABLE jak SELECT (CTAS) instrukcja T-SQL w Synapse SQL na potrzeby tworzenia rozwiązań. Artykuł zawiera również przykłady kodu.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

Instrukcja [CREATE TABLE as SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAs) to jedna z najważniejszych dostępnych funkcji języka T-SQL. CTAS jest operacją równoległą, która tworzy nową tabelę na podstawie danych wyjściowych instrukcji SELECT. CTAS to najprostszy i najszybszy sposób tworzenia i wstawiania danych do tabeli za pomocą jednego polecenia.

## <a name="selectinto-vs-ctas"></a>Wybierz... DO programu vs. CTAS

CTAS jest bardziej dostosowywalną wersją elementu [SELECT... INTO w](/sql/t-sql/queries/select-into-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) instrukcji.

Poniżej znajduje się przykład prostego wyboru... PRZEKSZTAŁCA

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

Wybierz... W programie nie pozwala na zmianę metody dystrybucji lub typu indeksu w ramach operacji. Tworzysz przy `[dbo].[FactInternetSales_new]` użyciu domyślnego typu dystrybucji ROUND_ROBIN i domyślnej struktury tabeli klastrowanego indeksu magazynu kolumn.

Z CTAS, z drugiej strony, można określić zarówno dystrybucję danych tabeli, jak i typ struktury tabeli. Aby skonwertować poprzedni przykład do CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> Jeśli próbujesz zmienić indeks operacji CTAS, a tabela źródłowa jest dystrybuowana z mieszaniem, należy zachować tę samą kolumnę dystrybucji i typ danych. Pozwala to uniknąć przenoszenia danych między różnymi dystrybucjami podczas operacji, co jest bardziej wydajne.

## <a name="use-ctas-to-copy-a-table"></a>Używanie CTAS do kopiowania tabeli

Być może jednym z najczęściej używanych CTAS jest utworzenie kopii tabeli w celu zmiany kodu DDL. Załóżmy, że pierwotnie utworzono tabelę jako `ROUND_ROBIN` i chcesz ją zmienić na tabelę dystrybuowaną w kolumnie. CTAS to sposób zmiany kolumny dystrybucji. Można również użyć CTAS, aby zmienić typ partycjonowania, indeksowania lub kolumny.

Załóżmy, że utworzono tę tabelę przy użyciu domyślnego typu dystrybucji `ROUND_ROBIN` , a nie do określenia kolumny dystrybucji w `CREATE TABLE` .

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25));
```

Teraz chcesz utworzyć nową kopię tej tabeli przy użyciu programu `Clustered Columnstore Index` , aby można było korzystać z wydajności tabel klastrowanych magazynu kolumn. Warto również rozproszyć tę tabelę `ProductKey` , ponieważ oczekujesz sprzężeń w tej kolumnie i chcesz uniknąć przenoszenia danych podczas przyłączania `ProductKey` . Na koniec warto również dodać partycjonowanie `OrderDateKey` , dzięki czemu można szybko usunąć stare dane, usuwając stare partycje. Oto instrukcja CTAS, która kopiuje swoją starą tabelę do nowej tabeli.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Na koniec można zmienić nazwy tabel, aby zamienić je w nową tabelę, a następnie usunąć starą tabelę.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Użyj CTAS, aby obejść Nieobsługiwane funkcje

Możesz również użyć CTAS, aby obejść wiele nieobsługiwanych funkcji wymienionych poniżej. Ta metoda może często okazać się przydatna, ponieważ nie tylko kod będzie zgodny, ale często działa szybciej na Synapse SQL. Ta wydajność jest wynikiem w pełni równoległego projektu. Scenariusze obejmują:

* SPRZĘŻENIa ANSI na aktualizacjach
* Sprzężenia ANSI podczas usuwania
* MERGE — instrukcja

> [!TIP]
> Spróbuj myśleć o "CTAS First". Rozwiązanie problemu przy użyciu CTAS jest generalnie dobrym rozwiązaniem, nawet jeśli piszesz więcej danych w wyniku.

## <a name="ansi-join-replacement-for-update-statements"></a>Zamiana sprzężenia ANSI na potrzeby instrukcji UPDATE

Może się okazać, że masz złożoną aktualizację. Aktualizacja sprzęga więcej niż dwie tabele przy użyciu składni sprzężenia ANSI w celu przeprowadzenia aktualizacji lub usunięcia.

Wyobraź sobie, że musiałeś zaktualizować tę tabelę:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

Oryginalne zapytanie mogło wyglądać podobnie do tego przykładu:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

Synapse SQL nie obsługuje sprzężeń ANSI w `FROM` klauzuli `UPDATE` instrukcji, dlatego nie można użyć poprzedniego przykładu bez modyfikowania.

Aby zastąpić poprzedni przykład, można użyć kombinacji CTAS i niejawnego sprzężenia:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-merge"></a>Zamiana sprzężenia ANSI na potrzeby scalania 

W usłudze Azure Synapse Analytics [scalanie](/sql/t-sql/statements/merge-transact-sql?view=sql-server-ver15) (wersja zapoznawcza) z niezgodnym elementem docelowym wymaga, aby element docelowy był tabelą rozproszoną.  Użytkownicy mogą użyć SPRZĘŻENIa ANSI z opcją [Update](/sql/t-sql/queries/update-transact-sql?view=sql-server-ver15) lub [delete](/sql/t-sql/statements/delete-transact-sql?view=sql-server-ver15) jako obejścia, aby zmodyfikować dane tabeli docelowej na podstawie wyniku łączenia z inną tabelą.  Oto przykład:

```sql
CREATE TABLE dbo.Table1   
    (ColA INT NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
CREATE TABLE dbo.Table2   
    (ColA INT NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
INSERT INTO dbo.Table1 VALUES(1, 10.0);  
INSERT INTO dbo.Table2 VALUES(1, 0.0);  
GO  
UPDATE dbo.Table2   
SET dbo.Table2.ColB = dbo.Table2.ColB + dbo.Table1.ColB  
FROM dbo.Table2   
    INNER JOIN dbo.Table1   
    ON (dbo.Table2.ColA = dbo.Table1.ColA);  
GO  
SELECT ColA, ColB   
FROM dbo.Table2;

```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Jawny typ danych stanu i wartość zerowania danych wyjściowych

W przypadku migrowania kodu może się okazać, że można uruchomić ten typ wzorca kodowania:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

Możesz sądzić, że należy przeprowadzić migrację tego kodu do CTAS i poprawność. Jednak w tym miejscu występuje ukryty problem.

Następujący kod nie daje tego samego wyniku:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

Zwróć uwagę, że kolumna "result" przenosi typ danych i wartości null wyrażenia. Przekazywanie typu danych może prowadzić do delikatnych wariancji w wartości, jeśli nie jest to staranne.

Wypróbuj następujący przykład:

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

Wartość przechowywana dla wyniku jest inna. Ponieważ wartość PERSISTED w kolumnie wynik jest używana w innych wyrażeniach, błąd jest jeszcze bardziej znaczący.

![Zrzut ekranu przedstawiający wyniki CTAS](./media/sql-data-warehouse-develop-ctas/ctas-results.png)

Jest to ważne w przypadku migracji danych. Mimo że drugie zapytanie jest raczej bardziej dokładne, wystąpił problem. Dane będą się różnić w porównaniu z systemem źródłowym i które prowadzą do pytań dotyczących integralności w ramach migracji. Jest to jeden z tych rzadkich przypadków, gdy odpowiedź "niewłaściwa" jest w tej samej chwili!

Z powodu niejawnego rzutowania typów zostanie wyświetlona różnica między dwoma wynikami. W pierwszym przykładzie tabela definiuje definicję kolumny. Gdy wiersz zostanie wstawiony, wykonywana jest niejawna konwersja typu. W drugim przykładzie nie istnieje niejawna konwersja typu, ponieważ wyrażenie definiuje typ danych kolumny.

Zwróć również uwagę, że kolumna w drugim przykładzie została zdefiniowana jako kolumna dopuszczająca wartość null, podczas gdy pierwszy przykład nie ma. Gdy tabela została utworzona w pierwszym przykładzie, wartość zerowa kolumn została jawnie zdefiniowana. W drugim przykładzie został on pozostawiony do wyrażenia, a domyślnie spowoduje to zdefiniowanie wartości NULL.

Aby rozwiązać te problemy, należy jawnie ustawić konwersję typu i wartość null w ZAZNACZeniu części instrukcji CTAS. Nie można ustawić tych właściwości w "CREATE TABLE".
W poniższym przykładzie pokazano, jak naprawić kod:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

. Weź pod uwagę następujące kwestie:

* Można użyć CAST lub CONVERT.
* Aby wymusić wartość NULL, użyj ISNULL, a nie łączenia. Zobacz poniższą uwagę.
* ISNULL jest skrajną funkcją.
* Druga część ISNULL jest stałą, 0.

> [!NOTE]
> Aby można było poprawnie ustawić wartość null, należy użyć ISNULL, a nie łączenia. Połączenie nie jest funkcją deterministyczną i dlatego wynik wyrażenia zawsze będzie dopuszczany do wartości null. ISNULL jest inne. Jest to deterministyczne. W związku z tym, gdy druga część funkcji ISNULL jest stałą lub literałem, wartość wyników nie będzie RÓWNa NULL.

Upewnienie się, że integralność obliczeń jest również ważna dla przełączania partycji tabeli. Wyobraź sobie, że ta tabela została zdefiniowana jako tabela faktów:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

Jednak pole Kwota jest wyrażeniem obliczanym. Nie jest częścią danych źródłowych.

Aby utworzyć podzielony zestaw danych, możesz chcieć użyć następującego kodu:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Zapytanie będzie działać prawidłowo. Problem występuje podczas próby przełączenia przełącznika partycji. Definicje tabel nie są zgodne. Aby można było dopasować definicje tabeli, zmodyfikuj CTAS, aby dodać `ISNULL` funkcję, aby zachować atrybut wartości null kolumny.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Można zobaczyć, że zgodność typów i utrzymywanie właściwości wartości null na CTAS to najlepsze rozwiązanie inżynieryjne. Pomaga zachować integralność obliczeń, a także zapewnia możliwość przełączenia na partycje.

CTAS jest jednym z najważniejszych instrukcji w Synapse SQL. Zadbaj o to, aby dokładnie je zrozumieć. Zapoznaj się z [dokumentacją CTAs](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej porad programistycznych, zobacz [Omówienie programowania](sql-data-warehouse-overview-develop.md).