---
title: 'Samouczek: Wdrażanie modelu predykcyjnego w R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W trzeciej części tego trzyczęściowego samouczka wdrożysz model predykcyjny w języku R za pomocą usług Azure SQL Database Machine Learning Services (wersja zapoznawcza).
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
ms.openlocfilehash: efcb73866c83dcc03b5db8b7b97f438fb3010511
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452812"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Samouczek: Wdrażanie modelu predykcyjnego w języku R za pomocą usług azure sql database machine learning services (wersja zapoznawcza)

W trzeciej części tego trzyczęściowego samouczka wdrożysz model predykcyjny opracowany w języku R w bazie danych SQL przy użyciu usług azure sql database machine learning services (wersja zapoznawcza).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Utworzysz procedurę składowaną z osadzonym skryptem języka R, który tworzy prognozy przy użyciu modelu. Ponieważ model jest wykonywany w bazie danych SQL platformy Azure, można łatwo trenować z danymi przechowywanymi w bazie danych.

W tym artykule, korzystając ze skryptów języka R opracowanych w częściach 1 i 2, dowiesz się, jak:

> [!div class="checklist"]
> * Tworzenie procedury składowanej, która generuje model uczenia maszynowego
> * Przechowywanie modelu w tabeli bazy danych
> * Tworzenie procedury składowanej, która sprawia, że prognozy przy użyciu modelu
> * Wykonywanie modelu z nowymi danymi

W [części 1](sql-database-tutorial-predictive-model-prepare-data.md), można dowiedzieć się, jak zaimportować przykładową bazę danych, a następnie przygotować dane, które mają być używane do szkolenia modelu predykcyjnego w R.

W [części drugiej](sql-database-tutorial-predictive-model-build-compare.md)nauczysz się tworzyć i szkolić wiele modeli uczenia maszynowego w językach R, a następnie wybierać najdokładniejszy.

## <a name="prerequisites"></a>Wymagania wstępne

* Część trzecia tej serii samouczków zakłada, że ukończyłeś [**część pierwsza**](sql-database-tutorial-predictive-model-prepare-data.md) i [**część druga**](sql-database-tutorial-predictive-model-build-compare.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Tworzenie procedury składowanej, która generuje model

W drugiej części tej serii samouczków zdecydowano, że model drzewa decyzyjnego (dtree) jest najdokładniejszy. Teraz przy użyciu skryptów języka R, które`generate_rental_rx_model`zostały opracowane, należy utworzyć procedurę składowaną ( ), która trenuje i generuje model dtree przy użyciu rxDTree z pakietu RevoScaleR.

Uruchom następujące polecenia w usłudze Azure Data Studio lub SSMS.

```sql
-- Stored procedure that trains and generates an R model using the rental_data and a decision tree algorithm
DROP PROCEDURE IF EXISTS generate_rental_rx_model;
GO
CREATE PROCEDURE generate_rental_rx_model (@trained_model VARBINARY(max) OUTPUT)
AS
BEGIN
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

rental_train_data$Holiday <- factor(rental_train_data$Holiday);
rental_train_data$Snow    <- factor(rental_train_data$Snow);
rental_train_data$WeekDay <- factor(rental_train_data$WeekDay);

#Create a dtree model and train it using the training data set
model_dtree <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = rental_train_data);
#Serialize the model before saving it to the database table
trained_model <- as.raw(serialize(model_dtree, connection=NULL));
'
        , @input_data_1 = N'
            SELECT RentalCount
                 , Year
                 , Month
                 , Day
                 , WeekDay
                 , Snow
                 , Holiday
            FROM dbo.rental_data
            WHERE Year < 2015
            '
        , @input_data_1_name = N'rental_train_data'
        , @params = N'@trained_model varbinary(max) OUTPUT'
        , @trained_model = @trained_model OUTPUT;
END;
GO
```

## <a name="store-the-model-in-a-database-table"></a>Przechowywanie modelu w tabeli bazy danych

Utwórz tabelę w bazie danych SamouczkDB, a następnie zapisz model w tabeli.

1. Utwórz tabelę (`rental_rx_models`) do przechowywania modelu.

    ```sql
    USE TutorialDB;
    DROP TABLE IF EXISTS rental_rx_models;
    GO
    CREATE TABLE rental_rx_models (
          model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(MAX) NOT NULL
        );
    GO
    ```

1. Zapisz model w tabeli jako obiekt binarny, o nazwie modelu "rxDTree".

    ```sql
    -- Save model to table
    TRUNCATE TABLE rental_rx_models;
    
    DECLARE @model VARBINARY(MAX);
    
    EXECUTE generate_rental_rx_model @model OUTPUT;
    
    INSERT INTO rental_rx_models (
          model_name
        , model
        )
    VALUES (
         'rxDTree'
        , @model
        );
    
    SELECT *
    FROM rental_rx_models;
    ```

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Tworzenie procedury składowanej, która sprawia, że prognozy

Utwórz procedurę`predict_rentalcount_new`składowaną ( ), która sprawia, że prognoz przy użyciu modelu przeszkolonego i zestaw nowych danych.

```sql
-- Stored procedure that takes model name and new data as input parameters and predicts the rental count for the new data
DROP PROCEDURE IF EXISTS predict_rentalcount_new;
GO
CREATE PROCEDURE predict_rentalcount_new (
      @model_name VARCHAR(100)
    , @input_query NVARCHAR(MAX)
    )
AS
BEGIN
    DECLARE @rx_model VARBINARY(MAX) = (
            SELECT model
            FROM rental_rx_models
            WHERE model_name = @model_name
            );

    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

#Convert types to factors
rentals$Holiday <- factor(rentals$Holiday);
rentals$Snow    <- factor(rentals$Snow);
rentals$WeekDay <- factor(rentals$WeekDay);

#Before using the model to predict, we need to unserialize it
rental_model <- unserialize(rx_model);

#Call prediction function
rental_predictions <- rxPredict(rental_model, rentals);
'
        , @input_data_1 = @input_query
        , @input_data_1_name = N'rentals'
        , @output_data_1_name = N'rental_predictions'
        , @params = N'@rx_model varbinary(max)'
        , @rx_model = @rx_model
    WITH RESULT SETS(("RentalCount_Predicted" FLOAT));
END;
GO
```

## <a name="execute-the-model-with-new-data"></a>Wykonywanie modelu z nowymi danymi

Teraz możesz użyć procedury `predict_rentalcount_new` składowanej, aby przewidzieć liczbę wypożyczeń na podstawie nowych danych.

```sql
-- Use the predict_rentalcount_new stored procedure with the model name and a set of features to predict the rental count
EXECUTE dbo.predict_rentalcount_new @model_name = 'rxDTree'
    , @input_query = '
        SELECT CONVERT(INT,  3) AS Month
             , CONVERT(INT, 24) AS Day
             , CONVERT(INT,  4) AS WeekDay
             , CONVERT(INT,  1) AS Snow
             , CONVERT(INT,  1) AS Holiday
        ';
GO
```

Powinien zostać wyświetlony wynik podobny do następującego.

```results
RentalCount_Predicted
332.571428571429
```

Pomyślnie utworzono, przeszkolono i wdrożono model w bazie danych SQL platformy Azure. Następnie użyto tego modelu w procedurze składowanej do przewidywania wartości na podstawie nowych danych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu korzystania z bazy danych SamouczkDB, usuń go z serwera bazy danych SQL usługi Azure.

W witrynie Azure portal wykonaj następujące kroki:

1. Z menu po lewej stronie w witrynie Azure portal wybierz **pozycję Wszystkie zasoby** lub bazy danych **SQL**.
1. W polu **Filtruj według nazwy...** wprowadź **opcję TutorialDB**i wybierz subskrypcję.
1. Wybierz bazę danych SamouczkDB.
1. Na stronie **Przegląd** wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W trzeciej części tej serii samouczków wykonana została ta instrukcja:

* Tworzenie procedury składowanej, która generuje model uczenia maszynowego
* Przechowywanie modelu w tabeli bazy danych
* Tworzenie procedury składowanej, która sprawia, że prognozy przy użyciu modelu
* Wykonywanie modelu z nowymi danymi

Aby dowiedzieć się więcej na temat korzystania z języka R w usługach azure SQL Database Machine Learning Services (wersja zapoznawcza), zobacz:

* [Pisanie zaawansowanych funkcji języka R w bazie danych SQL usługi Azure przy użyciu usług uczenia maszynowego (wersja zapoznawcza)](sql-database-machine-learning-services-functions.md)
* [Praca z danymi języka R i SQL w usługach azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-machine-learning-services-data-issues.md)
* [Dodawanie pakietu języka R do usług azure sql database machine learning services (wersja zapoznawcza)](sql-database-machine-learning-services-add-r-packages.md)
