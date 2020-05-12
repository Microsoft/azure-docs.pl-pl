---
title: Wykonywanie zapytań dotyczących plików JSON przy użyciu języka SQL na żądanie (wersja zapoznawcza)
description: W tej sekcji opisano sposób odczytywania plików JSON przy użyciu funkcji SQL na żądanie w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 4c1fe9ac5d3b2470fb70231a83e57f3e08d0dfb1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197579"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Wykonywanie zapytań dotyczących plików JSON przy użyciu funkcji SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics

W tym artykule dowiesz się, jak napisać zapytanie przy użyciu języka SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics. Celem zapytania jest odczytanie plików JSON. Obsługiwane formaty są wymienione w [OPENROWSET](develop-openrowset.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed przeczytaniem pozostałej części tego artykułu zapoznaj się z następującymi artykułami:

- [Konfiguracja pierwszego czasu](query-data-storage.md#first-time-setup)
- [Wymagania wstępne](query-data-storage.md#prerequisites)

## <a name="sample-json-files"></a>Przykładowe pliki JSON

Poniższa sekcja zawiera przykładowe skrypty do odczytu plików JSON. Pliki są przechowywane w kontenerze *JSON* , w *książkach*folderów i zawierają pojedynczy wpis książki o następującej strukturze:

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

## <a name="read-json-files"></a>Odczytaj pliki JSON

Aby przetwarzać pliki JSON przy użyciu JSON_VALUE i [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), należy odczytać plik JSON z magazynu jako pojedynczą kolumnę. Poniższy skrypt odczytuje plik *Book1. JSON* jako pojedynczą kolumnę:

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
> Odczytujesz cały plik JSON jako pojedynczy wiersz lub kolumnę. Tak więc FIELDTERMINATOR, FIELDQUOTE i ROWTERMINATOR są ustawione na 0x0B.

## <a name="query-json-files-using-json_value"></a>Wykonywanie zapytań dotyczących plików JSON przy użyciu JSON_VALUE

Poniższe zapytanie pokazuje, jak za pomocą [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) pobierać wartości skalarne (tytuł, wydawca) z książki zatytułowanej *probabilistyczne i metody statystyczne w Cryptology, wprowadzenie do wybranych artykułów*:

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

## <a name="query-json-files-using-json_query"></a>Wykonywanie zapytań dotyczących plików JSON przy użyciu JSON_QUERY

Poniższe zapytanie pokazuje, w jaki sposób używać [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) do pobierania obiektów i tablic (autorów) z książki zatytułowanej *probabilistyczne i metody statystyczne w Cryptology, wprowadzenie do wybranych tematów*:

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

## <a name="query-json-files-using-openjson"></a>Wykonywanie zapytań dotyczących plików JSON przy użyciu OPENJSON

Następujące zapytanie używa [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Spowoduje to pobranie obiektów i właściwości w ramach książki zatytułowanej *probabilistyczne i metody statystyczne w Cryptology, wprowadzenie do wybranych artykułów*:

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
        jsonContent NVARCHAR(max) -- Use appropriate length. Make sure JSON fits. 
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>Następne kroki

W następnych artykułach w tej serii pokazano, jak:

- [Wykonywanie zapytań dotyczących folderów i wielu plików](query-folders-multiple-csv-files.md)
- [Tworzenie widoków i korzystanie z nich](create-use-views.md)
