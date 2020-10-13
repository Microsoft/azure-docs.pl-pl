---
title: 'Samouczek: uruchamianie eksperymentów przy użyciu zautomatyzowanej platformy Azure'
description: Samouczek dotyczący sposobu uruchamiania eksperymentów uczenia maszynowego przy użyciu Apache Spark i zautomatyzowanej sieci platformy Azure
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick,
ms.openlocfilehash: da4cef50610b219689e2271e9f70fd1adb1a235f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540510"
---
# <a name="tutorial-run-experiments-using-azure-automated-ml-and-apache-spark"></a>Samouczek: uruchamianie eksperymentów przy użyciu zautomatyzowanej platformy Azure i Apache Spark

Azure Machine Learning to środowisko oparte na chmurze, które umożliwia uczenie, wdrażanie, Automatyzowanie i śledzenie modeli uczenia maszynowego oraz zarządzanie nimi. 

W tym samouczku użyjesz [automatycznego uczenia maszynowego](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) w Azure Machine Learning, aby utworzyć model regresji do przewidywania cen opłat za taksówkę NYC. Ten proces akceptuje dane szkoleniowe i ustawienia konfiguracji i automatycznie wykonuje iterację przez kombinacje różnych metod normalizacji/normalizacji funkcji, modeli i parametrów dołączania do najlepszego modelu.

W tym samouczku przedstawiono następujące zadania:
- Pobieranie danych przy użyciu Apache Spark i otwartych zestawów danych platformy Azure
- Przekształcanie i czyszczenie danych przy użyciu Apache Spark ramek dataframes
- Uczenie zautomatyzowanego modelu regresji uczenia maszynowego
- Oblicz dokładność modelu

### <a name="before-you-begin"></a>Zanim rozpoczniesz
- Utwórz pulę Apache Spark, postępując zgodnie z [samouczkiem Tworzenie puli Apache Spark](../quickstart-create-apache-spark-pool-studio.md).
- Ukończ [samouczek konfiguracji obszaru roboczego Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup) , jeśli nie masz istniejącego obszaru roboczego Azure Machine Learning. 

### <a name="understand-regression-models"></a>Omówienie modeli regresji
*Modele regresji* przewidują liczbowe wartości wyjściowe na podstawie niezależnych predykcyjnych. W regresji celem jest pomoc w ustaleniu relacji między niezależnymi zmiennymi predykcyjnymi przez oszacowanie wpływu jednej zmiennej na inne.  

### <a name="regression-analysis-example-on-the-nyc-taxi-data"></a>Przykład analizy regresji dla danych z NYC taksówkami
W tym przykładzie użyjesz platformy Spark do przeprowadzenia analizy danych TIP podróży z Nowego Jorku. Dane są dostępne za pomocą [otwartych zestawów danych platformy Azure](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Ten podzestaw zestawu danych zawiera informacje dotyczące żółtej podróży z taksówką, w tym informacje o każdej podróży, godzinie rozpoczęcia i zakończenia oraz lokalizacji, kosztu i innych interesujących atrybutów.

> [!IMPORTANT]
> 
> Za ściąganie danych z lokalizacji magazynu mogą być naliczane dodatkowe opłaty. W poniższych krokach utworzysz model do przewidywania cen opłat za taksówkę NYC. 
> 

##  <a name="download-and-prepare-the-data"></a>Pobieranie i przygotowywanie danych

1. Utwórz Notes przy użyciu jądra PySpark. Aby uzyskać instrukcje, zobacz [Tworzenie notesu](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook.)
   
   > [!Note]
   > 
   > Ze względu na jądro PySpark nie trzeba jawnie tworzyć kontekstów. Kontekst platformy Spark jest tworzony automatycznie po uruchomieniu pierwszej komórki kodu.
   >

2. Ponieważ dane pierwotne są w formacie Parquet, można użyć kontekstu Spark, aby ściągnąć plik do pamięci jako element danych bezpośrednio. Utwórz ramkę danych Spark, pobierając dane za pośrednictwem interfejsu API Open DataSets. W tym miejscu będziemy używać schematu Spark Dataframe *dla właściwości odczytu* w celu wywnioskowania typów danych i schematu. 
   
   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "nyctlc"
   blob_relative_path = "yellow"
   blob_sas_token = r""

   # Allow Spark to read from Blob remotely
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

   # Spark read parquet, note that it won't load any data yet by now
   df = spark.read.parquet(wasbs_path)
   ```

3. W zależności od rozmiaru puli Spark (wersja zapoznawcza) dane pierwotne mogą być zbyt duże lub zbyt dużo czasu na wykonanie operacji. Te dane można filtrować do mniejszej liczby przy użyciu ```start_date``` ```end_date``` filtrów i. Powoduje to zastosowanie filtru, który zwraca miesiąc danych. Po przefiltrowaniu ramki danych zostanie również uruchomiona ```describe()``` Funkcja w nowej ramce Dataframe, aby wyświetlić statystyki podsumowania dla każdego pola. 

   Na podstawie statystyk podsumowania możemy zobaczyć, że w danych znajdują się pewne nieprawidłowości i wartości. Na przykład statystyka pokazuje, że minimalna odległość podróży jest mniejsza niż 0. Będziemy musieli odfiltrować te nieregularne punkty danych.
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. Teraz będziemy generować funkcje z zestawu danych, wybierając zestaw kolumn i tworząc różne funkcje oparte na czasie w polu Data/godzina pobrania. Przefiltruje również elementy odstające, które zostały zidentyfikowane w poprzednim kroku, a następnie usuniemy kilka ostatnich kolumn, które nie są potrzebne do uczenia się.
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster and less expensive down sample for now
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

![Ilustracja ramki danych o taksówkach.](./media/apache-spark-machine-learning-aml-notebook/aml-dataset.png)

## <a name="generate-test-and-validation-datasets"></a>Generowanie zestawów danych testowych i walidacji

Po naszym ostatnim zestawie danych możemy podzielić dane na zestawy szkoleniowe i testowe przy użyciu ```random_ split ``` funkcji Spark. Korzystając z podanych wag, ta funkcja losowo dzieli dane na zestaw danych szkoleniowych na potrzeby szkolenia modelu i zestawu danych sprawdzania poprawności na potrzeby testowania.

```python
# Random split dataset using spark, convert Spark to Pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```

Ten krok zapewnia, że punkty danych przetestują gotowy model, który nie był używany do uczenia modelu. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Nawiązywanie połączenia z Obszar roboczy usługi Azure Machine Learning
W Azure Machine Learning  **obszar roboczy** to Klasa, która akceptuje informacje o subskrypcji i zasobach platformy Azure. Tworzy ona również zasób w chmurze służący do monitorowania i śledzenia przebiegów modelu. W tym kroku utworzymy obiekt obszaru roboczego z istniejącego obszaru roboczego Azure Machine Learning.
   
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
Aby przesłać zdalny eksperyment, konieczne będzie przekonwertowanie naszego zestawu danych na Azure Machine Learning ```TabularDatset``` . [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) reprezentuje dane w formacie tabelarycznym przez analizowanie podanych plików.

Poniższy kod pobiera istniejący obszar roboczy i domyślny Azure Machine Learning domyślnym magazynem danych. Następnie przekazuje magazyn danych i lokalizacje plików do parametru Path, aby utworzyć nowy ```TabularDataset``` . 

```python
import pandas 
from azureml.core import Dataset

# Get the AML Default Datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into AML Tabular Dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```

![Obraz przekazanego zestawu danych.](./media/apache-spark-machine-learning-aml-notebook/upload-dataset.png)

## <a name="submit-an-auto-ml-experiment"></a>Przesyłanie eksperymentu dotyczącego samochodów

#### <a name="define-training-settings"></a>Definiowanie ustawień szkoleniowych

1. Aby przesłać eksperyment, konieczne będzie zdefiniowanie parametrów eksperymentu i ustawień modelu na potrzeby szkolenia. Pełną listę ustawień można wyświetlić w [tym miejscu](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train).

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

2. Teraz będziemy przekazywać zdefiniowane ustawienia szkoleniowe jako \* \* parametr kwargs do obiektu AutoMLConfig. Ze względu na to, że firma Microsoft prowadzi szkolenia w usłudze Spark, musimy również przekazać kontekst Spark, który jest automatycznie dostępny dla ```sc``` zmiennej. Dodatkowo określimy dane szkoleniowe i typ modelu, który jest regresją w tym przypadku.

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
> Zautomatyzowane kroki wstępnego przetwarzania w usłudze Machine Learning (normalizacja funkcji, obsługa brakujących danych, konwertowanie tekstu na liczbowe itp.) staje się częścią modelu źródłowego. Przy użyciu modelu dla prognoz te same kroki przetwarzania wstępnego zastosowane podczas uczenia są automatycznie stosowane do danych wejściowych.

#### <a name="train-the-automatic-regression-model"></a>Trenowanie automatycznego modelu regresji 
Teraz utworzymy obiekt eksperymentu w obszarze roboczym Azure Machine Learning. Eksperyment działa jako kontener dla poszczególnych uruchomień. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```

Po zakończeniu eksperymentu dane wyjściowe będą zwracać szczegółowe informacje o zakończonych iteracjach. W każdej iteracji widać typ modelu, czas trwania i dokładność treningu. Pole najlepiej śledzi najlepszy wynik szkolenia w oparciu o typ metryki.

![Zrzut ekranu przedstawiający dane wyjściowe modelu.](./media/apache-spark-machine-learning-aml-notebook/aml-model-output.png)

> [!NOTE]
> Po przesłaniu eksperymentu AutoML uruchomi różne iteracje i typy modeli. Ten przebieg zazwyczaj trwa 1 – 1,5 godzin. 

#### <a name="retrieve-the-best-model"></a>Pobieranie najlepszego modelu
Aby wybrać najlepszy model z iteracji, użyjemy ```get_output``` funkcji do zwrócenia najlepszego przebiegu i dopasowanego modelu. Poniższy kod spowoduje pobranie najlepszego uruchomienia i dopasowanego modelu dla każdej zarejestrowanej metryki lub określonej iteracji.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

#### <a name="test-model-accuracy"></a>Dokładność modelu testu

1. Aby przetestować dokładność modelu, będziemy używać najlepszego modelu do uruchamiania prognoz opłat za taksówkę na zestawie danych testowych. ```predict```Funkcja używa najlepszego modelu i przewiduje wartości y (opłata za opłaty) z zestawu danych walidacji. 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

2. Błąd elementu głównego-średnika (RMSE) to często używany pomiar różnic między przykładowymi wartościami przewidzianymi przez model i zaobserwowane wartości. Obliczamy wartość średniego kwadratowego błędu w wyniku, porównując y_test Dataframe z wartościami przewidywanymi przez model. 

   Funkcja ```mean_squared_error``` przyjmuje dwie tablice i oblicza średniy kwadratowy błąd między nimi. Następnie przyjmmy pierwiastek kwadratowy wyniku. Ta Metryka wskazuje na to, jak daleko opłaty za przejazd z oddziałami są z rzeczywistych wartości opłat.

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate Root Mean Square Error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   
   Główny-średniy błąd jest dobrym pomiarem, jak dokładnie model przewiduje odpowiedź. Na podstawie wyników zobaczysz, że model jest dość dobry przy przewidywaniu opłat za taksówkę z funkcji zestawu danych, zazwyczaj w zakresie od + do $2,00

3. Uruchom następujący kod, aby obliczyć średni procent bezwzględnego błędu (MAPE). Ta Metryka wyraża dokładność jako wartość procentową błędu. Robi to poprzez Obliczanie różnicy bezwzględnej między każdą przewidywaną i rzeczywistą wartością, a następnie sumowanie wszystkich różnic. Następnie wyrażenie to sumuje jako procent sumy wartości rzeczywistych.

   ```python
   # Calculate MAPE and Model Accuracy 
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
   Na podstawie dwóch metryk dokładności przewidywania zobaczysz, że model jest dość dobry przy przewidywaniu opłat za taksówkę z funkcji zestawu danych. 

4. Po dopasowaniu modelu regresji liniowej będziemy teraz musieli określić, jak dobrze model dopasowuje dane. W tym celu będziemy wykreślać rzeczywiste wartości opłat w odniesieniu do przewidywanych danych wyjściowych. Ponadto zostanie również obliczona miara R-kwadratowa, aby zrozumieć, jak zamknąć dane, do dopasowanej linii regresji.

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the Actual vs Predicted Fare Amount Values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amont R^2={}".format(r2))
   plt.show()
   ```
   
   ![Zrzut ekranu przedstawiający wykres regresji.](./media/apache-spark-machine-learning-aml-notebook/aml-fare-amount.png)

   Wyniki można zobaczyć, że miary R-kwadratowe są rozliczeń na 95% z naszej wariancji. Jest to również sprawdzane przez rzeczywiste zaobserwowane wiersze. Im większa WARIANCJA, która jest uwzględniana przez model regresji, tym bliżej punktów danych nastąpi zastosowana linia regresji.  

## <a name="register-model-to-azure-machine-learning"></a>Zarejestruj model do Azure Machine Learning
Po sprawdzeniu najlepszego modelu możemy zarejestrować model do Azure Machine Learning. Po zarejestrowaniu modelu można pobrać lub wdrożyć zarejestrowany model i odebrać wszystkie zarejestrowane pliki.

```python
description = 'My AutoML Model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```

```Output
NYCGreenTaxiModel 1
```

## <a name="view-results-in-azure-machine-learning"></a>Wyświetl wyniki w Azure Machine Learning
Na koniec można także uzyskać dostęp do wyników iteracji, przechodząc do eksperymentu w Obszar roboczy usługi Azure Machine Learning. W tym miejscu będzie można Dig dodatkowe szczegóły dotyczące stanu przebiegu, próby modeli i inne metryki modelu. 

![Zrzut ekranu przedstawiający obszar roboczy AML.](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-aml-workspace.png)

## <a name="next-steps"></a>Następne kroki
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Samouczek MLlib Apache Spark](./apache-spark-machine-learning-mllib-notebook.md)
