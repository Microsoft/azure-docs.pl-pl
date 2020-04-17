---
title: 'Samouczek: Przygotowanie danych do szkolenia modelu predykcyjnego w R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W pierwszej części tej trzyczęściowej serii samouczków przygotujesz dane z bazy danych SQL platformy Azure, aby wyszkolić model predykcyjny w języku R za pomocą usług Azure SQL Database Machine Learning Services (wersja zapoznawcza).
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
ms.openlocfilehash: bf69d2963c74723cb3fea542e28288e4f136d5c3
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458764"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Samouczek: Przygotowanie danych do uczenia modelu predykcyjnego w języku R za pomocą usług Azure SQL Database Machine Learning Services (wersja zapoznawcza)

W pierwszej części tej trzyczęściowej serii samouczków zaimportujesz i przygotujesz dane z bazy danych SQL platformy Azure przy użyciu języka R. W dalszej części tej serii użyjesz tych danych do uczenia i wdrażania modelu uczenia maszynowego predykcyjnego w języku R za pomocą usług Azure SQL Database Machine Learning Services (wersja zapoznawcza).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

W tej serii samouczków wyobraź sobie, że posiadasz wypożyczalnię sprzętu narciarskiego i chcesz przewidzieć liczbę wypożyczeń, które będziesz mieć w przyszłości. Te informacje pomogą Ci przygotować zapasy, personel i obiekty.

W części pierwszej i drugiej tej serii opracujesz niektóre skrypty języka R w witrynie RStudio, aby przygotować dane i wyszkolić model uczenia maszynowego. Następnie w części trzeciej uruchomisz te skrypty języka R w bazie danych SQL przy użyciu procedur przechowywanych.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Importowanie przykładowej bazy danych do bazy danych SQL platformy Azure przy użyciu języka R
> * Ładowanie danych z bazy danych SQL platformy Azure do ramki danych języka R
> * Przygotuj dane w R, identyfikując niektóre kolumny jako kategoryczne

W [części drugiej](sql-database-tutorial-predictive-model-build-compare.md)dowiesz się, jak tworzyć i szkolić wiele modeli uczenia maszynowego w językach R, a następnie wybrać najdokładniejszy.

W [części trzeciej](sql-database-tutorial-predictive-model-deploy.md)dowiesz się, jak przechowywać model w bazie danych, a następnie utworzyć procedury przechowywane ze skryptów języka R opracowanych w częściach 1 i 2. Procedury przechowywane będą uruchamiane w bazie danych SQL, aby prognozowania na podstawie nowych danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — jeśli nie masz subskrypcji platformy Azure, [utwórz konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

* [Usługa Azure SQL Database z włączonymi usługami uczenia maszynowego (z](sql-database-machine-learning-services-overview.md) włączoną funkcją R).

* RevoScaleR pakiet — zobacz [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) dla opcji, aby zainstalować ten pakiet lokalnie.

* R IDE - Ten poradnik używa [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Narzędzie do zapytań SQL — w tym samouczku przyjęto założenie, że używasz [usługi Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) lub programu SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importowanie przykładowej bazy danych

Przykładowy zestaw danych użyty w tym samouczku został zapisany w pliku kopii zapasowej bazy danych **.bacpac,** aby można było go pobrać i używać.

1. Pobierz plik [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Postępuj zgodnie ze wskazówkami w [polu Importuj plik BACPAC, aby utworzyć bazę danych SQL platformy Azure,](https://docs.microsoft.com/azure/sql-database/sql-database-import)korzystając z następujących szczegółów:

   * Importowanie z pobranego pliku **TutorialDB.bacpac**
   * Podczas publicznej wersji zapoznawczej wybierz konfigurację **Gen5/vCore** dla nowej bazy danych
   * Nazwij nową bazę danych "TutorialDB"

## <a name="load-the-data-into-a-data-frame"></a>Ładowanie danych do ramki danych

Aby użyć danych w języku R, należy załadować dane z bazy`rentaldata`danych SQL platformy Azure do ramki danych ( ).

Utwórz nowy plik RScript w RStudio i uruchom następujący skrypt. Zastąp **serwer**, **UID**i **PWD** własnymi informacjami o połączeniu.

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
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

W tej przykładowej bazie danych większość preparatu została już wykonana, ale w tym miejscu wykonasz jeszcze jedno przygotowanie.
Poniższy skrypt języka R służy do identyfikowania trzech kolumn jako *kategorii,* zmieniając typy danych na *współczynnik*.

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

Dane są teraz przygotowane do szkolenia.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz kontynuować tego samouczka, usuń bazę danych SamouczkDB z serwera usługi Azure SQL Database.

W witrynie Azure portal wykonaj następujące kroki:

1. Z menu po lewej stronie w witrynie Azure portal wybierz **pozycję Wszystkie zasoby** lub bazy danych **SQL**.
1. W polu **Filtruj według nazwy...** wprowadź **opcję TutorialDB**i wybierz subskrypcję.
1. Wybierz bazę danych SamouczkDB.
1. Na stronie **Przegląd** wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W pierwszej części tej serii samouczków wykonana została ta instrukcja:

* Importowanie przykładowej bazy danych do bazy danych SQL platformy Azure przy użyciu języka R
* Ładowanie danych z bazy danych SQL platformy Azure do ramki danych języka R
* Przygotuj dane w R, identyfikując niektóre kolumny jako kategoryczne

Aby utworzyć model uczenia maszynowego, który używa danych z bazy danych SamouczkDB, wykonaj część druga tej serii samouczków:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie modelu predykcyjnego w języku R za pomocą usług azure sql database machine learning services (wersja zapoznawcza)](sql-database-tutorial-predictive-model-build-compare.md)
