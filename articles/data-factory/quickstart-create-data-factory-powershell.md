---
title: Kopiowanie danych w Blob Storage przy użyciu Azure Data Factory
description: Utwórz Azure Data Factory przy użyciu programu PowerShell, aby skopiować dane z jednej lokalizacji w usłudze Azure Blob Storage do innej lokalizacji.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: quickstart
ms.date: 04/10/2020
ms.author: jingwang
ms.openlocfilehash: a7fcb4be47e0e1e62c190a9b089243a178df8e7a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013367"
---
# <a name="quickstart-create-an-azure-data-factory-using-powershell"></a>Szybki Start: Tworzenie Azure Data Factory przy użyciu programu PowerShell

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Bieżąca wersja](quickstart-create-data-factory-powershell.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym przewodniku szybki start opisano sposób tworzenia Azure Data Factory przy użyciu programu PowerShell. Potok tworzony w tej fabryce danych **kopiuje** dane z jednego folderu do innego folderu w usłudze Azure Blob Storage. Aby zapoznać się z samouczkiem dotyczącym **przekształcania** danych przy użyciu Azure Data Factory, zobacz [Samouczek: Przekształcanie danych przy użyciu platformy Spark](transform-data-using-spark.md).

> [!NOTE]
> Ten artykuł nie zawiera szczegółowego wprowadzenia do usługi Data Factory. Aby zapoznać się z wprowadzeniem do usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)]

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zainstaluj najnowsze moduły Azure PowerShell, postępując zgodnie z instrukcjami w temacie [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/install-Az-ps).

#### <a name="log-in-to-powershell"></a>Logowanie do programu PowerShell

1. Uruchom program **PowerShell** na maszynie. Nie zamykaj programu PowerShell aż do końca tego samouczka Szybki start. Jeśli go zamkniesz i otworzysz ponownie, musisz uruchomić te polecenia jeszcze raz.

2. Uruchom poniższe polecenie i wprowadź tę samą nazwę użytkownika platformy Azure oraz hasło, których używasz do logowania się w witrynie Azure Portal:

    ```powershell
    Connect-AzAccount
    ```

3. Uruchom poniższe polecenie, aby wyświetlić wszystkie subskrypcje dla tego konta:

    ```powershell
    Get-AzSubscription
    ```

4. Jeśli z kontem jest skojarzonych wiele subskrypcji, uruchom poniższe polecenie, aby wybrać subskrypcję, z którą chcesz pracować. Zastąp **Identyfikator subskrypcji ID** subskrypcji platformy Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Zdefiniuj zmienną nazwy grupy zasobów, której użyjesz później w poleceniach programu PowerShell. Skopiuj poniższy tekst polecenia do programu PowerShell, podaj nazwę [grupy zasobów platformy Azure](../azure-resource-manager/management/overview.md) w podwójnych cudzysłowach, a następnie uruchom polecenie. Na przykład: `"ADFQuickStartRG"`.

     ```powershell
    $resourceGroupName = "ADFQuickStartRG";
    ```

    Jeśli grupa zasobów już istnieje, możesz zrezygnować z jej zastąpienia. Przypisz inną wartość do zmiennej `$ResourceGroupName` i ponownie uruchom polecenie.

2. Aby utworzyć grupę zasobów platformy Azure, uruchom następujące polecenie:

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'East US'
    ```

    Jeśli grupa zasobów już istnieje, możesz zrezygnować z jej zastąpienia. Przypisz inną wartość do zmiennej `$ResourceGroupName` i ponownie uruchom polecenie.

3. Zdefiniuj zmienną nazwy fabryki danych. 

    > [!IMPORTANT]
    >  Zaktualizuj nazwę fabryki danych, aby była unikatowa w skali globalnej, na przykład ADFTutorialFactorySP1127.

    ```powershell
    $dataFactoryName = "ADFQuickStartFactory";
    ```

4. Aby utworzyć fabrykę danych, uruchom następujące polecenie cmdlet **Set-AzDataFactoryV2** , używając właściwości Location i ResourceGroupName ze zmiennej $ResGrp:

    ```powershell
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
        -Location $ResGrp.Location -Name $dataFactoryName
    ```

Pamiętaj o następujących kwestiach:

* Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony następujący błąd, zmień nazwę i spróbuj ponownie.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli **współautora** lub **właściciela** albo **administratorem** subskrypcji platformy Azure.

* Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.


## <a name="create-a-linked-service"></a>Tworzenie usługi połączonej

Utwórz połączone usługi w fabryce danych w celu połączenia swoich magazynów danych i usług obliczeniowych z fabryką danych. W tym samouczku Szybki start utworzysz połączoną usługę Azure Storage, która będzie używana zarówno jako źródło, jak i ujście magazynu. Połączona usługa ma informacje o połączeniu, których usługa Data Factory używa w środowisku uruchomieniowym do nawiązywania z nią połączenia.

>[!TIP]
>W tym przewodniku szybki start użyjesz *klucza konta* jako typu uwierzytelniania dla magazynu danych, ale możesz wybrać inne obsługiwane metody uwierzytelniania: *Identyfikator URI sygnatury dostępu współdzielonego*,*nazwę główną usługi* i *tożsamość zarządzaną* , jeśli jest to konieczne. Aby uzyskać szczegółowe informacje, zapoznaj się z odpowiednimi sekcjami w [tym artykule](./connector-azure-blob-storage.md#linked-service-properties) .
>Aby bezpiecznie przechowywać wpisy tajne dla magazynów danych, zaleca się również korzystanie z Azure Key Vault. Szczegółowe ilustracje znajdują się w [tym artykule](./store-credentials-in-key-vault.md) .

1. Utwórz plik JSON o nazwie **AzureStorageLinkedService.json** w folderze **C:\ADFv2QuiclStartPSH** o następującej zawartości: (Utwórz folder ADFv2QuickStartPSH, jeśli jeszcze nie istnieje).

    > [!IMPORTANT]
    > Przed zapisaniem pliku zastąp wartości &lt;accountName&gt; i &lt;accountKey&gt; nazwą i kluczem konta usługi Azure Storage.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "annotations": [],
            "type": "AzureBlobStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net"
            }
        }
    }
    ```

    Jeśli używasz programu Notatnik, wybierz pozycję **Wszystkie pliki** na liście **Zapisz jako typ** w oknie dialogowym **Zapisywanie jako**. W przeciwnym razie do pliku może zostać dodane rozszerzenie `.txt`. Na przykład `AzureStorageLinkedService.json.txt`. W przypadku utworzenia pliku w Eksploratorze plików przed jego otwarciem w programie Notatnik rozszerzenie `.txt` może nie być widoczne, ponieważ opcja **Ukryj rozszerzenia znanych typów plików** jest domyślnie ustawiona. Przed przejściem do następnego kroku usuń rozszerzenie `.txt`.

2. W programie **PowerShell** przejdź do folderu **ADFv2QuickStartPSH**.

    ```powershell
    Set-Location 'C:\ADFv2QuickStartPSH'
    ```

3. Uruchom polecenie cmdlet **Set-AzDataFactoryV2LinkedService** , aby utworzyć połączoną usługę: **AzureStorageLinkedService**.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureStorageLinkedService" `
        -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobStorageLinkedService
    ```

## <a name="create-datasets"></a>Tworzenie zestawów danych

W tej procedurze tworzone są dwa zestawy danych: **InputDataset** i **OutputDataset**. Te zestawy danych są typu **Binary**. Odwołują się one do połączonej usługi Azure Storage utworzonej w poprzedniej sekcji.
Wejściowy zestaw danych reprezentuje dane źródłowe w folderze wejściowym. W definicji wejściowego zestawu danych określany jest kontener obiektów blob (**adftutorial**), folder (**input**) i plik (**emp.txt**), który zawiera dane źródłowe.
Wyjściowy zestaw danych reprezentuje dane, które są kopiowane do lokalizacji docelowej. W definicji wyjściowego zestawu danych określany jest kontener obiektów blob (**adftutorial**), folder (**output**) i plik, do którego kopiowane są dane. 
1. Utwórz plik JSON o nazwie **InputDataset.js** w folderze **C:\ADFv2QuickStartPSH** o następującej zawartości:

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "annotations": [],
            "type": "Binary",
            "typeProperties": {
                "location": {
                    "type": "AzureBlobStorageLocation",
                    "fileName": "emp.txt",
                    "folderPath": "input",
                    "container": "adftutorial"
                }
            }
        }
    }
    ```

2. Aby utworzyć zestaw danych: **InputDataset**, uruchom polecenie cmdlet **Set-AzDataFactoryV2Dataset** .

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "InputDataset" `
        -DefinitionFile ".\InputDataset.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```console
    DatasetName       : InputDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.BinaryDataset
    ```

3. Powtórz kroki, aby utworzyć wyjściowy zestaw danych. Utwórz plik JSON o nazwie **OutputDataset.js** w folderze **C:\ADFv2QuickStartPSH** o następującej zawartości:

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "annotations": [],
            "type": "Binary",
            "typeProperties": {
                "location": {
                    "type": "AzureBlobStorageLocation",
                    "folderPath": "output",
                    "container": "adftutorial"
                }
            }
        }
    }
    ```

4. Uruchom polecenie cmdlet **Set-AzDataFactoryV2Dataset** , aby utworzyć zestaw **danych**.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "OutputDataset" `
        -DefinitionFile ".\OutputDataset.json"
    ```

    Oto przykładowe dane wyjściowe:

    ```console
    DatasetName       : OutputDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.BinaryDataset
    ```
## <a name="create-a-pipeline"></a>Tworzenie potoku

W tej procedurze utworzysz potok za pomocą działania kopiowania, które korzysta z wejściowych i wyjściowych zestawów danych. Działanie kopiowania służy do kopiowania danych z pliku określonego w ustawieniach wejściowego zestawu danych do pliku określonego w ustawieniach wyjściowego zestawu danych.  

1. Utwórz plik JSON o nazwie **Adfv2QuickStartPipeline.json** w folderze **C:\Adfv2QuickStartPSH** o następującej zawartości:

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
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
                            "type": "BinarySource",
                            "storeSettings": {
                                "type": "AzureBlobStorageReadSettings",
                                "recursive": true
                            }
                        },
                        "sink": {
                            "type": "BinarySink",
                            "storeSettings": {
                                "type": "AzureBlobStorageWriteSettings"
                            }
                        },
                        "enableStaging": false
                    },
                    "inputs": [
                        {
                            "referenceName": "InputDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "OutputDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ],
            "annotations": []
        }
    }
    ```

2. Aby utworzyć potok: **Adfv2QuickStartPipeline**, uruchom polecenie cmdlet **Set-AzDataFactoryV2Pipeline** .

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline `
        -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName `
        -Name "Adfv2QuickStartPipeline" `
        -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

## <a name="create-a-pipeline-run"></a>Tworzenie uruchomienia potoku

W tym kroku utworzysz uruchomienie potoku.

Uruchom polecenie cmdlet **Invoke-AzDataFactoryV2Pipeline** , aby utworzyć uruchomienie potoku. Polecenie cmdlet zwraca identyfikator uruchomienia potoku w celu monitorowania w przyszłości.

  ```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline `
    -DataFactoryName $DataFactory.DataFactoryName `
    -ResourceGroupName $ResGrp.ResourceGroupName `
    -PipelineName $DFPipeLine.Name 
```

## <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

1. Uruchom następujący skrypt programu PowerShell, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych. Skopiuj/wklej poniższy skrypt w oknie programu PowerShell i naciśnij klawisz ENTER.

    ```powershell
    while ($True) {
        $Run = Get-AzDataFactoryV2PipelineRun `
            -ResourceGroupName $ResGrp.ResourceGroupName `
            -DataFactoryName $DataFactory.DataFactoryName `
            -PipelineRunId $RunId

        if ($Run) {
            if ($run.Status -ne 'InProgress') {
                Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
                $Run
                break
            }
            Write-Output "Pipeline is running...status: InProgress"
        }

        Start-Sleep -Seconds 10
    }
    ```

    Oto przykładowe dane wyjściowe uruchomienia potoku:

    ```console
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFQuickStartRG
    DataFactoryName   : ADFQuickStartFactory
    RunId             : 00000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 8/27/2019 7:23:07 AM
    Parameters        : {}
    RunStart          : 8/27/2019 7:22:56 AM
    RunEnd            : 8/27/2019 7:23:07 AM
    DurationInMs      : 11324
    Status            : Succeeded
    Message           : 
    ```
2. Uruchom następujący skrypt, aby pobrać szczegóły uruchomienia działania kopiowania, na przykład rozmiar odczytanych/zapisanych danych.

    ```powershell
    Write-Output "Activity run details:"
    $Result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineRunId $RunId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $Result

    Write-Output "Activity 'Output' section:"
    $Result.Output -join "`r`n"

    Write-Output "Activity 'Error' section:"
    $Result.Error -join "`r`n"
    ```
3. Upewnij się, że wyświetlone dane wyjściowe są podobne do następujących przykładowych danych wyjściowych uruchomienia działania:

    ```console
    ResourceGroupName : ADFQuickStartRG
    DataFactoryName   : ADFQuickStartFactory
    ActivityRunId     : 00000000-0000-0000-0000-000000000000
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink, enableStaging}
    Output            : {dataRead, dataWritten, filesRead, filesWritten...}
    LinkedServiceName :
    ActivityRunStart  : 8/27/2019 7:22:58 AM
    ActivityRunEnd    : 8/27/2019 7:23:05 AM
    DurationInMs      : 6828
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 20
    "dataWritten": 20
    "filesRead": 1
    "filesWritten": 1
    "sourcePeakConnections": 1
    "sinkPeakConnections": 1
    "copyDuration": 4
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (Central US)"
    "usedDataIntegrationUnits": 4
    "usedParallelCopies": 1
    "executionDetails": [
      {
        "source": {
          "type": "AzureBlobStorage"
        },
        "sink": {
          "type": "AzureBlobStorage"
        },
        "status": "Succeeded",
        "start": "2019-08-27T07:22:59.1045645Z",
        "duration": 4,
        "usedDataIntegrationUnits": 4,
        "usedParallelCopies": 1,
        "detailedDurations": {
          "queuingDuration": 3,
          "transferDuration": 1
        }
      }
    ]
    
    Activity 'Error' section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "CopyFromBlobToBlob"
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. Zapoznaj się z [samouczkami](tutorial-copy-data-dot-net.md), aby dowiedzieć się więcej o korzystaniu z usługi Data Factory w dalszych scenariuszach.
