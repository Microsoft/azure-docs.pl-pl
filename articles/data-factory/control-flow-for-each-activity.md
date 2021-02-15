---
title: Działanie ForEach w Azure Data Factory
description: Dla każdego działania definiuje powtarzający się przepływ sterowania w potoku. Służy do iterowania kolekcji i wykonywania określonych działań.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: c59108752677fc33e28578c3c679be24108806d5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385612"
---
# <a name="foreach-activity-in-azure-data-factory"></a>Działanie ForEach w Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działanie ForEach definiuje powtarzający się przepływ sterowania w potoku. To działanie służy do wykonywania iteracji po kolekcji i wykonuje określone działania w pętli. Implementacja pętli tego działania przypomina strukturę pętli Foreach w językach programowania.

## <a name="syntax"></a>Składnia
Właściwości są opisane w dalszej części tego artykułu. Właściwość Items jest kolekcją, a każdy element w kolekcji jest określany za pomocą instrukcji, `@item()` jak pokazano w następującej składni:  

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

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
name | Nazwa działania for-each. | Ciąg | Tak
typ | Musi być ustawiona na wartość **foreach** | Ciąg | Tak
issekwencyjne | Określa, czy pętla powinna być wykonywana sekwencyjnie, czy równolegle.  Maksymalnie 20 iteracji pętli można wykonać jednocześnie równolegle). Na przykład jeśli masz działanie ForEach iteracji dla działania kopiowania z 10 różnymi źródłami i ujściami zestawów danych z parametrem **Issekwencyjnym** ustawionym na wartość false, wszystkie kopie są wykonywane jednocześnie. Wartość domyślna to false. <br/><br/> Jeśli wartość "issekwencyjne" ma wartość FAŁSZ, upewnij się, że istnieje poprawna konfiguracja do uruchamiania wielu plików wykonywalnych. W przeciwnym razie ta właściwość powinna być stosowana z zachowaniem ostrożności, aby uniknąć ponoszenia konfliktów zapisu. Aby uzyskać więcej informacji, zobacz sekcję [wykonywanie równoległe](#parallel-execution) . | Wartość logiczna | Nie. Wartość domyślna to false.
batchCount | Liczba partii do użycia w celu kontrolowania liczby równoległych wykonań (gdy właściwość issekwencyjne ma wartość false). Jest to górny limit współbieżności, ale działanie for-each nie będzie zawsze wykonywane na tym numerze | Integer (maksimum 50) | Nie. Wartość domyślna to 20.
Elementy | Wyrażenie zwracające tablicę JSON do iteracji. | Wyrażenie (które zwraca tablicę JSON) | Tak
Działania | Działania do wykonania. | Lista działań | Tak

## <a name="parallel-execution"></a>Wykonywanie równoległe
Jeśli parametr **Issekwencyjny** ma wartość false, działanie iteruje równolegle z maksymalnie 20 współbieżnych iteracji. Tego ustawienia należy używać ostrożnie. Jeśli współbieżne iteracje są zapisywane w tym samym folderze, ale do różnych plików, to podejście jest bardziej precyzyjne. Jeśli współbieżne iteracje są zapisywane jednocześnie do dokładnie tego samego pliku, to podejście najprawdopodobniej powoduje wystąpienie błędu. 

## <a name="iteration-expression-language"></a>Język wyrażeń iteracji
W działaniu ForEach Podaj tablicę do iteracji dla **elementów** właściwości. Służy `@item()` do iterowania pojedynczego wyliczenia w działaniu foreach. Na przykład jeśli **element** jest tablicą: [1, 2, 3], `@item()` zwraca 1 w pierwszej iteracji, 2 w drugiej iteracji i 3 w trzeciej iteracji.

## <a name="iterating-over-a-single-activity"></a>Iteracja w ramach pojedynczego działania
**Scenariusz:** Skopiuj z tego samego pliku źródłowego w obiekcie blob platformy Azure do wielu plików docelowych w obiekcie blob platformy Azure.

### <a name="pipeline-definition"></a>Definicja potoku

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

### <a name="blob-dataset-definition"></a>Definicja zestawu danych obiektów BLOB

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

### <a name="run-parameter-values"></a>Wartości parametrów uruchamiania

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>Iterowanie wielu działań
Istnieje możliwość iteracji wielu działań (na przykład: kopiowania i działań w sieci Web) w działaniu ForEach. W tym scenariuszu zalecamy wyodrębnienie wielu działań w osobnym potoku. Następnie można użyć [działania ExecutePipeline](control-flow-execute-pipeline-activity.md) w potoku za pomocą działania ForEach w celu wywołania oddzielnego potoku z wieloma działaniami. 


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
**Scenariusz:** Wykonuje iterację w InnerPipeline w działaniu ForEach z działaniem Execute Pipeline. Wewnętrzne potoki jest kopiowane z definicją schematu sparametryzowane.

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

#### <a name="inner-pipeline-definition"></a>Wewnętrzna definicja potoku

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

#### <a name="source-dataset-definition"></a>Definicja zestawu danych źródłowych

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

#### <a name="master-pipeline-parameters"></a>Główne parametry potoku
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

## <a name="aggregating-outputs"></a>Agregowanie danych wyjściowych

Aby agregować dane wyjściowe działania __foreach__ , użyj _zmiennych_ i _Dołącz_ działanie zmienne.

Najpierw Zadeklaruj `array` _zmienną_ w potoku. Następnie Wywołaj działanie _Dołącz zmienną_ wewnątrz każdej pętli __foreach__ . Następnie można pobrać agregację z tablicy.

## <a name="limitations-and-workarounds"></a>Ograniczenia i obejścia

Poniżej przedstawiono niektóre ograniczenia działania ForEach i sugerowane obejścia.

| Ograniczenie | Obejście |
|---|---|
| Nie można zagnieżdżać pętli ForEach wewnątrz innej pętli ForEach (lub pętli "until"). | Zaprojektuj potok dwupoziomowy, w którym zewnętrzny potok z zewnętrzną pętlą ForEach powtarza się za pośrednictwem wewnętrznego potoku z zagnieżdżoną pętlą. |
| Działanie ForEach ma maksymalnie `batchCount` 50 do przetwarzania równoległego i maksymalnie 100 000 elementów. | Zaprojektuj potok dwupoziomowy, w którym zewnętrzny potok z działaniem ForEach iteruje za pośrednictwem wewnętrznego potoku. |
| | |

## <a name="next-steps"></a>Następne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez Data Factory: 

- [Działanie wykonywania potoku](control-flow-execute-pipeline-activity.md)
- [Działanie pobierania metadanych](control-flow-get-metadata-activity.md)
- [Działanie wyszukiwania](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
