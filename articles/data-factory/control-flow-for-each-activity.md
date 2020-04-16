---
title: ForEach działania w usłudze Azure Data Factory
description: For Each Activity definiuje powtarzający się przepływ sterowania w potoku. Służy do iteracji za waborowania kolekcji i wykonywania określonych działań.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: 35d61e896a395c3044a51780fef72d54c211a31f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417176"
---
# <a name="foreach-activity-in-azure-data-factory"></a>ForEach działania w usłudze Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

ForEach Działania definiuje powtarzający się przepływ sterowania w potoku. To działanie służy do wykonywania iteracji po kolekcji i wykonuje określone działania w pętli. Implementacja pętli tego działania przypomina strukturę pętli Foreach w językach programowania.

## <a name="syntax"></a>Składnia
Właściwości są opisane w dalszej części tego artykułu. Właściwość items jest kolekcją, a każdy element w `@item()` kolekcji jest określany przy użyciu, jak pokazano w następującej składni:  

```json
{  
   "name":"MyForEachActivityName",
   "type":"ForEach",
   "typeProperties":{  
      "isSequential":"true",
        "items": {
            "value": "@pipeline().parameters.mySinkDatasetFolderPathCollection",
            "type": "Expression"
        },
      "activities":[  
         {  
            "name":"MyCopyActivity",
            "type":"Copy",
            "typeProperties":{  
               ...
            },
            "inputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@pipeline().parameters.mySourceDatasetFolderPath"
                  }
               }
            ],
            "outputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@item()"
                  }
               }
            ]
         }
      ]
   }
}

```

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Dozwolone wartości | Wymagany
-------- | ----------- | -------------- | --------
name | Nazwa działania dla każdego. | Ciąg | Tak
type | Musi być ustawiona **na ForEach** | Ciąg | Tak
isSekwencja | Określa, czy pętla ma być wykonywana sekwencyjnie czy równolegle.  Maksymalnie 20 iteracji pętli może być wykonywanych jednocześnie równolegle). Na przykład jeśli masz ForEach działania iteracji nad działaniem kopiowania z 10 różnych źródeł i ujścia zestawów danych z **isSequential** ustawiony na False, wszystkie kopie są wykonywane jednocześnie. Wartość domyślna to False. <br/><br/> Jeśli "isSequential" jest ustawiona na False, upewnij się, że istnieje poprawna konfiguracja do uruchamiania wielu plików wykonywalnych. W przeciwnym razie ta właściwość powinna być używana z ostrożnością, aby uniknąć ponoszenia konfliktów zapisu. Aby uzyskać więcej informacji, zobacz [sekcję wykonywania równoległego.](#parallel-execution) | Wartość logiczna | Nie. Wartość domyślna to False.
liczba partii | Liczba partii, która ma być używana do kontrolowania liczby wykonywania równoległego (gdy isSequential jest ustawiona na false). Jest to górny limit współbieżności, ale dla każdego działania nie zawsze będzie wykonywane pod tym numerem | Liczba całkowita (maksymalnie 50) | Nie. Wartość domyślna to 20.
Items | Wyrażenie, które zwraca tablicy JSON do iteracyjne ponad. | Wyrażenie (które zwraca tablicę JSON) | Tak
Działania | Działania, które mają zostać wykonane. | Lista działań | Tak

## <a name="parallel-execution"></a>Równoległego
Jeśli **isSequential** jest ustawiona na false, działanie iteruje równolegle z maksymalnie 20 równoczesnych iteracji. To ustawienie należy stosować z ostrożnością. Jeśli równoczesnych iteracji są zapisywane w tym samym folderze, ale do różnych plików, to podejście jest w porządku. Jeśli równoczesnych iteracji są zapisywane jednocześnie do dokładnie tego samego pliku, to podejście najprawdopodobniej powoduje błąd. 

## <a name="iteration-expression-language"></a>Język wyrażenia iteracji
W forEach działania, podaj tablicy, które mają być iterowane dla **elementów**właściwości ." Służy `@item()` do iteracji za pomocą jednego wyliczenia w ForEach działania. Na przykład jeśli **elementy** jest tablica: [1, `@item()` 2, 3], zwraca 1 w pierwszej iteracji, 2 w drugiej iteracji i 3 w trzeciej iteracji.

## <a name="iterating-over-a-single-activity"></a>Iteracja nad jednym działaniem
**Scenariusz:** Skopiuj z tego samego pliku źródłowego w usłudze Azure Blob do wielu plików docelowych w obiekcie Blob platformy Azure.

### <a name="pipeline-definition"></a>Definicja rurociągu

```json
{
    "name": "<MyForEachPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyForEachActivity>",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "true",
                    "items": {
                        "value": "@pipeline().parameters.mySinkDatasetFolderPath",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "MyCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": "false"
                                },
                                "sink": {
                                    "type": "BlobSink",
                                    "copyBehavior": "PreserveHierarchy"
                                }
                            },
                            "inputs": [
                                {
                                    "referenceName": "<MyDataset>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@pipeline().parameters.mySourceDatasetFolderPath"
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "MyDataset",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@item()"
                                    }
                                }
                            ]
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "mySourceDatasetFolderPath": {
                "type": "String"
            },
            "mySinkDatasetFolderPath": {
                "type": "String"
            }
        }
    }
}

```

### <a name="blob-dataset-definition"></a>Definicja zestawu danych obiektów blob

```json
{  
   "name":"<MyDataset>",
   "properties":{  
      "type":"AzureBlob",
      "typeProperties":{  
         "folderPath":{  
            "value":"@dataset().MyFolderPath",
            "type":"Expression"
         }
      },
      "linkedServiceName":{  
         "referenceName":"StorageLinkedService",
         "type":"LinkedServiceReference"
      },
      "parameters":{  
         "MyFolderPath":{  
            "type":"String"
         }
      }
   }
}

```

### <a name="run-parameter-values"></a>Uruchamianie wartości parametrów

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>Iteruj nad wieloma działaniami
Jest możliwe do iteracji przez wiele działań (na przykład: kopiowanie i działania sieci web) w ForEach działania. W tym scenariuszu zaleca się wyodrębnić wiele działań w oddzielnym potoku. Następnie można użyć [ExecutePipeline działania](control-flow-execute-pipeline-activity.md) w potoku z ForEach działania do wywołania oddzielnego potoku z wielu działań. 


### <a name="syntax"></a>Składnia

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "<MyForEachMultipleActivities>"
        "typeProperties": {
          "isSequential": true,
          "items": {
            ...
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "name": "<MyInnerPipeline>"
              "typeProperties": {
                "pipeline": {
                  "referenceName": "<copyHttpPipeline>",
                  "type": "PipelineReference"
                },
                "parameters": {
                  ...
                },
                "waitOnCompletion": true
              }
            }
          ]
        }
      }
    ],
    "parameters": {
      ...
    }
  }
}

```

### <a name="example"></a>Przykład
**Scenariusz:** Iteruje za pomocą InnerPipeline w forEach działania z execute pipeline działania. Wewnętrzne kopie potoku z definicjami schematów sparametryzowanymi.

#### <a name="master-pipeline-definition"></a>Definicja potoku głównego

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "MyForEachActivity",
        "typeProperties": {
          "isSequential": true,
          "items": {
            "value": "@pipeline().parameters.inputtables",
            "type": "Expression"
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "typeProperties": {
                "pipeline": {
                  "referenceName": "InnerCopyPipeline",
                  "type": "PipelineReference"
                },
                "parameters": {
                  "sourceTableName": {
                    "value": "@item().SourceTable",
                    "type": "Expression"
                  },
                  "sourceTableStructure": {
                    "value": "@item().SourceTableStructure",
                    "type": "Expression"
                  },
                  "sinkTableName": {
                    "value": "@item().DestTable",
                    "type": "Expression"
                  },
                  "sinkTableStructure": {
                    "value": "@item().DestTableStructure",
                    "type": "Expression"
                  }
                },
                "waitOnCompletion": true
              },
              "name": "ExecuteCopyPipeline"
            }
          ]
        }
      }
    ],
    "parameters": {
      "inputtables": {
        "type": "Array"
      }
    }
  }
}

```

#### <a name="inner-pipeline-definition"></a>Definicja rurociągu wewnętrznego

```json
{
  "name": "InnerCopyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            }
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "name": "CopyActivity",
        "inputs": [
          {
            "referenceName": "sqlSourceDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sourceTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sourceTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sqlSinkDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sinkTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sinkTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceTableName": {
        "type": "String"
      },
      "sourceTableStructure": {
        "type": "String"
      },
      "sinkTableName": {
        "type": "String"
      },
      "sinkTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="source-dataset-definition"></a>Definicja źródłowego zestawu danych

```json
{
  "name": "sqlSourceDataset",
  "properties": {
    "type": "SqlServerTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "sqlserverLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="sink-dataset-definition"></a>Definicja zestawu danych ujścia

```json
{
  "name": "sqlSinkDataSet",
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "azureSqlLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="master-pipeline-parameters"></a>Parametry potoku głównego
```json
{
    "inputtables": [
        {
            "SourceTable": "department",
            "SourceTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ],
            "DestTable": "department2",
            "DestTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ]
        }
    ]
    
}
```

## <a name="aggregating-outputs"></a>Agregowanie produktów

Aby zagregować dane wyjściowe działania __foreach,__ należy wykorzystać _zmienne_ i _dołącz zmienne_ działania.

Najpierw zadeklarować `array` _zmienną_ w potoku. Następnie wywołać _Dołącz zmiennej_ działania wewnątrz każdej pętli __foreach.__ Następnie można pobrać agregacji z tablicy.

## <a name="limitations-and-workarounds"></a>Ograniczenia i obejścia

Oto kilka ograniczeń forEach działania i sugerowane obejścia.

| Ograniczenia | Obejście |
|---|---|
| Nie można zagnieżdżać ForEach pętli wewnątrz innej forEach pętli (lub Do pętli). | Zaprojektuj dwupoziomowy potok, w którym zewnętrzny potok zewnętrzną pętlą ForEach iteruje przez wewnętrzny potok z zagnieżdżoną pętlą. |
| ForEach Działania ma `batchCount` maksymalnie 50 do przetwarzania równoległego i maksymalnie 100.000 elementów. | Zaprojektuj potok dwupoziomowy, w którym zewnętrzny potok z forEach działania iteruje za pomocą potoku wewnętrznego. |
| | |

## <a name="next-steps"></a>Następne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez fabrykę danych: 

- [Działanie wykonywania potoku](control-flow-execute-pipeline-activity.md)
- [Działanie pobierania metadanych](control-flow-get-metadata-activity.md)
- [Działanie wyszukiwania](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
