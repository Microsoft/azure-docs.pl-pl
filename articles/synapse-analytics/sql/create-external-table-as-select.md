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
ms.openlocfilehash: cbf6d42f3b1d130a6bf89f07bd3a7009ff0e8fa8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647523"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Przechowywanie wyników zapytania w magazynie przy użyciu funkcji SQL na żądanie (wersja zapoznawcza) przy użyciu usługi Azure Synapse Analytics

W tym artykule dowiesz się, jak przechowywać wyniki zapytania do magazynu przy użyciu usługi SQL na żądanie (wersja zapoznawcza).

## <a name="prerequisites"></a>Wymagania wstępne

Pierwszym krokiem jest zapoznanie się z artykułami poniżej i upewnienie się, że spełniono wymagania wstępne:

- [Konfiguracja pierwszego czasu](query-data-storage.md#first-time-setup)
- [Wymagania wstępne](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>Utwórz tabelę zewnętrzną jako wybraną

Można użyć instrukcji CREATE EXTERNAL TABLE AS SELECT (CETAS), aby przechowywać wyniki zapytania w magazynie.

> [!NOTE]
> Zmień pierwszy wiersz zapytania, na przykład [mydbname], tak aby była używana utworzona baza danych. Jeśli baza danych nie została utworzona, zapoznaj się z [konfiguracją pierwszego czasu](query-data-storage.md#first-time-setup). Należy zmienić lokalizację zewnętrznego źródła danych webdatasource, aby wskazywało lokalizację, dla której masz uprawnienia do zapisu. 

```sql
USE [mydbname];
GO

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL [SasTokenWrite]
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

## <a name="use-the-external-table"></a>Korzystanie z tabeli zewnętrznej

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

Aby uzyskać więcej informacji na temat wykonywania zapytań o różne typy plików, zapoznaj się z artykułami [Single CSV zapytania](query-single-csv-file.md), [pliki Parquet zapytania](query-parquet-files.md)i [pliki zapytań JSON](query-json-files.md) .
