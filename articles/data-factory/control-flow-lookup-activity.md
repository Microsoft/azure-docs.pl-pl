---
title: Działanie Lookup w Azure Data Factory
description: Dowiedz się, jak używać działania Lookup do wyszukiwania wartości z zewnętrznego źródła. Do tych danych wyjściowych można uzyskać dalsze odwołania przez działania zakończone powodzeniem.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 02abdaf46ca2af6c96d3b5e8d4ce5876831bd415
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81418005"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Działanie Lookup w Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działanie Lookup może pobrać zestaw danych z dowolnych ze źródeł danych obsługiwanych przez Azure Data Factory. Użyj go w następującym scenariuszu:
- Dynamiczne określanie, które obiekty mają być używane w kolejnym działaniu, zamiast twardej kodowania nazwy obiektu. Niektóre przykłady obiektów to pliki i tabele.

Działanie Lookup odczytuje i zwraca zawartość pliku konfiguracji lub tabeli. Zwraca również wynik wykonania zapytania lub procedury składowanej. Wyniki działania Lookup można użyć w kolejnych działaniach kopiowania lub przekształcania, jeśli jest to wartość singleton. Dane wyjściowe mogą być używane w działaniu ForEach, jeśli jest tablicą atrybutów.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Dla działania Lookup są obsługiwane następujące źródła danych. Największa liczba wierszy, które mogą być zwracane przez działanie Lookup, to 5 000, o rozmiarze do 2 MB. Obecnie najdłuższy czas trwania działania wyszukiwania przed upływem limitu czasu wynosi godzinę.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Składnia

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>Właściwości typu

Nazwa | Opis | Typ | Wymagane?
---- | ----------- | ---- | --------
zestawu | Zawiera odwołanie do zestawu danych dla wyszukiwania. Pobierz szczegóły z sekcji **Właściwości zestawu danych** w każdym odpowiednim artykule łącznika. | Pary klucz wartość | Tak
source | Zawiera właściwości źródła specyficzne dla zestawu danych, takie same jak źródło działania kopiowania. Pobierz szczegóły z sekcji **właściwości działania kopiowania** w każdym odpowiednim artykule łącznika. | Pary klucz wartość | Tak
firstRowOnly | Wskazuje, czy zwrócić tylko pierwszy wiersz, czy wszystkie wiersze. | Boolean | Nie. Wartość domyślna to `true`.

> [!NOTE]
> 
> * Kolumny źródłowe z typem **ByteArray** nie są obsługiwane.
> * **Struktura** nie jest obsługiwana w definicjach zestawu danych. W przypadku plików w formacie tekstowym Użyj wiersza nagłówka, aby podać nazwę kolumny.
> * Jeśli źródłem wyszukiwania jest plik JSON, `jsonPathDefinition` ustawienie służące do przekształcania obiektu JSON nie jest obsługiwane. Zostaną pobrane wszystkie obiekty.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Użyj wyniku działania Lookup w kolejnym działaniu

Wynik wyszukiwania jest zwracany w `output` sekcji wyniku uruchomienia działania.

* **Gdy `firstRowOnly` jest ustawiona na `true` (domyślnie)**, format danych wyjściowych jest tak jak pokazano w poniższym kodzie. Wynik wyszukiwania jest pod stałym `firstRow` kluczem. Aby użyć wyniku w kolejnych działaniach, użyj wzorca `@{activity('MyLookupActivity').output.firstRow.TableName}` .

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Gdy `firstRowOnly` jest ustawiona na `false` **, format danych wyjściowych jest tak jak pokazano w poniższym kodzie. `count`Pole wskazuje liczbę rekordów, które są zwracane. Szczegółowe wartości są wyświetlane pod stałą `value` tablicą. W takim przypadku działanie Lookup jest [wykonywane przez działanie foreach](control-flow-for-each-activity.md). Można przekazać `value` tablicę do pola działania ForEach przy `items` użyciu wzorca `@activity('MyLookupActivity').output.value` . Aby uzyskać dostęp do elementów w `value` tablicy, użyj następującej składni: `@{activity('lookupActivity').output.value[zero based index].propertyname}` . Może to być na przykład `@{activity('lookupActivity').output.value[0].tablename}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

### <a name="copy-activity-example"></a>Przykład działania kopiowania
W tym przykładzie działanie Copy kopiuje dane z tabeli SQL w wystąpieniu Azure SQL Database do usługi Azure Blob Storage. Nazwa tabeli SQL jest przechowywana w pliku JSON w usłudze BLOB Storage. Działanie Lookup wyszukuje nazwę tabeli w czasie wykonywania. Dane JSON są modyfikowane dynamicznie przy użyciu tego podejścia. Nie trzeba ponownie wdrażać potoków ani zestawów danych. 

Ten przykład ilustruje wyszukiwanie tylko w pierwszym wierszu. W przypadku wyszukiwania dla wszystkich wierszy i łańcucha wyników za pomocą działania ForEach zapoznaj się z przykładami w artykule [kopiowanie wielu tabel luzem przy użyciu Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Potok
Ten potok zawiera dwie działania: Lookup i Copy. 

- Działanie Lookup jest skonfigurowane do korzystania z **LookupDataset**, które odwołuje się do lokalizacji w usłudze Azure Blob Storage. Działanie Lookup odczytuje nazwę tabeli SQL z pliku JSON w tej lokalizacji. 
- Działanie kopiowania używa danych wyjściowych działania Lookup, które jest nazwą tabeli SQL. Właściwość **TableName** w **SourceDataset** jest skonfigurowana tak, aby korzystała z danych wyjściowych działania Lookup. Działanie kopiowania kopiuje dane z tabeli SQL do lokalizacji w usłudze Azure Blob Storage. Lokalizacja jest określana przez właściwość **SinkDataset** . 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Zestaw danych wyszukiwania
Zestaw danych **wyszukiwania** to **sourcetable.jsw** pliku w folderze wyszukiwania usługi Azure Storage określonym przez typ **AzureStorageLinkedService** . 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
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
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>Zestaw danych **ujścia** dla działania kopiowania
Działanie kopiowania kopiuje dane z tabeli SQL do pliku **filebylookup.csv** znajdującego się w folderze **CSV** w usłudze Azure Storage. Plik jest określany przez właściwość **AzureStorageLinkedService** . 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
To konto magazynu zawiera plik JSON z nazwami tabel SQL. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>"
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Połączona usługa Azure SQL Database
To wystąpienie Azure SQL Database zawiera dane, które mają zostać skopiowane do magazynu obiektów BLOB. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;"
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.jsna

#### <a name="set-of-objects"></a>Zestaw obiektów

```json
{
  "Id": "1",
  "tableName": "Table1"
}
{
   "Id": "2",
  "tableName": "Table2"
}
```

#### <a name="array-of-objects"></a>Tablica obiektów

```json
[ 
    {
        "Id": "1",
        "tableName": "Table1"
    },
    {
        "Id": "2",
        "tableName": "Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>Ograniczenia i obejścia

Poniżej przedstawiono niektóre ograniczenia działania wyszukiwania i sugerowanych obejść.

| Ograniczenie | Obejście |
|---|---|
| Działanie Lookup ma maksymalnie 5 000 wierszy i ma maksymalny rozmiar wynoszący 2 MB. | Zaprojektuj potok dwupoziomowy, w którym potok zewnętrzny iteruje za pośrednictwem wewnętrznego potoku, który pobiera dane, które nie przekraczają maksymalnej liczby wierszy lub rozmiaru. |
| | |

## <a name="next-steps"></a>Następne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez Data Factory: 

- [Działanie wykonywania potoku](control-flow-execute-pipeline-activity.md)
- [Działanie ForEach](control-flow-for-each-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Aktywność sieci Web](control-flow-web-activity.md)
