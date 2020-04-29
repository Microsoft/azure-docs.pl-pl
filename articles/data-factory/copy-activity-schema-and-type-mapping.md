---
title: Mapowanie schematu w działaniu kopiowania
description: Dowiedz się więcej o tym, jak działa kopiowanie w Azure Data Factory mapuje schematy i typy danych z danych źródłowych na dane ujścia podczas kopiowania danych.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jingwang
ms.openlocfilehash: 9f04955fb910a6159dc09ac40a87a398e67d59d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414129"
---
# <a name="schema-mapping-in-copy-activity"></a>Mapowanie schematu w działaniu kopiowania
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, w jaki sposób działanie Copy Azure Data Factory ma mapowanie schematu i mapowanie typu danych z danych źródłowych na dane ujścia podczas wykonywania kopii danych.

## <a name="schema-mapping"></a>Mapowanie schematu

Mapowanie kolumn ma zastosowanie w przypadku kopiowania danych ze źródła do ujścia. Domyślnie Kopiuj **dane źródłowe mapy aktywności do ujścia według nazw kolumn**. Możesz określić [jawne mapowanie](#explicit-mapping) , aby dostosować mapowanie kolumn na podstawie potrzeb. Dokładniej działanie kopiowania:

1. Odczytywanie danych ze źródła i określanie schematu źródłowego
2. Użyj domyślnego mapowania kolumn, aby mapować kolumny według nazwy lub zastosować jawne Mapowanie kolumn, jeśli określono.
3. Zapisz dane do ujścia

### <a name="explicit-mapping"></a>Jawne mapowanie

Możesz określić kolumny, które mają być mapowane w działaniu kopiowania `translator`  ->  `mappings` — > właściwości. Poniższy przykład definiuje działanie kopiowania w potoku, aby skopiować dane z rozdzielanego tekstu do Azure SQL Database.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [{
        "referenceName": "DelimitedTextInput",
        "type": "DatasetReference"
    }],
    "outputs": [{
        "referenceName": "AzureSqlOutput",
        "type": "DatasetReference"
    }],
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": {
                        "name": "UserId",
                        "type": "Guid"
                    },
                    "sink": {
                        "name": "MyUserId"
                    }
                }, 
                {
                    "source": {
                        "name": "Name",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyName"
                    }
                }, 
                {
                    "source": {
                        "name": "Group",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyGroup"
                    }
                }
            ]
        }
    }
}
```

`translator`  ->  `mappings` Następujące właściwości są obsługiwane w obiekcie-> z `source` i: `sink`

| Właściwość | Opis                                                  | Wymagany |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Nazwa kolumny źródłowej lub ujścia.                           | Tak      |
| liczbą  | Indeks kolumn. Zacznij od 1. <br>Zastosuj i wymagane w przypadku używania tekstu rozdzielanego bez wiersza nagłówka. | Nie       |
| ścieżka     | Wyrażenie ścieżki JSON dla każdego pola do wyodrębnienia lub zamapowania. Zastosuj dla danych hierarchicznych, np. MongoDB/REST.<br>W przypadku pól pod obiektem głównym ścieżka JSON rozpoczyna się od elementu root $; w przypadku pól wewnątrz tablicy wybranej przez `collectionReference` właściwość ścieżka JSON zaczyna się od elementu Array. | Nie       |
| type     | Data Factory pośredni typ danych kolumny źródłowej lub ujścia. | Nie       |
| kultura  | Kultura kolumny źródłowej lub ujścia. <br>Zastosuj, gdy typ `Datetime` jest `Datetimeoffset`lub. Wartość domyślna to `en-us`. | Nie       |
| format   | Ciąg formatu, który ma być używany, `Datetime` gdy `Datetimeoffset`typem jest lub. Zapoznaj się z [niestandardowymi ciągami formatu daty i godziny](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) na potrzeby formatowania daty i godziny. | Nie       |

`translator`  ->  `mappings` Następujące właściwości są obsługiwane w dodatku do obiektów z `source` i: `sink`

| Właściwość            | Opis                                                  | Wymagany |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Obsługiwane tylko wtedy, gdy dane hierarchiczne, np. MongoDB/REST, są źródłem.<br>Jeśli chcesz wykonać iterację i wyodrębnić dane z obiektów **wewnątrz pola tablicy** o tym samym wzorcu i przekonwertować na na wiersz dla każdego obiektu, określ ścieżkę JSON tej tablicy, która ma być stosowana krzyżowo. | Nie       |

### <a name="alternative-column-mapping"></a>Alternatywne Mapowanie kolumn

Możesz określić działanie kopiowania — > `translator`  ->  `columnMappings` , aby mapować między danymi w kształcie tabelarycznym. W takim przypadku sekcja "Structure" jest wymagana zarówno dla zestawów danych wejściowych, jak i wyjściowych. Mapowanie kolumn obsługuje **mapowanie wszystkich lub podzbioru kolumn w źródłowym zestawie danych "struktura" na wszystkie kolumny w zestawie danych ujścia "Structure**". Poniżej przedstawiono warunki błędów, które powodują wyjątek:

* Wynik zapytania źródłowego magazynu danych nie zawiera nazwy kolumny określonej w sekcji "struktura" wejściowego zestawu danych.
* Magazyn danych ujścia (jeśli ze wstępnie zdefiniowanym schematem) nie ma nazwy kolumny określonej w sekcji "struktura" wyjściowego zestawu danych.
* Mniejsza liczba kolumn lub więcej kolumn w strukturze zestawu danych ujścia, niż określono w mapowaniu.
* Zduplikowane mapowanie.

W poniższym przykładzie wejściowy zestaw danych ma strukturę i wskazuje tabelę w lokalnej bazie danych Oracle.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

W tym przykładzie wyjściowy zestaw danych ma strukturę i wskazuje tabelę w Salesfoce.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

Poniższy kod JSON definiuje działanie kopiowania w potoku. Kolumny ze źródła zamapowane na kolumny w usłudze ujścia przy**użyciu właściwości** **translatora** -> obiektów.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Jeśli używasz składni `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` w celu określenia mapowania kolumn, jest ono nadal obsługiwane.

### <a name="alternative-schema-mapping"></a>Alternatywne mapowanie schematu

Możesz określić działanie kopiowania — > `translator`  ->  `schemaMapping` , aby mapować między danymi w kształcie hierarchicznym a danymi w formie tabelarycznej, np. kopiowania z MongoDB/REST do pliku tekstowego i kopiowania z programu Oracle do Azure Cosmos DB interfejsu API dla MongoDB. W sekcji działania `translator` kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type translatora działania kopiowania musi być ustawiona na wartość: **TabularTranslator** | Tak |
| schemaMapping | Kolekcja par klucz-wartość, która reprezentuje relację mapowania **ze strony źródłowej do ujścia**.<br/>- **Klucz:** reprezentuje źródło. Dla **źródła tabelarycznego**Określ nazwę kolumny, zgodnie z definicją w strukturze zestawu danych. dla **źródła hierarchicznego**Określ wyrażenie ścieżki JSON dla każdego pola, które ma zostać wyodrębnione i zamapowane.<br>- **Wartość:** reprezentuje obiekt sink. W przypadku **ujścia tabelarycznego**należy określić nazwę kolumny, zgodnie z definicją w strukturze zestawu danych. dla **obiektu ujścia hierarchiczne**Określ wyrażenie ścieżki JSON dla każdego pola do wyodrębnienia i mapowania. <br>W przypadku danych hierarchicznych dla pól w obszarze obiekt główny ścieżka JSON rozpoczyna się od elementu głównego $; w przypadku pól wewnątrz tablicy wybranej przez `collectionReference` właściwość ścieżka JSON zaczyna się od elementu Array.  | Tak |
| collectionReference | Jeśli chcesz wykonać iterację i wyodrębnić dane z obiektów **wewnątrz pola tablicy** o tym samym wzorcu i przekonwertować na na wiersz dla każdego obiektu, określ ścieżkę JSON tej tablicy, która ma być stosowana krzyżowo. Ta właściwość jest obsługiwana tylko wtedy, gdy dane hierarchiczne są źródłem. | Nie |

**Przykład: Kopiuj z MongoDB do Oracle:**

Na przykład jeśli masz dokument MongoDB o następującej zawartości:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

i chcesz skopiować go do tabeli Azure SQL w następującym formacie, przez spłaszczenie danych wewnątrz tablicy *(order_pd i order_price)* i sprzężenie krzyżowe ze wspólnymi informacjami głównymi *(liczba, Data i miasto)*:

| orderNumber | DataZamówienia | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Skonfiguruj regułę mapowania schematu jako przykład JSON działania kopiowania:

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Mapowanie typu danych

Działanie kopiowania wykonuje typy źródłowe na potrzeby mapowania typów ujścia z następującym podejściem dwuetapowym:

1. Konwertuj z natywnych typów źródła na Azure Data Factory pośrednie typy danych
2. Konwertuj z Azure Data Factory pośrednie typy danych na natywny typ ujścia

Działanie kopiowania obsługuje następujące pośrednie typy danych: 

* Byte []
* Boolean
* Datetime (data/godzina)
* DateTimeOffset
* Wartość dziesiętna
* Double
* Guid (identyfikator GUID)
* Int16
* Int32
* Int64
* Single
* String
* Zakres czasu

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi artykułami dotyczącymi działania kopiowania:

- [Przegląd działania kopiowania](copy-activity-overview.md)
