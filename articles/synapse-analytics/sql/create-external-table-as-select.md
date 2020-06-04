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
ms.openlocfilehash: 97e0b53aeac5a0adc939c87304ea35bb967eac52
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84345074"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Przechowywanie wyników zapytania w magazynie przy użyciu funkcji SQL na żądanie (wersja zapoznawcza) przy użyciu usługi Azure Synapse Analytics

W tym artykule dowiesz się, jak przechowywać wyniki zapytania do magazynu przy użyciu usługi SQL na żądanie (wersja zapoznawcza).

## <a name="prerequisites"></a>Wymagania wstępne

Pierwszym krokiem jest **utworzenie bazy danych** , w której będą wykonywane zapytania. Następnie zainicjuj obiekty, wykonując [skrypt Instalatora](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) w tej bazie danych. Ten skrypt instalacyjny utworzy źródła danych, poświadczenia w zakresie bazy danych i zewnętrzne formaty plików, które są używane do odczytywania danych w tych przykładach.

Postępuj zgodnie z instrukcjami w tym artykule, aby utworzyć źródła danych, poświadczenia zakresu bazy danych i zewnętrzne formaty plików, które są używane do zapisywania danych w magazynie danych wyjściowych.

## <a name="create-external-table-as-select"></a>Utwórz tabelę zewnętrzną jako wybraną

Można użyć instrukcji CREATE EXTERNAL TABLE AS SELECT (CETAS), aby przechowywać wyniki zapytania w magazynie.

> [!NOTE]
> Zmień pierwszy wiersz zapytania, na przykład [mydbname], tak aby była używana utworzona baza danych.

```sql
USE [mydbname];
GO

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL = [SasTokenWrite]
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
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0'
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
> Zmień pierwszy wiersz zapytania, na przykład [mydbname], tak aby była używana utworzona baza danych.

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

Aby uzyskać więcej informacji na temat wykonywania zapytań dotyczących różnych typów plików, zobacz artykuł [plik CSV zapytania Single](query-single-csv-file.md), [pliki Parquet zapytań](query-parquet-files.md)i [pliki zapytania JSON](query-json-files.md) .
