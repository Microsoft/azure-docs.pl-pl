---
title: Korzystanie z metadanych plików w zapytaniach
description: Funkcja OPENROWSET udostępnia informacje o pliku i ścieżce dotyczące każdego pliku użytego w zapytaniu do filtrowania lub analizowania danych na podstawie nazwy pliku i/lub ścieżki folderu.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431555"
---
# <a name="using-file-metadata-in-queries"></a>Korzystanie z metadanych plików w zapytaniach

Usługa zapytań na żądanie SQL może rozdzielić wiele plików i folderów zgodnie z opisem w artykule [foldery zapytań i wiele plików](query-folders-multiple-csv-files.md) . W tym artykule dowiesz się, jak używać informacji metadanych o nazwach plików i folderów w zapytaniach.

Czasami może być konieczne sprawdzenie, który plik lub źródło folderu jest skorelowane z określonym wierszem w zestawie wyników.

Można użyć funkcji `filepath` i `filename` zwrócić nazwy plików i/lub ścieżki w zestawie wyników. Można też użyć ich do filtrowania danych na podstawie nazwy pliku i/lub ścieżki folderu. Te funkcje są opisane w sekcji składnia [Nazwa](develop-storage-files-overview.md#filename-function) i [ścieżka funkcji](develop-storage-files-overview.md#filepath-function). Poniżej znajdziesz krótkie opisy dotyczące przykładów.

## <a name="prerequisites"></a>Wymagania wstępne

Przed przeczytaniem pozostałej części tego artykułu zapoznaj się z następującymi wymaganiami wstępnymi:

- [Konfiguracja pierwszego czasu](query-data-storage.md#first-time-setup)
- [Wymagania wstępne](query-data-storage.md#prerequisites)

## <a name="functions"></a>Funkcje

### <a name="filename"></a>Nazwa pliku

Ta funkcja zwraca nazwę pliku, z którego pochodzi wiersz.

Poniższy przykład odczytuje pliki danych NYC żółtej taksówki dla ostatnich trzech miesięcy 2017 i zwraca liczbę kolarstwu na plik. Część OPENROWSET zapytania określa, które pliki zostaną odczytane.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET') AS [r]
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

Poniższy przykład pokazuje, jak *Nazwa pliku ()* może być używana w klauzuli WHERE do filtrowania plików do odczytu. Uzyskuje dostęp do całego folderu w części OPENROWSET zapytania i filtruje pliki w klauzuli WHERE.

Wyniki będą takie same jak w poprzednim przykładzie.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
    FORMAT='PARQUET') AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.parquet', 'yellow_tripdata_2017-11.parquet', 'yellow_tripdata_2017-12.parquet')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Parametr

Funkcja FilePath zwraca pełną lub częściową ścieżkę:

- Gdy wywoływana bez parametru, zwraca pełną ścieżkę pliku, z której pochodzi wiersz.
- Gdy wywoływana z parametrem, zwraca część ścieżki, która pasuje do symbolu wieloznacznego na pozycji określonej w parametrze. Na przykład wartość parametru 1 zwróci część ścieżki, która pasuje do pierwszego symbolu wieloznacznego.

Poniższy przykład odczytuje NYC żółte pliki danych z taksówką dla ostatnich trzech miesięcy 2017. Zwraca liczbę kolarstwu na ścieżkę pliku. Część OPENROWSET zapytania określa, które pliki zostaną odczytane.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-1*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT,
        pickup_datetime DATETIME2,
        dropoff_datetime DATETIME2,
        passenger_count SMALLINT,
        trip_distance FLOAT,
        rate_code SMALLINT,
        store_and_fwd_flag SMALLINT,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type SMALLINT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

Poniższy przykład pokazuje, jak w klauzuli WHERE można użyć *ścieżki FilePath ()* w celu przefiltrowania plików, które mają być odczytywane.

Możesz użyć symboli wieloznacznych w składniku OPENROWSET zapytania i filtrować pliki w klauzuli WHERE. Wyniki będą takie same jak w poprzednim przykładzie.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_*-*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
WITH (
    vendor_id INT,
    pickup_datetime DATETIME2,
    dropoff_datetime DATETIME2,
    passenger_count SMALLINT,
    trip_distance FLOAT,
    rate_code SMALLINT,
    store_and_fwd_flag SMALLINT,
    pickup_location_id INT,
    dropoff_location_id INT,
    payment_type SMALLINT,
    fare_amount FLOAT,
    extra FLOAT,
    mta_tax FLOAT,
    tip_amount FLOAT,
    tolls_amount FLOAT,
    improvement_surcharge FLOAT,
    total_amount FLOAT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>Następne kroki

W następnym artykule dowiesz się, jak [wykonywać zapytania dotyczące plików Parquet](query-parquet-files.md).
