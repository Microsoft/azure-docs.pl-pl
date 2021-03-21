---
title: 'Samouczek: uczenie modelu w języku Python za pomocą automatycznej uczenia maszynowego'
description: Samouczek dotyczący sposobu uczenia modelu uczenia maszynowego w języku Python przy użyciu Apache Spark i zautomatyzowanej uczenia maszynowego.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: 89309cfe427183d594a5cc2f76332ae150d4f803
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102498680"
---
# <a name="tutorial-train-a-model-in-python-with-automated-machine-learning"></a>Samouczek: uczenie modelu w języku Python za pomocą automatycznej uczenia maszynowego

Azure Machine Learning to środowisko oparte na chmurze, które umożliwia uczenie, wdrażanie, Automatyzowanie i śledzenie modeli uczenia maszynowego oraz zarządzanie nimi. 

W tym samouczku użyjesz [automatycznego uczenia maszynowego](../../machine-learning/concept-automated-ml.md) w Azure Machine Learning, aby utworzyć model regresji do przewidywania cen opłat za taksówkę. Ten proces dociera do najlepszego modelu przez zaakceptowanie danych szkoleniowych i ustawień konfiguracji oraz automatyczne Iterowanie za pomocą kombinacji różnych metod, modeli i ustawień parametrów.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
- Pobierz dane przy użyciu Apache Spark i otwartych zestawów danych platformy Azure.
- Przekształcanie i czyszczenie danych przy użyciu Apache Spark ramek dataframes.
- Uczenie modelu regresji w ramach automatycznej uczenia maszynowego.
- Oblicz dokładność modelu.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

- Utwórz bezserwerową pulę Apache Spark, wykonując czynności opisane w obszarze [Utwórz bezserwerową pulę Apache Spark](../quickstart-create-apache-spark-pool-studio.md) Szybki Start.
- Ukończ samouczek [konfigurowania Azure Machine Learning obszaru roboczego](../../machine-learning/tutorial-1st-experiment-sdk-setup.md) , jeśli nie masz istniejącego obszaru roboczego Azure Machine Learning. 

## <a name="understand-regression-models"></a>Omówienie modeli regresji

*Modele regresji* przewidują liczbowe wartości wyjściowe na podstawie niezależnych predykcyjnych. W regresji celem jest pomoc w ustaleniu relacji między niezależnymi zmiennymi predykcyjnymi przez oszacowanie, jak jedna zmienna wpływa na inne.  

### <a name="example-based-on-new-york-city-taxi-data"></a>Przykład oparty na danych o taksówkach w Nowym Jorku

W tym przykładzie używasz platformy Spark do wykonywania niektórych analiz dotyczących danych TIP podróży od Nowego Jorku (NYC). Dane są dostępne za pomocą [otwartych zestawów danych platformy Azure](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Ten podzestaw zestawu danych zawiera informacje o żółtych podróży, w tym informacje o każdej podróży, godzinie rozpoczęcia i zakończenia oraz o położeniu kosztów.

> [!IMPORTANT]
> Za ściąganie danych z lokalizacji magazynu mogą być naliczane dodatkowe opłaty. W poniższych krokach opracowujesz model do przewidywania cen opłat za taksówkę NYC. 

##  <a name="download-and-prepare-the-data"></a>Pobieranie i przygotowywanie danych

Oto kroki tej procedury:

1. Utwórz Notes przy użyciu jądra PySpark. Aby uzyskać instrukcje, zobacz [Tworzenie notesu](../quickstart-apache-spark-notebook.md#create-a-notebook).
   
    > [!Note]
    > Ze względu na jądro PySpark nie trzeba jawnie tworzyć kontekstów. Kontekst platformy Spark jest tworzony automatycznie po uruchomieniu pierwszej komórki kodu.
  
2. Ponieważ dane pierwotne są w formacie Parquet, można użyć kontekstu Spark, aby ściągnąć plik bezpośrednio do pamięci jako ramka danych. Utwórz ramkę danych Spark, pobierając dane za pośrednictwem interfejsu API Open DataSets. W tym miejscu należy użyć właściwości Dataframe platformy Spark `schema on read` do wywnioskowania typów danych i schematu. 
   
    ```python
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow Spark to read from the blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # Spark read parquet; note that it won't load any data yet
    df = spark.read.parquet(wasbs_path)

    ```

3. W zależności od rozmiaru puli platformy Spark dane pierwotne mogą być zbyt duże lub zbyt dużo czasu na wykonanie operacji. Dane można filtrować w dół do mniejszego, takiego jak miesiąc danych, przy użyciu ```start_date``` ```end_date``` filtrów i. Po filtrowaniu ramki danych można również uruchomić ```describe()``` funkcję w nowej ramce Dataframe, aby wyświetlić statystyki podsumowania dla każdego pola. 

   Na podstawie statystyk podsumowania można zobaczyć, że w danych znajdują się pewne nieprawidłowości. Na przykład statystyka pokazuje, że minimalna odległość podróży jest mniejsza niż 0. Należy odfiltrować te nieregularne punkty danych.
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime< "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. Generuj funkcje z zestawu danych, wybierając zestaw kolumn i tworząc różne funkcje oparte na czasie w `datetime` polu odbioru. Odfiltruj elementy odstające, które zostały zidentyfikowane w poprzednim kroku, a następnie usuń kilka ostatnich kolumn, ponieważ nie są one potrzebne do uczenia się.
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster, and less expensive, downsample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   taxi_df = sampled_taxi_df.select('vendorID', 'passengerCount', 'tripDistance',  'startLon', 'startLat', 'endLon' \
                                   , 'endLat', 'paymentType', 'fareAmount', 'tipAmount'\
                                   , column('puMonth').alias('month_num') \
                                   , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                   , date_format('tpepPickupDateTime', 'EEEE').alias('day_of_week')\
                                   , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month')
                                   ,(unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('trip_time'))\
                           .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                   & (sampled_taxi_df.tipAmount >= 0)\
                                   & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                   & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                   & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 200)\
                                   & (sampled_taxi_df.rateCodeId <= 5)\
                                   & (sampled_taxi_df.paymentType.isin({"1", "2"})))
   taxi_df.show(10)
   ```
   
   Jak widać, spowoduje to utworzenie nowej ramki danych z dodatkowymi kolumnami na dzień miesiąca, godzinę pobrania, dzień tygodnia i łączny czas podróży. 

   ![Ilustracja ramki danych o taksówkach.](./media/azure-machine-learning-spark-notebook/dataset.png#lightbox)

## <a name="generate-test-and-validation-datasets"></a>Generowanie zestawów danych testowych i walidacji

Po utworzeniu końcowego zestawu danych można podzielić dane na zestawy szkoleniowe i testowe przy użyciu ```random_ split ``` funkcji w usłudze Spark. Korzystając z dostarczonych wag, ta funkcja losowo dzieli dane na zestaw danych szkoleniowych na potrzeby szkolenia modelu i zestawu danych sprawdzania poprawności na potrzeby testowania.

```python
# Random split dataset using Spark; convert Spark to pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```
Ten krok zapewnia, że punkty danych do testowania gotowego modelu nie były używane do uczenia modelu. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Nawiązywanie połączenia z obszarem roboczym Azure Machine Learning
W Azure Machine Learning obszar roboczy to Klasa, która akceptuje informacje o subskrypcji i zasobach platformy Azure. Tworzy ona również zasób w chmurze służący do monitorowania i śledzenia przebiegów modelu. W tym kroku utworzysz obiekt obszaru roboczego z istniejącego obszaru roboczego Azure Machine Learning.
   
```python
from azureml.core import Workspace

# Enter your workspace subscription, resource group, name, and region.
subscription_id = "<enter your subscription ID>" #you should be owner or contributor
resource_group = "<enter your resource group>" #you should be owner or contributor
workspace_name = "<enter your workspace name>" #your workspace name
workspace_region = "<enter workspace region>" #your region

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

```

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>Konwertowanie ramki Dataframe na zestaw danych Azure Machine Learning
Aby przesłać zdalny eksperyment, przekonwertuj zestaw danych na wystąpienie Azure Machine Learning ```TabularDatset``` . [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) reprezentuje dane w formacie tabelarycznym przez analizowanie podanych plików.

Poniższy kod pobiera istniejący obszar roboczy i domyślny magazyn danych Azure Machine Learning. Następnie przekazuje magazyn danych i lokalizacje plików do parametru Path, aby utworzyć nowe ```TabularDataset``` wystąpienie. 

```python
import pandas 
from azureml.core import Dataset

# Get the Azure Machine Learning default datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into an Azure Machine Learning tabular dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```
![Obraz przekazanego zestawu danych.](./media/azure-machine-learning-spark-notebook/upload-dataset.png)

## <a name="submit-an-automated-experiment"></a>Prześlij zautomatyzowany eksperyment

W poniższych sekcjach omówiono proces przesyłania zautomatyzowanego eksperymentu uczenia maszynowego.

### <a name="define-training-settings"></a>Definiowanie ustawień szkoleniowych
1. Aby przesłać eksperyment, należy zdefiniować parametr eksperymentu i ustawienia modelu dla szkolenia. Aby uzyskać pełną listę ustawień, zobacz [Konfigurowanie zautomatyzowanych eksperymentów uczenia maszynowego w języku Python](../../machine-learning/how-to-configure-auto-train.md).

   ```python
   import logging

   automl_settings = {
       "iteration_timeout_minutes": 10,
       "experiment_timeout_minutes": 30,
       "enable_early_stopping": True,
       "primary_metric": 'r2_score',
       "featurization": 'auto',
       "verbosity": logging.INFO,
       "n_cross_validations": 2}
   ```

1. Przekaż zdefiniowane ustawienia szkoleniowe jako `kwargs` parametr do `AutoMLConfig` obiektu. Ponieważ używasz platformy Spark, musisz również przekazać kontekst Spark, który jest automatycznie dostępny dla ```sc``` zmiennej. Ponadto należy określić dane szkoleniowe i typ modelu, który jest regresją w tym przypadku.

   ```python
   from azureml.train.automl import AutoMLConfig

   automl_config = AutoMLConfig(task='regression',
                                debug_log='automated_ml_errors.log',
                                training_data = dataset_training,
                                spark_context = sc,
                                model_explainability = False, 
                                label_column_name ="fareAmount",**automl_settings)
   ```

> [!NOTE]
>Zautomatyzowane kroki przetwarzania wstępnego uczenia maszynowego stają się częścią modelu źródłowego. Te kroki obejmują normalizację funkcji, obsługę brakujących danych i konwertowanie tekstu na wartość numeryczną. Gdy używasz modelu do prognozowania, te same kroki przetwarzania wstępnego zastosowane podczas szkolenia są automatycznie stosowane do danych wejściowych.

### <a name="train-the-automatic-regression-model"></a>Trenowanie automatycznego modelu regresji 
Następnie utworzysz obiekt eksperymentu w obszarze roboczym Azure Machine Learning. Eksperyment działa jako kontener dla poszczególnych uruchomień. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```
Po zakończeniu eksperymentu dane wyjściowe zwracają szczegółowe informacje o zakończonych iteracjach. W każdej iteracji widać typ modelu, czas trwania i dokładność treningu. `BEST`Pole śledzi najlepszy wynik szkolenia na podstawie typu metryki.

![Zrzut ekranu przedstawiający dane wyjściowe modelu.](./media/azure-machine-learning-spark-notebook/model-output.png)

> [!NOTE]
> Po przesłaniu eksperymentu automatycznego uczenia maszynowego jest uruchamiany różne iteracje i typy modeli. Ten przebieg zazwyczaj trwa od 60 do 90 minut. 

### <a name="retrieve-the-best-model"></a>Pobieranie najlepszego modelu
Aby wybrać najlepszy model z iteracji, użyj ```get_output``` funkcji do zwrócenia najlepszego uruchomienia i dopasowanego modelu. Poniższy kod umożliwia pobranie najlepszego uruchomienia i dopasowanego modelu dla każdej rejestrowanej metryki lub określonej iteracji.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

### <a name="test-model-accuracy"></a>Dokładność modelu testu
1. Aby przetestować dokładność modelu, należy użyć najlepszego modelu do uruchamiania prognoz opłat za taksówkę na testowym zestawie danych. ```predict```Funkcja używa najlepszego modelu i przewiduje wartości `y` (wartość opłaty) z zestawu danych walidacji. 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

1. Błąd średnika głównego — jest to często używany pomiar różnic między przykładowymi wartościami przewidzianymi przez model i zaobserwowanymi wartościami. Obliczany jest błąd średnika z wyników, porównując `y_test` ramkę danych z wartościami przewidywanymi przez model. 

   Funkcja ```mean_squared_error``` przyjmuje dwie tablice i oblicza średniy kwadratowy błąd między nimi. Następnie należy uzyskać pierwiastek kwadratowy wyniku. Ta Metryka wskazuje na przybliżenie, jak daleko od wartości rzeczywistej opłaty za taksówkę opłaty są nadane.

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate root-mean-square error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   Główny-średniy błąd jest dobrym pomiarem, jak dokładnie model przewiduje odpowiedź. Z wyników zobaczysz, że model jest dość dobry podczas przewidywania opłat za taksówkę z funkcji zestawu danych, zwykle w ramach $2,00.

1. Uruchom następujący kod, aby obliczyć średni procent bezwzględnych wartości odsetek. Ta Metryka wyraża dokładność jako wartość procentową błędu. Robi to poprzez Obliczanie różnicy bezwzględnej między każdą przewidywaną i rzeczywistą wartością, a następnie sumowanie wszystkich różnic. Następnie wyrażenie to stanowi wartość procentową sumy wartości rzeczywistych.

   ```python
   # Calculate mean-absolute-percent error and model accuracy 
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

   ```Output
   Model MAPE:
   0.03655071038487368

   Model Accuracy:
   0.9634492896151263
   ```
   Z dwóch metryk dotyczących dokładności przewidywania zobaczysz, że model jest dość dobry przy przewidywaniu opłat za taksówkę z funkcji zestawu danych. 

1. Po dopasowaniu modelu regresji liniowej należy teraz określić, jak dobrze model pasuje do danych. W tym celu należy sporządzić wartości rzeczywistej taryfy względem przewidywanych danych wyjściowych. Ponadto należy obliczyć miarę R-kwadratową, aby zrozumieć, jak zamyka się dane do dopasowanej linii regresji.

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score by using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the actual versus predicted fare amount values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amount R^2={}".format(r2))
   plt.show()

   ```
   ![Zrzut ekranu przedstawiający wykres regresji.](./media/azure-machine-learning-spark-notebook/fare-amount.png)

   Wyniki można zobaczyć, że miary R-kwadratowe są rozliczanie na 95 procent wariancji. Jest to również zweryfikowane przez rzeczywistą powierzchnię zamiast zaobserwowanego wykresu. Im większa jest WARIANCJA dla modelu regresji, tym bliżej punktów danych nastąpi zastosowana linia regresji.  

## <a name="register-the-model-to-azure-machine-learning"></a>Zarejestruj model w Azure Machine Learning
Po zweryfikowaniu najlepszego modelu możesz zarejestrować go w Azure Machine Learning. Następnie można pobrać lub wdrożyć zarejestrowany model i odebrać wszystkie zarejestrowane pliki.

```python
description = 'My automated ML model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```
```Output
NYCGreenTaxiModel 1
```
## <a name="view-results-in-azure-machine-learning"></a>Wyświetl wyniki w Azure Machine Learning
Możesz również uzyskać dostęp do wyników iteracji, przechodząc do eksperymentu w obszarze roboczym Azure Machine Learning. W tym miejscu możesz uzyskać dodatkowe szczegóły dotyczące stanu przebiegu, próby modeli i inne metryki modelu. 

![Zrzut ekranu przedstawiający obszar roboczy Azure Machine Learning.](./media/azure-machine-learning-spark-notebook/azure-machine-learning-workspace.png)

## <a name="next-steps"></a>Następne kroki
- [Azure Synapse Analytics](../index.yml)
- [Samouczek: Tworzenie aplikacji usługi Machine Learning przy użyciu usług Apache Spark MLlib i Azure Synapse Analytics](./apache-spark-machine-learning-mllib-notebook.md)