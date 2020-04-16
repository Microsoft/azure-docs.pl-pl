---
title: Mapowanie schematu w działaniu kopiowania
description: Dowiedz się, jak kopiować aktywność w mapach usługi Azure Data Factory schematów i typów danych z danych źródłowych do danych sink podczas kopiowania danych.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414129"
---
# <a name="schema-mapping-in-copy-activity"></a>Mapowanie schematu w działaniu kopiowania
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak działanie kopiowania usługi Azure Data Factory wykonuje mapowanie schematu i mapowanie typów danych z danych źródłowych do danych ujścia podczas wykonywania kopiowania danych.

## <a name="schema-mapping"></a>Mapowanie schematu

Mapowanie kolumn ma zastosowanie podczas kopiowania danych ze źródła do ujścia. Domyślnie skopiuj **dane źródłowe mapy aktywności, aby zatonąć według nazw kolumn**. Można określić [jawne mapowanie,](#explicit-mapping) aby dostosować mapowanie kolumn w zależności od potrzeb. Dokładniej, skopiuj działanie:

1. Odczytywanie danych ze źródła i określanie schematu źródłowego
2. Użyj domyślnego mapowania kolumn do mapowania kolumn według nazwy lub zastosuj jawne mapowanie kolumn, jeśli jest określone.
3. Zapisz dane do ujścia

### <a name="explicit-mapping"></a>Mapowanie jawne

Można określić kolumny do mapowania w funkcji `translator`  ->  `mappings` -> działania kopiowania. Poniższy przykład definiuje działanie kopiowania w potoku, aby skopiować dane z tekstu rozdzielanego do bazy danych SQL Azure.

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

Następujące właściwości są obsługiwane `translator`  ->  `mappings` w obszarze -> `source` obiektu `sink`z i:

| Właściwość | Opis                                                  | Wymagany |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Nazwa kolumny źródła lub ujścia.                           | Tak      |
| Porządkowych  | Indeks kolumny. Zacznij od 1. <br>Zastosuj i wymagane podczas korzystania z tekstu rozdzielanych bez wiersza nagłówka. | Nie       |
| ścieżka     | Wyrażenie ścieżki JSON dla każdego pola do wyodrębnienia lub mapy. Ubiegaj się o dane hierarchiczne np.<br>W przypadku pól pod obiektem głównym ścieżka JSON zaczyna się od głównego $; dla pól wewnątrz tablicy `collectionReference` wybranej przez właściwość ścieżka JSON rozpoczyna się od elementu tablicy. | Nie       |
| type     | Data Factory tymczasowy typ danych kolumny źródłowej lub ujścia. | Nie       |
| kultura  | Kultura kolumny źródłowej lub zlewu. <br>Zastosuj, gdy `Datetime` `Datetimeoffset`typ jest lub . Wartość domyślna to `en-us`. | Nie       |
| format   | Ciąg formatu, który `Datetime` ma `Datetimeoffset`być używany, gdy typ jest lub . Informacje na temat formatowania datetime można znaleźć w [obszarze Niestandardowe ciągi formatów daty i godziny.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) | Nie       |

Następujące właściwości są obsługiwane `translator`  ->  `mappings` w obszarze oprócz `source` `sink`obiektu z i:

| Właściwość            | Opis                                                  | Wymagany |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference (wniosek o zbieranie danych) | Obsługiwane tylko wtedy, gdy dane hierarchiczne np MongoDB/REST jest źródłem.<br>Jeśli chcesz iterować i wyodrębniać dane z obiektów **wewnątrz pola tablicy** z tym samym wzorcem i konwertować na wiersz na obiekt, określ ścieżkę JSON tej tablicy, aby zastosować krzyżowo. | Nie       |

### <a name="alternative-column-mapping"></a>Mapowanie kolumn alternatywnych

Można określić działanie kopiowania `translator`  ->  `columnMappings` -> do mapowania między danymi w kształcie tabelarycznym . W takim przypadku sekcja "struktura" jest wymagana zarówno dla wejściowych, jak i wyjściowych zestawów danych. Mapowanie kolumn obsługuje **mapowanie wszystkich lub podzbioru kolumn w źródłowym zestawie danych "struktura" do wszystkich kolumn w zestawie danych ujścia "struktura"**. Poniżej przedstawiono warunki błędu, które skutkują wyjątkiem:

* Wynik kwerendy magazynu danych źródłowych nie ma nazwy kolumny określonej w sekcji "struktura" zestawu danych wejściowych.
* Magazyn danych ujścia (jeśli ze wstępnie zdefiniowanym schematem) nie ma nazwy kolumny, która jest określona w wyjściowym zestawie danych "struktura" sekcji.
* Mniej kolumn lub więcej kolumn w "strukturze" zestawu danych ujścia niż określono w mapowaniu.
* Mapowanie duplikatów.

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

W tym przykładzie wyjściowy zestaw danych ma strukturę i wskazuje na tabelę w Salesfoce.

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

Następujący JSON definiuje działanie kopiowania w potoku. Kolumny ze źródła mapowane do kolumn w zmiecie przy użyciu **translator** -> **columnMappings** właściwości.

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

Jeśli używasz składni `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` do określania mapowania kolumn, jest on nadal obsługiwany w stanie, w jakim jest.

### <a name="alternative-schema-mapping"></a>Mapowanie schematu alternatywnego

Można określić działanie kopiowania `translator`  ->  `schemaMapping` -> do mapowania danych w kształcie hierarchicznym i danych w kształcie tabelarycznym, na przykład kopiowanie z MongoDB/REST do pliku tekstowego i kopiowanie z Oracle do interfejsu API usługi Azure Cosmos DB dla mongodb. Następujące właściwości są obsługiwane w `translator` sekcji działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu translatora działań kopiowania musi być ustawiona na: **TabularTranslator** | Tak |
| schematMapowanie | Kolekcja par klucz-wartość, która reprezentuje relację mapowania **od strony źródła do strony ujścia**.<br/>- **Klucz:** reprezentuje źródło. W przypadku **źródła tabelaryczne**należy określić nazwę kolumny zdefiniowaną w strukturze zestawu danych; dla **źródła hierarchicznego**, należy określić wyrażenie ścieżki JSON dla każdego pola do wyodrębnienia i zamapowania.<br>- **Wartość:** reprezentuje ujście. W przypadku **ujścia tabelaryczne**należy określić nazwę kolumny zdefiniowaną w strukturze zestawu danych; dla **ujścia hierarchicznego**, należy określić wyrażenie ścieżki JSON dla każdego pola do wyodrębnienia i zamapowania. <br>W przypadku danych hierarchicznych dla pól pod obiektem głównym ścieżka JSON zaczyna się od głównego $; dla pól wewnątrz tablicy `collectionReference` wybranej przez właściwość ścieżka JSON rozpoczyna się od elementu tablicy.  | Tak |
| collectionReference (wniosek o zbieranie danych) | Jeśli chcesz iterować i wyodrębniać dane z obiektów **wewnątrz pola tablicy** z tym samym wzorcem i konwertować na wiersz na obiekt, określ ścieżkę JSON tej tablicy, aby zastosować krzyżowo. Ta właściwość jest obsługiwana tylko wtedy, gdy dane hierarchiczne są źródłem. | Nie |

**Przykład: kopia z MongoDB do Oracle:**

Na przykład, jeśli masz dokument MongoDB z następującą zawartością:

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

i chcesz skopiować go do tabeli SQL platformy Azure w następującym formacie, spłaszczając dane wewnątrz tablicy *(order_pd i order_price)* i połączyć sprzężenie ze wspólnymi informacjami *głównymi (numer, data i miasto)*:

| Ordernumber | Datazamówienia | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Skonfiguruj regułę mapowania schematu jako następujący przykład JSON działania kopiowania:

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

Działanie kopiowania wykonuje typy źródeł do mapowania typów ujścia z następującym podejściem dwuetapowym:

1. Konwertowanie z natywnych typów źródeł na tymczasowe typy danych usługi Azure Data Factory
2. Konwersja tymczasowych typów danych usługi Azure Data Factory na typ ujścia macierzystego

Działanie kopiowania obsługuje następujące tymczasowe typy danych: 

* Bajt[]
* Wartość logiczna
* Datetime (data/godzina)
* Datetimeoffset
* Wartość dziesiętna
* Double
* Guid (identyfikator GUID)
* Int16
* Int32
* Int64
* Single
* Ciąg
* Zakres czasu

## <a name="next-steps"></a>Następne kroki
Zobacz inne artykuły dotyczące działania kopiowania:

- [Omówienie działania kopiowania](copy-activity-overview.md)
