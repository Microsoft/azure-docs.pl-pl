---
title: Tworzenie zautomatyzowanych eksperymentów uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Automatyczne Uczenie maszynowe wybiera algorytm dla Ciebie i generuje model gotowy do wdrożenia. Zapoznaj się z opcjami, których można użyć, aby skonfigurować zautomatyzowane eksperymenty uczenia maszynowego.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 05/20/2020
ms.custom: seodec18, tracking-python
ms.openlocfilehash: 4815e51d22501d6110f3bc26a878513d6d700ce7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87031290"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurowanie eksperymentów zautomatyzowanego uczenia maszynowego w języku Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym przewodniku dowiesz się, jak definiować różne ustawienia konfiguracji zautomatyzowanych eksperymentów uczenia maszynowego za pomocą [zestawu SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Automatyczne Uczenie maszynowe wybiera algorytm i parametry i tworzy model gotowy do wdrożenia. Istnieje kilka opcji, których można użyć do skonfigurowania zautomatyzowanych eksperymentów w usłudze Machine Learning.

Aby wyświetlić przykłady zautomatyzowanych eksperymentów dotyczących uczenia maszynowego, zobacz [Samouczek: uczenie modelu klasyfikacji z automatycznym uczeniem maszynowym](tutorial-auto-train-models.md) lub [modelami szkoleń z automatycznym uczeniem maszynowym w chmurze](how-to-auto-train-remote.md).

Opcje konfiguracji dostępne w ramach automatycznego uczenia maszynowego:

* Wybierz typ eksperymentu: klasyfikację, regresję lub prognozowanie szeregów czasowych
* Źródło danych, formaty i pobieranie danych
* Wybierz obiekt docelowy obliczeń: lokalny lub zdalny
* Ustawienia zautomatyzowanego eksperymentu w usłudze Machine Learning
* Uruchamianie eksperymentu automatycznego uczenia maszynowego
* Eksplorowanie metryk modelu
* Zarejestruj i Wdróż model

Jeśli wolisz nie używać kodu, możesz również [utworzyć automatyczne eksperymenty uczenia maszynowego w programie Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="select-your-experiment-type"></a>Wybieranie typu eksperymentu

Przed rozpoczęciem eksperymentu należy określić rodzaj rozwiązywanego problemu z uczeniem maszynowym. Funkcja automatycznego uczenia maszynowego obsługuje typy zadań klasyfikacji, regresji i prognozowania. Dowiedz się więcej o [typach zadań](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast).

Automatyczne Uczenie maszynowe obsługuje następujące algorytmy w trakcie procesu automatyzacji i dostrajania. Jako użytkownik nie ma potrzeby określania algorytmu.

> [!NOTE]
> Jeśli planujesz eksportować modele utworzone przez funkcję automl do [modelu ONNX](concept-onnx.md), tylko te algorytmy wskazywane przez * są możliwe do przekonwertowania na format ONNX. Dowiedz się więcej o [konwertowaniu modeli na ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Należy również pamiętać, że w tym momencie ONNX obsługuje tylko zadania klasyfikacji i regresji. 

Klasyfikacja | Regresja | Prognozowanie szeregów czasowych
|-- |-- |--
[Regresja logistyczna](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Sieć elastyczna](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Sieć elastyczna](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Lekki GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Lekki GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Lekki GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Zwiększanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Zwiększanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Zwiększanie gradientu](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Drzewo decyzyjne](https://scikit-learn.org/stable/modules/tree.html#regression)
[Najbliższe sąsiednie sąsiedzi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[Najbliższe sąsiednie sąsiedzi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[Najbliższe sąsiednie sąsiedzi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Liniowy SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS/Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Klasyfikacja wektorów pomocy technicznej (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Stochastycznego gradientu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[Stochastycznego gradientu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Las losowy](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Las losowy](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Las losowy](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Skrajnie losowe drzewa](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Skrajnie losowe drzewa](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Skrajnie losowe drzewa](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Klasyfikator średniej Perceptron](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)|[Gradient online z Regresorem](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest) |[AutoARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Algorytm Bayesa Bayesa](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Szybka Regresor liniowa](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[Stochastycznego gradientu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[Klasyfikator liniowy SVM](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)*||

Użyj `task` parametru w `AutoMLConfig` konstruktorze, aby określić typ eksperymentu.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Źródło i format danych

Automatyczne Uczenie maszynowe obsługuje dane, które znajdują się na pulpicie lokalnym lub w chmurze, takiej jak Azure Blob Storage. Dane można odczytać do **Pandas Dataframe** lub **Azure Machine Learning TabularDataset**.  [Dowiedz się więcej o zestawach danych](how-to-create-register-datasets.md).

Wymagania dotyczące danych szkoleniowych:
- Dane muszą być w formie tabelarycznej.
- Wartość do przewidywania, kolumna docelowa, musi znajdować się w danych.

Poniższy przykład kodu pokazuje, jak przechowywać dane w tych formatach.

* TabularDataset

  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* Ramka dataPandas

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Pobieranie danych na potrzeby uruchamiania eksperymentu w przypadku obliczeń zdalnych

W przypadku wykonywania zdalnego dane szkoleniowe muszą być dostępne ze zdalnych obliczeń. Klasa [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) w zestawie SDK udostępnia funkcje:

* łatwo Transferuj dane ze źródeł plików statycznych lub adresów URL do obszaru roboczego
* Udostępnij swoje dane dla skryptów szkoleniowych w przypadku korzystania z zasobów obliczeniowych w chmurze

Zobacz [instrukcje](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) dotyczące przykładu użycia `Dataset` klasy do instalowania danych w celu obliczenia.

## <a name="train-and-validation-data"></a>Dane dotyczące uczenia i walidacji

Możesz określić osobne zestawy pouczenia i walidacji bezpośrednio w `AutoMLConfig` konstruktorze, korzystając z następujących opcji. Dowiedz się więcej na temat [sposobu konfigurowania podziałów danych i krzyżowego sprawdzania poprawności](how-to-configure-cross-validation-data-splits.md) dla eksperymentów AutoML. 

### <a name="k-folds-cross-validation"></a>K — zgięcie krzyżowe

Użyj `n_cross_validations` Ustawienia, aby określić liczbę operacji sprawdzania krzyżowego. Zestaw danych szkoleniowych zostanie losowo podzielony na `n_cross_validations` zgięcia o równym rozmiarze. Podczas każdego zaokrąglania krzyżowego, jeden ze zgięciów będzie używany do walidacji modelu przeszkolonego na pozostałych zgięciach. Ten proces jest powtarzany do `n_cross_validations` momentu, aż każde zgięcie zostanie użyte raz jako zestaw walidacji. Zostaną zgłoszone średnie wyniki dla wszystkich `n_cross_validations` rund, a odpowiedni model zostanie przeszukany w całym zestawie danych szkoleniowych.

Dowiedz się więcej o tym, jak autoML stosuje krzyżowe sprawdzanie poprawności, aby [uniknąć nadmiernego dopasowania modeli](concept-manage-ml-pitfalls.md#prevent-over-fitting).

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo, krzyżowe sprawdzanie poprawności (powtarza losowe Podpróbkowanie)

Użyj, `validation_size` Aby określić procentowy zestaw danych szkoleniowych, który ma być używany do walidacji, i Użyj, `n_cross_validations` Aby określić liczbę operacji krzyżowych. Podczas każdego zaokrąglania krzyżowego, podzbiór rozmiaru `validation_size` będzie losowo wybierany do walidacji modelu przeszkolonego na pozostałych danych. Na koniec zostaną zgłoszone średnie wyniki dla wszystkich `n_cross_validations` rund, a odpowiedni model zostanie przeszukany w całym zestawie danych szkoleniowych. Monte Carlo nie jest obsługiwana w przypadku prognozowania szeregów czasowych.

### <a name="custom-validation-dataset"></a>Niestandardowy zestaw danych walidacji

Użyj niestandardowego zestawu danych walidacji, jeśli podział losowy nie jest akceptowalny, zazwyczaj dane szeregów czasowych lub niezrównoważone dane. Możesz określić własny zestaw danych walidacji. Model zostanie oceniony względem określonego zestawu danych sprawdzania poprawności zamiast losowego zestawu danych. Dowiedz się więcej na temat [konfigurowania niestandardowego zestawu walidacji przy użyciu zestawu SDK](how-to-configure-cross-validation-data-splits.md#provide-validation-data).

## <a name="compute-to-run-experiment"></a>Obliczenia w celu uruchomienia eksperymentu

Następnie określ, gdzie będzie szkolony model. Zautomatyzowany eksperyment szkoleniowy do uczenia maszynowego można uruchomić na następujących opcjach obliczeniowych:
* Komputer lokalny, taki jak pulpit lokalny lub laptop, zazwyczaj w przypadku małego zestawu danych i nadal w fazie eksploracji.
* Maszyna zdalna w chmurze — [Azure Machine Learning zarządzane obliczenia](concept-compute-target.md#amlcompute) to usługa zarządzana, która umożliwia uczenie modeli uczenia maszynowego w klastrach maszyn wirtualnych platformy Azure. 

  Zobacz tę [witrynę usługi GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) , aby poznać przykłady notesów z lokalnymi i zdalnymi obiektami docelowymi obliczeniowymi.

* Klaster Azure Databricks w ramach subskrypcji platformy Azure. Więcej szczegółów można znaleźć tutaj — [instalator Azure Databricks klaster dla zautomatyzowanej ml](how-to-configure-environment.md#azure-databricks)

  Zobacz tę [witrynę w witrynie GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) , aby zapoznać się z przykładami notesów z Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Skonfiguruj ustawienia eksperymentu

Istnieje kilka opcji, których można użyć do skonfigurowania automatycznego eksperymentu uczenia maszynowego. Te parametry są ustawiane przez utworzenie wystąpienia `AutoMLConfig` obiektu. Zapoznaj się z pełną listą parametrów w [klasie AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) .

Oto niektóre przykłady:

1. Eksperyment klasyfikacji korzystający z AUC ważone jako Metryka podstawowa z limitem czasu eksperymentu w minutach ustawionym na 30 minut i 2 zgięcia wzajemnej walidacji.

   ```python
       automl_classifier=AutoMLConfig(
       task='classification',
       primary_metric='AUC_weighted',
       experiment_timeout_minutes=30,
       blacklist_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
2. Poniżej znajduje się przykład zestawu eksperymentów regresji, który ma zostać zakończony po 60 minutach z pięcioma zgięciami krzyżowymi walidacji.

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      whitelist_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```

Trzy różne `task` wartości parametrów (trzeci typ zadania to `forecasting` , i używa podobnej puli algorytmu jako `regression` zadania) określają listę modeli do zastosowania. Użyj `whitelist` parametrów lub, `blacklist` Aby kontynuować modyfikowanie iteracji z dostępnymi modelami do dołączania lub wykluczania. Listę obsługiwanych modeli można znaleźć w [klasie SupportedModels](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) ([Klasyfikacja](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification), [prognozowanie](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)i [regresja](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)).

Aby uniknąć błędów przekroczenia limitu czasu eksperymentów, automatyczna usługa weryfikacji ML będzie wymagała `experiment_timeout_minutes` Ustawienia co najmniej 15 minut lub 60 minut, jeśli rozmiar wiersza według kolumny przekracza 10 000 000.

### <a name="primary-metric"></a>Metryka podstawowa
Metryka podstawowa określa metrykę, która ma być używana podczas uczenia modelu na potrzeby optymalizacji. Dostępne metryki, które można wybrać, są określane przez wybrany typ zadania, a w poniższej tabeli przedstawiono prawidłowe Podstawowe metryki dla każdego typu zadania.

|Klasyfikacja | Regresja | Prognozowanie szeregów czasowych
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Zapoznaj się z określonymi definicjami tych metryk w temacie [Omówienie zautomatyzowanych wyników uczenia maszynowego](how-to-understand-automated-ml.md).

### <a name="data-featurization"></a>Cechowania danych

W każdym automatycznym doświadczeniu uczenia maszynowego Twoje dane są [automatycznie skalowane i znormalizowane](how-to-configure-auto-features.md#) , aby ułatwić *określonym* algorytmom, które są wrażliwe na funkcje różnej skali.  Można jednak również włączyć dodatkowe cechowania, takie jak brakujące wartości, które nie przypisywania, kodowania i transformacji.

Podczas konfigurowania eksperymentów w `AutoMLConfig` obiekcie można włączyć/wyłączyć ustawienie `featurization` . W poniższej tabeli przedstawiono zaakceptowane ustawienia dla cechowania w [klasie AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

|Konfiguracja cechowania | Opis |
| ------------- | ------------- |
|`"featurization": 'auto'`| Wskazuje, że w ramach przetwarzania wstępnego [guardrails danych i kroki cechowania](how-to-configure-auto-features.md#featurization) są wykonywane automatycznie. **Ustawienie domyślne**|
|`"featurization": 'off'`| Wskazuje, że krok cechowania nie powinien być wykonywany automatycznie.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Wskazuje dostosowany krok cechowania. [Dowiedz się, jak dostosować cechowania](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> Zautomatyzowane kroki cechowania uczenia maszynowego (normalizacja funkcji, obsługa brakujących danych, konwertowanie tekstu na liczbowe itp.) staje się częścią modelu źródłowego. Korzystając z modelu dla prognoz, te same kroki cechowania stosowane podczas uczenia są automatycznie stosowane do danych wejściowych.

### <a name="time-series-forecasting"></a>Prognozowanie szeregów czasowych
Zadanie szeregów czasowych `forecasting` wymaga dodatkowych parametrów w obiekcie Configuration:

1. `time_column_name`: Wymagany parametr, który definiuje nazwę kolumny w danych szkoleniowych zawierających prawidłową serię czasową.
1. `forecast_horizon`: Określa, ile okresów ma być prognozowanie. Zakres liczby całkowitej jest w jednostkach częstotliwości szeregów czasowych. Na przykład jeśli masz dane szkoleniowe z częstotliwością dzienną, możesz określić, jak daleko w dni ma być nadany model.
1. `time_series_id_column_names`: Definiuje kolumny, które w unikatowy sposób identyfikują serię czasową w danych, która ma wiele wierszy z tą samą sygnaturą czasową. Na przykład w przypadku prognozowania sprzedaży określonej marki według sklepu należy zdefiniować kolumny magazynu i marki jako identyfikatory szeregów czasowych. Dla każdego grupowania zostaną utworzone oddzielne prognozy. Jeśli identyfikatory szeregów czasowych nie są zdefiniowane, zakłada się, że zestaw danych jest jedną serią czasową.

Przykłady ustawień używanych poniżej można znaleźć w [notesie przykładowym](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

```python
# Setting Store and Brand as time series identifiers for training.
time_series_id_column_names = ['Store', 'Brand']
nseries = data.groupby(time_series_id_column_names).ngroups

# View the number of time series data with defined time series identifiers
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'time_series_id_column_names': time_series_id_column_names,
    'drop_column_names': ['logQuantity'],
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

### <a name="ensemble-configuration"></a><a name="ensemble"></a>Konfiguracja kompletna

Modele kompletów są domyślnie włączone i pojawiają się jako ostateczne iteracje przebiegu w zautomatyzowanym przebiegu uczenia maszynowego. Obecnie obsługiwane metody kompletu są głosune i ułożone na stosie. Głosowanie jest implementowane jako odmowa głosu przy użyciu średniej ważonej, a implementacja stosu korzysta z dwóch implementacji warstwy, w której pierwsza warstwa ma takie same modele jak zestaw do głosowania, a drugi model warstwy jest używany do znajdowania optymalnej kombinacji modeli z pierwszej warstwy. Jeśli używasz modeli ONNX **lub** włączono wyjaśnienie modelu, stos zostanie wyłączony i będzie można używać tylko głosu.

Istnieje wiele argumentów domyślnych, które mogą być podane jako `kwargs` obiekt w `AutoMLConfig` celu zmiany domyślnego zachowania DB.

* `ensemble_download_models_timeout_sec`: Podczas generowania modelu **VotingEnsemble** i **StackEnsemble** pobierane są liczne dopasowane modele z poprzednich uruchomień podrzędnych. Jeśli wystąpi ten błąd: `AutoMLEnsembleException: Could not find any models for running ensembling` , może być konieczne dostarczenie więcej czasu na pobranie modeli. Wartość domyślna to 300 sekund do pobierania tych modeli równolegle i nie obowiązuje limit maksymalnego limitu czasu. Skonfiguruj ten parametr o wyższej wartości niż 300 s, jeśli potrzebujesz więcej czasu. 

  > [!NOTE]
  >  Jeśli zostanie osiągnięty limit czasu i zostaną pobrane modele, ensembling kontynuuje pracę z dowolną liczbą modeli, które zostały pobrane. Nie jest wymagane, aby wszystkie modele musiały zostać pobrane do końca przed upływem tego limitu czasu.

Następujące parametry dotyczą tylko modeli **StackEnsemble** : 

* `stack_meta_learner_type`: meta-uczyć się model przeszkolony w danych wyjściowych poszczególnych modeli heterogenicznych. Domyślne meta uczy są `LogisticRegression` przeznaczone do zadań klasyfikacji (lub `LogisticRegressionCV` w przypadku włączenia wzajemnej walidacji) oraz `ElasticNet` dla zadań regresji/prognozowania (lub w `ElasticNetCV` przypadku włączenia weryfikacji krzyżowej). Ten parametr może być jednym z następujących ciągów:,,,,, `LogisticRegression` `LogisticRegressionCV` `LightGBMClassifier` `ElasticNet` `ElasticNetCV` `LightGBMRegressor` , lub `LinearRegression` .

* `stack_meta_learner_train_percentage`: określa proporcję zestawu szkoleniowego (podczas wybierania typu uczenia i walidacji), które mają zostać zarezerwowane do uczenia się. Wartość domyślna to `0.2` . 

* `stack_meta_learner_kwargs`: parametry opcjonalne do przekazania do inicjatora meta. Te parametry i typy parametrów stanowią duplikaty parametrów i typów parametrów z odpowiedniego konstruktora modelu i są przekazywane do konstruktora modelu.

Poniższy kod przedstawia przykład określania zachowania niestandardowego w `AutoMLConfig` obiekcie.

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

Szkolenia w ramach usługi jest domyślnie włączone, ale można je wyłączyć za pomocą `enable_voting_ensemble` `enable_stack_ensemble` parametrów logicznych i.

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

## <a name="run-experiment"></a>Uruchom eksperyment

Dla zautomatyzowanej tablicy należy utworzyć `Experiment` obiekt, który jest obiektem nazwanym w `Workspace` używanym do uruchamiania eksperymentów.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Prześlij eksperyment, aby uruchomić i wygenerować model. Przekaż `AutoMLConfig` do metody w `submit` celu wygenerowania modelu.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Zależności są najpierw instalowane na nowym komputerze.  Przed wyświetleniem danych wyjściowych może upłynąć do 10 minut.
>Ustawienie `show_output` powoduje `True` , że dane wyjściowe są wyświetlane w konsoli programu.

### <a name="exit-criteria"></a><a name="exit"></a>Kryteria wyjścia

Istnieje kilka opcji, które można zdefiniować, aby zakończyć eksperyment.
1. Brak kryteriów: Jeśli nie określisz żadnych parametrów zakończenia, eksperyment będzie kontynuowany do momentu, gdy nie zostanie wprowadzony żaden kolejny postęp w głównej metryki.
1. Zakończ po upływie długiego czasu: użycie `experiment_timeout_minutes` w ustawieniach pozwala określić, jak długo w minutach ma być kontynuowane eksperymentowanie.
1. Zakończ po osiągnięciu wyniku: użycie `experiment_exit_score` spowoduje zakończenie eksperymentu po osiągnięciu podstawowego wyniku metryki.

### <a name="explore-model-metrics"></a>Eksplorowanie metryk modelu

Możesz wyświetlić wyniki szkolenia w widżecie lub inline, jeśli jesteś w notesie. Aby uzyskać więcej informacji [, zobacz Śledzenie i szacowanie modeli](how-to-track-experiments.md#view-run-details) .

Aby uzyskać szczegółowe informacje na temat pobierania lub rejestrowania modelu wdrożenia w usłudze sieci Web, zobacz [jak i gdzie wdrożyć model](how-to-deploy-and-where.md).

## <a name="understand-automated-ml-models"></a>Zrozumienie zautomatyzowanych modeli ML

Każdy model wygenerowany przy użyciu zautomatyzowanej ML obejmuje następujące kroki:
+ Zautomatyzowana funkcja inżynierii (Jeśli `"featurization": 'auto'` )
+ Skalowanie/Normalizacja i algorytm przy użyciu wartości parametru

Czynimy to przezroczyste, aby uzyskać te informacje z fitted_model danych wyjściowych z zautomatyzowanej ML.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Zautomatyzowana funkcja inżynierii

Zapoznaj się z listą procesu przetwarzania wstępnego i [zautomatyzowanej funkcji]() , która występuje, gdy `"featurization": 'auto'` .

Rozważmy następujący przykład:
+ Istnieją cztery funkcje wejściowe: A (numeryczne), B (numeryczne), C (liczbowe), D (DateTime)
+ Funkcja liczbowa C została porzucona, ponieważ jest kolumną identyfikatora ze wszystkimi unikatowymi wartościami
+ Funkcje liczbowe a i B mają brakujące wartości i dlatego są przypisane do średniej
+ Funkcja DateTime D jest featurized do 11 różnych wbudowanych funkcji

Użyj tych 2 interfejsów API w pierwszym kroku dopasowanego modelu, aby poznać więcej.  Zapoznaj się z [tym przykładowym notesem](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ Interfejs API 1: `get_engineered_feature_names()` zwraca listę nazw przytworzonych funkcji.

  Użycie:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Ta lista zawiera wszystkie zaprojektowane nazwy funkcji.

  >[!Note]
  >Użyj elementu "timeseriestransformer" dla zadania = "prognozowanie", w przeciwnym razie użyj "datatransformer" dla zadania "regresja" lub "Klasyfikacja".

+ API 2: `get_featurization_summary()` zwraca podsumowanie cechowania dla wszystkich funkcji wejściowych.

  Użycie:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Użyj elementu "timeseriestransformer" dla zadania = "prognozowanie", w przeciwnym razie użyj "datatransformer" dla zadania "regresja" lub "Klasyfikacja".

  Dane wyjściowe:
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   Gdzie:

   |Dane wyjściowe|Definicja|
   |----|--------|
   |RawFeatureName|Podano nazwę funkcji/kolumny wejściowej z zestawu danych.|
   |TypeDetected|Wykryto typ danych funkcji wejściowej.|
   |Porzucony|Wskazuje, czy funkcja wejściowa została porzucona lub użyta.|
   |EngineeringFeatureCount|Liczba funkcji generowanych przez automatyczne transformacje inżynieryjnych funkcji.|
   |Przekształcenia|Lista transformacji zastosowanych do funkcji wejściowych do generowania przetworzonych funkcji.|
### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Skalowanie/Normalizacja i algorytm za pomocą wartości parametrów:

Aby zrozumieć wartości skalowania/normalizacji oraz algorytm/parametry dla potoku, użyj fitted_model. kroki. [Dowiedz się więcej o skalowaniu/normalizacji](how-to-configure-auto-features.md). Oto przykładowe dane wyjściowe:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Aby uzyskać więcej informacji, Użyj tej funkcji pomocnika: 

```python
from pprint import pprint


def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()


print_model(model)
```

Następujące przykładowe dane wyjściowe dotyczą potoku przy użyciu określonego algorytmu (LogisticRegression z RobustScalar, w tym przypadku).

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>Prawdopodobieństwo przewidywania klasy

Modele wytwarzane za pomocą zautomatyzowanej ML wszystkie mają obiekty otoki, które są dublowane z klasy pochodzenia Open Source. Większość obiektów otoki modelu klasyfikacji zwróconych przez zautomatyzowaną `predict_proba()` tablicę implementuje funkcję, która akceptuje przykład danych macierzy podobnej do tablicy lub rozrzedzonej (wartości X), i zwraca tablicę n-wymiarową każdego przykładu i odpowiadającą jej wartość prawdopodobieństwa klasy.

Przy założeniu, że pobrano najlepszy przebieg i zamontowany model przy użyciu tych samych wywołań z powyższych, można wywołać `predict_proba()` bezpośrednio z poziomu dopasowanego modelu, dostarczając `X_test` przykład w odpowiednim formacie, w zależności od typu modelu.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Jeśli model źródłowy nie obsługuje `predict_proba()` funkcji lub format jest niepoprawny, zostanie zgłoszony wyjątek specyficzny dla klasy modelu. Zobacz dokumenty referencyjne [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) i [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) , aby zapoznać się z przykładami tego, jak ta funkcja jest zaimplementowana dla różnych typów modeli.

<a name="explain"></a>

## <a name="model-interpretability"></a>Możliwość interpretowania modelu

Funkcja interpretacji modelu umożliwia zrozumienie, dlaczego modele są realizowane, oraz wartości ważności funkcji. Zestaw SDK zawiera różne pakiety umożliwiające włączenie funkcji interpretacji modelu zarówno w przypadku szkolenia, jak i czasu wnioskowania dla modeli lokalnych i wdrożonych.

Zapoznaj [się z](how-to-machine-learning-interpretability-automl.md) przykładami dotyczącymi sposobu włączania funkcji interpretacji w ramach zautomatyzowanych eksperymentów usługi Machine Learning.

Aby uzyskać ogólne informacje na temat sposobu włączenia wyjaśnień modelu i ważności funkcji w innych obszarach zestawu SDK poza funkcją automatycznego uczenia maszynowego, zapoznaj się z artykułem [koncepcja](how-to-machine-learning-interpretability.md) w zakresie interpretacji.

> [!NOTE]
> Model ForecastTCN nie jest obecnie obsługiwany przez klienta wyjaśnień. Ten model nie zwróci pulpitu nawigacyjnego wyjaśnienie, jeśli jest zwracany jako najlepszy model i nie obsługuje uruchomionych wyjaśnień na żądanie.

## <a name="next-steps"></a>Następne kroki

+ Dowiedz się więcej o tym [, jak i gdzie wdrożyć model](how-to-deploy-and-where.md).

+ Dowiedz się więcej o tym, [Jak szkolić model regresji z automatycznym uczeniem maszynowym](tutorial-auto-train-models.md) lub [jak korzystać z funkcji automatycznego uczenia maszynowego w ramach zasobu zdalnego](how-to-auto-train-remote.md).
+ Dowiedz się, jak uczenie wielu modeli za pomocą autoML w [akceleratorze rozwiązań wielu modeli](https://aka.ms/many-models).
