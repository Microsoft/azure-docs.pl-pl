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
ms.reviewer: jrasnick
ms.openlocfilehash: 2e00ecd6048239683951a2d1e60d3bcb0eb5aa68
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242436"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Wykonywanie zapytań dotyczących plików Parquet przy użyciu funkcji SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics

W tym artykule dowiesz się, jak napisać zapytanie przy użyciu programu SQL na żądanie (wersja zapoznawcza), które będzie odczytywać pliki Parquet.

## <a name="quickstart-example"></a>Przykład szybkiego startu

`OPENROWSET` funkcja umożliwia odczytywanie zawartości pliku Parquet, podając adres URL pliku.

### <a name="read-parquet-file"></a>Odczytaj plik Parquet

Najprostszym sposobem, aby zobaczyć zawartość `PARQUET` pliku, jest podanie adresu URL pliku do `OPENROWSET` działania i określenie Parquet `FORMAT` . Jeśli plik jest publicznie dostępny lub jeśli tożsamość usługi Azure AD ma dostęp do tego pliku, powinna być widoczna zawartość pliku przy użyciu zapytania, jak pokazano w następującym przykładzie:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet',
    format = 'parquet') as rows
```

Upewnij się, że masz dostęp do tego pliku. Jeśli plik jest chroniony za pomocą klucza SAS lub niestandardowej tożsamości platformy Azure, konieczne będzie skonfigurowanie [poświadczeń na poziomie serwera dla logowania SQL](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential).

### <a name="data-source-usage"></a>Użycie źródła danych

Poprzedni przykład używa pełnej ścieżki do pliku. Alternatywnie można utworzyć zewnętrzne źródło danych z lokalizacją wskazującą folder główny magazynu, a następnie użyć tego źródła danych i ścieżki względnej do pliku w `OPENROWSET` funkcji:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) as rows
```

Jeśli źródło danych jest chronione za pomocą klucza SAS lub tożsamości niestandardowej, można skonfigurować [Źródło danych z poświadczeniami o zakresie bazy danych](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

### <a name="explicitly-specify-schema"></a>Jawnie określ schemat

`OPENROWSET` umożliwia jawne Określanie kolumn, które mają być odczytywane z klauzuli File using `WITH` :

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) with ( date_rep date, cases int, geo_id varchar(6) ) as rows
```

W poniższych sekcjach można zobaczyć, jak wykonywać zapytania dotyczące różnych typów plików PARQUET.

## <a name="prerequisites"></a>Wymagania wstępne

Pierwszym krokiem jest **utworzenie bazy danych** ze źródłem danych, która odwołuje się do konta magazynu [NYC Yellow](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) . Następnie zainicjuj obiekty, wykonując [skrypt Instalatora](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) w tej bazie danych. Ten skrypt instalacyjny spowoduje utworzenie źródeł danych, poświadczeń z zakresem bazy danych i zewnętrznych formatów plików, które są używane w tych przykładach.

## <a name="dataset"></a>Zestaw danych

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

W przypadku mapowania typu Parquet na typ natywny Sprawdź mapowanie typu SQL [dla Parquet](develop-openrowset.md#type-mapping-for-parquet).

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak [wykonywać zapytania dotyczące zagnieżdżonych typów Parquet](query-parquet-nested-types.md).
