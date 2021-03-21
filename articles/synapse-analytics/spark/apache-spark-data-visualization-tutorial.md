---
title: Wizualizowanie danych za pomocą platformy Apache Spark
description: Twórz rozbudowane wizualizacje danych przy użyciu Apache Spark i notesów analitycznych Synapse platformy Azure
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: 56af49b6fa862c93822293056752182b534ac442
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942265"
---
# <a name="analyze-data-with-apache-spark"></a>Analizowanie danych za pomocą platformy Apache Spark

W tym samouczku dowiesz się, jak przeprowadzać analizę danych w trakcie nauki, korzystając z usługi Azure Open DataSets i Apache Spark. Następnie można wizualizować wyniki w notesie programu Synapse Studio w usłudze Azure Synapse Analytics.

W szczególności przeanalizuje zestaw danych o [taksówkach w Nowym Jorku (NYC)](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) . Dane są dostępne za pomocą otwartych zestawów danych platformy Azure. Ten podzestaw zestawu danych zawiera informacje o żółtych rejsach z taksówką: informacje o każdej podróży, godzinie rozpoczęcia i zakończeniu oraz o położeniu oraz o kosztach i innych interesujących atrybutach.
  
## <a name="before-you-begin"></a>Zanim rozpoczniesz
Utwórz pulę Apache Spark, postępując zgodnie z [samouczkiem Tworzenie puli Apache Spark](../articles/../quickstart-create-apache-spark-pool-studio.md). 

## <a name="download-and-prepare-the-data"></a>Pobieranie i przygotowywanie danych
1. Utwórz Notes przy użyciu jądra PySpark. Aby uzyskać instrukcje, zobacz [Tworzenie notesu](../quickstart-apache-spark-notebook.md#create-a-notebook). 
   
   > [!Note]
   > Ze względu na jądro PySpark nie trzeba jawnie tworzyć kontekstów. Kontekst platformy Spark jest tworzony automatycznie po uruchomieniu pierwszej komórki kodu.

2. W tym samouczku użyjemy kilku różnych bibliotek, aby pomóc nam w wizualizacji zestawu danych. Aby przeprowadzić tę analizę, Zaimportuj następujące biblioteki: 

   ```python
   import matplotlib.pyplot as plt
   import seaborn as sns
   import pandas as pd
   ```

3. Ponieważ dane pierwotne są w formacie Parquet, można użyć kontekstu Spark, aby ściągnąć plik do pamięci jako element danych bezpośrednio. Utwórz ramkę danych Spark, pobierając dane za pośrednictwem interfejsu API Open DataSets. W tym miejscu używamy schematu Spark Dataframe *dla właściwości odczytu* w celu wywnioskowania typów danych i schematu.

   ```python
   from azureml.opendatasets import NycTlcYellow
   from datetime import datetime
   from dateutil import parser

   end_date = parser.parse('2018-06-06')
   start_date = parser.parse('2018-05-01')
   nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
   df = nyc_tlc.to_spark_dataframe()
   ```

4. Po odczytaniu danych należy wykonać wstępne filtrowanie, aby wyczyścić zestaw danych. Firma Microsoft może usunąć niepotrzebne kolumny i dodać kolumny wyodrębniające ważne informacje. Ponadto będziemy odfiltrować anomalie w zestawie danych.

   ```python
   # Filter the dataset 
   from pyspark.sql.functions import *

   filtered_df = df.select('vendorID', 'passengerCount', 'tripDistance','paymentType', 'fareAmount', 'tipAmount'\
                                   , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                   , dayofweek('tpepPickupDateTime').alias('day_of_week')\
                                   , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month'))\
                               .filter((df.passengerCount > 0)\
                                   & (df.tipAmount >= 0)\
                                   & (df.fareAmount >= 1) & (df.fareAmount <= 250)\
                                   & (df.tripDistance > 0) & (df.tripDistance <= 200))

   filtered_df.createOrReplaceTempView("taxi_dataset")
   ```

## <a name="analyze-data"></a>Analizowanie danych
Jako analityk danych masz szeroką gamę dostępnych narzędzi, które ułatwiają wyodrębnienie szczegółowych informacji z danych. W tej części samouczka przeprowadzimy kilka przydatnych narzędzi dostępnych w ramach notesów usługi Azure Synapse Analytics. W tej analizie chcemy zrozumieć czynniki, które dają wyższe porady dotyczące pozostałego okresu.

### <a name="apache-spark-sql-magic"></a>Apache Spark SQL Magic 
Najpierw będziemy przeanalizować dane poznawcze, Apache Spark SQL i Magic poleceń za pomocą notesu Synapse platformy Azure. Po otrzymaniu zapytania będziemy wizualizować wyniki przy użyciu wbudowanej ```chart options``` funkcji. 

1. W notesie Utwórz nową komórkę i Skopiuj poniższy kod. Korzystając z tego zapytania, chcemy zrozumieć, jak średnie kwoty etykiet zostały zmienione w wybranym okresie. To zapytanie pomoże również zidentyfikować inne przydatne informacje, w tym minimalną/maksymalną kwotę pozostałej porady dziennie i średnią kwotę opłaty.
   
   ```sql
   %%sql
   SELECT 
       day_of_month
       , MIN(tipAmount) AS minTipAmount
       , MAX(tipAmount) AS maxTipAmount
       , AVG(tipAmount) AS avgTipAmount
       , AVG(fareAmount) as fareAmount
   FROM taxi_dataset 
   GROUP BY day_of_month
   ORDER BY day_of_month ASC
   ```

2. Po zakończeniu wykonywania zapytania można wizualizować wyniki, przełączając się do widoku wykresu. Ten przykład tworzy wykres liniowy, określając ```day_of_month``` pole jako klucz i ```avgTipAmount``` jako wartość. Po dokonaniu wyboru wybierz pozycję **Zastosuj** , aby odświeżyć wykres. 
   
## <a name="visualize-data"></a>Wizualizowanie danych
Oprócz wbudowanych opcji wykresów notesu można używać popularnych bibliotek open source do tworzenia własnych wizualizacji. W poniższych przykładach będziemy używać Seaborn i matplotlib. Są to najczęściej używane biblioteki języka Python do wizualizacji danych. 

> [!Note]
> Domyślnie każda pula Apache Spark w usłudze Azure Synapse Analytics zawiera zestaw najczęściej używanych i domyślnych bibliotek. Pełną listę bibliotek można wyświetlić w dokumentacji [środowiska uruchomieniowego usługi Azure Synapse](../spark/apache-spark-version-support.md) . Ponadto w celu udostępnienia w aplikacjach innej firmy lub lokalnie skompilowanego kodu można [zainstalować bibliotekę](../spark/apache-spark-azure-portal-add-libraries.md) na jednej z pul platformy Spark.

1. Aby ułatwić programowanie i mniej kosztownych, będziemy próbkować ten zestaw danych. Użyjemy wbudowanej funkcji próbkowania Apache Spark. Ponadto zarówno Seaborn, jak i matplotlib wymagają tablicy Dataframe lub NumPy. Aby uzyskać Pandas Dataframe, użyj polecenia, ```toPandas()``` Aby przekonwertować ramkę danych.

   ```python
   # To make development easier, faster, and less expensive, downsample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   # The charting package needs a Pandas DataFrame or NumPy array to do the conversion
   sampled_taxi_pd_df = sampled_taxi_df.toPandas()
   ```

1. Chcemy zrozumieć rozkład porad w naszym zestawie danych. Będziemy używać matplotlib do tworzenia histogramu, który pokazuje rozkład kwoty i liczby pozostałej porady. Na podstawie dystrybucji możemy zobaczyć, że porady są pochylone w kierunku wielkości mniejszej niż lub równej $10.

   ```python
   # Look at a histogram of tips by count by using Matplotlib

   ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
   ax1.set_title('Tip amount distribution')
   ax1.set_xlabel('Tip Amount ($)')
   ax1.set_ylabel('Counts')
   plt.suptitle('')
   plt.show()
   ```

   ![Histogram etykiet.](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. Następnie chcemy zrozumieć relacje między wskazówkami dotyczącymi danej podróży i dniem tygodnia. Użyj Seaborn do utworzenia wykresu skrzynkowego, który podsumowuje trendy dla każdego dnia tygodnia. 

   ```python
   # View the distribution of tips by day of week using Seaborn
   ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
   ax.set_title('Tip amount distribution per day')
   ax.set_xlabel('Day of Week')
   ax.set_ylabel('Tip Amount ($)')
   plt.show()

   ```
   ![Wykres przedstawiający rozkład porad na dzień.](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. Kolejną hipotezą w naszej firmie może być to, że istnieje dodatnia relacja między liczbą osób i łączną kwotą pozostałej z taksówki. Aby sprawdzić tę relację, uruchom następujący kod w celu wygenerowania wykresu skrzynkowego, który ilustruje rozkład etykiet dla każdej liczby pasażerów. 
   
   ```python
   # How many passengers tipped by various amounts 
   ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
   ax2.set_title('Tip amount by Passenger count')
   ax2.set_xlabel('Passenger count')
   ax2.set_ylabel('Tip Amount ($)')
   ax2.set_ylim(0,30)
   plt.suptitle('')
   plt.show()
   ```
   ![Wykres przedstawiający obszar whisky.](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. Na koniec chcemy zrozumieć relacje między kwotą opłaty i ilością pozostałej. W oparciu o wyniki możemy zobaczyć, że istnieje kilka uwag, w których nie podano. Jednak zobaczymy również dodatnią relację między ogólną kwotą opłaty i poradami.
   
   ```python
   # Look at the relationship between fare and tip amounts

   ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
   ax.set_title('Tip amount by Fare amount')
   ax.set_xlabel('Fare Amount ($)')
   ax.set_ylabel('Tip Amount ($)')
   plt.axis([-2, 80, -2, 20])
   plt.suptitle('')
   plt.show()
   ```
   ![Wykres punktowy kwoty Porada.](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>Zamknij wystąpienie platformy Spark

Po zakończeniu działania aplikacji Zamknij Notes, aby zwolnić zasoby. Zamknij kartę lub wybierz pozycję **Zakończ sesję** w panelu stanu w dolnej części notesu.

## <a name="see-also"></a>Zobacz też

- [Przegląd: Apache Spark w usłudze Azure Synapse Analytics](apache-spark-overview.md)
- [Tworzenie modelu uczenia maszynowego za pomocą platformy Apache SparkML](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>Następne kroki

- [Azure Synapse Analytics](../index.yml)
- [Apache Spark Oficjalna dokumentacja](https://spark.apache.org/docs/latest/)