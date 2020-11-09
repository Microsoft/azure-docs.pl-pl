---
title: Dokumentacja algorytmów i modułów
description: Dowiedz się więcej na temat modułów projektanta Azure Machine Learning, których można użyć do tworzenia własnych projektów uczenia maszynowego.
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/09/2020
ms.openlocfilehash: 89ad9aae7c0d01971bbcfc7e392cb9d455ef85cd
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376846"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Informacje o algorytmach & module dla projektanta Azure Machine Learning

Ta zawartość referencyjna zawiera informacje techniczne na temat każdego z algorytmów uczenia maszynowego i modułów dostępnych w programie Azure Machine Learning Designer.

Każdy moduł reprezentuje zestaw kodu, który może zostać uruchomiony niezależnie i wykonać zadanie uczenia maszynowego z uwzględnieniem wymaganych danych wejściowych. Moduł może zawierać określony algorytm lub wykonać zadanie, które jest ważne w uczeniu maszynowym, takie jak brakująca wartość zastępcza lub analiza statystyczna.

Aby uzyskać pomoc dotyczącą wybierania algorytmów, zobacz 
* [Jak wybrać algorytmy](../how-to-select-algorithms.md)
* [Arkusz Ściągawka algorytmu Azure Machine Learning](../algorithm-cheat-sheet.md)

> [!TIP]
> W każdym potoku projektanta można uzyskać informacje dotyczące określonego modułu. Wybierz link **Dowiedz się więcej** na karcie moduł po umieszczeniu wskaźnika na module na liście modułów lub w prawym okienku modułu.

## <a name="data-preparation-modules"></a>Moduły przygotowywania danych


| Funkcja | Opis | Moduł |
| --- |--- | --- |
| Dane wejściowe i wyjściowe | Przenieś dane ze źródeł w chmurze do potoku. Zapisuj wyniki lub dane pośrednie w usłudze Azure Storage, SQL Database lub Hive, a następnie uruchamiaj potok lub korzystaj z magazynu w chmurze, aby wymieniać dane między potokami.  | [Ręczne wprowadzanie danych](enter-data-manually.md) <br/> [Eksportuj dane](export-data.md) <br/> [Importuj dane](import-data.md) |
| Przekształcanie danych | Operacje na danych, które są unikatowe dla uczenia maszynowego, takie jak normalizowanie lub pakowania danych, Zmniejszanie liczby wymiarów i konwertowanie danych między różnymi formatami plików.| [Dodaj kolumny](add-columns.md) <br/> [Dodawanie wierszy](add-rows.md) <br/> [Stosowanie operacji matematycznej](apply-math-operation.md) <br/> [Stosowanie przekształcenia SQL](apply-sql-transformation.md) <br/> [Czyszczenie brakujących danych](clean-missing-data.md) <br/> [Obcinanie wartości](clip-values.md) <br/> [Konwertowanie na plik CSV](convert-to-csv.md) <br/> [Konwertowanie na zestaw danych](convert-to-dataset.md) <br/> [Konwertowanie na wartości wskaźnika](convert-to-indicator-values.md) <br/> [Edytowanie metadanych](edit-metadata.md) <br/> [Grupowanie danych w pojemniki](group-data-into-bins.md) <br/> [Łączenie danych](join-data.md) <br/> [Normalizowanie danych](normalize-data.md) <br/> [Partycjonowanie i próbkowanie](partition-and-sample.md)  <br/> [Usuwanie zduplikowanych wierszy](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Wybieranie przekształcenia kolumn](select-columns-transform.md) <br/> [Wybieranie kolumn w zestawie danych](select-columns-in-dataset.md) <br/> [Dzielenie danych](split-data.md) |
| Wybór funkcji | Wybierz podzestaw odpowiednich, przydatnych funkcji do użycia podczas tworzenia modelu analitycznego. | [Wybór funkcji oparty na filtrze](filter-based-feature-selection.md) <br/> [Ważność funkcji permutacji](permutation-feature-importance.md) |
| Funkcje statystyczne | Zapewniają szeroką gamę metod statystycznych związanych z nauką danych. | [Podsumowywanie danych](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Algorytmy uczenia maszynowego

| Funkcja | Opis | Moduł |
| --- |--- | --- |
| Regresja | Przewidywanie wartości. | [Regresja wzmocnionego drzewa decyzyjnego](boosted-decision-tree-regression.md) <br/> [Regresja lasu decyzyjnego](decision-forest-regression.md) <br/> [Szybka regresja kwantylowa lasu decyzyjnego](fast-forest-quantile-regression.md)  <br/> [Regresja liniowa](linear-regression.md)  <br/> [Regresja sieci neuronowej](neural-network-regression.md)  <br/> [Regresja Poissona](poisson-regression.md)  <br/>|
| Klastrowanie | Grupuj dane jednocześnie.| [Klastrowanie metodą k-średnich](k-means-clustering.md)
| Klasyfikacja | Przewidywanie klasy.  Wybierz jeden z dwuklasowego algorytmu lub algorytmów wieloklasowych.| [Wieloklasowe wzmocnione drzewo decyzyjne](multiclass-boosted-decision-tree.md) <br/> [Wieloklasowy las decyzyjny](multiclass-decision-forest.md) <br/> [Wieloklasowa regresja logistyczna](multiclass-logistic-regression.md)  <br/> [Wieloklasowa sieć neuronowa](multiclass-neural-network.md) <br/> [Jeden przeciw wszystkim — moduł wieloklasowy](one-vs-all-multiclass.md) <br/> [Jeden i jeden wieloklasowy](one-vs-one-multiclass.md) <br/>[Dwuklasowy uśredniony perceptron](two-class-averaged-perceptron.md) <br/>  [Two-Class Boosted Decision Tree (Dwuklasowe wzmocnione drzewo decyzyjne)](two-class-boosted-decision-tree.md)  <br/> [Dwuklasowy las decyzyjny](two-class-decision-forest.md) <br/>  [Dwuklasowa regresja logistyczna](two-class-logistic-regression.md) <br/> [Dwuklasowa sieć neuronowa](two-class-neural-network.md) <br/> [Dwuklasowa maszyna wektorów nośnych](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Moduły do kompilowania i oceniania modeli

| Funkcja | Opis | Moduł |
| --- |--- | --- |
| Uczenie modelu | Uruchom dane za pomocą algorytmu. |  [Trenowanie modelu klastrowania](train-clustering-model.md) <br/> [Trenowanie modelu](train-model.md) <br/> [Uczenie modelu Pytorch](train-pytorch-model.md) <br/> [Dostrajanie hiperparametrów modelu](tune-model-hyperparameters.md) |
| Ocenianie i ocenianie modelu | Zmierz dokładność nauczonego modelu. | [Stosowanie przekształcenia](apply-transformation.md) <br/> [Przypisywanie danych do klastrów](assign-data-to-clusters.md) <br/> [Krzyżowa weryfikacja modelu](cross-validate-model.md) <br/> [Ocena modelu](evaluate-model.md) <br/> [Ocenianie modelu obrazów](score-image-model.md) <br/> [Klasyfikacja modelu](score-model.md) |
| Język Python | Napisz kod i Osadź go w module, aby zintegrować Język Python z potokiem. | [Tworzenie modelu języka Python](create-python-model.md) <br/> [Wykonywanie skryptu w języku Python](execute-python-script.md) |
| Język R | Napisz kod i Osadź go w module, aby zintegrować język R z potokiem. | [Wykonywanie skryptu języka R](execute-r-script.md) |
| Analiza tekstu | Udostępniaj wyspecjalizowane narzędzia obliczeniowe służące do pracy z tekstem ze strukturą i bez struktury. |  [Konwertowanie słów na wektory](convert-word-to-vector.md) <br/> [Wyodrębnianie cech n-gramów z tekstu](extract-n-gram-features-from-text.md) <br/> [Tworzenie skrótów funkcji](feature-hashing.md) <br/> [Wstępne przetwarzanie tekstu](preprocess-text.md) <br/> [Alokacja ukrytej zmiennej Dirichleta](latent-dirichlet-allocation.md) <br/> [Ocenianie modelu Vowpal Wabbit](score-vowpal-wabbit-model.md) <br/> [Trenowanie modelu Vowpal Wabbit](train-vowpal-wabbit-model.md)|
| Przetwarzanie obrazów | Przetwarzanie wstępne danych obrazu i moduły powiązane z rozpoznawaniem obrazu. |  [Stosowanie transformacji obrazów](apply-image-transformation.md) <br/> [Konwertowanie na katalog obrazów](convert-to-image-directory.md) <br/> [Inicjowanie transformacji obrazów](init-image-transformation.md) <br/> [Dzielenie katalogu obrazów](split-image-directory.md) <br/> [Model DenseNet](densenet.md) <br/> [Model ResNet](resnet.md) |
| Zalecenie | Kompiluj modele rekomendacji. | [Ocena modułu poleceń](evaluate-recommender.md) <br/> [Wynik modułu poleceń SVD](score-svd-recommender.md) <br/> [Wyniki modułu poleceń Wide and Deep](score-wide-and-deep-recommender.md)<br/> [Trenowanie modułu poleceń SVD](train-SVD-recommender.md) <br/> [Trenowanie modułu poleceń Wide and Deep](train-wide-and-deep-recommender.md)|
| Wykrywanie anomalii | Tworzenie modeli wykrywania anomalii. | [Wykrywanie anomalii oparte na analizie głównych składowych](pca-based-anomaly-detection.md) <br/> [Trenowanie modelu wykrywania anomalii](train-anomaly-detection-model.md) |


## <a name="web-service"></a>Usługa internetowa

Dowiedz się więcej na temat [modułów usługi sieci Web](web-service-input-output.md) , które są niezbędne do wnioskowania w czasie rzeczywistym w programie Azure Machine Learning Designer.

## <a name="error-messages"></a>Komunikaty o błędach

Dowiedz się więcej o [komunikatach o błędach i kodach wyjątków](designer-error-codes.md) , które mogą wystąpić przy użyciu modułów w programie Azure Machine Learning Designer.

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Tworzenie modelu w projektancie w celu przewidywania cen Autokorekty](../tutorial-designer-automobile-price-train-score.md)
