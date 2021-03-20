---
title: Aktualizowanie modeli Machine Learning przy użyciu Azure Data Factory
description: Opisuje sposób tworzenia potoków predykcyjnych przy użyciu Azure Data Factory V1 i Azure Machine Learning Studio (klasyczny)
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 7a27ed657ba21d9e2125df903b40d74cd81eacf6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100379305"
---
# <a name="updating-azure-machine-learning-studio-classic-models-using-update-resource-activity"></a>Aktualizowanie modeli Azure Machine Learning Studio (klasycznych) za pomocą działania aktualizacji zasobów

> [!div class="op_single_selector" title1="Działania transformacji"]
> * [Działanie Hive](data-factory-hive-activity.md) 
> * [Aktywność trzody chlewnej](data-factory-pig-activity.md)
> * [Działanie MapReduce](data-factory-map-reduce.md)
> * [Działanie przesyłania strumieniowego Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Działanie platformy Spark](data-factory-spark.md)
> * [Działanie wykonywania wsadowego w usłudze Azure Machine Learning Studio (wersja klasyczna)](data-factory-azure-ml-batch-execution-activity.md)
> * [Działanie aktualizacji zasobów w usłudze Azure Machine Learning Studio (wersja klasyczna)](data-factory-azure-ml-update-resource-activity.md)
> * [Działania procedur składowanych](data-factory-stored-proc-activity.md)
> * [Działania języka U-SQL usługi Data Lake Analytics](data-factory-usql-activity.md)
> * [Niestandardowe działanie platformy .NET](data-factory-use-custom-activities.md)


> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz temat [aktualizowanie modeli uczenia maszynowego w programie Data Factory](../update-machine-learning-models.md).

Ten artykuł stanowi uzupełnienie głównego artykułu z integracją Azure Data Factory Azure Machine Learning Studio (klasyczny): [Tworzenie potoków predykcyjnych przy użyciu Azure Machine Learning Studio (klasyczne) i Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). Jeśli jeszcze tego nie zrobiono, zapoznaj się z głównym artykułem przed przeczytaniem tego artykułu. 

## <a name="overview"></a>Omówienie
W miarę upływu czasu modele predykcyjne w Azure Machine Learning Studio (klasyczne) eksperymenty oceniania muszą być ponownie przenoszone przy użyciu nowych wejściowych zestawów danych. Po wykonaniu ponownych szkoleń, chcesz zaktualizować usługę oceniania w sieci Web przy użyciu ponownie przemieszczonego modelu ML. Typowymi krokami dotyczącymi włączania funkcji ponownego uczenia i aktualizowania modeli Studio (klasycznych) za pośrednictwem usług sieci Web są:

1. Utwórz eksperyment w [Azure Machine Learning Studio (klasyczny)](https://studio.azureml.net).
2. Gdy korzystasz z modelu, użyj Azure Machine Learning Studio (klasyczny), aby opublikować usługi sieci Web zarówno dla **eksperymentu szkoleniowego** , jak i dla **eksperymentu predykcyjnego**.

W poniższej tabeli opisano usługi sieci Web używane w tym przykładzie.  Aby uzyskać szczegółowe informacje, zobacz [modele Azure Machine Learning Studio (klasyczne)](../../machine-learning/classic/retrain-machine-learning-model.md) .

- **Szkolenie usługi sieci Web** — odbiera dane szkoleniowe i produkuje przeszkolone modele. Dane wyjściowe ponownego szkolenia to plik. ilearner w usłudze Azure Blob Storage. **Domyślny punkt końcowy** jest tworzony automatycznie podczas publikowania eksperymentu szkoleniowego jako usługi sieci Web. Można utworzyć więcej punktów końcowych, ale przykład używa tylko domyślnego punktu końcowego.
- **Ocenianie usługi sieci Web** — odbiera przykłady danych bez etykiet i tworzy przewidywania. Dane wyjściowe przewidywania mogą mieć różne formy, takie jak plik. csv lub wiersze w Azure SQL Database, w zależności od konfiguracji eksperymentu. Domyślny punkt końcowy jest tworzony automatycznie podczas publikowania eksperymentu predykcyjnego jako usługi sieci Web. 

Na poniższej ilustracji przedstawiono relacje między szkoleniami a punktami końcowymi oceniania w Azure Machine Learning Studio (klasyczny).

![usługi sieci Web](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Możesz wywołać **usługę sieci Web szkoleniowej** przy użyciu **działania wykonywania wsadowego Azure Machine Learning Studio (klasycznego)**. Wywoływanie usługi sieci Web szkoleniowej jest takie samo jak wywołanie Azure Machine Learning Studio (klasycznej) usługi sieci Web (ocenianie usługi sieci Web) dla danych oceniania. W poprzednich sekcjach opisano sposób wywołania usługi sieci Web Azure Machine Learning Studio (klasycznej) z potoku Azure Data Factory. 

Możesz wywołać **usługę sieci Web oceniania** za pomocą **działania Azure Machine Learning Studio (klasyczne) aktualizowanie zasobów** , aby zaktualizować usługę sieci Web przy użyciu nowo nauczonego modelu. W poniższych przykładach przedstawiono definicje połączonych usług: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Ocenianie usługi sieci Web to klasyczna usługa sieci Web
Jeśli usługa sieci Web oceniania to **klasyczna usługa sieci Web**, utwórz drugi **inny niż domyślny i aktualizowalny punkt końcowy** przy użyciu Azure Portal. Kroki można znaleźć w artykule [Tworzenie punktów końcowych](../../machine-learning/classic/create-endpoint.md) . Po utworzeniu niedomyślnego, aktualizowalnego punktu końcowego wykonaj następujące czynności:

* Kliknij pozycję **wykonywanie wsadowe** , aby pobrać wartość identyfikatora URI dla właściwości JSON **mlEndpoint** .
* Kliknij link **zaktualizuj zasób** , aby uzyskać wartość identyfikatora URI dla właściwości JSON **Właściwości updateresourceendpoint** . Klucz interfejsu API znajduje się na samej stronie punktu końcowego (w prawym dolnym rogu).

![aktualizowalny punkt końcowy](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

W poniższym przykładzie przedstawiono przykładową definicję JSON dla połączonej usługi Azure. Połączona usługa używa apiKey do uwierzytelniania.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Usługa sieci Web oceniania jest Azure Resource Manager usługą sieci Web 
Jeśli usługa sieci Web jest nowym typem usługi sieci Web, która uwidacznia punkt końcowy Azure Resource Manager, nie trzeba dodawać drugiego punktu końcowego **innego niż domyślny** . **Właściwości updateresourceendpoint** w połączonej usłudze ma format: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Podczas wykonywania zapytania dotyczącego usługi sieci Web w [portalu usług sieci web Azure Machine Learning Studio (klasyczny)](https://services.azureml.net/)można uzyskać wartości dla posiadaczy umieszczania w adresie URL. Nowy typ punktu końcowego zasobu aktualizacji wymaga tokenu usługi AAD (Azure Active Directory). Określ **servicePrincipalId** i **servicePrincipalKey** w połączonej usłudze Studio (klasycznej). Zobacz [jak utworzyć nazwę główną usługi i przypisać uprawnienia do zarządzania zasobem platformy Azure](../../active-directory/develop/howto-create-service-principal-portal.md). Oto przykładowa Definicja usługi połączonej z usługą Azure: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

Poniższy scenariusz zawiera więcej szczegółów. Przykładem dla modeli reszkoleniowych i aktualizujących Studio (klasycznych) z potoku Azure Data Factory.

## <a name="scenario-retraining-and-updating-a-studio-classic-model"></a>Scenariusz: przeszkolenie i Aktualizacja modelu Studio (klasycznego)
Ta sekcja zawiera przykładowy potok, który używa **działania wykonywania wsadowego Azure Machine Learning Studio (klasycznego)** do ponownego uczenia modelu. Potok używa również **działania aktualizacji zasobów Azure Machine Learning Studio (klasyczny)** do aktualizowania modelu w usłudze sieci Web oceniania. Sekcja zawiera również fragmenty kodu JSON dla wszystkich połączonych usług, zestawów danych i potoków w przykładzie.

Oto widok diagramu potoku przykładowego. Jak widać, działanie wykonywania wsadowego programu Studio (klasycznej) pobiera dane wejściowe szkoleniowe i generuje dane wyjściowe szkolenia (plik iLearner). Działanie "Aktualizuj zasób" programu Studio (klasycznej) przyjmuje dane wyjściowe szkolenia i aktualizuje model w punkcie końcowym usługi sieci Web oceniania. Działanie Aktualizuj zasób nie produkuje żadnych danych wyjściowych. PlaceholderBlob to tylko fikcyjny wyjściowy zestaw danych, który jest wymagany przez usługę Azure Data Factory do uruchomienia potoku.

![Diagram potoku](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Połączona usługa Azure Blob Storage:
Usługa Azure Storage przechowuje następujące dane:

* dane szkoleniowe. Dane wejściowe dla usługi sieci Web szkoleniowej dla programu Studio (klasycznej).  
* plik iLearner. Dane wyjściowe z usługi sieci Web szkoleniowej Studio (klasycznej). Ten plik jest również wejściem do działania Aktualizuj zasób.  

Oto przykładowa definicja JSON połączonej usługi:

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>Szkoleniowy zestaw danych wejściowych:
Następujący zestaw danych przedstawia dane szkolenia danych wejściowych dla usługi sieci Web szkoleniowej dla programu Studio (klasycznej). Działanie wykonywania wsadowego programu Studio (klasycznej) pobiera ten zestaw danych jako dane wejściowe.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

### <a name="training-output-dataset"></a>Szkoleniowy zestaw danych wyjściowych:
Następujący zestaw danych reprezentuje wyjściowy plik iLearner z usługi sieci Web szkolenia Azure Machine Learning Studio (klasycznego). Działanie wykonywania wsadowego Azure Machine Learning Studio (klasycznej) generuje ten zestaw danych. Ten zestaw danych jest również dane wejściowe dla działania aktualizowania zasobu Azure Machine Learning Studio (klasycznego).

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-studio-classic-training-endpoint"></a>Punkt końcowy szkolenia usługi połączonej dla programu Studio (klasyczny)
Poniższy fragment kodu JSON definiuje połączoną usługę Studio (klasyczną), która wskazuje domyślny punkt końcowy usługi sieci Web szkoleniowej.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

W **Azure Machine Learning Studio (klasyczny)** wykonaj następujące czynności, aby uzyskać wartości dla **mlEndpoint** i **apiKey**:

1. W menu po lewej stronie kliknij pozycję **usługi sieci Web** .
2. Kliknij pozycję **szkoleniowa usługa sieci Web** na liście usług sieci Web.
3. Kliknij przycisk Kopiuj obok pola tekstowego **klucz interfejsu API** . Wklej klucz w schowku do edytora Data Factory JSON.
4. W **Azure Machine Learning Studio (klasyczny)** kliknij link **wykonywania wsadowego** .
5. Skopiuj **Identyfikator URI żądania** z sekcji **żądania** i wklej go do edytora Data Factory JSON.   

### <a name="linked-service-for-studio-classic-updatable-scoring-endpoint"></a>Dołączona usługa dla programu Studio (klasyczna) aktualizowalny punkt końcowy oceny:
Poniższy fragment kodu JSON definiuje połączoną usługę Studio (klasyczną), która wskazuje na niedomyślny aktualizowalny punkt końcowy usługi sieci Web oceniania.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>Zastępczy wyjściowy zestaw danych:
Działanie aktualizacji zasobów programu Studio (klasycznej) nie generuje żadnych danych wyjściowych. Jednak Azure Data Factory wymaga wyjściowego zestawu danych do kierowania harmonogramu potoku. W związku z tym w tym przykładzie używamy zestawu danych fikcyjnego/zastępczego.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>Potok
Potok ma dwie działania: **AzureMLBatchExecution** i **AzureMLUpdateResource**. Działanie wykonywania wsadowego w Azure Machine Learning Studio (klasycznej) pobiera dane szkoleniowe jako dane wejściowe i tworzy plik iLearner jako dane wyjściowe. Działanie wywołuje szkoleniową usługę sieci Web (eksperyment szkoleniowy ujawniony jako usługa sieci Web) z danymi szkolenia danych wejściowych i odbiera plik ilearner z usługi WebService. PlaceholderBlob to tylko fikcyjny wyjściowy zestaw danych, który jest wymagany przez usługę Azure Data Factory do uruchomienia potoku.

![Diagram potoku](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```