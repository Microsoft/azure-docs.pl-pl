---
title: 'Samouczek: wprowadzenie do analizy przy użyciu platformy Spark'
description: W tym samouczku dowiesz się, jak analizować dane za pomocą Apache Spark
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: f03f98a1a1f36900d65e132a3420ac037214aaff
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893544"
---
# <a name="analyze-with-apache-spark"></a>Analizowanie za pomocą Apache Spark

## <a name="analyze-nyc-taxi-data-in-blob-storage--using-spark"></a>Analizowanie danych z NYC taksówki w usłudze BLOB Storage przy użyciu platformy Spark

W tym samouczku przedstawiono podstawowe kroki służące do ładowania i analizowania danych za pomocą Apache Spark dla usługi Azure Synapse.

1. W centrum **danych** kliknij pozycję **Dodaj nowy zasób**(plus przycisk powyżej **połączone**)  >> **Przeglądaj przykłady**. Znajdź **NYCe taksówki & Limousine z Komisją** i kliknij ją. W dolnej części strony naciśnij pozycję **Kontynuuj** i po tym, aby **dodać zestaw danych**. Teraz w centrum **danych** w obszarze **połączone** wybierz kliknij prawym przyciskiem myszy pozycję **Azure Blob Storage >> przykładowe zestawy danych >> nyc_tlc_yellow** i wybierz pozycję **Nowy Notes**
1. Spowoduje to utworzenie nowego notesu o następującym kodzie:
    ```
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```
1. W notesie wybierz pulę platformy Spark w menu **Dołącz do**
1. Kliknij pozycję **Uruchom** w komórce.

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Ładowanie danych z NYC taksówki do bazy danych Spark nyctaxi

Dostępne są dane w tabeli w **SQLDB1**. Załaduj je do bazy danych Spark o nazwie **nyctaxi**.

1. W programie Synapse Studio przejdź do centrum **opracowywania** .
1. Wybierz pozycję **+**  >  **Notes**.
1. W górnej części notesu ustaw wartość **Dołącz do** na **Spark1**.
1. Wybierz pozycję **Dodaj kod** , aby dodać komórkę kodu notesu, a następnie wklej następujący tekst:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Przejdź do centrum **danych** , kliknij prawym przyciskiem myszy pozycję **bazy danych**, a następnie wybierz polecenie **Odśwież**. Powinny zostać wyświetlone następujące bazy danych:

    - **SQLDB1** (Pula SQL)
    - **nyctaxi** (Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analizowanie danych z NYC taksówki przy użyciu platformy Spark i notesów

1. Wróć do notesu.
1. Utwórz nową komórkę kodu i wprowadź następujący tekst. Następnie uruchom komórkę, aby wyświetlić dane z NYC taksówki załadowane do bazy danych **nyctaxi** Spark.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Uruchom następujący kod, aby wykonać tę samą analizę, która była wcześniej z pulą SQL **SQLDB1**. Ten kod zapisuje wyniki analizy w tabeli o nazwie **nyctaxi. passengercountstats** i wizualizowa wyniki.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. W obszarze wyniki komórki wybierz pozycję **Wykres** , aby wyświetlić wizualizację danych.

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Dostosowywanie wizualizacji danych za pomocą platformy Spark i notesów

Możesz kontrolować sposób renderowania wykresów za pomocą notesów. Poniższy kod przedstawia prosty przykład. Używa popularnych bibliotek **matplotlib** i **Seaborn**. Kod renderuje ten sam rodzaj wykresu liniowego jako zapytania SQL, które zostały wcześniej uruchomione.

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```



## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Ładowanie danych z tabeli platformy Spark do tabeli puli SQL

Wcześniej skopiowano dane z tabeli puli SQL **SQLDB1. dbo. Trip** do tabeli Spark **nyctaxi. Trip**. Następnie używając platformy Spark, agregujemy dane do tabeli Spark **nyctaxi. passengercountstats**. Teraz skopiujemy dane z **nyctaxi. passengercountstats** do tabeli puli SQL o nazwie **SQLDB1. dbo. passengercountstats**.

Uruchom następującą komórkę w notesie. Kopiuje tabelę zagregowanych danych platformy Spark z powrotem do tabeli puli SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie danych za pomocą programu SQL na żądanie](get-started-analyze-sql-on-demand.md)


