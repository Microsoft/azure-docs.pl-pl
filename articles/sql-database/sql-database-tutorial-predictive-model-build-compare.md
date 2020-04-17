---
title: 'Samouczek: Trenuj i porównuj modele predykcyjne w R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W drugiej części tej trzyczęściowej serii samouczków utworzysz dwa modele predykcyjne w języku R za pomocą usług Azure SQL Database Machine Learning Services (wersja zapoznawcza), a następnie wybierz najdokładniejszy model.
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
ms.openlocfilehash: 0985b37280e3cd363ba1728a5ec33b0012611ab2
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452931"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Samouczek: Tworzenie modelu predykcyjnego w języku R za pomocą usług azure sql database machine learning services (wersja zapoznawcza)

W drugiej części tej trzyczęściowej serii samouczków utworzysz dwa modele predykcyjne w R i wybierzesz najdokładniejszy model. W następnej części tej serii wdrożysz ten model w bazie danych SQL za pomocą usług Azure SQL Database Machine Learning Services (wersja zapoznawcza).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Trenuj dwa modele uczenia maszynowego
> * 6.00.
> * Porównaj wyniki, aby wybrać najdokładniejszy model

W [części 1](sql-database-tutorial-predictive-model-prepare-data.md), można dowiedzieć się, jak zaimportować przykładową bazę danych, a następnie przygotować dane, które mają być używane do szkolenia modelu predykcyjnego w R.

W [części trzeciej](sql-database-tutorial-predictive-model-deploy.md)dowiesz się, jak przechowywać model w bazie danych, a następnie utworzyć procedury przechowywane ze skryptów języka R opracowanych w częściach 1 i 2. Procedury przechowywane będą uruchamiane w bazie danych SQL, aby prognozowania na podstawie nowych danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Część druga tego samouczka zakłada, że ukończono [**część pierwsza**](sql-database-tutorial-predictive-model-prepare-data.md) i jej wymagania wstępne.

## <a name="train-two-models"></a>Trenuj dwa modele

Aby znaleźć najlepszy model danych dotyczących wynajmu nart, utwórz dwa różne modele (regresja liniowa i drzewo decyzyjne) i zobacz, który z nich dokładniej przewiduje. Użyjesz ramki `rentaldata` danych utworzonej w pierwszej części tej serii.

```r
#First, split the dataset into two different sets:
# one for training the model and the other for validating it
train_data = rentaldata[rentaldata$Year < 2015,];
test_data  = rentaldata[rentaldata$Year == 2015,];

#Use the RentalCount column to check the quality of the prediction against actual values
actual_counts <- test_data$RentalCount;

#Model 1: Use rxLinMod to create a linear regression model, trained with the training data set
model_linmod <- rxLinMod(RentalCount ~  Month + Day + WeekDay + Snow + Holiday, data = train_data);

#Model 2: Use rxDTree to create a decision tree model, trained with the training data set
model_dtree  <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = train_data);
```

## <a name="make-predictions-from-both-models"></a>6.00.

Użyj funkcji predict, aby przewidzieć liczbę wypożyczeń przy użyciu każdego przeszkolonego modelu.

```r
#Use both models to make predictions using the test data set.
predict_linmod <- rxPredict(model_linmod, test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

predict_dtree  <- rxPredict(model_dtree,  test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

#To verify it worked, look at the top rows of the two prediction data sets.
head(predict_linmod);
head(predict_dtree);
```

```results
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1         27.45858          42       2     11     4      0       0
2        387.29344         360       3     29     1      0       0
3         16.37349          20       4     22     4      0       0
4         31.07058          42       3      6     6      0       0
5        463.97263         405       2     28     7      1       0
6        102.21695          38       1     12     2      1       0
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1          40.0000          42       2     11     4      0       0
2         332.5714         360       3     29     1      0       0
3          27.7500          20       4     22     4      0       0
4          34.2500          42       3      6     6      0       0
5         645.7059         405       2     28     7      1       0
6          40.0000          38       1     12     2      1       0
```

## <a name="compare-the-results"></a>Porównaj wyniki

Teraz chcesz zobaczyć, który z modeli daje najlepsze prognozy. Szybki i łatwy sposób, aby to zrobić, jest użycie podstawowej funkcji kreślenia, aby wyświetlić różnicę między rzeczywistymi wartościami w danych szkoleniowych a przewidywanymi wartościami.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![Porównanie dwóch modeli](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Wygląda na to, że model drzewa decyzyjnego jest dokładniejszy z dwóch modeli.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz kontynuować tego samouczka, usuń bazę danych SamouczkDB z serwera usługi Azure SQL Database.

W witrynie Azure portal wykonaj następujące kroki:

1. Z menu po lewej stronie w witrynie Azure portal wybierz **pozycję Wszystkie zasoby** lub bazy danych **SQL**.
1. W polu **Filtruj według nazwy...** wprowadź **opcję TutorialDB**i wybierz subskrypcję.
1. Wybierz bazę danych SamouczkDB.
1. Na stronie **Przegląd** wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W drugiej części tej serii samouczków wykonana została ta instrukcja:

* Trenuj dwa modele uczenia maszynowego
* 6.00.
* Porównaj wyniki, aby wybrać najdokładniejszy model

Aby wdrożyć utworzony model uczenia maszynowego, wykonaj część trzecia tej serii samouczków:

> [!div class="nextstepaction"]
> [Samouczek: Wdrażanie modelu predykcyjnego w języku R za pomocą usług azure sql database machine learning services (wersja zapoznawcza)](sql-database-tutorial-predictive-model-deploy.md)
