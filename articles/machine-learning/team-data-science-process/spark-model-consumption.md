---
title: Operacjonalizować Spark — skompilowane modele uczenia maszynowego — proces nauki danych zespołu
description: Jak ładować i zdobywać modele szkoleniowe przechowywane w usłudze Azure Blob Storage (WASB) przy użyciu języka Python.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: bb38a76de41885b6f39a1c6dce7c44bcb52a4d60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86027447"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Operacjonalizować platformy Spark — skompilowane modele uczenia maszynowego

W tym temacie pokazano, jak operacjonalizować zapisany model uczenia maszynowego (ML) przy użyciu języka Python w klastrach usługi HDInsight Spark. Opisano w nim sposób ładowania modeli uczenia maszynowego, które zostały skompilowane przy użyciu platformy Spark MLlib i są przechowywane w usłudze Azure Blob Storage (WASB), oraz sposobu oceny ich z zestawami danych, które zostały również zapisane w WASB. Pokazuje, jak wstępnie przetwarzać dane wejściowe, przekształcać funkcje przy użyciu funkcji indeksowania i kodowania w zestawie narzędzi MLlib oraz jak utworzyć obiekt danych z etykietami, który może być używany jako dane wejściowe do oceniania z modelami ML. Modele używane do oceny obejmują regresję liniową, regresję logistyczną, losowe modele lasów i modele drzewa zwiększające gradienty.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Klastry Spark i notesy Jupyter
Kroki konfiguracji i kod operacjonalizować modelu ML są dostępne w tym instruktażu w celu użycia klastra usługi HDInsight Spark 1,6 oraz klastra Spark 2,0. Kod dla tych procedur jest również udostępniany w notesach Jupyter.

### <a name="notebook-for-spark-16"></a>Notes dla platformy Spark 1,6
Notes [pySpark-Machine-Learning-Data-nauka-Spark-model-zużycie. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) Jupyter pokazuje, jak operacjonalizować zapisany model przy użyciu języka Python w klastrach usługi HDInsight. 

### <a name="notebook-for-spark-20"></a>Notes dla platformy Spark 2,0
Aby zmodyfikować Notes Jupyter dla platformy Spark 1,6 do użycia z klastrem usługi HDInsight Spark 2,0, Zastąp plik kodu Python [tym plikiem](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Ten kod pokazuje, jak zużywać modele utworzone w platformie Spark 2,0.


## <a name="prerequisites"></a>Wymagania wstępne

1. Aby ukończyć ten przewodnik, musisz mieć konto platformy Azure i klaster usługi HDInsight 1,6 (lub Spark 2,0). Zapoznaj się z instrukcjami dotyczącymi sposobu spełniania tych wymagań, zobacz [Omówienie analizy danych przy użyciu platformy Spark w usłudze Azure HDInsight](spark-overview.md) . Ten temat zawiera również opis danych o wykorzystaniu NYC 2013 w tym miejscu oraz instrukcje dotyczące wykonywania kodu z notesu Jupyter w klastrze Spark. 
2. Utwórz modele uczenia maszynowego, które mają zostać ocenione w tym miejscu przez zapoznanie się z tematem [eksplorowanie i modelowanie danych za pomocą platformy Spark](spark-data-exploration-modeling.md) dla klastra Spark 1,6 lub notesów platformy Spark 2,0. 
3. W przypadku notesów platformy Spark 2,0 użyto dodatkowego zestawu danych dla zadania klasyfikacji, dobrze znanego zestawu danych wyjściowych w czasie od 2011 i 2012. Opis notesów i linków do nich znajduje się w [README.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) dla repozytorium GitHub zawierającego je. Ponadto kod i połączone notesy są ogólne i powinny być wykonywane w dowolnym klastrze Spark. Jeśli nie korzystasz z usługi HDInsight Spark, kroki instalacji i zarządzania klastrami mogą się nieco różnić od tego, co jest widoczne w tym miejscu. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Konfiguracja: lokalizacje magazynu, biblioteki i wstępnie ustawiony kontekst platformy Spark
Platforma Spark może odczytywać i zapisywać dane w Azure Storage Blob (WASB). Wszystkie istniejące dane przechowywane w tym miejscu mogą być przetwarzane przy użyciu platformy Spark, a wyniki są przechowywane ponownie w WASB.

Aby zapisać modele lub pliki w WASB, należy prawidłowo określić ścieżkę. Do kontenera domyślnego dołączonego do klastra Spark można odwoływać się przy użyciu ścieżki rozpoczynającej się od: *"wasb///"*. Poniższy przykład kodu określa lokalizację danych do odczytu i ścieżkę katalogu magazynu modelu, w którym zapisano dane wyjściowe modelu. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Ustaw ścieżki katalogów dla lokalizacji przechowywania w WASB
Modele są zapisywane w: "wasb:///user/remoteuser/NYCTaxi/Models". Jeśli ta ścieżka nie jest poprawnie ustawiona, modele nie są ładowane na potrzeby oceniania.

Wyniki wynikowe zostały zapisane w: "wasb:///user/remoteuser/NYCTaxi/ScoredResults". Jeśli ścieżka do folderu jest niepoprawna, wyniki nie są zapisywane w tym folderze.   

> [!NOTE]
> Lokalizacje ścieżki plików można kopiować i wklejać do symboli zastępczych w tym kodzie z poziomu danych wyjściowych ostatniej komórki z notesu **Machine-Learning-Data-nauka-ipynb** .   
> 
> 

Oto kod ustawiania ścieżek katalogów: 

```python
# LOCATION OF DATA TO BE SCORED (TEST DATA)
taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";

# SET THE MODEL STORAGE DIRECTORY PATH 
# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 

# SET SCORDED RESULT DIRECTORY PATH
# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 

# FILE LOCATIONS FOR THE MODELS TO BE SCORED
logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

# RECORD START TIME
import datetime
datetime.datetime.now()
```

**ROZDZIELCZOŚCI**

DateTime. DateTime (2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Importowanie bibliotek
Ustaw kontekst platformy Spark i zaimportuj niezbędne biblioteki o następujący kod

```python
#IMPORT LIBRARIES
import pyspark
from pyspark import SparkConf
from pyspark import SparkContext
from pyspark.sql import SQLContext
import matplotlib
import matplotlib.pyplot as plt
from pyspark.sql import Row
from pyspark.sql.functions import UserDefinedFunction
from pyspark.sql.types import *
import atexit
from numpy import array
import numpy as np
import datetime
```

### <a name="preset-spark-context-and-pyspark-magics"></a>Wstępnie ustawiony kontekst Spark i PySpark MAGICS
Jądra PySpark, które są dostarczane z notesami Jupyter, mają kontekst predefiniowany. W związku z tym przed rozpoczęciem pracy z aplikacją, którą tworzysz, nie trzeba jawnie ustawiać kontekstów platformy Spark ani Hive. Te konteksty są domyślnie dostępne:

* SC — dla platformy Spark 
* SqlContext — dla programu Hive

Jądro PySpark zawiera wstępnie zdefiniowane "MAGICS", które są poleceniami specjalnymi, które można wywołać za pomocą%%. Istnieją dwa takie polecenia, które są używane w tych przykładach kodu.

* **%% lokalne** Określono, że kod w kolejnych wierszach jest wykonywany lokalnie. Kod musi być prawidłowym kodem w języku Python.
* **%% SQL-o \<variable name>** 
* Wykonuje zapytanie programu Hive względem elementu SqlContext. Jeśli parametr-o zostanie przesłany, wynik zapytania jest utrwalany w lokalnym kontekście języka Python%% jako Pandas Dataframe.

Aby uzyskać więcej informacji na temat jądra dla notesów Jupyter oraz wstępnie zdefiniowanych "magicznych", zobacz [jądra dostępne dla notesów Jupyter z klastrami usługi HDInsight Spark Linux w usłudze HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Pozyskiwanie danych i tworzenie oczyszczonej ramki danych
Ta sekcja zawiera kod serii zadań wymaganych do pozyskiwania danych do oceny. Zapoznaj się z przyłączonym do 0,1% przykładem pliku podróży i opłatami (zapisanym jako plik. tsv), sformatuj dane, a następnie tworzy czystą ramkę danych.

Pliki podróży i opłat za taksówkę zostały dołączone na podstawie procedury podanej w temacie: [proces analizy danych zespołu w działaniu: korzystanie z klastrów usługi HDInsight Hadoop](hive-walkthrough.md) .

```python
# INGEST DATA AND CREATE A CLEANED DATA FRAME

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT FILE FROM PUBLIC BLOB
taxi_test_file = sc.textFile(taxi_test_file_loc)

# GET SCHEMA OF THE FILE FROM HEADER
taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)

# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))

# GET SCHEMA OF THE FILE FROM HEADER
schema_string = taxi_test_file.first()
fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
fields[7].dataType = IntegerType() #Pickup hour
fields[8].dataType = IntegerType() # Pickup week
fields[9].dataType = IntegerType() # Weekday
fields[10].dataType = IntegerType() # Passenger count
fields[11].dataType = FloatType() # Trip time in secs
fields[12].dataType = FloatType() # Trip distance
fields[19].dataType = FloatType() # Fare amount
fields[20].dataType = FloatType() # Surcharge
fields[21].dataType = FloatType() # Mta_tax
fields[22].dataType = FloatType() # Tip amount
fields[23].dataType = FloatType() # Tolls amount
fields[24].dataType = FloatType() # Total amount
fields[25].dataType = IntegerType() # Tipped or not
fields[26].dataType = IntegerType() # Tip class
taxi_schema = StructType(fields)

# CREATE DATA FRAME
taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)

# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
    .drop('direct_distance').drop('surcharge')\
    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_test_cleaned.cache()
taxi_df_test_cleaned.count()

# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
taxi_df_test_cleaned.registerTempTable("taxi_test")

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ROZDZIELCZOŚCI**

Czas wykonania powyżej komórki: 46,37 s

## <a name="prepare-data-for-scoring-in-spark"></a>Przygotowywanie danych do oceniania w platformie Spark
W tej sekcji przedstawiono sposób indeksowania, kodowania i skalowania funkcji kategorii w celu przygotowania ich do użycia w algorytmach uczenia nadzorowanego MLlib na potrzeby klasyfikacji i regresji.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Przekształcanie funkcji: Indeksuj i Koduj funkcje kategorii dla danych wejściowych do modeli na potrzeby oceniania
W tej sekcji pokazano, jak indeksować dane kategorii przy użyciu `StringIndexer` i kodować funkcje z `OneHotEncoder` danymi wejściowymi do modeli.

[StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) koduje kolumnę ciągów etykiet do kolumny indeksów etykiet. Indeksy są uporządkowane według częstotliwości etykiet. 

[OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) mapuje kolumnę indeksów etykiet do kolumny wektorów binarnych z co najwyżej jedną wartością. To kodowanie umożliwia stosowanie algorytmów, które oczekują funkcji ciągłego wyceniania, takich jak regresja logistyczna, do zastosowania do funkcji kategorii.

```python
#INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_test 
"""
taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_test_with_newFeatures.cache()
taxi_df_test_with_newFeatures.count()

# INDEX AND ONE-HOT ENCODING
stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
indexed = model.transform(taxi_df_test_with_newFeatures)
encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
encoded1 = encoder.transform(indexed)

# INDEX AND ENCODE RATE_CODE
stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
model = stringIndexer.fit(encoded1)
indexed = model.transform(encoded1)
encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
encoded2 = encoder.transform(indexed)

# INDEX AND ENCODE PAYMENT_TYPE
stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
model = stringIndexer.fit(encoded2)
indexed = model.transform(encoded2)
encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
encoded3 = encoder.transform(indexed)

# INDEX AND ENCODE TRAFFIC TIME BINS
stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
model = stringIndexer.fit(encoded3)
indexed = model.transform(encoded3)
encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
encodedFinal = encoder.transform(indexed)

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ROZDZIELCZOŚCI**

Czas wykonania powyżej komórki: 5,37 s

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Tworzenie obiektów RDD z tablicami funkcji dla danych wejściowych do modeli
Ta sekcja zawiera kod, który pokazuje, jak indeksować kategorii dane tekstowe jako obiekt RDD i jeden-gorąca, tak aby można go było używać do uczenia i testowania regresji MLlib logistycznej i modeli opartych na drzewie. Indeksowane dane są przechowywane w [odpornych obiektów zestawu danych rozproszonych (RDD)](https://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) . Odporne to podstawowe streszczenie w platformie Spark. Obiekt RDD reprezentuje niezmienne, partycjonowane kolekcje elementów, które mogą być obsługiwane równolegle z platformą Spark.

Zawiera również kod, który pokazuje, jak skalować dane za pomocą `StandardScalar` MLlib na potrzeby regresji liniowej z stochastycznego gradientem (SGD), popularnym algorytmem do uczenia szerokiego zakresu modeli uczenia maszynowego. [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) jest używany do skalowania funkcji do wariancji jednostek. Skalowanie funkcji, znane także jako Normalizacja danych, pozwala upewnić się, że funkcje o ogólnie wykorzystanych wartościach nie mają nadmiernej wagi w funkcji celu. 

```python
# CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT LIBRARIES
from pyspark.mllib.linalg import Vectors
from pyspark.mllib.feature import StandardScaler, StandardScalerModel
from pyspark.mllib.util import MLUtils
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    return  features

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    return  features

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingRegression(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    return  features

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
def parseRowOneHotRegression(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    return  features

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)

# FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)

# SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
oneHotTESTregScaled = scaler.transform(oneHotTESTreg)

# CACHE RDDS IN MEMORY
indexedTESTbinary.cache();
oneHotTESTbinary.cache();
indexedTESTreg.cache();
oneHotTESTreg.cache();
oneHotTESTregScaled.cache();

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ROZDZIELCZOŚCI**

Czas wykonania powyżej komórki: 11,72 s

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Wyniki z modelem regresji logistycznej i zapisywanie danych wyjściowych w obiekcie blob
W kodzie w tej sekcji przedstawiono sposób ładowania modelu regresji logistycznej, który został zapisany w usłudze Azure Blob Storage, i używania go do przewidywania, czy Porada jest płatna za podróżą z użyciem taksówki, oceny jej przy użyciu standardowych metryk klasyfikacji, a następnie zapisywania i wykreślania wyników w usłudze BLOB Storage. Wyniki wynikowe są przechowywane w obiektach RDD. 

```python
# SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT LIBRARIES
from pyspark.mllib.classification import LogisticRegressionModel

## LOAD SAVED MODEL
savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))

## SAVE SCORED RESULTS (RDD) TO BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
dirfilename = scoredResultDir + logisticregressionfilename;
predictions.saveAsTextFile(dirfilename)


# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**ROZDZIELCZOŚCI**

Czas wykonania powyżej komórki: 19,22 s

## <a name="score-a-linear-regression-model"></a>Ocena modelu regresji liniowej
Używamy [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) do uczenia modelu regresji liniowej przy użyciu gradientu STOCHASTYCZNEGO (SGD) do optymalizacji, aby przewidzieć płatną liczbę Porada. 

W kodzie w tej sekcji pokazano, jak załadować model regresji liniowej z usługi Azure Blob Storage, oceny przy użyciu zmiennych skalowanych, a następnie zapisać wyniki z powrotem do obiektu BLOB.

```python
#SCORE LINEAR REGRESSION MODEL

# RECORD START TIME
timestart = datetime.datetime.now()

#LOAD LIBRARIES
from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel

# LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
dirfilename = scoredResultDir + linearregressionfilename;
predictions.saveAsTextFile(dirfilename)

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ROZDZIELCZOŚCI**

Czas wykonania powyżej komórki: 16,63 s

## <a name="score-classification-and-regression-random-forest-models"></a>Klasyfikowanie modeli lasów losowych i regresji
W kodzie w tej sekcji pokazano, jak załadować zapisane modele lasów klasyfikacji i regresji losowo zapisane w usłudze Azure Blob Storage, oceny wydajności za pomocą klasyfikatora standardowego i miar regresji, a następnie zapisać wyniki z powrotem do magazynu obiektów BLOB.

[Losowe lasy](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) są kompletnymi drzewami decyzyjnymi.  Łączą się z wieloma drzewami decyzyjnymi, aby zmniejszyć ryzyko naruszenia. Lasy losowe mogą obsługiwać funkcje kategorii, dołączać do ustawienia klasyfikacji wieloklasowej, nie wymagają skalowania funkcji i mogą przechwytywać interakcje z funkcjami. Losowe lasy to jeden z najbardziej pomyślnych modeli uczenia maszynowego na potrzeby klasyfikacji i regresji.

[platforma Spark. mllib](https://spark.apache.org/mllib/) obsługuje losowe lasy do klasyfikacji binarnej i wieloklasowej oraz do regresji przy użyciu funkcji ciągłego i kategorii. 

```python
# SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT MLLIB LIBRARIES    
from pyspark.mllib.tree import RandomForest, RandomForestModel


# CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
predictions = savedModel.predict(indexedTESTbinary)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
dirfilename = scoredResultDir + rfclassificationfilename;
predictions.saveAsTextFile(dirfilename)


# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
predictions = savedModel.predict(indexedTESTreg)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
dirfilename = scoredResultDir + rfregressionfilename;
predictions.saveAsTextFile(dirfilename)

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**ROZDZIELCZOŚCI**

Czas wykonania powyżej komórki: 31,07 s

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Klasyfikacja oceny i modele drzewa zwiększające gradienty
W kodzie w tej sekcji przedstawiono sposób ładowania modeli drzewa zwiększania gradientów klasyfikacji i regresji z usługi Azure Blob Storage, oceny ich wydajności przy użyciu miar standardowego klasyfikatora i regresji, a następnie zapisywania wyników z powrotem do magazynu obiektów BLOB. 

**platforma Spark. mllib** obsługuje GBTS dla klasyfikacji binarnej oraz regresję przy użyciu funkcji ciągłego i kategorii. 

[Drzewa podwyższające liczbę gradientów](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) są kompletnymi drzewami decyzyjnymi. GBTSe drzewa decyzyjne w sposób iteracyjny, aby zminimalizować funkcję strat. Program GBTS może obsługiwać funkcje kategorii, nie wymaga skalowania funkcji i może przechwytywać interakcje i funkcje. Ten algorytm może być również używany w ustawieniu klasyfikacji wieloklasowej.

```python
# SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT MLLIB LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

#LOAD AND SCORE THE MODEL
savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
predictions = savedModel.predict(indexedTESTbinary)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
dirfilename = scoredResultDir + btclassificationfilename;
predictions.saveAsTextFile(dirfilename)


# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

# LOAD AND SCORE MODEL 
savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
predictions = savedModel.predict(indexedTESTreg)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
dirfilename = scoredResultDir + btregressionfilename;
predictions.saveAsTextFile(dirfilename)


# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ROZDZIELCZOŚCI**

Czas wykonania powyżej komórki: 14,6 s

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Czyszczenie obiektów z pamięci i drukowanie wynikowych lokalizacji plików

```python
# UNPERSIST OBJECTS CACHED IN MEMORY
taxi_df_test_cleaned.unpersist()
indexedTESTbinary.unpersist();
oneHotTESTbinary.unpersist();
indexedTESTreg.unpersist();
oneHotTESTreg.unpersist();
oneHotTESTregScaled.unpersist();


# PRINT OUT PATH TO SCORED OUTPUT FILES
print "logisticRegFileLoc: " + logisticregressionfilename;
print "linearRegFileLoc: " + linearregressionfilename;
print "randomForestClassificationFileLoc: " + rfclassificationfilename;
print "randomForestRegFileLoc: " + rfregressionfilename;
print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
print "BoostedTreeRegressionFileLoc: " + btregressionfilename;
```

**ROZDZIELCZOŚCI**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05 -0317 _22_ 58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Korzystanie z modeli platformy Spark za pomocą interfejsu sieci Web
Platforma Spark zapewnia mechanizm zdalnego przesyłania zadań wsadowych lub interakcyjnych zapytań za pomocą interfejsu REST ze składnikiem o nazwie usługi Livy. Usługi Livy jest domyślnie włączona w klastrze usługi HDInsight Spark. Aby uzyskać więcej informacji na temat usługi Livy, zobacz: [przesyłanie zadań platformy Spark zdalnie przy użyciu usługi Livy](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

Możesz użyć usługi Livy, aby zdalnie przesłać zadanie wsadowe ocenia plik, który jest przechowywany w obiekcie blob platformy Azure, a następnie zapisuje wyniki w innym obiekcie blob. W tym celu należy przekazać skrypt języka Python z  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) do obiektu BLOB klastra Spark. Aby skopiować skrypt do obiektu BLOB klastra, można użyć narzędzia, takiego jak **Eksplorator usługi Microsoft Azure Storage** lub **AzCopy** . W naszym przypadku przekazano skrypt do ***wasb:///example/Python/ConsumeGBNYCReg.py***.   

> [!NOTE]
> Wymagane klucze dostępu można znaleźć w portalu dla konta magazynu skojarzonego z klastrem Spark. 
> 
> 

Po przekazaniu do tej lokalizacji ten skrypt jest uruchamiany w klastrze Spark w kontekście rozproszonym. Ładuje on model i uruchamia przewidywania dotyczące plików wejściowych opartych na modelu.  

Możesz wywołać ten skrypt zdalnie, tworząc proste żądanie HTTPS/REST na usługi Livy.  Poniżej znajduje się polecenie zwinięcie, które służy do konstruowania żądania HTTP w celu zdalnego wywołania skryptu języka Python. Zastąp CLUSTERLOGIN, CLUSTERPASSWORD, CLUSTERname odpowiednimi wartościami dla klastra Spark.

```console
# CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches
```

Można użyć dowolnego języka w systemie zdalnym do wywołania zadania Spark za pośrednictwem usługi Livy, tworząc proste wywołanie HTTPS z uwierzytelnianiem podstawowym.   

> [!NOTE]
> Podczas wykonywania tego wywołania HTTP warto używać biblioteki żądań języka Python, ale nie jest ona obecnie instalowana domyślnie w Azure Functions. Zamiast tego używane są starsze biblioteki HTTP.   
> 
> 

Oto kod języka Python dla wywołania HTTP:

```python
#MAKE AN HTTPS CALL ON LIVY. 

import os

# OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILABLE BY DEFAULT
import httplib, urllib, base64

# REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
host = '<spark cluster name>.azurehdinsight.net:443'
username='<username>'
password='<password>'

#AUTHORIZATION
conn = httplib.HTTPSConnection(host)
auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}

# SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
# IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
response = conn.getresponse().read()
print(response)
conn.close()
```

Możesz również dodać ten kod w języku Python, aby [Azure Functions](https://azure.microsoft.com/documentation/services/functions/) wyzwolić przesłane zadanie Spark, które ocenia obiekt BLOB na podstawie różnych zdarzeń, takich jak czasomierz, tworzenie lub Aktualizacja obiektu BLOB. 

Jeśli wolisz korzystać z kodu bezpłatnego klienta, użyj [Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) do wywołania oceny wsadowej Spark przez zdefiniowanie akcji HTTP w **projektancie Logic Apps** i ustawienie jej parametrów. 

* W obszarze Azure Portal Utwórz nową aplikację logiki, wybierając pozycję **+ Nowy**  ->  **Sieć Web + aplikacje mobilne**  ->  **aplikację logiki**. 
* Aby wyświetlić **projektanta Logic Apps**, wprowadź nazwę aplikacji logiki i App Service plan.
* Wybierz akcję HTTP i wprowadź parametry pokazane na poniższym rysunku:

![Projektant usługi Logic Apps](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>Co dalej?
**Czyszczenie danych krzyżowych i parametrów**: zobacz [Zaawansowane eksplorowanie i modelowanie w systemie Spark,](spark-advanced-data-exploration-modeling.md) w jaki sposób można przeszkoleć modele przy użyciu weryfikacji krzyżowej i funkcji Hyper-Parameter.

