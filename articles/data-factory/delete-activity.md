---
title: Działanie usuwania w usłudze Azure Data Factory
description: Dowiedz się, jak usuwać pliki w różnych magazynach plików przy użyciu działania usuwania w Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: 3021d29f472dbbf43ae53981287b1f4676e8f932
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392718"
---
# <a name="delete-activity-in-azure-data-factory"></a>Działanie usuwania w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Możesz użyć działania Usuń w Azure Data Factory, aby usunąć pliki lub foldery z lokalnych magazynów magazynów lub magazynów w chmurze. To działanie służy do czyszczenia lub archiwizowania plików, gdy nie są już potrzebne.

> [!WARNING]
> Nie można przywrócić usuniętych plików lub folderów (chyba że magazyn ma włączone usuwanie nietrwałe). Należy zachować ostrożność przy usuwaniu plików lub folderów za pomocą działania usuwania.

## <a name="best-practices"></a>Najlepsze rozwiązania

Poniżej przedstawiono kilka zaleceń dotyczących używania działania usuwania:

-   Wykonaj kopię zapasową plików przed usunięciem ich z działania usuwania, jeśli chcesz przywrócić je w przyszłości.

-   Upewnij się, że Data Factory ma uprawnienia do zapisu w celu usunięcia folderów lub plików z magazynu magazynu.

-   Upewnij się, że pliki, które są zapisywane w tym samym czasie, nie są usuwane. 

-   Jeśli chcesz usunąć pliki lub foldery z systemu lokalnego, upewnij się, że używasz własnego środowiska Integration Runtime z wersją nowszą niż 3,14.

## <a name="supported-data-stores"></a>Obsługiwane magazyny danych

-   [Azure Blob Storage](connector-azure-blob-storage.md)
-   [Usługa Azure Data Lake Storage 1. generacji](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)
-   [System plików](connector-file-system.md)
-   [FTP](connector-ftp.md)
-   [SFTP](connector-sftp.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [Google Cloud Storage](connector-google-cloud-storage.md)
-   [HDFS](connector-hdfs.md)

## <a name="syntax"></a>Składnia

```json
{
    "name": "DeleteActivity",
    "type": "Delete",
    "typeProperties": {
        "dataset": {
            "referenceName": "<dataset name>",
            "type": "DatasetReference"
        },
        "storeSettings": {
            "type": "<source type>",
            "recursive": true/false,
            "maxConcurrentConnections": <number>
        },
        "enableLogging": true/false,
        "logStorageSettings": {
            "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference"
            },
            "path": "<path to save log file>"
        }
    }
}
```

## <a name="type-properties"></a>Właściwości typu

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| zestawu | Zawiera odwołanie do zestawu danych w celu określenia plików lub folderów do usunięcia | Tak |
| rozpoznawania | Wskazuje, czy pliki są usuwane rekursywnie z podfolderów, czy tylko z określonego folderu.  | Nie. Wartość domyślna to `false`. |
| maxConcurrentConnections | Liczba połączeń do równoczesnego połączenia z magazynem magazynów w celu usunięcia folderów lub plików.   |  Nie. Wartość domyślna to `1`. |
| enablelogging | Wskazuje, czy konieczne jest zapisanie nazw folderów lub plików, które zostały usunięte. W przypadku wartości true należy dodatkowo podać konto magazynu, aby zapisać plik dziennika, aby można było śledzić zachowania działania usuwania, odczytując plik dziennika. | Nie |
| logStorageSettings | Dotyczy tylko gdy EnableLogging = true.<br/><br/>Grupa właściwości magazynu, które można określić w miejscu, w którym chcesz zapisać plik dziennika zawierający nazwy folderów lub plików, które zostały usunięte przez działanie usuwania. | Nie |
| linkedServiceName | Dotyczy tylko gdy EnableLogging = true.<br/><br/>Połączona usługa [Azure Storage](connector-azure-blob-storage.md#linked-service-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#linked-service-properties)lub [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) do przechowywania pliku dziennika zawierającego nazwy folderów lub plików, które zostały usunięte przez działanie usuwania. Należy pamiętać, że musi być skonfigurowany przy użyciu tego samego typu Integration Runtime z tego, który jest używany przez działanie Delete do usuwania plików. | Nie |
| path | Dotyczy tylko gdy EnableLogging = true.<br/><br/>Ścieżka zapisu pliku dziennika na koncie magazynu. Jeśli nie podasz ścieżki, usługa utworzy dla Ciebie kontener. | Nie |

## <a name="monitoring"></a>Monitorowanie

Istnieją dwa miejsca, w których można zobaczyć i monitorować wyniki działania usuwania: 
-   Z danych wyjściowych działania usuwania.
-   Z pliku dziennika.

### <a name="sample-output-of-the-delete-activity"></a>Przykładowe dane wyjściowe działania usuwania

```json
{ 
  "datasetName": "AmazonS3",
  "type": "AmazonS3Object",
  "prefix": "test",
  "bucketName": "adf",
  "recursive": true,
  "isWildcardUsed": false,
  "maxConcurrentConnections": 2,  
  "filesDeleted": 4,
  "logPath": "https://sample.blob.core.windows.net/mycontainer/5c698705-a6e2-40bf-911e-e0a927de3f07",
  "effectiveIntegrationRuntime": "MyAzureIR (West Central US)",
  "executionDuration": 650
}
```

### <a name="sample-log-file-of-the-delete-activity"></a>Przykładowy plik dziennika działania usuwania

| Nazwa | Kategoria | Stan | Błąd |
|:--- |:--- |:--- |:--- |
| TEST1/yyy.jswłączone | Plik | Usunięte |  |
| TEST2/hello789.txt | Plik | Usunięte |  |
| TEST2/test3/hello000.txt | Plik | Usunięte |  |
| TEST2/test3/zzz.jsna | Plik | Usunięte |  |

## <a name="examples-of-using-the-delete-activity"></a>Przykłady użycia działania usuwania

### <a name="delete-specific-folders-or-files"></a>Usuwanie określonych folderów lub plików

Magazyn ma następującą strukturę folderów:

Pierwiastek<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt

Teraz używasz działania usuwania do usuwania folderu lub plików przez połączenie różnych wartości właściwości z zestawu danych i działania usuwania:

| folderPath | fileName | rozpoznawania | Dane wyjściowe |
|:--- |:--- |:--- |:--- |
| Główny/Folder_A_2 | NULL | Fałsz | Pierwiastek<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Główny/Folder_A_2 | NULL | Prawda | Pierwiastek<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_A_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_1/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>7.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |
| Główny/Folder_A_2 | *. txt | Fałsz | Pierwiastek<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Główny/Folder_A_2 | *. txt | Prawda | Pierwiastek<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |

### <a name="periodically-clean-up-the-time-partitioned-folder-or-files"></a>Okresowo oczyszczanie folderu lub plików na partycje czasowej

Można utworzyć potok w celu okresowego czyszczenia pliku lub folderu podzielonego na partycje.  Na przykład struktura folderów jest podobna do: `/mycontainer/2018/12/14/*.csv` .  Można użyć zmiennej systemowej ADF z wyzwalacza harmonogramu, aby zidentyfikować foldery lub pliki, które mają zostać usunięte w każdym uruchomieniu potoku. 

#### <a name="sample-pipeline"></a>Przykładowy potok

```json
{
    "name":"cleanup_time_partitioned_folder",
    "properties":{
        "activities":[
            {
                "name":"DeleteOneFolder",
                "type":"Delete",
                "dependsOn":[

                ],
                "policy":{
                    "timeout":"7.00:00:00",
                    "retry":0,
                    "retryIntervalInSeconds":30,
                    "secureOutput":false,
                    "secureInput":false
                },
                "userProperties":[

                ],
                "typeProperties":{
                    "dataset":{
                        "referenceName":"PartitionedFolder",
                        "type":"DatasetReference",
                        "parameters":{
                            "TriggerTime":{
                                "value":"@formatDateTime(pipeline().parameters.TriggerTime, 'yyyy/MM/dd')",
                                "type":"Expression"
                            }
                        }
                    },
                    "logStorageSettings":{
                        "linkedServiceName":{
                            "referenceName":"BloblinkedService",
                            "type":"LinkedServiceReference"
                        },
                        "path":"mycontainer/log"
                    },
                    "enableLogging":true,
                    "storeSettings":{
                        "type":"AzureBlobStorageReadSettings",
                        "recursive":true
                    }
                }
            }
        ],
        "parameters":{
            "TriggerTime":{
                "type":"string"
            }
        },
        "annotations":[

        ]
    }
}
```

#### <a name="sample-dataset"></a>Przykładowy zestaw danych

```json
{
    "name":"PartitionedFolder",
    "properties":{
        "linkedServiceName":{
            "referenceName":"BloblinkedService",
            "type":"LinkedServiceReference"
        },
        "parameters":{
            "TriggerTime":{
                "type":"string"
            }
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "folderPath":{
                    "value":"@dataset().TriggerTime",
                    "type":"Expression"
                },
                "container":{
                    "value":"mycontainer",
                    "type":"Expression"
                }
            }
        }
    }
}
```

#### <a name="sample-trigger"></a>Przykładowy wyzwalacz

```json
{
    "name": "DailyTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "cleanup_time_partitioned_folder",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "TriggerTime": "@trigger().scheduledTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Day",
                "interval": 1,
                "startTime": "2018-12-13T00:00:00.000Z",
                "timeZone": "UTC",
                "schedule": {
                    "minutes": [
                        59
                    ],
                    "hours": [
                        23
                    ]
                }
            }
        }
    }
}
```

### <a name="clean-up-the-expired-files-that-were-last-modified-before-201811"></a>Wyczyść wygasłe pliki, które zostały ostatnio zmodyfikowane przed 2018.1.1

Można utworzyć potok w celu oczyszczenia starych lub wygasłych plików, wykorzystując Filtr atrybutów pliku: "LastModified" w zestawie danych.  

#### <a name="sample-pipeline"></a>Przykładowy potok

```json
{
    "name":"CleanupExpiredFiles",
    "properties":{
        "activities":[
            {
                "name":"DeleteFilebyLastModified",
                "type":"Delete",
                "dependsOn":[

                ],
                "policy":{
                    "timeout":"7.00:00:00",
                    "retry":0,
                    "retryIntervalInSeconds":30,
                    "secureOutput":false,
                    "secureInput":false
                },
                "userProperties":[

                ],
                "typeProperties":{
                    "dataset":{
                        "referenceName":"BlobFilesLastModifiedBefore201811",
                        "type":"DatasetReference"
                    },
                    "logStorageSettings":{
                        "linkedServiceName":{
                            "referenceName":"BloblinkedService",
                            "type":"LinkedServiceReference"
                        },
                        "path":"mycontainer/log"
                    },
                    "enableLogging":true,
                    "storeSettings":{
                        "type":"AzureBlobStorageReadSettings",
                        "recursive":true,
                        "modifiedDatetimeEnd":"2018-01-01T00:00:00.000Z"
                    }
                }
            }
        ],
        "annotations":[

        ]
    }
}
```

#### <a name="sample-dataset"></a>Przykładowy zestaw danych

```json
{
    "name":"BlobFilesLastModifiedBefore201811",
    "properties":{
        "linkedServiceName":{
            "referenceName":"BloblinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":"*",
                "folderPath":"mydirectory",
                "container":"mycontainer"
            }
        }
    }
}
```

### <a name="move-files-by-chaining-the-copy-activity-and-the-delete-activity"></a>Przenoszenie plików przez łańcuch działania kopiowania i działanie usuwania

Plik można przenieść za pomocą działania kopiowania, aby skopiować plik, a następnie usunąć działanie, aby usunąć plik w potoku.  Jeśli chcesz przenieść wiele plików, możesz użyć działania getmetadatas + Filter Activity + foreach Activity + aktywność Copy + Delete, jak w poniższym przykładzie:

> [!NOTE]
> Jeśli chcesz przenieść cały folder przez zdefiniowanie zestawu danych zawierającego tylko ścieżkę do folderu, a następnie za pomocą działania kopiowania i działania Usuń, aby odwołać się do tego samego zestawu danych reprezentującego folder, musisz zachować ostrożność. Jest to spowodowane tym, że trzeba upewnić się, że nie będzie nowych plików dołączanych do folderu między operacją kopiowania a operacją usuwania.  Jeśli w folderze znajdują się nowe pliki docierające do folderu, gdy działanie kopiowania po prostu zostało zakończone, ale działanie usuwania nie zostało jeszcze jaśniejsze, możliwe jest, że działanie usuwania usunie ten nowy plik, który nie został skopiowany do miejsca docelowego, usuwając cały folder. 

#### <a name="sample-pipeline"></a>Przykładowy potok

```json
{
    "name":"MoveFiles",
    "properties":{
        "activities":[
            {
                "name":"GetFileList",
                "type":"GetMetadata",
                "dependsOn":[

                ],
                "policy":{
                    "timeout":"7.00:00:00",
                    "retry":0,
                    "retryIntervalInSeconds":30,
                    "secureOutput":false,
                    "secureInput":false
                },
                "userProperties":[

                ],
                "typeProperties":{
                    "dataset":{
                        "referenceName":"OneSourceFolder",
                        "type":"DatasetReference",
                        "parameters":{
                            "Container":{
                                "value":"@pipeline().parameters.SourceStore_Location",
                                "type":"Expression"
                            },
                            "Directory":{
                                "value":"@pipeline().parameters.SourceStore_Directory",
                                "type":"Expression"
                            }
                        }
                    },
                    "fieldList":[
                        "childItems"
                    ],
                    "storeSettings":{
                        "type":"AzureBlobStorageReadSettings",
                        "recursive":true
                    },
                    "formatSettings":{
                        "type":"BinaryReadSettings"
                    }
                }
            },
            {
                "name":"FilterFiles",
                "type":"Filter",
                "dependsOn":[
                    {
                        "activity":"GetFileList",
                        "dependencyConditions":[
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties":[

                ],
                "typeProperties":{
                    "items":{
                        "value":"@activity('GetFileList').output.childItems",
                        "type":"Expression"
                    },
                    "condition":{
                        "value":"@equals(item().type, 'File')",
                        "type":"Expression"
                    }
                }
            },
            {
                "name":"ForEachFile",
                "type":"ForEach",
                "dependsOn":[
                    {
                        "activity":"FilterFiles",
                        "dependencyConditions":[
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties":[

                ],
                "typeProperties":{
                    "items":{
                        "value":"@activity('FilterFiles').output.value",
                        "type":"Expression"
                    },
                    "batchCount":20,
                    "activities":[
                        {
                            "name":"CopyAFile",
                            "type":"Copy",
                            "dependsOn":[

                            ],
                            "policy":{
                                "timeout":"7.00:00:00",
                                "retry":0,
                                "retryIntervalInSeconds":30,
                                "secureOutput":false,
                                "secureInput":false
                            },
                            "userProperties":[

                            ],
                            "typeProperties":{
                                "source":{
                                    "type":"BinarySource",
                                    "storeSettings":{
                                        "type":"AzureBlobStorageReadSettings",
                                        "recursive":false,
                                        "deleteFilesAfterCompletion":false
                                    },
                                    "formatSettings":{
                                        "type":"BinaryReadSettings"
                                    },
                                    "recursive":false
                                },
                                "sink":{
                                    "type":"BinarySink",
                                    "storeSettings":{
                                        "type":"AzureBlobStorageWriteSettings"
                                    }
                                },
                                "enableStaging":false,
                                "dataIntegrationUnits":0
                            },
                            "inputs":[
                                {
                                    "referenceName":"OneSourceFile",
                                    "type":"DatasetReference",
                                    "parameters":{
                                        "Container":{
                                            "value":"@pipeline().parameters.SourceStore_Location",
                                            "type":"Expression"
                                        },
                                        "Directory":{
                                            "value":"@pipeline().parameters.SourceStore_Directory",
                                            "type":"Expression"
                                        },
                                        "filename":{
                                            "value":"@item().name",
                                            "type":"Expression"
                                        }
                                    }
                                }
                            ],
                            "outputs":[
                                {
                                    "referenceName":"OneDestinationFile",
                                    "type":"DatasetReference",
                                    "parameters":{
                                        "Container":{
                                            "value":"@pipeline().parameters.DestinationStore_Location",
                                            "type":"Expression"
                                        },
                                        "Directory":{
                                            "value":"@pipeline().parameters.DestinationStore_Directory",
                                            "type":"Expression"
                                        },
                                        "filename":{
                                            "value":"@item().name",
                                            "type":"Expression"
                                        }
                                    }
                                }
                            ]
                        },
                        {
                            "name":"DeleteAFile",
                            "type":"Delete",
                            "dependsOn":[
                                {
                                    "activity":"CopyAFile",
                                    "dependencyConditions":[
                                        "Succeeded"
                                    ]
                                }
                            ],
                            "policy":{
                                "timeout":"7.00:00:00",
                                "retry":0,
                                "retryIntervalInSeconds":30,
                                "secureOutput":false,
                                "secureInput":false
                            },
                            "userProperties":[

                            ],
                            "typeProperties":{
                                "dataset":{
                                    "referenceName":"OneSourceFile",
                                    "type":"DatasetReference",
                                    "parameters":{
                                        "Container":{
                                            "value":"@pipeline().parameters.SourceStore_Location",
                                            "type":"Expression"
                                        },
                                        "Directory":{
                                            "value":"@pipeline().parameters.SourceStore_Directory",
                                            "type":"Expression"
                                        },
                                        "filename":{
                                            "value":"@item().name",
                                            "type":"Expression"
                                        }
                                    }
                                },
                                "logStorageSettings":{
                                    "linkedServiceName":{
                                        "referenceName":"BloblinkedService",
                                        "type":"LinkedServiceReference"
                                    },
                                    "path":"container/log"
                                },
                                "enableLogging":true,
                                "storeSettings":{
                                    "type":"AzureBlobStorageReadSettings",
                                    "recursive":true
                                }
                            }
                        }
                    ]
                }
            }
        ],
        "parameters":{
            "SourceStore_Location":{
                "type":"String"
            },
            "SourceStore_Directory":{
                "type":"String"
            },
            "DestinationStore_Location":{
                "type":"String"
            },
            "DestinationStore_Directory":{
                "type":"String"
            }
        },
        "annotations":[

        ]
    }
}
```

#### <a name="sample-datasets"></a>Przykładowe zestawy danych

Zestaw danych używany przez działanie GetMetadata do wyliczania listy plików.

```json
{
    "name":"OneSourceFolder",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "parameters":{
            "Container":{
                "type":"String"
            },
            "Directory":{
                "type":"String"
            }
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "folderPath":{
                    "value":"@{dataset().Directory}",
                    "type":"Expression"
                },
                "container":{
                    "value":"@{dataset().Container}",
                    "type":"Expression"
                }
            }
        }
    }
}
```

Zestaw danych dla źródła danych używanego przez działanie kopiowania i działanie usuwania.

```json
{
    "name":"OneSourceFile",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "parameters":{
            "Container":{
                "type":"String"
            },
            "Directory":{
                "type":"String"
            },
            "filename":{
                "type":"string"
            }
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":{
                    "value":"@dataset().filename",
                    "type":"Expression"
                },
                "folderPath":{
                    "value":"@{dataset().Directory}",
                    "type":"Expression"
                },
                "container":{
                    "value":"@{dataset().Container}",
                    "type":"Expression"
                }
            }
        }
    }
}
```

Zestaw danych dla miejsca docelowego danych używany przez działanie kopiowania.

```json
{
    "name":"OneDestinationFile",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "parameters":{
            "Container":{
                "type":"String"
            },
            "Directory":{
                "type":"String"
            },
            "filename":{
                "type":"string"
            }
        },
        "annotations":[

        ],
        "type":"Binary",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":{
                    "value":"@dataset().filename",
                    "type":"Expression"
                },
                "folderPath":{
                    "value":"@{dataset().Directory}",
                    "type":"Expression"
                },
                "container":{
                    "value":"@{dataset().Container}",
                    "type":"Expression"
                }
            }
        }
    }
}
```

Możesz również pobrać szablon, aby przenieść pliki z tego [miejsca](solution-template-move-files.md).

## <a name="known-limitation"></a>Znane ograniczenie

-   Działanie usuwania nie obsługuje usuwania listy folderów opisanych przez symbol wieloznaczny.

-   W przypadku korzystania z filtru atrybutów plików w działaniu usuwania: modifiedDatetimeStart i modifiedDatetimeEnd aby wybrać pliki do usunięcia, upewnij się, że ustawienie "wildcardFileName": "*" w działaniu Usuń również.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o przenoszeniu plików w Azure Data Factory.

-   [Narzędzie do kopiowanie danych w usłudze Azure Data Factory](copy-data-tool.md)
