---
title: Działanie Lookup w Azure Data Factory
description: Dowiedz się, jak używać działania Lookup do wyszukiwania wartości z zewnętrznego źródła. Do tych danych wyjściowych można uzyskać dalsze odwołania przez działania zakończone powodzeniem.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2021
ms.openlocfilehash: 4b2fb49899b6a676520fe0912dd122dd72cce023
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712916"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Działanie Lookup w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działanie Lookup może pobrać zestaw danych z dowolnych ze źródeł danych obsługiwanych przez Azure Data Factory. można jej użyć do dynamicznego określania, które obiekty mają działać w kolejnym działaniu, a nie do twardej kodowania nazwy obiektu. Niektóre przykłady obiektów to pliki i tabele.

Działanie Lookup odczytuje i zwraca zawartość pliku konfiguracji lub tabeli. Zwraca również wynik wykonania zapytania lub procedury składowanej. Wynik może być wartością pojedynczą lub tablicą atrybutów, które mogą być używane w kolejnych operacjach kopiowania, przekształcania lub przepływu sterowania, takich jak działanie ForEach.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

. Weź pod uwagę następujące kwestie:

- Działanie Lookup może zwrócić do **5000 wierszy**; Jeśli zestaw wyników zawiera więcej rekordów, zwracane są pierwsze 5000 wierszy.
- Dane wyjściowe działania Lookup obsługują rozmiar do **4 MB** , działanie zakończy się niepowodzeniem, jeśli rozmiar przekracza limit. 
- Najdłuższy czas trwania działania Lookup przed upływem limitu czasu wynosi **24 godziny**.
- Gdy używasz zapytania lub procedury składowanej do wyszukiwania danych, upewnij się, że został zwrócony jeden i dokładnie jeden zestaw wyników. W przeciwnym razie działanie Lookup kończy się niepowodzeniem.

Dla działania Lookup są obsługiwane następujące źródła danych. 

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Składnia

```json
{
    "name":"LookupActivity",
    "type":"Lookup",
    "typeProperties":{
        "source":{
            "type":"<source type>"
        },
        "dataset":{
            "referenceName":"<source dataset name>",
            "type":"DatasetReference"
        },
        "firstRowOnly":<true or false>
    }
}
```

## <a name="type-properties"></a>Właściwości typu

Nazwa | Opis | Typ | Wymagane?
---- | ----------- | ---- | --------
zestawu | Zawiera odwołanie do zestawu danych dla wyszukiwania. Pobierz szczegóły z sekcji **Właściwości zestawu danych** w każdym odpowiednim artykule łącznika. | Pary klucz wartość | Tak
source | Zawiera właściwości źródła specyficzne dla zestawu danych, takie same jak źródło działania kopiowania. Pobierz szczegóły z sekcji **właściwości działania kopiowania** w każdym odpowiednim artykule łącznika. | Pary klucz wartość | Tak
firstRowOnly | Wskazuje, czy zwrócić tylko pierwszy wiersz, czy wszystkie wiersze. | Wartość logiczna | Nie. Wartość domyślna to `true`.

> [!NOTE]
> 
> * Kolumny źródłowe z typem **ByteArray** nie są obsługiwane.
> * **Struktura** nie jest obsługiwana w definicjach zestawu danych. W przypadku plików w formacie tekstowym Użyj wiersza nagłówka, aby podać nazwę kolumny.
> * Jeśli źródłem wyszukiwania jest plik JSON, `jsonPathDefinition` ustawienie służące do przekształcania obiektu JSON nie jest obsługiwane. Zostaną pobrane wszystkie obiekty.

## <a name="use-the-lookup-activity-result"></a>Użyj wyniku działania Lookup

Wynik wyszukiwania jest zwracany w `output` sekcji wyniku uruchomienia działania.

* **Gdy `firstRowOnly` jest ustawiona na `true` (domyślnie)**, format danych wyjściowych jest tak jak pokazano w poniższym kodzie. Wynik wyszukiwania jest pod stałym `firstRow` kluczem. Aby użyć wyniku w kolejnych działaniach, użyj wzorca  `@{activity('LookupActivity').output.firstRow.table}` .

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "schema":"dbo",
            "table":"Table1"
        }
    }
    ```

* **Gdy `firstRowOnly` jest ustawiona na `false`**, format danych wyjściowych jest tak jak pokazano w poniższym kodzie. `count`Pole wskazuje liczbę rekordów, które są zwracane. Szczegółowe wartości są wyświetlane pod stałą `value` tablicą. W takim przypadku działanie Lookup jest [wykonywane przez działanie foreach](control-flow-for-each-activity.md). Można przekazać `value` tablicę do pola działania ForEach przy `items` użyciu wzorca `@activity('MyLookupActivity').output.value` . Aby uzyskać dostęp do elementów w `value` tablicy, użyj następującej składni: `@{activity('lookupActivity').output.value[zero based index].propertyname}` . Może to być na przykład `@{activity('lookupActivity').output.value[0].schema}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "schema":"dbo",
                "table":"Table1"
            },
            {
                "Id": "2",
                "schema":"dbo",
                "table":"Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>Przykład

W tym przykładzie potok zawiera dwie działania: **Lookup** i **copy**. Działanie kopiowania kopiuje dane z tabeli SQL w wystąpieniu Azure SQL Database do usługi Azure Blob Storage. Nazwa tabeli SQL jest przechowywana w pliku JSON w usłudze BLOB Storage. Działanie Lookup wyszukuje nazwę tabeli w czasie wykonywania. Dane JSON są modyfikowane dynamicznie przy użyciu tego podejścia. Nie trzeba ponownie wdrażać potoków ani zestawów danych. 

Ten przykład ilustruje wyszukiwanie tylko w pierwszym wierszu. W przypadku wyszukiwania dla wszystkich wierszy i łańcucha wyników za pomocą działania ForEach zapoznaj się z przykładami w artykule [kopiowanie wielu tabel luzem przy użyciu Azure Data Factory](tutorial-bulk-copy.md).


### <a name="pipeline"></a>Potok

- Działanie Lookup jest skonfigurowane do korzystania z **LookupDataset**, które odwołuje się do lokalizacji w usłudze Azure Blob Storage. Działanie Lookup odczytuje nazwę tabeli SQL z pliku JSON w tej lokalizacji. 
- Działanie kopiowania używa danych wyjściowych działania Lookup, który jest nazwą tabeli SQL. Właściwość **TableName** w **SourceDataset** jest skonfigurowana tak, aby korzystała z danych wyjściowych działania Lookup. Działanie kopiowania kopiuje dane z tabeli SQL do lokalizacji w usłudze Azure Blob Storage. Lokalizacja jest określana przez właściwość **SinkDataset** . 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "JsonSource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        },
                        "formatSettings": {
                            "type": "JsonReadSettings"
                        }
                    },
                    "dataset": {
                        "referenceName": "LookupDataset",
                        "type": "DatasetReference"
                    },
                    "firstRowOnly": true
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "dependsOn": [
                    {
                        "activity": "LookupActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "AzureSqlSource",
                        "sqlReaderQuery": {
                            "value": "select * from [@{activity('LookupActivity').output.firstRow.schema}].[@{activity('LookupActivity').output.firstRow.table}]",
                            "type": "Expression"
                        },
                        "queryTimeout": "02:00:00",
                        "partitionOption": "None"
                    },
                    "sink": {
                        "type": "DelimitedTextSink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        },
                        "formatSettings": {
                            "type": "DelimitedTextWriteSettings",
                            "quoteAllText": true,
                            "fileExtension": ".txt"
                        }
                    },
                    "enableStaging": false,
                    "translator": {
                        "type": "TabularTranslator",
                        "typeConversion": true,
                        "typeConversionSettings": {
                            "allowDataTruncation": true,
                            "treatBooleanAsNumber": false
                        }
                    }
                },
                "inputs": [
                    {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schemaName": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "tableName": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "SinkDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schema": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "table": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],
        "annotations": [],
        "lastPublishTime": "2020-08-17T10:48:25Z"
    }
}
```

### <a name="lookup-dataset"></a>Zestaw danych wyszukiwania

Zestaw danych **wyszukiwania** to **sourcetable.jsw** pliku w folderze wyszukiwania usługi Azure Storage określonym przez typ **AzureBlobStorageLinkedService** . 

```json
{
    "name": "LookupDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "Json",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "sourcetable.json",
                "container": "lookup"
            }
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>**Źródłowy** zestaw danych dla działania kopiowania

**Źródłowy** zestaw danych używa danych wyjściowych działania Lookup, który jest nazwą tabeli SQL. Działanie kopiowania kopiuje dane z tej tabeli SQL do lokalizacji w usłudze Azure Blob Storage. Lokalizacja jest określana przez zestaw danych **ujścia** . 

```json
{
    "name": "SourceDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureSqlDatabase",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schemaName": {
                "type": "string"
            },
            "tableName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "AzureSqlTable",
        "schema": [],
        "typeProperties": {
            "schema": {
                "value": "@dataset().schemaName",
                "type": "Expression"
            },
            "table": {
                "value": "@dataset().tableName",
                "type": "Expression"
            }
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>Zestaw danych **ujścia** dla działania kopiowania

Działanie kopiowania kopiuje dane z tabeli SQL do pliku **filebylookup.csv** znajdującego się w folderze **CSV** w usłudze Azure Storage. Plik jest określany przez właściwość **AzureBlobStorageLinkedService** . 

```json
{
    "name": "SinkDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schema": {
                "type": "string"
            },
            "table": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@{dataset().schema}_@{dataset().table}.csv",
                    "type": "Expression"
                },
                "container": "csv"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.jsna

W pliku można używać następujących dwóch rodzajów formatów **sourcetable.js** .

#### <a name="set-of-objects"></a>Zestaw obiektów

```json
{
   "Id":"1",
   "schema":"dbo",
   "table":"Table1"
}
{
   "Id":"2",
   "schema":"dbo",
   "table":"Table2"
}
```

#### <a name="array-of-objects"></a>Tablica obiektów

```json
[ 
    {
        "Id": "1",
        "schema":"dbo",
        "table":"Table1"
    },
    {
        "Id": "2",
        "schema":"dbo",
        "table":"Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>Ograniczenia i obejścia

Poniżej przedstawiono niektóre ograniczenia działania wyszukiwania i sugerowanych obejść.

| Ograniczenie | Obejście |
|---|---|
| Działanie Lookup ma maksymalnie 5 000 wierszy i ma maksymalny rozmiar 4 MB. | Zaprojektuj potok dwupoziomowy, w którym potok zewnętrzny iteruje za pośrednictwem wewnętrznego potoku, który pobiera dane, które nie przekraczają maksymalnej liczby wierszy lub rozmiaru. |
| | |

## <a name="next-steps"></a>Następne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez Data Factory: 

- [Działanie wykonywania potoku](control-flow-execute-pipeline-activity.md)
- [Działanie ForEach](control-flow-for-each-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Aktywność sieci Web](control-flow-web-activity.md)
