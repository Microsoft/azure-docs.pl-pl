---
title: Tworzenie aplikacji do uczenia maszynowego za pomocą aplikacji Apache Spark MLlib i Usługi Azure Synapse Analytics
description: Dowiedz się, jak używać Apache Spark MLlib do tworzenia aplikacji uczenia maszynowego, która analizuje zestaw danych przy użyciu klasyfikacji za pomocą regresji logistycznej.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 9dc4047b9e95b088bb614858091f43286cefe361
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430008"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Tworzenie aplikacji do uczenia maszynowego za pomocą aplikacji Apache Spark MLlib i Usługi Azure Synapse Analytics

W tym artykule dowiesz się, jak użyć Apache Spark [MLlib](https://spark.apache.org/mllib/) do utworzenia aplikacji uczenia maszynowego, która wykonuje prostą analizę predykcyjną w otwartym zestawie danych platformy Azure. Platforma Spark udostępnia wbudowane biblioteki uczenia maszynowego. W tym przykładzie użyto *klasyfikacji* za pomocą regresji logistycznej.

MLlib to podstawowa biblioteka platformy Spark, która udostępnia wiele narzędzi przydatnych do zadań uczenia maszynowego, w tym narzędzia, które nadają się do:

- Klasyfikacja
- Regresja
- Klastrowanie
- Modelowanie tematów
- Rozkład wartości pojedynczej (SVD) i analiza głównego komponentu (PCA)
- Badanie hipotez i obliczanie przykładowych statystyk

## <a name="understand-classification-and-logistic-regression"></a>Zrozumienie klasyfikacji i regresji logistycznej

*Klasyfikacja*, popularne zadanie uczenia maszynowego, to proces sortowania danych wejściowych na kategorie. Zadaniem algorytmu klasyfikacji jest dowiedzieć się, jak przypisać *etykiety* do danych wejściowych, które podasz. Na przykład można myśleć o algorytmie uczenia maszynowego, który akceptuje informacje o stanie jako dane wejściowe i dzieli zapasy na dwie kategorie: zapasy, które należy sprzedać i zapasy, które należy zachować.

*Regresja logistyczna* jest algorytmem, którego można użyć do klasyfikacji. Interfejs API regresji logistycznej platformy Spark jest przydatny w *klasyfikacji binarnej*lub klasyfikacji danych wejściowych do jednej z dwóch grup. Aby uzyskać więcej informacji na temat regresji logistycznych, zobacz [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Podsumowując, proces regresji logistycznej tworzy *funkcję logistyczną,* która może służyć do przewidywania prawdopodobieństwa, że wektor wejściowy należy do jednej lub drugiej grupy.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Przykład analizy predykcyjnej na temat danych taksówki NYC

W tym przykładzie użyjesz platformy Spark do przeprowadzenia analizy predykcyjnej danych porad dotyczących podróży taksówką z Nowego Jorku. Dane są dostępne za pośrednictwem [zestawów danych Azure Open](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Ten podzbiór zestawu danych zawiera informacje o żółtych przejazdach taksówką, w tym informacje o każdej podróży, godzinie rozpoczęcia i zakończenia i lokalizacjach, kosztach i innych interesujących atrybutach.

> [!IMPORTANT]
> Może istnieć dodatkowe opłaty za ściąganie tych danych z jego lokalizacji przechowywania.

W poniższych krokach opracowywać model, aby przewidzieć, czy dana podróż zawiera wskazówkę, czy nie.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Tworzenie aplikacji do uczenia maszynowego Apache Spark MLlib

1. Utwórz notes przy użyciu jądra PySpark. Aby uzyskać instrukcje, zobacz [Tworzenie notesu](./apache-spark-notebook-create-spark-use-sql.md#create-a-notebook).
2. Importuj typy wymagane dla tej aplikacji. Skopiuj i wklej następujący kod do pustej komórki, a następnie naciśnij **klawisze SHIFT + ENTER**lub uruchom komórkę za pomocą niebieskiej ikony odtwarzania po lewej stronie kodu.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    Ze względu na jądro PySpark nie trzeba tworzyć żadnych kontekstów jawnie. Kontekst platformy Spark jest tworzony automatycznie po uruchomieniu pierwszej komórki kodu.

## <a name="construct-the-input-dataframe"></a>Konstruowanie wejściowej ramki danych

Ponieważ nieprzetworzone dane są w formacie Parkietu, można użyć kontekstu Platformy Spark, aby bezpośrednio wyciągnąć plik do pamięci jako ramkę danych. Podczas gdy poniższy kod używa opcji domyślnych, możliwe jest wymuszenie mapowania typów danych i innych atrybutów schematu w razie potrzeby.

1. Uruchom następujące wiersze, aby utworzyć ramkę danych platformy Spark, wklejając kod do nowej komórki. Pierwsza sekcja przypisuje informacje o dostępie do magazynu platformy Azure do zmiennych. Druga sekcja umożliwia spark do odczytu z magazynu obiektów blob zdalnie. Ostatni wiersz kodu odczytuje parkiet, ale w tym momencie nie są ładowane żadne dane.

    ```python
    # Azure storage access info
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow SPARK to read from Blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # SPARK read parquet, note that it won't load any data yet by now
    df = spark.read.parquet(wasbs_path)
    ```

2. Wyciąganie wszystkich tych danych generuje około 1,5 miliarda wierszy. W zależności od rozmiaru puli platformy Spark (wersja zapoznawcza) nieprzetworzone dane mogą być zbyt duże lub zająć zbyt dużo czasu, aby działać na. Możesz filtrować te dane do czegoś mniejszego. W razie potrzeby dodaj następujące wiersze, aby filtrować dane do około 2 milionów wierszy, aby uzyskać bardziej responsywne środowisko. Użyj tych parametrów, aby wyciągnąć jeden tydzień danych.

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. Wadą prostego filtrowania jest to, że z statystycznego punktu widzenia może ono wprowadzić stronniczość do danych. Innym podejściem jest użycie próbkowania wbudowanego w platformę Spark. Poniższy kod zmniejsza zestaw danych w dół do około 2000 wierszy, jeśli są stosowane po kod powyżej. Ten krok próbkowania może być używany zamiast prostego filtru lub w połączeniu z filtrem prostym.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. Teraz można spojrzeć na dane, aby zobaczyć, co zostało przeczytane. Zwykle lepiej jest przeglądać dane za pomocą podzbioru, a nie pełnego zestawu w zależności od rozmiaru zestawu danych. Poniższy kod oferuje dwa sposoby wyświetlania danych: pierwszy jest podstawowy, a drugi zapewnia znacznie bogatsze środowisko siatki, a także możliwość graficznego wizualizacji danych.

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. W zależności od rozmiaru wygenerowanego zestawu danych i konieczności eksperymentowania lub uruchamiania notesu wiele razy, może być wskazane, aby buforować zestaw danych lokalnie w obszarze roboczym. Istnieją trzy sposoby wykonywania jawnego buforowania:

   - Zapisywanie ramki danych lokalnie jako pliku
   - Zapisywanie ramki danych jako tymczasowej tabeli lub widoku
   - Zapisywanie ramki danych jako stałej tabeli

Pierwsze 2 z tych metod są zawarte w poniższych przykładach kodu.

Tworzenie tabeli tymczasowej lub widoku zapewnia różne ścieżki dostępu do danych, ale trwa tylko przez czas trwania sesji wystąpienia platformy Spark.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>Zrozumienie danych

Zwykle można przejść przez fazę *analizy danych badawczych* (EDA) w tym momencie, aby rozwinąć zrozumienie danych. Poniższy kod przedstawia trzy różne wizualizacje danych związanych z poradami, które prowadzą do wniosków dotyczących stanu i jakości danych.

```python
# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()

# Look at tips by amount count histogram
ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# How many passengers tip'd by various amounts
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# Look at the relationship between fare and tip amounts
ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```

![Histogram](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
![Box Wąsów Działka](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![Scatter Działka](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>Przygotowywanie danych

Dane w postaci nieprzetworzonej często nie nadają się do przekazywania bezpośrednio do modelu. Seria akcji musi być wykonana na danych, aby uzyskać go do stanu, w którym model może go używać.

W poniższym kodzie wykonywane są cztery klasy operacji:

- Usuwanie wartości odstających/nieprawidłowych wartości poprzez filtrowanie.
- Usuwanie kolumn, które nie są potrzebne.
- Tworzenie nowych kolumn pochodzących z nieprzetworzonych danych, aby model działał bardziej efektywnie, czasami nazywany featurization.
- Etykietowanie, jak podejmujesz klasyfikacji binarnej (będzie wskazówka lub nie w danej podróży) istnieje potrzeba, aby przeliczyć kwotę końcówki na wartość 0 lub 1.

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

Drugie podanie jest następnie dokonywane nad danymi, aby dodać końcowe funkcje.

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>Tworzenie modelu regresji logistycznej

Ostatnim zadaniem jest przekonwertowanie oznaczonych danych na format, który może być analizowany przez regresję logistyczną. Dane wejściowe do algorytmu regresji logistycznej musi być zestawem *par wektorowych funkcji etykiety,* gdzie *wektor funkcji* jest wektorem liczb reprezentujących punkt wejściowy. Musimy więc przekonwertować kolumny kategoryczne na liczby. Kolumny `trafficTimeBins` `weekdayString` i kolumny muszą zostać przekonwertowane na reprezentacje całkowite. Istnieje wiele podejść do wykonywania konwersji, jednak podejście przyjęte w tym przykładzie jest *OneHotEncoding*, wspólne podejście.

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Powoduje to nowy dataframe ze wszystkimi kolumnami w odpowiednim formacie do szkolenia modelu.

## <a name="train-a-logistic-regression-model"></a>Szkolenie modelu regresji logistycznej

Pierwszym zadaniem jest podzielenie zestawu danych na zestaw szkoleniowy i zestaw testowania lub sprawdzania poprawności. Podział tutaj jest dowolny i należy bawić się z różnymi ustawieniami podziału, aby sprawdzić, czy mają one wpływ na model.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Teraz, gdy istnieją dwa DataFrames, następnym zadaniem jest utworzenie formuły modelu i uruchomić go względem szkolenia DataFrame, a następnie sprawdź poprawność względem testowania DataFrame. Należy eksperymentować z różnymi wersjami formuły modelu, aby zobaczyć wpływ różnych kombinacji.

```python
## Create a new LR object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create an LR model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional but its another form of inter session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset, evaluation using AUROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

Dane wyjściowe z tej komórki są

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Tworzenie wizualnej reprezentacji prognozowania

Teraz można skonstruować wizualizację końcową, aby pomóc w uzasadnieniu wyników tego testu. [Krzywa ROC](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) jest jednym ze sposobów, aby przejrzeć wynik.

```python
## Plot the ROC curve, no need for pandas as this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![Krzywa ROC dla modelu końcówki regresji logistycznej](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "Krzywa ROC dla modelu końcówki regresji logistycznej")

## <a name="shut-down-the-spark-instance"></a>Zamykanie wystąpienia platformy Spark

Po zakończeniu uruchamiania aplikacji zamknij notes, aby zwolnić zasoby, zamykając kartę lub wybierz **pozycję Zakończ sesję** z panelu stanu u dołu notesu.

## <a name="see-also"></a>Zobacz też

- [Omówienie: Platforma Apache Spark w usłudze Azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Następne kroki

- [.NET dla apache Spark dokumentacji](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Oficjalna dokumentacja Apache Spark](https://spark.apache.org/docs/latest/)

>[!NOTE]
> Niektóre oficjalne apache spark dokumentacji opiera się na użyciu konsoli Platformy Spark, który nie jest dostępny na platformie Azure Synapse Spark. Zamiast tego użyj [notebooka](../spark/apache-spark-notebook-create-spark-use-sql.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) lub [intellij.](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
