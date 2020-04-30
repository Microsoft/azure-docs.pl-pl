---
title: Tworzenie fabryki danych Azure przy użyciu szablonu Menedżer zasobów
description: W tym samouczku przedstawiono tworzenie przykładowego potoku usługi Azure Data Factory przy użyciu szablonu usługi Azure Resource Manager.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 02/20/2019
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 5ea45346de5ea841867dd13dd4c9a0ed26647448
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81419123"
---
# <a name="tutorial-create-an-azure-data-factory-using-azure-resource-manager-template"></a>Samouczek: tworzenie fabryki danych na platformie Azure przy użyciu szablonu usługi Azure Resource Manager

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Bieżąca wersja](quickstart-create-data-factory-resource-manager-template.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym przewodniku Szybki start wyjaśniono, jak skorzystać z szablonu usługi Azure Resource Manager w celu utworzenia fabryki danych na platformie Azure. Potok tworzony w tej fabryce danych **kopiuje** dane z jednego folderu do innego folderu w usłudze Azure Blob Storage. Aby zapoznać się z samouczkiem dotyczącym **przekształcania** danych przy użyciu Azure Data Factory, zobacz [Samouczek: Przekształcanie danych przy użyciu platformy Spark](transform-data-using-spark.md).

> [!NOTE]
> Ten artykuł nie zawiera szczegółowego wprowadzenia do usługi Data Factory. Aby zapoznać się z wprowadzeniem do usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)]

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zainstaluj najnowsze moduły Azure PowerShell, postępując zgodnie z instrukcjami w temacie [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

Ogólne informacje na temat szablonów usługi Azure Resource Manager zawiera temat [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

W poniższej sekcji przedstawiono pełny szablon usługi Resource Manager umożliwiający definiowanie jednostek usługi Data Factory, dzięki czemu można szybko przejść przez samouczek i przetestować szablon. Aby dowiedzieć się, jak jest zdefiniowana każda jednostka usługi Data Factory, zobacz sekcję [Jednostki usługi Data Factory w szablonie](#data-factory-entities-in-the-template).

Aby dowiedzieć się więcej na temat składni JSON i właściwości dla zasobów usługi Data Factory w szablonie, zobacz [Typy zasobów Microsoft.DataFactory](/azure/templates/microsoft.datafactory/allversions).

## <a name="data-factory-json"></a>Plik JSON usługi Data Factory

Utwórz plik JSON o nazwie **ADFTutorialARM. JSON** w folderze **C:\ADFTutorial** (Utwórz folder ADFTutorial, jeśli jeszcze nie istnieje) z następującą zawartością:

```json
{  
    "$schema":"http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion":"1.0.0.0",
    "parameters":{  
        "dataFactoryName":{  
            "type":"string",
            "metadata":"Data Factory Name"
        },
        "dataFactoryLocation":{  
            "type":"string",
            "defaultValue":"East US",
            "metadata":{  
                "description":"Location of the data factory. Currently, only East US, East US 2, and West Europe are supported. "
            }
        },
        "storageAccountName":{  
            "type":"string",
            "metadata":{  
                "description":"Name of the Azure storage account that contains the input/output data."
            }
        },
        "storageAccountKey":{  
            "type":"securestring",
            "metadata":{  
                "description":"Key for the Azure storage account."
            }
        },
        "triggerStartTime": {
            "type": "string",
            "metadata": {
                "description": "Start time for the trigger."
            }
        },
        "triggerEndTime": {
            "type": "string",
            "metadata": {
                "description": "End time for the trigger."
            }
        }
    },      
    "variables":{  
        "factoryId":"[concat('Microsoft.DataFactory/factories/', parameters('dataFactoryName'))]"
    },
    "resources":[  
        {  
            "name":"[parameters('dataFactoryName')]",
            "apiVersion":"2018-06-01",
            "type":"Microsoft.DataFactory/factories",
            "location":"[parameters('dataFactoryLocation')]",
            "identity":{  
                "type":"SystemAssigned"
            },
            "resources":[  
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateStorageLinkedService')]",
                    "type":"Microsoft.DataFactory/factories/linkedServices",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "annotations":[  

                        ],
                        "type":"AzureBlobStorage",
                        "typeProperties":{  
                            "connectionString":"[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetIn')]",
                    "type":"Microsoft.DataFactory/factories/datasets",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "linkedServiceName":{  
                            "referenceName":"ArmtemplateStorageLinkedService",
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
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetOut')]",
                    "type":"Microsoft.DataFactory/factories/datasets",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "linkedServiceName":{  
                            "referenceName":"ArmtemplateStorageLinkedService",
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
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateSampleCopyPipeline')]",
                    "type":"Microsoft.DataFactory/factories/pipelines",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "activities":[  
                            {  
                                "name":"MyCopyActivity",
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
                                            "recursive":true
                                        }
                                    },
                                    "sink":{  
                                        "type":"BinarySink",
                                        "storeSettings":{  
                                            "type":"AzureBlobStorageWriteSettings"
                                        }
                                    },
                                    "enableStaging":false
                                },
                                "inputs":[  
                                    {  
                                        "referenceName":"ArmtemplateTestDatasetIn",
                                        "type":"DatasetReference",
                                        "parameters":{  

                                        }
                                    }
                                ],
                                "outputs":[  
                                    {  
                                        "referenceName":"ArmtemplateTestDatasetOut",
                                        "type":"DatasetReference",
                                        "parameters":{  

                                        }
                                    }
                                ]
                            }
                        ],
                        "annotations":[  

                        ]
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetIn')]",
                        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetOut')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmTemplateTestTrigger')]",
                    "type":"Microsoft.DataFactory/factories/triggers",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "annotations":[  

                        ],
                        "runtimeState":"Started",
                        "pipelines":[  
                            {  
                                "pipelineReference":{  
                                    "referenceName":"ArmtemplateSampleCopyPipeline",
                                    "type":"PipelineReference"
                                },
                                "parameters":{  

                                }
                            }
                        ],
                        "type":"ScheduleTrigger",
                        "typeProperties":{  
                            "recurrence":{  
                                "frequency":"Hour",
                                "interval":1,
                                "startTime":"[parameters('triggerStartTime')]",
                                "endTime":"[parameters('triggerEndTime')]",
                                "timeZone":"UTC"
                            }
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/pipelines/ArmtemplateSampleCopyPipeline')]"
                    ]
                }
            ]
        }
    ]
}
```
## <a name="parameters-json"></a>Parametry JSON

Utwórz plik JSON o nazwie **ADFTutorialARM-Parameters.json** zawierający parametry szablonu usługi Azure Resource Manager.

> [!IMPORTANT]
> - Określ nazwę i klucz konta usługi Azure Storage w pliku parametrów za pomocą parametrów **storageAccountName** i **storageAccountKey**. W tym magazynie obiektów blob platformy Azure utworzono kontener adftutorial i przekazano przykładowy plik (emp.txt) do folderu wejściowego.
> - Za pomocą parametru **dataFactoryName** określ nazwę fabryki danych unikatową w skali globalnej, na przykład: ARMTutorialFactoryJohnDoe11282017.
> - Za pomocą parametru **triggerStartTime** podaj bieżący dzień w formacie: `2019-09-08T00:00:00`.
> - Za pomocą parametru **triggerEndTime** podaj kolejny dzień w formacie: `2019-09-09T00:00:00`. Możesz również sprawdzić bieżącą godzinę w strefie UTC i określić czas zakończenia za godzinę lub dwie. Jeśli na przykład bieżąca godzina w strefie UTC to 1:32, podaj wartość `2019-09-09:03:00:00` jako czas zakończenia. W tym przypadku wyzwalacz uruchomi potok dwukrotnie (o godz. 2:00 i 3:00).

```json
{  
    "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":"1.0.0.0",
    "parameters":{  
        "dataFactoryName":{  
            "value":"<datafactoryname>"
        },
        "dataFactoryLocation":{  
            "value":"East US"
        },
        "storageAccountName":{  
            "value":"<yourstorageaccountname>"
        },
        "storageAccountKey":{  
            "value":"<yourstorageaccountkey>"
        },
        "triggerStartTime":{  
            "value":"2019-09-08T11:00:00"
        },
        "triggerEndTime":{  
            "value":"2019-09-08T14:00:00"
        }
    }
}
```

> [!IMPORTANT]
> Można używać oddzielnych plików parametrów JSON dla środowisk programistycznego, testowego i produkcyjnego do użycia z tym samym szablonem JSON usługi Data Factory. Za pomocą skryptu programu Power Shell można zautomatyzować wdrażanie jednostek usługi Data Factory w tych środowiskach.

## <a name="deploy-data-factory-entities"></a>Wdrażanie jednostek usługi Data Factory

W programie PowerShell uruchom następujące polecenie, aby wdrożyć jednostki Data Factory w grupie zasobów (w tym przypadku ADFTutorialResourceGroup jako przykład) przy użyciu szablonu Menedżer zasobów utworzonego wcześniej w tym przewodniku Szybki Start.

```powershell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFTutorial\ADFTutorialARM.json -TemplateParameterFile C:\ADFTutorial\ADFTutorialARM-Parameters.json
```

Zostaną wyświetlone dane wyjściowe podobne do tych w następującym przykładzie:

```console
DeploymentName          : MyARMDeployment
ResourceGroupName       : ADFTutorialResourceGroup
ProvisioningState       : Succeeded
Timestamp               : 9/8/2019 10:52:29 AM
Mode                    : Incremental
TemplateLink            : 
Parameters              : 
                          Name                   Type                       Value     
                          =====================  =========================  ==========
                          dataFactoryName        String                     <data factory name>
                          dataFactoryLocation    String                     East US   
                          storageAccountName     String                     <storage account name>
                          storageAccountKey      SecureString                         
                          triggerStartTime       String                     9/8/2019 11:00:00 AM
                          triggerEndTime         String                     9/8/2019 2:00:00 PM
                          
Outputs                 : 
DeploymentDebugLogLevel : 
```

## <a name="start-the-trigger"></a>Uruchamianie wyzwalacza

Szablon umożliwia wdrożenie następujących jednostek usługi Data Factory:

- Połączona usługa Azure Storage
- Binarne zestawy danych (dane wejściowe i wyjściowe)
- Potok z działaniem kopiowania
- Wyzwalacz potoku

Wdrożony wyzwalacz jest w stanie zatrzymanym. Jednym z metod uruchomienia wyzwalacza jest użycie polecenia cmdlet **Start-AzDataFactoryV2Trigger** programu PowerShell. Poniższa procedura zawiera szczegółowy opis kroków:

1. W oknie programu PowerShell utwórz zmienną do przechowywania nazwy grupy zasobów. Skopiuj poniższe polecenie do okna programu PowerShell i naciśnij klawisz ENTER. Jeśli podano inną nazwę grupy zasobów dla polecenia New-AzResourceGroupDeployment, zaktualizuj wartość w tym miejscu.

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```
2. Utwórz zmienną do przechowywania nazwy fabryki danych. Podaj tę samą nazwę, którą określono w pliku ADFTutorialARM-Parameters.json.

    ```powershell
    $dataFactoryName = "<yourdatafactoryname>"
    ```
3. Ustaw zmienną dla nazwy wyzwalacza. Nazwa wyzwalacza jest trwale zakodowana w pliku szablonu usługi Resource Manager (ADFTutorialARM.json).

    ```powershell
    $triggerName = "ArmTemplateTestTrigger"
    ```
4. Po podaniu nazw fabryki danych i wyzwalacza, pobierz **stan wyzwalacza**, uruchamiając następujące polecenie programu PowerShell:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

    Oto przykładowe dane wyjściowe:

    ```json

    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFQuickstartsDataFactory0905
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Stopped
    ```
    
    Zwróć uwagę, że stan środowiska uruchomieniowego wyzwalacza to **Zatrzymany**.
5. **Uruchom wyzwalacz**. Wyzwalacz uruchamia potok zdefiniowany w szablonie o pełnej godzinie. Na przykład jeśli polecenie zostanie wykonane o godzinie 14:25, po raz pierwszy wyzwalacz uruchomi potok o godzinie 15:00. Następnie potok będzie uruchamiany co godzinę do czasu zakończenia podanego dla wyzwalacza.

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Oto przykładowe dane wyjściowe:
    
    ```console
    Confirm
    Are you sure you want to start trigger 'ArmTemplateTestTrigger' in data factory 'ARMFactory1128'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
    True
    ```
6. Upewnij się, że wyzwalacz został uruchomiony, ponownie uruchamiając polecenie Get-AzDataFactoryV2Trigger.

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Oto przykładowe dane wyjściowe:
    
    ```console
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFQuickstartsDataFactory0905
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Started
    ```

## <a name="monitor-the-pipeline"></a>Monitorowanie potoku

1. Po zalogowaniu się do witryny [Azure Portal](https://portal.azure.com/) kliknij pozycję **Wszystkie usługi**, przeprowadź wyszukiwanie przy użyciu słowa kluczowego, takiego jak **fabryki da**, i wybierz pozycję **Fabryki danych**.

2. Na stronie **Fabryki danych** kliknij utworzoną fabrykę danych. W razie potrzeby przefiltruj listę, wpisując nazwę fabryki danych.

3. Na stronie Fabryka danych kliknij kafelek **tworzenie & monitor** .

4. **Na stronie Wprowadzenie** wybierz **kartę Monitorowanie**.  ![Monitorowanie uruchomienia potoku](media/doc-common-process/get-started-page-monitor-button.png)

    > [!IMPORTANT]
    > Wyświetlane uruchomienia potoku są ustawione tylko na pełne godziny (na przykład: 4:00, 5:00, 6:00 itd.). Kliknij przycisk **Odśwież** na pasku narzędzi, aby odświeżyć listę po upływie kolejnej godziny.

5. Kliknij link **Wyświetl uruchomienia działania** w kolumnie **Akcje** .

    ![Link akcji potoku](media/quickstart-create-data-factory-resource-manager-template/pipeline-actions-link.png)

6. Zostaną wyświetlone uruchomienia działania skojarzone z uruchomieniem potoku. W tym przewodniku Szybki start potok ma tylko jedno działanie typu Kopiowanie. Widać uruchomienie tego działania.

    ![Uruchomienia działania](media/quickstart-create-data-factory-resource-manager-template/activity-runs.png)
7. Kliknij link **dane wyjściowe** w kolumnie akcje. Dane wyjściowe operacji kopiowania zostaną wyświetlone w oknie **Dane wyjściowe**. Kliknij przycisk Maksymalizuj, aby wyświetlić pełne dane wyjściowe. Zmaksymalizowane okno możesz przywrócić lub zamknąć.

8. Po sprawdzeniu stanu uruchomienia zatrzymaj wyzwalacz. Wyzwalacz uruchamia potok co godzinę. Przy każdym uruchomieniu potok kopiuje ten sam plik z folderu wejściowego do folderu wyjściowego. Aby zatrzymać wyzwalacz, uruchom następujące polecenie w oknie programu PowerShell.
    
    ```powershell
    Stop-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)]

## <a name="json-definitions-for-entities"></a><a name="data-factory-entities-in-the-template"></a> Definicje JSON dotyczące jednostek

Następujące jednostki usługi Data Factory są zdefiniowane w szablonie JSON:

- [Połączona usługa Azure Storage](#azure-storage-linked-service)
- [Binarny zestaw danych wejściowych](#binary-input-dataset)
- [Binarny wyjściowy zestaw danych](#binary-output-dataset)
- [Potok danych z działaniem kopiowania](#data-pipeline)
- [Wyzwalacz](#trigger)

#### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage

Polecenie AzureStorageLinkedService łączy konto usługi Azure Storage z fabryką danych. W ramach wymagań wstępnych utworzono kontener i przekazano dane na to konto magazynu. W tej sekcji określa się nazwę i klucz konta magazynu platformy Azure. Szczegóły dotyczące właściwości JSON używanych do definiowania połączonej usługi Azure Storage zawiera temat [Połączona usługa Azure Storage](connector-azure-blob-storage.md#linked-service-properties).

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateStorageLinkedService')]",
    "type":"Microsoft.DataFactory/factories/linkedServices",
    "apiVersion":"2018-06-01",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]"
    ]
}
```

Parametr connectionString używa parametrów storageAccountName i storageAccountKey. Wartości tych parametrów są przekazywane przy użyciu pliku konfiguracji. Definicja używa także zmiennych azureStorageLinkedService i dataFactoryName zdefiniowanych w szablonie.

#### <a name="binary-input-dataset"></a>Binarny zestaw danych wejściowych

Połączona usługa magazynu Azure określa parametry połączenia, z których korzysta usługa Data Factory w czasie wykonywania, aby połączyć się z kontem magazynu Azure. W definicji binarnego zestawu danych można określić nazwy kontenera obiektów blob, folderu i pliku, który zawiera dane wejściowe. Zobacz [Właściwości binarnego zestawu danych](format-binary.md#dataset-properties) , aby uzyskać szczegółowe informacje na temat właściwości JSON używanych do definiowania binarnego zestawu danych.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetIn')]",
    "type":"Microsoft.DataFactory/factories/datasets",
    "apiVersion":"2018-06-01",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"ArmtemplateStorageLinkedService",
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
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
    ]
}
```

#### <a name="binary-output-dataset"></a>Binarny wyjściowy zestaw danych

Należy podać nazwę folderu usługi Azure Blob Storage, w którym są przechowywane dane skopiowane z folderu wejściowego. Zobacz [Właściwości binarnego zestawu danych](format-binary.md#dataset-properties) , aby uzyskać szczegółowe informacje na temat właściwości JSON używanych do definiowania binarnego zestawu danych.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetOut')]",
    "type":"Microsoft.DataFactory/factories/datasets",
    "apiVersion":"2018-06-01",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"ArmtemplateStorageLinkedService",
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
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
    ]
}
```

#### <a name="data-pipeline"></a>Potok danych

Należy zdefiniować potok, który kopiuje dane z jednego binarnego zestawu danych do innego binarnego zestawu danych. Opisy elementów JSON używanych do definiowania potoku w tym przykładzie zawiera temat [Pipeline JSON](concepts-pipelines-activities.md#pipeline-json) (Kod JSON potoku).

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateSampleCopyPipeline')]",
    "type":"Microsoft.DataFactory/factories/pipelines",
    "apiVersion":"2018-06-01",
    "properties":{  
        "activities":[  
            {  
                "name":"MyCopyActivity",
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
                            "recursive":true
                        }
                    },
                    "sink":{  
                        "type":"BinarySink",
                        "storeSettings":{  
                            "type":"AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging":false
                },
                "inputs":[  
                    {  
                        "referenceName":"ArmtemplateTestDatasetIn",
                        "type":"DatasetReference",
                        "parameters":{  

                        }
                    }
                ],
                "outputs":[  
                    {  
                        "referenceName":"ArmtemplateTestDatasetOut",
                        "type":"DatasetReference",
                        "parameters":{  

                        }
                    }
                ]
            }
        ],
        "annotations":[  

        ]
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetIn')]",
        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetOut')]"
    ]
}
```

#### <a name="trigger"></a>Wyzwalacz

Należy zdefiniować wyzwalacz, który uruchamia potok co godzinę. Wdrożony wyzwalacz jest w stanie zatrzymanym. Uruchom wyzwalacz za pomocą polecenia cmdlet **Start-AzDataFactoryV2Trigger** . Aby uzyskać więcej informacji na temat wyzwalaczy, zobacz artykuł [wykonywanie potoku i wyzwalacze](concepts-pipeline-execution-triggers.md#trigger-execution) .

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmTemplateTestTrigger')]",
    "type":"Microsoft.DataFactory/factories/triggers",
    "apiVersion":"2018-06-01",
    "properties":{  
        "annotations":[  

        ],
        "runtimeState":"Started",
        "pipelines":[  
            {  
                "pipelineReference":{  
                    "referenceName":"ArmtemplateSampleCopyPipeline",
                    "type":"PipelineReference"
                },
                "parameters":{  

                }
            }
        ],
        "type":"ScheduleTrigger",
        "typeProperties":{  
            "recurrence":{  
                "frequency":"Hour",
                "interval":1,
                "startTime":"[parameters('triggerStartTime')]",
                "endTime":"[parameters('triggerEndTime')]",
                "timeZone":"UTC"
            }
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/pipelines/ArmtemplateSampleCopyPipeline')]"
    ]
}
```

## <a name="reuse-the-template"></a>Ponowne użycie szablonu

W ramach samouczka został utworzony szablon służący do definiowania jednostek usługi Data Factory i szablon służący do przekazywania wartości dla parametrów. Aby użyć tego samego szablonu do wdrożenia jednostek usługi Data Factory w różnych środowiskach, należy utworzyć plik parametrów dla każdego środowiska i użyć go podczas wdrażania do środowiska.

Przykład:

```powershell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```

Należy zauważyć, że pierwsze polecenie używa pliku parametrów dla środowiska programistycznego, drugie dla środowiska testowego, a trzecie dla środowiska produkcyjnego.

Można także ponownie użyć szablonu do wykonywania powtarzających się zadań. Na przykład może być potrzebne utworzenie wielu fabryk danych z co najmniej jednym potokiem, które implementują tę samą logikę, lecz każda fabryka danych używa innego konta magazynu platformy Azure. W tym scenariuszu do tworzenia fabryk danych jest używany ten sam szablon w tym samym środowisku (programistycznym, testowym lub produkcyjnym) w połączeniu z różnymi plikami parametrów.

## <a name="next-steps"></a>Następne kroki

Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. Zapoznaj się z [samouczkami](tutorial-copy-data-dot-net.md), aby dowiedzieć się więcej o korzystaniu z usługi Data Factory w dalszych scenariuszach.
