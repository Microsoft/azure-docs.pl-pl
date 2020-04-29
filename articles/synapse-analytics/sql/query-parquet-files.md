---
title: Badaj pliki Parquet za pomocą SQL na żądanie (wersja zapoznawcza)
description: W tym artykule dowiesz się, jak wysyłać zapytania o pliki Parquet przy użyciu usługi SQL na żądanie (wersja zapoznawcza).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431698"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Wykonywanie zapytań dotyczących plików Parquet przy użyciu funkcji SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics

W tym artykule dowiesz się, jak napisać zapytanie przy użyciu programu SQL na żądanie (wersja zapoznawcza), które będzie odczytywać pliki Parquet.

## <a name="prerequisites"></a>Wymagania wstępne

Przed przeczytaniem dalszej części tego artykułu zapoznaj się z następującymi artykułami:

- [Konfiguracja pierwszego czasu](query-data-storage.md#first-time-setup)
- [Wymagania wstępne](query-data-storage.md#prerequisites)

## <a name="dataset"></a>Dataset

Pliki Parquet można badać w taki sam sposób jak w przypadku odczytywania plików CSV. Jedyną różnicą jest to, że parametr FILEFORMAT powinien mieć wartość PARQUET. W przykładach w tym artykule przedstawiono szczegółowe informacje dotyczące odczytywania plików Parquet.

> [!NOTE]
> Nie ma potrzeby określania kolumn w klauzuli OPENROWSET WITH podczas odczytywania plików Parquet. SQL na żądanie będzie używać metadanych w pliku Parquet i powiązywać kolumny według nazwy.

Dla przykładowych zapytań będziesz używać folderu *Parquet/taksówki* . Zawiera NYC taksówki — w przypadku każdej z 2016 lipca Podróżje dane. do czerwca 2018.

Dane są partycjonowane według roku i miesiąca, a struktura folderu jest następująca:

- Year = 2016
  - miesiąc = 6
  - ...
  - miesiąc = 12
- rok = 2017
  - miesiąc = 1
  - ...
  - miesiąc = 12
- Year = 2018 r
  - miesiąc = 1
  - ...
  - miesiąc = 6

## <a name="query-set-of-parquet-files"></a>Zestaw zapytań o pliki Parquet

Podczas wykonywania zapytania dotyczącego plików Parquet można określić tylko kolumny zainteresowania.

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

Nie musisz używać klauzuli OPENROWSET WITH podczas odczytywania plików Parquet. Nazwy kolumn i typy danych są automatycznie odczytywane z plików Parquet.

W poniższym przykładzie przedstawiono możliwości automatycznego wnioskowania schematu dla plików Parquet. Zwraca liczbę wierszy we wrześniu 2017 bez określania schematu.

> [!NOTE]
> Nie musisz określać kolumn w klauzuli OPENROWSET WITH podczas odczytywania plików Parquet. W takim przypadku usługa zapytań SQL na żądanie będzie używać metadanych w pliku Parquet i powiązywać kolumny według nazwy.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>Wykonywanie zapytań względem danych partycjonowanych

Zestaw danych podany w tym przykładzie jest podzielony (podzielony na partycje) na oddzielne podfoldery. Można wskazać określone partycje przy użyciu funkcji FilePath. Ten przykład pokazuje opłaty za stawki za rok, miesiąc i payment_type przez pierwsze trzy miesiące 2017.

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

## <a name="type-mapping"></a>Mapowanie typu

Pliki Parquet zawierają opisy typów dla każdej kolumny. W poniższej tabeli opisano, jak typy Parquet są mapowane na typy natywne języka SQL.

| Typ Parquet | Parquet — typ logiczny (Adnotacja) | Typ danych SQL |
| --- | --- | --- |
| TYPU | | bit |
| DANE BINARNE/BYTE_ARRAY | | varbinary |
| DOUBLE | | float |
| FLOAT | | liczba rzeczywista |
| ELEMENTEM | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binarny |
| BINARNY |KODOWANIA |varchar \*(sortowanie UTF8) |
| BINARNY |PARAMETRY |varchar \*(sortowanie UTF8) |
| BINARNY |PODSTAWOWE|varchar \*(sortowanie UTF8) |
| BINARNY |INTERFEJSU |uniqueidentifier |
| BINARNY |DOKŁADNOŚCI |decimal |
| BINARNY |JSON |varchar (max) \*(sortowanie UTF8) |
| BINARNY |BSON |varbinary (max) |
| FIXED_LEN_BYTE_ARRAY |DOKŁADNOŚCI |decimal |
| BYTE_ARRAY |DAT |varchar (max), serializacji do formatu standardowego |
| ELEMENTEM |INT (8, prawda) |smallint |
| ELEMENTEM |INT (16, true) |smallint |
| ELEMENTEM |INT (32, true) |int |
| ELEMENTEM |INT (8, FAŁSZ) |tinyint |
| ELEMENTEM |INT (16, FAŁSZ) |int |
| ELEMENTEM |INT (32, false) |bigint |
| ELEMENTEM |DATE |date |
| ELEMENTEM |DOKŁADNOŚCI |decimal |
| ELEMENTEM |CZAS (MŁYNER)|time |
| INT64 |INT (64, true) |bigint |
| INT64 |INT (64, false) |Liczba dziesiętna (20, 0) |
| INT64 |DOKŁADNOŚCI |decimal |
| INT64 |TIME (MICROS/NANOS) |time |
|INT64 |SYGNATURA CZASOWA (MILL/MICROS/NANOS) |datetime2 |
|[Typ złożony](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |STAW |varchar (max), serializacja do formatu JSON |
|[Typ złożony](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|ZMAPOWAĆ|varchar (max), serializacja do formatu JSON |

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak [wykonywać zapytania dotyczące zagnieżdżonych typów Parquet](query-parquet-nested-types.md).
