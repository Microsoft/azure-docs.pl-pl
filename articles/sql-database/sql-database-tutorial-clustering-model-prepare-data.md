---
title: 'Samouczek: Przygotowanie danych do wykonywania klastrowania w r'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W pierwszej części tej trzyczęściowej serii samouczków przygotujesz dane z bazy danych SQL platformy Azure do wykonywania klastrowania w języku R za pomocą usług Azure SQL Database Machine Learning Services (wersja zapoznawcza).
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
ms.openlocfilehash: abe7d5ed1d4ba1308abde04aee32a3ea222456b8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452880"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Samouczek: Przygotowywanie danych do wykonywania klastrowania w języku R za pomocą usług azure sql database machine learning services (wersja zapoznawcza)

W pierwszej części tej trzyczęściowej serii samouczków zaimportujesz i przygotujesz dane z bazy danych SQL platformy Azure przy użyciu języka R. W dalszej części tej serii użyjesz tych danych do uczenia i wdrażania modelu klastrowania w języku R za pomocą usług Azure SQL Database Machine Learning Services (wersja zapoznawcza).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

*Klastrowanie* można wyjaśnić jako organizowanie danych w grupach, w których członkowie grupy są w jakiś sposób podobni.
Algorytm **K-Means** służy do wykonywania klastrowania klientów w zestawie danych zakupów i zwrotów produktów. Koncentrując klientów, możesz efektywniej skupić swoje działania marketingowe, kierując reklamy na określone grupy.
K-Oznacza klastrowanie jest *nienadzorowany* algorytm uczenia się, który wyszukuje wzorce w danych na podstawie podobieństw.

W części pierwszej i drugiej tej serii opracujesz niektóre skrypty języka R w witrynie RStudio, aby przygotować dane i wyszkolić model uczenia maszynowego. Następnie w części trzeciej uruchomisz te skrypty języka R w bazie danych SQL przy użyciu procedur przechowywanych.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Importowanie przykładowej bazy danych do bazy danych SQL platformy Azure
> * Oddzielaj odbiorców wzdłuż różnych wymiarów za pomocą języka R
> * Ładowanie danych z bazy danych SQL platformy Azure do ramki danych języka R

W [części drugiej](sql-database-tutorial-clustering-model-build.md)dowiesz się, jak utworzyć i wyszkolić model klastrowania K-Means w językach R.

W [części trzeciej](sql-database-tutorial-clustering-model-deploy.md)dowiesz się, jak utworzyć procedurę składowaną w bazie danych SQL platformy Azure, która może wykonywać klastrowanie w języku R na podstawie nowych danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

* [Usługa Azure SQL Database z włączonymi usługami uczenia maszynowego (z](sql-database-machine-learning-services-overview.md) włączoną funkcją R).

* RevoScaleR pakiet — zobacz [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) dla opcji, aby zainstalować ten pakiet lokalnie.

* R IDE - Ten poradnik używa [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Narzędzie do zapytań SQL — w tym samouczku przyjęto założenie, że używasz [usługi Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) lub programu SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importowanie przykładowej bazy danych

Przykładowy zestaw danych użyty w tym samouczku został zapisany w pliku kopii zapasowej bazy danych **.bacpac,** aby można było go pobrać i używać. Ten zestaw danych pochodzi z zestawu danych [tpcx-bb](http://www.tpc.org/tpcx-bb/default.asp) dostarczonego przez [Radę Wydajności Przetwarzania Transakcji (TPC).](http://www.tpc.org/default.asp)

1. Pobierz plik [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Postępuj zgodnie ze wskazówkami w [polu Importuj plik BACPAC, aby utworzyć bazę danych SQL platformy Azure,](https://docs.microsoft.com/azure/sql-database/sql-database-import)korzystając z następujących szczegółów:

   * Importuj z pobranego pliku **tpcxbb_1gb.bacpac**
   * Podczas publicznej wersji zapoznawczej wybierz konfigurację **Gen5/vCore** dla nowej bazy danych
   * Nazwij nową bazę danych "tpcxbb_1gb"

## <a name="separate-customers"></a>Oddzielni klienci

Utwórz nowy plik RScript w RStudio i uruchom następujący skrypt.
W kwerendzie SQL oddzielasz klientów następującymi wymiarami:

* **orderRatio** = współczynnik zamówienia zwrotu (całkowita liczba zamówień częściowo lub w pełni zwróconych w porównaniu z całkowitą liczbą zamówień)
* **itemsRatio** = stosunek zwrotu towaru (całkowita liczba zwróconych towarów w porównaniu z liczbą zakupionych przedmiotów)
* **monetaryRatio** = współczynnik kwoty zwrotu (całkowita kwota pieniężna zwróconych towarów w porównaniu z zakupioną kwotą)
* **częstotliwość** = częstotliwość powrotu

W funkcji **wklej** zastąp **Serwer**, **UID**i **PWD** własnymi informacjami o połączeniu.

```r
# Define the connection string to connect to the tpcxbb_1gb database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
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

## <a name="load-the-data-into-a-data-frame"></a>Ładowanie danych do ramki danych

Teraz użyj następującego skryptu, aby zwrócić wyniki z kwerendy do ramki danych R przy użyciu funkcji **rxSqlServerData.**
W ramach procesu zdefiniujesz typ dla wybranych kolumn (przy użyciu colClasses), aby upewnić się, że typy są poprawnie przenoszone do języka R.

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

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

***Jeśli nie zamierzasz kontynuować tego samouczka,*** usuń tpcxbb_1gb bazę danych z serwera usługi Azure SQL Database.

W witrynie Azure portal wykonaj następujące kroki:

1. Z menu po lewej stronie w witrynie Azure portal wybierz **pozycję Wszystkie zasoby** lub bazy danych **SQL**.
1. W polu **Filtruj według nazwy...** wprowadź **tpcxbb_1gb**i wybierz subskrypcję.
1. Wybierz **bazę danych tpcxbb_1gb.**
1. Na stronie **Przegląd** wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W pierwszej części tej serii samouczków wykonana została ta instrukcja:

* Importowanie przykładowej bazy danych do bazy danych SQL platformy Azure
* Oddzielaj odbiorców wzdłuż różnych wymiarów za pomocą języka R
* Ładowanie danych z bazy danych SQL platformy Azure do ramki danych języka R

Aby utworzyć model uczenia maszynowego, który używa tych danych klienta, wykonaj część druga tej serii samouczków:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie modelu predykcyjnego w języku R za pomocą usług azure sql database machine learning services (wersja zapoznawcza)](sql-database-tutorial-clustering-model-build.md)
