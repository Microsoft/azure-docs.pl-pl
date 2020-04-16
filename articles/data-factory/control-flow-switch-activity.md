---
title: Przełączanie aktywności w fabryce danych platformy Azure
description: Działanie Przełącznik umożliwia sterowanie przepływem przetwarzania na podstawie warunku.
services: data-factory
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/08/2019
ms.openlocfilehash: 4f839de6e276727fa910f91eccc34601cf34f85c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418018"
---
# <a name="switch-activity-in-azure-data-factory"></a>Przełączanie aktywności w fabryce danych platformy Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Switch działania zapewnia taką samą funkcjonalność, że switch instrukcji zapewnia w językach programowania. Ocenia zestaw działań odpowiadających przypadku, który pasuje do oceny stanu.

## <a name="syntax"></a>Składnia

```json

{
   "name": "<Name of the activity>",
    "type": "Switch",
    "typeProperties": {
        "expression": {
            "value": "<expression that evaluates to some string value>",
            "type": "Expression"
        },
        "cases": [
            {
                "value": "<string value that matches expression evaluation>",
                "activities": [
                    {
                        "<Activity 1 definition>"
                    },
                    {
                        "<Activity 2 definition>"
                    },
                    {
                        "<Activity N definition>"
                    }
                ]
            }           
        ],
        "defaultActivities": [
            {
                "<Activity 1 definition>"
            },
            {
                "<Activity 2 definition>"
            },
            {
                "<Activity N definition>"
            }
        ]
    }
}
```

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Dozwolone wartości | Wymagany
-------- | ----------- | -------------- | --------
name | Nazwa działania przełącznika. | Ciąg | Tak
type | Musi być ustawiona na *Przełącznik** | Ciąg | Tak
wyrażenie | Wyrażenie, które musi oceniać wartość ciągu | Wyrażenie z ciągiem typu wynik | Tak
Przypadkach | Zestaw przypadków, które zawierają wartość i zestaw działań do wykonania, gdy wartość jest zgodna z oceną wyrażenia. Musi podać co najmniej jeden przypadek. Istnieje maksymalny limit 25 przypadków. | Tablica obiektów case | Tak
defaultAkcje | Zestaw działań, które są wykonywane, gdy ocena wyrażenia nie jest spełniony. | Tablica działań | Tak

## <a name="example"></a>Przykład

Potok w tym przykładzie kopiuje dane z folderu wejściowego do folderu wyjściowego. Folder wyjściowy jest określany przez wartość parametru potoku: routeSelection.

> [!NOTE]
> Ta sekcja zawiera definicje JSON i przykładowe polecenia programu PowerShell do uruchomienia potoku. Aby uzyskać przewodnik z instrukcjami krok po kroku, aby utworzyć potok fabryki danych przy użyciu definicji programu Azure PowerShell i JSON, zobacz [samouczek: tworzenie fabryki danych przy użyciu programu Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-switch-activity-adfv2quickstartpipelinejson"></a>Potok z działaniem przełącznika (Adfv2QuickStartPipeline.json)

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "MySwitch",
                "type": "Switch",
                "typeProperties": {
                    "expression": {
                        "value": "@pipeline().parameters.routeSelection",
                        "type": "Expression"
                    },
                    "cases": [
                        {
                            "value": "1",
                            "activities": [
                                {
                                    "name": "CopyFromBlobToBlob1",
                                    "type": "Copy",
                                    "inputs": [
                                        {
                                            "referenceName": "BlobDataset",
                                            "parameters": {
                                                "path": "@pipeline().parameters.inputPath"
                                            },
                                            "type": "DatasetReference"
                                        }
                                    ],
                                    "outputs": [
                                        {
                                            "referenceName": "BlobDataset",
                                            "parameters": {
                                                "path": "@pipeline().parameters.outputPath1",
                                            },
                                            "type": "DatasetReference"
                                        }
                                    ],
                                    "typeProperties": {
                                        "source": {
                                            "type": "BlobSource"
                                        },
                                        "sink": {
                                            "type": "BlobSink"
                                        }
                                    }
                                }
                            ]
                        },
                        {
                            "value": "2",    
                                "activities": [
                                    {
                                        "name": "CopyFromBlobToBlob2",
                                        "type": "Copy",
                                        "inputs": [
                                            {
                                                "referenceName": "BlobDataset",
                                                "parameters": {
                                                    "path": "@pipeline().parameters.inputPath",
                                                },
                                                "type": "DatasetReference"
                                            }
                                        ],
                                        "outputs": [
                                            {
                                                "referenceName": "BlobDataset",
                                                "parameters": {
                                                    "path": "@pipeline().parameters.outputPath2",
                                                },
                                                "type": "DatasetReference"
                                            }
                                        ],
                                        "typeProperties": {
                                            "source": {
                                                "type": "BlobSource"
                                            },
                                            "sink": {
                                                "type": "BlobSink"
                                            }
                                        }
                                    }
                                ]
                            },
                            {
                                "value": "3",
                                "activities": [
                                    {
                                        "name": "CopyFromBlobToBlob3",
                                        "type": "Copy",
                                        "inputs": [
                                                {
                                                "referenceName": "BlobDataset",
                                                "parameters": {
                                                    "path": "@pipeline().parameters.inputPath",
                                                },
                                                "type": "DatasetReference"
                                            }
                                        ],
                                        "outputs": [
                                            {
                                                "referenceName": "BlobDataset",
                                                "parameters": {
                                                    "path": "@pipeline().parameters.outputPath3",
                                                },
                                                "type": "DatasetReference"
                                            }
                                        ],
                                        "typeProperties": {
                                            "source": {
                                                "type": "BlobSource"
                                            },
                                            "sink": {
                                                "type": "BlobSink"
                                            }
                                        }
                                    }
                                ]
                        },
                    ],
                    "defaultActivities": []
                }                    
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath1": {
                "type": "String"
            },
            "outputPath2": {
                "type": "String"
            },
            "outputPath3": {
                "type": "String"
            },
            "routeSelection": {
                "type": "String"
            }
        }
    }
}

```

### <a name="azure-storage-linked-service-azurestoragelinkedservicejson"></a>Usługa połączona usługi Azure Storage (AzureStorageLinkedService.json)

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<Azure Storage account name>;AccountKey=<Azure Storage account key>"
        }
    }
}
```

### <a name="parameterized-azure-blob-dataset-blobdatasetjson"></a>Sparametryzowany zestaw danych obiektów blob platformy Azure (blobDataset.json)

Potok ustawia **folderPath** do wartości **outputPath1** lub **outputPath2** parametru potoku. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="pipeline-parameter-json-pipelineparametersjson"></a>Parametr potoku JSON (PipelineParameters.json)

```json
{
    "inputPath": "adftutorial/input",
    "outputPath1": "adftutorial/outputCase1",
    "outputPath2": "adftutorial/outputCase2",
    "outputPath2": "adftutorial/outputCase3",
    "routeSelection": "1"
}
```

### <a name="powershell-commands"></a>Polecenia programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Te polecenia zakładają, że pliki JSON zostały zapisane w folderze: C:\ADF. 

```powershell
Connect-AzAccount
Select-AzSubscription "<Your subscription name>"

$resourceGroupName = "<Resource Group Name>"
$dataFactoryName = "<Data Factory Name. Must be globally unique>";
Remove-AzDataFactoryV2 $dataFactoryName -ResourceGroupName $resourceGroupName -force


Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile "C:\ADF\AzureStorageLinkedService.json"
Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile "C:\ADF\BlobDataset.json"
Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile "C:\ADF\Adfv2QuickStartPipeline.json"
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile C:\ADF\PipelineParameters.json
while ($True) {
    $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

    if ($run) {
        if ($run.Status -ne 'InProgress') {
            Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
            $run
            break
        }
        Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
    }

    Start-Sleep -Seconds 30
}
Write-Host "Activity run details:" -foregroundcolor "Yellow"
$result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
$result

Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
$result.Output -join "`r`n"

Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
$result.Error -join "`r`n"
```

## <a name="next-steps"></a>Następne kroki

Zobacz inne działania przepływu sterowania obsługiwane przez fabrykę danych: 

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie wykonywania potoku](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie pobierania metadanych](control-flow-get-metadata-activity.md)
- [Działanie wyszukiwania](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
