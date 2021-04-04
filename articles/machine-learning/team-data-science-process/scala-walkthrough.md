---
title: Analiza danych przy użyciu Scala i platformy Spark na platformie Azure — proces nauki o danych zespołowych
description: Jak używać Scala dla nadzorowanych zadań uczenia maszynowego z skalowalnymi pakietami MLlib i Spark ML platformy Spark w klastrze Azure HDInsight Spark.
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
ms.openlocfilehash: 9ae4549fe343422bbf60275a97768ca407f2dc7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321367"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Analiza danych przy użyciu języka Scala i platformy Spark na platformie Azure
W tym artykule pokazano, jak używać Scala do nadzorowanych zadań uczenia maszynowego z skalowalnymi pakietami MLlib i Spark ML platformy Spark w klastrze Azure HDInsight Spark. Przeprowadzi Cię przez zadania, które stanowią [proces analizy danych](./index.yml): pozyskiwanie i Eksploracja danych, Wizualizacja, Inżynieria funkcji, modelowanie i użycie modelu. Modele w artykule obejmują regresję logistyczną i liniową, lasy losowe i drzewa z podwyższeniem poziomu gradientu (GBTs), a także dwa popularne, nadzorowane zadania uczenia maszynowego:

* Problem z regresją: przewidywanie kwoty pozostałej ($) dla podróży z taksówką
* Klasyfikacja binarna: przewidywanie porady lub brak porady (1/0) na potrzeby podróży z taksówką

Proces modelowania wymaga szkoleń i oceny dla zestawu danych testowych i odpowiednich metryk dokładności. W tym artykule można dowiedzieć się, jak przechowywać te modele w usłudze Azure Blob Storage oraz jak oceny i oceny ich wydajności predykcyjnej. W tym artykule opisano również bardziej zaawansowane tematy dotyczące optymalizowania modeli przy użyciu operacji czyszczenia krzyżowego i funkcji Hyper-Parameter. Używane dane to przykład zestawu danych 2013 o wykorzystaniu z NYCów i opłat za przejazd w serwisie GitHub.

[Scala](https://www.scala-lang.org/), język oparty na maszynie wirtualnej Java, integruje koncepcje programowania zorientowane obiektowo i funkcjonalne. Jest to skalowalny język, który jest dobrze dostosowany do rozproszonego przetwarzania w chmurze i działa w klastrach platformy Azure Spark.

[Platforma Spark](https://spark.apache.org/) to platforma przetwarzania równoległego typu open source, która obsługuje przetwarzanie w pamięci w celu zwiększenia wydajności aplikacji do analizy danych Big Data. Aparat przetwarzania Spark jest zbudowany z myślą o szybkości, łatwości użycia i zaawansowanej analizie. Możliwości obliczeniowe rozproszone w pamięci platformy Spark sprawiają, że jest dobrym rozwiązaniem dla iteracyjnych algorytmów w obliczeniach uczenia maszynowego i grafu. Pakiet [Spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) zapewnia jednolity zestaw interfejsów API wysokiego poziomu opartych na ramkach danych, które ułatwiają tworzenie i dostrajanie praktycznych potoków uczenia maszynowego. [MLlib](https://spark.apache.org/mllib/) to skalowalna biblioteka uczenia maszynowego platformy Spark, która oferuje możliwości modelowania w tym środowisku rozproszonym.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) to oparta na platformie Azure oferta platformy Spark typu open source. Obejmuje ona również obsługę notesów Jupyter Scala w klastrze Spark i może uruchamiać zapytania programu Spark SQL Interactive w celu przekształcania, filtrowania i wizualizacji danych przechowywanych w usłudze Azure Blob Storage. Fragmenty kodu Scala w tym artykule, które dostarczają rozwiązań i pokazują odpowiednie wykresy umożliwiające wizualizację danych w notesach Jupyter zainstalowanych w klastrach Spark. Kroki modelowania w tych tematach zawierają kod, który pokazuje, jak uczenie, oszacować, zapisać i korzystać z poszczególnych typów modeli.

Kroki instalacji i kod w tym artykule dotyczą usługi Azure HDInsight 3,4 Spark 1,6. Jednak kod w tym artykule i [Scala Jupyter Notebook](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb) są ogólne i powinny być wykonywane w dowolnym klastrze Spark. Kroki instalacji i zarządzania klastrami mogą się nieco różnić od tego, co zostało przedstawione w tym artykule, jeśli nie używasz usługi HDInsight Spark.

> [!NOTE]
> Aby uzyskać informacje na temat sposobu korzystania z języka Python zamiast Scala do wykonywania zadań związanych z kompleksowym procesem nauki o danych, zobacz [nauka danych przy użyciu platformy Spark w usłudze Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
* Wymagana jest subskrypcja platformy Azure. Jeśli jeszcze tego nie masz, [Uzyskaj bezpłatną wersję próbną platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Do wykonania poniższych procedur potrzebny jest klaster usługi Azure HDInsight 3,4 Spark 1,6. Aby utworzyć klaster, zobacz instrukcje w artykule [wprowadzenie: tworzenie Apache Spark w usłudze Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Ustaw typ i wersję klastra w menu **Wybierz typ klastra** .

![Konfiguracja typu klastra usługi HDInsight](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Opis danych o wykorzystaniu z NYCych taksówki i instrukcje dotyczące wykonywania kodu z notesu programu Jupyter w klastrze Spark znajdują się w odpowiednich sekcjach [Omówienie informacji o nauce danych przy użyciu platformy Spark w usłudze Azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Wykonywanie kodu Scala z notesu Jupyter w klastrze Spark
Notes Jupyter można uruchomić z poziomu Azure Portal. Znajdź klaster Spark na pulpicie nawigacyjnym, a następnie kliknij go, aby przejść do strony zarządzania klastra. Następnie kliknij pozycję **pulpity nawigacyjne klastra**, a następnie kliknij przycisk **Jupyter Notebook** , aby otworzyć Notes skojarzony z klastrem Spark.

![Pulpit nawigacyjny klastra i notesy Jupyter](./media/scala-walkthrough/spark-jupyter-on-portal.png)

Możesz również uzyskać dostęp do notesów Jupyter w &lt; klastrze https:// &gt; . azurehdinsight.NET/Jupyter. Zastąp wartość *ClusterName* nazwą klastra. Do uzyskania dostępu do notesów Jupyter wymagane jest hasło do konta administratora.

![Przejdź do notesów Jupyter przy użyciu nazwy klastra](./media/scala-walkthrough/spark-jupyter-notebook.png)

Wybierz pozycję **Scala** , aby wyświetlić katalog zawierający kilka przykładów notesów z pakietami, które korzystają z interfejsu API PySpark. Modelowanie eksploracji i ocenianie przy użyciu notesu Scala. ipynb zawierającego przykłady kodu dla tego zestawu tematów platformy Spark jest dostępne w witrynie [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).

Notes można przekazać bezpośrednio z witryny GitHub do serwera Jupyter Notebook w klastrze Spark. Na stronie głównej Jupyter kliknij przycisk **Przekaż** . W Eksploratorze plików Wklej adres URL witryny GitHub (nieprzetworzona zawartość) notesu Scala, a następnie kliknij przycisk **Otwórz**. Notes Scala jest dostępny pod następującym adresem URL:

[Eksploracja — modelowanie i ocenianie — za pomocą-Scala. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Konfiguracja: wstępnie ustawiony kontekst platformy Spark i programu Hive, magiczna platforma Spark i biblioteki platformy Spark
### <a name="preset-spark-and-hive-contexts"></a>Wstępnie ustawiony kontekst platformy Spark i programu Hive

```scala
# SET THE START TIME
import java.util.Calendar
val beginningTime = Calendar.getInstance().getTime()
```

Jądra platformy Spark dostarczane z notesami Jupyter mają wstępnie ustawione konteksty. Nie musisz jawnie ustawiać kontekstów platformy Spark lub Hive przed rozpoczęciem pracy z aplikacją, którą tworzysz. Konteksty predefiniowane są następujące:

* `sc` dla SparkContext
* `sqlContext` dla HiveContext

### <a name="spark-magics"></a>Magiczna platforma Spark
Jądro Spark zawiera kilka wstępnie zdefiniowanych "Magic", które są poleceniami specjalnymi, z których można nawiązać połączenie `%%` . Dwa z tych poleceń są używane w poniższych przykładach kodu.

* `%%local` Określa, że kod w kolejnych wierszach będzie wykonywany lokalnie. Kod musi być prawidłowym kodem Scala.
* `%%sql -o <variable name>` Wykonuje zapytanie programu Hive względem `sqlContext` . Jeśli `-o` parametr zostanie przesłany, wynik zapytania jest utrwalany w `%%local` kontekście Scala jako ramka danych Spark.

Aby uzyskać więcej informacji na temat jądra dla notesów Jupyter oraz ich wstępnie zdefiniowanych "magicznych", które są wywoływane za pomocą `%%` (na przykład `%%local` ), zobacz [jądra dostępne dla notesów Jupyter z klastrami usługi HDInsight Spark Linux w usłudze HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Importowanie bibliotek
Zaimportuj platformy Spark, MLlib i inne potrzebne biblioteki, korzystając z poniższego kodu.

```scala
# IMPORT SPARK AND JAVA LIBRARIES
import org.apache.spark.sql.SQLContext
import org.apache.spark.sql.functions._
import java.text.SimpleDateFormat
import java.util.Calendar
import sqlContext.implicits._
import org.apache.spark.sql.Row

# IMPORT SPARK SQL FUNCTIONS
import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
import org.apache.spark.sql.functions.rand

# IMPORT SPARK ML FUNCTIONS
import org.apache.spark.ml.Pipeline
import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

# IMPORT SPARK MLLIB FUNCTIONS
import org.apache.spark.mllib.linalg.{Vector, Vectors}
import org.apache.spark.mllib.util.MLUtils
import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
import org.apache.spark.mllib.tree.configuration.BoostingStrategy
import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

# SPECIFY SQLCONTEXT
val sqlContext = new SQLContext(sc)
```

## <a name="data-ingestion"></a>Wprowadzanie danych
Pierwszym krokiem w procesie nauki danych jest pozyskiwanie danych, które mają być analizowane. Dane pochodzą ze źródeł zewnętrznych lub systemów, w których znajdują się w środowisku eksploracji i modelowania danych. W tym artykule pozyskane dane są przyłączonym do 0,1% próbki pliku podróży i opłat za taksówkę (przechowywane jako plik. tsv). Środowisko eksploracji i modelowania danych to platforma Spark. Ta sekcja zawiera kod umożliwiający wykonanie następującej serii zadań:

1. Ustaw ścieżki katalogów dla magazynu danych i modeli.
2. Przeczytaj w zestawie danych wejściowych (zapisany jako plik. tsv).
3. Zdefiniuj schemat danych i Wyczyść dane.
4. Utwórz oczyszczoną ramkę danych i Buforuj ją w pamięci.
5. Zarejestruj dane jako tabelę tymczasową w elemencie SqlContext.
6. Wykonaj zapytanie względem tabeli i zaimportuj wyniki do ramki danych.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Ustawianie ścieżek katalogów dla lokalizacji przechowywania w usłudze Azure Blob Storage
Platforma Spark może odczytywać i zapisywać dane w usłudze Azure Blob Storage. Możesz użyć platformy Spark do przetworzenia wszelkich istniejących danych, a następnie ponownie zapisać wyniki w usłudze BLOB Storage.

Aby zapisać modele lub pliki w usłudze BLOB Storage, należy prawidłowo określić ścieżkę. Odwołuje się do domyślnego kontenera dołączonego do klastra Spark przy użyciu ścieżki rozpoczynającej się od `wasb:///` . Odwołuje się do innych lokalizacji za pomocą `wasb://` .

Poniższy przykład kodu określa lokalizację danych wejściowych do odczytu i ścieżkę do magazynu obiektów blob, który jest dołączony do klastra Spark, w którym zostanie zapisany model.

```scala
# SET PATHS TO DATA AND MODEL FILE LOCATIONS
# INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
val header = taxi_train_file.first;

# SET THE MODEL STORAGE DIRECTORY PATH
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";
```

### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Importowanie danych, tworzenie RDD i Definiowanie ramki danych zgodnie ze schematem

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
val sqlContext = new SQLContext(sc)
val taxi_schema = StructType(
    Array(
        StructField("medallion", StringType, true),
        StructField("hack_license", StringType, true),
        StructField("vendor_id", StringType, true),
        StructField("rate_code", DoubleType, true),
        StructField("store_and_fwd_flag", StringType, true),
        StructField("pickup_datetime", StringType, true),
        StructField("dropoff_datetime", StringType, true),
        StructField("pickup_hour", DoubleType, true),
        StructField("pickup_week", DoubleType, true),
        StructField("weekday", DoubleType, true),
        StructField("passenger_count", DoubleType, true),
        StructField("trip_time_in_secs", DoubleType, true),
        StructField("trip_distance", DoubleType, true),
        StructField("pickup_longitude", DoubleType, true),
        StructField("pickup_latitude", DoubleType, true),
        StructField("dropoff_longitude", DoubleType, true),
        StructField("dropoff_latitude", DoubleType, true),
        StructField("direct_distance", StringType, true),
        StructField("payment_type", StringType, true),
        StructField("fare_amount", DoubleType, true),
        StructField("surcharge", DoubleType, true),
        StructField("mta_tax", DoubleType, true),
        StructField("tip_amount", DoubleType, true),
        StructField("tolls_amount", DoubleType, true),
        StructField("total_amount", DoubleType, true),
        StructField("tipped", DoubleType, true),
        StructField("tip_class", DoubleType, true)
        )
    )

# CAST VARIABLES ACCORDING TO THE SCHEMA
val taxi_temp = (taxi_train_file.map(_.split("\t"))
                        .filter((r) => r(0) != "medallion")
                         .map(p => Row(p(0), p(1), p(2),
                            p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                            p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                            p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                            p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


# CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
        .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
        .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
        .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
        .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
        .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
        .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
        .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

# CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Rozdzielczości**

Godzina, o której ma zostać uruchomiona komórka: 8 sekund.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Kwerenda tabeli i importowanie wyników do ramki danych
Następnie Zbadaj tabelę, aby uzyskać opłaty za przejazd, pasażera i dane TIP; Odfiltruj uszkodzone i nieleżące dane; i wydrukuj kilka wierszy.

```scala
# QUERY THE DATA
val sqlStatement = """
    SELECT fare_amount, passenger_count, tip_amount, tipped
    FROM taxi_train
    WHERE passenger_count > 0 AND passenger_count < 7
    AND fare_amount > 0 AND fare_amount < 200
    AND payment_type in ('CSH', 'CRD')
    AND tip_amount > 0 AND tip_amount < 25
"""
val sqlResultsDF = sqlContext.sql(sqlStatement)

# SHOW ONLY THE TOP THREE ROWS
sqlResultsDF.show(3)
```

**Rozdzielczości**

| fare_amount | passenger_count | tip_amount | Przechylony |
| --- | --- | --- | --- |
|        13,5 |1.0 |2.9 |1.0 |
|        16,0 |2.0 |3.4 |1.0 |
|        10.5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Eksploracja i Wizualizacja danych
Po przeniesieniu danych do platformy Spark następnym krokiem w procesie analizy danych jest dokładniejsze zrozumienie danych za pomocą eksploracji i wizualizacji. W tej sekcji przeanalizuje dane dotyczące taksówki przy użyciu zapytań SQL. Następnie zaimportuj wyniki do ramki danych, aby wykreślić zmienne docelowe i funkcje potencjalnej kontroli wzrokowej przy użyciu funkcji automatycznej wizualizacji Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Używanie lokalnych i SQL Magic do kreślenia danych
Domyślnie dane wyjściowe dowolnego fragmentu kodu, który jest uruchamiany z notesu Jupyter, są dostępne w kontekście sesji utrwalonej na węzłach procesu roboczego. Jeśli chcesz zapisać podróż do węzłów procesu roboczego dla każdego obliczenia, a wszystkie dane, które są potrzebne do obliczenia, są dostępne lokalnie w węźle serwera Jupyter (który jest węzłem głównym), możesz użyć `%%local` Magic do uruchomienia fragmentu kodu na serwerze Jupyter.

* **SQL Magic** ( `%%sql` ). Jądro usługi HDInsight Spark obsługuje proste wbudowane zapytania HiveQL dla elementu SqlContext. Argument ( `-o VARIABLE_NAME` ) utrwala dane wyjściowe zapytania SQL jako Pandasą ramkę danych na serwerze Jupyter. To ustawienie oznacza, że dane wyjściowe będą dostępne w trybie lokalnym.
* `%%local`**Magic**. `%%local`Magic uruchamia kod lokalnie na serwerze Jupyter, który jest węzłem głównym klastra usługi HDInsight. Zwykle używasz `%%local` Magic w połączeniu z `%%sql` Magic z `-o` parametrem. `-o`Parametr będzie utrwalał dane wyjściowe zapytania SQL lokalnie, a następnie `%%local` Magic wywoła następny zestaw fragmentów kodu do lokalnego uruchamiania na danych wyjściowych zapytań SQL, które są utrwalane lokalnie.

### <a name="query-the-data-by-using-sql"></a>Wykonywanie zapytań dotyczących danych przy użyciu języka SQL
To zapytanie pobiera liczbę podróży z taksówką według kwoty opłaty, liczby pasażerów i kwoty pozostałej.

```scala
# RUN THE SQL QUERY
%%sql -q -o sqlResults
SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25
```

W poniższym kodzie, `%%local` Magic tworzy lokalną ramkę danych, SQLResults. Możesz użyć SQLResults do kreślenia przy użyciu matplotlib.

> [!TIP]
> Lokalna magiczna jest używana wiele razy w tym artykule. Jeśli zestaw danych jest duży, zapoznaj się z przykładem, aby utworzyć ramkę danych, która może pasować do pamięci lokalnej.
> 
> 

### <a name="plot-the-data"></a>Wykreślanie danych
Można wykreolić przy użyciu kodu języka Python, gdy ramka danych znajduje się w kontekście lokalnym jako ramka danych Pandas.

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
sqlResults
```

 Jądro platformy Spark automatycznie wizualizuje dane wyjściowe zapytań SQL (HiveQL) po uruchomieniu kodu. Można wybrać kilka typów wizualizacji:

* Tabela
* Kołowy
* Liniowy
* Warstwowy
* Słupkowy

Oto kod do wykreślenia danych:

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
import matplotlib.pyplot as plt
%matplotlib inline

# PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# PLOT TIP BY PASSENGER COUNT
ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.show()
```

**Rozdzielczości**

![Histogram kwoty porady](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Kwota Porada wg liczby osób](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Kwota Porada według kwoty opłaty](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Twórz funkcje i Przekształcaj funkcje, a następnie przygotowywaj dane dla danych wejściowych w funkcjach modelowania
W przypadku funkcji modelowania opartych na drzewie z platformy Spark ML i MLlib należy przygotować miejsce docelowe i funkcje przy użyciu różnych technik, takich jak pakowania, indeksowanie, kodowanie jednostronicowe i wektoryzacji. Poniżej przedstawiono procedury, które należy wykonać w tej sekcji:

1. Utwórz nową funkcję przez **pakowania** godziny w przedziałach czasowych.
2. Zastosuj **indeksowanie i jednostronicowe kodowanie** do funkcji kategorii.
3. **Próbki i podzielić dane** na części szkoleń i testowych.
4. **Określ zmienne i funkcje szkoleniowe**, a następnie utwórz indeksowane lub jednokodowane, zakodowane szkolenie i testowanie danych wejściowych z rozproszonym punktem z etykietami (odporne) lub ramkami danych.
5. Automatycznie **Kategoryzuj i vectorize funkcje i elementy docelowe** , które mają być używane jako dane wejściowe dla modeli uczenia maszynowego.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Utwórz nową funkcję przez pakowania godziny w przedziałach czasowych
Ten kod pokazuje, jak utworzyć nową funkcję przez pakowania godziny w przedziałach czasowych i w jaki sposób buforować powstającą ramkę danych w pamięci. Gdzie odporne i ramki danych są używane wielokrotnie, buforowanie prowadzi do lepszego czasu wykonania. W związku z tym będziesz w stanie buforować odporne i ramki danych na kilku etapach w poniższych procedurach.

```scala
# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
val sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_train
"""
val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indeksowanie i jednostronicowe kodowanie funkcji kategorii
Funkcje modelowania i przewidywania MLlib wymagają funkcji z danymi wejściowymi kategorii, które mają być indeksowane lub kodowane przed użyciem. W tej sekcji pokazano, jak indeksować lub kodować funkcje kategorii dla danych wejściowych w funkcjach modelowania.

Należy indeksować lub kodować modele na różne sposoby, w zależności od modelu. Na przykład modele regresji logistycznej i liniowej wymagają kodowania jednogorąca. Na przykład funkcja z trzema kategoriami może być rozwinięta do trzech kolumn funkcji. Każda kolumna będzie zawierać 0 lub 1 w zależności od kategorii obserwacji. MLlib udostępnia funkcję [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) dla kodowania jednostronicowego. Ten koder mapuje kolumnę indeksów etykiet do kolumny wektorów binarnych z co najwyżej jedną wartością. Dzięki temu kodowaniu algorytmy, które oczekują funkcji wartości numerycznych, takich jak regresja logistyczna, mogą być stosowane do funkcji kategorii.

Tutaj Przekształć tylko cztery zmienne, aby pokazać przykłady, które są ciągami znaków. Można również indeksować inne zmienne, takie jak dzień tygodnia, reprezentowane przez wartości liczbowe, jako zmienne kategorii.

Do indeksowania, używania `StringIndexer()` i dla jednostronicowego kodowania należy używać `OneHotEncoder()` funkcji z MLlib. Oto kod do indeksowania i kodowania funkcji kategorii:

```scala
# CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# INDEX AND ENCODE VENDOR_ID
val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
val encoded1 = encoder.transform(indexed)

# INDEX AND ENCODE RATE_CODE
val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
val indexed = stringIndexer.transform(encoded1)
val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
val encoded2 = encoder.transform(indexed)

# INDEX AND ENCODE PAYMENT_TYPE
val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
val indexed = stringIndexer.transform(encoded2)
val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
val encoded3 = encoder.transform(indexed)

# INDEX AND TRAFFIC TIME BINS
val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
val indexed = stringIndexer.transform(encoded3)
val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
val encodedFinal = encoder.transform(indexed)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Rozdzielczości**

Czas do uruchomienia komórki: 4 sekundy.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Próbkowanie i dzielenie zestawu danych na szkolenia i frakcje testowe
Ten kod tworzy losowe próbkowanie danych (25%, w tym przykładzie). Chociaż próbkowanie nie jest wymagane do tego przykładu ze względu na rozmiar zestawu danych, w artykule pokazano, jak można ją Przykładowo, aby wiedzieć, jak z niej korzystać w razie potrzeby. Gdy próbki są duże, mogą one zaoszczędzić znaczący czas podczas uczenia modeli. Następnie należy podzielić próbkę na część szkoleniową (75%, w tym przykładzie) i część testowa (25%, w tym przykładzie) do użycia w ramach modelowania klasyfikacji i regresji.

Dodaj liczbę losową (od 0 do 1) do każdego wiersza (w kolumnie "Rand"), która może służyć do wybierania założenia wzajemnego sprawdzania poprawności podczas szkoleń.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
val samplingFraction = 0.25;
val trainingFraction = 0.75;
val testingFraction = (1-trainingFraction);
val seed = 1234;
val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
val sampledDFcount = encodedFinalSampledTmp.count().toInt

val generateRandomDouble = udf(() => {
    scala.util.Random.nextDouble
})

# ADD A RANDOM NUMBER FOR CROSS-VALIDATION
val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

# SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
# INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
val trainData = splits(0)
val testData = splits(1)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Rozdzielczości**

Godzina uruchomienia komórki: 2 sekundy.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Określanie zmiennej i funkcji szkoleniowych, a następnie Tworzenie zaszyfrowanego lub jednostronicowego szkolenia, a także testowanie danych wejściowych odporne punktów lub ramek danych
Ta sekcja zawiera kod, który pokazuje, jak indeksować kategorii dane tekstowe jako typ danych z oznaczonym punktem i kodować go, aby można było używać go do uczenia i testowania regresji logistycznej MLlib i innych modeli klasyfikacji. Obiekty oznaczonych punktów są odporne, które są sformatowane w sposób, który jest wymagany jako dane wejściowe większością algorytmów uczenia maszynowego w MLlib. [Punkt oznaczony etykietą](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) jest wektorem lokalnym, czyli gęstym lub rozrzedzonym, skojarzonym z etykietą/odpowiedzią.

W tym kodzie należy określić zmienną Target (zależną) i funkcje, które mają być używane do uczenia modeli. Następnie można utworzyć indeksowane lub jednokrotnie zakodowane szkolenie, a także testować dane wejściowe odporne punktów z etykietami lub ramki danych.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

# SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

# CREATE INDEXED LABELED POINT RDD OBJECTS
val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

# CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
val indexedTESTbinaryDF = indexedTESTbinary.toDF()
val indexedTRAINregDF = indexedTRAINreg.toDF()
val indexedTESTregDF = indexedTESTreg.toDF()

# CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
val OneHotTRAIN = assemblerOneHot.transform(trainData)
val OneHotTEST = assemblerOneHot.transform(testData)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Rozdzielczości**

Czas do uruchomienia komórki: 4 sekundy.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Automatycznie Kategoryzuj i vectorize funkcje i elementy docelowe, które mają być używane jako dane wejściowe dla modeli uczenia maszynowego
Użyj platformy Spark ML do kategoryzacji obiektów docelowych i funkcji, które mają być używane w funkcjach modelowania opartych na drzewie. Kod wykonuje dwa zadania:

* Tworzy binarny element docelowy dla klasyfikacji przez przypisanie wartości 0 lub 1 do każdego punktu danych z zakresu od 0 do 1 przy użyciu wartości progowej 0,5.
* Automatycznie klasyfikuje funkcje. Jeśli liczba unikatowych wartości liczbowych dla dowolnej funkcji jest mniejsza niż 32, ta funkcja jest kategoryzowana.

Oto kod dla tych dwóch zadań.

```scala
# CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

# TRAIN DATA
val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
val indexerModel = indexer.fit(indexedTRAINbinaryDF)
val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

# TEST DATA
val indexerModel = indexer.fit(indexedTESTbinaryDF)
val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

# CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
# CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

# TRAIN DATA
val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
val indexerModel = indexer.fit(indexedTRAINregDF)
val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

# TEST DATA
val indexerModel = indexer.fit(indexedTESTbinaryDF)
val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)
```


## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Binarny model klasyfikacji: przewidywanie, czy Porada powinna być płacona
W tej sekcji utworzysz trzy typy modeli klasyfikacji binarnych, aby przewidzieć, czy Porada powinna być płacona:

* **Model regresji logistycznej** przy użyciu funkcji Spark ml `LogisticRegression()`
* **Losowy model klasyfikacji lasów** przy użyciu funkcji Spark ml `RandomForestClassifier()`
* **Model klasyfikacji drzewa zwiększający gradient** przy użyciu `GradientBoostedTrees()` funkcji MLlib

### <a name="create-a-logistic-regression-model"></a>Tworzenie modelu regresji logistycznej
Następnie Utwórz model regresji logistycznej przy użyciu funkcji Spark ML `LogisticRegression()` . Tworzysz model kodu budynku w serii kroków:

1. **Uczenie danych modelu** za pomocą jednego zestawu parametrów.
2. **Oceń model** dla zestawu danych testowych przy użyciu metryk.
3. **Zapisz model** w usłudze BLOB Storage na potrzeby przyszłego użycia.
4. **Ocena modelu** względem danych testowych.
5. **Wykreśl wyniki** przy użyciu krzywych charakterystycznych dla odbiorników (ROC).

Oto kod dla następujących procedur:

```scala
# CREATE A LOGISTIC REGRESSION MODEL
val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
val lrModel = lr.fit(OneHotTRAIN)

# PREDICT ON THE TEST DATA SET
val predictions = lrModel.transform(OneHotTEST)

# SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)
println("ROC on test data = " + ROC)

# SAVE THE MODEL
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "LogisticRegression__"
val filename = modelDir.concat(modelName).concat(datestamp)
lrModel.save(filename);
```

Ładuj, punktacja i Zapisz wyniki.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

# SCORE THE MODEL ON THE TEST DATA
val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
predictions.registerTempTable("testResults")

# SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE ROC RESULTS
println("ROC on test data = " + ROC)
```

**Rozdzielczości**

ROC na test Data = 0.9827381497557599

Użyj języka Python w lokalnych ramkach danych Pandas, aby wykreślić krzywą ROC.

```scala
# QUERY THE RESULTS
%%sql -q -o sqlResults
SELECT tipped, probability from testResults


# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
from sklearn.metrics import roc_curve,auc

sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
predictions_pddf = sqlResults[["tipped","probFloat"]]

# PREDICT THE ROC CURVE
# predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
prob = predictions_pddf["probFloat"]
fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT THE ROC CURVE
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

**Rozdzielczości**

![Krzywa ROCa TIP lub bez niej](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Utwórz losowy model klasyfikacji lasów
Następnie utwórz losowy model klasyfikacji lasów przy użyciu funkcji Spark ML `RandomForestClassifier()` , a następnie Oceń model na danych testowych.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE THE RANDOM FOREST CLASSIFIER MODEL
val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

# FIT THE MODEL
val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

# EVALUATE THE MODEL
val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
val Test_f1Score = evaluator.evaluate(predictions)
println("F1 score on test data: " + Test_f1Score);

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)
println("ROC on test data = " + ROC)
```

**Rozdzielczości**

ROC na test Data = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>Tworzenie modelu klasyfikacji GBT
Następnie utwórz GBT model klasyfikacji przy użyciu `GradientBoostedTrees()` funkcji MLlib, a następnie Oceń model na danych testowych.

```scala
# TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE GBT CLASSIFICATION MODEL
val boostingStrategy = BoostingStrategy.defaultParams("Classification")
boostingStrategy.numIterations = 20
boostingStrategy.treeStrategy.numClasses = 2
boostingStrategy.treeStrategy.maxDepth = 5
boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

# TRAIN THE MODEL
val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

# SAVE THE MODEL IN BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "GBT_Classification__"
val filename = modelDir.concat(modelName).concat(datestamp)
gbtModel.save(sc, filename);

# EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
val labelAndPreds = indexedTESTbinary.map { point =>
  val prediction = gbtModel.predict(point.features)
  (point.label, prediction)
}
val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
//println("Learned classification GBT model:\n" + gbtModel.toDebugString)
println("Test Error = " + testErr)

# USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
val metrics = new MulticlassMetrics(labelAndPreds)
println(s"Precision: ${metrics.precision}")
println(s"Recall: ${metrics.recall}")
println(s"F1 Score: ${metrics.fMeasure}")

val metrics = new BinaryClassificationMetrics(labelAndPreds)
println(s"Area under PR curve: ${metrics.areaUnderPR}")
println(s"Area under ROC curve: ${metrics.areaUnderROC}")

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE ROC METRIC
println(s"Area under ROC curve: ${metrics.areaUnderROC}")
```

**Rozdzielczości**

Obszar pod krzywą ROC: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Model regresji: prognozowana kwota Porada
W tej sekcji utworzysz dwa typy modeli regresji do przewidywania ilości Porada:

* **Model regresji liniowej** przy użyciu funkcji Spark ml `LinearRegression()` . Będziesz zapisywać model i oszacować model na danych testowych.
* **Model regresji drzewa zwiększający gradient** przy użyciu funkcji Spark ml `GBTRegressor()` .

### <a name="create-a-regularized-linear-regression-model"></a>Tworzenie standardowego modelu regresji liniowej

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

# FIT THE MODEL BY USING DATA FRAMES
val lrModel = lr.fit(OneHotTRAIN)
println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

# SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
val trainingSummary = lrModel.summary
println(s"numIterations: ${trainingSummary.totalIterations}")
println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
trainingSummary.residuals.show()
println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
println(s"r2: ${trainingSummary.r2}")

# SAVE THE MODEL IN AZURE BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "LinearRegression__"
val filename = modelDir.concat(modelName).concat(datestamp)
lrModel.save(filename);

# PRINT THE COEFFICIENTS
println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

# SCORE THE MODEL ON TEST DATA
val predictions = lrModel.transform(OneHotTEST)

# EVALUATE THE MODEL ON TEST DATA
val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
val r2 = evaluator.evaluate(predictions)
println("R-sqr on test data = " + r2)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```


**Rozdzielczości**

Czas do uruchomienia komórki: 13 sekund.

```scala
# LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

# SCORE THE MODEL ON TEST DATA
val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
predictions.registerTempTable("testResults")

# EVALUATE THE MODEL ON TEST DATA
val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
val r2 = evaluator.evaluate(predictions)
println("R-sqr on test data = " + r2)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE RESULTS
println("R-sqr on test data = " + r2)
```

**Rozdzielczości**

R-SQR na test Data = 0.5960320470835743

Następnie Zbadaj wyniki testu jako ramkę danych i użyj AutoVizWidget i matplotlib do wizualizacji.

```sql
# RUN A SQL QUERY
%%sql -q -o sqlResults
select * from testResults

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
sqlResults
```

Kod tworzy lokalną ramkę danych z wyników zapytania i wykreśla dane. `%%local`Magic tworzy lokalną ramkę danych, `sqlResults` która może być używana do kreślenia w matplotlib.

> [!NOTE]
> Ten magiczny element jest używany wielokrotnie w tym artykule. Jeśli ilość danych jest duża, należy przykładowo utworzyć ramkę danych, która może pasować do pamięci lokalnej.
> 
> 

Utwórz wykresy przy użyciu języka Python matplotlib.

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
sqlResults
%matplotlib inline
import numpy as np

# PLOT THE RESULTS
ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
#ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
plt.axis([-1, 15, -1, 8])
plt.show(ax)
```

**Rozdzielczości**

![Kwota Porada: rzeczywiste a przewidywane](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Tworzenie modelu regresji GBT
Utwórz model regresji GBT przy użyciu funkcji Spark ML `GBTRegressor()` , a następnie Oceń model na danych testowych.

[Drzewa z podwyższeniem gradientu](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) są kompletnymi drzewami decyzyjnymi. GBTS pociąga drzewa decyzyjne iteracyjnie, aby zminimalizować funkcję strat. Można użyć GBTS do regresji i klasyfikacji. Mogą obsługiwać funkcje kategorii, nie wymagają skalowania funkcji i mogą przechwytywać interakcje i funkcje. Można ich również użyć w ustawieniu klasyfikacji wieloklasowej.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# TRAIN A GBT REGRESSION MODEL
val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

# MAKE PREDICTIONS
val predictions = gbtModel.transform(indexedTESTwithCatFeat)

# COMPUTE TEST SET R2
val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
val Test_R2 = evaluator.evaluate(predictions)


# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE RESULTS
println("Test R-sqr is: " + Test_R2);
```

**Rozdzielczości**

Test R-SQR to: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Zaawansowane narzędzia do modelowania do optymalizacji
W tej sekcji użyjesz narzędzi uczenia maszynowego, których deweloperzy często używają do optymalizacji modelu. W tym celu można zoptymalizować modele uczenia maszynowego trzy różne sposoby przy użyciu czyszczenia parametrów i weryfikacji krzyżowej:

* Podziel dane na zestawy uczenia i walidacji, Optymalizuj model przy użyciu funkcji rozpoznawania parametrów w zestawie szkoleń i Oceń zestaw walidacji (regresja liniowa)
* Optymalizowanie modelu przy użyciu funkcji CrossValidator z walidacją i parametrów funkcji Hyper-Parameter
* Optymalizuj model przy użyciu niestandardowego kodu do sprawdzania krzyżowego i czyszczenia parametrów, aby użyć dowolnej funkcji uczenia maszynowego i zestawu parametrów (regresja liniowa)

**Wzajemne sprawdzanie poprawności** jest techniką, która ocenia, jak dobrze jest przeszkolony model na znanym zestawie danych, aby przewidzieć funkcje zestawów danych, na których nie zostały przeszkolone. Ogólnym dobrym pomysłem jest to, że model jest szkolony na zestawie danych znanych danych, a następnie dokładność jego prognoz jest testowana względem niezależnego zestawu danych. Typowa implementacja polega na podzieleniu zestawu danych na składowe *k*, a następnie nauczenie modelu w sposób okrężny na wszystkich, ale jednym ze zgięciów.

**Optymalizacja parametrów funkcji Hyper-Parameter** to problem polegający na wyborze zestawu funkcji Hyper-Parameters dla algorytmu uczenia, zazwyczaj z celem optymalizacji miary wydajności algorytmu w niezależnym zestawie danych. Parametr Hyper-Parameter jest wartością, która musi być określona poza procedurą szkolenia modelu. Założenia dotyczące wartości parametrów funkcji Hyper-Parameter mogą mieć wpływ na elastyczność i dokładność modelu. Drzewa decyzyjne mają parametry funkcji Hyper-Parameters, na przykład na żądaną głębokość i liczbę liści w drzewie. Należy ustawić nieprawidłową klasyfikację dla maszyny wektorowej pomocy technicznej (SVM).

Typowym sposobem wykonania optymalizacji parametrów funkcji Hyper-Parameter jest użycie przeszukiwania siatki, nazywanego również **wyczyszczeniem parametrów**. W wyszukiwaniu siatki, pełne wyszukiwanie odbywa się za pomocą wartości określonego podzestawu przestrzeni parametrów funkcji Hyper-Parameter dla algorytmu uczenia. Wzajemne sprawdzanie poprawności może dostarczyć metrykę wydajności w celu sortowania optymalnych wyników wytwarzanych przez algorytm wyszukiwania w siatce. W przypadku używania krzyżowego sprawdzania poprawności parametrów funkcji Hyper-parametr można ograniczyć problemy, takie jak niedopasowanie modelu do danych szkoleniowych. Dzięki temu model zachowuje pojemność do zastosowania do ogólnego zestawu danych, z którego zostały wyodrębnione dane szkoleniowe.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Optymalizuj model regresji liniowej z funkcją czyszczenia parametrów funkcji Hyper-Parameter
Następnie należy podzielić dane na zestawy uczenia i walidacji, użyć funkcji czyszczenia parametrów w zestawie szkoleń, aby zoptymalizować model i oszacować zestaw walidacji (regresja liniowa).

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# RENAME `tip_amount` AS A LABEL
val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
OneHotTRAINLabeled.cache()
OneHotTESTLabeled.cache()

# DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

# DEFINE THE PARAMETER GRID
val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

# DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
val trainPct = 0.75
val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
val model = trainValidationSplit.fit(OneHotTRAINLabeled)

# MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

# COMPUTE TEST SET R2
val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
val Test_R2 = evaluator.evaluate(testResults)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

println("Test R-sqr is: " + Test_R2);
```

**Rozdzielczości**

Test R-SQR to: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optymalizuj model klasyfikacji binarnej przy użyciu operacji czyszczenia krzyżowego i funkcji Hyper-Parameter
W tej sekcji pokazano, jak zoptymalizować binarny model klasyfikacji przy użyciu operacji czyszczenia krzyżowego i funkcji Hyper-Parameter. Ta funkcja używa funkcji Spark ML `CrossValidator` .

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
indexedTRAINwithCatFeatBinTargetRF.cache()
indexedTESTwithCatFeatBinTargetRF.cache()

# DEFINE THE ESTIMATOR FUNCTION
val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

# DEFINE THE PARAMETER GRID
val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

# SPECIFY THE NUMBER OF FOLDS
val numFolds = 3

# DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

# MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

# COMPUTE THE TEST F1 SCORE
val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
val Test_f1Score = evaluator.evaluate(testResults)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Rozdzielczości**

Godzina uruchomienia komórki: 33 sekund.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Optymalizowanie modelu regresji liniowej za pomocą niestandardowej weryfikacji krzyżowej i kodu odczyszczenia parametrów
Następnie Zoptymalizuj model przy użyciu kodu niestandardowego i zidentyfikuj najlepsze parametry modelu przy użyciu kryterium o najwyższej dokładności. Następnie Utwórz model końcowy, Oceń model na danych testowych i Zapisz model w usłudze BLOB Storage. Na koniec Załaduj model, dane testów oceny i Oceń dokładność.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

val nFolds = 3
val numModels = paramGrid.size
val numParamsinGrid = 2

# SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

var maxDepth = -1
var numTrees = -1
var param = ""
var paramval = -1
var validateLB = -1.0
var validateUB = -1.0
val h = 1.0 / nFolds;
val RMSE  = Array.fill(numModels)(0.0)

# CREATE K-FOLDS
val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


# LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
for (i <- 0 to (nFolds-1)) {
    validateLB = i * h
    validateUB = (i + 1) * h
    val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
    val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
    val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
    val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
    validationLabPt.cache()
    trainCVLabPt.cache()

    for (nParamSets <- 0 to (numModels-1)) {
        for (nParams <- 0 to (numParamsinGrid-1)) {
            param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
            paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
            if (param == "maxDepth") {maxDepth = paramval}
            if (param == "numTrees") {numTrees = paramval}
        }
        val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                  numTrees=numTrees, maxDepth=maxDepth,
                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
        val labelAndPreds = validationLabPt.map { point =>
                                                 val prediction = rfModel.predict(point.features)
                                                  ( prediction, point.label )
                                                }
        val validMetrics = new RegressionMetrics(labelAndPreds)
        val rmse = validMetrics.rootMeanSquaredError
        RMSE(nParamSets) += rmse
    }
    validationLabPt.unpersist();
    trainCVLabPt.unpersist();
}
val minRMSEindex = RMSE.indexOf(RMSE.min)

# GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
var best_maxDepth = -1
var best_numTrees = -1
for (nParams <- 0 to (numParamsinGrid-1)) {
    param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
    paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
    if (param == "maxDepth") {best_maxDepth = paramval}
    if (param == "numTrees") {best_numTrees = paramval}
}

# CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                  numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)

# SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "BestCV_RF_Regression__"
val filename = modelDir.concat(modelName).concat(datestamp)
best_rfModel.save(sc, filename);

# PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
val labelAndPreds = indexedTESTreg.map { point =>
                                        val prediction = best_rfModel.predict(point.features)
                                        ( prediction, point.label )
                                       }

val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
val test_rsqr = new RegressionMetrics(labelAndPreds).r2

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");


# LOAD THE MODEL
val savedRFModel = RandomForestModel.load(sc, filename)

val labelAndPreds = indexedTESTreg.map { point =>
                                        val prediction = savedRFModel.predict(point.features)
                                        ( prediction, point.label )
                                       }
# TEST THE MODEL
val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
val test_rsqr = new RegressionMetrics(labelAndPreds).r2
```

**Rozdzielczości**

Godzina uruchomienia komórki: 61 sekund.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Automatyczne korzystanie z modeli uczenia maszynowego opartego na platformie Spark z Scala
Aby zapoznać się z omówieniem tematów, które przeprowadzą Cię przez zadania wchodzące w skład procesu analizy danych na platformie Azure, zobacz [zespół ds. analizy danych](./index.yml).

[Przewodniki dotyczące procesów naukowych dla nauki o danych zespołowych](walkthroughs.md) opisują inne przewodniki, które pokazują kroki w procesie nauki danych zespołu dla konkretnych scenariuszy. Instruktaż ilustrują również sposób łączenia narzędzi i usług w chmurze i lokalnych z przepływem pracy lub potoku w celu utworzenia inteligentnej aplikacji.

[Wynikowe modele usługi Uczenie maszynowe oparte na platformie Spark](spark-model-consumption.md) pokazują, jak używać kodu Scala do automatycznego ładowania i oceny nowych zestawów danych za pomocą modeli uczenia maszynowego wbudowanych w platformie Spark i zapisanych w usłudze Azure Blob Storage. Można postępować zgodnie z zawartymi w nim instrukcjami i po prostu zamienić kod języka Python na kod Scala w tym artykule, aby zapewnić automatyczne użycie.