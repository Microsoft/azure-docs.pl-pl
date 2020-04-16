---
title: Tworzenie i używanie tabel zewnętrznych w języku SQL na żądanie (wersja zapoznawcza)
description: W tej sekcji dowiesz się, jak tworzyć i używać tabel zewnętrznych w języku SQL na żądanie (wersja zapoznawcza). Tabele zewnętrzne są przydatne, gdy chcesz kontrolować dostęp do danych zewnętrznych w programie SQL on-demand i jeśli chcesz używać narzędzi, takich jak Usługa Power BI, w połączeniu z programem SQL na żądanie.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a708e5e713e62ab5b7ca70b61235a84830b051cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424552"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Tworzenie i używanie tabel zewnętrznych w języku SQL na żądanie (wersja zapoznawcza) przy użyciu usługi Azure Synapse Analytics

W tej sekcji dowiesz się, jak tworzyć i używać tabel zewnętrznych w języku SQL na żądanie (wersja zapoznawcza). Tabele zewnętrzne są przydatne, gdy chcesz kontrolować dostęp do danych zewnętrznych w programie SQL on-demand i jeśli chcesz używać narzędzi, takich jak Usługa Power BI, w połączeniu z programem SQL na żądanie.

## <a name="prerequisites"></a>Wymagania wstępne

Pierwszym krokiem jest przejrzenie poniższych artykułów i upewnienie się, że spełniasz wymagania wstępne dotyczące tworzenia i używania tabel zewnętrznych SQL na żądanie:

- [Konfiguracja po raz pierwszy](query-data-storage.md#first-time-setup)
- [Wymagania wstępne](query-data-storage.md#prerequisites)

## <a name="create-an-external-table"></a>Tworzenie tabeli zewnętrznej

Tabele zewnętrzne można tworzyć w taki sam sposób, jak zwykłe tabele zewnętrzne programu SQL Server. Poniższa kwerenda tworzy tabelę zewnętrzną, która odczytuje plik *population.csv.*

> [!NOTE]
> Zmień pierwszy wiersz w kwerendzie, czyli [mydbname], aby użyć utworzonej bazy danych. Jeśli baza danych nie została utworzona, przeczytaj artykuł [Pierwsza konfiguracja](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [CsvDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT CSVFileFormat
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT,
    FORMAT_OPTIONS (
        FIELD_TERMINATOR = ',',
        STRING_DELIMITER = '"',
        FIRST_ROW = 2
    )
);
GO

CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
);
WITH (
    LOCATION = 'population/population.csv',
    DATA_SOURCE = CsvDataSource,
    FILE_FORMAT = CSVFileFormat
);
GO
```

## <a name="use-a-external-table"></a>Używanie tabeli zewnętrznej

Tabele zewnętrzne w kwerendach można używać w taki sam sposób, w jaki są używane w kwerendach programu SQL Server.

Poniższa kwerenda pokazuje przy użyciu *tabeli* zewnętrznej populacji utworzonej w [sekcji Tworzenie tabeli zewnętrznej.](#create-an-external-table) Zwraca nazwy krajów z ich populacją w 2019 roku w porządku malejącym.

> [!NOTE]
> Zmień pierwszy wiersz w kwerendzie, czyli [mydbname], aby użyć utworzonej bazy danych. Jeśli baza danych nie została utworzona, przeczytaj artykuł [Pierwsza konfiguracja](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Następne kroki

Informacje na temat przechowywania wyników kwerendy w magazynie można znaleźć w [programie Store, w odniesieniu do wyników kwerendy magazynu](../sql/create-external-table-as-select.md).
