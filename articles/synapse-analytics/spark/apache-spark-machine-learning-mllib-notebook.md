---
title: 'Samouczek: Tworzenie aplikacji Machine Learning za pomocą Apache Spark MLlib'
description: Samouczek dotyczący sposobu korzystania z programu Apache Spark MLlib do tworzenia aplikacji usługi Machine Learning, która analizuje zestaw danych przy użyciu klasyfikacji logistycznej.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 595b3a57594401df6b61db1fcf8ee16be98ef364
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95900429"
---
# <a name="tutorial-build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Samouczek: Tworzenie aplikacji usługi Machine Learning przy użyciu usług Apache Spark MLlib i Azure Synapse Analytics

W tym artykule dowiesz się, jak za pomocą programu Apache Spark [MLlib](https://spark.apache.org/mllib/) utworzyć aplikację uczenia maszynowego, która wykonuje prostą analizę predykcyjną w otwartym zestawie danych platformy Azure. Platforma Spark udostępnia wbudowane biblioteki uczenia maszynowego. W tym przykładzie stosowana jest *Klasyfikacja* przy użyciu regresji logistycznej.

SparkML i MLlib to podstawowe biblioteki platformy Spark, które udostępniają wiele narzędzi, które są przydatne w przypadku zadań uczenia maszynowego, w tym narzędzi, które są odpowiednie dla:

- Klasyfikacja
- Regresja
- Klastrowanie
- Modelowanie tematów
- Wieloskładnikowa dekompozycja wartości (SVD) i podstawowa analiza składników (PPW)
- Testowanie hipotez i Obliczanie statystyk przykładowych

## <a name="understand-classification-and-logistic-regression"></a>Zrozumienie klasyfikacji i regresji logistycznej

*Klasyfikacja*, popularne zadanie uczenia maszynowego, to proces sortowania danych wejściowych do kategorii. Jest to zadanie algorytmu klasyfikacji, aby ustalić sposób przypisywania *etykiet* do danych wejściowych dostarczanych przez użytkownika. Można na przykład traktować algorytm uczenia maszynowego, który akceptuje informacje o zapasach jako dane wejściowe i dzieli zasoby na dwie kategorie: zasoby, które powinny być sprzedawane i zasoby, które powinny być przechowywane.

*Regresja logistyczna* to algorytm, którego można użyć do klasyfikacji. Interfejs API regresji logistycznej platformy Spark jest przydatny w przypadku *klasyfikacji binarnej* lub klasyfikowania danych wejściowych do jednej z dwóch grup. Aby uzyskać więcej informacji na temat regresji logistycznej, zobacz [witrynę Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Podsumowując, proces regresji logistycznej wytwarza *funkcję logistyczną* , która może służyć do przewidywania prawdopodobieństwa, że wektor wejściowy należy do jednej grupy lub drugiego.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Przykład analizy predykcyjnej w przypadku danych z NYC taksówkami

W tym przykładzie używasz platformy Spark do przeprowadzenia analizy predykcyjnej na podstawie danych TIP podróży z Nowego Jorku. Dane są dostępne za pomocą [otwartych zestawów danych platformy Azure](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Ten podzestaw zestawu danych zawiera informacje dotyczące żółtej podróży z taksówką, w tym informacje o każdej podróży, godzinie rozpoczęcia i zakończenia oraz lokalizacji, kosztu i innych interesujących atrybutów.

> [!IMPORTANT]
> Za ściąganie danych z lokalizacji magazynu mogą być naliczane dodatkowe opłaty.

W poniższych krokach opracowujesz model do przewidywania, czy konkretny rejs zawiera poradę, czy nie.

## <a name="create-an-apache-spark--machine-learning-model"></a>Tworzenie modelu uczenia maszynowego Apache Spark

1. Utwórz Notes przy użyciu jądra PySpark. Aby uzyskać instrukcje, zobacz [Tworzenie notesu](../quickstart-apache-spark-notebook.md#create-a-notebook).
2. Zaimportuj typy wymagane dla tej aplikacji. Skopiuj i wklej następujący kod do pustej komórki, a następnie naciśnij klawisze **SHIFT + ENTER** lub Uruchom komórkę przy użyciu niebieskiej ikony odtwarzania z lewej strony kodu.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp, date_format, col, when
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    Ze względu na jądro PySpark nie trzeba jawnie tworzyć kontekstów. Kontekst platformy Spark jest tworzony automatycznie po uruchomieniu pierwszej komórki kodu.

## <a name="construct-the-input-dataframe"></a>Konstruowanie wejściowej ramki danych

Ponieważ dane pierwotne są w formacie Parquet, można użyć kontekstu Spark, aby ściągnąć plik do pamięci jako element danych bezpośrednio. Chociaż Poniższy kod używa opcji domyślnych, możliwe jest wymuszenie mapowania typów danych i innych atrybutów schematu, jeśli jest to konieczne.

1. Uruchom następujące wiersze, aby utworzyć ramkę danych Spark, wklejając kod w nowej komórce. Spowoduje to pobranie danych za pośrednictwem interfejsu API Open DataSets. Ściąganie wszystkich tych danych spowoduje wygenerowanie około 1 500 000 000 wierszy. W zależności od rozmiaru puli Apache Spark bezserwerowej (wersja zapoznawcza) dane pierwotne mogą być zbyt duże lub zbyt dużo czasu na wykonanie operacji. Dane można filtrować do mniejszej liczby. Poniższy przykład kodu używa start_date i end_date do zastosowania filtru zwracającego pojedynczy miesiąc danych.

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. Minusem do prostego filtrowania polega na tym, że z perspektywy statystycznej, może wprowadzić bias do danych. Innym podejściem jest użycie próbkowania wbudowanego w platformę Spark. Poniższy kod zmniejsza zestaw danych w dół do około 2000 wierszy, jeśli są stosowane po powyższym kodzie. Ten krok próbkowania może być używany zamiast prostego filtru lub w połączeniu z prostym filtrem.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. Teraz można przeglądać dane, aby zobaczyć, co zostało odczytane. Zwykle lepszym rozwiązaniem jest przejrzenie danych z podzestawem, a nie z pełnym zestawem, w zależności od rozmiaru zestawu danych. Poniższy kod oferuje dwa sposoby wyświetlania danych: była to podstawowa, a druga, zapewniająca znacznie bogatsze środowisko siatki, a także możliwość wizualizacji danych graficznie.

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. W zależności od rozmiaru wygenerowanego zestawu danych i konieczności eksperymentowania lub uruchamiania notesu wiele razy może być zalecane buforowanie zestawu danych lokalnie w obszarze roboczym. Istnieją trzy sposoby przeprowadzenia jawnej pamięci podręcznej:

   - Zapisz lokalnie ramkę danych jako plik
   - Zapisz ramkę danych jako tabelę tymczasową lub widok
   - Zapisz ramkę danych jako tabelę trwałą

Pierwsze 2 z tych metod są zawarte w poniższym przykładzie kodu.

Tworzenie tabeli tymczasowej lub widoku zapewnia różne ścieżki dostępu do danych, ale tylko w czasie trwania sesji wystąpienia Spark.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="prepare-the-data"></a>Przygotowywanie danych

Dane w jego pierwotnej postaci często nie są odpowiednie do bezpośredniego przekazywania do modelu. Należy wykonać serię działań na danych, aby uzyskać je w stanie, w którym model może go wykorzystać.

W kodzie poniżej czterech klas operacji są wykonywane:

- Usuwanie elementów wartości odstających/nieprawidłowych za pomocą filtrowania.
- Usuwanie kolumn, które nie są zbędne.
- Tworzenie nowych kolumn pochodzących od danych pierwotnych, aby model działał bardziej efektywnie, czasami nazywany cechowania.
- Etykietowanie — ponieważ obejmujesz klasyfikację binarną (czy jest to Porada lub nie w danej podróży), istnieje potrzeba przekonwertowania kwoty Porada na wartość 0 lub 1.

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

Następnie w celu dodania funkcji końcowych zostanie wykonane drugie przekazanie danych.

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

Ostatnim zadaniem jest przekonwertowanie etykiet danych na format, który może być analizowany przez regresję logistyczną. Wejście do algorytmu regresji logistycznej musi być zestawem *par wektorów funkcji etykiet*, gdzie *wektor funkcji* jest wektorem liczb reprezentujących punkt wejściowy. Dlatego musimy przekonwertować kolumny kategorii na liczby. `trafficTimeBins`Kolumny i `weekdayString` wymagają konwersji na reprezentacje typu Integer. Istnieje wiele metod wykonywania konwersji, ale podejście wykonywane w tym przykładzie to *OneHotEncoding*, typowe podejście.

```python
# Since the sample uses an algorithm that only works with numeric features, convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Ta akcja powoduje, że Nowa ramka danych ze wszystkimi kolumnami w odpowiednim formacie do uczenia modelu.

## <a name="train-a-logistic-regression-model"></a>Uczenie modelu regresji logistycznej

Pierwsze zadanie polega na podzieleniu zestawu danych na zestaw szkoleniowy i test lub zestaw walidacji. Podział w tym miejscu jest dowolny i należy go odtworzyć przy użyciu różnych ustawień podziału, aby sprawdzić, czy wpływają one na model.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Teraz, gdy istnieją dwa ramki danych, następnym zadaniem jest utworzenie formuły modelu i uruchomienie jej w odniesieniu do ramki danych szkoleniowych, a następnie zweryfikowanie względem testowanej ramki danych. Należy eksperymentować z różnymi wersjami formuły modelu, aby zobaczyć wpływ różnych kombinacji.

> [!Note]
> Aby zapisać model, należy potrzebować roli platformy Azure obiektów blob magazynu. W obszarze konto magazynu przejdź do pozycji Access Control (IAM), a następnie wybierz pozycję **Dodaj przypisanie roli**. Przypisywanie danych obiektu blob magazynu współautor roli platformy Azure do serwera SQL Database. Tylko członkowie z uprawnieniami właściciela mogą wykonać ten krok. W przypadku różnych wbudowanych ról platformy Azure Zapoznaj się z tym [przewodnikiem](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

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

Dane wyjściowe z tej komórki to:

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Utwórz wizualną reprezentację przewidywania

Teraz możesz skonstruować ostateczną wizualizację, aby pomóc Ci w podaniu wyników testu. [Krzywa Roc](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) jest jednym ze sposobów, aby sprawdzić wynik.

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

![Krzywa ROC dla modelu TIP regresji logistycznej](./media/apache-spark-machine-learning-mllib-notebook/nyc-taxi-roc.png)

## <a name="shut-down-the-spark-instance"></a>Zamknij wystąpienie platformy Spark

Po zakończeniu uruchamiania aplikacji Zamknij Notes, aby zwolnić zasoby, zamykając kartę lub wybierz pozycję **Zakończ sesję** w panelu stanu w dolnej części notesu.

## <a name="see-also"></a>Zobacz też

- [Przegląd: Apache Spark w usłudze Azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja platformy .NET dla Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark Oficjalna dokumentacja](https://spark.apache.org/docs/2.4.5/)

>[!NOTE]
> Niektóre oficjalne dokumenty Apache Spark opierają się na użyciu konsoli platformy Spark, która nie jest dostępna w usłudze Azure Synapse Spark. Zamiast tego należy użyć [notesu](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) lub środowiska [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .
