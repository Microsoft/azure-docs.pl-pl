---
title: 'Samouczek: uczenie i porównywanie modeli predykcyjnych w języku R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W drugiej części tej serii samouczków można utworzyć dwa modele predykcyjne w języku R z Azure SQL Database Machine Learning Services (wersja zapoznawcza), a następnie wybrać najbardziej precyzyjny model.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81452931"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Samouczek: Tworzenie modelu predykcyjnego w języku R z Azure SQL Database Machine Learning Services (wersja zapoznawcza)

W drugiej części tej serii samouczków z trzema częściami utworzysz dwa modele predykcyjne w języku R i wybierzemy najbardziej precyzyjny model. W następnej części tej serii ten model zostanie wdrożony w bazie danych SQL za pomocą Azure SQL Database Machine Learning Services (wersja zapoznawcza).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Uczenie dwóch modeli uczenia maszynowego
> * Tworzenie prognoz z obu modeli
> * Porównaj wyniki, aby wybrać najbardziej precyzyjny model

W [części pierwszej](sql-database-tutorial-predictive-model-prepare-data.md)przedstawiono sposób importowania przykładowej bazy danych, a następnie przygotowania danych do uczenia modelu predykcyjnego w języku R.

W [trzeciej części](sql-database-tutorial-predictive-model-deploy.md)dowiesz się, jak przechowywać model w bazie danych, a następnie tworzyć procedury składowane na podstawie skryptów języka R, które zostały opracowane w częściach jeden i dwa. Procedury składowane zostaną uruchomione w bazie danych SQL w celu przeprowadzenia prognoz na podstawie nowych danych.

## <a name="prerequisites"></a>Wymagania wstępne

* W drugiej części tego samouczka założono, że została wykonana [**część pierwszej**](sql-database-tutorial-predictive-model-prepare-data.md) i jej wymagania wstępne.

## <a name="train-two-models"></a>Uczenie dwóch modeli

Aby znaleźć najlepszy model dla danych o wypożyczeniu narciarskim, Utwórz dwa różne modele (regresja liniowa i drzewo decyzyjne) i sprawdź, który z nich jest bardziej dokładny. Będziesz używać ramki `rentaldata` danych utworzonej w pierwszej części tej serii.

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

## <a name="make-predictions-from-both-models"></a>Tworzenie prognoz z obu modeli

Użyj funkcji przewidywania, aby przewidzieć liczbę czynszów przy użyciu każdego z przeszkolonych modeli.

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

Teraz chcesz zobaczyć, które modele dają najlepsze przewidywania. Szybka i łatwa metoda to użycie podstawowej funkcji kreolenia do wyświetlania różnicy między wartościami rzeczywistymi danych szkoleniowych i prognozowanymi wartościami.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![Porównywanie dwóch modeli](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Wygląda na to, że model drzewa decyzyjnego jest dokładniejszy dla dwóch modeli.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz kontynuować pracy z tym samouczkiem, Usuń bazę danych TutorialDB z serwera Azure SQL Database.

W Azure Portal wykonaj następujące kroki:

1. Z menu po lewej stronie w Azure Portal wybierz pozycję **wszystkie zasoby** lub **bazy danych SQL**.
1. W polu **Filtruj według nazwy...** wpisz **TutorialDB**i wybierz swoją subskrypcję.
1. Wybierz bazę danych TutorialDB.
1. Na stronie **Przegląd** wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W drugiej części tej serii samouczków zostały wykonane następujące czynności:

* Uczenie dwóch modeli uczenia maszynowego
* Tworzenie prognoz z obu modeli
* Porównaj wyniki, aby wybrać najbardziej precyzyjny model

Aby wdrożyć utworzony model uczenia maszynowego, wykonaj trzecią część tej serii samouczków:

> [!div class="nextstepaction"]
> [Samouczek: Wdrażanie modelu predykcyjnego w języku R z Azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-tutorial-predictive-model-deploy.md)
