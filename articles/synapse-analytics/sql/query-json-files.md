---
title: Kwerenda JSON plików przy użyciu SQL na żądanie (wersja zapoznawcza)
description: W tej sekcji wyjaśniono, jak odczytywać pliki JSON przy użyciu języka SQL na żądanie w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1054328216d0517b3f450ba4fe08f3bef32d68f7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431724"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Kwerenda plików JSON przy użyciu SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics

W tym artykule dowiesz się, jak napisać kwerendę przy użyciu języka SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics. Celem kwerendy jest odczyt plików JSON.

## <a name="prerequisites"></a>Wymagania wstępne

Przed przeczytaniem pozostałej części tego artykułu zapoznaj się z następującymi artykułami:

- [Konfiguracja po raz pierwszy](query-data-storage.md#first-time-setup)
- [Wymagania wstępne](query-data-storage.md#prerequisites)

## <a name="sample-json-files"></a>Przykładowe pliki JSON

Poniższa sekcja zawiera przykładowe skrypty do odczytu plików JSON. Pliki są przechowywane w kontenerze *json,* *portfele folderów*i zawierają pojedynczy wpis książki z następującą strukturą:

```json
{
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

## <a name="read-json-files"></a>Odczytywanie plików JSON

Aby przetworzyć pliki JSON przy użyciu JSON_VALUE i [JSON_QUERY,](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)należy odczytać plik JSON z magazynu jako pojedynczą kolumnę. Następujący skrypt odczytuje plik *book1.json* jako pojedynczą kolumnę:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/book1.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r];
```

> [!NOTE]
> Cały plik JSON jest odczytywaniem jako pojedynczego wiersza lub kolumny. Tak więc FIELDTERMINATOR, FIELDQUOTE i ROWTERMINATOR są ustawione na 0x0b.

## <a name="query-json-files-using-json_value"></a>Kwerenda JSON plików przy użyciu JSON_VALUE

Poniższa kwerenda pokazuje, jak używać [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) do pobierania wartości skalarnych (tytuł, wydawca) z książki zatytułowanej *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected articles:*

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-json_query"></a>Kwerenda JSON plików przy użyciu JSON_QUERY

W poniższej kwerendzie pokazano, jak używać [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) do pobierania obiektów i tablic (autorów) z książki zatytułowanej *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics:*

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-openjson"></a>Kwerenda plików JSON przy użyciu OPENJSON

Następująca kwerenda używa [funkcji OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Będzie pobierać obiekty i właściwości w książce zatytułowanej *Probabilistic i metody statystyczne w kryptologii, Wprowadzenie wybranych artykułów:*

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent NVARCHAR(4000) --Note that you have to use NVARCHAR(4000) for OPENJSON to work.
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>Następne kroki

Następne artykuły z tej serii zademonstrują, jak:

- [Wykonywanie zapytań o foldery i wiele plików](query-folders-multiple-csv-files.md)
- [Tworzenie i używanie widoków](create-use-views.md).
