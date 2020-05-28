---
title: 'Samouczek: Przygotowywanie danych do uczenia modelu predykcyjnego w języku R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W pierwszej części tej serii samouczków można przygotować dane z usługi Azure SQL Database w celu uczenia modelu predykcyjnego w języku R z Azure SQL Database Machine Learning Services (wersja zapoznawcza).
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
ms.date: 07/26/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a82467a097c50314e8f26f4a5cc4507f867ad504
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053771"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Samouczek: Przygotowywanie danych do uczenia modelu predykcyjnego w języku R z Azure SQL Database Machine Learning Services (wersja zapoznawcza)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W pierwszej części tej serii samouczków z trzema częściami zaimportujesz i przygotujesz dane z bazy danych Azure SQL Database przy użyciu języka R. W dalszej części tej serii będziesz używać tych danych do uczenia i wdrożenia modelu uczenia maszynowego w języku R z Azure SQL Database Machine Learning Services (wersja zapoznawcza).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

W tej serii samouczków Wyobraź sobie, że masz swoją firmę do wypożyczalni i chcesz przewidzieć liczbę czynszów, które będziesz mieć w przyszłości. Te informacje pomogą Ci zapewnić, że Twoje zasoby, personel i udogodnienia są gotowe.

W części jednej i dwóch tej serii utworzysz skrypty języka R w programie RStudio, aby przygotować dane i szkolić model uczenia maszynowego. Następnie w części trzeciej można uruchamiać te skrypty języka R w bazie danych SQL przy użyciu procedur składowanych.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
>
> * Importowanie przykładowej bazy danych do bazy danych Azure SQL Database przy użyciu języka R
> * Ładowanie danych z bazy danych Azure SQL Database do ramki danych języka R
> * Przygotuj dane w języku R, identyfikując niektóre kolumny jako kategorii

W [drugiej części](predictive-model-build-compare-tutorial.md)znajdziesz informacje na temat tworzenia i uczenia wielu modeli uczenia maszynowego w języku R, a następnie wybrania najwyższej dokładności jednego.

W [trzeciej części](predictive-model-deploy-tutorial.md)dowiesz się, jak przechowywać model w bazie danych, a następnie tworzyć procedury składowane na podstawie skryptów języka R, które zostały opracowane w częściach jeden i dwa. Procedury składowane zostaną uruchomione w bazie danych SQL w celu przeprowadzenia prognoz na podstawie nowych danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz konto](https://azure.microsoft.com/free/) .

* [Azure SQL Database z włączonym Machine Learning Services (z R)](machine-learning-services-overview.md) .

* Pakiet kolekcję funkcji revoscaler-zobacz [kolekcję funkcji revoscaler](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) dla opcji, aby zainstalować ten pakiet lokalnie.

* Środowisko IDE języka R — w tym samouczku jest stosowany [pulpit RStudio](https://www.rstudio.com/products/rstudio/download/).

* Narzędzie zapytania SQL — w tym samouczku przyjęto założenie, że używasz [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) lub [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importowanie przykładowej bazy danych

Przykładowy zestaw danych używany w tym samouczku został zapisany w pliku kopii zapasowej bazy danych **. BACPAC** do pobrania i użycia.

1. Pobierz plik [TutorialDB. BACPAC](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Postępuj zgodnie z instrukcjami w temacie [Importowanie pliku BACPAC, aby utworzyć bazę danych Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-import)przy użyciu następujących szczegółów:

   * Importuj z pobranego pliku **TutorialDB. BACPAC**
   * W publicznej wersji zapoznawczej wybierz konfigurację **5 rdzeń/rdzeń wirtualny** dla nowej bazy danych.
   * Nazwa nowej bazy danych "TutorialDB"

## <a name="load-the-data-into-a-data-frame"></a>Załaduj dane do ramki danych

Aby użyć danych w języku R, załadujesz dane z bazy danych Azure SQL Database do ramki danych ( `rentaldata` ).

Utwórz nowy plik RScript w RStudio i uruchom następujący skrypt. Zastąp **serwer**, **UID**i **PWD** własnymi informacjami o połączeniu.

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Logical SQL server>",
               "; Database=TutorialDB",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");

#Get the data from the table
SQL_rentaldata <- RxSqlServerData(table = "dbo.rental_data", connectionString = connStr, returnDataFrame = TRUE);

#Import the data into a data frame
rentaldata <- rxImport(SQL_rentaldata);

#Take a look at the structure of the data and the top rows
head(rentaldata);
str(rentaldata);
```

Powinny być widoczne wyniki podobne do następujących.

```results
   Year  Month  Day  RentalCount  WeekDay  Holiday  Snow
1  2014    1     20      445         2        1      0
2  2014    2     13       40         5        0      0
3  2013    3     10      456         1        0      0
4  2014    3     31       38         2        0      0
5  2014    4     24       23         5        0      0
6  2015    2     11       42         4        0      0
'data.frame':       453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : num  2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : int  1 0 0 0 0 0 0 0 0 0 ...
$ Snow       : num  0 0 0 0 0 0 0 0 0 0 ...
```

## <a name="prepare-the-data"></a>Przygotowywanie danych

W tej przykładowej bazie danych większość przygotowań została już wykonana, ale w tym miejscu wykonasz jeszcze jedno przygotowanie.
Użyj poniższego skryptu języka R, aby zidentyfikować trzy kolumny jako *Kategorie* , zmieniając typy danych na *Factor*.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

Powinny być widoczne wyniki podobne do następujących.

```results
data.frame':      453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : Factor w/ 7 levels "1","2","3","4",..: 2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : Factor w/ 2 levels "0","1": 2 1 1 1 1 1 1 1 1 1 ...
$ Snow       : Factor w/ 2 levels "0","1": 1 1 1 1 1 1 1 1 1 1 ...
```

Dane są teraz przygotowywane do uczenia się.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz kontynuować pracy z tym samouczkiem, Usuń bazę danych TutorialDB z serwera.

W Azure Portal wykonaj następujące kroki:

1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** lub **bazy danych SQL**.
1. W polu **Filtruj według nazwy...** wpisz **TutorialDB**i wybierz swoją subskrypcję.
1. Wybierz bazę danych TutorialDB.
1. Na stronie **Przegląd** wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W pierwszej części tej serii samouczków zostały wykonane następujące czynności:

* Importowanie przykładowej bazy danych do bazy danych Azure SQL Database przy użyciu języka R
* Ładowanie danych z bazy danych Azure SQL Database do ramki danych języka R
* Przygotuj dane w języku R, identyfikując niektóre kolumny jako kategorii

Aby utworzyć model uczenia maszynowego, który używa danych z bazy danych TutorialDB, wykonaj dwie części tej serii samouczków:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie modelu predykcyjnego w języku R z Azure SQL Database Machine Learning Services (wersja zapoznawcza)](predictive-model-build-compare-tutorial.md)
