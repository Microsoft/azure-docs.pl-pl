---
title: 'Szybki start: rozpoczynanie analizowania za pomocą platformy Spark'
description: W tym samouczku nauczysz się analizować dane za pomocą Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: de48f906f4dc86bf6297cfb3b76f406df49feec3
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363856"
---
# <a name="analyze-with-apache-spark"></a>Analizowanie za pomocą Apache Spark

W tym samouczku poznasz podstawowe kroki ładowania i analizowania danych za pomocą Apache Spark na Azure Synapse.

## <a name="create-a-serverless-apache-spark-pool"></a>Tworzenie puli Apache Spark serwera

1. W Synapse Studio okienku po lewej stronie wybierz pozycję **Zarządzaj** Apache Spark  >  **pulami.**
1. Wybierz **pozycję Nowy** 
1. W **Apache Spark nazwy puli wprowadź** **Spark1**.
1. W **przypadku rozmiaru węzła** wprowadź wartość Small **(Mały).**
1. Dla **ustawienia Liczba węzłów** ustaw wartość minimalną na 3, a maksymalną na 3
1. Wybierz pozycję **Przeglądanie + tworzenie** > **Utwórz**. Twoja Apache Spark będzie gotowa w ciągu kilku sekund.

## <a name="understanding-serverless-apache-spark-pools"></a>Informacje o pulach Apache Spark bez serwera

Bez serwerowa pula platformy Spark to sposób wskazywania, jak użytkownik chce pracować z platformą Spark. Po rozpoczęciu korzystania z puli w razie potrzeby tworzona jest sesja platformy Spark. Pula kontroluje, ile zasobów platformy Spark będzie używanych przez sesję oraz jak długo sesja będzie trwała, zanim zostanie automatycznie wstrzymana. Płacisz za zasoby platformy Spark używane podczas tej sesji, a nie za samą pulę. Dzięki temu pula platformy Spark umożliwia pracę z platformą Spark bez konieczności martwienia się o zarządzanie klastrami. Jest to podobne do tego, jak działa bez serwera pula SQL.

## <a name="analyze-nyc-taxi-data-with-a-spark-pool"></a>Analizowanie danych dotyczących taksówek w NYC przy użyciu puli Spark

1. W Synapse Studio przejdź do centrum **Opracowywanie**
2. Tworzenie nowego notesu
3. Utwórz nową komórkę kodu i wklej następujący kod do tej komórki.
    ```py
    %%pyspark
    df = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTripSmall.parquet', format='parquet')
    display(df.limit(10))
    ```
1. W notesie w menu **Dołącz do** wybierz utworzoną wcześniej bez serwera pulę **Spark1.**
1. Wybierz **pozycję Uruchom** w komórce. W razie potrzeby synapse rozpocznie nową sesję platformy Spark, aby uruchomić tę komórkę. Jeśli jest potrzebna nowa sesja platformy Spark, początkowo jego utworzenia potrwa około dwóch sekund. 
1. Jeśli chcesz tylko zobaczyć schemat ramki danych, uruchom komórkę z następującym kodem:

    ```py
    %%pyspark
    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Ładowanie danych dotyczących taksówek w NYC do bazy danych Spark nyctaxi

Dane są dostępne za pośrednictwem ramki danych o **nazwie df**. Załaduj go do bazy danych Spark o **nazwie nyctaxi**.

1. Dodaj nową komórkę kodu do notesu, a następnie wprowadź następujący kod:

    ```py
    %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analizowanie danych dotyczących taksówek w NYC przy użyciu platformy Spark i notesów

1. Utwórz nową komórkę kodu i wprowadź następujący kod. 

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Uruchom komórkę, aby wyświetlić dane dotyczące taksówek w Stanie Jorku załadowane do bazy danych **nyctaxi** Spark.
1. Utwórz nową komórkę kodu i wprowadź następujący kod. Przejrzymy te dane i zapiszemy wyniki w tabeli o nazwie **nyctaxi.passengercountstats**.

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

1. W wynikach komórki wybierz **pozycję Wykres,** aby wyświetlić wizualizowane dane.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie danych za pomocą dedykowanej puli SQL](get-started-analyze-sql-pool.md)
