---
title: 'Samouczek: Tworzenie modelu klastrowania w R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W drugiej części tej trzyczęściowej serii samouczków skompilujesz model K-Means do wykonywania klastrowania w języku R za pomocą usług Azure SQL Database Machine Learning Services (wersja zapoznawcza).
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
ms.openlocfilehash: ebea6117420ee6de67025dfd4cfba71e905cb9ec
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453101"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Samouczek: Tworzenie modelu klastrowania w języku R za pomocą usług azure sql database machine learning services (wersja zapoznawcza)

W drugiej części tej trzyczęściowej serii samouczków zbudujemy model K-Means w R do wykonywania klastrowania. W następnej części tej serii wdrożysz ten model w bazie danych SQL za pomocą usług Azure SQL Database Machine Learning Services (wersja zapoznawcza).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Definiowanie liczby klastrów dla algorytmu K-Means
> * Wykonywanie klastrowania
> * Analiza wyników

W [części 1](sql-database-tutorial-clustering-model-prepare-data.md), dowiesz się, jak przygotować dane z bazy danych SQL platformy Azure do wykonywania klastrowania.

W [części trzeciej](sql-database-tutorial-clustering-model-deploy.md)dowiesz się, jak utworzyć procedurę składowaną w bazie danych SQL platformy Azure, która może wykonywać klastrowanie w języku R na podstawie nowych danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Część druga tego samouczka zakłada, że ukończono [**część pierwsza**](sql-database-tutorial-clustering-model-prepare-data.md) i jej wymagania wstępne.

## <a name="define-the-number-of-clusters"></a>Definiowanie liczby klastrów

Aby zgromadzenie danych klienta, użyjesz algorytmu klastrowania **K-Means,** jednego z najprostszych i najbardziej znanych sposobów grupowania danych.
Możesz przeczytać więcej o K-Means w [kompletny przewodnik po K oznacza algorytm klastrowania](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

Algorytm akceptuje dwa dane wejściowe: dane, a także wstępnie zdefiniowaną liczbę "*k*" reprezentującą liczbę klastrów do wygenerowania.
Dane wyjściowe *to* k klastry z danymi wejściowymi podzielonymi na partycje między klastrami.

Aby określić liczbę klastrów dla algorytmu do użycia, należy użyć wykresu w obrębie grupy sumy kwadratów, według liczby klastrów wyodrębnionych. Odpowiednia liczba klastrów do użycia jest na zgięciu lub "kolanie" wykresu.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Wykres łokciowy](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

Na podstawie wykresu, wygląda na *to, k = 4* byłoby dobrą wartość, aby spróbować. Ta wartość *k* pogrupuje klientów w cztery klastry.

## <a name="perform-clustering"></a>Wykonywanie klastrowania

W poniższym skrypcie języka R użyjesz funkcji **rxKmeans**, która jest k-oznacza funkcja w pakiecie RevoScaleR.

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in SQL database
# called return_cluster
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters=4,
                   outFile=return_cluster,
                   outColName="cluster",
                   extraVarsToWrite=c("customer"),
                   overwrite=TRUE);

# Read the customer returns cluster table from the database
customer_cluster <- rxDataStep(return_cluster);
```

## <a name="analyze-the-results"></a>Analiza wyników

Teraz, gdy już się klastrowanie przy użyciu K-Means, następnym krokiem jest przeanalizowanie wyniku i sprawdzić, czy można znaleźć żadnych informacji zasysanych.

**Clust** obiekt zawiera wyniki z K-Oznacza klastrowania.

```r
#Look at the clustering details to analyze results
clust
```

```results
Call:
rxKmeans(formula = ~orderRatio + itemsRatio + monetaryRatio + 
    frequency, data = customer_returns, outFile = return_cluster, 
    outColName = "cluster", extraVarsToWrite = c("customer"), 
    overwrite = TRUE, numClusters = 4)
Data: customer_returns
Number of valid observations: 37336
Number of missing observations: 0 
Clustering algorithm:  

K-means clustering with 4 clusters of sizes 31675, 671, 2851, 2139
Cluster means:
   orderRatio   itemsRatio monetaryRatio frequency
1 0.000000000 0.0000000000    0.00000000  0.000000
2 0.007451565 0.0000000000    0.04449653  4.271237
3 1.008067345 0.2707821817    0.49515232  1.031568
4 0.000000000 0.0004675082    0.10858272  1.186068
Within cluster sum of squares by cluster:
         1          2          3          4
    0.0000  1329.0160 18561.3157   363.2188
```

Cztery środki klastra są podane przy użyciu zmiennych zdefiniowanych w [części pierwsza:](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers)

* *orderRatio* = współczynnik zamówienia zwrotu (całkowita liczba zamówień częściowo lub w pełni zwróconych w porównaniu z całkowitą liczbą zamówień)
* *itemsRatio* = stosunek zwrotu towaru (całkowita liczba zwróconych towarów w porównaniu z liczbą zakupionych przedmiotów)
* *monetaryRatio* = współczynnik kwoty zwrotu (całkowita kwota pieniężna zwróconych towarów w porównaniu z zakupioną kwotą)
* *częstotliwość* = częstotliwość powrotu

Wyszukiwanie danych przy użyciu K-Means często wymaga dalszej analizy wyników i dalszych kroków, aby lepiej zrozumieć każdy klaster, ale może zapewnić kilka dobrych potencjalnych klientów.
Oto kilka sposobów interpretacji tych wyników:

* Klaster 1 (największy klaster) wydaje się być grupą klientów, którzy nie są aktywni (wszystkie wartości są zerowe).
* Klaster 3 wydaje się być grupą, która wyróżnia się pod względem zachowania powrotu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

***Jeśli nie zamierzasz kontynuować tego samouczka,*** usuń tpcxbb_1gb bazę danych z serwera usługi Azure SQL Database.

W witrynie Azure portal wykonaj następujące kroki:

1. Z menu po lewej stronie w witrynie Azure portal wybierz **pozycję Wszystkie zasoby** lub bazy danych **SQL**.
1. W polu **Filtruj według nazwy...** wprowadź **tpcxbb_1gb**i wybierz subskrypcję.
1. Wybierz **bazę danych tpcxbb_1gb.**
1. Na stronie **Przegląd** wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W drugiej części tej serii samouczków wykonana została ta instrukcja:

* Definiowanie liczby klastrów dla algorytmu K-Means
* Wykonywanie klastrowania
* Analiza wyników

Aby wdrożyć utworzony model uczenia maszynowego, wykonaj część trzecia tej serii samouczków:

> [!div class="nextstepaction"]
> [Samouczek: Wdrażanie modelu klastrowania w języku R za pomocą usług azure sql database machine learning services (wersja zapoznawcza)](sql-database-tutorial-clustering-model-deploy.md)