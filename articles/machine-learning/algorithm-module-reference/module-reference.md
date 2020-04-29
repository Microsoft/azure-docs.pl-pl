---
title: Dokumentacja algorytmów i modułów
description: Informacje o modułach dostępnych w programie Azure Machine Learning Designer (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: c46c21b5662b924552f850f226a12c7350b4625a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81458203"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer-preview"></a>Informacje o algorytmach & module dla projektanta Azure Machine Learning (wersja zapoznawcza)

Ta zawartość referencyjna zawiera informacje techniczne na temat każdego z algorytmów uczenia maszynowego i modułów dostępnych w programie Azure Machine Learning Designer (wersja zapoznawcza).

Każdy moduł reprezentuje zestaw kodu, który może zostać uruchomiony niezależnie i wykonać zadanie uczenia maszynowego z uwzględnieniem wymaganych danych wejściowych. Moduł może zawierać określony algorytm lub wykonać zadanie, które jest ważne w uczeniu maszynowym, takie jak brakująca wartość zastępcza lub analiza statystyczna.

Aby uzyskać pomoc dotyczącą wybierania algorytmów, zobacz 
* [Jak wybrać algorytmy](../how-to-select-algorithms.md)
* [Arkusz Ściągawka algorytmu Azure Machine Learning](../algorithm-cheat-sheet.md)

> [!TIP]
> W każdym potoku projektanta można uzyskać informacje dotyczące określonego modułu. Wybierz moduł, a następnie wybierz łącze **więcej pomocy** w okienku **szybkie pomoc** .

## <a name="data-preparation-modules"></a>Moduły przygotowywania danych


| Funkcjonalność | Opis | Moduł |
| --- |--- | --- |
| Wprowadzanie i wyprowadzanie danych | Przenieś dane ze źródeł w chmurze do potoku. Zapisuj wyniki lub dane pośrednie w usłudze Azure Storage, w bazie danych SQL lub Hive, podczas uruchamiania potoku lub korzystaj z magazynu w chmurze, aby wymieniać dane między potokami.  | [Ręczne wprowadzanie danych](enter-data-manually.md) <br/> [Eksportuj dane](export-data.md) <br/> [Importowanie danych](import-data.md) |
| Transformacja danych | Operacje na danych, które są unikatowe dla uczenia maszynowego, takie jak normalizowanie lub pakowania danych, Zmniejszanie liczby wymiarów i konwertowanie danych między różnymi formatami plików.| [Dodawanie kolumn](add-columns.md) <br/> [Dodawanie wierszy](add-rows.md) <br/> [Stosowanie operacji matematycznej](apply-math-operation.md) <br/> [Stosowanie przekształcenia SQL](apply-sql-transformation.md) <br/> [Czyszczenie brakujących danych](clean-missing-data.md) <br/> [Obcinanie wartości](clip-values.md) <br/> [Konwertowanie na plik CSV](convert-to-csv.md) <br/> [Konwertowanie na zestaw danych](convert-to-dataset.md) <br/> [Konwertowanie na wartości wskaźnika](convert-to-indicator-values.md) <br/> [Edytowanie metadanych](edit-metadata.md) <br/> [Łączenie danych](join-data.md) <br/> [Normalizowanie danych](normalize-data.md) <br/> [Partycjonowanie i próbkowanie](partition-and-sample.md)  <br/> [Usuwanie zduplikowanych wierszy](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Wybieranie przekształcenia kolumn](select-columns-transform.md) <br/> [Wybieranie kolumn w zestawie danych](select-columns-in-dataset.md) <br/> [Dzielenie danych](split-data.md) |
| Wybór funkcji | Wybierz podzestaw odpowiednich, przydatnych funkcji do użycia podczas tworzenia modelu analitycznego. | [Wybór funkcji oparty na filtrze](filter-based-feature-selection.md) <br/> [Ważność funkcji permutacji](permutation-feature-importance.md) |
| Funkcje statystyczne | Zapewniają szeroką gamę metod statystycznych związanych z nauką danych. | [Podsumowywanie danych](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Algorytmy uczenia maszynowego

| Funkcjonalność | Opis | Moduł |
| --- |--- | --- |
| Regresja | Przewidywanie wartości. | [Regresja wzmocnionego drzewa decyzyjnego](boosted-decision-tree-regression.md) <br/> [Regresja lasu decyzyjnego](decision-forest-regression.md) <br/> [Regresja liniowa](linear-regression.md)  <br/> [Regresja sieci neuronowej](neural-network-regression.md)  <br/> |
| Klastrowanie | Grupuj dane jednocześnie.| [Klastrowanie metodą k-średnich](k-means-clustering.md)
| Klasyfikacja | Przewidywanie klasy.  Wybierz jeden z dwuklasowego algorytmu lub algorytmów wieloklasowych.| [Wieloklasowe wzmocnione drzewo decyzyjne](multiclass-boosted-decision-tree.md) <br/> [Wieloklasowy las decyzyjny](multiclass-decision-forest.md) <br/> [Wieloklasowa regresja logistyczna](multiclass-logistic-regression.md)  <br/> [Wieloklasowa sieć neuronowa](multiclass-neural-network.md) <br/> [Jeden przeciw wszystkim — moduł wieloklasowy](one-vs-all-multiclass.md) <br/> [Dwuklasowy uśredniony perceptron](two-class-averaged-perceptron.md) <br/>  [Two-Class Boosted Decision Tree (Dwuklasowe wzmocnione drzewo decyzyjne)](two-class-boosted-decision-tree.md)  <br/> [Dwuklasowy las decyzyjny](two-class-decision-forest.md) <br/>  [Dwuklasowa regresja logistyczna](two-class-logistic-regression.md) <br/> [Dwuklasowa sieć neuronowa](two-class-neural-network.md) <br/> [Dwuklasowa maszyna wektorów nośnych](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Moduły do kompilowania i oceniania modeli

| Funkcjonalność | Opis | Moduł |
| --- |--- | --- |
| Trenowanie modelu | Uruchom dane za pomocą algorytmu. |  [Trenowanie modelu klastrowania](train-clustering-model.md) <br/> [Trenowanie modelu](train-model.md)  <br/> [Dostrajanie hiperparametrów modelu](tune-model-hyperparameters.md) |
| Ocenianie i ocenianie modelu | Zmierz dokładność nauczonego modelu. | [Stosowanie przekształcenia](apply-transformation.md) <br/> [Przypisywanie danych do klastrów](assign-data-to-clusters.md) <br/> [Krzyżowa weryfikacja modelu](cross-validate-model.md) <br/> [Ocena modelu](evaluate-model.md) <br/> [Klasyfikacja modelu](score-model.md) |
| Język Python | Napisz kod i Osadź go w module, aby zintegrować Język Python z potokiem. | [Tworzenie modelu języka Python](create-python-model.md) <br/> [Wykonywanie skryptu w języku Python](execute-python-script.md) |
| Język R | Napisz kod i Osadź go w module, aby zintegrować język R z potokiem. | [Wykonywanie skryptu języka R](execute-r-script.md) |
| Analiza tekstu | Udostępniaj wyspecjalizowane narzędzia obliczeniowe służące do pracy z tekstem ze strukturą i bez struktury. | [Wyodrębnianie cech n-gramów z tekstu](extract-n-gram-features-from-text.md) <br/> [Tworzenie skrótów funkcji](feature-hashing.md) <br/> [Wstępne przetwarzanie tekstu](preprocess-text.md) <br/> [Alokacja ukrytej zmiennej Dirichleta](latent-dirichlet-allocation.md) |
| Zalecenie | Kompiluj modele rekomendacji. | [Ocena modułu poleceń](evaluate-recommender.md) <br/> [Wynik modułu poleceń SVD](score-svd-recommender.md) <br/> [Trenowanie modułu poleceń SVD](train-SVD-recommender.md) |
| Wykrywanie anomalii | Tworzenie modeli wykrywania anomalii. | [Wykrywanie anomalii oparte na analizie głównych składowych](pca-based-anomaly-detection.md) <br/> [Trenowanie modelu wykrywania anomalii](train-anomaly-detection-model.md) |


## <a name="web-service"></a>Usługa internetowa

Dowiedz się więcej na temat [modułów usługi sieci Web](web-service-input-output.md) , które są niezbędne do wnioskowania w czasie rzeczywistym w programie Azure Machine Learning Designer.

## <a name="error-messages"></a>Komunikaty o błędach

Dowiedz się więcej o [komunikatach o błędach i kodach wyjątków](designer-error-codes.md) , które mogą wystąpić przy użyciu modułów w programie Azure Machine Learning Designer.

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Tworzenie modelu w projektancie w celu przewidywania cen Autokorekty](../tutorial-designer-automobile-price-train-score.md)
