---
title: Wysyłanie zapytań do folderów i wielu plików przy użyciu puli SQL bezserwerowej
description: Pula SQL bezserwerowa obsługuje odczytywanie wielu plików/folderów przy użyciu symboli wieloznacznych, które są podobne do symboli wieloznacznych używanych w systemie operacyjnym Windows.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 83c4d88e1a87f6b546e26dd55da338a36f16ebe4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462627"
---
# <a name="query-folders-and-multiple-files"></a>Wykonywanie zapytań względem folderów i wielu plików  

W tym artykule dowiesz się, jak napisać zapytanie przy użyciu bezserwerowej puli SQL w usłudze Azure Synapse Analytics.

Pula SQL bezserwerowa obsługuje odczytywanie wielu plików/folderów przy użyciu symboli wieloznacznych, które są podobne do symboli wieloznacznych używanych w systemie operacyjnym Windows. Jednak większa elastyczność jest obecna, ponieważ wiele symboli wieloznacznych jest dozwolonych.

## <a name="prerequisites"></a>Wymagania wstępne

Pierwszym krokiem jest **utworzenie bazy danych** , w której będą wykonywane zapytania. Następnie zainicjuj obiekty, wykonując [skrypt Instalatora](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) w tej bazie danych. Ten skrypt instalacyjny spowoduje utworzenie źródeł danych, poświadczeń z zakresem bazy danych i zewnętrznych formatów plików, które są używane w tych przykładach.

Użyjesz folderu *CSV/taksówki* do wykonania przykładowych zapytań. Zawiera NYCe taksówki — w przypadku podróży z listopada są rejestrowane dane z lipca 2016 do 2018 czerwca. Pliki w *formacie CSV/taksówki* są nazwane po roku i miesiącu przy użyciu następującego wzorca: yellow_tripdata_ <year> - <month> . csv

## <a name="read-all-files-in-folder"></a>Odczytaj wszystkie pliki w folderze

W poniższym przykładzie są odczytywane wszystkie pliki danych NYC żółtej taksówki z folderu *CSV/taksówki* i zwracana jest łączna liczba pasażerów i kolarstwu rocznie. Pokazuje także użycie funkcji agregujących.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        pickup_datetime DATETIME2 2, 
        passenger_count INT 4
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
        BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        payment_type INT 10,
        fare_amount FLOAT 11
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Wszystkie pliki z pojedynczym OPENROWSET muszą mieć tę samą strukturę (tj. liczbę kolumn i ich typy danych).

## <a name="read-folders"></a>Odczytuj foldery

Ścieżka do funkcji OPENROWSET może być również ścieżką do folderu. Poniższe sekcje zawierają te typy zapytań.

### <a name="read-all-files-from-specific-folder"></a>Odczytaj wszystkie pliki z określonego folderu

Wszystkie pliki w folderze można odczytać za pomocą symbolu wieloznacznego poziomu pliku, jak pokazano w [Odczytaj wszystkie pliki w folderze](#read-all-files-in-folder). Istnieje jednak możliwość wykonywania zapytań względem folderu i używania wszystkich plików znajdujących się w tym folderze.

Jeśli ścieżka podana w polu OPENROWSET wskazuje folder, wszystkie pliki w tym folderze będą używane jako źródło zapytania. Następujące zapytanie odczytaje wszystkie pliki w folderze *CSV/taksówki* .

> [!NOTE]
> Zwróć uwagę na obecność/na końcu ścieżki w zapytaniu poniżej. Oznacza folder. Jeśli parametr/zostanie pominięty, zapytanie będzie ukierunkowane na plik o nazwie *taksówki* .

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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
> Zwróć uwagę na obecność/na końcu ścieżki w zapytaniu poniżej. Oznacza folder. Jeśli parametr/zostanie pominięty, zapytanie będzie miało pliki docelowe o nazwie *t &ast; i* .

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

## <a name="traverse-folders-recursively"></a>Cyklicznie przechodzenie między folderami

Bezserwerowa Pula SQL może przechodzenie między folderami cyklicznie, jeśli na końcu ścieżki określono/* *. Następujące zapytanie odczytaje wszystkie pliki ze wszystkich folderów i podfolderów znajdujących się w folderze *CSV* .

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/**', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

## <a name="multiple-wildcards"></a>Wiele symboli wieloznacznych

Można użyć wielu symboli wieloznacznych na różnych poziomach ścieżki. Na przykład możesz wzbogacić poprzednie zapytanie, aby odczytywać pliki tylko z 2017 danymi, ze wszystkich folderów, których nazwy zaczynają się od *t* i kończą z *i*.

> [!NOTE]
> Zwróć uwagę na obecność/na końcu ścieżki w zapytaniu poniżej. Oznacza folder. Jeśli parametr/zostanie pominięty, zapytanie będzie miało pliki docelowe o nazwie *t &ast; i* .
> Istnieje maksymalny limit 10 symboli wieloznacznych na zapytanie.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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
