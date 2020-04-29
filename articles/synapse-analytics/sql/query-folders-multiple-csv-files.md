---
title: Wysyłanie zapytań do folderów i wielu plików CSV za pomocą SQL na żądanie (wersja zapoznawcza)
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457369"
---
# <a name="query-folders-and-multiple-csv-files"></a>Foldery zapytań i wiele plików CSV  

W tym artykule dowiesz się, jak napisać zapytanie przy użyciu języka SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics.

SQL na żądanie obsługuje odczytywanie wielu plików/folderów przy użyciu symboli wieloznacznych, które są podobne do symboli wieloznacznych używanych w systemie operacyjnym Windows. Jednak większa elastyczność jest obecna, ponieważ wiele symboli wieloznacznych jest dozwolonych.

## <a name="prerequisites"></a>Wymagania wstępne

Przed zainstalowaniem pozostałej części tego artykułu Pamiętaj, aby przejrzeć artykuły wymienione poniżej:

- [Konfiguracja pierwszego czasu](query-data-storage.md#first-time-setup)
- [Wymagania wstępne](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>Odczytaj wiele plików w folderze

Użyjesz folderu *CSV/taksówki* do wykonania przykładowych zapytań. Zawiera NYCe taksówki — w przypadku podróży z listopada są rejestrowane dane z lipca 2016 do 2018 czerwca.

Pliki w *formacie CSV/taksówki* są nazwane po roku i miesiącu:

- yellow_tripdata_2016 -07. csv
- yellow_tripdata_2016 -08. csv
- yellow_tripdata_2016 -09. csv
- ...
- yellow_tripdata_2018 -04. csv
- yellow_tripdata_2018 -05. csv
- yellow_tripdata_2018 -06. csv

Każdy plik ma następującą strukturę:
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>Odczytaj wszystkie pliki w folderze
    
W poniższym przykładzie są odczytywane wszystkie pliki danych NYC żółtej taksówki z folderu *CSV/taksówki* i zwracana jest łączna liczba pasażerów i kolarstwu rocznie. Pokazuje także użycie funkcji agregujących.

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
> Wszystkie pliki z pojedynczym OPENROWSET muszą mieć tę samą strukturę (tj. liczbę kolumn i ich typy danych).

### <a name="read-subset-of-files-in-folder"></a>Odczytaj podzestaw plików w folderze

Poniższy przykład 2017 odczytuje pliki danych NYC żółtej taksówki z folderu *CSV/taksówki* przy użyciu symbolu wieloznacznego i zwraca łączną kwotę opłaty za opłatę za typ płatności.

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
> Wszystkie pliki z pojedynczym OPENROWSET muszą mieć tę samą strukturę (tj. liczbę kolumn i ich typy danych).

## <a name="read-folders"></a>Odczytuj foldery

Ścieżka do funkcji OPENROWSET może być również ścieżką do folderu. Poniższe sekcje zawierają te typy zapytań.

### <a name="read-all-files-from-specific-folder"></a>Odczytaj wszystkie pliki z określonego folderu

Wszystkie pliki w folderze można odczytać za pomocą symbolu wieloznacznego poziomu pliku, jak pokazano w [Odczytaj wszystkie pliki w folderze](#read-all-files-in-folder). Istnieje jednak możliwość tworzenia zapytań względem folderu i używania wszystkich plików w tym folderze.

Jeśli ścieżka podana w polu OPENROWSET wskazuje folder, wszystkie pliki w tym folderze będą używane jako źródło zapytania. Następujące zapytanie odczytaje wszystkie pliki w folderze *CSV/taksówki* .

> [!NOTE]
> Zwróć uwagę na obecność/na końcu ścieżki w zapytaniu poniżej. Oznacza folder. Jeśli parametr/zostanie pominięty, zapytanie będzie ukierunkowane na plik o nazwie *taksówki* .

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
> Wszystkie pliki z pojedynczym OPENROWSET muszą mieć tę samą strukturę (tj. liczbę kolumn i ich typy danych).

### <a name="read-all-files-from-multiple-folders"></a>Odczytaj wszystkie pliki z wielu folderów

Istnieje możliwość odczytywania plików z wielu folderów przy użyciu symbolu wieloznacznego. Następujące zapytanie odczytaje wszystkie pliki ze wszystkich folderów znajdujących się w folderze *CSV* o nazwach zaczynających się od *t* i kończące *się na i.*

> [!NOTE]
> Zwróć uwagę na obecność/na końcu ścieżki w zapytaniu poniżej. Oznacza folder. Jeśli parametr/zostanie pominięty, zapytanie będzie miało pliki docelowe o nazwie *t&ast;i* .

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
> Wszystkie pliki z pojedynczym OPENROWSET muszą mieć tę samą strukturę (tj. liczbę kolumn i ich typy danych).

Ponieważ istnieje tylko jeden folder pasujący do kryteriów, wynik zapytania jest taki sam jak [Odczyt wszystkich plików w folderze](#read-all-files-in-folder).

## <a name="multiple-wildcards"></a>Wiele symboli wieloznacznych

Można użyć wielu symboli wieloznacznych na różnych poziomach ścieżki. Na przykład możesz wzbogacić poprzednie zapytanie, aby odczytywać pliki tylko z 2017 danymi, ze wszystkich folderów, których nazwy zaczynają się od *t* i kończą z *i*.

> [!NOTE]
> Zwróć uwagę na obecność/na końcu ścieżki w zapytaniu poniżej. Oznacza folder. Jeśli parametr/zostanie pominięty, zapytanie będzie miało pliki docelowe o nazwie *t&ast;i* .
> Istnieje maksymalny limit 10 symboli wieloznacznych na zapytanie.

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
> Wszystkie pliki z pojedynczym OPENROWSET muszą mieć tę samą strukturę (tj. liczbę kolumn i ich typy danych).

Ponieważ istnieje tylko jeden folder pasujący do kryteriów, wynik zapytania jest taki sam jak [Odczyt podzestawu plików w folderze](#read-subset-of-files-in-folder) i [odczytywanie wszystkich plików z określonego folderu](#read-all-files-from-specific-folder). Bardziej złożone scenariusze użycia symboli wieloznacznych są omówione w [plikach Parquet zapytań](query-parquet-files.md).

## <a name="next-steps"></a>Następne kroki

Więcej informacji można znaleźć w artykule [dotyczącym określonych plików zapytania](query-specific-files.md) .
