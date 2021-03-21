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
ms.openlocfilehash: 8559bd0a354a64872e58d014d1027ed971773b60
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655346"
---
# <a name="analyze-with-apache-spark"></a>Analizowanie za pomocą Apache Spark

W tym samouczku przedstawiono podstawowe kroki służące do ładowania i analizowania danych za pomocą Apache Spark dla usługi Azure Synapse.

## <a name="create-a-serverless-apache-spark-pool"></a>Utwórz bezserwerową pulę Apache Spark

1. W programie Synapse Studio w okienku po lewej stronie wybierz pozycję **Zarządzaj**  >  **pulami Apache Spark**.
1. Wybierz pozycję **Nowy** 
1. W obszarze **Nazwa puli Apache Spark** wprowadź **Spark1**.
1. Dla **rozmiaru węzła** wprowadź **małe**.
1. Dla **liczby węzłów** ustaw wartość minimalną na 3 i wartość maksymalną na 3.
1. Wybierz pozycję **Przeglądanie + tworzenie** > **Utwórz**. Pula Apache Spark będzie gotowa w ciągu kilku sekund.

## <a name="understanding-serverless-apache-spark-pools"></a>Informacje o pulach Apache Spark bezserwerowych

Bezserwerowa Pula platformy Spark jest sposobem wskazywania, w jaki sposób użytkownik chce współpracować z platformą Spark. Po rozpoczęciu korzystania z puli w razie konieczności tworzona jest sesja Spark. Pula kontroluje, ile zasobów platformy Spark będzie używanych przez daną sesję, oraz czas, przez jaki sesja była przed chwilą wstrzymywana automatycznie. Płacisz za zasoby platformy Spark używane podczas tej sesji, a nie dla samej puli. W ten sposób pula platformy Spark umożliwia współpracę z platformą Spark, bez konieczności zarządzania klastrami. Jest to podobne do tego, jak działa Pula SQL bezserwerowa.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analizowanie danych z NYC taksówki w usłudze BLOB Storage przy użyciu platformy Spark

1. W programie Synapse Studio przejdź do centrum **opracowywania**
2. Utwórz newnNotebook z domyślnym językiem ustawionym na **PySpark (Python)**.
3. Utwórz nową komórkę kodu i wklej poniższy kod do tej komórki.
    ```
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    df = data.to_spark_dataframe()
    # Display 10 rows
    display(df.limit(10))
    ```
1. W notesie w menu **Dołącz do** wybierz **Spark1** pulę platformy Spark bezserwerową utworzoną wcześniej.
1. Wybierz pozycję **Uruchom** w komórce. Synapse rozpocznie nową sesję platformy Spark, aby uruchomić tę komórkę w razie potrzeby. Jeśli jest wymagana Nowa sesja platformy Spark, początkowo trwa około dwóch sekund. 
1. Jeśli chcesz tylko zobaczyć schemat ramki danych, uruchom komórkę o następującym kodzie:

    ```py
    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Ładowanie danych z NYC taksówki do bazy danych Spark nyctaxi

Dane są dostępne za pośrednictwem Dataframe o nazwie **Data**. Załaduj je do bazy danych Spark o nazwie **nyctaxi**.

1. Dodaj nowy element do notesu, a następnie wprowadź następujący kod:

    ```py
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```
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


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie danych za pomocą dedykowanej puli SQL](get-started-analyze-sql-pool.md)
