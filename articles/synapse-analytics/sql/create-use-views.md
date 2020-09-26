---
title: Tworzenie i Używanie widoków w SQL na żądanie (wersja zapoznawcza)
description: W tej sekcji dowiesz się, jak tworzyć widoki i używać ich do zawijania zapytań SQL na żądanie (wersja zapoznawcza). Widoki umożliwią ponowne użycie tych zapytań. Widoki są również potrzebne, jeśli chcesz użyć narzędzi, takich jak Power BI, w połączeniu z SQL na żądanie.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: f91611ab3c75a96f13ab84312ca0b2157e69af0d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91289314"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Tworzenie i Używanie widoków w usłudze SQL na żądanie (wersja zapoznawcza) przy użyciu usługi Azure Synapse Analytics

W tej sekcji dowiesz się, jak tworzyć widoki i używać ich do zawijania zapytań SQL na żądanie (wersja zapoznawcza). Widoki umożliwią ponowne użycie tych zapytań. Widoki są również potrzebne, jeśli chcesz użyć narzędzi, takich jak Power BI, w połączeniu z SQL na żądanie.

## <a name="prerequisites"></a>Wymagania wstępne

Pierwszym krokiem jest utworzenie bazy danych, w której widok zostanie utworzony, i zainicjowanie obiektów potrzebnych do uwierzytelnienia w usłudze Azure Storage przez wykonanie [skryptu instalacyjnego](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) w tej bazie danych. Wszystkie zapytania w tym artykule zostaną wykonane w przykładowej bazie danych.

## <a name="create-a-view"></a>Tworzenie widoku

Możesz tworzyć widoki w taki sam sposób, jak w przypadku zwykłych widoków SQL Server. Poniższe zapytanie tworzy widok, który odczytuje *population.csv* pliku.

> [!NOTE]
> Zmień pierwszy wiersz zapytania, na przykład [mydbname], tak aby była używana utworzona baza danych.

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', 
        FIELDTERMINATOR =',', 
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r];
```

Widok w tym przykładzie używa `OPENROWSET` funkcji, która używa ścieżki bezwzględnej do plików bazowych. Jeśli masz `EXTERNAL DATA SOURCE` główny adres URL magazynu, możesz użyć `OPENROWSET` z `DATA_SOURCE` i względnej ścieżki pliku:

```
CREATE VIEW TaxiView
AS SELECT *, nyc.filepath(1) AS [year], nyc.filepath(2) AS [month]
FROM
    OPENROWSET(
        BULK 'parquet/taxi/year=*/month=*/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc
```

## <a name="use-a-view"></a>Korzystanie z widoku

W zapytaniach można używać widoków w taki sam sposób jak w przypadku zapytań SQL Server.

Poniższe zapytanie ilustruje użycie widoku *population_csv* utworzonego w temacie [Tworzenie widoku](#create-a-view). Zwraca nazwy kraju/regionu z populacją w 2019 w kolejności malejącej.

> [!NOTE]
> Zmień pierwszy wiersz zapytania, na przykład [mydbname], tak aby była używana utworzona baza danych.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat wykonywania zapytań o różne typy plików, zapoznaj się z artykułami [Single CSV pliku](query-single-csv-file.md), [Query Parquet Files](query-parquet-files.md)i [plików zapytania JSON](query-json-files.md) .
