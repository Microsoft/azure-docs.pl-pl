---
title: Tworzenie zautomatyzowanych eksperymentów uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak definiować źródła danych, obliczenia i ustawienia konfiguracji dla eksperymentów zautomatyzowanego uczenia maszynowego.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperf-fy21q1, automl
ms.openlocfilehash: d0a15b16c04a28bcc67caeeceedfcbad485b7157
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861467"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurowanie eksperymentów zautomatyzowanego uczenia maszynowego w języku Python


W tym przewodniku dowiesz się, jak definiować różne ustawienia konfiguracji eksperymentów zautomatyzowanego uczenia maszynowego przy użyciu Azure Machine Learning [SDK.](/python/api/overview/azure/ml/intro) Zautomatyzowane uczenie maszynowe wybiera algorytm i hiperparametry, a następnie generuje model gotowy do wdrożenia. Istnieje kilka opcji, których można użyć do skonfigurowania eksperymentów zautomatyzowanego uczenia maszynowego.

Aby uzyskać end-to-end przykład eksperymentu zautomatyzowanego uczenia maszynowego, zobacz [Samouczek: szkolenie modelu klasyfikacji za pomocą zautomatyzowanego uczenia maszynowego.](tutorial-auto-train-models.md)

Opcje konfiguracji dostępne w zautomatyzowanym uczeniu maszynowym:

* Wybierz typ eksperymentu: Klasyfikacja, Regresja lub Prognozowanie szeregów czasu
* Źródło danych, formatuje i pobiera dane
* Wybieranie docelowego obiektu obliczeniowego: lokalnego lub zdalnego
* Ustawienia eksperymentu zautomatyzowanego uczenia maszynowego
* Uruchamianie eksperymentu zautomatyzowanego uczenia maszynowego
* Eksplorowanie metryk modelu
* Rejestrowanie i wdrażanie modelu

Jeśli nie chcesz korzystać z kodu, możesz również utworzyć eksperymenty zautomatyzowanego uczenia [maszynowego](how-to-use-automated-ml-for-ml-models.md)w Azure Machine Learning studio .

## <a name="prerequisites"></a>Wymagania wstępne

W przypadku tego artykułu, który jest potrzebny, 
* Obszar roboczy usługi Azure Machine Learning. Aby utworzyć obszar roboczy, zobacz [Create an Azure Machine Learning workspace (Tworzenie Azure Machine Learning obszaru roboczego).](how-to-manage-workspace.md)

* Zainstalowany Azure Machine Learning Python SDK.
    Aby zainstalować zestaw SDK, możesz użyć dowolnego z tych 
    * Utwórz wystąpienie obliczeniowe, które automatycznie instaluje zestaw SDK i jest wstępnie skonfigurowane dla przepływów pracy uczenia maszynowego. Aby [uzyskać więcej informacji, zobacz Create and manage an Azure Machine Learning compute instance](how-to-create-manage-compute-instance.md) (Tworzenie wystąpienia obliczeniowego i zarządzanie tym wystąpieniem). 

    * [Zainstaluj `automl` pakiet samodzielnie,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment)co obejmuje [domyślną instalację](/python/api/overview/azure/ml/install#default-install) zestawu SDK.
    
    > [!WARNING]
    > Język Python 3.8 nie jest zgodny z `automl` . 

## <a name="select-your-experiment-type"></a>Wybieranie typu eksperymentu

Przed rozpoczęciem eksperymentu należy określić rodzaj rozwiązywanych problemów uczenia maszynowego. Zautomatyzowane uczenie maszynowe obsługuje typy `classification` zadań , `regression` i `forecasting` . Dowiedz się więcej o [typach zadań](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast).

Poniższy kod używa `task` parametru w `AutoMLConfig` konstruktorze, aby określić typ eksperymentu jako `classification` .

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Źródło i format danych

Zautomatyzowane uczenie maszynowe obsługuje dane, które znajdują się na komputerze lokalnym lub w chmurze, na przykład w usłudze Azure Blob Storage. Dane można odczytać do ramki **danych pandas lub** Azure Machine Learning **TabularDataset**. [Dowiedz się więcej o zestawach danych](how-to-create-register-datasets.md).

Wymagania dotyczące trenowania danych w uczeniu maszynowym:
- Dane muszą być w postaci tabelarowej.
- Kolumna docelowa, która ma być przewidywana, musi znajdować się w danych.

**W przypadku eksperymentów** zdalnych dane szkoleniowe muszą być dostępne ze zdalnego wystąpienia obliczeniowego. Zautomatyzowane uczenie maszynowe akceptuje [tabelaryczne zestawy danych usługi Azure Machine Learning](/python/api/azureml-core/azureml.data.tabulardataset) tylko podczas pracy na zdalnych zasobach obliczeniowych. 

Zestawy danych usługi Azure Machine Learning udostępniają następujące funkcje:

* Łatwe przesyłanie danych z plików statycznych lub źródeł adresów URL do obszaru roboczego.
* Udostępnianie danych dla skryptów szkoleniowych w przypadku korzystania z zasobów obliczeniowych w chmurze. Zobacz [How to train with datasets (Jak](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) trenować przy użyciu zestawów danych), aby uzyskać przykład użycia klasy do mount data to your remote compute target (Jak zainstalować dane w `Dataset` zdalnym docelowym obiektem obliczeniowym).

Poniższy kod tworzy zestaw TabularDataset z internetowego adresu URL. Zobacz [Tworzenie zestawów Danych Tabelarównych,](how-to-create-register-datasets.md#create-a-tabulardataset) aby uzyskać przykłady kodu dotyczące tworzenia zestawów danych z innych źródeł, takich jak lokalne pliki i magazyny danych.

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**W przypadku lokalnych eksperymentów obliczeniowych** zalecamy ramki danych pandas, aby przyspieszyć czas przetwarzania.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Trenowania, walidacji i danych testowych

Możesz określić oddzielne dane **szkoleniowe i zestawy danych walidacji** bezpośrednio w `AutoMLConfig` konstruktorze. Dowiedz się więcej na [temat sposobu konfigurowania podziałów danych i krzyżowego sprawdzania poprawności](how-to-configure-cross-validation-data-splits.md) eksperymentów autoML. 

Jeśli nie określisz jawnie parametru lub , zautomatyzowane ml stosuje domyślne techniki w celu `validation_data` `n_cross_validation` określenia sposobu weryfikacji. To określenie zależy od liczby wierszy w zestawie danych przypisanych do `training_data` parametru. 

|Rozmiar &nbsp; danych &nbsp; treningowych| Technika walidacji |
|---|-----|
|**Większe &nbsp; niż &nbsp; 20 000 &nbsp; wierszy**| Stosowany jest podział danych trenowania/walidacji. Wartość domyślna to 10% początkowego zestawu danych treningowych jako zestawu walidacji. Z kolei ten zestaw walidacji jest używany do obliczania metryk.
|**Mniej &nbsp; niż &nbsp; 20 000 &nbsp; wierszy**| Stosowana jest metoda krzyżowej weryfikacji. Domyślna liczba składań zależy od liczby wierszy. <br> **Jeśli zestaw danych jest mniejszy niż 1000 wierszy,** używane jest 10 kropek. <br> **Jeśli wiersze są w okresie od 1000 do 20 000**, używane są trzy kropki.

W tej chwili musisz podać własne dane **testowe na** potrzeby oceny modelu. Przykładowy kod przynoszący własne dane testowe do oceny modelu można znaleźć w sekcji **Test** tego [notesu Jupyter.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)

## <a name="compute-to-run-experiment"></a>Obliczenia w celu uruchomienia eksperymentu

Następnie ustal, gdzie model zostanie wytrenowany. Eksperyment treningowy zautomatyzowanego uczenia maszynowego można uruchomić w następujących opcjach obliczeniowych. Poznaj [zalety i wady lokalnych i zdalnych opcji obliczeniowych](concept-automated-ml.md#local-remote). 

* Komputer **lokalny,** taki jak lokalny komputer stacjonarny lub laptop — zazwyczaj wtedy, gdy masz mały zestaw danych i nadal jesteś na etapie eksploracji. Zapoznaj się z [tym notesem](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb) zawierającym przykład obliczeń lokalnych. 
 
* Maszyna **zdalna** w chmurze [— Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute) to usługa zarządzana, która umożliwia trenowanie modeli uczenia maszynowego w klastrach maszyn wirtualnych platformy Azure. 

    Zobacz [ten notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb), aby zapoznać się z przykładem obliczeń zdalnych przy użyciu usługi Azure Machine Learning Managed Compute. 

* Klaster **Azure Databricks w** ramach subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz Konfigurowanie klastra [Azure Databricks zautomatyzowanego uczenia maszynowego.](how-to-configure-databricks-automl-environment.md) Zobacz tę [witrynę GitHub,](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) aby uzyskać przykłady notesów z Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Konfigurowanie ustawień eksperymentu

Istnieje kilka opcji, których można użyć do skonfigurowania eksperymentu zautomatyzowanego uczenia maszynowego. Te parametry są ustawiane przez iniekcję `AutoMLConfig` obiektu. Zobacz [klasę AutoMLConfig,](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) aby uzyskać pełną listę parametrów.

Oto niektóre przykłady:

1. Eksperyment klasyfikacji używający metryki ważonej AUC jako podstawowej metryki z limitem czasu eksperymentu ustawionym na 30 minut i 2 kropkami krzyżowej walidacji.

   ```python
       automl_classifier=AutoMLConfig(task='classification',
                                      primary_metric='AUC_weighted',
                                      experiment_timeout_minutes=30,
                                      blocked_models=['XGBoostClassifier'],
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=2)
   ```
1. Poniższy przykład to eksperyment regresji ustawiony na zakończenie po 60 minutach z pięcioma krzyżami walidacji.

   ```python
      automl_regressor = AutoMLConfig(task='regression',
                                      experiment_timeout_minutes=60,
                                      allowed_models=['KNN'],
                                      primary_metric='r2_score',
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=5)
   ```


1. Zadania prognozowania wymagają dodatkowej konfiguracji. Aby uzyskać więcej informacji, zobacz artykuł [Autotrain a time-series forecast model (Automatyczne](how-to-auto-train-forecast.md) trenowanie modelu prognozy szeregów czasu). 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'forecast_horizon': n_test_periods
    }
    
    automl_config = AutoMLConfig(task = 'forecasting',
                                 debug_log='automl_oj_sales_errors.log',
                                 primary_metric='normalized_root_mean_squared_error',
                                 experiment_timeout_minutes=20,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 path=project_folder,
                                 verbosity=logging.INFO,
                                 **time_series_settings)
    ```
    
### <a name="supported-models"></a>Obsługiwane modele

Zautomatyzowane uczenie maszynowe próbuje różnych modeli i algorytmów podczas procesu automatyzacji i dostrajania. Jako użytkownik nie musisz określać algorytmu. 

Trzy różne `task` wartości parametrów określają listę algorytmów lub modeli do zastosowania. Użyj parametrów `allowed_models` lub , aby dodatkowo `blocked_models` zmodyfikować iteracje z dostępnymi modelami, które mają być dołączane lub wykluczane. 

W poniższej tabeli przedstawiono podsumowanie obsługiwanych modeli według typu zadania. 

> [!NOTE]
> Jeśli planujesz wyeksportować modele utworzone przy użyciu automatycznego uczenia maszynowego do modelu [ONNX,](concept-onnx.md)tylko algorytmy oznaczone * mogą zostać przekonwertowane na format ONNX. Dowiedz się więcej o [konwertowaniu modeli na model ONNX.](concept-automated-ml.md#use-with-onnx) <br> <br> Należy również zauważyć, że w tej chwili system ONNX obsługuje tylko zadania klasyfikacji i regresji. 

Klasyfikacja | Regresja | Prognozowanie szeregów czasowych
|-- |-- |--
[Regresja logistyczna](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Sieć elastyczna](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Zwiększanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Zwiększanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Zwiększanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#regression)
[K najbliższych sąsiadów](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K najbliższych sąsiadów](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K najbliższych sąsiadów](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Liniowa SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS/Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Klasyfikacja wektorów pomocy technicznej (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Stochastyczny spadek gradientu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[Stochastyczny spadek gradientu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Las losowy](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Las losowy](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Las losowy](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Niezwykle losowe drzewa](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Niezwykle losowe drzewa](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Niezwykle losowe drzewa](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Averaged Perceptron Classifier](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)|[Regressor spadku gradientu online](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) |[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Naiwny bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Fast Linear Regressor](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)|[Proroka](https://facebook.github.io/prophet/docs/quick_start.html)
[Stochastyczny spadek gradientu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[Klasyfikator liniowych svm](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)*||

### <a name="primary-metric"></a>Metryka podstawowa
Parametr `primary metric` określa metrykę do użycia podczas trenowania modelu w celu optymalizacji. Dostępne metryki, które można wybrać, są określane na podstawie wybranego typu zadania. W poniższej tabeli przedstawiono prawidłowe metryki podstawowe dla każdego typu zadania.

Wybór podstawowej metryki do optymalizacji zautomatyzowanego uczenia maszynowego zależy od wielu czynników. Zalecamy wybór metryki, która najlepiej odpowiada potrzebom biznesowym. Następnie zastanów się, czy metryka jest odpowiednia dla Twojego profilu zestawu danych (rozmiar danych, zakres, rozkład klas itp.).

Zapoznaj się z określonymi definicjami tych metryk w temacie [Understand automated machine learning results (Informacje o wynikach zautomatyzowanego uczenia maszynowego).](how-to-understand-automated-ml.md)

|Klasyfikacja | Regresja | Prognozowanie szeregów czasowych
|--|--|--
|`accuracy`| `spearman_correlation` | `spearman_correlation`
|`AUC_weighted` | `normalized_root_mean_squared_error` | `normalized_root_mean_squared_error`
|`average_precision_score_weighted` | `r2_score` | `r2_score`
|`norm_macro_recall` | `normalized_mean_absolute_error` | `normalized_mean_absolute_error`
|`precision_score_weighted` |

### <a name="primary-metrics-for-classification-scenarios"></a>Podstawowe metryki dla scenariuszy klasyfikacji 

Metryki progowe, takie jak , , i, mogą nie być również optymalizowane pod kątem zestawów danych, które są małe, mają bardzo duży niesyfeksowanie klasy (dysproporcja klas) lub gdy oczekiwana wartość metryki jest bardzo zbliżona do `accuracy` `average_precision_score_weighted` `norm_macro_recall` `precision_score_weighted` 0,0 lub 1,0. W takich przypadkach lepszym wyborem dla metryki podstawowej może `AUC_weighted` być . Po zakończeniu zautomatyzowanego uczenia maszynowego możesz wybrać model zwycięzcą na podstawie metryki najlepiej dopasowanej do Twoich potrzeb biznesowych.

| Metric | Przykładowe przypadeky użycia |
| ------ | ------- |
| `accuracy` | Klasyfikacja obrazów, analiza tonacji, przewidywanie zmian |
| `AUC_weighted` | Wykrywanie oszustw, klasyfikacja obrazów, wykrywanie anomalii/wykrywanie spamu |
| `average_precision_score_weighted` | Analiza tonacji |
| `norm_macro_recall` | Przewidywanie zmian |
| `precision_score_weighted` |  |

### <a name="primary-metrics-for-regression-scenarios"></a>Podstawowe metryki dla scenariuszy regresji

Metryki takie jak i mogą lepiej reprezentować jakość modelu, gdy skala wartości do przewidywania obejmuje `r2_score` `spearman_correlation` wiele zamówień wielkości. Na przykład oszacowanie pensji, gdzie wiele osób ma pensję od 20 tys. USD do 100 tys. USD, ale skala jest bardzo wysoka z pewnymi pensjami w zakresie 100 mln USD. 

`normalized_mean_absolute_error` i w tym przypadku traktują błąd przewidywania o wartości 20 000 USD tak samo dla pracownika z pensją 30 tys. USD, jak pracownik, który zarobi `normalized_root_mean_squared_error` 20 mln USD. Chociaż w rzeczywistości przewidywanie tylko 20 tys. USD od pensji 20 mln USD jest bardzo zbliżone (różnica względna na poziomie 0,1%). natomiast 20 tys. USD od 30 tys. USD nie jest blisko (duża różnica względna 67%). `normalized_mean_absolute_error` Wartości `normalized_root_mean_squared_error` i są przydatne, gdy wartości do przewidywania są w podobnej skali.

| Metric | Przykładowe przypadeky użycia |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Przewidywanie cen (dom/produkt/porada), przeglądanie przewidywania wyniku |
| `r2_score` | Opóźnienie linii lotniczych, szacowanie pensji, czas rozwiązania usterki |
| `normalized_mean_absolute_error` |  |

### <a name="primary-metrics-for-time-series-forecasting-scenarios"></a>Podstawowe metryki dla scenariuszy prognozowania szeregów czasu

Zobacz uwagi dotyczące regresji powyżej.

| Metric | Przykładowe przypadeky użycia |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Przewidywanie cen (prognozowanie), optymalizacja spisu, prognozowanie popytu |
| `r2_score` | Przewidywanie cen (prognozowanie), optymalizacja spisu, prognozowanie popytu |
| `normalized_mean_absolute_error` | |

### <a name="data-featurization"></a>Cechowanie danych

W każdym eksperymencie zautomatyzowanego uczenia maszynowego dane  są automatycznie skalowane i normalizowane, aby pomóc niektórym algorytmom, które są wrażliwe na funkcje w różnych skalach. To skalowanie i normalizacja jest określane jako cechowanie. Aby uzyskać więcej informacji i przykłady kodu, zobacz Cechowanie w [uchucie automatycznym.](how-to-configure-auto-features.md#) 

Podczas konfigurowania eksperymentów w `AutoMLConfig` obiekcie można włączyć/wyłączyć ustawienie `featurization` . W poniższej tabeli przedstawiono akceptowane ustawienia cechowania w [obiekcie AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Konfiguracja do cechowania | Opis |
| ------------- | ------------- |
|`"featurization": 'auto'`| Wskazuje, że w ramach przetwarzania wstępnego bariery ochronne danych i kroki cechowania [są](how-to-configure-auto-features.md#featurization) wykonywane automatycznie. **Ustawienie domyślne**.|
|`"featurization": 'off'`| Wskazuje, że krok cechowania nie powinien być wykonywane automatycznie.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Wskazuje, że należy użyć dostosowanego kroku do cechowania. [Dowiedz się, jak dostosować cechowanie](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> Kroki cechowania zautomatyzowanego uczenia maszynowego (normalizacja funkcji, obsługa brakujących danych, konwertowanie tekstu na liczbowe itp.) stają się częścią bazowego modelu. W przypadku używania modelu do przewidywania te same kroki cechowania zastosowane podczas trenowania są automatycznie stosowane do danych wejściowych.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a>Konfiguracja zespołów

Modele zespołów są domyślnie włączone i są wyświetlane jako końcowe iteracje przebiegów w uruchomieniu autoML. Obecnie **obsługiwane są usługi VotingEnsemble** i **StackEnsemble.** 

W głosowaniu zaimplementowano głosowanie programowe, które używa średnich ważonych. Implementacja układania korzysta z implementacji dwuwarstwowej, w której pierwsza warstwa ma takie same modele jak zespół głosujących, a drugi model warstwowy służy do znalezienia optymalnej kombinacji modeli z pierwszej warstwy. 

Jeśli używasz modeli ONNX  lub masz włączoną funkcję objaśniania modelu, stos jest wyłączony i używane jest tylko głosowanie.

Trenowania zespołów można wyłączyć przy użyciu `enable_voting_ensemble` parametrów `enable_stack_ensemble` logicznych i .

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

Aby zmienić domyślne zachowanie grupy, istnieje wiele argumentów domyślnych, które można określić, jak `kwargs` w `AutoMLConfig` obiekcie.

> [!IMPORTANT]
>  Poniższe parametry nie są jawnymi parametrami klasy AutoMLConfig. 

* `ensemble_download_models_timeout_sec`: Podczas **generowania modeli VotingEnsemble** **i StackEnsemble** pobieranych jest wiele dopasowanych modeli z poprzednich przebiegów podrzędnych. Jeśli wystąpi ten błąd: , może być konieczne podanie więcej czasu na pobranie `AutoMLEnsembleException: Could not find any models for running ensembling` modeli. Wartość domyślna to 300 sekund na potrzeby równoległego pobierania tych modeli i nie ma żadnego maksymalnego limitu czasu. Skonfiguruj ten parametr z wyższą wartością niż 300 s, jeśli jest potrzebny więcej czasu. 

  > [!NOTE]
  >  Jeśli limit czasu zostanie osiągnięty i zostaną pobrane modele, ensembling będzie kontynuowane wraz z tak wieloma modelami, jaka została pobrana. Nie jest wymagane, aby wszystkie modele były pobierane, aby zakończyć się w tym czasie.

Następujące parametry dotyczą tylko modeli **StackEnsemble:** 

* `stack_meta_learner_type`: metauczenia to model wytrenowany na podstawie danych wyjściowych poszczególnych modeli heterogenicznych. Domyślna metaufikatorzy są dla zadań klasyfikacji (lub jeśli włączono krzyżową walidację) oraz dla zadań regresji/prognozowania (lub jeśli włączono `LogisticRegression` `LogisticRegressionCV` `ElasticNet` `ElasticNetCV` krzyżową weryfikację). Ten parametr może być jednym z następujących ciągów: `LogisticRegression` , , , , , , lub `LogisticRegressionCV` `LightGBMClassifier` `ElasticNet` `ElasticNetCV` `LightGBMRegressor` `LinearRegression` .

* `stack_meta_learner_train_percentage`: określa proporcję zestawu szkoleniowego (podczas wybierania typu trenowania i walidacji trenowania) do zarezerwowania na szkolenie metauczenia. Wartość domyślna to `0.2`. 

* `stack_meta_learner_kwargs`: opcjonalne parametry do przekazania do inicjatora metauczenia. Te parametry i typy parametrów odzwierciedlają parametry i typy parametrów z odpowiedniego konstruktora modelu i są przekazywane do konstruktora modelu.

Poniższy kod przedstawia przykład określania niestandardowego zachowania grupy w `AutoMLConfig` obiekcie.

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

<a name="exit"></a> 

### <a name="exit-criteria"></a>Kryteria zakończenia

Istnieje kilka opcji, które można zdefiniować w pliku AutoMLConfig, aby zakończyć eksperyment.

|Kryteria| description (opis)
|----|----
Brak &nbsp; kryteriów | Jeśli nie zdefiniujemy żadnych parametrów wyjścia, eksperyment będzie kontynuowany, dopóki nie zostanie wykonany żaden postęp w metryce podstawowej.
Po &nbsp; &nbsp; upływie &nbsp; czasu &nbsp;| Użyj `experiment_timeout_minutes` w ustawieniach, aby określić, jak długo (w minutach) eksperyment powinien być nadal uruchamiany. <br><br> Aby uniknąć błędów przekroczeniu limitów czasu eksperymentu, istnieje co najmniej 15 minut lub 60 minut, jeśli rozmiar wiersza według kolumny przekracza 10 milionów.
&nbsp;Osiągnięto &nbsp; &nbsp; wynik &nbsp;| Użycie kończy eksperyment po osiągnięciu określonego podstawowego wyniku `experiment_exit_score` metryki.

## <a name="run-experiment"></a>Uruchamianie eksperymentu

W przypadku zautomatyzowanego uczenia maszynowego `Experiment` tworzysz obiekt , który jest nazwany w obiekcie `Workspace` używanym do uruchamiania eksperymentów.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Prześlij eksperyment, aby uruchomić i wygenerować model. Przekaż `AutoMLConfig` do metody `submit` , aby wygenerować model.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Zależności są najpierw instalowane na nowej maszynie.  Zanim dane wyjściowe będą wyświetlane, może upłynieć do 10 minut.
>Ustawienie `show_output` `True` powoduje, że dane wyjściowe są wyświetlane w konsoli.

### <a name="multiple-child-runs-on-clusters"></a>Wiele przebiegów podrzędnych w klastrach

Przebiegi podrzędne eksperymentu zautomatyzowanego uczenia maszynowego można wykonywać w klastrze, który już uruchamia inny eksperyment. Jednak czas zależy od liczby węzłów w klastrze oraz od tego, czy te węzły są dostępne do uruchomienia innego eksperymentu.

Każdy węzeł w klastrze działa jako pojedyncza maszyna wirtualna, która może wykonać jeden przebieg trenowania. W przypadku zautomatyzowanego uczenia maszynowego oznacza to przebieg podrzędny. Jeśli wszystkie węzły są zajęte, nowy eksperyment zostanie w kolejce. Jeśli jednak istnieją bezpłatne węzły, w nowym eksperymencie zautomatyzowane przebiegi podrzędne uczenia maszynowego będą uruchamiane równolegle na dostępnych węzłach/maszynach wirtualnych.

Aby ułatwić zarządzanie przebiegami podrzędnymi i ich możliwością wykonania, zalecamy utworzenie dedykowanego klastra na eksperyment i dopasowanie liczby eksperymentu do liczby węzłów w `max_concurrent_iterations` klastrze. W ten sposób użyjemy wszystkich węzłów klastra w tym samym czasie z liczbą współbieżnych podrzędnych przebiegów/iteracji.

Skonfiguruj  `max_concurrent_iterations` w `AutoMLConfig` obiekcie . Jeśli nie jest skonfigurowany, domyślnie dozwolone jest tylko jedno równoczesne podrzędne uruchomienie/iteracja na eksperyment.  

## <a name="explore-models-and-metrics"></a>Eksplorowanie modeli i metryk

Zautomatyzowane uczenia maszynowego oferuje opcje monitorowania i oceny wyników trenowania. 

* Wyniki trenowania możesz wyświetlić w widżecie lub w tekście, jeśli jesteś w notesie. Aby uzyskać [więcej informacji,](#monitor) zobacz Monitorowanie przebiegów zautomatyzowanego uczenia maszynowego.

* Definicje i przykłady wykresów wydajności i metryk podanych dla każdego uruchomienia można znaleźć w temacie Evaluate automated machine learning experiment results (Ocena wyników eksperymentu [zautomatyzowanego uczenia maszynowego).](how-to-understand-automated-ml.md) 

* Aby uzyskać podsumowanie cech i zrozumieć, jakie funkcje zostały dodane do określonego modelu, zobacz [Featurization transparency (Przezroczystość cechowania).](how-to-configure-auto-features.md#featurization-transparency) 

Hiperparametry, techniki skalowania i normalizacji oraz algorytmy stosowane do określonego uruchomienia zautomatyzowanego uczenia maszynowego można wyświetlić za pomocą następującego rozwiązania kodu niestandardowego. 

Poniżej zdefiniowano metodę niestandardową , która drukuje hiperparametry każdego kroku zautomatyzowanego potoku `print_model()` trenowania uczenia maszynowego.
 
```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
        elif hasattr(step[1], '_base_learners') and hasattr(step[1], '_meta_learner'):
            print("\nMeta Learner")
            pprint(step[1]._meta_learner)
            print()
            for estimator in step[1]._base_learners:
                print_model(estimator[1], estimator[0]+ ' - ')
        else:
            pprint(step[1].get_params())
            print()   
```

W przypadku przebiegu lokalnego lub zdalnego, który właśnie został przesłany i wytrenowany z poziomu tego samego notesu eksperymentu, można przekazać najlepszy model przy użyciu `get_output()` metody . 

```python
best_run, fitted_model = run.get_output()
print(best_run)
         
print_model(fitted_model)
```

Następujące dane wyjściowe wskazują, że:
 
* Technika StandardScalerWrapper została użyta do skalowania i normalizacji danych przed trenowaniem.

* Algorytm XGBoostClassifier został zidentyfikowany jako najlepszy przebieg, a także pokazuje wartości hiperparametrów. 

```python
StandardScalerWrapper
{'class_name': 'StandardScaler',
 'copy': True,
 'module_name': 'sklearn.preprocessing.data',
 'with_mean': False,
 'with_std': False}

XGBoostClassifier
{'base_score': 0.5,
 'booster': 'gbtree',
 'colsample_bylevel': 1,
 'colsample_bynode': 1,
 'colsample_bytree': 0.6,
 'eta': 0.4,
 'gamma': 0,
 'learning_rate': 0.1,
 'max_delta_step': 0,
 'max_depth': 8,
 'max_leaves': 0,
 'min_child_weight': 1,
 'missing': nan,
 'n_estimators': 400,
 'n_jobs': 1,
 'nthread': None,
 'objective': 'multi:softprob',
 'random_state': 0,
 'reg_alpha': 0,
 'reg_lambda': 1.6666666666666667,
 'scale_pos_weight': 1,
 'seed': None,
 'silent': None,
 'subsample': 0.8,
 'tree_method': 'auto',
 'verbose': -10,
 'verbosity': 1}
```

W przypadku istniejącego przebiegu z innego eksperymentu w obszarze roboczym uzyskaj konkretny identyfikator przebiegu, który chcesz eksplorować, i przekaż go do `print_model()` metody . 

```python
from azureml.train.automl.run import AutoMLRun

ws = Workspace.from_config()
experiment = ws.experiments['automl-classification']
automl_run = AutoMLRun(experiment, run_id = 'AutoML_xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx')

automl_run
best_run, model_from_aml = automl_run.get_output()

print_model(model_from_aml)

```
> [!NOTE]
> Algorytmy zautomatyzowanego uczenia maszynowego mają naturalną losowość, która może powodować niewielkie odchylenia końcowego wyniku metryk zalecanego modelu, na przykład dokładność. Zautomatyzowane uczenia maszynowego wykonuje również operacje na danych, takie jak podział trenowania i testowania, podział trenowania i walidacja krzyżowa, jeśli jest to konieczne. Dlatego jeśli wielokrotnie uruchamiasz eksperyment z tymi samymi ustawieniami konfiguracji i metryką podstawową, prawdopodobnie zobaczysz różnice w końcowym wyniku metryk każdego eksperymentu ze względu na te czynniki. 

## <a name="monitor-automated-machine-learning-runs"></a><a name="monitor"></a> Monitorowanie przebiegów zautomatyzowanego uczenia maszynowego

W przypadku przebiegów zautomatyzowanego uczenia maszynowego aby uzyskać dostęp do wykresów z poprzedniego uruchomienia, zastąp `<<experiment_name>>` odpowiednią nazwą eksperymentu:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Widżet notesu Jupyter dla aplikacji Automated Machine Learning](./media/how-to-configure-auto-train/azure-machine-learning-auto-ml-widget.png)

## <a name="register-and-deploy-models"></a>Rejestrowanie i wdrażanie modeli

Możesz zarejestrować model, aby wrócić do niego w celu późniejszego użycia. 

Aby zarejestrować model z automatycznego uruchomienia uczenia maszynowego, użyj [`register_model()`](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) metody . 

```Python

best_run, fitted_model = run.get_output()
print(fitted_model.steps)

model_name = best_run.properties['model_name']
description = 'AutoML forecast example'
tags = None

model = remote_run.register_model(model_name = model_name, 
                                  description = description, 
                                  tags = tags)
```


Aby uzyskać szczegółowe informacje na temat tworzenia konfiguracji wdrożenia i wdrażania zarejestrowanego modelu w usłudze internetowej, zobacz jak i gdzie [wdrożyć model.](how-to-deploy-and-where.md?tabs=python#define-a-deployment-configuration)

> [!TIP]
> W przypadku zarejestrowanych modeli wdrożenie jednym kliknięciem jest dostępne za pośrednictwem [Azure Machine Learning studio](https://ml.azure.com). Zobacz, [jak wdrażać zarejestrowane modele z programu Studio.](how-to-use-automated-ml-for-ml-models.md#deploy-your-model) 
<a name="explain"></a>

## <a name="model-interpretability"></a>Możliwość interpretowania modelu

Możliwość interpretowania modeli pozwala zrozumieć, dlaczego modele przewidywały, oraz poznać wartości ważności podstawowych cech. Zestaw SDK zawiera różne pakiety umożliwiające funkcje możliwości interpretacji modelu, zarówno w czasie trenowania, jak i wnioskowania, dla modeli lokalnych i wdrożonych.

Zapoznaj się z [how-to for](how-to-machine-learning-interpretability-automl.md) code samples on how to enable interpretability features specifically within automated machine learning experiments (Jak włączyć funkcje możliwości interpretowania w eksperymentach zautomatyzowanego uczenia maszynowego).

Aby uzyskać ogólne informacje na temat sposobu, w jaki wyjaśnienia modelu i znaczenie funkcji [](how-to-machine-learning-interpretability.md) mogą być włączane w innych obszarach zestawu SDK poza zautomatyzowanym uczeniem maszynowym, zobacz artykuł koncepcyjny na temat możliwości interpretacji.

> [!NOTE]
> Model ForecastTCN nie jest obecnie obsługiwany przez klienta wyjaśnienia. Ten model nie zwróci pulpitu nawigacyjnego wyjaśnienia, jeśli zostanie zwrócony jako najlepszy model i nie obsługuje przebiegów wyjaśnienia na żądanie.

## <a name="next-steps"></a>Następne kroki

+ Dowiedz się więcej [o tym, jak i gdzie wdrożyć model.](how-to-deploy-and-where.md)

+ Dowiedz się więcej na [temat trenowania modelu regresji za pomocą zautomatyzowanego uczenia maszynowego.](tutorial-auto-train-models.md)

+ Dowiedz się, jak trenować wiele modeli za pomocą rozwiązania AutoML w [akceleratorze rozwiązań wielu modeli.](https://aka.ms/many-models)

+ [Rozwiązywanie problemów z eksperymentami zautomatyzowanego uczenia maszynowego.](how-to-troubleshoot-auto-ml.md) 
