---
title: Tworzenie pierwszej fabryki danych (szablon Menedżer zasobów)
description: W tym samouczku przedstawiono tworzenie przykładowego potoku usługi Azure Data Factory przy użyciu szablonu usługi Azure Resource Manager.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.openlocfilehash: ce8710cb8f1cf49752f95340d931ddd79d43ec35
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96496385"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-resource-manager-template"></a>Samouczek: tworzenie pierwszej fabryki danych Azure przy użyciu szablonu usługi Azure Resource Manager
> [!div class="op_single_selector"]
> * [Przegląd i wymagania wstępne](data-factory-build-your-first-pipeline.md)
> * [Program Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [Program PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Szablon Menedżer zasobów](data-factory-build-your-first-pipeline-using-arm.md)
> * [Interfejs API REST](data-factory-build-your-first-pipeline-using-rest-api.md)
> 
 
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Szybki start: tworzenie fabryki danych przy użyciu usługi Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

W tym artykule opisano użycie szablonu usługi Azure Resource Manager do tworzenia pierwszej fabryki danych Azure. Aby wykonać instrukcje z tego samouczka przy użyciu innych narzędzi/zestawów SDK, wybierz jedną z opcji z listy rozwijanej.

Potok w tym samouczku zawiera jedno działanie: **działanie Hive usługi HDInsight**. To działanie uruchamia skrypt Hive w klastrze Azure HDInsight, który przekształca dane wejściowe, aby wygenerować dane wyjściowe. Uruchamianie potoku zaplanowano raz w miesiącu między określonym czasem rozpoczęcia i zakończenia. 

> [!NOTE]
> Potok danych przedstawiony w tym samouczku przekształca dane wejściowe w celu wygenerowania danych wyjściowych. Aby zapoznać się z samouczkiem dotyczącym kopiowania danych przy użyciu usługi Azure Data Factory, zobacz [Tutorial: Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Samouczek: Kopiowanie danych z usługi Blob Storage do usługi SQL Database).
> 
> Potok w tym samouczku zawiera tylko jedno działanie typu: HDInsightHive. Potok może obejmować więcej niż jedno działanie. Dwa działania można połączyć w łańcuch (uruchomić jedno działanie po drugim), ustawiając wyjściowy zestaw danych jednego działania jako zestaw wejściowy drugiego. Więcej informacji znajduje się w artykule dotyczącym [planowania i wykonywania w usłudze Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Przeczytanie artykułu [Omówienie samouczka](data-factory-build-your-first-pipeline.md) oraz wykonanie kroków **wymagań wstępnych**.
* Postępuj zgodnie z instrukcjami w artykule [How to install and configure Azure PowerShell](/powershell/azure/) (Instalowanie i konfigurowanie programu Azure PowerShell), aby zainstalować najnowszą wersję programu Azure PowerShell na komputerze.
* Artykuł [Authoring Azure Resource Manager Templates](../../azure-resource-manager/templates/template-syntax.md) (Tworzenie szablonów usługi Azure Resource Manager) zawiera informacje dotyczące szablonów usługi Azure Resource Manager. 

## <a name="in-this-tutorial"></a>Informacje o tym samouczku

| Jednostka | Opis |
| --- | --- |
| Połączona usługa Azure Storage |Łączy konto usługi Azure Storage z fabryką danych. Konto usługi Azure Storage będzie przechowywać dane wejściowe i wyjściowe dla potoku w tym przykładzie. |
| Połączona usługa HDInsight na żądanie |Łączy klaster usługi HDInsight na żądanie z fabryką danych. Klaster jest automatycznie tworzony na potrzeby przetwarzania danych i usuwany po zakończeniu przetwarzania. |
| Wejściowy zestaw danych obiektów blob platformy Azure |Przywołuje połączoną usługę Azure Storage. Połączona usługa przywołuje konto usługi Azure Storage, a zestaw danych obiektów blob platformy Azure określa kontener, folder i nazwę pliku w magazynie, który przechowuje dane wejściowe. |
| Wyjściowy zestaw danych obiektów blob platformy Azure |Przywołuje połączoną usługę Azure Storage. Połączona usługa przywołuje konto usługi Azure Storage, a zestaw danych obiektów blob platformy Azure określa kontener, folder i nazwę pliku w magazynie, który przechowuje dane wyjściowe. |
| Potok danych |Potok ma jedno działanie typu HDInsightHive, które zużywa wejściowy zestaw danych i tworzy wyjściowy zestaw danych. |

Fabryka danych może obejmować jeden lub wiele potoków. Potok może obejmować jedno lub wiele działań. Istnieją dwa typy działań: [przenoszenia danych](data-factory-data-movement-activities.md) i [przekształcania danych](data-factory-data-transformation-activities.md). W tym samouczku tworzony jest potok z jednym działaniem (Działanie programu Hive).

W poniższej sekcji przedstawiono pełny szablon usługi Resource Manager umożliwiający definiowanie jednostek usługi Data Factory, dzięki czemu można szybko przejść przez samouczek i przetestować szablon. Aby dowiedzieć się, jak jest zdefiniowana każda jednostka usługi Data Factory, zobacz sekcję [Jednostki usługi Data Factory w szablonie](#data-factory-entities-in-the-template). Aby dowiedzieć się więcej na temat składni JSON i właściwości dla zasobów usługi Data Factory w szablonie, zobacz [Typy zasobów Microsoft.DataFactory](/azure/templates/microsoft.datafactory/allversions).

## <a name="data-factory-json-template"></a>Szablon JSON usługi Data Factory
Szablon najwyższego poziomu usługi Resource Manager umożliwiający zdefiniowanie fabryki danych to: 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/factories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```
Utwórz plik JSON o nazwie **ADFTutorialARM.json** w folderze **C:\ADFGetStarted** o następującej zawartości:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the input/output data." } },
          "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
          "blobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
          "inputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that has the input file." } },
          "inputBlobName": { "type": "string", "metadata": { "description": "Name of the input file/blob." } },
          "outputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that will hold the transformed data." } },
          "hiveScriptFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that contains the Hive query file." } },
          "hiveScriptFile": { "type": "string", "metadata": { "description": "Name of the hive query (HQL) file." } }
    },
    "variables": {
          "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
          "azureStorageLinkedServiceName": "AzureStorageLinkedService",
          "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
          "blobInputDatasetName": "AzureBlobInput",
          "blobOutputDatasetName": "AzureBlobOutput",
          "pipelineName": "HiveTransformPipeline"
    },
    "resources": [
      {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "West US",
        "resources": [
          {
            "type": "linkedservices",
            "name": "[variables('azureStorageLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureStorage",
                  "description": "Azure Storage linked service",
                  "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                  }
            }
          },
          {
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "HDInsightOnDemand",
                  "typeProperties": {
                    "version": "3.5",
                    "clusterSize": 1,
                    "timeToLive": "00:05:00",
                    "osType": "Linux",
                    "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
                  }
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobInputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "fileName": "[parameters('inputBlobName')]",
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
                    "interval": 1
                  },
                  "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobOutputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
                    "interval": 1
                  }
            }
          },
          {
            "type": "datapipelines",
            "name": "[variables('pipelineName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]",
                  "[variables('hdInsightOnDemandLinkedServiceName')]",
                  "[variables('blobInputDatasetName')]",
                  "[variables('blobOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "description": "Pipeline that transforms data using Hive script.",
                  "activities": [
                {
                      "type": "HDInsightHive",
                      "typeProperties": {
                        "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                        "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                        "defines": {
                              "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                              "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                        }
                      },
                      "inputs": [
                        {
                              "name": "[variables('blobInputDatasetName')]"
                        }
                      ],
                      "outputs": [
                        {
                              "name": "[variables('blobOutputDatasetName')]"
                        }
                      ],
                      "policy": {
                        "concurrency": 1,
                        "retry": 3
                      },
                      "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                      },
                      "name": "RunSampleHiveActivity",
                      "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
                }
                  ],
                  "start": "2017-07-01T00:00:00Z",
                  "end": "2017-07-02T00:00:00Z",
                  "isPaused": false
              }
          }
        ]
      }
    ]
}
```

> [!NOTE]
> Temat [Samouczek: Tworzenie potoku za pomocą działania kopiowania przy użyciu szablonu usługi Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md) zawiera kolejny przykład szablonu usługi Resource Manager umożliwiającego utworzenie fabryki danych platformy Azure.  
> 
> 

## <a name="parameters-json"></a>Parametry JSON
Utwórz plik JSON o nazwie **ADFTutorialARM-Parameters.json** zawierający parametry szablonu usługi Azure Resource Manager.  

> [!IMPORTANT]
> Określ nazwę i klucz konta usługi Azure Storage w pliku parametrów za pomocą parametrów **storageAccountName** i **storageAccountKey**. 
> 
> 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "value": "<Name of your Azure Storage account>"
        },
        "storageAccountKey": {
            "value": "<Key of your Azure Storage account>"
        },
        "blobContainer": {
            "value": "adfgetstarted"
        },
        "inputBlobFolder": {
            "value": "inputdata"
        },
        "inputBlobName": {
            "value": "input.log"
        },
        "outputBlobFolder": {
            "value": "partitioneddata"
        },
        "hiveScriptFolder": {
              "value": "script"
        },
        "hiveScriptFile": {
              "value": "partitionweblogs.hql"
        }
    }
}
```

> [!IMPORTANT]
> Można używać oddzielnych plików parametrów JSON dla środowisk programistycznego, testowego i produkcyjnego do użycia z tym samym szablonem JSON usługi Data Factory. Za pomocą skryptu programu Power Shell można zautomatyzować wdrażanie jednostek usługi Data Factory w tych środowiskach. 
> 
> 

## <a name="create-data-factory"></a>Tworzenie fabryki danych
1. Uruchom **Azure PowerShell** i uruchom następujące polecenie: 
   * Uruchom poniższe polecenie i wprowadź nazwę użytkownika oraz hasło, których używasz do logowania się w witrynie Azure Portal.
     ```PowerShell
     Connect-AzAccount
     ```  
   * Uruchom poniższe polecenie, aby wyświetlić wszystkie subskrypcje dla tego konta.
     ```PowerShell
     Get-AzSubscription
     ``` 
   * Uruchom poniższe polecenie, aby wybrać subskrypcję, z którą chcesz pracować. Ta subskrypcja powinna być taka sama jak używana w witrynie Azure Portal.
     ```
     Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
     ```   
2. Uruchom następujące polecenie, aby wdrożyć jednostki usługi Data Factory przy użyciu szablonu usługi Resource Manager utworzonego w kroku 1. 

    ```PowerShell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>Monitorowanie potoku
1. Po zalogowaniu się do [Azure Portal](https://portal.azure.com/)kliknij przycisk **Przeglądaj** i wybierz opcję **fabryki danych**.
     ![Przeglądaj->Fabryki danych](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2. W bloku **Fabryki danych** kliknij utworzoną przez siebie fabrykę danych (**TutorialFactoryARM**).    
3. W bloku **Fabryka danych** dla swojej fabryki danych kliknij opcję **Diagram**.

     ![Kafelek diagramu](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4. W **widoku diagramu** zostanie wyświetlony przegląd potoków i zestawów danych używanych w tym samouczku.
   
   ![Widok diagramu](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
5. W widoku diagramu kliknij dwukrotnie zestaw danych **AzureBlobOutput**. Zostanie wyświetlony wycinek, który jest obecnie przetwarzany.
   
    ![Zrzut ekranu pokazujący zestaw danych AzureBlobOutput.](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
6. Po zakończeniu przetwarzania wycinek będzie mieć stan **Gotowe**. Tworzenie klastra usługi HDInsight na żądanie zwykle trwa trochę czasu (około 20 minut). Dlatego należy oczekiwać, że przetworzenie wycinka przez potok zajmie **około 30 minut**.
   
    ![Zestaw danych](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)    
7. Gdy wycinek będzie w stanie **Gotowe**, sprawdź folder **partitioneddata** w kontenerze **adfgetstarted** w magazynie obiektów blob pod kątem danych wyjściowych.  

Zobacz artykuł [Monitor datasets and pipeline](data-factory-monitor-manage-pipelines.md) (Monitorowanie zestawów danych i potoku), aby uzyskać instrukcje dotyczące korzystania z bloków w witrynie Azure Portal w celu monitorowania potoku i zestawów danych utworzonych przez siebie w ramach tego samouczka.

Do monitorowania potoków danych możesz też użyć aplikacji Monitorowanie i zarządzanie. Szczegółowe informacje dotyczące korzystania z aplikacji znajdują się w artykule [Monitor and manage Azure Data Factory pipelines using Monitoring App](data-factory-monitor-manage-app.md) (Monitorowanie potoków usługi Fabryka danych Azure oraz zarządzanie nimi za pomocą aplikacji do monitorowania). 

> [!IMPORTANT]
> Po pomyślnym przetworzeniu wycinka plik wejściowy zostanie usunięty. Tak więc, jeśli chcesz ponownie uruchomić wycinek lub ponownie wykonać instrukcje z tego samouczka, przekaż plik wejściowy (input.log) do folderu inputdata kontenera adfgetstarted.
> 
> 

## <a name="data-factory-entities-in-the-template"></a>Jednostki usługi Data Factory w szablonie
### <a name="define-data-factory"></a>Definiowanie fabryki danych
Fabrykę danych definiuje się w szablonie usługi Resource Manager jak pokazano w następującym przykładzie:  

```json
"resources": [
{
    "name": "[variables('dataFactoryName')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/factories",
    "location": "West US"
}
```
Parametr dataFactoryName jest zdefiniowany jako: 

```json
"dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
```
To jest unikatowy ciąg oparty na identyfikatorze grupy zasobów.  

### <a name="defining-data-factory-entities"></a>Definiowanie jednostek usługi Data Factory
Następujące jednostki usługi Data Factory są zdefiniowane w szablonie JSON: 

* [Połączona usługa Azure Storage](#azure-storage-linked-service)
* [Połączona usługa HDInsight na żądanie](#hdinsight-on-demand-linked-service)
* [Wejściowy zestaw danych obiektów blob platformy Azure](#azure-blob-input-dataset)
* [Wyjściowy zestaw danych obiektów blob platformy Azure](#azure-blob-output-dataset)
* [Potok danych z działaniem kopiowania](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
W tej sekcji określa się nazwę i klucz konta magazynu platformy Azure. Szczegóły dotyczące właściwości JSON używanych do definiowania połączonej usługi Azure Storage zawiera temat [Połączona usługa Azure Storage](data-factory-azure-blob-connector.md#azure-storage-linked-service). 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
Parametry **ConnectionString** używają parametrów StorageAccountName i storageAccountKey. Wartości tych parametrów są przekazywane przy użyciu pliku konfiguracji. Definicja używa także zmiennych azureStorageLinkedService i dataFactoryName zdefiniowanych w szablonie. 

#### <a name="hdinsight-on-demand-linked-service"></a>Połączona usługa HDInsight na żądanie
Szczegółowe informacje o właściwościach JSON używanych do definiowania połączonej usługi HDInsight na żądanie zawiera temat [Usługi połączone usługi Compute](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).  

```json
{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
        }
    }
}
```

Pamiętaj o następujących kwestiach: 

* Usługa Data Factory tworzy klaster usługi HDInsight **oparty na systemie Linux** za pomocą powyższego kodu JSON. Szczegółowe informacje znajdują się w artykule [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Połączona usługa HDInsight na żądanie). 
* Możesz użyć **własnego klastra usługi HDInsight** zamiast korzystać z klastra usługi HDInsight na żądanie. Szczegółowe informacje znajdują się w artykule [HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) (Połączona usługa HDInsight).
* Klaster usługi HDInsight tworzy **kontener domyślny** w magazynie obiektów BLOB określonym w kodzie JSON (**linkedServiceName**). Usługa HDInsight nie powoduje usunięcia tego kontenera w przypadku usunięcia klastra. To zachowanie jest celowe. W przypadku połączonej usługi HDInsight na żądanie klaster usługi HDInsight jest tworzony za każdym razem, gdy trzeba przetworzyć wycinek — o ile w tym momencie nie istnieje aktywny klaster (**timeToLive**) — i zostaje usunięty po zakończeniu przetwarzania.
  
    Po przetworzeniu większej liczby wycinków w usłudze Azure Blob Storage będzie widocznych wiele kontenerów. Jeśli nie są potrzebne do rozwiązywania problemów z zadaniami, można je usunąć, aby zmniejszyć koszt przechowywania. Nazwy tych kontenerów są zgodne ze wzorcem: "ADF **twojanazwafabrykidanych nazwapołączonejusługi** - **linkedservicename**-znacznikdatygodziny". Użyj narzędzi, takich jak [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/) , aby usunąć kontenery w usłudze Azure Blob Storage.

Szczegółowe informacje znajdują się w artykule [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Połączona usługa HDInsight na żądanie).

#### <a name="azure-blob-input-dataset"></a>Wejściowy zestaw danych obiektów blob platformy Azure
Określane są nazwy kontenera obiektów blob, folderu i pliku, który zawiera dane wejściowe. Szczegóły dotyczące właściwości JSON używanych do definiowania zestawu danych obiektów blob platformy Azure zawiera temat [Azure Blob dataset properties](data-factory-azure-blob-connector.md#dataset-properties) (Właściwości zestawu danych obiektów blob plaformy Azure). 

```json
{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "[parameters('inputBlobName')]",
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true
    }
}
```
Ta definicja wykorzystuje następujące parametry zdefiniowane w szablonie parametrów: blobContainer, inputBlobFolder i inputBlobName. 

#### <a name="azure-blob-output-dataset"></a>Wyjściowy zestaw danych obiektów blob platformy Azure
Określane są nazwy kontenera obiektów blob i folderu przechowującego dane wyjściowe. Szczegóły dotyczące właściwości JSON używanych do definiowania zestawu danych obiektów blob platformy Azure zawiera temat [Azure Blob dataset properties](data-factory-azure-blob-connector.md#dataset-properties) (Właściwości zestawu danych obiektów blob plaformy Azure).  

```json
{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        }
    }
}
```

Ta definicja wykorzystuje następujące parametry zdefiniowane w szablonie parametrów: blobContainer i outputBlobFolder. 

#### <a name="data-pipeline"></a>Potok danych
Definiuje się potok przekształcający dane za pomocą skryptu Hive uruchamianego w klastrze usługi Azure HDInsight na żądanie. Opisy elementów JSON używanych do definiowania potoku w tym przykładzie zawiera temat [Pipeline JSON](data-factory-create-pipelines.md#pipeline-json) (Kod JSON potoku). 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('hdInsightOnDemandLinkedServiceName')]",
        "[variables('blobInputDatasetName')]",
        "[variables('blobOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "description": "Pipeline that transforms data using Hive script.",
        "activities": [
        {
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                "defines": {
                    "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                    "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                }
            },
            "inputs": [
            {
                "name": "[variables('blobInputDatasetName')]"
            }
            ],
            "outputs": [
            {
                "name": "[variables('blobOutputDatasetName')]"
            }
            ],
            "policy": {
                "concurrency": 1,
                "retry": 3
            },
            "scheduler": {
                "frequency": "Month",
                "interval": 1
            },
            "name": "RunSampleHiveActivity",
            "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
        }
        ],
        "start": "2017-07-01T00:00:00Z",
        "end": "2017-07-02T00:00:00Z",
        "isPaused": false
    }
}
```

## <a name="reuse-the-template"></a>Ponowne użycie szablonu
W ramach samouczka został utworzony szablon służący do definiowania jednostek usługi Data Factory i szablon służący do przekazywania wartości dla parametrów. Aby użyć tego samego szablonu do wdrożenia jednostek usługi Data Factory w różnych środowiskach, należy utworzyć plik parametrów dla każdego środowiska i użyć go podczas wdrażania do środowiska.     

Przykład:  

```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```
Należy zauważyć, że pierwsze polecenie używa pliku parametrów dla środowiska programistycznego, drugie dla środowiska testowego, a trzecie dla środowiska produkcyjnego.  

Można także ponownie użyć szablonu do wykonywania powtarzających się zadań. Na przykład w sytuacji, gdy jest potrzebne utworzenie wielu fabryk danych z co najmniej jednym potokiem, które implementują tę samą logikę, lecz każda fabryka danych używa innego magazynu platformy Azure i kont usługi Azure SQL Database. W tym scenariuszu do tworzenia fabryk danych jest używany ten sam szablon w tym samym środowisku (programistycznym, testowym lub produkcyjnym) w połączeniu z różnymi plikami parametrów. 

## <a name="resource-manager-template-for-creating-a-gateway"></a>Szablon usługi Resource Manager do tworzenia bramy
Poniżej przedstawiono przykładowy szablon usługi Resource Manager do tworzenia logicznej bramy w tle. Zainstaluj bramę na komputerze lokalnym lub maszynie wirtualnej IaaS platformy Azure i zarejestruj bramę w usłudze Data Factory przy użyciu klucza. Szczegółowe informacje znajdują się w artykule [Move data between on-premises and cloud](data-factory-move-data-between-onprem-and-cloud.md) (Przenoszenie danych między komputerem lokalnym i chmurą).

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
    },
    "variables": {
        "dataFactoryName":  "GatewayUsingArmDF",
        "apiVersion": "2015-10-01",
        "singleQuote": "'"
    },
    "resources": [
        {
            "name": "[variables('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/factories",
            "location": "eastus",
            "resources": [
                {
                    "dependsOn": [ "[concat('Microsoft.DataFactory/factories/', variables('dataFactoryName'))]" ],
                    "type": "gateways",
                    "apiVersion": "[variables('apiVersion')]",
                    "name": "GatewayUsingARM",
                    "properties": {
                        "description": "my gateway"
                    }
                }            
            ]
        }
    ]
}
```
Ten szablon służy do tworzenia fabryki danych o nazwie GatewayUsingArmDF z bramą o nazwie: GatewayUsingARM. 

## <a name="see-also"></a>Zobacz też

| Temat | Opis |
|:--- |:--- |
| [Potoki](data-factory-create-pipelines.md) |Ten artykuł ułatwia zapoznanie się z potokami i działaniami w usłudze Azure Data Factory oraz ze sposobem konstruowania za ich pomocą przepływów pracy typu end-to-end opartych na danych na potrzeby scenariusza lub firmy. |
| [Zestawy danych](data-factory-create-datasets.md) |Ten artykuł ułatwia zapoznanie się z zestawami danych w usłudze Azure Data Factory. |
| [Planowanie i wykonywanie](data-factory-scheduling-and-execution.md) |W tym artykule wyjaśniono aspekty planowania i wykonywania modelu aplikacji usługi Fabryka danych Azure. |
| [Monitorowanie potoków i zarządzanie nimi za pomocą aplikacji do monitorowania](data-factory-monitor-manage-app.md) |Ten artykuł zawiera instrukcje dotyczące monitorowania i debugowania potoków oraz zarządzania nimi przy użyciu aplikacji do monitorowania i zarządzania. |

