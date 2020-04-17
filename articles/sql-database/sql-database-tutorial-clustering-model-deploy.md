---
title: 'Samouczek: Wdrażanie modelu klastrowania w r'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W trzeciej części tej trzyczęściowej serii samouczków wdrożysz model klastrowania w języku R za pomocą usług Azure SQL Database Machine Learning Services (wersja zapoznawcza).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 07/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ef478246108d40a0c97d7dab03ecf1e5b474410b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452897"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Samouczek: Wdrażanie modelu klastrowania w języku R za pomocą usług azure sql database machine learning services (wersja zapoznawcza)

W trzeciej części tej trzyczęściowej serii samouczków wdrożysz model klastrowania opracowany w języku R w bazie danych SQL przy użyciu usług azure sql database machine learning services (wersja zapoznawcza).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Utworzysz procedurę składowaną z osadzonym skryptem języka R, który wykonuje klastrowanie. Ponieważ model jest wykonywany w bazie danych SQL platformy Azure, można łatwo trenować z danymi przechowywanymi w bazie danych.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Tworzenie procedury składowanej, która generuje model
> * Wykonywanie klastrowania w bazie danych SQL
> * Korzystanie z informacji klastrowania

W [części 1](sql-database-tutorial-clustering-model-prepare-data.md), dowiesz się, jak przygotować dane z bazy danych SQL platformy Azure do wykonywania klastrowania.

W [części drugiej](sql-database-tutorial-clustering-model-build.md), dowiedziałeś się, jak utworzyć i trenować K-Means model klastrowania w R.

## <a name="prerequisites"></a>Wymagania wstępne

* Część trzecia tej serii samouczków zakłada, że ukończyłeś [**część pierwsza**](sql-database-tutorial-clustering-model-prepare-data.md) i [**część druga**](sql-database-tutorial-clustering-model-build.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Tworzenie procedury składowanej, która generuje model

Uruchom następujący skrypt T-SQL, aby utworzyć procedurę składowaną. Procedura odtwarza kroki opracowane w częściach pierwszej i drugiej z tej serii samouczków:

* klasyfikowanie klientów na podstawie ich historii zakupów i zwrotów
* generowanie czterech klastrów klientów przy użyciu algorytmu K-Means

Procedura przechowuje wynikowe mapowania klastra klientów w tabeli bazy danych **customer_return_clusters**.

```sql
USE [tpcxbb_1gb]
DROP PROC IF EXISTS generate_customer_return_clusters;
GO
CREATE procedure [dbo].[generate_customer_return_clusters]
AS
/*
  This procedure uses R to classify customers into different groups
  based on their purchase & return history.
*/
BEGIN
    DECLARE @duration FLOAT
    , @instance_name NVARCHAR(100) = @@SERVERNAME
    , @database_name NVARCHAR(128) = db_name()
-- Input query to generate the purchase history & return metrics
    , @input_query NVARCHAR(MAX) = N'
SELECT ss_customer_sk AS customer,
    round(CASE 
            WHEN (
                    (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio,
    round(CASE 
            WHEN (
                    (orders_items = 0)
                    OR (returns_items IS NULL)
                    OR (orders_items IS NULL)
                    OR ((returns_items / orders_items) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio,
    round(CASE 
            WHEN (
                    (orders_money = 0)
                    OR (returns_money IS NULL)
                    OR (orders_money IS NULL)
                    OR ((returns_money / orders_money) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio,
    round(CASE 
            WHEN (returns_count IS NULL)
                THEN 0.0
            ELSE returns_count
            END, 0) AS frequency
FROM (
    SELECT ss_customer_sk,
        -- return order ratio
        COUNT(DISTINCT (ss_ticket_number)) AS orders_count,
        -- return ss_item_sk ratio
        COUNT(ss_item_sk) AS orders_items,
        -- return monetary amount ratio
        SUM(ss_net_paid) AS orders_money
    FROM store_sales s
    GROUP BY ss_customer_sk
    ) orders
LEFT OUTER JOIN (
    SELECT sr_customer_sk,
        -- return order ratio
        count(DISTINCT (sr_ticket_number)) AS returns_count,
        -- return ss_item_sk ratio
        COUNT(sr_item_sk) AS returns_items,
        -- return monetary amount ratio
        SUM(sr_return_amt) AS returns_money
    FROM store_returns
    GROUP BY sr_customer_sk
    ) returned ON ss_customer_sk = sr_customer_sk
 '
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
# Define the connection string
connStr <- paste("Driver=SQL Server; Server=", instance_name,
               "; Database=", database_name,
               "; Trusted_Connection=true; ",
                  sep="" );

# Input customer data that needs to be classified.
# This is the result we get from the query.
customer_returns <- RxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);
# Output table to hold the customer cluster mappings
return_cluster = RxSqlServerData(table = "customer_return_clusters",
                                 connectionString = connStr);

# Set seed for random number generator for predictability
set.seed(10);

# Generate clusters using rxKmeans and output clusters to a table
# called "customer_return_clusters"
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters = 4,
                   outFile = return_cluster,
                   outColName = "cluster",
                   writeModelVars = TRUE ,
                   extraVarsToWrite = c("customer"),
                   overwrite = TRUE);
'
    , @input_data_1 = N''
    , @params = N'@instance_name nvarchar(100), @database_name nvarchar(128), @input_query nvarchar(max), @duration float OUTPUT'
    , @instance_name = @instance_name
    , @database_name = @database_name
    , @input_query = @input_query
    , @duration = @duration OUTPUT;
END;

GO
```

## <a name="perform-clustering-in-sql-database"></a>Wykonywanie klastrowania w bazie danych SQL

Teraz, po utworzeniu procedury składowanej, wykonaj następujący skrypt, aby wykonać klastrowanie.

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

Sprawdź, czy to działa i czy faktycznie mamy listę klientów i ich mapowania klastra.

```sql
--Select data from table customer_return_clusters
--to verify that the clustering data was loaded
SELECT TOP (5) *
FROM customer_return_clusters;
```

```result
cluster  customer  orderRatio  itemsRatio  monetaryRatio  frequency
1        29727     0           0           0              0
4        26429     0           0           0.041979       1
2        60053     0           0           0.065762       3
2        97643     0           0           0.037034       3
2        32549     0           0           0.031281       4
```

## <a name="use-the-clustering-information"></a>Korzystanie z informacji klastrowania

Ponieważ procedura klastrowania jest przechowywana w bazie danych, można wydajnie wykonywać klastrowanie danych klientów przechowywanych w tej samej bazie danych. Procedurę można wykonać za każdym razem, gdy dane klienta są aktualizowane i używać zaktualizowanych informacji klastrowania.

Załóżmy, że chcesz wysłać promocyjną wiadomość e-mail do klientów w klastrze 3, grupy, która ma bardziej aktywne zachowanie zwrotu (możesz zobaczyć, jak cztery klastry zostały opisane w [części drugiej).](sql-database-tutorial-clustering-model-build.md#analyze-the-results) Poniższy kod wybiera adresy e-mail klientów w klastrze 3.

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

Wartość **r.cluster** można zmienić, aby zwracać adresy e-mail dla klientów w innych klastrach.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu tego samouczka można usunąć tpcxbb_1gb bazę danych z serwera usługi Azure SQL Database.

W witrynie Azure portal wykonaj następujące kroki:

1. Z menu po lewej stronie w witrynie Azure portal wybierz **pozycję Wszystkie zasoby** lub bazy danych **SQL**.
1. W polu **Filtruj według nazwy...** wprowadź **tpcxbb_1gb**i wybierz subskrypcję.
1. Wybierz **bazę danych tpcxbb_1gb.**
1. Na stronie **Przegląd** wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W trzeciej części tej serii samouczków wykonana została ta instrukcja:

* Tworzenie procedury składowanej, która generuje model
* Wykonywanie klastrowania w bazie danych SQL
* Korzystanie z informacji klastrowania

Aby dowiedzieć się więcej na temat korzystania z języka R w usługach azure SQL Database Machine Learning Services (wersja zapoznawcza), zobacz:

* [Samouczek: Przygotowanie danych do uczenia modelu predykcyjnego w języku R za pomocą usług Azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-tutorial-predictive-model-prepare-data.md)
* [Pisanie zaawansowanych funkcji języka R w bazie danych SQL usługi Azure przy użyciu usług uczenia maszynowego (wersja zapoznawcza)](sql-database-machine-learning-services-functions.md)
* [Praca z danymi języka R i SQL w usługach azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-machine-learning-services-data-issues.md)
* [Dodawanie pakietu języka R do usług azure sql database machine learning services (wersja zapoznawcza)](sql-database-machine-learning-services-add-r-packages.md)
