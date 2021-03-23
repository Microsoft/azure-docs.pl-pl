---
title: Działanie wykonywania potoku w Azure Data Factory
description: Dowiedz się, jak za pomocą działania Execute Pipeline wywołać jeden Data Factory potoku z innego potoku Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: a24bd6988a9bc8f15723ebf931f2751a09c42e26
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783748"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Działanie wykonywania potoku w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działanie Execute Pipeline umożliwia potokowi usługi Data Factory wywoływanie innego potoku.



## <a name="syntax"></a>Składnia

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
name | Nazwa działania wykonywania potoku. | Ciąg | Tak
typ | Musi być ustawiona na: **ExecutePipeline**. | Ciąg | Tak
proces | Odwołanie do potoku do potoku zależnego, który wywołuje ten potok. Obiekt odwołania potoku ma dwie właściwości: **ReferenceName** i **Type**. Właściwość ReferenceName określa nazwę potoku odwołania. Właściwość Type musi być ustawiona na wartość PipelineReference. | PipelineReference | Tak
parameters | Parametry, które mają zostać przesłane do wywoływanego potoku | Obiekt JSON, który mapuje nazwy parametrów na wartości argumentów | Nie
waitOnCompletion | Określa, czy wykonanie działania czeka na zakończenie zależnego wykonania potoku. Wartość domyślna to false. | Wartość logiczna | Nie

## <a name="sample"></a>Przykład
Ten scenariusz ma dwa potoki:

- **Potok główny** — ten potok ma jedno działanie Execute Pipeline, które wywołuje wywoływany potok. Potok główny przyjmuje dwa parametry: `masterSourceBlobContainer` , `masterSinkBlobContainer` .
- **Wywołano potok** — ten potok ma jedno działanie kopiowania, które kopiuje dane ze źródła obiektów blob platformy Azure do ujścia obiektów blob platformy Azure. Wywoływany potok przyjmuje dwa parametry: `sourceBlobContainer` , `sinkBlobContainer` .

### <a name="master-pipeline-definition"></a>Definicja potoku głównego

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobContainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>Wywołana definicja potoku

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

**Połączona usługa**

```json
{
    "name": "BlobStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=*****;AccountKey=*****"
    }
  }
}
```

**Zestaw danych źródłowych**
```json
{
    "name": "SourceBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sourceBlobContainer",
        "type": "Expression"
      },
      "fileName": "salesforce.txt"
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

**Zestaw danych będący ujściem**
```json
{
    "name": "sinkBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sinkBlobContainer",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

### <a name="running-the-pipeline"></a>Uruchamianie potoku

Do uruchomienia głównego potoku w tym przykładzie są przesyłane następujące wartości parametrów masterSourceBlobContainer i masterSinkBlobContainer: 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

Potok główny przekazuje te wartości do wywoływanego potoku, jak pokazano w następującym przykładzie: 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobContainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>Następne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez Data Factory: 

- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie pobierania metadanych](control-flow-get-metadata-activity.md)
- [Działanie wyszukiwania](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
