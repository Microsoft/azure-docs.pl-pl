---
title: Używanie opcji Grupuj według
description: Porady dotyczące implementowania opcji Grupuj według dla dedykowanych pul SQL w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3f0879aa9b6f9e084d0c51f0bb371740d333c1b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683260"
---
# <a name="group-by-options-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Grupuj według opcji dla dedykowanych pul SQL w usłudze Azure Synapse Analytics

W tym artykule znajdziesz wskazówki dotyczące implementowania opcji Grupuj według w dedykowanych pulach SQL.

## <a name="what-does-group-by-do"></a>Co robi Grupuj według?

Klauzula [Group by](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL agreguje dane do zestawu podsumowującego wierszy. Grupuj według zawiera pewne opcje, które nie obsługują dedykowanej puli SQL. Dostępne są następujące rozwiązania:

* Grupuj według z pakietem zbiorczym
* ZESTAWY GRUPUJĄCE
* Grupuj według modułu

## <a name="rollup-and-grouping-sets-options"></a>Opcje zestawu zbiorczego i grupowania

Najprostszą opcją jest użycie UNION ALL, aby wykonać pakiet zbiorczy, zamiast polegać na jawnej składni. Wynik jest dokładnie taki sam.

W poniższym przykładzie użyto instrukcji GROUP BY z opcją ROLLUP:

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

## <a name="cube-options"></a>Opcje modułu

Można utworzyć grupę według modułu przy użyciu metody UNION ALL. Problem polega na tym, że kod może szybko stać się skomplikowany i nieporęczny. Aby rozwiązać ten problem, można użyć bardziej zaawansowanego podejścia.

Korzystając z poprzedniego przykładu, pierwszym krokiem jest zdefiniowanie elementu "Cube", który definiuje wszystkie poziomy agregacji, które chcemy utworzyć.

Zwróć uwagę na SPRZĘŻENIe krzyżowe dwóch tabel pochodnych, ponieważ spowoduje to wygenerowanie wszystkich poziomów dla nas. Pozostała część kodu jest w trakcie formatowania:

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

Na poniższej ilustracji przedstawiono wyniki CTAS:

![Grupuj według modułu](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

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

Trzeci krok polega na zapętleniu względem naszego modułu kolumn wykonujących agregację. Zapytanie zostanie uruchomione jednokrotnie dla każdego wiersza w #Cube tabeli tymczasowej. Wyniki są przechowywane w tabeli tymczasowej #Results:

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

Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](sql-data-warehouse-overview-develop.md).
