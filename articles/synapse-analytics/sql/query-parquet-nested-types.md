---
title: Parquet typów zagnieżdżonych zapytania przy użyciu języka SQL na żądanie (wersja zapoznawcza)
description: W tym artykule dowiesz się, jak wykonywać zapytania dotyczące zagnieżdżonych typów Parquet.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a1e3d3c7494aa75b3f6d481d12135316791772d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431659"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Parquet typy zagnieżdżone zapytania przy użyciu języka SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics

W tym artykule dowiesz się, jak napisać zapytanie przy użyciu języka SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics.  To zapytanie będzie odczytywać Parquet typy zagnieżdżone.

## <a name="prerequisites"></a>Wymagania wstępne

Przed przeczytaniem pozostałej części tego artykułu zapoznaj się z następującymi artykułami:

- [Konfiguracja pierwszego czasu](query-data-storage.md#first-time-setup)
- [Wymagania wstępne](query-data-storage.md#prerequisites)

## <a name="project-nested-or-repeated-data"></a>Dane zagnieżdżone lub powtórzone projektu

Następujące zapytanie odczytuje plik *justSimpleArray. Parquet* . Projektuje wszystkie kolumny z pliku Parquet, w tym zagnieżdżone lub powtórzone dane.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>Dostęp do elementów z zagnieżdżonych kolumn

Następujące zapytanie odczytuje plik *structExample. Parquet* i pokazuje, jak elementy powierzchni zagnieżdżonej kolumny:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/structExample.parquet',
        FORMAT='PARQUET'
    )
    WITH (
        -- you can see original n"sted columns values by uncommenting lines below
        --DateStruct VARCHAR(8000),
        [DateStruct.Date] DATE,
        --TimeStruct VARCHAR(8000),
        [TimeStruct.Time] TIME,
        --TimestampStruct VARCHAR(8000),
        [TimestampStruct.Timestamp] DATETIME2,
        --DecimalStruct VARCHAR(8000),
        [DecimalStruct.Decimal] DECIMAL(18, 5),
        --FloatStruct VARCHAR(8000),
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Dostęp do elementów z powtórzonych kolumn

Następujące zapytanie odczytuje plik *justSimpleArray. Parquet* i używa [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) do pobierania elementu **skalarnego** z wewnątrz powtórzonej kolumny, takiej jak tablica lub mapa:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

Następujące zapytanie odczytuje plik *mapExample. Parquet* i używa [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) do pobierania **nieskalarnego** elementu z wewnątrz powtórzonej kolumny, takiej jak tablica lub mapa:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/mapExample.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="next-steps"></a>Następne kroki

W następnym artykule przedstawiono sposób [wykonywania zapytań dotyczących plików JSON](query-json-files.md).
