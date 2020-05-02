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
ms.openlocfilehash: dd7666bb9f22214fb4701e6be9edc171912d9bf9
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691868"
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
> Zmień pierwszy wiersz zapytania, na przykład [mydbname], tak aby była używana utworzona baza danych. Jeśli baza danych nie została utworzona, zapoznaj się z [konfiguracją pierwszego czasu](query-data-storage.md#first-time-setup). Należy zmienić lokalizację zewnętrznego źródła danych webdatasource, aby wskazywało lokalizację, dla której masz uprawnienia do zapisu. 

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
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
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
