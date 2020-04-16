---
title: Dokumentacja algorytmów i modułów
description: Dowiedz się więcej o modułach dostępnych w projektancie usługi Azure Machine Learning (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 02/22/2020
ms.openlocfilehash: 7fcfc7c15f94d7b569629e53534d731126172260
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399058"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer-preview"></a>Odwołanie do modułu & algorytmu dla projektanta usługi Azure Machine Learning (wersja zapoznawcza)

Ta zawartość referencyjna zawiera tło techniczne dla każdego z algorytmów uczenia maszynowego i modułów dostępnych w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Każdy moduł reprezentuje zestaw kodu, który można uruchomić niezależnie i wykonać zadanie uczenia maszynowego, biorąc pod uwagę wymagane dane wejściowe. Moduł może zawierać określony algorytm lub wykonać zadanie, które jest ważne w uczeniu maszynowym, takie jak brak zastępowania wartości lub analizy statystycznej.

Aby uzyskać pomoc dotyczącą wyboru algorytmów, zobacz 
* [Jak wybrać algorytmy](../how-to-select-algorithms.md)
* [Arkusz kodu algorytmu usługi Azure Machine Learning](../algorithm-cheat-sheet.md)

> [!TIP]
> W dowolnym potoku w projektancie można uzyskać informacje o określonym module. Wybierz moduł, a następnie wybierz **łącze pomocy w** okienku Szybka **pomoc.**

## <a name="data-preparation-modules"></a>Moduły przygotowania danych


| Funkcjonalność | Opis | Moduł |
| --- |--- | --- |
| Wprowadzanie i wyprowadzanie danych | Przenoszenie danych ze źródeł chmury do potoku. Zapisz wyniki lub dane pośrednie w usłudze Azure Storage, bazie danych SQL lub gałęzi, podczas uruchamiania potoku lub użyj magazynu w chmurze do wymiany danych między potokami.  | [Ręczne wprowadzanie danych](enter-data-manually.md) <br/> [Eksportowanie danych](export-data.md) <br/> [Importowanie danych](import-data.md) |
| Transformacja danych | Operacje na danych, które są unikatowe dla uczenia maszynowego, takie jak normalizacji lub binning danych, zmniejszenie wymiarowości i konwersji danych między różnymi formatami plików.| [Dodawanie kolumn](add-columns.md) <br/> [Dodawanie wierszy](add-rows.md) <br/> [Stosowanie operacji matematycznej](apply-math-operation.md) <br/> [Stosowanie przekształcenia SQL](apply-sql-transformation.md) <br/> [Czyszczenie brakujących danych](clean-missing-data.md) <br/> [Obcinanie wartości](clip-values.md) <br/> [Konwertowanie na plik CSV](convert-to-csv.md) <br/> [Konwertowanie na zestaw danych](convert-to-dataset.md) <br/> [Konwertowanie na wartości wskaźnika](convert-to-indicator-values.md) <br/> [Edytowanie metadanych](edit-metadata.md) <br/> [Łączenie danych](join-data.md) <br/> [Normalizowanie danych](normalize-data.md) <br/> [Partycjonowanie i próbkowanie](partition-and-sample.md)  <br/> [Usuwanie zduplikowanych wierszy](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Wybieranie przekształcenia kolumn](select-columns-transform.md) <br/> [Wybieranie kolumn w zestawie danych](select-columns-in-dataset.md) <br/> [Spil Data (Podziel dane)](split-data.md) |
| Wybór funkcji | Wybierz podzbiór odpowiednich, przydatnych funkcji do użycia przy tworzeniu modelu analitycznego. | [Wybór funkcji oparty na filtrze](filter-based-feature-selection.md) <br/> [Ważność funkcji permutacji](permutation-feature-importance.md) |
| Funkcje statystyczne | Zapewnienie szerokiej gamy metod statystycznych związanych z nauką o danych. | [Podsumowywanie danych](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Algorytmy uczenia maszynowego

| Funkcjonalność | Opis | Moduł |
| --- |--- | --- |
| Regresja | Przewiduj wartość. | [Regresja wzmocnionego drzewa decyzyjnego](boosted-decision-tree-regression.md) <br/> [Regresja lasu decyzyjnego](decision-forest-regression.md) <br/> [Regresja liniowa](linear-regression.md)  <br/> [Regresja sieci neuronowej](neural-network-regression.md)  <br/> |
| Klastrowanie | Grupuj dane razem.| [Klastrowanie metodą k-średnich](k-means-clustering.md)
| Klasyfikacja | Wytypuj klasę.  Wybierz spośród algorytmów binarnych (dwuklasowych) lub wieloklasowych.| [Wieloklasowe wzmocnione drzewo decyzyjne](multiclass-boosted-decision-tree.md) <br/> [Wieloklasowy las decyzyjny](multiclass-decision-forest.md) <br/> [Wieloklasowa regresja logistyczna](multiclass-logistic-regression.md)  <br/> [Wieloklasowa sieć neuronowa](multiclass-neural-network.md) <br/> [Jeden kontra wszystkie multiklasy](one-vs-all-multiclass.md) <br/> [Dwuklasowy uśredniony perceptron](two-class-averaged-perceptron.md) <br/>  [Two-Class Boosted Decision Tree (Dwuklasowe wzmocnione drzewo decyzyjne)](two-class-boosted-decision-tree.md)  <br/> [Dwuklasowy las decyzyjny](two-class-decision-forest.md) <br/>  [Dwuklasowa regresja logistyczna](two-class-logistic-regression.md) <br/> [Dwuklasowa sieć neuronowa](two-class-neural-network.md) <br/> [Dwuklasowa maszyna wektorów nośnych](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Moduły do tworzenia i oceny modeli

| Funkcjonalność | Opis | Moduł |
| --- |--- | --- |
| Trenowanie modelu | Uruchom dane za pomocą algorytmu. |  [Trenowanie modelu klastrowania](train-clustering-model.md) <br/> [Train Model (Trenuj model)](train-model.md)  <br/> [Dostrajanie hiperparametrów modelu](tune-model-hyperparameters.md) |
| Ocenianie i ocena modelu | Zmierz dokładność przeszkolonego modelu. | [Stosowanie przekształcenia](apply-transformation.md) <br/> [Przypisywanie danych do klastrów](assign-data-to-clusters.md) <br/> [Krzyżowa weryfikacja modelu](cross-validate-model.md) <br/> [Ocena modelu](evaluate-model.md) <br/> [Klasyfikacja modelu](score-model.md) |
| Język Python | Napisz kod i osadź go w module, aby zintegrować Pythona z potokiem. | [Tworzenie modelu języka Python](create-python-model.md) <br/> [Wykonywanie skryptu w języku Python](execute-python-script.md) |
| Język R | Napisz kod i osadź go w module, aby zintegrować R z potoku. | [Wykonywanie skryptu języka R](execute-r-script.md) |
| Analiza tekstu | Zapewnij specjalistyczne narzędzia obliczeniowe do pracy z tekstem strukturalnym i nieustrukturyzowanym. | [Wyodrębnianie cech n-gramów z tekstu](extract-n-gram-features-from-text.md) <br/> [Tworzenie skrótów funkcji](feature-hashing.md) <br/> [Wstępne przetwarzanie tekstu](preprocess-text.md) <br/> [Alokacja utajonego dirichletu](latent-dirichlet-allocation.md) |
| Zalecenie | Tworzenie modeli rekomendacji. | [Ocena modułu poleceń](evaluate-recommender.md) <br/> [Wynik modułu poleceń SVD](score-svd-recommender.md) <br/> [Trenowanie modułu poleceń SVD](train-SVD-recommender.md) |
| Wykrywanie anomalii | Tworzenie modeli wykrywania anomalii. | [Wykrywanie anomalii oparte na pca](pca-based-anomaly-detection.md) <br/> [Model wykrywania anomalii pociągu](train-anomaly-detection-model.md) |


## <a name="error-messages"></a>Komunikaty o błędach

Dowiedz się więcej o [komunikatach o błędach i kodach wyjątków,](designer-error-codes.md) które mogą wystąpić przy użyciu modułów w projektancie usługi Azure Machine Learning.

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Tworzenie modelu w projektancie do przewidywania cen samochodów](../tutorial-designer-automobile-price-train-score.md)
