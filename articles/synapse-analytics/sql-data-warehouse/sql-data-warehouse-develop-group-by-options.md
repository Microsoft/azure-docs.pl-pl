---
title: Korzystanie z opcji grupowanie według
description: Porady dotyczące implementowania grupy według opcji w puli języka SQL Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5d8d4c6d47e33ca365415542c2da9779b4d7d1dd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416193"
---
# <a name="group-by-options-in-synapse-sql-pool"></a>Grupowanie według opcji w puli sql synapse

W tym artykule znajdziesz wskazówki dotyczące implementowania grupy według opcji w puli SQL.

## <a name="what-does-group-by-do"></a>Do czego działa GRUPA BY?

Klauzula [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL agreguje dane do zestawu podsumowań wierszy. GRUPA WEDŁUG ma kilka opcji, które nie obsługuje puli SQL. Te opcje mają obejścia, które są następujące:

* GRUPA WEDŁUG Z ROLLUP
* ZESTAWY GRUPOWANIA
* GRUPA WEDŁUG Z CUBE

## <a name="rollup-and-grouping-sets-options"></a>Opcje zestawów zestawienia i grupowania

Najprostszą opcją w tym miejscu jest użycie UNION ALL do wykonywania zestawienia, a nie polegania na jawnej składni. Wynik jest dokładnie taki sam.

Poniższy przykład przy użyciu instrukcji GROUP BY z opcją ROLLUP:

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

Przy użyciu funkcji ROLLUP w poprzednim przykładzie żąda następujących agregacji:

* Kraj i region
* Kraj
* Suma końcowa

Aby zastąpić rollup i zwrócić te same wyniki, można użyć UNION ALL i jawnie określić wymagane agregacje:

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

Aby zastąpić ZESTAWY GRUPOWANIA, stosuje się zasadę próbki. Wystarczy utworzyć wszystkie sekcje UNION dla poziomów agregacji, które mają być wyświetlane.

## <a name="cube-options"></a>Opcje modułu

Możliwe jest utworzenie GRUPY PRZEZ Z CUBE przy użyciu podejścia UNION ALL. Problem polega na tym, że kod może szybko stać się uciążliwe i nieporęczne. Aby rozwiązać ten problem, można użyć tego bardziej zaawansowanego podejścia.

Przy użyciu poprzedniego przykładu pierwszym krokiem jest zdefiniowanie "moduł", który definiuje wszystkie poziomy agregacji, które chcemy utworzyć.

Zanotuj CROSS JOIN dwóch tabel pochodnych, ponieważ generuje to wszystkie poziomy dla nas. Reszta kodu służy do formatowania:

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

![Grupowanie według kostki](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

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

Trzecim krokiem jest pętla nad naszym modułem kolumn wykonujących agregację. Kwerenda zostanie uruchomiony raz dla każdego wiersza w tabeli tymczasowej #Cube. Wyniki są przechowywane w tabeli temp #Results:

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

Na koniec można zwrócić wyniki, czytając z #Results tabeli tymczasowej:

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Rozbijając kod na sekcje i generowania konstrukcji pętli, kod staje się łatwiejsze w zarządzaniu i utrzymania.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej wskazówek dotyczących rozwoju, zobacz [omówienie rozwoju](sql-data-warehouse-overview-develop.md).
