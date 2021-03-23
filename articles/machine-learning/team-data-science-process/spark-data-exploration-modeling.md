---
title: Eksplorowanie i modelowanie danych za pomocą procesu analizy danych w zespole platformy Spark
description: Prezentuje możliwości eksploracji i modelowania zestawu narzędzi Spark MLlib w usłudze HDInsight Spark.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: sample
ms.date: 06/03/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperf-fy20q4
ms.openlocfilehash: 531e5cf274d40eae19075514d50aec5a6524aeec
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775344"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Eksplorowanie i modelowanie danych za pomocą platformy Spark

Dowiedz się, jak używać usługi HDInsight Spark do uczenia modeli uczenia maszynowego do prognozowania opłat za taksówkę przy użyciu platformy Spark MLlib

Ten przykład przedstawia różne kroki [procesu nauki o danych zespołowych](./index.yml). Podzbiór NYCego i taryfy czasowej 2013 jest używany do ładowania, eksplorowania i przygotowywania danych. Następnie, korzystając z MLlib Spark, modele klasyfikacji i regresji binarnej są przeszkolone, aby przewidzieć, czy Porada zostanie zapłacona za podróż, i oszacować kwotę tej porady.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz mieć konto platformy Azure i klaster usługi HDInsight 1,6 (lub Spark 2,0). Zapoznaj się z instrukcjami dotyczącymi sposobu spełniania tych wymagań, zobacz [Omówienie analizy danych przy użyciu platformy Spark w usłudze Azure HDInsight](spark-overview.md) . Ten temat zawiera również opis danych o wykorzystaniu NYC 2013 w tym miejscu oraz instrukcje dotyczące wykonywania kodu z notesu Jupyter w klastrze Spark. 

### <a name="spark-clusters-and-notebooks"></a>Klastry Spark i notesy

Kroki instalacji i kod zostały przedstawione w tym przewodniku dotyczącym korzystania z usługi HDInsight Spark 1,6. Jednak dla klastrów usługi HDInsight Spark 1,6 i Spark 2,0 są udostępniane notesy Jupyter. Opis notesów i linków do nich znajduje się w [README.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) dla repozytorium GitHub zawierającego je. Ponadto kod i połączone notesy są ogólne i powinny być wykonywane w dowolnym klastrze Spark. Jeśli nie korzystasz z usługi HDInsight Spark, kroki instalacji i zarządzania klastrami mogą się nieco różnić od tego, co jest widoczne w tym miejscu. Dla wygody poniżej przedstawiono linki do notesów Jupyter dla platformy Spark 1,6 (do uruchomienia w jądrze pySpark serwera Jupyter Notebook) i platformy Spark 2,0 (do uruchomienia w jądrze pySpark3 serwera Jupyter Notebook):

- [Notesy platformy Spark 1,6](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/): zawierają informacje dotyczące sposobu przeprowadzania eksploracji danych, modelowania i oceniania przy użyciu kilku różnych algorytmów.
- [Notesy platformy Spark 2,0](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/): zawierają informacje dotyczące wykonywania zadań związanych z regresją i klasyfikacją. Zestawy danych mogą się różnić, ale kroki i koncepcje dotyczą różnych zestawów danych.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

> [!NOTE]
> Poniższe opisy dotyczą korzystania z platformy Spark 1,6. W przypadku wersji Spark 2,0 użyj notesów opisanych powyżej. 

## <a name="setup"></a>Konfiguracja

Platforma Spark może odczytywać i zapisywać dane w Azure Storage Blob (znane również jako WASB). Wszystkie istniejące dane przechowywane w tym miejscu mogą być przetwarzane przy użyciu platformy Spark, a wyniki są przechowywane ponownie w WASB.

Aby zapisać modele lub pliki w WASB, należy prawidłowo określić ścieżkę. Do kontenera domyślnego dołączonego do klastra Spark można odwoływać się przy użyciu ścieżki rozpoczynającej się od ciągu: "wasb:///". Do innych lokalizacji odwołuje się "wasb://".

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Ustaw ścieżki katalogów dla lokalizacji przechowywania w WASB

Poniższy przykład kodu określa lokalizację danych do odczytu i ścieżkę katalogu magazynu modelu, do którego zapisano dane wyjściowe modelu:

```python
# SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

# LOCATION OF TRAINING DATA
taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

# SET THE MODEL STORAGE DIRECTORY PATH 
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 
```

### <a name="import-libraries"></a>Importowanie bibliotek

Konfiguracja wymaga również importowania niezbędnych bibliotek. Ustaw kontekst platformy Spark i zaimportuj niezbędne biblioteki przy użyciu następującego kodu:

```python
# IMPORT LIBRARIES
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

Jądra PySpark, które są dostarczane z notesami Jupyter, mają kontekst predefiniowany. Dlatego przed rozpoczęciem pracy z aplikacją, którą tworzysz, nie trzeba jawnie ustawiać kontekstów platformy Spark ani Hive. Te konteksty są domyślnie dostępne dla Ciebie. Te konteksty są następujące:

* SC — dla platformy Spark 
* SqlContext — dla programu Hive

Jądro PySpark zawiera wstępnie zdefiniowane "MAGICS", które są poleceniami specjalnymi, które można wywołać za pomocą%%. Istnieją dwa takie polecenia, które są używane w tych przykładach kodu.

* **%% lokalne** Określa, że kod w kolejnych wierszach ma być wykonywany lokalnie. Kod musi być prawidłowym kodem w języku Python.
* **%% SQL-o \<variable name>** Wykonuje zapytanie programu Hive względem elementu SqlContext. Jeśli parametr-o zostanie przesłany, wynik zapytania jest utrwalany w lokalnym kontekście języka Python%% jako Pandas Dataframe.

Aby uzyskać więcej informacji na temat jądra notesu Jupyter i wstępnie zdefiniowanych "MAGICS", zobacz [jądra dostępne dla notesów Jupyter z klastrami usługi HDInsight Spark Linux w usłudze HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="load-the-data"></a>Ładowanie danych

Pierwszym krokiem w procesie nauki danych jest pozyskiwanie danych do analizy ze źródeł, gdzie znajdują się w środowisku eksploracji i modelowania danych. Środowisko to platforma Spark w tym instruktażu. Ta sekcja zawiera kod służący do ukończenia serii zadań:

* pozyskiwanie przykładu danych do modelowania
* odczyt w wejściowym zestawie danych (zapisany jako plik. tsv)
* formatowanie i czyszczenie danych
* Tworzenie i buforowanie obiektów (odporne lub ramek danych) w pamięci
* Zarejestruj ją jako tabelę tymczasową w kontekście SQL.

Oto kod dotyczący pozyskiwania danych.

```python
# INGEST DATA

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT FILE FROM PUBLIC BLOB
taxi_train_file = sc.textFile(taxi_train_file_loc)

# GET SCHEMA OF THE FILE FROM HEADER
schema_string = taxi_train_file.first()
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

# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))


# CREATE DATA FRAME
taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
    .drop('direct_distance').drop('surcharge')\
    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )


# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**ROZDZIELCZOŚCI**

Czas wykonania powyżej komórki: 51,72 s

## <a name="explore-the-data"></a>Eksplorowanie danych

Po przeprowadzeniu danych do platformy Spark następnym krokiem w procesie analizy danych jest dokładniejsze zrozumienie danych za pomocą eksploracji i wizualizacji. W tej sekcji analizujemy dane dotyczące taksówki przy użyciu zapytań SQL i kreślą zmienne docelowe oraz funkcje potencjalnej kontroli wzrokowej. W odróżnieniu od liczby osób korzystających z przedziałów w podróży, częstotliwości postanowień oraz sposobu, w jaki porady są różne według kwoty płatności i typu.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Wykreśl histogram częstotliwości liczby pasażerów w próbce podróży z taksówką

Ten kod i kolejne fragmenty kodu używają programu SQL Magic do wykonywania zapytań dotyczących przykładu i lokalnego Magic w celu wykreślenia danych.

* **SQL Magic ( `%%sql` )** jądro usługi HDInsight PySpark obsługuje łatwe wbudowane zapytania HiveQL względem elementu SqlContext. Argument (-o VARIABLE_NAME) utrwala dane wyjściowe zapytania SQL jako element Pandas Dataframe na serwerze Jupyter. To ustawienie powoduje, że dane wyjściowe są dostępne w trybie lokalnym.
* **`%%local` Magic** służy do uruchamiania kodu lokalnie na serwerze Jupyter, który jest węzła głównego klastra usługi HDInsight. Zwykle używasz `%%local` Magic w połączeniu z `%%sql` parametrem Magic with-o. Parametr-o będzie utrwalał dane wyjściowe zapytania SQL lokalnie, a następnie%% Local Magic wywoła następny zestaw fragmentów kodu do lokalnego uruchamiania na podstawie danych wyjściowych zapytań SQL, które są utrwalane lokalnie

Dane wyjściowe są automatycznie wizualizowane po uruchomieniu kodu.

To zapytanie pobiera liczbę podróży według liczby pasażerów. 

```sql
# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

# HIVEQL QUERY AGAINST THE sqlContext
%%sql -q -o sqlResults
SELECT passenger_count, COUNT(*) as trip_counts 
FROM taxi_train 
WHERE passenger_count > 0 and passenger_count < 7 
GROUP BY passenger_count 
```

Ten kod tworzy lokalną ramkę danych z wyników zapytania i wykreśla dane. `%%local`Magic tworzy lokalną ramkę danych, `sqlResults` która może być używana do wykreślania przy użyciu matplotlib. 

> [!NOTE]
> Ten PySpark Magic jest używany wiele razy w tym instruktażu. Jeśli ilość danych jest duża, należy przykładowo utworzyć ramkę danych, która może pasować do pamięci lokalnej.

```python
#CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
sqlResults
```

Oto kod do wykreślania podróży według liczby osób

```python
# PLOT PASSENGER NUMBER VS. TRIP COUNTS
%%local
import matplotlib.pyplot as plt
%matplotlib inline

x_labels = sqlResults['passenger_count'].values
fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
fig.set_xticklabels(x_labels)
fig.set_title('Counts of trips by passenger count')
fig.set_xlabel('Passenger count in trips')
fig.set_ylabel('Trip counts')
plt.show()
```

**ROZDZIELCZOŚCI**

![Częstotliwość podróży według liczby pasażerów](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Można wybrać różne typy wizualizacji (tabela, wykres kołowy, linia, obszar lub Słupek) za pomocą przycisków menu **Typ** w notesie. Wykres słupkowy jest pokazywany w tym miejscu.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Wykreśl histogram z kwot postanowień i informacje o tym, jak kwota pozostała w zależności od liczby pasażerów i ilości opłat.

Użyj zapytania SQL, aby uzyskać przykładowe dane.

```sql
# PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE

# HIVEQL QUERY AGAINST THE sqlContext
%%sql -q -o sqlResults
SELECT fare_amount, passenger_count, tip_amount, tipped 
FROM taxi_train 
WHERE passenger_count > 0 
AND passenger_count < 7 
AND fare_amount > 0 
AND fare_amount < 200 
AND payment_type in ('CSH', 'CRD') 
AND tip_amount > 0 
AND tip_amount < 25
```

Ta komórka kodu używa zapytania SQL do tworzenia trzech wykresów danych.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# TIP BY PASSENGER COUNT
ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 100, -2, 20])
plt.show()
```

**ROZDZIELCZOŚCI** 

![Dystrybucja kwoty TIP](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Kwota Porada wg liczby osób](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Kwota Porada według kwoty opłaty](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="prepare-the-data"></a>Przygotowywanie danych

W tej sekcji opisano i przedstawiono kod procedur służących do przygotowywania danych do użycia w modelu ML. Przedstawiono w nim sposób wykonywania następujących zadań:

* Utwórz nową funkcję przez pakowania godziny w przedziałach czasowych
* Indeksuj i Koduj funkcje kategorii
* Utwórz obiekty z etykietami punktów dla danych wejściowych w funkcjach ML
* Utwórz losowe Podpróbkowanie danych i podziel je na zestawy szkoleniowe i testowe
* Skalowanie cech
* Obiekty pamięci podręcznej w pamięci

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Utwórz nową funkcję przez pakowania godziny w przedziałach czasowych

Ten kod pokazuje, jak utworzyć nową funkcję przez pakowania godziny w przedziałach czasowych, a następnie jak buforować powstającą ramkę danych w pamięci. Gdy odporne rozproszone zestawy danych (odporne) i Frames są używane wielokrotnie, buforowanie prowadzi do lepszego czasu wykonania. Odpowiednio odporne i ramki danych w kilku etapach w przewodniku. 

```python
# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_train 
"""
taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
# THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
# MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

**ROZDZIELCZOŚCI**

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Indeksuj i Koduj funkcje kategorii dla danych wejściowych w funkcjach modelowania

W tej sekcji pokazano, jak indeksować lub kodować funkcje kategorii dla danych wejściowych w funkcjach modelowania. Funkcje modelowania i przewidywania MLlib wymagają funkcji z danymi wejściowymi kategorii, które mają być indeksowane lub kodowane przed użyciem. W zależności od modelu należy zaindeksować lub zakodować je na różne sposoby:  

* **Modelowanie oparte na drzewie** wymaga, aby kategorie były kodowane jako wartości liczbowe (na przykład funkcja z trzema kategoriami może być zakodowana za pomocą 0, 1, 2). Ten algorytm jest dostarczany przez funkcję [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) MLlib. Ta funkcja koduje kolumnę ciągów etykiet do kolumny indeksów etykiet, które są uporządkowane według częstotliwości etykiet. Chociaż indeksowane z wartościami liczbowymi dla danych wejściowych i obsługi danych, można określić algorytmy oparte na drzewie, aby traktować je odpowiednio jako kategorie. 
* **Modele regresji logistycznej i liniowej** wymagają kodowania jednostronicowego, gdzie na przykład funkcja z trzema kategoriami może być rozwinięta do trzech kolumn funkcji, z których każda zawiera 0 lub 1, w zależności od kategorii obserwacji. MLlib udostępnia funkcję [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) , która umożliwia jednostronicowe kodowanie. Ten koder mapuje kolumnę indeksów etykiet do kolumny wektorów binarnych z co najwyżej jedną wartością. To kodowanie umożliwia stosowanie algorytmów, które oczekują wartości numerycznych, takich jak regresja logistyczna, do zastosowania do funkcji kategorii.

Oto kod do indeksowania i kodowania funkcji kategorii:

```python
# INDEX AND ENCODE CATEGORICAL FEATURES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES    
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

# INDEX AND ENCODE VENDOR_ID
stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
indexed = model.transform(taxi_df_train_with_newFeatures)
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

# INDEX AND TRAFFIC TIME BINS
stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
model = stringIndexer.fit(encoded3)
indexed = model.transform(encoded3)
encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
encodedFinal = encoder.transform(indexed)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ROZDZIELCZOŚCI**

Czas wykonania powyżej komórki: 1,28 s

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Utwórz obiekty z etykietami punktów dla danych wejściowych w funkcjach ML

Ta sekcja zawiera kod, który pokazuje, jak indeksować kategorii dane tekstowe jako typ danych z etykietami i kodować go tak, aby mógł służyć do uczenia i testowania regresji logistycznej MLlib i innych modeli klasyfikacji. Obiekty z etykietami są odporne na rozproszone zestawy danych (RDD) sformatowane w sposób, który jest wymagany jako dane wejściowe za pomocą większości algorytmów ML w MLlib. [Punkt oznaczony etykietą](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) jest wektorem lokalnym, czyli gęstym lub rozrzedzonym, skojarzonym z etykietą/odpowiedzią.  

Ta sekcja zawiera kod, który pokazuje, jak indeksować kategorii dane tekstowe jako typ danych z [etykietami](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) i kodować go tak, aby mógł służyć do uczenia i testowania regresji logistycznej MLlib i innych modeli klasyfikacji. Obiekty z etykietami są odporne na rozproszone zestawy danych (RDD) składające się z etykiety (zmiennej Target/Response) i wektora funkcji. Ten format jest wymagany jako dane wejściowe przez wiele algorytmów ML w MLlib.

Oto kod służący do indeksowania i kodowania funkcji tekstowych dla klasyfikacji binarnej.

```python
# FUNCTIONS FOR BINARY CLASSIFICATION

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    labPt = LabeledPoint(line.tipped, features)
    return  labPt

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tipped, features)
    return  labPt
```

Oto kod służący do kodowania i indeksowania funkcji tekstu kategorii dla analizy regresji liniowej.

```python
# FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingRegression(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])

    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
def parseRowOneHotRegression(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt
```

### <a name="create-a-random-subsampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Utwórz losowe Podpróbkowanie danych i podziel je na zestawy szkoleniowe i testowe

Ten kod tworzy losowe próbkowanie danych (w tym miejscu użyto 25%). Chociaż nie jest to wymagane na potrzeby tego przykładu ze względu na rozmiar zestawu danych, pokazujemy, jak można ją przykładować w tym miejscu, aby wiedzieć, jak używać go do własnego problemu w razie potrzeby. Gdy próbki są duże, próbkowanie może znacznie zaoszczędzić czas podczas modeli szkoleniowych. Następnie wyodrębnimy przykład do części szkoleniowej (75% tutaj) i części testowej (25% tutaj) do użycia w ramach modelowania klasyfikacji i regresji.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.sql.functions import rand

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
samplingFraction = 0.25;
trainingFraction = 0.75; testingFraction = (1-trainingFraction);
seed = 1234;
encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
indexedTESTbinary = testData.map(parseRowIndexingBinary)
oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
oneHotTESTbinary = testData.map(parseRowOneHotBinary)

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg = trainData.map(parseRowIndexingRegression)
indexedTESTreg = testData.map(parseRowIndexingRegression)
oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
oneHotTESTreg = testData.map(parseRowOneHotRegression)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ROZDZIELCZOŚCI**

Czas wykonania powyżej komórki: 0,24 sekund

### <a name="feature-scaling"></a>Skalowanie cech

Skalowanie funkcji, znane także jako Normalizacja danych, pozwala upewnić się, że funkcje o ogólnie wykorzystanych wartościach nie mają nadmiernej wagi w funkcji celu. Kod skalowania funkcji używa [StandardScaler](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.mllib.classification.LogisticRegressionWithLBFGS.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS
) do skalowania funkcji do wariancji jednostek. Jest ona dostarczana przez MLlib do użycia w regresji liniowej z stochastycznego gradientem (SGD), popularnym algorytmem do uczenia szerokiego zakresu innych modeli uczenia maszynowego, takich jak standardowe regresje lub obsługa maszyn wektorowych (SVM).

> [!NOTE]
> Znaleźliśmy algorytm LinearRegressionWithSGD do uwzględnienia w skalowaniu funkcji.

Oto kod służący do skalowania zmiennych, które mają być używane ze standardowym algorytmem SGD liniowym.

```python
# FEATURE SCALING

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from pyspark.mllib.linalg import Vectors
from pyspark.mllib.feature import StandardScaler, StandardScalerModel
from pyspark.mllib.util import MLUtils

# SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
label = oneHotTRAINreg.map(lambda x: x.label)
features = oneHotTRAINreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

label = oneHotTESTreg.map(lambda x: x.label)
features = oneHotTESTreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ROZDZIELCZOŚCI**

Czas wykonania powyżej komórki: 13,17 s

### <a name="cache-objects-in-memory"></a>Obiekty pamięci podręcznej w pamięci

Czas potrzebny na przeszkolenie i przetestowanie algorytmów ML może zostać zmniejszony przez buforowanie obiektów ramki danych wejściowych używanych do klasyfikacji, regresji i skalowania.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.cache()
indexedTESTbinary.cache()
oneHotTRAINbinary.cache()
oneHotTESTbinary.cache()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.cache()
indexedTESTreg.cache()
oneHotTRAINreg.cache()
oneHotTESTreg.cache()

# SCALED FEATURES
oneHotTRAINregScaled.cache()
oneHotTESTregScaled.cache()

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ROZDZIELCZOŚCI** 

Czas wykonania powyżej komórki: 0,15 sekund

## <a name="train-a-binary-classification-model"></a>Uczenie modelu klasyfikacji binarnej

W tej sekcji pokazano, jak używać trzech modeli dla zadania klasyfikacji binarnej w celu przewidywania, czy Porada jest płatna za podróż z taksówką. Przedstawione modele to:

* Regularna regresja logistyczna 
* Losowy model lasów
* Drzewa zwiększające gradienty

Każda sekcja kodu konstrukcyjnego modelu jest podzielona na kroki: 

1. **Modeluj dane szkoleniowe** za pomocą jednego zestawu parametrów
2. **Ocena modelu** dla zestawu danych testowych z metrykami
3. **Zapisywanie modelu** w obiekcie BLOB do użycia w przyszłości

### <a name="classification-using-logistic-regression"></a>Klasyfikacja przy użyciu regresji logistycznej

W kodzie w tej sekcji przedstawiono sposób uczenia, szacowania i zapisywania modelu regresji logistycznej z [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , który przewiduje, czy Porada jest płatna za podróż, w zestawie danych podróży i taryfy NYC.

**Uczenie modelu regresji logistycznej przy użyciu czyszczenia i parametrów**

```python
# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

# GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
from sklearn.metrics import roc_curve,auc
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics


# CREATE MODEL WITH ONE SET OF PARAMETERS
logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                               regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                               tolerance=0.0001, validateData=True, numClasses=2)

# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(logitModel.weights))
print("Intercept: " + str(logitModel.intercept))

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ROZDZIELCZOŚCI** 

Współczynniki: [0.0082065285375,-0.0223675576104,-0.0183812028036,-3.48124578069 e-05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1,00439726852, 3.46567827545,-3.51025855172,-0.0471341112232, 0.043521833294, 0.000243375810385

Przechwycenie:-0.0111216486893

Czas wykonania powyżej komórki: 14,43 s

**Oceń binarny model klasyfikacji ze standardowymi metrykami**

```python
#EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

# RECORD START TIME
timestart = datetime.datetime.now()

# PREDICT ON TEST DATA WITH MODEL
predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))

# INSTANTIATE METRICS OBJECT
metrics = BinaryClassificationMetrics(predictionAndLabels)

# AREA UNDER PRECISION-RECALL CURVE
print("Area under PR = %s" % metrics.areaUnderPR)

# AREA UNDER ROC CURVE
print("Area under ROC = %s" % metrics.areaUnderROC)
metrics = MulticlassMetrics(predictionAndLabels)

# OVERALL STATISTICS
precision = metrics.precision()
recall = metrics.recall()
f1Score = metrics.fMeasure()
print("Summary Stats")
print("Precision = %s" % precision)
print("Recall = %s" % recall)
print("F1 Score = %s" % f1Score)


## SAVE MODEL WITH DATE-STAMP
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
dirfilename = modelDir + logisticregressionfilename;
logitModel.save(sc, dirfilename);

# OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
predictionAndLabelsDF = predictionAndLabels.toDF()
predictionAndLabelsDF.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**ROZDZIELCZOŚCI** 

Obszar w pozycji PR = 0.985297691373

Obszar w obszarze ROC = 0.983714670256

Podsumowanie podsumowania

Precyzja = 0.984304060189

Odwołaj = 0.984304060189

Znak F1 = 0.984304060189

Czas wykonania powyżej komórki: 57,61 s

**Wykreśl krzywą ROC.**

*PredictionAndLabelsDF* jest zarejestrowany w tabeli, *tmp_results* w poprzedniej komórce. *tmp_results* może służyć do wykonywania zapytań i wyników wyjściowych w ramce danych SQLResults do wykreślania. Oto kod.

```python
# QUERY RESULTS                              
%%sql -q -o sqlResults
SELECT * from tmp_results
```

Oto kod umożliwiający prognozowanie i Wykreślanie krzywej ROC.

```python
# MAKE PREDICTIONS AND PLOT ROC-CURVE

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
from sklearn.metrics import roc_curve,auc

# MAKE PREDICTIONS
predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
prob = predictions_pddf["probability"] 
fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT ROC CURVE
plt.figure(figsize=(5,5))
plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
plt.plot([0, 1], [0, 1], 'k--')
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.05])
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('ROC Curve')
plt.legend(loc="lower right")
plt.show()
```

**ROZDZIELCZOŚCI**

![ROC regresji logistycznej — curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Losowa Klasyfikacja lasów

W tym rozdziale przedstawiono sposób uczenia, szacowania i zapisywania losowego modelu lasów, który przewiduje, czy Porada jest płatna za podróż w ramach zestawu danych dotyczących podróży i taryfy NYC.

```python
#PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

# TRAIN RANDOMFOREST MODEL
rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                       categoricalFeaturesInfo=categoricalFeaturesInfo,
                                       numTrees=25, featureSubsetStrategy="auto",
                                       impurity='gini', maxDepth=5, maxBins=32)
## UN-COMMENT IF YOU WANT TO PRINT TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# AREA UNDER ROC CURVE
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfclassificationfilename = "RandomForestClassification_" + datestamp;
dirfilename = modelDir + rfclassificationfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ROZDZIELCZOŚCI**

Obszar w obszarze ROC = 0.985297691373

Czas wykonania powyżej komórki: 31,09 s

### <a name="gradient-boosting-trees-classification"></a>Klasyfikacja drzew z podwyższaniem gradientu

W kodzie w tej sekcji przedstawiono sposób uczenia, szacowania i zapisywania modelu drzewa zwiększania gradientów, który przewiduje, czy Porada jest płatna za podróż w ramach zestawu danych dotyczących podróży i taryfy NYC.

```python
#PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
#print('Learned classification GBT model:')
#print(bgtModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# AREA UNDER ROC CURVE
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN A BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
dirfilename = modelDir + btclassificationfilename;

gbtModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ROZDZIELCZOŚCI**

Obszar w obszarze ROC = 0.985297691373

Czas wykonania powyżej komórki: 19,76 s

## <a name="train-a-regression-model"></a>Trenowanie modelu regresji

W tej sekcji pokazano, jak używać trzech modeli dla zadania regresji do przewidywania ilości Porada płatnej za podróżka w oparciu o inne funkcje Tip. Przedstawione modele to:

* Cykliczna regresja liniowa
* Las losowy
* Drzewa zwiększające gradienty

Te modele zostały opisane we wprowadzeniu. Każda sekcja kodu konstrukcyjnego modelu jest podzielona na kroki: 

1. **Modeluj dane szkoleniowe** za pomocą jednego zestawu parametrów
2. **Ocena modelu** dla zestawu danych testowych z metrykami
3. **Zapisywanie modelu** w obiekcie BLOB do użycia w przyszłości

### <a name="linear-regression-with-sgd"></a>Regresja liniowa z SGD

W kodzie w tej sekcji pokazano, jak używać skalowanych funkcji do uczenia regresji liniowej korzystającej z stochastycznego gradientu (SGD) na potrzeby optymalizacji oraz jak obliczyć, oszacować i zapisać model na platformie Azure Blob Storage (WASB).

> [!TIP]
> W naszym środowisku mogą występować problemy związane z spójnością modeli LinearRegressionWithSGD, a parametry muszą być starannie zmieniane i zoptymalizowane w celu uzyskania prawidłowego modelu. Znacznie ułatwia skalowanie zmiennych.

```python
#PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
from pyspark.mllib.evaluation import RegressionMetrics
from scipy import stats

# USE SCALED FEATURES TO TRAIN MODEL
linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(linearModel.weights))
print("Intercept: " + str(linearModel.intercept))

# SCORE ON SCALED TEST DATA-SET & EVALUATE
predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
testMetrics = RegressionMetrics(predictionAndLabels)

# PRINT TEST METRICS
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
dirfilename = modelDir + linearregressionfilename;

linearModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ROZDZIELCZOŚCI**

Współczynniki: [0.00457675809917,-0.0226314167349,-0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981,-0.000987181489428,-0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995,-0.00990211159703,-0.00637410344522, 0.545083566179,-0.536756072402, 0.0105762393099,-0.0130117577055, 0.0129304737772,-0.00171065945959]

Przechwycenie: 0.853872718283

RMSE = 1.24190115863

R-SQR = 0.608017146081

Czas wykonania powyżej komórki: 58,42 s

### <a name="random-forest-regression"></a>Losowa regresja lasów

Kod w tej sekcji przedstawia sposób uczenia, szacowania i zapisywania losowej regresji dla lasów, która przewiduje ilość pozostałej wartości dla danych podróży NYC.

```python
#PREDICT TIP AMOUNTS USING RANDOM FOREST

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics


## TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=25, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=10, maxBins=32)
## UN-COMMENT IF YOU WANT TO PRING TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

## PREDICT AND EVALUATE ON TEST DATA-SET
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

# TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfregressionfilename = "RandomForestRegression_" + datestamp;
dirfilename = modelDir + rfregressionfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ROZDZIELCZOŚCI**

RMSE = 0.891209218139

R-SQR = 0.759661334921

Czas wykonania powyżej komórki: 49,21 s

### <a name="gradient-boosting-trees-regression"></a>Regresja dla drzew wzrostu gradientu

Kod w tej sekcji przedstawia sposób uczenia, szacowania i zapisywania modelu drzewa zwiększania gradientów, który przewiduje ilość pozostałej wartości dla danych podróży NYC.

**Uczenie i szacowanie**

```python
#PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
from pyspark.mllib.util import MLUtils

## TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

## EVALUATE A TEST DATA-SET
predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

# TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
dirfilename = modelDir + btregressionfilename;
gbtModel.save(sc, dirfilename)

# CONVERT RESULTS TO DF AND REGISTER TEMP TABLE
test_predictions = sqlContext.createDataFrame(predictionAndLabels)
test_predictions.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ROZDZIELCZOŚCI**

RMSE = 0.908473148639

R-SQR = 0.753835096681

Czas wykonania powyżej komórki: 34,52 s

**Wprowadź**

*tmp_results* jest zarejestrowany jako tabela programu Hive w poprzedniej komórce. Wyniki z tabeli są wyprowadzane do ramki danych *SQLResults* do wykreślania. Oto kod

```python
# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

# SELECT RESULTS
%%sql -q -o sqlResults
SELECT * from tmp_results
```

Oto kod, który służy do wykreślania danych przy użyciu serwera Jupyter.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
import numpy as np

# PLOT 
ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
plt.axis([-1, 20, -1, 20])
plt.show(ax)
```

**ROZDZIELCZOŚCI**

![Rzeczywiste — w porównaniu z przewidywaniami — kwoty](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Czyszczenie obiektów z pamięci

Służy `unpersist()` do usuwania obiektów w pamięci podręcznej.

```python
# REMOVE ORIGINAL DFs
taxi_df_train_cleaned.unpersist()
taxi_df_train_with_newFeatures.unpersist()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.unpersist()
indexedTESTbinary.unpersist()
oneHotTRAINbinary.unpersist()
oneHotTESTbinary.unpersist()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.unpersist()
indexedTESTreg.unpersist()
oneHotTRAINreg.unpersist()
oneHotTESTreg.unpersist()

# SCALED FEATURES
oneHotTRAINregScaled.unpersist()
oneHotTESTregScaled.unpersist()
```

## <a name="save-the-models"></a>Zapisz modele

Aby korzystać z i wypróbować niezależny zestaw danych opisany w temacie Ocena [i Ocena modelu uczenia maszynowego opartego na platformie Spark](spark-model-consumption.md) , należy skopiować i wkleić te nazwy plików zawierające zapisane modele utworzone w tym miejscu w notesie Jupyter zużycia. Oto kod umożliwiający wydrukowanie ścieżek do plików modeli, których potrzebujesz.

```python
# MODEL FILE LOCATIONS FOR CONSUMPTION
print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";
```

**ROZDZIELCZOŚCI**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05 -0317 _03_ 23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05 -0317 _05_ 21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05 -0317 _04_ 11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05 -0317 _06_ 08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05 -0317 _04_ 36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05 -0317 _06_ 51.737282"

## <a name="whats-next"></a>Co dalej?

Teraz, po utworzeniu modeli regresji i klasyfikacji przy użyciu MlLib Spark, możesz dowiedzieć się, jak obliczyć i oszacować te modele. Zaawansowana Eksploracja i Modeling notesu do modelowania danych omówieniach się w taki sposób, aby lepiej sprawdzać poprawność, czyszczenie parametrów funkcji Hyper-i obliczanie modelu. 

**Użycie modelu:** Aby dowiedzieć się, jak obliczyć i oszacować modele klasyfikacji i regresji utworzone w tym temacie, zobacz [ocenę i ocenę modeli uczenia maszynowego opartych na platformie Spark](spark-model-consumption.md).

Czyszczenie z zastosowaniem **krzyżowym i** przeszukiwaniem: zobacz [Zaawansowane Eksploracja i modelowanie danych za pomocą platformy Spark](spark-advanced-data-exploration-modeling.md) , w jaki sposób można przeszkoleć modele przy użyciu weryfikacji krzyżowej i funkcji Hyper-Parameter.
