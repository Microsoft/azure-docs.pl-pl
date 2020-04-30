---
title: Przechowywanie wyników zapytania w magazynie
description: W tym artykule dowiesz się, jak przechowywać wyniki zapytania do magazynu przy użyciu usługi SQL na żądanie (wersja zapoznawcza).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 462185feb2b9cbebd17ce9cba54c2b23deea6c75
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81425147"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Przechowywanie wyników zapytania w magazynie przy użyciu funkcji SQL na żądanie (wersja zapoznawcza) przy użyciu usługi Azure Synapse Analytics

W tym artykule dowiesz się, jak przechowywać wyniki zapytania do magazynu przy użyciu usługi SQL na żądanie (wersja zapoznawcza).

## <a name="prerequisites"></a>Wymagania wstępne

Pierwszym krokiem jest zapoznanie się z artykułami poniżej i upewnienie się, że spełniono wymagania wstępne:

- [Konfiguracja pierwszego czasu](query-data-storage.md#first-time-setup)
- [Wymagania wstępne](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>Utwórz tabelę zewnętrzną jako wybraną

Możesz użyć instrukcji CREATE EXTERNAL TABLE AS SELECT (CETAS), aby przechowywać wyniki zapytania do magazynu.

> [!NOTE]
> Zmień pierwszy wiersz zapytania, na przykład [mydbname], tak aby była używana utworzona baza danych. Jeśli baza danych nie została utworzona, zapoznaj się z [konfiguracją pierwszego czasu](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://showdemoweu.dfs.core.windows.net/data/population_csv/population.csv',
        FORMAT='CSV'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-a-external-table-created"></a>Użyj utworzonej tabeli zewnętrznej

Możesz użyć tabeli zewnętrznej utworzonej za pomocą CETAS, jak zwykła tabela zewnętrzna.

> [!NOTE]
> Zmień pierwszy wiersz zapytania, na przykład [mydbname], tak aby była używana utworzona baza danych. Jeśli baza danych nie została utworzona, zapoznaj się z [konfiguracją pierwszego czasu](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat wykonywania zapytań o różne typy plików, zapoznaj się z artykułami [Single CSV pliku](query-single-csv-file.md), [Query Parquet Files](query-parquet-files.md)i [plików zapytania JSON](query-json-files.md) .
