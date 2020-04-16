---
title: Pliki parkietu kwerend przy użyciu sql na żądanie (wersja zapoznawcza)
description: W tym artykule dowiesz się, jak wysyłać zapytania do plików parkietu przy użyciu języka SQL na żądanie (wersja zapoznawcza).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431698"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Pliki parkietu zapytań przy użyciu języka SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics

W tym artykule dowiesz się, jak napisać kwerendę przy użyciu języka SQL na żądanie (wersja zapoznawcza), która będzie odczytywać pliki parkietu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed przeczytaniem pozostałej części tego artykułu, zapoznaj się z następującymi artykułami:

- [Konfiguracja po raz pierwszy](query-data-storage.md#first-time-setup)
- [Wymagania wstępne](query-data-storage.md#prerequisites)

## <a name="dataset"></a>Dataset

Możesz wysyłać zapytania do plików Parkietu w taki sam sposób, jak odczytywane pliki CSV. Jedyną różnicą jest to, że parametr FILEFORMAT powinien być ustawiony na PARKIET. Przykłady w tym artykule pokazują specyfikę czytania plików parkietu.

> [!NOTE]
> Podczas odczytywania plików parkietu nie trzeba określać kolumn w klauzuli OPENROWSET WITH. SQL na żądanie będzie wykorzystywać metadane w pliku Parkiet i kolumny powiązania według nazwy.

Użyjesz *folderu parkiet/taksówka* dla przykładowych zapytań. Zawiera dane NYC Taxi - Yellow Taxi Trip Records z lipca 2016. do czerwca 2018 r.

Dane są podzielone według roku i miesiąca, a struktura folderów jest następująca:

- rok=2016
  - miesiąc=6
  - ...
  - miesiąc=12
- rok=2017
  - miesiąc=1
  - ...
  - miesiąc=12
- rok=2018
  - miesiąc=1
  - ...
  - miesiąc=6

## <a name="query-set-of-parquet-files"></a>Zestaw zapytań plików parkietu

Podczas wykonywania kwerendy w plikach parkietu można określić tylko kolumny będące przedmiotem zainteresowania.

```sql
SELECT
        YEAR(pickup_datetime),
        passenger_count,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) WITH (
        pickup_datetime DATETIME2,
        passenger_count INT
    ) AS nyc
GROUP BY
    passenger_count,
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime),
    passenger_count;
```

## <a name="automatic-schema-inference"></a>Automatyczne wnioskowanie schematu

Nie trzeba używać openrowset with klauzuli podczas odczytywania plików Parkietu. Nazwy kolumn i typy danych są automatycznie odczytywane z plików parkietu.

W poniższym przykładzie przedstawiono możliwości wnioskowania schematu automatycznego dla plików parkietu. Zwraca liczbę wierszy we wrześniu 2017 r. bez określania schematu.

> [!NOTE]
> Podczas odczytywania plików parkietu nie trzeba określać kolumn w klauzuli OPENROWSET WITH. W takim przypadku usługa sql on-demand Query będzie wykorzystywać metadane w pliku Parkiet i powiązać kolumny według nazwy.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>Zapytanie o dane podzielone na partycje

Zestaw danych podany w tym przykładzie jest podzielony (podzielony na partycje) na oddzielne podfoldery. Można kierować określone partycje za pomocą funkcji ścieżki pliku. W tym przykładzie przedstawiono kwoty taryf według roku, miesiąca i payment_type w ciągu pierwszych trzech miesięcy 2017 r.

> [!NOTE]
> Zapytanie SQL na żądanie jest zgodne ze schematem partycjonowania Hive/Hadoop.

```sql
SELECT
    nyc.filepath(1) AS [year],
    nyc.filepath(2) AS [month],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND pickup_datetime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type
ORDER BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type;
```

## <a name="type-mapping"></a>Mapowanie typów

Pliki parkietu zawierają opisy typów dla każdej kolumny. W poniższej tabeli opisano sposób mapowania typów parkietów na typy natywne SQL.

| Typ parkietu | Typ logiczny parkietu (adnotacja) | Typ danych SQL |
| --- | --- | --- |
| Boolean | | bit |
| BINARNE / BYTE_ARRAY | | varbinary |
| Podwójne | | float |
| Float | | rzeczywiste |
| INT32 (WT32) | | int |
| INT64 | | bigint |
| Int96 (wład. | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binarny |
| Binarnym |UtF8 (polski) |varchar \*(sortowanie UTF8) |
| Binarnym |Ciąg |varchar \*(sortowanie UTF8) |
| Binarnym |Enum|varchar \*(sortowanie UTF8) |
| Binarnym |Uuid |uniqueidentifier |
| Binarnym |Dziesiętnych |decimal |
| Binarnym |JSON |varchar(maks) \*(sortowanie UTF8) |
| Binarnym |Bson |Varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |Dziesiętnych |decimal |
| BYTE_ARRAY |Interwał |varchar(max), serializowane w formacie standardowym |
| INT32 (WT32) |INT(8, prawda) |smallint |
| INT32 (WT32) |INT(16, prawda) |smallint |
| INT32 (WT32) |INT(32, prawda) |int |
| INT32 (WT32) |INT(8, fałsz) |tinyint |
| INT32 (WT32) |INT(16, fałsz) |int |
| INT32 (WT32) |INT(32, fałsz) |bigint |
| INT32 (WT32) |DATE |date |
| INT32 (WT32) |Dziesiętnych |decimal |
| INT32 (WT32) |CZAS (MILLIS )|time |
| INT64 |INT(64, prawda) |bigint |
| INT64 |INT(64, false ) |dziesiętne(20,0) |
| INT64 |Dziesiętnych |decimal |
| INT64 |CZAS (MICROS / NANOS) |time |
|INT64 |SYGNATURA CZASOWA (MILLIS / MICROS / NANOS) |datetime2 |
|[Typ złożony](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |Listy |varchar(max), serializowane w JSON |
|[Typ złożony](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|Mapę|varchar(max), serializowane w JSON |

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak [zapytać o typy zagnieżdżone parkietu](query-parquet-nested-types.md).
