---
title: 'Samouczek: Przygotowywanie danych do przeprowadzenia klastrowania w języku R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W pierwszej części tej serii samouczków można przygotować dane z usługi Azure SQL Database w celu przeprowadzenia klastrowania w języku R z Azure SQL Database Machine Learning Services (wersja zapoznawcza).
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
ms.openlocfilehash: c06e1b13f87972cbcd50e888edf55158b77881d8
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053393"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Samouczek: Przygotowywanie danych do przeprowadzenia klastrowania w języku R z Azure SQL Database Machine Learning Services (wersja zapoznawcza)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W pierwszej części tej serii samouczków z trzema częściami zaimportujesz i przygotujesz dane z bazy danych Azure SQL Database przy użyciu języka R. W dalszej części tej serii będziesz używać tych danych do uczenia i wdrażania modelu klastrowania w języku R z Azure SQL Database Machine Learning Services (wersja zapoznawcza).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

*Klastrowanie* może być wyjaśnione jako organizacja danych w grupach, w których członkowie grupy są podobne w jakiś sposób.
Aby przeprowadzić klastrowanie klientów w zestawie danych zakupów i zwrotów produktów, należy użyć algorytmu **K-oznaczania** . Klienci korzystający z klastrów mogą skupić wysiłki marketingowe bardziej wydajnie, przeznaczoną dla konkretnych grup.
K-oznacza, że usługa klastrowania jest *nienadzorowanym algorytmem uczenia* , który szuka wzorców danych na podstawie podobieństw.

W części jednej i dwóch tej serii utworzysz skrypty języka R w programie RStudio, aby przygotować dane i szkolić model uczenia maszynowego. Następnie w części trzeciej można uruchamiać te skrypty języka R w bazie danych SQL przy użyciu procedur składowanych.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
>
> * Importowanie przykładowej bazy danych do Azure SQL Database
> * Oddzielni klienci i różne wymiary przy użyciu języka R
> * Ładowanie danych z bazy danych Azure SQL Database do ramki danych języka R

W [części](clustering-model-build-tutorial.md)drugiej dowiesz się, jak utworzyć i szkolić model klastra K-oznaczania w języku R.

W [trzeciej części](clustering-model-deploy-tutorial.md)dowiesz się, jak utworzyć procedurę przechowywaną w bazie danych Azure SQL Database, która może wykonywać klastry w języku R na podstawie nowych danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz konto](https://azure.microsoft.com/free/) .

* [Azure SQL Database z włączonym Machine Learning Services (z R)](machine-learning-services-overview.md) .

* Pakiet kolekcję funkcji revoscaler-zobacz [kolekcję funkcji revoscaler](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) dla opcji, aby zainstalować ten pakiet lokalnie.

* Środowisko IDE języka R — w tym samouczku jest stosowany [pulpit RStudio](https://www.rstudio.com/products/rstudio/download/).

* Narzędzie zapytania SQL — w tym samouczku przyjęto założenie, że używasz [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) lub [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importowanie przykładowej bazy danych

Przykładowy zestaw danych używany w tym samouczku został zapisany w pliku kopii zapasowej bazy danych **. BACPAC** do pobrania i użycia. Ten zestaw danych pochodzi z zestawu danych [TPCX-BB](http://www.tpc.org/tpcx-bb/default.asp) dostarczonego przez [Radę wydajności przetwarzania transakcji (TPC)](http://www.tpc.org/default.asp).

1. Pobierz plik [tpcxbb_1gb. BACPAC](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Postępuj zgodnie z instrukcjami w temacie [Importowanie pliku BACPAC, aby utworzyć bazę danych Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-import)przy użyciu następujących szczegółów:

   * Importuj z pobranego pliku **tpcxbb_1gb. BACPAC**
   * W publicznej wersji zapoznawczej wybierz konfigurację **5 rdzeń/rdzeń wirtualny** dla nowej bazy danych.
   * Nazwa nowej bazy danych "tpcxbb_1gb"

## <a name="separate-customers"></a>Oddzieli klienci

Utwórz nowy plik RScript w RStudio i uruchom następujący skrypt.
W zapytaniu SQL są oddzielani klienci według następujących wymiarów:

* **orderRatio** = współczynnik zwrotnego zamówienia (całkowita liczba zamówień częściowo lub w pełni zwróconych w stosunku do łącznej liczby zamówień)
* **itemsRatio** = współczynnik elementu powrotu (całkowita liczba zwracanych elementów w porównaniu z liczbą zakupionych elementów)
* **monetaryRatio** = współczynnik kwoty zwrotu (całkowita kwota pieniężna zwróconych elementów w porównaniu z zakupioną ilością)
* **częstotliwość** = częstotliwość powrotu

W funkcji **wklejania** Zastąp wartość **Server**, **UID**i **PWD** własnymi informacjami o połączeniu.

```r
# Define the connection string to connect to the tpcxbb_1gb database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Logical SQL server>",
               "; Database=tpcxbb_1gb",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");


#Define the query to select data from SQL Server
input_query <- "
SELECT ss_customer_sk AS customer
    ,round(CASE
            WHEN (
                       (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio
    ,round(CASE
            WHEN (
                     (orders_items = 0)
                  OR (returns_items IS NULL)
                  OR (orders_items IS NULL)
                  OR ((returns_items / orders_items) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio
    ,round(CASE
            WHEN (
                     (orders_money = 0)
                  OR (returns_money IS NULL)
                  OR (orders_money IS NULL)
                  OR ((returns_money / orders_money) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio
    ,round(CASE
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
"
```

## <a name="load-the-data-into-a-data-frame"></a>Załaduj dane do ramki danych

Teraz użyj poniższego skryptu, aby zwrócić wyniki zapytania do ramki danych języka R przy użyciu funkcji **rxSqlServerData** .
W ramach procesu zdefiniujesz typ wybranych kolumn (przy użyciu colClasses), aby upewnić się, że typy są prawidłowo przenoszone do języka R.

```r
# Query SQL Server using input_query and get the results back
# to data frame customer_returns
# Define the types for selected columns (using colClasses),
# to make sure that the types are correctly transferred to R
customer_returns <- rxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);

# Transform the data from an input dataset to an output dataset
customer_data <- rxDataStep(customer_returns);

# Take a look at the data just loaded from SQL Server
head(customer_data, n = 5);
```

Powinny być widoczne wyniki podobne do następujących.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

***Jeśli nie chcesz kontynuować pracy z tym samouczkiem***, Usuń bazę danych tpcxbb_1gb z serwera.

W Azure Portal wykonaj następujące kroki:

1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** lub **bazy danych SQL**.
1. W polu **Filtruj według nazwy...** wprowadź **tpcxbb_1gb**i wybierz swoją subskrypcję.
1. Wybierz bazę danych **tpcxbb_1gb** .
1. Na stronie **Przegląd** wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W pierwszej części tej serii samouczków zostały wykonane następujące czynności:

* Importowanie przykładowej bazy danych do usługi Azure SQL Database
* Oddzielni klienci i różne wymiary przy użyciu języka R
* Ładowanie danych z bazy danych Azure SQL Database do ramki danych języka R

Aby utworzyć model uczenia maszynowego, który używa tych danych klienta, wykonaj dwie części tej serii samouczków:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie modelu predykcyjnego w języku R z Azure SQL Database Machine Learning Services (wersja zapoznawcza)](clustering-model-build-tutorial.md)
