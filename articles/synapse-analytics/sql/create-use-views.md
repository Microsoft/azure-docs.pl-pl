---
title: Tworzenie i używanie widoków w języku SQL na żądanie (wersja zapoznawcza)
description: W tej sekcji dowiesz się, jak tworzyć i używać widoków do zawijania zapytań SQL na żądanie (wersja zapoznawcza). Widoki umożliwiają ponowne użycie tych zapytań. Widoki są również potrzebne, jeśli chcesz używać narzędzi, takich jak Usługa Power BI, w połączeniu z programem SQL na żądanie.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0f5323193706fdd00739be6c71a4fe12cfedf21b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424538"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Tworzenie i używanie widoków w języku SQL na żądanie (wersja zapoznawcza) przy użyciu usługi Azure Synapse Analytics

W tej sekcji dowiesz się, jak tworzyć i używać widoków do zawijania zapytań SQL na żądanie (wersja zapoznawcza). Widoki umożliwiają ponowne użycie tych zapytań. Widoki są również potrzebne, jeśli chcesz używać narzędzi, takich jak Usługa Power BI, w połączeniu z programem SQL na żądanie.

## <a name="prerequisites"></a>Wymagania wstępne

Pierwszym krokiem jest przejrzenie poniższych artykułów i upewnienie się, że spełniasz wymagania wstępne dotyczące tworzenia i używania widoków SQL na żądanie:

- [Konfiguracja po raz pierwszy](query-data-storage.md#first-time-setup)
- [Wymagania wstępne](query-data-storage.md#prerequisites)

## <a name="create-a-view"></a>Tworzenie widoku

Widoki można tworzyć w taki sam sposób, jak zwykłe widoki programu SQL Server. Poniższa kwerenda tworzy widok, który odczytuje plik *population.csv.*

> [!NOTE]
> Zmień pierwszy wiersz w kwerendzie, czyli [mydbname], aby użyć utworzonej bazy danych. Jeśli baza danych nie została utworzona, przeczytaj artykuł [Pierwsza konfiguracja](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
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

## <a name="use-a-view"></a>Korzystanie z widoku

Widoki w kwerendach można używać w taki sam sposób, w jaki używane są widoki w kwerendach programu SQL Server.

Poniższa kwerenda pokazuje, przy użyciu widoku *population_csv* utworzyliśmy w [Tworzenie widoku](#create-a-view). Zwraca nazwy krajów z ich populacją w 2019 roku w porządku malejącym.

> [!NOTE]
> Zmień pierwszy wiersz w kwerendzie, czyli [mydbname], aby użyć utworzonej bazy danych. Jeśli baza danych nie została utworzona, przeczytaj artykuł [Pierwsza konfiguracja](query-data-storage.md#first-time-setup).

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

Aby uzyskać informacje na temat sposobu wykonywania zapytań o różne typy plików, zobacz [pojedyncze pliki CSV kwerendy,](query-single-csv-file.md) [pliki parkietu kwerendy](query-parquet-files.md)i artykuły [z plikami JSON kwerendy.](query-json-files.md)
