---
title: Foldery zapytań i wiele plików CSV przy użyciu sql na żądanie (wersja zapoznawcza)
description: SQL na żądanie (wersja zapoznawcza) obsługuje odczytywanie wielu plików/folderów przy użyciu symboli wieloznacznych, które są podobne do symboli wieloznacznych używanych w systemie operacyjnym Windows.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8f8af7fab7113e38b91c3f5f1bcc41b4e4fba2c1
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457369"
---
# <a name="query-folders-and-multiple-csv-files"></a>Foldery kwerend i wiele plików CSV  

W tym artykule dowiesz się, jak napisać kwerendę przy użyciu języka SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics.

SQL na żądanie obsługuje odczytywanie wielu plików/folderów przy użyciu symboli wieloznacznych, które są podobne do symboli wieloznacznych używanych w systemie operacyjnym Windows. Jednak większa elastyczność jest obecny, ponieważ wiele symboli wieloznacznych są dozwolone.

## <a name="prerequisites"></a>Wymagania wstępne

Przed przeczytaniem pozostałej części tego artykułu, upewnij się, że przegląd artykułów wymienionych poniżej:

- [Konfiguracja po raz pierwszy](query-data-storage.md#first-time-setup)
- [Wymagania wstępne](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>Odczyt wielu plików w folderze

Użyjesz folderu *csv/taxi,* aby wykonać przykładowe zapytania. Zawiera dane NYC Taxi - Yellow Taxi Trip Records od lipca 2016 do czerwca 2018.

Pliki w *csv / taxi* są nazwane po roku i miesiącu:

- yellow_tripdata_2016-07.csv
- yellow_tripdata_2016-08.csv
- yellow_tripdata_2016-09.csv
- ...
- yellow_tripdata_2018-04.csv
- yellow_tripdata_2018-05.csv
- yellow_tripdata_2018-06.csv

Każdy plik ma następującą strukturę:
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>Odczyt wszystkich plików w folderze
    
Poniższy przykład odczytuje wszystkie pliki danych NYC Yellow Taxi z folderu *csv/taxi* i zwraca całkowitą liczbę pasażerów i przejazdów rocznie. Pokazuje również użycie funkcji agregujących.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/*.*',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
           trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
           payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Wszystkie pliki dostępne za pomocą jednego zestawu OPENROWSET muszą mieć taką samą strukturę (tj. liczbę kolumn i ich typy danych).

### <a name="read-subset-of-files-in-folder"></a>Odczyt podzbioru plików w folderze

W poniższym przykładzie odczytuje pliki danych 2017 NYC Yellow Taxi z folderu *csv/taxi* przy użyciu symbolu wieloznacznego i zwraca całkowitą kwotę taryfy na typ płatności.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Wszystkie pliki dostępne za pomocą jednego zestawu OPENROWSET muszą mieć taką samą strukturę (tj. liczbę kolumn i ich typy danych).

## <a name="read-folders"></a>Odczyt folderów

Ścieżka udostępnina openrowset może być również ścieżką do folderu. Poniższe sekcje obejmują te typy zapytań.

### <a name="read-all-files-from-specific-folder"></a>Odczytywanie wszystkich plików z określonego folderu

Możesz odczytać wszystkie pliki w folderze przy użyciu symbolu wieloznacznego na poziomie pliku, jak pokazano w [Przeczytaj wszystkie pliki w folderze](#read-all-files-in-folder). Istnieje jednak sposób na zapytanie folderu i wykorzystanie wszystkich plików w tym folderze.

Jeśli ścieżka podana w programie OPENROWSET wskazuje na folder, wszystkie pliki w tym folderze będą używane jako źródło zapytania. Poniższa kwerenda odczyta wszystkie pliki w folderze *csv/taxi.*

> [!NOTE]
> Zwróć uwagę na istnienie / na końcu ścieżki w poniższej kwerendzie. Oznacza folder. Jeśli / zostanie pominięty, kwerenda będzie kierować plik o nazwie *taxi* zamiast.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Wszystkie pliki dostępne za pomocą jednego zestawu OPENROWSET muszą mieć taką samą strukturę (tj. liczbę kolumn i ich typy danych).

### <a name="read-all-files-from-multiple-folders"></a>Odczytywanie wszystkich plików z wielu folderów

Można odczytać pliki z wielu folderów przy użyciu symbolu wieloznacznego. Poniższa kwerenda odczyta wszystkie pliki ze wszystkich folderów znajdujących się w folderze *csv,* które mają nazwy zaczynające się od *t,* a kończące na *i*.

> [!NOTE]
> Zwróć uwagę na istnienie / na końcu ścieżki w poniższej kwerendzie. Oznacza folder. Jeśli / zostanie pominięty, kwerenda będzie kierować pliki o nazwie *t&ast;i* zamiast.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/', 
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Wszystkie pliki dostępne za pomocą jednego zestawu OPENROWSET muszą mieć taką samą strukturę (tj. liczbę kolumn i ich typy danych).

Ponieważ masz tylko jeden folder, który spełnia kryteria, wynik kwerendy jest taki sam jak [Odczyt wszystkich plików w folderze](#read-all-files-in-folder).

## <a name="multiple-wildcards"></a>Wiele symboli wieloznacznych

Można użyć wielu symboli wieloznacznych na różnych poziomach ścieżki. Na przykład można wzbogacić poprzednie zapytanie, aby odczytywać pliki tylko danymi 2017, ze wszystkich folderów, których nazwy zaczynają się od *t,* a kończą na *i*.

> [!NOTE]
> Zwróć uwagę na istnienie / na końcu ścieżki w poniższej kwerendzie. Oznacza folder. Jeśli / zostanie pominięty, kwerenda będzie kierować pliki o nazwie *t&ast;i* zamiast.
> Istnieje maksymalny limit 10 symboli wieloznacznych na kwerendę.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Wszystkie pliki dostępne za pomocą jednego zestawu OPENROWSET muszą mieć taką samą strukturę (tj. liczbę kolumn i ich typy danych).

Ponieważ masz tylko jeden folder, który spełnia kryteria, wynik kwerendy jest taki sam jak [Odczyt podzbiór plików w folderze](#read-subset-of-files-in-folder) i [Przeczytaj wszystkie pliki z określonego folderu](#read-all-files-from-specific-folder). Bardziej złożone scenariusze użycia symboli wieloznacznych są omówione w [plikach parkietu kwerendy](query-parquet-files.md).

## <a name="next-steps"></a>Następne kroki

Więcej informacji można znaleźć w artykule w [programie Query specific files](query-specific-files.md) article.
