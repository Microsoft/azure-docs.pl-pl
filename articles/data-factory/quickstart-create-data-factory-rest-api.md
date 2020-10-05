---
title: Tworzenie fabryki danych Azure przy użyciu interfejsu API REST
description: Utwórz potok usługi Azure Data Factory, aby skopiować dane z jednej lokalizacji w usłudze Azure Blob Storage do innej lokalizacji.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 06/10/2019
ms.author: jingwang
ms.openlocfilehash: 1d1db69215294ac4aa4849bbaa1a886a91f0ba7e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89439167"
---
# <a name="quickstart-create-an-azure-data-factory-and-pipeline-by-using-the-rest-api"></a>Szybki start: Tworzenie fabryki danych platformy Azure i potoku przy użyciu interfejsu API REST

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Bieżąca wersja](quickstart-create-data-factory-rest-api.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory to oparta na chmurze usługa integracji danych, za pomocą której możesz tworzyć oparte na danych przepływy pracy w chmurze służące do organizowania oraz automatyzowania przenoszenia i przekształcania danych. Za pomocą Azure Data Factory można tworzyć i planować przepływy pracy oparte na danych (nazywane potokami), które mogą pozyskiwanie danych z różnych magazynów danych, przetwarzanie/Przekształcanie danych przy użyciu usług obliczeniowych, takich jak Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics i Azure Machine Learning, oraz publikowanie danych wyjściowych w magazynach danych, takich jak Azure Synapse Analytics (dawniej SQL Data Warehouse) dla aplikacji analizy biznesowej do użycia.

Ten samouczek Szybki start opisuje sposób używania interfejsu API REST w celu utworzenia usługi Azure Data Factory. Potok w tej usłudze Data Factory kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji, możesz utworzyć konto [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Storage**. Magazyn obiektów blob jest używany jako magazyn **źródła** i **ujścia** danych. Jeśli nie masz konta usługi Azure Storage, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu](../storage/common/storage-account-create.md).
* Utwórz **kontener obiektów blob** w usłudze Blob Storage, utwórz **folder** wejściowy w kontenerze i przekaż niektóre pliki do folderu. Za pomocą narzędzi, takich jak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) , można nawiązać połączenie z usługą Azure Blob Storage, utworzyć kontener obiektów blob, przekazać plik wejściowy i zweryfikować plik wyjściowy.
* Zainstaluj **Azure PowerShell**. Wykonaj instrukcje podane w temacie [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-Az-ps). Ten samouczek Szybki start używa programu PowerShell do wywoływania interfejsu API REST.
* **Utwórz aplikację w usłudze Azure Active Directory**, wykonując [tę instrukcję](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Zwróć uwagę na następujące wartości, których można użyć w kolejnych krokach: **Identyfikator aplikacji**, **clientSecrets**i **Identyfikator dzierżawy**. Przypisz aplikację do roli „**Współautor**”.

## <a name="set-global-variables"></a>Ustawianie zmiennych globalnych

1. Uruchom program **PowerShell**. Nie zamykaj programu Azure PowerShell aż do końca tego samouczka Szybki start. Jeśli go zamkniesz i otworzysz ponownie, musisz uruchomić te polecenia jeszcze raz.

    Uruchom poniższe polecenie i wprowadź nazwę użytkownika oraz hasło, których używasz do logowania się w witrynie Azure Portal:

    ```powershell
    Connect-AzAccount
    ```
    Uruchom poniższe polecenie, aby wyświetlić wszystkie subskrypcje dla tego konta:

    ```powershell
    Get-AzSubscription
    ```
    Uruchom poniższe polecenie, aby wybrać subskrypcję, z którą chcesz pracować. Zastąp **Identyfikator subskrypcji ID** subskrypcji platformy Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Uruchom następujące polecenia, zastępując symbole zastępcze własnymi wartościami, aby ustawić zmienne globalne, które zostaną użyte w kolejnych krokach.

    ```powershell
    $tenantID = "<your tenant ID>"
    $appId = "<your application ID>"
    $clientSecrets = "<your clientSecrets for the application>"
    $subscriptionId = "<your subscription ID to create the factory>"
    $resourceGroupName = "<your resource group to create the factory>"
    $factoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $apiVersion = "2018-06-01"
    ```

## <a name="authenticate-with-azure-ad"></a>Uwierzytelnianie za pomocą usługi Azure AD

Uruchom następujące polecenia w celu uwierzytelniania za pomocą usługi Azure Active Directory (AAD):

```powershell
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]"https://login.microsoftonline.com/${tenantId}"
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($appId, $clientSecrets)
$result = $AuthContext.AcquireTokenAsync("https://management.core.windows.net/", $cred).GetAwaiter().GetResult()
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Uruchom następujące polecenia, aby utworzyć fabrykę danych:

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}?api-version=${apiVersion}"
$body = @"
{
    "name": "$dataFactoryName",
    "location": "East US",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Pamiętaj o następujących kwestiach:

* Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony następujący błąd, zmień nazwę i spróbuj ponownie.

    ```
    Data factory name "ADFv2QuickStartDataFactory" is not available.
    ```
* Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.

Oto przykładowa odpowiedź:

```json
{  
    "name":"<dataFactoryName>",
    "identity":{  
        "type":"SystemAssigned",
        "principalId":"<service principal ID>",
        "tenantId":"<tenant ID>"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{  
        "provisioningState":"Succeeded",
        "createTime":"2019-09-03T02:10:27.056273Z",
        "version":"2018-06-01"
    },
    "eTag":"\"0200c876-0000-0100-0000-5d6dcb930000\"",
    "location":"East US",
    "tags":{  

    }
}
```
## <a name="create-linked-services"></a>Tworzenie połączonych usług

Połączone usługi tworzy się w fabryce danych w celu połączenia magazynów danych i usług obliczeniowych z fabryką danych. W tym samouczku Szybki start musisz utworzyć tylko jedną połączoną usługę Azure Storage zarówno jako źródło kopii, jak i ujście magazynu o nazwie „AzureStorageLinkedService” w przykładzie.

Uruchom następujące polecenia, aby utworzyć połączoną usługę o nazwie **AzureStorageLinkedService**:

Przed wykonaniem poleceń zastąp wartości &lt;accountName&gt; i &lt;accountKey&gt; nazwą i kluczem konta usługi Azure Storage.

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/linkedservices/AzureStorageLinkedService?api-version=${apiVersion}"
$body = @"
{  
    "name":"AzureStorageLinkedService",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Oto przykładowe dane wyjściowe:

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/linkedservices/AzureStorageLinkedService",
    "name":"AzureStorageLinkedService",
    "type":"Microsoft.DataFactory/factories/linkedservices",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"DefaultEndpointsProtocol=https;AccountName=<accountName>;"
        }
    },
    "etag":"07011a57-0000-0100-0000-5d6e14a20000"
}
```
## <a name="create-datasets"></a>Tworzenie zestawów danych

Zdefiniuj zestaw danych, który reprezentuje dane do skopiowania ze źródła do ujścia. W tym przykładzie utworzysz dwa zestawy danych: InputDataset i OutputDataset. Odwołują się one do połączonej usługi Azure Storage utworzonej w poprzedniej sekcji. Wejściowy zestaw danych reprezentuje dane źródłowe w folderze wejściowym. W definicji wejściowego zestawu danych określany jest kontener obiektów blob (adftutorial), folder (input) i plik (emp.txt), który zawiera dane źródłowe. Wyjściowy zestaw danych reprezentuje dane, które są kopiowane do lokalizacji docelowej. W definicji wyjściowego zestawu danych określany jest kontener obiektów blob (adftutorial), folder (output) i plik, do którego kopiowane są dane.

**Utwórz InputDataset**

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/InputDataset?api-version=${apiVersion}"
$body = @"
{  
    "name":"InputDataset",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "fileName":"emp.txt",
                "folderPath":"input",
                "container":"adftutorial"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Oto przykładowe dane wyjściowe:

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/InputDataset",
    "name":"InputDataset",
    "type":"Microsoft.DataFactory/factories/datasets",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":"@{type=AzureBlobStorageLocation; fileName=emp.txt; folderPath=input; container=adftutorial}"
        }
    },
    "etag":"07011c57-0000-0100-0000-5d6e14b40000"
}
```
**Utwórz OutputDataset**

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/OutputDataset?api-version=${apiVersion}"
$body = @"
{  
    "name":"OutputDataset",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "folderPath":"output",
                "container":"adftutorial"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Oto przykładowe dane wyjściowe:

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/OutputDataset",
    "name":"OutputDataset",
    "type":"Microsoft.DataFactory/factories/datasets",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":"@{type=AzureBlobStorageLocation; folderPath=output; container=adftutorial}"
        }
    },
    "etag":"07013257-0000-0100-0000-5d6e18920000"
}
```
## <a name="create-pipeline"></a>Tworzenie potoku

W niniejszym przykładzie ten potok zawiera jedno działanie i pobiera dwa parametry — ścieżkę wejściowego obiektu blob i ścieżkę wyjściowego obiektu blob. Wartości tych parametrów są ustawiane w chwili wyzwolenia/uruchomienia potoku. Działanie kopiowania dotyczy tego samego zestawu danych obiektu blob, który został utworzony w poprzednim kroku jako wejście i wyjście. W przypadku użycia zestawu danych jako zestawu danych wejściowych określana jest ścieżka wejściowa. Natomiast w przypadku użycia zestawu danych jako zestawu danych wyjściowych określana jest ścieżka wyjściowa.

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelines/Adfv2QuickStartPipeline?api-version=${apiVersion}"
$body = @"
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
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Oto przykładowe dane wyjściowe:

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/pipelines/Adfv2QuickStartPipeline",
    "name":"Adfv2QuickStartPipeline",
    "type":"Microsoft.DataFactory/factories/pipelines",
    "properties":{  
        "activities":[  
            "@{name=CopyFromBlobToBlob; type=Copy; dependsOn=System.Object[]; policy=; userProperties=System.Object[]; typeProperties=; inputs=System.Object[]; outputs=System.Object[]}"
        ],
        "annotations":[  

        ]
    },
    "etag":"07012057-0000-0100-0000-5d6e14c00000"
}
```

## <a name="create-pipeline-run"></a>Tworzenie uruchomienia potoku

W tym kroku ustawisz wartości parametrów **inputPath** i **outputPath** określonych w potoku za pomocą rzeczywistych wartości ścieżek źródła i ujścia obiektu blob oraz wyzwolisz uruchomienie potoku. Identyfikator uruchomienia potoku zwracany w treści odpowiedzi jest używany później do monitorowania interfejsu API.

Zastąp wartość **inputPath** i **outputPath** ścieżką źródłową i ujścia obiektu BLOB, aby skopiować dane z i do przed zapisaniem pliku.


```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartPipeline/createRun?api-version=${apiVersion}"
$response = Invoke-RestMethod -Method POST -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
$runId = $response.runId
```

Oto przykładowe dane wyjściowe:

```json
{  
    "runId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc"
}
```

## <a name="monitor-pipeline"></a>Monitorowanie potoku

1. Uruchom następujący skrypt, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```

    Oto przykładowe dane wyjściowe:

    ```json
    {  
        "runId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc",
        "debugRunId":null,
        "runGroupId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc",
        "pipelineName":"Adfv2QuickStartPipeline",
        "parameters":{  

        },
        "invokedBy":{  
            "id":"2bb3938176ee43439752475aa12b2251",
            "name":"Manual",
            "invokedByType":"Manual"
        },
        "runStart":"2019-09-03T07:22:47.0075159Z",
        "runEnd":"2019-09-03T07:22:57.8862692Z",
        "durationInMs":10878,
        "status":"Succeeded",
        "message":"",
        "lastUpdated":"2019-09-03T07:22:57.8862692Z",
        "annotations":[  

        ],
        "runDimension":{  

        },
        "isLatest":true
    }
    ```

2. Uruchom następujący skrypt, aby pobrać szczegóły uruchomienia działania kopiowania, na przykład rozmiar odczytanych/zapisanych danych.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelineruns/${runId}/queryActivityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method POST -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```
    Oto przykładowe dane wyjściowe:

    ```json
    {  
        "value":[  
            {  
                "activityRunEnd":"2019-09-03T07:22:56.6498704Z",
                "activityName":"CopyFromBlobToBlob",
                "activityRunStart":"2019-09-03T07:22:49.0719311Z",
                "activityType":"Copy",
                "durationInMs":7577,
                "retryAttempt":null,
                "error":"@{errorCode=; message=; failureType=; target=CopyFromBlobToBlob}",
                "activityRunId":"32951886-814a-4d6b-b82b-505936e227cc",
                "iterationHash":"",
                "input":"@{source=; sink=; enableStaging=False}",
                "linkedServiceName":"",
                "output":"@{dataRead=20; dataWritten=20; filesRead=1; filesWritten=1; sourcePeakConnections=1; sinkPeakConnections=1; copyDuration=4; throughput=0.01; errors=System.Object[]; effectiveIntegrationRuntime=DefaultIntegrationRuntime (Central US); usedDataIntegrationUnits=4; usedParallelCopies=1; executionDetails=System.Object[]}",
                "userProperties":"",
                "pipelineName":"Adfv2QuickStartPipeline",
                "pipelineRunId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc",
                "status":"Succeeded",
                "recoveryStatus":"None",
                "integrationRuntimeNames":"defaultintegrationruntime",
                "executionDetails":"@{integrationRuntime=System.Object[]}"
            }
        ]
    }
    ```
## <a name="verify-the-output"></a>Sprawdzanie danych wyjściowych

Użyj Eksploratora usługi Azure Storage, aby sprawdzić, czy plik jest kopiowany do "outputPath" z "inputPath", jak określono podczas tworzenia uruchomienia potoku.

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Zasoby, które zostały utworzone w ramach tego przewodnika Szybki start, możesz wyczyścić na dwa sposoby. Możesz usunąć [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) zawierającą wszystkie zasoby w tej grupie. Jeśli chcesz zachować inne zasoby bez zmian, usuń tylko fabrykę danych utworzoną w tym samouczku.

Uruchom poniższe polecenie, aby usunąć całą grupę zasobów:
```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Uruchom następujące polecenie, aby usunąć tylko fabrykę danych:

```powershell
Remove-AzDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. Zapoznaj się z [samouczkami](tutorial-copy-data-dot-net.md), aby dowiedzieć się więcej o korzystaniu z usługi Data Factory w dalszych scenariuszach.
