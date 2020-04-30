---
title: Tworzenie i używanie tabel zewnętrznych w programie SQL na żądanie (wersja zapoznawcza)
description: W tej sekcji dowiesz się, jak tworzyć tabele zewnętrzne i korzystać z nich na żądanie SQL (wersja zapoznawcza). Tabele zewnętrzne są przydatne, gdy chcesz kontrolować dostęp do danych zewnętrznych w programie SQL na żądanie, a jeśli chcesz używać narzędzi, takich jak Power BI, w połączeniu z SQL na żądanie.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a708e5e713e62ab5b7ca70b61235a84830b051cd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424552"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Tworzenie i używanie tabel zewnętrznych w programie SQL na żądanie (wersja zapoznawcza) przy użyciu usługi Azure Synapse Analytics

W tej sekcji dowiesz się, jak tworzyć tabele zewnętrzne i korzystać z nich na żądanie SQL (wersja zapoznawcza). Tabele zewnętrzne są przydatne, gdy chcesz kontrolować dostęp do danych zewnętrznych w programie SQL na żądanie, a jeśli chcesz używać narzędzi, takich jak Power BI, w połączeniu z SQL na żądanie.

## <a name="prerequisites"></a>Wymagania wstępne

Pierwszym krokiem jest zapoznanie się z artykułami poniżej i upewnienie się, że spełniono wymagania wstępne dotyczące tworzenia i używania tabel zewnętrznych na żądanie SQL:

- [Konfiguracja pierwszego czasu](query-data-storage.md#first-time-setup)
- [Wymagania wstępne](query-data-storage.md#prerequisites)

## <a name="create-an-external-table"></a>Tworzenie tabeli zewnętrznej

Tabele zewnętrzne można tworzyć w ten sam sposób, w jaki tworzysz regularne SQL Server tabele zewnętrzne. Poniższe zapytanie tworzy tabelę zewnętrzną, która odczytuje plik. *CSV* .

> [!NOTE]
> Zmień pierwszy wiersz zapytania, na przykład [mydbname], tak aby była używana utworzona baza danych. Jeśli baza danych nie została utworzona, zapoznaj się z [konfiguracją pierwszego czasu](query-data-storage.md#first-time-setup).

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

## <a name="use-a-external-table"></a>Korzystanie z tabeli zewnętrznej

W zapytaniach można używać tabel zewnętrznych w taki sam sposób, w jaki są one używane w kwerendach SQL Server.

Poniższe zapytanie ilustruje użycie tabeli zewnętrznej *populacji* utworzonej w sekcji [Tworzenie tabeli zewnętrznej](#create-an-external-table) . Zwraca nazwy krajów z ich populacją w 2019 w kolejności malejącej.

> [!NOTE]
> Zmień pierwszy wiersz zapytania, na przykład [mydbname], tak aby była używana utworzona baza danych. Jeśli baza danych nie została utworzona, zapoznaj się z [konfiguracją pierwszego czasu](query-data-storage.md#first-time-setup).

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

Aby uzyskać informacje na temat sposobu przechowywania wyników zapytania do magazynu, zapoznaj się z [wynikami zapytania Store do magazynu](../sql/create-external-table-as-select.md).
