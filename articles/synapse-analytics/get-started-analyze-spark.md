---
title: 'Szybki Start: Rozpoczynanie analizy przy użyciu platformy Spark'
description: W ramach tego samouczka nauczysz się analizować dane przy użyciu Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 6b3c1ac2ea3625a768e16a3465230a5386c98ddc
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102423717"
---
# <a name="analyze-with-apache-spark"></a>Analizowanie za pomocą Apache Spark

W tym samouczku przedstawiono podstawowe kroki służące do ładowania i analizowania danych za pomocą Apache Spark dla usługi Azure Synapse.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analizowanie danych z NYC taksówki w usłudze BLOB Storage przy użyciu platformy Spark

1. W centrum **danych** kliknij **+** przycisk, aby **dodać nowy zasób**, a następnie kliknij przycisk >> **Przeglądaj Galerię**. 
1. Znajdź **NYCe taksówki & Limousine z Komisją** i kliknij ją. 
1. W dolnej części strony naciśnij pozycję **Kontynuuj**, a następnie **Dodaj zestaw danych**. 
1. Po chwili w obszarze **połączonej** centrum **danych** kliknij prawym przyciskiem myszy pozycję **Azure Blob Storage >> przykładowe zestawy danych >> nyc_tlc_yellow** i wybierz opcję **Nowy Notes**, a następnie **Załaduj do ramki danych**.
1. Spowoduje to utworzenie nowego notesu o następującym kodzie:
    ```

    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    df = data.to_spark_dataframe()
    # Display 10 rows
    display(df.limit(10))
    ```
1. W notesie w menu **Dołącz do** wybierz **Spark1** pulę platformy Spark bezserwerową utworzoną wcześniej.
1. Wybierz pozycję **Uruchom** w komórce. Synapse rozpocznie nową sesję platformy Spark, aby uruchomić tę komórkę w razie potrzeby. Jeśli jest wymagana Nowa sesja platformy Spark, intially to zajmie około dwóch sekund. 
1. Jeśli chcesz tylko zobaczyć schemat ramki danych, uruchom komórkę o następującym kodzie:
    ```

    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Ładowanie danych z NYC taksówki do bazy danych Spark nyctaxi

Dane są dostępne w tabeli w **SQLPOOL1**. Załaduj je do bazy danych Spark o nazwie **nyctaxi**.

1. W programie Synapse Studio przejdź do centrum **opracowywania** .
1. Wybierz pozycję **+**  >  **Notes**.
1. W górnej części notesu ustaw wartość **Dołącz do** na **Spark1**.
1. W pierwszej komórce kodu nowego notesu wpisz następujący kod:


    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```


1. Uruchom skrypt. Może upłynąć do 2-3 minut.
1. W centrum **danych** na karcie **obszar roboczy** kliknij prawym przyciskiem myszy pozycję **bazy danych**, a następnie wybierz polecenie **Odśwież**. Na liście powinna zostać wyświetlona baza danych **nyctaxi (Spark)** .


## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analizowanie danych z NYC taksówki przy użyciu platformy Spark i notesów

1. Wróć do notesu.
1. Utwórz nową komórkę kodu i wprowadź następujący kod. 


   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Uruchom komórkę, aby wyświetlić dane z NYC taksówki, które zostały załadowane do bazy danych **nyctaxi** Spark.
1. Utwórz nową komórkę kodu i wprowadź następujący kod. Następnie uruchom komórkę, aby wykonać tę samą analizę, która była wcześniej z dedykowaną pulą SQL **SQLPOOL1**. Ten kod zapisuje i wyświetla wyniki analizy w tabeli o nazwie **nyctaxi. passengercountstats**.


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

## <a name="load-data-from-a-spark-table-into-a-dedicated-sql-pool-table"></a>Ładowanie danych z tabeli Spark do dedykowanej tabeli puli SQL

Wcześniej skopiowano dane z tabeli dedykowanej puli SQL **SQLPOOL1. dbo. Trip** do tabeli Spark **nyctaxi. Trip**. Następnie agregujesz dane do tabeli Spark **nyctaxi. passengercountstats**. Teraz skopiujesz dane z **nyctaxi. passengercountstats** do dedykowanej tabeli puli SQL o nazwie **SQLPOOL1. dbo. passengercountstats**.

1. Utwórz nową komórkę kodu i wprowadź następujący kod. Uruchom komórkę w notesie. Kopiuje tabelę zagregowanych danych platformy Spark z powrotem do dedykowanej tabeli puli SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie danych za pomocą puli SQL bezserwerowej](get-started-analyze-sql-on-demand.md)
