---
title: Korzystanie z opcji Grupuj według w programie Synapse SQL
description: Synapse SQL umożliwia tworzenie rozwiązań przez implementację różnych opcji Grupuj według.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: d160040620fcaa60a8adc955efcc54ac3ca2609e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321449"
---
# <a name="group-by-options-in-synapse-sql"></a>Grupowanie według opcji w Synapse SQL

Synapse SQL umożliwia tworzenie rozwiązań przez implementację różnych opcji Grupuj według. 

## <a name="what-group-by-does"></a>Co robi Grupuj według

Klauzula [Group by](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL agreguje dane do zestawu podsumowującego wierszy.

Pula SQL bezserwerowa obsługuje cały zakres opcji Grupuj według. Dedykowana Pula SQL obsługuje ograniczoną liczbę opcji Grupuj według.

## <a name="group-by-options-supported-in-dedicated-sql-pool"></a>Grupowanie według opcji obsługiwanych w dedykowanej puli SQL

Grupuj według zawiera pewne opcje, które nie obsługują dedykowanej puli SQL. Dostępne są następujące rozwiązania:

* Grupuj według z pakietem zbiorczym
* ZESTAWY GRUPUJĄCE
* Grupuj według modułu

### <a name="rollup-and-grouping-sets-options"></a>Opcje zestawu zbiorczego i grupowania

Najprostszą opcją jest użycie UNION ALL, aby wykonać pakiet zbiorczy, zamiast polegać na jawnej składni. Wynik jest dokładnie taki sam

Poniższy przykład używa instrukcji GROUP BY z opcją ROLLUP:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Przy użyciu pakietu zbiorczego poprzedni przykład żąda następujących agregacji:

* Kraj i region
* Kraj
* Suma końcowa

Aby zastąpić pakiet zbiorczy i zwrócić te same wyniki, można użyć klauzuli UNION ALL i jawnie określić wymagane agregacje:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Aby zastąpić zestawy grupowania, stosowana jest zasada przykładowa. Wystarczy utworzyć tylko sekcje UNION dla poziomów agregacji, które mają być wyświetlane.

### <a name="cube-options"></a>Opcje modułu

Można utworzyć grupę według modułu przy użyciu metody UNION ALL. Problem polega na tym, że kod może szybko stać się skomplikowany i nieporęczny. Aby rozwiązać ten problem, można użyć bardziej zaawansowanego podejścia.

Pierwszym krokiem jest zdefiniowanie elementu "Cube", który definiuje wszystkie poziomy agregacji, które chcemy utworzyć. Zwróć uwagę na SPRZĘŻENIe krzyżowe dwóch tabel pochodnych w miarę generowania wszystkich poziomów. Pozostała część kodu jest w trakcie formatowania.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Na poniższej ilustracji przedstawiono wyniki [CREATE TABLE jako wybrane](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true):

![Grupuj według modułu](./media/develop-group-by-options/develop-group-by-cube.png)

Drugim krokiem jest określenie tabeli docelowej do przechowywania wyników pośrednich:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

Trzeci krok polega na przejściu w pętli względem modułu kolumn wykonujących agregację. Zapytanie zostanie uruchomione jednokrotnie dla każdego wiersza w #Cube tabeli tymczasowej. Wyniki są przechowywane w tabeli tymczasowej #Results:

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Na koniec można zwrócić wyniki, odczytując z tabeli tymczasowej #Results:

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Dzieląc kod na sekcje i generując konstrukcję zapętlenia, kod jest łatwiejsze do zarządzania i łatwość utrzymania.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](develop-overview.md).
