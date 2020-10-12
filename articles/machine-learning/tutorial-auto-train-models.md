---
title: 'Samouczek regresji: automatyczna ML'
titleSuffix: Azure Machine Learning
description: W ramach tego samouczka nauczysz się generować model uczenia maszynowego przy użyciu funkcji automatycznego uczenia maszynowego. Korzystając z usługi Azure Machine Learning, można w sposób zautomatyzowany wykonywać wstępne przetwarzanie danych, wybierać algorytmy i hiperparametry.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.date: 08/14/2020
ms.custom: devx-track-python
ms.openlocfilehash: cf6616dcc3935946ad4a7213263bb20281d25354
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90896785"
---
# <a name="tutorial-use-automated-machine-learning-to-predict-taxi-fares"></a>Samouczek: Używanie automatycznego uczenia maszynowego do przewidywania opłat za taksówkę


W tym samouczku użyjesz automatycznego uczenia maszynowego w Azure Machine Learning, aby utworzyć model regresji do przewidywania cen opłat za taksówkę NYC. Ten proces akceptuje dane szkoleniowe i ustawienia konfiguracji, a następnie automatycznie wykonuje iterację przez kombinacje różnych metod normalizacji/normalizacji funkcji, modeli i parametrów z parametrami, aby dotrzeć do najlepszego modelu.

![Diagram przepływu](./media/tutorial-auto-train-models/flow2.png)

W tym samouczku przedstawiono następujące zadania:

> [!div class="checklist"]
> * Pobieranie, przekształcanie i czyszczenie danych przy użyciu usługi Azure Open DataSets
> * Uczenie zautomatyzowanego modelu regresji uczenia maszynowego
> * Oblicz dokładność modelu

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję](https://aka.ms/AMLFree) Azure Machine Learning dzisiaj.

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [Samouczek instalacji](tutorial-1st-experiment-sdk-setup.md) , jeśli nie masz jeszcze obszaru roboczego Azure Machine Learning lub maszyny wirtualnej notesu.
* Po zakończeniu pracy z samouczkiem Instalatora Otwórz Notes *samouczków/Regression-automl-NYC-Taxi-Data/Regression-Automated-ml. ipynb* przy użyciu tego samego serwera notesu.

Ten samouczek jest również dostępny w witrynie [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) , jeśli chcesz uruchomić go w [środowisku lokalnym](how-to-configure-environment.md#local). Uruchom, `pip install azureml-sdk[automl] azureml-opendatasets azureml-widgets` Aby pobrać wymagane pakiety.

## <a name="download-and-prepare-data"></a>Pobieranie i przygotowywanie danych

Zaimportuj niezbędne pakiety. Pakiet Open DataSets zawiera klasę reprezentującą każde źródło danych ( `NycTlcGreen` na przykład) do łatwego filtrowania parametrów daty przed pobraniem.

```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Zacznij od utworzenia ramki Dataframe do przechowywania danych z taksówki. W przypadku pracy w środowisku innym niż platforma Spark otwieranie zestawów danych umożliwia tylko pobieranie z określonych klas tylko jednego miesiąca z niektórymi klasami `MemoryError` .

Aby pobrać dane dotyczące taksówki, można iteracyjnie pobrać jeden miesiąc i przed dołączeniem go do `green_taxi_df` losowo próbkowanych rekordów 2 000 z każdego miesiąca, aby uniknąć przeładowania ramek danych. Następnie Wyświetl podgląd danych.


```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2015","%m/%d/%Y")
end = datetime.strptime("1/31/2015","%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
```

|vendorID| lpepPickupDatetime|  lpepDropoffDatetime|    passengerCount| tripDistance|   puLocationId|   doLocationId|   pickupLongitude|    pickupLatitude| dropoffLongitude    |...|   płatnośćtype |fareAmount |Ultimate| mtaTax| improvementSurcharge|   tipAmount|  tollsAmount|    ehailFee|   totalAmount|    czas trwania|
|----|----|----|----|----|----|---|--|---|---|---|----|----|----|--|---|----|-----|----|----|----|----|---|
|131969|2|2015-01-11 05:34:44|2015-01-11 05:45:03|3|4,84|Brak|Brak|-73,88|40,84|-73,94|...|2|15,00|0,50|0,50|0.3|0,00|0,00|NaN|16,30|1,00
|1129817|2|2015-01-20 16:26:29|2015-01-20 16:30:26|1|0,69|Brak|Brak|-73,96|40,81|-73,96|...|2|4,50|1,00|0,50|0.3|0,00|0,00|NaN|6,30|1,00
|1278620|2|2015-01-01 05:58:10|2015-01-01 06:00:55|1|0,45|Brak|Brak|-73,92|40,76|-73,91|...|2|4,00|0,00|0,50|0.3|0,00|0,00|NaN|4,80|1,00
|348430|2|2015-01-17 02:20:50|2015-01-17 02:41:38|1|0,00|Brak|Brak|-73,81|40,70|-73,82|...|2|12,50|0,50|0,50|0.3|0,00|0,00|NaN|13,80|1,00
1269627|1|2015-01-01 05:04:10|2015-01-01 05:06:23|1|0,50|Brak|Brak|-73,92|40,76|-73,92|...|2|4,00|0,50|0,50|0|0,00|0,00|NaN|5,00|1,00
|811755|1|2015-01-04 19:57:51|2015-01-04 20:05:45|2|1.10|Brak|Brak|-73,96|40,72|-73,95|...|2|6,50|0,50|0,50|0.3|0,00|0,00|NaN|7,80|1,00
|737281|1|2015-01-03 12:27:31|2015-01-03 12:33:52|1|0,90|Brak|Brak|-73,88|40,76|-73,87|...|2|6,00|0,00|0,50|0.3|0,00|0,00|NaN|6,80|1,00
|113951|1|2015-01-09 23:25:51|2015-01-09 23:39:52|1|3,30|Brak|Brak|-73,96|40,72|-73,91|...|2|12,50|0,50|0,50|0.3|0,00|0,00|NaN|13,80|1,00
|150436|2|2015-01-11 17:15:14|2015-01-11 17:22:57|1|1,19|Brak|Brak|-73,94|40,71|-73,95|...|1|7,00|0,00|0,50|0.3|1,75|0,00|NaN|9,55|1,00
|432136|2|2015-01-22 23:16:33 2015-01-22 23:20:13 1 0,65|Brak|Brak|-73,94|40,71|-73,94|...|2|5,00|0,50|0,50|0.3|0,00|0,00|NaN|6,30|1,00

Teraz, gdy początkowe dane są ładowane, zdefiniuj funkcję do tworzenia różnych funkcji opartych na czasie w polu Data/godzina pobrania. Spowoduje to utworzenie nowych pól o numerze miesiąca, dzień miesiąca, dzień tygodnia i godzinie dnia i umożliwi modelowi współczynniki w sezonowości opartym na czasie. Użyj `apply()` funkcji w ramce Dataframe, aby iteracyjnie zastosować `build_time_features()` funkcję do każdego wiersza w danych z taksówką.

```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day))

green_taxi_df[["month_num", "day_of_month","day_of_week", "hour_of_day"]] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
```

|vendorID| lpepPickupDatetime|  lpepDropoffDatetime|    passengerCount| tripDistance|   puLocationId|   doLocationId|   pickupLongitude|    pickupLatitude| dropoffLongitude    |...|   płatnośćtype|fareAmount  |Ultimate| mtaTax| improvementSurcharge|   tipAmount|  tollsAmount|    ehailFee|   totalAmount|czas trwania|month_num|day_of_month|day_of_week|hour_of_day
|----|----|----|----|----|----|---|--|---|---|---|----|----|----|--|---|----|-----|----|----|----|----|---|----|----|----
|131969|2|2015-01-11 05:34:44|2015-01-11 05:45:03|3|4,84|Brak|Brak|-73,88|40,84|-73,94|...|2|15,00|0,50|0,50|0.3|0,00|0,00|NaN|16,30|1,00|1|11|6|5
|1129817|2|2015-01-20 16:26:29|2015-01-20 16:30:26|1|0,69|Brak|Brak|-73,96|40,81|-73,96|...|2|4,50|1,00|0,50|0.3|0,00|0,00|NaN|6,30|1,00|1|20|1|16
|1278620|2|2015-01-01 05:58:10|2015-01-01 06:00:55|1|0,45|Brak|Brak|-73,92|40,76|-73,91|...|2|4,00|0,00|0,50|0.3|0,00|0,00|NaN|4,80|1,00|1|1|3|5
|348430|2|2015-01-17 02:20:50|2015-01-17 02:41:38|1|0,00|Brak|Brak|-73,81|40,70|-73,82|...|2|12,50|0,50|0,50|0.3|0,00|0,00|NaN|13,80|1,00|1|17|5|2
1269627|1|2015-01-01 05:04:10|2015-01-01 05:06:23|1|0,50|Brak|Brak|-73,92|40,76|-73,92|...|2|4,00|0,50|0,50|0|0,00|0,00|NaN|5,00|1,00|1|1|3|5
|811755|1|2015-01-04 19:57:51|2015-01-04 20:05:45|2|1.10|Brak|Brak|-73,96|40,72|-73,95|...|2|6,50|0,50|0,50|0.3|0,00|0,00|NaN|7,80|1,00|1|4|6|19
|737281|1|2015-01-03 12:27:31|2015-01-03 12:33:52|1|0,90|Brak|Brak|-73,88|40,76|-73,87|...|2|6,00|0,00|0,50|0.3|0,00|0,00|NaN|6,80|1,00|1|3|5|12
|113951|1|2015-01-09 23:25:51|2015-01-09 23:39:52|1|3,30|Brak|Brak|-73,96|40,72|-73,91|...|2|12,50|0,50|0,50|0.3|0,00|0,00|NaN|13,80|1,00|1|9|4|23
|150436|2|2015-01-11 17:15:14|2015-01-11 17:22:57|1|1,19|Brak|Brak|-73,94|40,71|-73,95|...|1|7,00|0,00|0,50|0.3|1,75|0,00|NaN|9,55|1,00|1|11|6|17
|432136|2|2015-01-22 23:16:33 2015-01-22 23:20:13 1 0,65|Brak|Brak|-73,94|40,71|-73,94|...|2|5,00|0,50|0,50|0.3|0,00|0,00|NaN|6,30|1,00|1|22|3|23

Usuń niektóre kolumny, które nie są potrzebne do szkolenia ani tworzenia dodatkowych funkcji.

```python
columns_to_remove = ["lpepPickupDatetime", "lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df.head(5)
```

### <a name="cleanse-data"></a>Oczyszczanie danych

Uruchom `describe()` funkcję w nowej ramce Dataframe, aby wyświetlić statystyki podsumowania dla każdego pola.

```python
green_taxi_df.describe()
```

|vendorID|passengerCount|tripDistance|pickupLongitude|pickupLatitude|dropoffLongitude|dropoffLatitude|  totalAmount|month_num day_of_month|day_of_week|hour_of_day
|----|----|---|---|----|---|---|---|---|---|---|---
|count|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00|48000,00
|średnia|1,78|1,37|2,87|-73,83|40,69|-73,84|40,70|14,75|6,50|15,13|3,27|13,52
|odchylenie standardowe|0,41|1,04|2,93|2,76|1,52|2,61|1,44|12,08|3,45|8,45|1,95|6,83
|min|1,00|0,00|0,00|-74,66|0,00|-74,66|0,00|-300,00|1,00|1,00|0,00|0,00
|25%|2,00|1,00|1.06|-73,96|40,70|-73,97|40,70|7,80|3,75|8,00|2,00|9,00
|50%|2,00|1,00|1,90|-73,94|40,75|-73,94|40,75|11,30|6,50|15,00|3,00|15,00
|75%|2,00|1,00|3,60|-73,92|40,80|-73,91|40,79|17,80|9,25|22,00|5,00|19,00
|max|2,00|9,00|97,57|0,00|41,93|0,00|41,94|450,00|12,00|30,00|6,00|23,00


Ze statystyk podsumowujących widać, że istnieje kilka pól, które mają wartości odstających lub wartość, które spowodują zmniejszenie dokładności modelu. Najpierw odfiltruj pola lat/Long, aby znajdować się w granicach obszaru Manhattan. Spowoduje to odfiltrowanie dłuższych podróży lub podróży, które znajdują się w odniesieniu do ich relacji z innymi funkcjami.

Dodatkowo należy filtrować `tripDistance` pole tak, aby było większe niż zero, ale mniej niż 31 kilometrów (Haversine odległość między dwoma parami lat/Long). Eliminuje to długotrwałe podróże, które mają niespójny koszt podróży.

W końcu `totalAmount` pole ma wartości ujemne dla opłat za taksówkę, które nie mają sensu w kontekście naszego modelu, a `passengerCount` pole ma złe dane o wartościach minimalnych równych zero.

Odfiltruj te anomalie przy użyciu funkcji zapytania, a następnie usuń kilka ostatnich kolumn niezbędnych do szkolenia.


```python
final_df = green_taxi_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>=0.25 and tripDistance<31")
final_df = final_df.query("passengerCount>0 and totalAmount>0")

columns_to_remove_for_training = ["pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Ponownie wywołaj `describe()` dane, aby upewnić się, że czyszczenie działało zgodnie z oczekiwaniami. Masz teraz przygotowany i wyczyszczony zestaw danych o taksówkach, dniach wolnych i pogoda, które mają być używane do szkolenia modelu uczenia maszynowego.

```python
final_df.describe()
```

## <a name="configure-workspace"></a>Konfigurowanie obszaru roboczego

Utwórz obiekt obszaru roboczego na podstawie istniejącego obszaru roboczego. [Obszar roboczy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) to Klasa, która akceptuje informacje o subskrypcji i zasobach platformy Azure. Tworzy ona również zasób w chmurze służący do monitorowania i śledzenia przebiegów modelu. `Workspace.from_config()` Odczytuje **config.js** pliku i ładuje szczegóły uwierzytelniania do obiektu o nazwie `ws` . Obiekt `ws` jest używany w kodzie w tym samouczku.

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
```

## <a name="split-the-data-into-train-and-test-sets"></a>Podział danych na zestawy treningowe i testowe

Podziel dane na zestawy szkoleniowe i testowe przy użyciu `train_test_split` funkcji w `scikit-learn` bibliotece. Ta funkcja dzieli dane na zestaw danych x (**Features**) na potrzeby szkolenia modeli oraz zestawu danych y (**wartości do przewidywania**) na potrzeby testowania.

Parametr `test_size` określa procent danych przydzielanych do testowania. `random_state`Parametr ustawia inicjator dla generatora losowego, dzięki czemu podziały testu pociąga są deterministyczne.

```python
from sklearn.model_selection import train_test_split

x_train, x_test = train_test_split(final_df, test_size=0.2, random_state=223)
```

Celem tego etapu jest uzyskanie punktów danych do przetestowania ukończono modelu, które nie były używane do jego wytrenowania. Pozwoli to zmierzyć rzeczywistą dokładność.

Innymi słowy, dobrze wytrenowany model powinien umożliwiać dokładne prognozowanie na podstawie danych, które jeszcze się nie pojawiły. Masz teraz dane przygotowane do samodzielnego uczenia modelu uczenia maszynowego.

## <a name="automatically-train-a-model"></a>Automatyczne trenowanie modelu

Aby przeprowadzić automatyczne trenowanie modelu, wykonaj następujące czynności:
1. Zdefiniuj ustawienia przebiegu eksperymentu. Dołącz do konfiguracji dane treningowe i zmodyfikuj ustawienia, które sterują procesem treningu.
1. Prześlij eksperyment do strojenia modelu. Po przesłaniu eksperymentu proces wykonuje iterację z użyciem różnych algorytmów uczenia maszynowego i ustawień hiperparametrycznych z uwzględnieniem zdefiniowanych ograniczeń. Na podstawie zoptymalizowanej metryki dokładności jest wybierany model o najlepszym dopasowaniu.

### <a name="define-training-settings"></a>Definiowanie ustawień szkoleniowych

Zdefiniuj parametr eksperymentu i ustawienia modelu dla szkolenia. Wyświetl pełną listę [ustawień](how-to-configure-auto-train.md). Przesłanie eksperymentu z tymi ustawieniami domyślnymi zajmie około 5-20 minut, ale jeśli chcesz skrócić czas wykonywania, Zmniejsz `experiment_timeout_hours` parametr.

|Właściwość| Wartość w ramach tego samouczka |Opis|
|----|----|---|
|**iteration_timeout_minutes**|2|Limit czasu w minutach dla każdej iteracji. Zmniejszenie tej wartości powoduje skrócenie całkowitego czasu wykonywania.|
|**experiment_timeout_hours**|0.3|Maksymalny czas (w godzinach), przez jaki połączone wszystkie iteracje mogą upłynąć przed zakończeniem eksperymentu.|
|**enable_early_stopping**|Prawda|Oflaguj, aby włączyć wczesne zakończenie, jeśli wynik nie zostanie ulepszony w krótkim czasie.|
|**primary_metric**| spearman_correlation | Metryka, który ma być optymalizowana. Na podstawie tej metryki zostanie wybrany model o najlepszym dopasowaniu.|
|**cechowania**| auto | Przy użyciu **opcji**autoeksperymenty mogą wstępnie przetwarzać dane wejściowe (obsługujące brakujące dane, konwertowanie tekstu na liczbowe itd.)|
|**verbosity**| logging.INFO | Steruje poziomem rejestrowania.|
|**n_cross_validations**|5|Liczba podziałów krzyżowego sprawdzania poprawności w przypadku nieokreślenia danych weryfikacji.|

```python
import logging

automl_settings = {
    "iteration_timeout_minutes": 2,
    "experiment_timeout_hours": 0.3,
    "enable_early_stopping": True,
    "primary_metric": 'spearman_correlation',
    "featurization": 'auto',
    "verbosity": logging.INFO,
    "n_cross_validations": 5
}
```

Użyj zdefiniowanych ustawień szkoleniowych jako `**kwargs` parametru do `AutoMLConfig` obiektu. Ponadto określ dane treningowe i typ modelu — w tym przypadku `regression`.

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             training_data=x_train,
                             label_column_name="totalAmount",
                             **automl_settings)
```

> [!NOTE]
> Zautomatyzowane kroki wstępnego przetwarzania w usłudze Machine Learning (normalizacja funkcji, obsługa brakujących danych, konwertowanie tekstu na liczbowe itp.) staje się częścią modelu źródłowego. Przy użyciu modelu dla prognoz te same kroki przetwarzania wstępnego zastosowane podczas uczenia są automatycznie stosowane do danych wejściowych.

### <a name="train-the-automatic-regression-model"></a>Trenowanie automatycznego modelu regresji

Utwórz obiekt eksperymentu w obszarze roboczym. Eksperyment działa jako kontener dla poszczególnych uruchomień. Przekaż zdefiniowany `automl_config` obiekt do eksperymentu i ustaw dane wyjściowe, aby `True` wyświetlić postęp podczas przebiegu.

Po rozpoczęciu eksperymentu dane wyjściowe pokazują aktualizacje na żywo, gdy zostanie uruchomione eksperyment. W każdej iteracji widać typ modelu, czas trwania i dokładność treningu. Pole `BEST` umożliwia śledzenie najlepszego wyniku w uruchomionym treningu na podstawie typu metryki.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, "taxi-experiment")
local_run = experiment.submit(automl_config, show_output=True)
```

```output
Running on local machine
Parent Run ID: AutoML_1766cdf7-56cf-4b28-a340-c4aeee15b12b
Current status: DatasetFeaturization. Beginning to featurize the dataset.
Current status: DatasetEvaluation. Gathering dataset statistics.
Current status: FeaturesGeneration. Generating features for the dataset.
Current status: DatasetFeaturizationCompleted. Completed featurizing the dataset.
Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
Current status: ModelSelection. Beginning model selection.

****************************************************************************************************
ITERATION: The iteration being evaluated.
PIPELINE: A summary description of the pipeline being evaluated.
DURATION: Time taken for the current iteration.
METRIC: The result of computing score on the fitted pipeline.
BEST: The best observed score thus far.
****************************************************************************************************

 ITERATION   PIPELINE                                       DURATION      METRIC      BEST
         0   StandardScalerWrapper RandomForest             0:00:16       0.8746    0.8746
         1   MinMaxScaler RandomForest                      0:00:15       0.9468    0.9468
         2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.9303    0.9468
         3   StandardScalerWrapper LightGBM                 0:00:10       0.9424    0.9468
         4   RobustScaler DecisionTree                      0:00:09       0.9449    0.9468
         5   StandardScalerWrapper LassoLars                0:00:09       0.9440    0.9468
         6   StandardScalerWrapper LightGBM                 0:00:10       0.9282    0.9468
         7   StandardScalerWrapper RandomForest             0:00:12       0.8946    0.9468
         8   StandardScalerWrapper LassoLars                0:00:16       0.9439    0.9468
         9   MinMaxScaler ExtremeRandomTrees                0:00:35       0.9199    0.9468
        10   RobustScaler ExtremeRandomTrees                0:00:19       0.9411    0.9468
        11   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9077    0.9468
        12   StandardScalerWrapper LassoLars                0:00:15       0.9433    0.9468
        13   MinMaxScaler ExtremeRandomTrees                0:00:14       0.9186    0.9468
        14   RobustScaler RandomForest                      0:00:10       0.8810    0.9468
        15   StandardScalerWrapper LassoLars                0:00:55       0.9433    0.9468
        16   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9026    0.9468
        17   StandardScalerWrapper RandomForest             0:00:13       0.9140    0.9468
        18   VotingEnsemble                                 0:00:23       0.9471    0.9471
        19   StackEnsemble                                  0:00:27       0.9463    0.9471
```

## <a name="explore-the-results"></a>Eksplorowanie wyników

Poznaj wyniki automatycznego szkolenia przy użyciu [widżetu Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true). Widżet umożliwia wyświetlenie wykresu i tabeli wszystkich poszczególnych iteracji przebiegów oraz metryki i metadanych dokładności szkoleniowej. Ponadto można odfiltrować różne metryki dokładności niż Metryka podstawowa za pomocą selektora listy rozwijanej.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Szczegóły przebiegu widżetu Jupyter](./media/tutorial-auto-train-models/automl-dash-output.png)
![Wykres widżetu Jupyter](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="retrieve-the-best-model"></a>Pobieranie najlepszego modelu

Wybierz najlepszy model z iteracji. `get_output`Funkcja zwraca najlepszy przebieg i zamontowany model dla ostatniego dopasowania wywołania. Korzystając z przeciążeń na `get_output` , można pobrać najlepszy i montowany model dla każdej zarejestrowanej metryki lub konkretnej iteracji.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

### <a name="test-the-best-model-accuracy"></a>Testowanie dokładności najlepszego modelu

Użyj najlepszego modelu, aby uruchomić przewidywania na zestawie danych testowych w celu przewidywania opłat za taksówkę. Funkcja `predict` używa najlepszego modelu i przewiduje wartości y, **kosztu podróży**z `x_test` zestawu danych. Wyświetl pierwsze 10 wartości przewidywanego kosztu z zestawu `y_predict`.

```python
y_test = x_test.pop("totalAmount")

y_predict = fitted_model.predict(x_test)
print(y_predict[:10])
```

Oblicz wartość `root mean squared error` dla wyników. Przekonwertuj `y_test` ramkę danych na listę w celu porównania do wartości prognozowanych. Funkcja `mean_squared_error` pobiera dwie tablice wartości i oblicza średnią wartość błędu kwadratowego między nimi. Wyciągnięcie pierwiastka kwadratowego z wyniku powoduje błąd w tych samych jednostkach co zmienna y **koszt**. Wskazuje na to, jak daleko opłaty za taksówkę są wysunięte z rzeczywistych opłat.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Uruchom następujący kod, aby obliczyć średni procent bezwzględnego błędu (MAPE) za pomocą `y_actual` zestawów pełnych i `y_predict` danych. Ta metryka oblicza wartości bezwzględne różnic między poszczególnymi wartościami przewidywanymi i rzeczywistymi oraz sumuje wszystkie różnice. Następnie wyrażenie to sumuje jako procent sumy wartości rzeczywistych.

```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

```output
Model MAPE:
0.14353867606052823

Model Accuracy:
0.8564613239394718
```


Na podstawie dwóch metryk dokładności przewidywania zobaczysz, że model jest dość dobry przy przewidywaniu opłat za taksówkę z funkcji zestawu danych, zazwyczaj w zakresie od +-$4,00 i około 15% błędu.

Tradycyjny proces opracowywania modelu uczenia maszynowego intensywnie korzysta z zasobów. Wymaga dużej wiedzy o danej dziedzinie oraz czasu, który trzeba poświęcić na uruchamianie kilkudziesięciu modeli i porównywanie ich wyników. Użycie automatycznego uczenia maszynowego jest doskonałym sposobem na szybkie przetestowanie wielu różnych modeli w danym scenariuszu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Nie wykonuj tej sekcji, jeśli planujesz Uruchamianie innych samouczków Azure Machine Learning.

### <a name="stop-the-compute-instance"></a>Zatrzymaj wystąpienie obliczeniowe

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Usuń wszystko

Jeśli nie planujesz korzystać z utworzonych zasobów, usuń je, aby nie nawiązać żadnych opłat.

1. W witrynie Azure Portal na końcu z lewej strony wybierz pozycję **Grupy zasobów**.
1. Wybierz utworzoną grupę zasobów z listy.
1. Wybierz pozycję **Usuń grupę zasobów**.
1. Wpisz nazwę grupy zasobów. Następnie wybierz pozycję **Usuń**.

Możesz też zachować grupę zasobów i usunąć jeden obszar roboczy. Wyświetl właściwości obszaru roboczego i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zautomatyzowanego uczenia maszynowego wykonano następujące czynności:

> [!div class="checklist"]
> * Skonfigurowano obszar roboczy i przygotowano dane do eksperymentu.
> * Przeprowadzono trenowanie przy użyciu zautomatyzowanego modelu regresji lokalnie z użyciem niestandardowych parametrów.
> * Zbadano i przejrzano wyniki trenowania.

[Wdróż model](tutorial-deploy-models-with-aml.md) za pomocą usługi Azure Machine Learning.
