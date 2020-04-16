---
title: Działanie odnośnika w fabryce danych platformy Azure
description: Dowiedz się, jak używać działania odnośnego do wyszukiwania wartości ze źródła zewnętrznego. Dane wyjściowe mogą być dalej odwoływane przez kolejne działania.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418005"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Działanie odnośnika w fabryce danych platformy Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działanie odnośnika można pobrać zestaw danych z dowolnego źródła danych obsługiwanych przez usługę Azure Data Factory. Użyj go w następującym scenariuszu:
- Dynamicznie określać, które obiekty mają działać w kolejnych działaniach, zamiast twardego kodowania nazwy obiektu. Niektóre przykłady obiektów to pliki i tabele.

Działanie odnośnika odczytuje i zwraca zawartość pliku konfiguracyjnego lub tabeli. Zwraca również wynik wykonywania kwerendy lub procedury składowanej. Dane wyjściowe z działania odnośnika mogą być używane w kolejnym działaniu kopiowania lub przekształcania, jeśli jest to wartość pojedynczego. Dane wyjściowe mogą być używane w ForEach działania, jeśli jest to tablica atrybutów.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Następujące źródła danych są obsługiwane dla działania odnośnika. Największa liczba wierszy, które mogą być zwracane przez działanie odnośnika jest 5000, do 2 MB w rozmiarze. Obecnie najdłuższy czas trwania działania odnośnika przed przesuwem czasu wynosi jedną godzinę.

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

Nazwa | Opis | Typ | Wymagana?
---- | ----------- | ---- | --------
Dataset | Zawiera odwołanie do zestawu danych dla wyszukiwania. Pobierz szczegóły z sekcji **Właściwości zestawu danych** w każdym odpowiednim artykule łącznika. | Pary klucz wartość | Tak
source | Zawiera właściwości źródła specyficzne dla zestawu danych, takie same jak źródło działania kopiowania. Pobierz szczegóły z sekcji **Właściwości działania kopiowania** w każdym odpowiednim artykule łącznika. | Pary klucz wartość | Tak
firstRowOnly | Wskazuje, czy ma być zwracany tylko pierwszy wiersz, czy wszystkie wiersze. | Wartość logiczna | Nie. Wartość domyślna to `true`.

> [!NOTE]
> 
> * Kolumny źródłowe z typem **ByteArray** nie są obsługiwane.
> * **Struktura** nie jest obsługiwana w definicjach zestawów danych. W przypadku plików w formacie tekstowym użyj wiersza nagłówka, aby podać nazwę kolumny.
> * Jeśli źródłem wyszukiwania jest plik JSON, `jsonPathDefinition` ustawienie przekształcania obiektu JSON nie jest obsługiwane. Zostaną pobrane całe obiekty.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Użyj wyniku działania odnośnika w kolejnym działaniu

Wynik odnośnika jest `output` zwracany w sekcji wyniku uruchomienia działania.

* **Gdy `firstRowOnly` jest `true` ustawiona na (domyślnie),** format wyjściowy jest pokazany w poniższym kodzie. Wynik wyszukiwania znajduje się `firstRow` pod kluczem stałym. Aby użyć wyniku w kolejnych działaniach, użyj wzorca `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Gdy `firstRowOnly` jest `false`ustawiona na **, format wyjściowy jest pokazany w poniższym kodzie. Pole `count` wskazuje, ile rekordów jest zwracanych. Szczegółowe wartości są wyświetlane `value` w stałej tablicy. W takim przypadku po działaniu odnośnika następuje [działanie Foreach](control-flow-for-each-activity.md). Tablicę `value` należy przekazać do pola `items` Działania ForEach `@activity('MyLookupActivity').output.value`przy użyciu wzorca programu . Aby uzyskać dostęp `value` do elementów w tablicy, należy użyć następującej składni: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Może to być na przykład `@{activity('lookupActivity').output.value[0].tablename}`.

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
W tym przykładzie działanie kopiowania kopiuje dane z tabeli SQL w wystąpieniu bazy danych SQL platformy Azure do magazynu obiektów blob platformy Azure. Nazwa tabeli SQL jest przechowywana w pliku JSON w magazynie obiektów Blob. Działanie odnośnika wyszukuje nazwę tabeli w czasie wykonywania. JSON jest modyfikowany dynamicznie przy użyciu tego podejścia. Nie trzeba ponownie rozmieszczać potoków lub zestawów danych. 

W tym przykładzie pokazano wyszukiwanie tylko dla pierwszego wiersza. Aby uzyskać wyszukiwanie dla wszystkich wierszy i łańcuch wyników z ForEach działania, zobacz przykłady w [Kopiowanie wielu tabel zbiorczo przy użyciu usługi Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Potok
Ten potok zawiera dwa działania: Odnośnik i Kopiuj. 

- Działanie odnośnika jest skonfigurowany do korzystania z **Zestawu odnośnych,** który odnosi się do lokalizacji w magazynie obiektów Blob platformy Azure. Działanie odnośnika odczytuje nazwę tabeli SQL z pliku JSON w tej lokalizacji. 
- Działanie kopiowania używa danych wyjściowych działania odnośnika, który jest nazwą tabeli SQL. Właściwość **tableName** w **zestawie SourceDataset** jest skonfigurowana do używania danych wyjściowych z działania odnośnika. Skopiuj działanie kopiuje dane z tabeli SQL do lokalizacji w magazynie obiektów Blob platformy Azure. Lokalizacja jest określona przez **SinkDataset** właściwości. 

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

### <a name="lookup-dataset"></a>Zestaw danych odnośnika
Zestaw danych **wyszukiwania** jest źródłowym **plikiem.json** w folderze odnośników usługi Azure Storage określonym przez typ **AzureStorageLinkedService.** 

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
**Źródłowy** zestaw danych używa danych wyjściowych działania odnośnika, która jest nazwą tabeli SQL. Skopiuj działanie kopiuje dane z tej tabeli SQL do lokalizacji w magazynie obiektów Blob platformy Azure. Lokalizacja jest określona przez zestaw danych **ujścia.** 

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
Skopiuj działanie kopiuje dane z tabeli SQL do pliku **filebylookup.csv** w folderze **csv** w usłudze Azure Storage. Plik jest określony przez **AzureStorageLinkedService** właściwości. 

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
To wystąpienie bazy danych SQL usługi Azure zawiera dane, które mają zostać skopiowane do magazynu obiektów Blob. 

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

### <a name="sourcetablejson"></a>źródło.json

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

Oto kilka ograniczeń działania odnośnika i sugerowane obejścia.

| Ograniczenia | Obejście |
|---|---|
| Działanie odnośnika ma maksymalnie 5000 wierszy i maksymalny rozmiar 2 MB. | Zaprojektuj potok dwupoziomowy, w którym potok zewnętrzny iteruje zaobiega zaostrzoną w potoku wewnętrznym, który pobiera dane, które nie przekraczają maksymalnej liczby wierszy lub rozmiaru. |
| | |

## <a name="next-steps"></a>Następne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez fabrykę danych: 

- [Wykonywanie działania potoku](control-flow-execute-pipeline-activity.md)
- [Działanie ForEach](control-flow-for-each-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Aktywność w sieci Web](control-flow-web-activity.md)
