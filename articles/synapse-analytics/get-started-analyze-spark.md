---
title: 'Szybki Start: Rozpoczynanie analizy przy użyciu platformy Spark'
description: W tym samouczku dowiesz się, jak analizować dane za pomocą Apache Spark
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: d7b198790b1ecc884321ad42c97eb5cf0c239b7e
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241985"
---
# <a name="analyze-with-apache-spark"></a>Analizowanie za pomocą Apache Spark

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analizowanie danych z NYC taksówki w usłudze BLOB Storage przy użyciu platformy Spark

W tym samouczku przedstawiono podstawowe kroki służące do ładowania i analizowania danych za pomocą Apache Spark dla usługi Azure Synapse.

1. W centrum **danych** kliknij pozycję **Dodaj nowy zasób**(plus przycisk powyżej **połączone**)  >> **Przeglądaj przykłady**. 
1. Znajdź **NYCe taksówki & Limousine z Komisją** i kliknij ją. 
1. W dolnej części strony naciśnij pozycję **Kontynuuj** i po tym, aby **dodać zestaw danych**. 
1. Teraz w centrum **danych** w obszarze **połączone** kliknij prawym przyciskiem myszy pozycję **Azure Blob Storage >> przykładowe zestawy danych >> nyc_tlc_yellow** i wybierz pozycję **Nowy Notes**
1. Spowoduje to utworzenie nowego notesu o następującym kodzie:
    ```
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```
1. W notesie wybierz bezserwerową pulę platformy Spark w menu **Dołącz do**
1. Wybierz pozycję **Uruchom** w komórce
1. Jeśli chcesz tylko zobaczyć schemat ramki danych, uruchom komórkę o następującym kodzie:
    ```
    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Ładowanie danych z NYC taksówki do bazy danych Spark nyctaxi

Dostępne są dane w tabeli w **SQLPOOL1**. Załaduj je do bazy danych Spark o nazwie **nyctaxi**.

1. W programie Synapse Studio przejdź do centrum **opracowywania** .
1. Wybierz pozycję **+**  >  **Notes**.
1. W górnej części notesu ustaw wartość **Dołącz do** na **Spark1**.
1. Wybierz pozycję **Dodaj kod** , aby dodać komórkę kodu notesu, a następnie wklej następujący tekst:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Przejdź do centrum **danych** , kliknij prawym przyciskiem myszy pozycję **bazy danych**, a następnie wybierz polecenie **Odśwież**. Powinny zostać wyświetlone następujące bazy danych:

    - **SQLPOOL1 (SQL)**
    - **nyctaxi (Spark)**

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analizowanie danych z NYC taksówki przy użyciu platformy Spark i notesów

1. Wróć do notesu.
1. Utwórz nową komórkę kodu i wprowadź następujący tekst. Następnie uruchom komórkę, aby wyświetlić dane z NYC taksówki załadowane do bazy danych **nyctaxi** Spark.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Uruchom następujący kod, aby wykonać tę samą analizę, która była wcześniej z dedykowaną pulą SQL **SQLPOOL1**. Ten kod zapisuje wyniki analizy w tabeli o nazwie **nyctaxi. passengercountstats** i wizualizowa wyniki.

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

Wcześniej skopiowano dane z tabeli dedykowanej puli SQL **SQLPOOL1. dbo. Trip** do tabeli Spark **nyctaxi. Trip**. Następnie używając platformy Spark, agregujemy dane do tabeli Spark **nyctaxi. passengercountstats**. Teraz skopiujemy dane z **nyctaxi. passengercountstats** do dedykowanej tabeli puli SQL o nazwie **SQLPOOL1. dbo. passengercountstats**.

Uruchom następującą komórkę w notesie. Kopiuje tabelę zagregowanych danych platformy Spark z powrotem do dedykowanej tabeli puli SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie danych za pomocą puli SQL bezserwerowej](get-started-analyze-sql-on-demand.md)


