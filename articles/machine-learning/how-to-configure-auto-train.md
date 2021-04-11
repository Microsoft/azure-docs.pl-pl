---
title: Tworzenie zautomatyzowanych eksperymentów uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak definiować źródła danych, obliczenia i ustawienia konfiguracji dla zautomatyzowanych eksperymentów usługi Machine Learning.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperf-fy21q1, automl
ms.openlocfilehash: 12a6761ac2cd305e6ff949ffa59ee3bbdff1934d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732894"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurowanie eksperymentów zautomatyzowanego uczenia maszynowego w języku Python


W tym przewodniku dowiesz się, jak definiować różne ustawienia konfiguracji zautomatyzowanych eksperymentów uczenia maszynowego za pomocą [zestawu SDK Azure Machine Learning](/python/api/overview/azure/ml/intro). Automatyczne Uczenie maszynowe wybiera algorytm i parametry i tworzy model gotowy do wdrożenia. Istnieje kilka opcji, których można użyć do skonfigurowania zautomatyzowanych eksperymentów w usłudze Machine Learning.

Aby wyświetlić przykłady zautomatyzowanych eksperymentów dotyczących uczenia maszynowego, zobacz [Samouczek: uczenie modelu klasyfikacji przy użyciu zautomatyzowanego uczenia maszynowego](tutorial-auto-train-models.md) lub [modeli szkoleń z automatycznym uczeniem maszynowym w chmurze](how-to-auto-train-remote.md).

Opcje konfiguracji dostępne w ramach automatycznego uczenia maszynowego:

* Wybierz typ eksperymentu: klasyfikację, regresję lub prognozowanie szeregów czasowych
* Źródło danych, formaty i pobieranie danych
* Wybierz obiekt docelowy obliczeń: lokalny lub zdalny
* Ustawienia zautomatyzowanego eksperymentu w usłudze Machine Learning
* Uruchamianie eksperymentu zautomatyzowanego uczenia maszynowego
* Eksplorowanie metryk modelu
* Zarejestruj i Wdróż model

Jeśli wolisz nie używać kodu, możesz również [utworzyć automatyczne eksperymenty uczenia maszynowego w programie Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule należy 
* Obszar roboczy usługi Azure Machine Learning. Aby utworzyć obszar roboczy, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

* Zainstalowano Azure Machine Learning Python SDK.
    Aby zainstalować zestaw SDK, można 
    * Utwórz wystąpienie obliczeniowe, które automatycznie zainstaluje zestaw SDK i jest wstępnie skonfigurowany dla przepływów pracy usługi ML. Aby uzyskać więcej informacji [, zobacz Tworzenie wystąpienia obliczeniowego Azure Machine Learning i zarządzanie nim](how-to-create-manage-compute-instance.md) . 

    * [Zainstaluj `automl` pakiet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment), który obejmuje [domyślną instalację](/python/api/overview/azure/ml/install#default-install) zestawu SDK.

## <a name="select-your-experiment-type"></a>Wybieranie typu eksperymentu

Przed rozpoczęciem eksperymentu należy określić rodzaj rozwiązywanego problemu z uczeniem maszynowym. Automatyczne Uczenie maszynowe obsługuje typy zadań `classification` , `regression` i `forecasting` . Dowiedz się więcej o [typach zadań](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast).

Poniższy kod używa `task` parametru w `AutoMLConfig` konstruktorze, aby określić typ eksperymentu jako `classification` .

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Źródło i format danych

Zautomatyzowane uczenie maszynowe obsługuje dane, które znajdują się na komputerze lokalnym lub w chmurze, na przykład w usłudze Azure Blob Storage. Dane można odczytać do **Pandas Dataframe** lub **Azure Machine Learning TabularDataset**. [Dowiedz się więcej o zestawach danych](how-to-create-register-datasets.md).

Wymagania dotyczące danych szkoleniowych w usłudze Machine Learning:
- Dane muszą być w formie tabelarycznej.
- Wartość do przewidywania, kolumna docelowa, musi znajdować się w danych.

**W przypadku eksperymentów zdalnych** dane szkoleniowe muszą być dostępne ze zdalnych obliczeń. Zautomatyzowane uczenie maszynowe akceptuje [tabelaryczne zestawy danych usługi Azure Machine Learning](/python/api/azureml-core/azureml.data.tabulardataset) tylko podczas pracy na zdalnych zasobach obliczeniowych. 

Zestawy danych usługi Azure Machine Learning udostępniają następujące funkcje:

* Łatwo Transferuj dane ze źródeł plików statycznych lub adresów URL do obszaru roboczego.
* Udostępnianie danych dla skryptów szkoleniowych w przypadku korzystania z zasobów obliczeniowych w chmurze. Zobacz, [jak uczenie się z zestawami danych](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) , aby zapoznać się z przykładem użycia `Dataset` klasy do instalowania danych do zdalnego obiektu docelowego obliczeń.

Poniższy kod tworzy TabularDataset na podstawie adresu URL sieci Web. Zobacz [Create a TabularDatasets](how-to-create-register-datasets.md#create-a-tabulardataset) for Code — przykłady dotyczące tworzenia zestawów danych z innych źródeł, takich jak pliki lokalne i magazyny danych.

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**W przypadku lokalnych eksperymentów obliczeniowych** zalecamy Pandas dataframes w celu skrócenia czasu przetwarzania.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Szkolenia, walidacja i dane testowe

Możesz określić oddzielne **dane szkoleniowe i zestawy danych sprawdzania poprawności** bezpośrednio w `AutoMLConfig` konstruktorze. Dowiedz się więcej na temat [sposobu konfigurowania podziałów danych i krzyżowego sprawdzania poprawności](how-to-configure-cross-validation-data-splits.md) dla eksperymentów AutoML. 

Jeśli nie określisz jawnie `validation_data` `n_cross_validation` parametru lub, zautomatyzowanej sieci stosuje domyślne techniki, aby określić sposób sprawdzania poprawności. To obliczanie zależy od liczby wierszy w zestawie danych przypisanych do `training_data` parametru. 

|&nbsp;Rozmiar danych szkoleniowych &nbsp;| Technika walidacji |
|---|-----|
|**Więcej &nbsp; niż &nbsp; 20 000 &nbsp; wierszy**| Zastosowano podział danych szkolenia/walidacji. Wartość domyślna to przejęcie 10% początkowego zestawu danych szkoleniowych jako zestawu walidacji. Z kolei ten zestaw walidacji jest używany do obliczania metryk.
|**Mniejsze &nbsp; niż &nbsp; 20 000 &nbsp; wierszy**| Stosowana jest metoda weryfikacji krzyżowej. Domyślna liczba zagięć zależy od liczby wierszy. <br> **Jeśli zestaw danych jest mniejszy niż 1 000 wierszy**, używane są 10 zagięć. <br> **Jeśli wiersze są z zakresu od 1 000 do 20 000**, używane są trzy składowe.

W tej chwili należy podać własne **dane testowe** do oceny modelu. Przykładowy kod służący do wprowadzania własnych danych testowych do oceny modelu znajduje się w sekcji **test** [tego notesu Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb).

## <a name="compute-to-run-experiment"></a>Obliczenia w celu uruchomienia eksperymentu

Następnie określ, gdzie będzie szkolony model. Eksperyment treningowy zautomatyzowanego uczenia maszynowego można uruchomić w następujących opcjach obliczeniowych. Poznaj [zalety i wady lokalnych i zdalnych opcji obliczeniowych](concept-automated-ml.md#local-remote). 

* Komputer **lokalny** , taki jak pulpit lokalny lub laptop, zazwyczaj w przypadku małego zestawu danych i nadal w fazie eksploracji. Zapoznaj się z [tym notesem](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb) zawierającym przykład obliczeń lokalnych. 
 
* Maszyna **zdalna** w chmurze — [Azure Machine Learning zarządzane obliczenia](concept-compute-target.md#amlcompute) to usługa zarządzana, która umożliwia uczenie modeli uczenia maszynowego w klastrach maszyn wirtualnych platformy Azure. 

    Zobacz [ten notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb), aby zapoznać się z przykładem obliczeń zdalnych przy użyciu usługi Azure Machine Learning Managed Compute. 

* **Klaster Azure Databricks** w ramach subskrypcji platformy Azure. Więcej szczegółów można znaleźć w temacie [Konfigurowanie klastra Azure Databricks na potrzeby zautomatyzowanej ml](how-to-configure-databricks-automl-environment.md). Zobacz tę [witrynę w witrynie GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) , aby zapoznać się z przykładami notesów z Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Skonfiguruj ustawienia eksperymentu

Istnieje kilka opcji, których można użyć do skonfigurowania automatycznego eksperymentu uczenia maszynowego. Te parametry są ustawiane przez utworzenie wystąpienia `AutoMLConfig` obiektu. Zapoznaj się z pełną listą parametrów w [klasie AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) .

Oto niektóre przykłady:

1. Eksperyment klasyfikacji korzystający z AUC ważone jako Metryka podstawowa z limitem czasu eksperymentu w minutach ustawionym na 30 minut i 2 zgięcia wzajemnej walidacji.

   ```python
       automl_classifier=AutoMLConfig(task='classification',
                                      primary_metric='AUC_weighted',
                                      experiment_timeout_minutes=30,
                                      blocked_models=['XGBoostClassifier'],
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=2)
   ```
1. Poniższy przykład to eksperyment regresji ustawiony na koniec po 60 minutach z pięcioma zgięciami krzyżowymi.

   ```python
      automl_regressor = AutoMLConfig(task='regression',
                                      experiment_timeout_minutes=60,
                                      allowed_models=['KNN'],
                                      primary_metric='r2_score',
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=5)
   ```


1. Zadania prognozowania wymagają dodatkowej instalacji, aby uzyskać więcej informacji, zobacz artykuł [model prognozowania "autouczenie"](how-to-auto-train-forecast.md) . 

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

Automatyczne Uczenie maszynowe próbuje różne modele i algorytmy podczas procesu automatyzacji i dostrajania. Jako użytkownik nie ma potrzeby określania algorytmu. 

Trzy różne `task` wartości parametrów określają listę algorytmów lub modeli, które mają zostać zastosowane. Użyj `allowed_models` parametrów lub, `blocked_models` Aby kontynuować modyfikowanie iteracji z dostępnymi modelami do dołączania lub wykluczania. 

Poniższa tabela zawiera podsumowanie obsługiwanych modeli według typu zadania. 

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
[Klasyfikator średniej Perceptron](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)|[Gradient online z Regresorem](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) |[AutoARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Algorytm Bayesa Bayesa](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Szybka Regresor liniowa](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[Stochastycznego gradientu (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[Klasyfikator liniowy SVM](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)*||

### <a name="primary-metric"></a>Metryka podstawowa
`primary metric`Parametr określa metrykę, która ma być używana podczas uczenia modelu na potrzeby optymalizacji. Dostępne metryki, które można wybrać, są określane przez wybrany typ zadania, a w poniższej tabeli przedstawiono prawidłowe Podstawowe metryki dla każdego typu zadania.

Wybór głównej metryki dla zautomatyzowanej uczenia maszynowego w celu optymalizacji zależy od wielu czynników. Zalecamy podstawowe zagadnienie, aby wybrać metrykę, która najlepiej odpowiada Twoim potrzebom biznesowym. Następnie należy rozważyć, czy Metryka jest odpowiednia dla profilu zestawu danych (rozmiar danych, zakres, dystrybucja klas itp.).

Zapoznaj się z określonymi definicjami tych metryk w temacie [Omówienie zautomatyzowanych wyników uczenia maszynowego](how-to-understand-automated-ml.md).

|Klasyfikacja | Regresja | Prognozowanie szeregów czasowych
|--|--|--
|`accuracy`| `spearman_correlation` | `spearman_correlation`
|`AUC_weighted` | `normalized_root_mean_squared_error` | `normalized_root_mean_squared_error`
|`average_precision_score_weighted` | `r2_score` | `r2_score`
|`norm_macro_recall` | `normalized_mean_absolute_error` | `normalized_mean_absolute_error`
|`precision_score_weighted` |

### <a name="primary-metrics-for-classification-scenarios"></a>Podstawowe metryki dla scenariuszy klasyfikacji 

Metryki po stronie progowej, takie jak `accuracy` ,, `average_precision_score_weighted` `norm_macro_recall` i `precision_score_weighted` mogą nie optymalizować dla zestawów danych, które są małe, mają bardzo duże pochylenie klas (niezrównoważone klasy) lub gdy oczekiwana wartość metryki jest bardzo bliska 0,0 lub 1,0. W takich przypadkach `AUC_weighted` może być lepszym wyborem dla metryki głównej. Po zakończeniu automatycznego uczenia maszynowego można wybrać model, który zostanie utworzony na podstawie metryki najlepiej dopasowanej do potrzeb Twojej firmy.

| Metric | Przykładowe przypadki użycia: |
| ------ | ------- |
| `accuracy` | Klasyfikacja obrazu, analiza tonacji, Prognoza zmian |
| `AUC_weighted` | Wykrywanie oszustw, klasyfikacja obrazu, wykrywanie anomalii/wykrywanie spamu |
| `average_precision_score_weighted` | Analiza tonacji |
| `norm_macro_recall` | Prognoza zmian |
| `precision_score_weighted` |  |

### <a name="primary-metrics-for-regression-scenarios"></a>Podstawowe metryki dla scenariuszy regresji

Metryki takie jak `r2_score` i `spearman_correlation` mogą lepiej reprezentować jakość modelu, gdy skala wartości do przewidywania obejmuje wiele zamówień wielkości. W przypadku oszacowania wynagrodzeń na wystąpieniach, gdzie wiele osób ma wynagrodzenie $20 000 do $100 000, ale skala jest bardzo wysoka z niektórymi zarobkami w zakresie $100 mln. 

`normalized_mean_absolute_error``normalized_root_mean_squared_error`w takim przypadku w tym przypadku błąd przewidywania $20 000 jest taki sam dla procesu roboczego z wynagrodzeniem $30k jako proces roboczy, który ma wartość $20 mln. W rzeczywistości przewidywanie tylko $20 000 wyłączone z wynagrodzenia $20 mln jest bardzo bliskie (mała 0,1% różnicy względnej), natomiast $20 000 off from $30k nie jest bliski (duża wartość 67%). `normalized_mean_absolute_error` i `normalized_root_mean_squared_error` są przydatne, gdy wartości do przewidywania są w podobnej skali.

| Metric | Przykładowe przypadki użycia: |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Prognoza cenowa (dom/produkt/Porada), przegląd prognozowania oceny |
| `r2_score` | Opóźnienie linii lotniczej, oszacowanie wynagrodzeń, czas rozpoznawania usterek |
| `normalized_mean_absolute_error` |  |

### <a name="primary-metrics-for-time-series-forecasting-scenarios"></a>Podstawowe metryki dla scenariuszy prognozowania szeregów czasowych

Zobacz uwagi dotyczące regresji powyżej.

| Metric | Przykładowe przypadki użycia: |
| ------ | ------- |
| `spearman_correlation` | |
| `normalized_root_mean_squared_error` | Prognozowanie cen (prognozowanie), optymalizacja spisu, prognozowanie popytu |
| `r2_score` | Prognozowanie cen (prognozowanie), optymalizacja spisu, prognozowanie popytu |
| `normalized_mean_absolute_error` | |

### <a name="data-featurization"></a>Cechowania danych

W każdym automatycznym doświadczeniu uczenia maszynowego Twoje dane są automatycznie skalowane i znormalizowane, aby ułatwić *określonym* algorytmom, które są wrażliwe na funkcje różnej skali. Takie skalowanie i normalizacja są określane jako cechowania. Zobacz [cechowania in AutoML](how-to-configure-auto-features.md#) , aby uzyskać szczegółowe informacje i przykłady kodu. 

Podczas konfigurowania eksperymentów w `AutoMLConfig` obiekcie można włączyć/wyłączyć ustawienie `featurization` . W poniższej tabeli przedstawiono zaakceptowane ustawienia dla cechowania w [obiekcie AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Konfiguracja cechowania | Opis |
| ------------- | ------------- |
|`"featurization": 'auto'`| Wskazuje, że w ramach przetwarzania wstępnego [guardrails danych i kroki cechowania](how-to-configure-auto-features.md#featurization) są wykonywane automatycznie. **Ustawienie domyślne**.|
|`"featurization": 'off'`| Wskazuje, że krok cechowania nie powinien być wykonywany automatycznie.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Wskazuje dostosowany krok cechowania. [Dowiedz się, jak dostosować cechowania](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> Zautomatyzowane kroki cechowania uczenia maszynowego (normalizacja funkcji, obsługa brakujących danych, konwertowanie tekstu na liczbowe itp.) staje się częścią modelu źródłowego. Korzystając z modelu dla prognoz, te same kroki cechowania stosowane podczas uczenia są automatycznie stosowane do danych wejściowych.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a>Konfiguracja kompletna

Modele kompletów są domyślnie włączone i pojawiają się jako ostateczne iteracje przebiegu w AutoML. Obecnie **VotingEnsemble** i **StackEnsemble** są obsługiwane. 

Głosowanie implementuje rozdanie głosu, które używa średnich ważonych. Implementacja stosu korzysta z dwóch implementacji warstwy, w której pierwsza warstwa ma takie same modele jak kompletna prawa głosu, a drugi model warstwy służy do znajdowania optymalnej kombinacji modeli z pierwszej warstwy. 

W przypadku korzystania z modeli ONNX **lub** z włączonym wyjaśnieniem modelu stos jest wyłączony i wykorzystane jest tylko głosowanie.

Szkolenia typu "kompletność" można wyłączyć za pomocą `enable_voting_ensemble` `enable_stack_ensemble` parametrów i.

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

Aby zmienić domyślne zachowanie funkcji, istnieje wiele argumentów domyślnych, które mogą być dostarczane jako `kwargs` `AutoMLConfig` obiekty.

> [!IMPORTANT]
>  Następujące parametry nie są jawnymi parametrami klasy AutoMLConfig. 

* `ensemble_download_models_timeout_sec`: Podczas generowania modelu **VotingEnsemble** i **StackEnsemble** pobierane są liczne dopasowane modele z poprzednich uruchomień podrzędnych. Jeśli wystąpi ten błąd: `AutoMLEnsembleException: Could not find any models for running ensembling` , może być konieczne dostarczenie więcej czasu na pobranie modeli. Wartość domyślna to 300 sekund do pobierania tych modeli równolegle i nie obowiązuje limit maksymalnego limitu czasu. Skonfiguruj ten parametr o wyższej wartości niż 300 s, jeśli potrzebujesz więcej czasu. 

  > [!NOTE]
  >  Jeśli zostanie osiągnięty limit czasu i zostaną pobrane modele, ensembling kontynuuje pracę z dowolną liczbą modeli, które zostały pobrane. Nie jest wymagane, aby wszystkie modele musiały zostać pobrane do końca przed upływem tego limitu czasu.

Następujące parametry dotyczą tylko modeli **StackEnsemble** : 

* `stack_meta_learner_type`: meta-uczyć się model przeszkolony w danych wyjściowych poszczególnych modeli heterogenicznych. Domyślne meta uczy są `LogisticRegression` przeznaczone do zadań klasyfikacji (lub `LogisticRegressionCV` w przypadku włączenia wzajemnej walidacji) oraz `ElasticNet` dla zadań regresji/prognozowania (lub w `ElasticNetCV` przypadku włączenia weryfikacji krzyżowej). Ten parametr może być jednym z następujących ciągów:,,,,, `LogisticRegression` `LogisticRegressionCV` `LightGBMClassifier` `ElasticNet` `ElasticNetCV` `LightGBMRegressor` , lub `LinearRegression` .

* `stack_meta_learner_train_percentage`: określa proporcję zestawu szkoleniowego (podczas wybierania typu uczenia i walidacji), które mają zostać zarezerwowane do uczenia się. Wartość domyślna to `0.2`. 

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

<a name="exit"></a> 

### <a name="exit-criteria"></a>Kryteria wyjścia

Istnieje kilka opcji, które można zdefiniować w AutoMLConfig, aby zakończyć eksperyment.

|Kryteria| description (opis)
|----|----
Brak &nbsp; kryteriów | Jeśli nie określisz żadnych parametrów zakończenia, eksperyment kontynuuje działanie, dopóki nie zostanie wprowadzony kolejny postęp w głównej metryce.
Po upływie &nbsp; &nbsp; dłuższego &nbsp; &nbsp; czasu| Użyj `experiment_timeout_minutes` Ustawienia w ustawieniach, aby określić czas, w ciągu którego eksperyment powinien nadal działać. <br><br> Aby zapobiec błędom przekroczenia limitu czasu eksperymentu, istnieje co najmniej 15 minut lub 60 minut, jeśli rozmiar wiersza według kolumny przekracza 10 000 000.
&nbsp; &nbsp; &nbsp; &nbsp; Osiągnięto wynik| Użyj `experiment_exit_score` kończenia eksperymentu po osiągnięciu określonego podstawowego wyniku metryki.

## <a name="run-experiment"></a>Uruchom eksperyment

Dla zautomatyzowanej tablicy należy utworzyć `Experiment` obiekt, który jest obiektem nazwanym w `Workspace` używanym do uruchamiania eksperymentów.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
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

### <a name="multiple-child-runs-on-clusters"></a>Wiele przebiegów podrzędnych w klastrach

Na klastrze, na którym jest już uruchomiony inny eksperyment, można wykonywać zautomatyzowane uruchomienia eksperymentów z systemem. Jednak czas zależy od liczby węzłów w klastrze, a jeśli te węzły są dostępne do uruchamiania innego eksperymentu.

Każdy węzeł w klastrze działa jako indywidualna maszyna wirtualna (VM), która może wykonywać pojedynczy przebieg szkoleniowy; w przypadku zautomatyzowanej ML oznacza to uruchomienie podrzędne. Jeśli wszystkie węzły są zajęte, nowy eksperyment zostanie umieszczony w kolejce. Jeśli jednak istnieją wolne węzły, nowy eksperyment uruchomi automatyczne uruchamianie elementów podrzędnych ML w ramach dostępnych węzłów/maszyn wirtualnych.

Aby ułatwić zarządzanie uruchomieniami podrzędnymi i kiedy można je wykonać, zalecamy utworzenie dedykowanego klastra na eksperyment i dopasowanie `max_concurrent_iterations` do liczby węzłów w klastrze. W ten sposób można używać wszystkich węzłów klastra w tym samym czasie z liczbą współbieżnych uruchomień/iteracji podrzędnych.

Skonfiguruj  `max_concurrent_iterations` w `AutoMLConfig` obiekcie. Jeśli nie jest skonfigurowany, domyślnie dozwolone jest tylko jedno współbieżne uruchomienie/iteracja na eksperymentie.  

## <a name="explore-models-and-metrics"></a>Eksplorowanie modeli i metryk

Opcja zautomatyzowanej sieci umożliwia monitorowanie i ocenę wyników szkoleniowych. 

* Możesz wyświetlić wyniki szkolenia w widżecie lub inline, jeśli jesteś w notesie. Aby uzyskać więcej informacji [, zobacz Jak monitorować automatyczne przebiegi](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs) maszyn.

* Aby zapoznać się z definicjami i przykładowymi wykresami wydajności i metrykami podanymi dla każdego przebiegu, zobacz [ocenę zautomatyzowanych wyników eksperymentu w usłudze Machine Learning](how-to-understand-automated-ml.md) . 

* Aby uzyskać podsumowanie cechowania i zrozumieć, jakie funkcje zostały dodane do określonego modelu, zobacz [cechowania przezroczystość](how-to-configure-auto-features.md#featurization-transparency). 

Można wyświetlić parametry, metody skalowania i normalizacji oraz algorytm stosowany do konkretnej zautomatyzowanej sieci, uruchamiając następujące niestandardowe rozwiązanie kodu. 

Poniżej definiuje metodę niestandardową, `print_model()` która drukuje parametry każdego kroku potoku szkolenia automatu ml.
 
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

W przypadku przebiegu lokalnego lub zdalnego, który został właśnie przesłany i przeszkolony z poziomu tego samego notesu eksperymentu, można przekazać najlepszy model przy użyciu `get_output()` metody. 

```python
best_run, fitted_model = run.get_output()
print(best_run)
         
print_model(fitted_model)
```

Następujące dane wyjściowe wskazują, że:
 
* Technika StandardScalerWrapper została użyta do skalowania i normalizacji danych przed szkoleniem.

* Algorytm XGBoostClassifier został zidentyfikowany jako najlepszy przebieg, a także zawiera wartości parametrów. 

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

W przypadku istniejącego przebiegu z innego eksperymentu w obszarze roboczym Uzyskaj konkretny identyfikator uruchomienia, który chcesz zbadać i przekazać do `print_model()` metody. 

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
> Algorytmy zautomatyzowanej sieci mają nieodłączną losowość, która może spowodować nieznaczne wahania w końcowym wyniku metryk, na przykład dokładność. Zautomatyzowanej ML wykonuje również operacje na danych, takie jak podzielenie testów pociągowych, podzielone lub krzyżowe sprawdzanie poprawności w razie potrzeby. Dlatego w przypadku uruchamiania eksperymentu z tymi samymi ustawieniami konfiguracji i metryką podstawową wiele razy prawdopodobnie zobaczysz zmiany w przypadku wszystkich eksperymentów końcowych metryk, ze względu na te czynniki. 

## <a name="register-and-deploy-models"></a>Rejestrowanie i wdrażanie modeli

Możesz zarejestrować model, aby można było wrócić do niego do późniejszego użycia. 

Aby zarejestrować model na podstawie zautomatyzowanego przebiegu ML, użyj [`register_model()`](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) metody. 

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


Aby uzyskać szczegółowe informacje na temat sposobu tworzenia konfiguracji wdrożenia i wdrażania zarejestrowanego modelu w usłudze sieci Web, zobacz [jak i gdzie wdrożyć model](how-to-deploy-and-where.md?tabs=python#define-a-deployment-configuration).

> [!TIP]
> W przypadku zarejestrowanych modeli wdrożenie jednym kliknięciem jest dostępne za pośrednictwem [Azure Machine Learning Studio](https://ml.azure.com). Zobacz [jak wdrożyć zarejestrowane modele z programu Studio](how-to-use-automated-ml-for-ml-models.md#deploy-your-model). 
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

+ Dowiedz się, jak uczenie wielu modeli za pomocą AutoML w [akceleratorze rozwiązań wielu modeli](https://aka.ms/many-models).

+ [Rozwiązywanie problemów dotyczących zautomatyzowanych eksperymentów ml](how-to-troubleshoot-auto-ml.md). 
