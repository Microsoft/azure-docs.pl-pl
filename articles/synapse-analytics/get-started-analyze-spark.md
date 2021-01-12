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
ms.date: 07/20/2020
ms.openlocfilehash: 3b5f5d64498922e9fc35942ff4570d801aa6c516
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118884"
---
# <a name="analyze-with-apache-spark"></a>Analizowanie za pomocą Apache Spark

W tym samouczku przedstawiono podstawowe kroki służące do ładowania i analizowania danych za pomocą Apache Spark dla usługi Azure Synapse.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analizowanie danych z NYC taksówki w usłudze BLOB Storage przy użyciu platformy Spark

1. W centrum **danych** wybierz pozycję **Dodaj nowy zasób** (plus przycisk powyżej **połączone**) > **Przeglądaj Galerię**.
1. Wybierz kolejno **NYC taksówke & Limousine Commission-żółtej z podróży**.
1. W dolnej części strony wybierz pozycję **Kontynuuj**  >  **Dodawanie zestawu danych**.
1. W obszarze **połączone** centrum **danych** kliknij prawym przyciskiem myszy pozycję **Azure Blob Storage**, a następnie wybierz pozycję **przykładowe zestawy danych**  >  **nyc_tlc_yellow**.
1. Wybierz pozycję **Nowy Notes** , aby utworzyć nowy notes o następującym kodzie:

    ```py
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```

1. W menu **Dołącz do** w notesie wybierz bezserwerową pulę platformy Spark.
1. Wybierz pozycję **Uruchom** w komórce.
1. Jeśli chcesz zobaczyć tylko schemat ramki danych, uruchom komórkę o następującym kodzie:

    ```py
    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Ładowanie danych z NYC taksówki do bazy danych Spark nyctaxi

Dane są dostępne w tabeli w **SQLPOOL1**. Załaduj je do bazy danych Spark o nazwie **nyctaxi**.

1. W programie Synapse Studio przejdź do centrum **opracowywania** .
1. Wybierz pozycję **+**  >  **Notes**.
1. W górnej części notesu ustaw wartość **Dołącz do** na **Spark1**.
1. Wybierz pozycję **Dodaj kod** , aby dodać komórkę kodu notesu, a następnie wprowadź następujący tekst:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Wybierz pozycję **Uruchom** w komórce.
1. W centrum **danych** kliknij prawym przyciskiem myszy pozycję **bazy danych**, a następnie wybierz polecenie **Odśwież**. Powinny zostać wyświetlone następujące bazy danych:

    - **SQLPOOL1 (SQL)**
    - **nyctaxi (Spark)**

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analizowanie danych z NYC taksówki przy użyciu platformy Spark i notesów

1. Wróć do notesu.
1. Utwórz nową komórkę kodu i wprowadź następujący tekst.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Uruchom komórkę, aby wyświetlić dane z NYC taksówki, które zostały załadowane do bazy danych **nyctaxi** Spark.
1. Uruchom następujący kod, aby wykonać tę samą analizę, która była wcześniej w dedykowanej puli SQL **SQLPOOL1**. Ten kod zapisuje i wyświetla wyniki analizy w tabeli o nazwie **nyctaxi. passengercountstats**.

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

Wcześniej skopiowano dane z dedykowanej tabeli puli SQL **SQLPOOL1. dbo. Trip** do tabeli Spark **nyctaxi. Trip**. Następnie agregujesz dane do tabeli Spark **nyctaxi. passengercountstats**. Teraz skopiujesz dane z **nyctaxi. passengercountstats** do dedykowanej tabeli puli SQL o nazwie **SQLPOOL1. dbo. passengercountstats**.

Uruchom następującą komórkę w notesie. Kopiuje tabelę zagregowanych danych platformy Spark z powrotem do dedykowanej tabeli puli SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie danych za pomocą puli SQL bezserwerowej](get-started-analyze-sql-on-demand.md)
