---
title: Przechowywanie wyników kwerend do magazynu
description: W tym artykule dowiesz się, jak przechowywać wyniki kwerend do magazynu przy użyciu sql na żądanie (wersja zapoznawcza).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 462185feb2b9cbebd17ce9cba54c2b23deea6c75
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425147"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Przechowywanie wyników kwerend do magazynu przy użyciu sql na żądanie (wersja zapoznawcza) przy użyciu usługi Azure Synapse Analytics

W tym artykule dowiesz się, jak przechowywać wyniki kwerend do magazynu przy użyciu sql on-demand (wersja zapoznawcza).

## <a name="prerequisites"></a>Wymagania wstępne

Pierwszym krokiem jest przejrzenie poniższych artykułów i upewnienie się, że spełniasz wymagania wstępne:

- [Konfiguracja po raz pierwszy](query-data-storage.md#first-time-setup)
- [Wymagania wstępne](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>Tworzenie tabeli zewnętrznej w miarę wyboru

Instrukcji CREATE EXTERNAL TABLE AS SELECT (CETAS) można użyć do przechowywania wyników kwerendy w magazynie.

> [!NOTE]
> Zmień pierwszy wiersz w kwerendzie, czyli [mydbname], aby użyć utworzonej bazy danych. Jeśli baza danych nie została utworzona, przeczytaj artykuł [Pierwsza konfiguracja](query-data-storage.md#first-time-setup).

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

## <a name="use-a-external-table-created"></a>Korzystanie z utworzonej tabeli zewnętrznej

Można użyć tabeli zewnętrznej utworzonej za pośrednictwem CETAS jak zwykłej tabeli zewnętrznej.

> [!NOTE]
> Zmień pierwszy wiersz w kwerendzie, czyli [mydbname], aby użyć utworzonej bazy danych. Jeśli baza danych nie została utworzona, przeczytaj artykuł [Pierwsza konfiguracja](query-data-storage.md#first-time-setup).

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

Aby uzyskać informacje na temat sposobu wykonywania zapytań o różne typy plików, zobacz [pojedyncze pliki CSV kwerendy,](query-single-csv-file.md) [pliki parkietu kwerendy](query-parquet-files.md)i artykuły [z plikami JSON kwerendy.](query-json-files.md)
