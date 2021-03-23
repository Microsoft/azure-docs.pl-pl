---
title: Zaawansowane eksplorowanie i modelowanie danych za pomocą procesu analizy danych w zespole platformy Spark
description: Korzystaj z usługi HDInsight Spark do eksplorowania danych i uczenia modeli klasyfikacji i regresji binarnej przy użyciu funkcji optymalizacji krzyżowej i przeprowadzenia weryfikacji.
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
ms.openlocfilehash: b84b7387411e7df8e092a2e810591697319f9554
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774681"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Zaawansowane eksplorowanie i modelowanie danych za pomocą platformy Spark

W tym instruktażu Usługa HDInsight Spark używa do eksploracji danych i uczenia modeli klasyfikacji i regresji binarnej przy użyciu funkcji optymalizacji krzyżowej i przeprowadzenia parametrów w przypadku przykładowej NYCi i 2013 taryfy danych. Przeprowadzimy Cię przez kroki [procesu analizy danych](./index.yml), kompleksowego, korzystającego z klastra usługi HDInsight Spark do przetwarzania i przechowywania danych oraz modeli. Proces eksploruje i wizualizowa dane wprowadzane z Azure Storage Blob, a następnie przygotowuje dane do tworzenia modeli predykcyjnych. Język Python został użyty do kodu rozwiązania i przedstawienia odpowiednich wykresów. Te modele są kompilowane przy użyciu zestawu narzędzi Spark MLlib Toolkit do wykonywania zadań modelowania binarnych i regresji. 

* Zadanie **klasyfikacji binarnej** służy do przewidywania, czy dla podróży jest płacona Porada. 
* Zadanie **regresji** służy do przewidywania wielkości końcówki na podstawie innych funkcji etykietki. 

Kroki modelowania zawierają również kod pokazujący, jak wyszkolić, oszacować i zapisać każdy typ modelu. W temacie omówiono niektóre z tych samych podstaw, co w przypadku [eksploracji i modelowania danych za pomocą platformy Spark](spark-data-exploration-modeling.md) . Jest to jednak bardziej "Zaawansowane" w tym, że używa również weryfikacji krzyżowej z przeczyszczeniem parametrów, aby szkolić optymalnie precyzyjne modele klasyfikacji i regresji. 

**Krzyżowe sprawdzanie poprawności (CV)** to technika, która ocenia, jak dobrze jest przeszkolony model na znanym zestawie danych, aby przewidzieć funkcje zestawów DataSet, na których nie zostały przeszkolone.  Wspólna implementacja użyta w tym miejscu polega na podzieleniu zestawu danych na zgięcie w języku K, a następnie przeszkoleniu modelu w sposób okrężny na wszystkich, ale jednym ze zgięciów. Możliwe jest, że model do prognozowania w przypadku przetestowania względem niezależnego zestawu danych w tym złożeniu nie jest używany do uczenia modelu.

**Optymalizacja parametrów** polega na wyborze zestawu parametrów dla algorytmu uczenia, zazwyczaj z celem optymalizacji miary wydajności algorytmu w niezależnym zestawie danych. **Parametry** są wartościami, które muszą być określone poza procedurą szkolenia modelu. Założenia dotyczące tych wartości mogą mieć wpływ na elastyczność i dokładność modeli. Drzewa decyzyjne mają moje parametry, na przykład takie jak żądana głębokość i liczba liści w drzewie. Maszyny wektorowe obsługi (SVMs) wymagają ustawienia okresu karania za nieprawidłową klasyfikację. 

Typowym sposobem wykonania optymalizacji parametrów jest przeszukiwanie siatki lub **wyczyszczenie parametrów**. To wyszukiwanie przechodzi przez podzestaw obszaru hiperprzestrzeni dla algorytmu uczenia. Wzajemne sprawdzanie poprawności może dostarczyć metrykę wydajności w celu sortowania optymalnych wyników wytwarzanych przez algorytm przeszukiwania siatki. Użycie CV z parametrami do odwzorowania pomaga ograniczyć problemy, takie jak niedopasowanie modelu do danych szkoleniowych, dzięki czemu model zachowuje zdolność do zastosowania do ogólnego zestawu danych, z którego zostały wyodrębnione dane szkoleniowe.

Modele, z których korzystamy, obejmują regresję logistyczną i liniową, lasy losowe i podwyższające gradienty drzew:

* [Regresja liniowa z SGD](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.mllib.regression.LinearRegressionWithSGD.html#pyspark.mllib.regression.LinearRegressionWithSGD
) to model regresji liniowej, który używa metody stochastycznego gradientu (SGD) i optymalizacji i skalowania funkcji, aby przewidzieć płatne kwoty. 
* [Regresja logistyczna przy użyciu](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) regresji LBFGS lub "neurologia" to model regresji, który może być używany, gdy zmienna zależna jest kategorii do klasyfikowania danych. LBFGS jest algorytmem optymalizacji quasi-niutonach, który przybliża algorytm Broyden – Fletcher – Goldfarb – Shanno (BFGS) przy użyciu ograniczonej ilości pamięci komputera i jest szeroko używany w uczeniu maszynowym.
* [Losowe lasy](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) są kompletnymi drzewami decyzyjnymi.  Łączą się z wieloma drzewami decyzyjnymi, aby zmniejszyć ryzyko naruszenia. Losowe lasy są używane do regresji i klasyfikacji oraz obsługują funkcje kategorii i można je rozszerzyć do ustawienia klasyfikacji wieloklasowej. Nie wymagają one skalowania funkcji i są w stanie przechwytywać interakcje i funkcje. Losowe lasy to jeden z najbardziej pomyślnych modeli uczenia maszynowego na potrzeby klasyfikacji i regresji.
* [Drzewa podwyższające wartości gradientu](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) są kompletnymi drzewami decyzyjnymi. GBTSe drzewa decyzyjne w sposób iteracyjny, aby zminimalizować funkcję strat. GBTS jest używany do regresji i klasyfikacji i może obsługiwać funkcje kategorii, nie wymaga skalowania funkcji, a także może przechwytywać interakcje i funkcje. Mogą być również używane w ustawieniu klasyfikacji wieloklasowej.

Przykłady modelowania używające funkcji CV i parametru odchylenia są wyświetlane dla problemu klasyfikacji binarnej. Prostsze przykłady (bez odchylenia parametrów) są prezentowane w głównym temacie dla zadań regresji. Jednak w dodatku można sprawdzić poprawność przy użyciu elastycznej sieci do regresji liniowej i CV z użyciem odchylenia parametrów przy użyciu dla losowych regresji lasów. **Elastyczna sieć** to rozstosowana Metoda regresji w celu dopasowania modeli regresji liniowej, które liniowo łączą metryki L1 i L2 jako kary za metody [Lasso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) i [pierścieniowe](https://en.wikipedia.org/wiki/Tikhonov_regularization) .   

<!-- -->

> [!NOTE]
> Mimo że zestaw narzędzi Spark MLlib został zaprojektowany tak, aby działał w dużych zestawach danych, stosunkowo małą próbkę (~ 30 MB przy użyciu wierszy 170K, o 0,1% oryginalnego zestawu danych NYC) jest tutaj używana dla wygody. Ćwiczenie w tym miejscu działają wydajnie (w około 10 minutach) w klastrze usługi HDInsight z 2 węzłami procesu roboczego. Ten sam kod, z drobnymi modyfikacjami, może służyć do przetwarzania większych zestawów danych z odpowiednimi modyfikacjami dotyczącymi buforowania danych w pamięci i zmieniania rozmiaru klastra.

<!-- -->

## <a name="setup-spark-clusters-and-notebooks"></a>Konfiguracja: klastry Spark i notesy
Kroki instalacji i kod zostały przedstawione w tym przewodniku dotyczącym korzystania z usługi HDInsight Spark 1,6. Jednak dla klastrów usługi HDInsight Spark 1,6 i Spark 2,0 są udostępniane notesy Jupyter. Opis notesów i linków do nich znajduje się w [README.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) dla repozytorium GitHub zawierającego je. Ponadto kod i połączone notesy są ogólne i powinny być wykonywane w dowolnym klastrze Spark. Jeśli nie korzystasz z usługi HDInsight Spark, kroki instalacji i zarządzania klastrami mogą się nieco różnić od tego, co jest widoczne w tym miejscu. Aby uzyskać wygodę, poniżej przedstawiono linki do notesów Jupyter dla platformy Spark 1,6 i 2,0 do uruchomienia w jądrze pyspark serwera Jupyter Notebook:

### <a name="spark-16-notebooks"></a>Notesy platformy Spark 1,6

[pySpark-Machine-Learning-Data-nauka-Spark-Advanced-Data-Eksploracja-Modeling. ipynb](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): zawiera tematy w notesie #1 i projektowanie modeli przy użyciu strojenia parametrów i krzyżowego sprawdzania poprawności.

### <a name="spark-20-notebooks"></a>Notesy platformy Spark 2,0

[Spark 2.0-pySpark3-Machine-Learning-Data-nauka-Spark-Advanced-Data-Eksplorowanie — Modeling. ipynb](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): ten plik zawiera informacje na temat sposobu przeprowadzania eksploracji danych, modelowania i oceniania w klastrach platformy Spark 2,0.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Konfiguracja: lokalizacje magazynu, biblioteki i wstępnie ustawiony kontekst platformy Spark
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
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

# PRINT START TIME
import datetime
datetime.datetime.now()
```

**ROZDZIELCZOŚCI**

DateTime. DateTime (2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>Importowanie bibliotek
Zaimportuj niezbędne biblioteki przy użyciu następującego kodu:

```python
# LOAD PYSPARK LIBRARIES
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

Aby uzyskać więcej informacji na temat jądra dla notesów Jupyter oraz wstępnie zdefiniowanych "magicznych", zobacz [jądra dostępne dla notesów Jupyter z klastrami usługi HDInsight Spark Linux w usłudze HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Pozyskiwanie danych z publicznego obiektu BLOB:
Pierwszym krokiem w procesie nauki danych jest pozyskiwanie danych do analizy ze źródeł, w których znajdują się one w środowisku eksploracji i modelowania danych. To środowisko jest platforma Spark w tym instruktażu. Ta sekcja zawiera kod służący do ukończenia serii zadań:

* pozyskiwanie przykładu danych do modelowania
* odczyt w wejściowym zestawie danych (zapisany jako plik. tsv)
* formatowanie i czyszczenie danych
* Tworzenie i buforowanie obiektów (odporne lub ramek danych) w pamięci
* Zarejestruj ją jako tabelę tymczasową w kontekście SQL.

Oto kod dotyczący pozyskiwania danych.

```python
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

# CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
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

Czas wykonania powyżej komórki: 276,62 s

## <a name="data-exploration--visualization"></a>Wizualizacja & eksploracji danych
Po przeprowadzeniu danych do platformy Spark następnym krokiem w procesie analizy danych jest dokładniejsze zrozumienie danych za pomocą eksploracji i wizualizacji. W tej sekcji analizujemy dane dotyczące taksówki przy użyciu zapytań SQL i kreślą zmienne docelowe oraz funkcje potencjalnej kontroli wzrokowej. W odróżnieniu od liczby osób korzystających z przedziałów w podróży, częstotliwości postanowień oraz sposobu, w jaki porady są różne według kwoty płatności i typu.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Wykreśl histogram częstotliwości liczby pasażerów w próbce podróży z taksówką
Ten kod i kolejne fragmenty kodu używają programu SQL Magic do wykonywania zapytań dotyczących przykładu i lokalnego Magic w celu wykreślenia danych.

* **SQL Magic ( `%%sql` )** jądro usługi HDInsight PySpark obsługuje łatwe wbudowane zapytania HiveQL względem elementu SqlContext. Argument (-o VARIABLE_NAME) utrwala dane wyjściowe zapytania SQL jako element Pandas Dataframe na serwerze Jupyter. Oznacza to, że jest dostępny w trybie lokalnym.
* **`%%local` Magic** służy do uruchamiania kodu lokalnie na serwerze Jupyter, który jest węzła głównego klastra usługi HDInsight. Zwykle używasz `%%local` Magic po użyciu `%%sql -o` Magic do uruchomienia zapytania. Parametr-o będzie utrwalał dane wyjściowe zapytania SQL lokalnie. Następnie `%%local` Magic wyzwala następny zestaw fragmentów kodu do uruchomienia lokalnego na danych wyjściowych zapytań SQL, które zostały utrwalone lokalnie. Dane wyjściowe są automatycznie wizualizowane po uruchomieniu kodu.

To zapytanie pobiera liczbę podróży według liczby pasażerów. 

```sql
# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

# SQL QUERY
%%sql -q -o sqlResults
SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count
```

Ten kod tworzy lokalną ramkę danych z wyników zapytania i wykreśla dane. `%%local`Magic tworzy lokalną ramkę danych, `sqlResults` która może być używana do wykreślania przy użyciu matplotlib. 

<!-- -->

> [!NOTE]
> Ten PySpark Magic jest używany wiele razy w tym instruktażu. Jeśli ilość danych jest duża, należy przykładowo utworzyć ramkę danych, która może pasować do pamięci lokalnej.

<!-- -->

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
sqlResults
```

Oto kod do wykreślania podróży według liczby osób

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
import matplotlib.pyplot as plt
%matplotlib inline

# PLOT PASSENGER NUMBER VS TRIP COUNTS
x_labels = sqlResults['passenger_count'].values
fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
fig.set_xticklabels(x_labels)
fig.set_title('Counts of trips by passenger count')
fig.set_xlabel('Passenger count in trips')
fig.set_ylabel('Trip counts')
plt.show()
```

**ROZDZIELCZOŚCI**

![Częstotliwość podróży według liczby pasażerów](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Można wybrać różne typy wizualizacji (tabela, wykres kołowy, linia, obszar lub Słupek) za pomocą przycisków menu **Typ** w notesie. Wykres słupkowy jest pokazywany w tym miejscu.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Wykreśl histogram z kwot postanowień i informacje o tym, jak kwota pozostała w zależności od liczby pasażerów i ilości opłat.
Użyj zapytania SQL, aby uzyskać przykładowe dane..

```sql
# SQL SQUERY
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
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline

# TIP BY PAYMENT TYPE AND PASSENGER COUNT
ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# TIP BY PASSENGER COUNT
ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount ($) by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
ax.set_title('Tip amount by Fare amount ($)')
ax.set_xlabel('Fare Amount')
ax.set_ylabel('Tip Amount')
plt.axis([-2, 120, -2, 30])
plt.show()
```

**ROZDZIELCZOŚCI** 

![Dystrybucja kwoty TIP](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Kwota Porada wg liczby osób](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Kwota Porada według kwoty opłaty](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Inżynieria funkcji, transformacja i przygotowanie danych do modelowania
W tej sekcji opisano i przedstawiono kod procedur służących do przygotowywania danych do użycia w modelu ML. Przedstawiono w nim sposób wykonywania następujących zadań:

* Utwórz nową funkcję przez partycjonowanie godzin w postaci przedziałów czasowych
* Indeksuj i kategorii funkcje kodowania na gorąco
* Utwórz obiekty z etykietami punktów dla danych wejściowych w funkcjach ML
* Utwórz losowe Podpróbkowanie danych i podziel je na zestawy szkoleniowe i testowe
* Skalowanie cech
* Obiekty pamięci podręcznej w pamięci

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Tworzenie nowej funkcji przez partycjonowanie czasów ruchu w pojemnikach
Ten kod pokazuje, jak utworzyć nową funkcję przez partycjonowanie czasów ruchu sieciowego do zasobników, a następnie jak buforuje powstającą ramkę danych w pamięci. Buforowanie prowadzi do ulepszonego czasu wykonywania, w którym są używane wielokrotnie rozproszone zestawy danych (odporne) i ramki. W tym instruktażu odporne i klatki danych są buforowane w kilku etapach.

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
```

```python
# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
# THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
# MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

**ROZDZIELCZOŚCI**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Funkcje indeksu i kodowania z jednym gorącą funkcją kategorii
W tej sekcji pokazano, jak indeksować lub kodować funkcje kategorii dla danych wejściowych w funkcjach modelowania. Funkcja modelowania i przewidywania MLlib wymaga, aby funkcje z danymi wejściowymi kategorii były indeksowane lub kodowane przed użyciem. 

W zależności od modelu należy zaindeksować lub zakodować je na różne sposoby. Na przykład modele regresji logistycznej i liniowej wymagają kodowania jednostronicowego, gdzie na przykład funkcja z trzema kategoriami może być rozwinięta do trzech kolumn funkcji, z których każda zawiera 0 lub 1, w zależności od kategorii obserwacji. MLlib udostępnia funkcję [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) , która umożliwia jednostronicowe kodowanie. Ten koder mapuje kolumnę indeksów etykiet do kolumny wektorów binarnych z co najwyżej jedną wartością. To kodowanie umożliwia stosowanie algorytmów, które oczekują wartości numerycznych, takich jak regresja logistyczna, do zastosowania do funkcji kategorii.

Oto kod do indeksowania i kodowania funkcji kategorii:

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer

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

Czas wykonania powyżej komórki: 3,14 s

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Utwórz obiekty z etykietami punktów dla danych wejściowych w funkcjach ML
Ta sekcja zawiera kod, który pokazuje, jak indeksować dane tekstowe kategorii jako typ danych z etykietami oraz jak kodować. Ta transformacja przygotowuje dane tekstowe do użycia w celu uczenia i przetestowania regresji logistycznej MLlib i innych modeli klasyfikacji. Obiekty z etykietami są odporne na rozproszone zestawy danych (RDD) sformatowane w sposób, który jest wymagany jako dane wejściowe za pomocą większości algorytmów ML w MLlib. [Punkt oznaczony etykietą](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) jest wektorem lokalnym, czyli gęstym lub rozrzedzonym, skojarzonym z etykietą/odpowiedzią.

Oto kod służący do indeksowania i kodowania funkcji tekstowych dla klasyfikacji binarnej.

```python
# FUNCTIONS FOR BINARY CLASSIFICATION

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                         line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
    labPt = LabeledPoint(line.tipped, features)
    return  labPt

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                               line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
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
Ten kod tworzy losowe próbkowanie danych (w tym miejscu użyto 25%). Chociaż nie jest to wymagane dla tego przykładu ze względu na rozmiar zestawu danych, pokazujemy, jak można próbkować dane w tym miejscu. Następnie wiesz, jak z niego korzystać w razie potrzeby. Gdy próbki są duże, próbkowanie może znacznie zaoszczędzić czas podczas modeli szkoleniowych. Następnie wyodrębnimy przykład do części szkoleniowej (75% tutaj) i części testowej (25% tutaj) do użycia w ramach modelowania klasyfikacji i regresji.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
from pyspark.sql.functions import rand

samplingFraction = 0.25;
trainingFraction = 0.75; testingFraction = (1-trainingFraction);
seed = 1234;
encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

# CACHE TRAIN AND TEST DATA
trainData.cache()
testData.cache()

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

Czas wykonania powyżej komórki: 0,31 sekund

### <a name="feature-scaling"></a>Skalowanie cech
Skalowanie funkcji, znane także jako Normalizacja danych, pozwala upewnić się, że funkcje o ogólnie wykorzystanych wartościach nie mają nadmiernej wagi w funkcji celu. Kod skalowania funkcji używa [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) do skalowania funkcji do wariancji jednostek. Jest on dostarczany przez MLlib do użycia w regresji liniowej ze gradientem stochastycznego (SGD). SGD to popularny algorytm szkoleniowy dotyczący szerokiego zakresu innych modeli uczenia maszynowego, takich jak standardowe regresje lub obsługa maszyn wektorowych (SVM).   

> [!TIP]
> Znaleźliśmy algorytm LinearRegressionWithSGD do uwzględnienia w skalowaniu funkcji.   
> 
> 

Oto kod służący do skalowania zmiennych, które mają być używane ze standardowym algorytmem SGD liniowym.

```python
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

Czas wykonania powyżej komórki: 11,67 s

### <a name="cache-objects-in-memory"></a>Obiekty pamięci podręcznej w pamięci
Czas potrzebny na przeszkolenie i przetestowanie algorytmów ML może zostać zmniejszony przez buforowanie obiektów ramki danych wejściowych używanych do klasyfikacji, regresji i skalowania funkcji.

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

Czas wykonania powyżej komórki: 0,13 sekund

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Przewidywanie, czy Porada jest płatna za pomocą binarnych modeli klasyfikacji
W tej sekcji pokazano, jak używać trzech modeli dla zadania klasyfikacji binarnej w celu przewidywania, czy Porada jest płatna za podróż z taksówką. Przedstawione modele to:

* Regresja logistyczna 
* Las losowy
* Drzewa zwiększające gradienty

Każda sekcja kodu konstrukcyjnego modelu jest podzielona na kroki: 

1. **Modeluj dane szkoleniowe** za pomocą jednego zestawu parametrów
2. **Ocena modelu** dla zestawu danych testowych z metrykami
3. **Zapisywanie modelu** w obiekcie BLOB do użycia w przyszłości

Pokazujemy, jak przeprowadzić krzyżowe sprawdzanie poprawności (CV) z czyszczeniem parametrów na dwa sposoby:

1. Użycie **ogólnego** kodu niestandardowego, który można zastosować do dowolnego algorytmu w MLlib i do dowolnego zestawu parametrów w algorytmie. 
2. Korzystanie z **funkcji potoku PySpark CrossValidator**. CrossValidator ma kilka ograniczeń dla platformy Spark 1.5.0: 
   
   * Nie można zapisać lub utrwalić modeli potoku do użycia w przyszłości.
   * Nie można używać dla każdego parametru w modelu.
   * Nie można użyć dla każdego algorytmu MLlib.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Ogólne, krzyżowe sprawdzanie poprawności i przeczyszczenie parametrów używane z algorytmem regresji logistycznej dla klasyfikacji binarnej
W kodzie w tej sekcji przedstawiono sposób uczenia, szacowania i zapisywania modelu regresji logistycznej z [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , który przewiduje, czy Porada jest płatna za podróż, w zestawie danych podróży i taryfy NYC. Model jest przeszkolony przy użyciu operacji krzyżowego sprawdzania poprawności (CV) i czyszczenia parametrów wdrożonych przy użyciu niestandardowego kodu, który można zastosować do dowolnego z algorytmów uczenia w MLlib.   

<!-- -->

> [!NOTE]
> Wykonanie tego niestandardowego kodu CV może potrwać kilka minut.

<!-- -->

**Uczenie modelu regresji logistycznej przy użyciu czyszczenia i parametrów**

```python
# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

# GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
from pyspark.mllib.evaluation import BinaryClassificationMetrics

# CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
from sklearn.grid_search import ParameterGrid
grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
paramGrid = list(ParameterGrid(grid))
numModels = len(paramGrid)

# SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
nFolds = 3;
h = 1.0 / nFolds;
metricSum = np.zeros(numModels);

# BEGIN CV WITH PARAMETER SWEEP
for i in range(nFolds):
    # Create training and x-validation sets
    validateLB = i * h
    validateUB = (i + 1) * h
    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
    validation = trainData.filter(condition)
    # Create LabeledPoints from data-frames
    if i > 0:
        trainCVLabPt.unpersist()
        validationLabPt.unpersist()
    trainCV = trainData.filter(~condition)
    trainCVLabPt = trainCV.map(parseRowOneHotBinary)
    trainCVLabPt.cache()
    validationLabPt = validation.map(parseRowOneHotBinary)
    validationLabPt.cache()
    # For parameter sets compute metrics from x-validation
    for j in range(numModels):
        regt = paramGrid[j]['regType']
        regp = paramGrid[j]['regParam']
        iters = paramGrid[j]['iterations']
        tol = paramGrid[j]['tolerance']
        # Train logistic regression model with hypermarameter set
        model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                  regParam=regp, tolerance = tol, intercept=True)
        predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
        # Use ROC-AUC as accuracy metrics
        validMetrics = BinaryClassificationMetrics(predictionAndLabels)
        metric = validMetrics.areaUnderROC
        metricSum[j] += metric

avgAcc = metricSum / nFolds;
bestParam = paramGrid[np.argmax(avgAcc)];

# UNPERSIST OBJECTS
trainCVLabPt.unpersist()
validationLabPt.unpersist()

# TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                              iterations=bestParam['iterations'], 
                                              regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                              intercept=True)


# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(logitBest.weights))
print("Intercept: " + str(logitBest.intercept))

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

W kodzie w tej sekcji pokazano, jak oszacować model regresji logistycznej względem zestawu danych testowych, łącznie z wykresem krzywej ROC.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT LIBRARIES
from sklearn.metrics import roc_curve,auc
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics

# PREDICT ON TEST DATA WITH BEST/FINAL MODEL
predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))

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

# OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
predictionAndLabelsDF = predictionAndLabels.toDF()
predictionAndLabelsDF.registerTempTable("tmp_results");

# PRINT ELAPSED TIME    
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ROZDZIELCZOŚCI**

Obszar w pozycji PR = 0.985336538462

Obszar w obszarze ROC = 0.983383274312

Podsumowanie podsumowania

Precyzja = 0.984174341679

Odwołaj = 0.984174341679

Znak F1 = 0.984174341679

Czas wykonania powyżej komórki: 2,67 s

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

#PREDICTIONS
predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
prob = predictions_pddf["probability"] 
fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT ROC CURVES
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

![Krzywa ROC regresji logistycznej dla ogólnego podejścia](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Utrwalanie modelu w obiekcie BLOB w celu użycia w przyszłości**

Kod w tej sekcji pokazuje, jak zapisać model regresji logistycznej na potrzeby zużycia.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.classification import LogisticRegressionModel

# PERSIST MODEL
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
dirfilename = modelDir + logisticregressionfilename;

logitBest.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**ROZDZIELCZOŚCI**

Czas wykonania powyżej komórki: 34,57 s

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Użycie funkcji potoku MLlib CrossValidator z modelem regresji logistycznej (regresja elastyczna)
W kodzie w tej sekcji przedstawiono sposób uczenia, szacowania i zapisywania modelu regresji logistycznej z [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , który przewiduje, czy Porada jest płatna za podróż, w zestawie danych podróży i taryfy NYC. Model jest szkolony przy użyciu operacji krzyżowego sprawdzania poprawności (CV) i czyszczenia parametrów wdrożonych przy użyciu funkcji potoku MLlib CrossValidator dla CV z odchyleniami parametrów.   

<!-- -->

> [!NOTE]
> Wykonanie tego kodu CV MLlib może potrwać kilka minut.

<!-- -->

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.classification import LogisticRegression
from pyspark.ml import Pipeline
from pyspark.ml.evaluation import BinaryClassificationEvaluator
from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
from sklearn.metrics import roc_curve,auc

# DEFINE ALGORITHM / MODEL
lr = LogisticRegression()

# DEFINE GRID PARAMETERS
paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                              .addGrid(lr.maxIter, (5, 10))\
                              .addGrid(lr.tol, (1e-4, 1e-5))\
                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
                              .build()

# DEFINE CV WITH PARAMETER SWEEP
cv = CrossValidator(estimator= lr,
                    estimatorParamMaps=paramGrid,
                    evaluator=BinaryClassificationEvaluator(),
                    numFolds=3)

# CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])

# TRAIN WITH CROSS-VALIDATION
cv_model = cv.fit(trainDataFrame)


## PREDICT AND EVALUATE ON TEST DATA-SET

# USE TEST DATASET FOR PREDICTION
testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
test_predictions = cv_model.transform(testDataFrame)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
``` 

**ROZDZIELCZOŚCI**

Czas wykonania powyżej komórki: 107,98 s

**Wykreśl krzywą ROC.**

*PredictionAndLabelsDF* jest zarejestrowany w tabeli, *tmp_results* w poprzedniej komórce. *tmp_results* może służyć do wykonywania zapytań i wyników wyjściowych w ramce danych SQLResults do wykreślania. Oto kod.

```python
# QUERY RESULTS
%%sql -q -o sqlResults
SELECT label, prediction, probability from tmp_results
```

Oto kod do wykreślenia krzywej ROC.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
%%local
from sklearn.metrics import roc_curve,auc

# ROC CURVE
prob = [x["values"][1] for x in sqlResults["probability"]]
fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

#PLOT
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

![Krzywa ROC regresji logistycznej z użyciem CrossValidator MLlib](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Losowa Klasyfikacja lasów
W kodzie w tej sekcji przedstawiono sposób uczenia, obliczania i zapisywania losowej regresji dla lasów, która przewiduje, czy Porada jest płatna za podróż w ramach zestawu danych dotyczących podróży i taryfy NYC.

```python
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
## UN-COMMENT IF YOU WANT TO PRING TREES
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

Obszar w obszarze ROC = 0.985336538462

Czas wykonania powyżej komórki: 26,72 s

### <a name="gradient-boosting-trees-classification"></a>Klasyfikacja drzew z podwyższaniem gradientu
W kodzie w tej sekcji przedstawiono sposób uczenia, szacowania i zapisywania modelu drzewa zwiększania gradientów, który przewiduje, czy Porada jest płatna za podróż w ramach zestawu danych dotyczących podróży i taryfy NYC.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                numIterations=10)
## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
#print('Learned classification GBT model:')
#print(bgtModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# Area under ROC curve
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

Obszar w obszarze ROC = 0.985336538462

Czas wykonania powyżej komórki: 28,13 s

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Prognozowana kwota Porada z modelami regresji (bez użycia CV)
W tej sekcji pokazano, jak używać trzech modeli dla zadania regresji: przewidywanie kwoty TIP płatnej za podróżka w oparciu o inne funkcje Tip. Przedstawione modele to:

* Cykliczna regresja liniowa
* Las losowy
* Drzewa zwiększające gradienty

Te modele zostały opisane we wprowadzeniu. Każda sekcja kodu konstrukcyjnego modelu jest podzielona na kroki: 

1. **Modeluj dane szkoleniowe** za pomocą jednego zestawu parametrów
2. **Ocena modelu** dla zestawu danych testowych z metrykami
3. **Zapisywanie modelu** w obiekcie BLOB do użycia w przyszłości   

<!-- -->

> [!NOTE] 
> Wzajemne sprawdzanie poprawności nie jest używane z trzema modelami regresji w tej sekcji, ponieważ przedstawiono je szczegółowo dla modeli regresji logistycznej. Przykład pokazujący, jak używać CV z elastyczną usługą net for regresji liniowej, w dodatku tego tematu.

<!-- -->

<!-- -->

> [!NOTE] 
> W naszym środowisku mogą występować problemy związane z zbieżnością modeli LinearRegressionWithSGD i należy uważnie zmienić lub zoptymalizować parametry w celu uzyskania prawidłowego modelu. Znacznie ułatwia skalowanie zmiennych. Elastyczna regresja netto, pokazana w dodatku do tego tematu, może być również używana zamiast LinearRegressionWithSGD.

<!-- -->

### <a name="linear-regression-with-sgd"></a>Regresja liniowa z SGD
W kodzie w tej sekcji pokazano, jak używać skalowanych funkcji do uczenia regresji liniowej korzystającej z stochastycznego gradientu (SGD) na potrzeby optymalizacji oraz jak obliczyć, oszacować i zapisać model na platformie Azure Blob Storage (WASB).

> [!TIP]
> W naszym środowisku mogą występować problemy związane z spójnością modeli LinearRegressionWithSGD, a parametry muszą być starannie zmieniane i zoptymalizowane w celu uzyskania prawidłowego modelu. Znacznie ułatwia skalowanie zmiennych.
> 
> 

```python
# LINEAR REGRESSION WITH SGD 

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

print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
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

Współczynniki: [0.0141707753435,-0.0252930927087,-0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092,-0.00456498588241,-0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632,-0.00289545676449,-0.00791124681938, 0.54396316518,-0.536293513569, 0.0119076553369,-0.0173039244582, 0.0119632796147, 0.00146764882502]

Przechwycenie: 0.854507624459

RMSE = 1.23485131376

R-SQR = 0.597963951127

Czas wykonania powyżej komórki: 38,62 s

### <a name="random-forest-regression"></a>Losowa regresja lasów
Kod w tej sekcji przedstawia sposób uczenia, szacowania i zapisywania losowego modelu lasów, który przewiduje ilość pozostałej części danych podróży NYC.   

<!-- -->

> [!NOTE]
> Poprawność przez odczyszczenie parametrów przy użyciu kodu niestandardowego znajduje się w dodatku.

<!-- -->

```python
#PREDICT TIP AMOUNTS USING RANDOM FOREST

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics


# TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=25, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=10, maxBins=32)
# UN-COMMENT IF YOU WANT TO PRING TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

# PREDICT AND EVALUATE ON TEST DATA-SET
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

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

RMSE = 0.931981967875

R-SQR = 0.733445485802

Czas wykonania powyżej komórki: 25,98 s

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

# TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

# EVALUATE A TEST DATA-SET
predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
test_predictions= sqlContext.createDataFrame(predictionAndLabels)
test_predictions_pddf = test_predictions.toPandas()

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
dirfilename = modelDir + btregressionfilename;
gbtModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ROZDZIELCZOŚCI**

RMSE = 0.928172197114

R-SQR = 0.732680354389

Czas wykonania powyżej komórki: 20,9 s

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
import numpy as np

# PLOT
ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
plt.axis([-1, 15, -1, 15])
plt.show(ax)
```

![Rzeczywiste — w porównaniu z przewidywaniami — kwoty](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Dodatek: dodatkowe zadania regresji używające krzyżowego sprawdzania poprawności z wycieraniami parametrów
Ten dodatek zawiera kod pokazujący, jak wykonywać metodę OKS przy użyciu elastycznej sieci na potrzeby regresji liniowej oraz jak wykonywać metodę OKS z wycieraniam parametrów przy użyciu niestandardowego kodu do losowej regresji lasów.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Krzyżowe sprawdzanie poprawności przy użyciu elastycznej sieci na potrzeby regresji liniowej
W kodzie w tej sekcji pokazano, jak wykonać wzajemne sprawdzanie poprawności przy użyciu elastycznej sieci do regresji liniowej oraz jak oszacować model na podstawie danych testowych.

```python
###  CV USING ELASTIC NET FOR LINEAR REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.regression import LinearRegression
from pyspark.ml import Pipeline
from pyspark.ml.evaluation import RegressionEvaluator
from pyspark.ml.tuning import CrossValidator, ParamGridBuilder

# DEFINE ALGORITHM/MODEL
lr = LinearRegression()

# DEFINE GRID PARAMETERS
paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                              .addGrid(lr.maxIter, (5, 10))\
                              .addGrid(lr.tol, (1e-4, 1e-5))\
                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
                              .build() 

# DEFINE PIPELINE 
# SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
pipeline = Pipeline(stages=[lr])

# DEFINE CV WITH PARAMETER SWEEP
cv = CrossValidator(estimator= lr,
                    estimatorParamMaps=paramGrid,
                    evaluator=RegressionEvaluator(),
                    numFolds=3)

# CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])

# TRAIN WITH CROSS-VALIDATION
cv_model = cv.fit(trainDataFrame)


# EVALUATE MODEL ON TEST SET
testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])

# MAKE PREDICTIONS ON TEST DOCUMENTS
# cvModel uses the best model found (lrModel).
predictionAndLabels = cv_model.transform(testDataFrame)

# CONVERT TO DF AND SAVE REGISTER DF AS TABLE
predictionAndLabels.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ROZDZIELCZOŚCI**

Czas wykonania powyżej komórki: 161,21 s

**Oceń przy użyciu metryki R-SQR**

*tmp_results* jest zarejestrowany jako tabela programu Hive w poprzedniej komórce. Wyniki z tabeli są wyprowadzane do ramki danych *SQLResults* do wykreślania. Oto kod

```python
# SELECT RESULTS
%%sql -q -o sqlResults
SELECT label,prediction from tmp_results
```

Oto kod, aby obliczyć R-SQR.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
from scipy import stats

#R-SQR TEST METRIC
corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
r2 = (corstats[2]*corstats[2])
print("R-sqr = %s" % r2)
```

**ROZDZIELCZOŚCI**

R-SQR = 0.619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Krzyżowe sprawdzanie poprawności z odchyleniami parametrów przy użyciu niestandardowego kodu do losowej regresji lasów
W kodzie w tej sekcji pokazano, jak przeprowadzić weryfikację krzyżową z odchyleniami parametrów przy użyciu niestandardowego kodu do losowej regresji lasów i jak oszacować model na podstawie danych testowych.

```python
# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
# GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics
from sklearn.grid_search import ParameterGrid

## CREATE PARAMETER GRID
grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
paramGrid = list(ParameterGrid(grid))

## SPECIFY LEVELS OF CATEGORICAL VARIBLES
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

# SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
nFolds = 3;
numModels = len(paramGrid)
h = 1.0 / nFolds;
metricSum = np.zeros(numModels);

for i in range(nFolds):
    # Create training and x-validation sets
    validateLB = i * h
    validateUB = (i + 1) * h
    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
    validation = trainData.filter(condition)
    # Create labeled points from data-frames
    if i > 0:
        trainCVLabPt.unpersist()
        validationLabPt.unpersist()
    trainCV = trainData.filter(~condition)
    trainCVLabPt = trainCV.map(parseRowIndexingRegression)
    trainCVLabPt.cache()
    validationLabPt = validation.map(parseRowIndexingRegression)
    validationLabPt.cache()
    # For parameter sets compute metrics from x-validation
    for j in range(numModels):
        maxD = paramGrid[j]['maxDepth']
        numT = paramGrid[j]['numTrees']
        # Train logistic regression model with hypermarameter set
        rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=numT, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=maxD, maxBins=32)
        predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
        predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
        # Use ROC-AUC as accuracy metrics
        validMetrics = RegressionMetrics(predictionAndLabels)
        metric = validMetrics.rootMeanSquaredError
        metricSum[j] += metric

avgAcc = metricSum/nFolds;
bestParam = paramGrid[np.argmin(avgAcc)];

# UNPERSIST OBJECTS
trainCVLabPt.unpersist()
validationLabPt.unpersist()

## TRAIN FINAL MODL WIHT BEST PARAMETERS
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

# EVALUATE MODEL ON TEST DATA
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

#PRINT TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**ROZDZIELCZOŚCI**

RMSE = 0.906972198262

R-SQR = 0.740751197012

Czas wykonania powyżej komórki: 69,17 s

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Czyszczenie obiektów z pamięci i drukowanie lokalizacji modeli
Służy `unpersist()` do usuwania obiektów w pamięci podręcznej.

```python
# UNPERSIST OBJECTS CACHED IN MEMORY

# REMOVE ORIGINAL DFs
taxi_df_train_cleaned.unpersist()
taxi_df_train_with_newFeatures.unpersist()
trainData.unpersist()
trainData.unpersist()

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

**ROZDZIELCZOŚCI**

PythonRDD [122] o RDD w PythonRDD. scala: 43

* * Ścieżka wyjściowa do plików modelu do użycia w notesie użycia. * * Aby użyć i wypróbować niezależny zestaw danych, należy skopiować i wkleić te nazwy plików w "notesie zużycia".

```python
# PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";
```

**ROZDZIELCZOŚCI**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05 -0316 _47_ 30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05 -0316 _51_ 28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05 -0316 _50_ 17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05 -0316 _51_ 57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05 -0316 _50_ 40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05 -0316 _52_ 18.827237"

## <a name="whats-next"></a>Co dalej?
Teraz, po utworzeniu modeli regresji i klasyfikacji przy użyciu MlLib Spark, możesz dowiedzieć się, jak obliczyć i oszacować te modele.

**Użycie modelu:** Aby dowiedzieć się, jak obliczyć i oszacować modele klasyfikacji i regresji utworzone w tym temacie, zobacz [ocenę i ocenę modeli uczenia maszynowego opartych na platformie Spark](spark-model-consumption.md).
