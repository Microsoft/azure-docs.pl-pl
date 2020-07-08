---
title: Badaj pliki Parquet za pomocą SQL na żądanie (wersja zapoznawcza)
description: W tym artykule dowiesz się, jak wysyłać zapytania o pliki Parquet przy użyciu usługi SQL na żądanie (wersja zapoznawcza).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 4bab1ef4588a705f0dd6cdb34be8272868f826e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85207570"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Wykonywanie zapytań dotyczących plików Parquet przy użyciu funkcji SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics

W tym artykule dowiesz się, jak napisać zapytanie przy użyciu programu SQL na żądanie (wersja zapoznawcza), które będzie odczytywać pliki Parquet.

## <a name="prerequisites"></a>Wymagania wstępne

Pierwszym krokiem jest **utworzenie bazy danych** ze źródłem danych, która odwołuje się do konta magazynu [NYC Yellow](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) . Następnie zainicjuj obiekty, wykonując [skrypt Instalatora](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) w tej bazie danych. Ten skrypt instalacyjny spowoduje utworzenie źródeł danych, poświadczeń z zakresem bazy danych i zewnętrznych formatów plików, które są używane w tych przykładach.

## <a name="dataset"></a>Dataset

W tym przykładzie jest używany [NYC żółtej taksówki](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) . Pliki Parquet można badać w taki sam sposób jak w przypadku [odczytywania plików CSV](query-parquet-files.md). Jedyną różnicą jest to, że `FILEFORMAT` parametr powinien zostać ustawiony na `PARQUET` . W przykładach w tym artykule przedstawiono szczegółowe informacje dotyczące odczytywania plików Parquet.

## <a name="query-set-of-parquet-files"></a>Zestaw zapytań o pliki Parquet

Podczas wykonywania zapytania dotyczącego plików Parquet można określić tylko kolumny zainteresowania.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>Automatyczne wnioskowanie schematu

Nie musisz używać klauzuli OPENROWSET WITH podczas odczytywania plików Parquet. Nazwy kolumn i typy danych są automatycznie odczytywane z plików Parquet.

W poniższym przykładzie przedstawiono możliwości automatycznego wnioskowania schematu dla plików Parquet. Zwraca liczbę wierszy we wrześniu 2017 bez określania schematu.

> [!NOTE]
> Nie musisz określać kolumn w klauzuli OPENROWSET WITH podczas odczytywania plików Parquet. W takim przypadku usługa zapytań SQL na żądanie będzie używać metadanych w pliku Parquet i powiązywać kolumny według nazwy.

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>Wykonywanie zapytań względem danych partycjonowanych

Zestaw danych podany w tym przykładzie jest podzielony (podzielony na partycje) na oddzielne podfoldery. Można wskazać określone partycje przy użyciu funkcji FilePath. Ten przykład pokazuje opłaty za stawki za rok, miesiąc i payment_type przez pierwsze trzy miesiące 2017.

> [!NOTE]
> Zapytanie SQL na żądanie jest zgodne ze schematem partycjonowania Hive/Hadoop.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
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
| BINARNY |UTF8 |varchar \* (sortowanie UTF8) |
| BINARNY |PARAMETRY |varchar \* (sortowanie UTF8) |
| BINARNY |PODSTAWOWE|varchar \* (sortowanie UTF8) |
| BINARNY |INTERFEJSU |uniqueidentifier |
| BINARNY |DOKŁADNOŚCI |decimal |
| BINARNY |JSON |varchar (max) \* (sortowanie UTF8) |
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
