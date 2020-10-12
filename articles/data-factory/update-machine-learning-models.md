---
title: Aktualizowanie modeli Azure Machine Learning Studio (klasycznych) przy użyciu Azure Data Factory
description: Opisuje sposób tworzenia potoków predykcyjnych przy użyciu Azure Data Factory i Azure Machine Learning Studio (klasyczny)
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 83950c2d3c5439886ff787b69d9da4d0c214de31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092545"
---
# <a name="update-azure-machine-learning-studio-classic-models-by-using-update-resource-activity"></a>Aktualizowanie modeli Azure Machine Learning Studio (klasycznych) za pomocą działania aktualizacji zasobów

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ten artykuł stanowi uzupełnienie głównego artykułu z integracją Azure Data Factory Azure Machine Learning Studio (klasyczny): [Tworzenie potoków predykcyjnych przy użyciu Azure Machine Learning Studio (klasyczne) i Azure Data Factory](transform-data-using-machine-learning.md). Jeśli jeszcze tego nie zrobiono, zapoznaj się z głównym artykułem przed przeczytaniem tego artykułu.

## <a name="overview"></a>Omówienie
W ramach procesu modeli operacjonalizowania Azure Machine Learning Studio (klasycznych) model jest przeszkolony i zapisywany. Następnie należy użyć jej do utworzenia predykcyjnej usługi sieci Web. Usługę sieci Web można następnie wykorzystać w witrynach sieci Web, pulpitach nawigacyjnych i aplikacjach mobilnych.

Modele tworzone przy użyciu Azure Machine Learning Studio (klasyczne) nie są zwykle statyczne. Ponieważ nowe dane staną się dostępne lub gdy użytkownik interfejsu API ma własne dane, należy ponownie przeszkolić model. 

Przeszkolenie może odbywać się często. Za pomocą działania wykonywania wsadowego i aktualizowania działań związanych z zasobami można Azure Machine Learning Studio operacjonalizować (klasyczny) model do ponownego uczenia i zaktualizować predykcyjną usługę sieci Web przy użyciu Data Factory.

Na poniższej ilustracji przedstawiono relacje między szkoleniem i predykcyjnymi usługami sieci Web.

![usługi sieci Web](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-studio-classic-update-resource-activity"></a>Działanie aktualizacji zasobów Azure Machine Learning Studio (klasycznej)

Poniższy fragment kodu JSON definiuje działanie wykonywania wsadowego Azure Machine Learning Studio (klasycznego).

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```

| Właściwość                      | Opis                              | Wymagane |
| :---------------------------- | :--------------------------------------- | :------- |
| name                          | Nazwa działania w potoku     | Tak      |
| description                   | Tekst opisujący działanie działania.  | Nie       |
| typ                          | W przypadku działania dotyczącego aktualizacji zasobów Azure Machine Learning Studio (klasycznej) typem działania jest  **AzureMLUpdateResource**. | Tak      |
| linkedServiceName             | Połączona usługa Azure Machine Learning Studio (klasyczna), która zawiera właściwość właściwości updateresourceendpoint. | Tak      |
| trainedModelName              | Nazwa modułu przeszkolonego modelu w eksperymentie usługi sieci Web do zaktualizowania | Tak      |
| trainedModelLinkedServiceName | Nazwa połączonej usługi Azure Storage przechowująca plik ilearner, który jest przekazywany przez operację aktualizacji | Tak      |
| trainedModelFilePath          | Względna ścieżka pliku w trainedModelLinkedService do reprezentowania pliku ilearner, który jest przekazywany przez operację aktualizacji | Tak      |

## <a name="end-to-end-workflow"></a>Kompletny przepływ pracy

Cały proces operacjonalizowania ponownego szkolenia modelu i aktualizowania predykcyjnych usług sieci Web obejmuje następujące kroki:

- Wywołaj **usługę szkoleniową sieci Web** przy użyciu **działania wykonywania wsadowego**. Wywoływanie usługi sieci Web szkoleniowej jest takie samo jak wywołanie usługi sieci Web predykcyjnej opisanej w temacie [Tworzenie potoków predykcyjnych przy użyciu Azure Machine Learning Studio (klasycznego) i Data Factory działania wykonywania wsadowego](transform-data-using-machine-learning.md). Dane wyjściowe usługi sieci Web szkoleniowej to plik iLearner, którego można użyć do zaktualizowania predykcyjnej usługi sieci Web.
- Wywołaj **punkt końcowy zasobu aktualizacji** **predykcyjnej usługi sieci Web** przy użyciu **działania Aktualizuj zasób** w celu zaktualizowania usługi sieci Web przy użyciu nowo nauczonego modelu.

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Połączona usługa Azure Machine Learning Studio (klasyczna)

Aby można było korzystać z powyższego przepływu pracy, należy utworzyć dwie Azure Machine Learning Studio (klasyczne) usługi połączone:

1. Połączona usługa Azure Machine Learning Studio (klasyczna) do usługi sieci Web szkoleń, ta połączona usługa jest używana przez działanie wykonywania wsadowego w taki sam sposób jak w przypadku [tworzenia potoków predykcyjnych przy użyciu Azure Machine Learning Studio (klasycznego) i Data Factory działania wykonywania wsadowego](transform-data-using-machine-learning.md). Różnica polega na tym, że usługa sieci Web szkoleniowej to plik iLearner, który następnie jest używany przez aktualizację działania zasobów do aktualizowania predykcyjnej usługi sieci Web.
2. Połączona usługa Azure Machine Learning Studio (klasyczna) do punktu końcowego zasobu aktualizacji predykcyjnej usługi sieci Web. Ta połączona usługa jest używana przez aktualizację działania zasobów do aktualizowania predykcyjnej usługi sieci Web przy użyciu pliku iLearner zwróconego z poprzedniego kroku.

W przypadku drugiej połączonej usługi Azure Machine Learning Studio (klasycznej) Konfiguracja jest różna, gdy Azure Machine Learning Studio (klasyczna) usługa sieci Web jest klasyczną usługą sieci Web lub nową usługą sieci Web. Różnice są omawiane osobno w poniższych sekcjach.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Usługa sieci Web to nowa usługa sieci Web Azure Resource Manager

Jeśli usługa sieci Web jest nowym typem usługi sieci Web, która uwidacznia punkt końcowy Azure Resource Manager, nie trzeba dodawać drugiego punktu końcowego **innego niż domyślny** . **Właściwości updateresourceendpoint** w połączonej usłudze ma format:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Podczas wykonywania zapytania dotyczącego usługi sieci Web w [portalu usług sieci web Azure Machine Learning Studio (klasyczny)](https://services.azureml.net/)można uzyskać wartości dla posiadaczy umieszczania w adresie URL.

Nowy typ punktu końcowego zasobu aktualizacji wymaga uwierzytelniania nazwy głównej usługi. Aby użyć uwierzytelniania nazwy głównej usługi, zarejestruj jednostkę aplikacji w Azure Active Directory (Azure AD) i nadaj jej rolę **współautora** lub **właściciela** subskrypcji lub grupy zasobów, do której należy usługa sieci Web. Zapoznaj się z tematem [Tworzenie nazwy głównej usługi i przypisywanie uprawnień do zarządzania zasobami platformy Azure](../active-directory/develop/howto-create-service-principal-portal.md). Należy zwrócić uwagę na następujące wartości, których można użyć do zdefiniowania połączonej usługi:

- Identyfikator aplikacji
- Klucz aplikacji
- Identyfikator dzierżawy

Oto definicja połączonej usługi:

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
                "type": "SecureString",
                "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

Poniższy scenariusz zawiera więcej szczegółów. Ma przykład na potrzeby ponownego uczenia i aktualizowania modeli Azure Machine Learning Studio (klasycznych) z potoku Azure Data Factory.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-studio-classic-model"></a>Przykład: ponowne uczenie i aktualizowanie modelu Azure Machine Learning Studio (klasyczny)

Ta sekcja zawiera przykładowy potok, który używa **działania wykonywania wsadowego Azure Machine Learning Studio (klasycznego)** do ponownego uczenia modelu. Potok używa również **działania aktualizacji zasobów Azure Machine Learning Studio (klasyczny)** do aktualizowania modelu w usłudze sieci Web oceniania. Sekcja zawiera również fragmenty kodu JSON dla wszystkich połączonych usług, zestawów danych i potoków w przykładzie.

### <a name="azure-blob-storage-linked-service"></a>Połączona usługa Azure Blob Storage:
Usługa Azure Storage przechowuje następujące dane:

* dane szkoleniowe. Dane wejściowe dla usługi sieci Web szkolenia Azure Machine Learning Studio (klasycznego).
* plik iLearner. Dane wyjściowe z usługi sieci Web szkolenia Azure Machine Learning Studio (klasycznego). Ten plik jest również wejściem do działania Aktualizuj zasób.

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

### <a name="linked-service-for-azure-machine-learning-studio-classic-training-endpoint"></a>Połączona usługa dla Azure Machine Learning Studio (klasyczny) — punkt końcowy szkolenia
Poniższy fragment kodu JSON definiuje połączoną usługę Azure Machine Learning Studio (klasyczną), która wskazuje domyślny punkt końcowy usługi sieci Web szkoleniowej.

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

### <a name="linked-service-for-azure-machine-learning-studio-classic-updatable-scoring-endpoint"></a>Połączona usługa dla Azure Machine Learning Studio (klasyczny) aktualizowalny punkt końcowy oceniania:
Poniższy fragment kodu JSON definiuje połączoną usługę Azure Machine Learning Studio (klasyczną), która wskazuje na aktualizowalny punkt końcowy usługi sieci Web oceniania.

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

### <a name="pipeline"></a>Potok
Potok ma dwie działania: **AzureMLBatchExecution** i **AzureMLUpdateResource**. Działanie wykonywania wsadowego pobiera dane szkoleniowe jako dane wejściowe i tworzy plik iLearner jako dane wyjściowe. Następnie działanie Aktualizuj zasób pobiera ten plik iLearner i używa go do aktualizowania predykcyjnej usługi sieci Web.

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        },
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        }
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/output"
                        }
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                        "type": "LinkedServiceReference",
                        "referenceName": "StorageLinkedService"
                    },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [
                    {
                        "activity": "amlbeGetilearner",
                        "dependencyConditions": [ "Succeeded" ]
                    }
                ]
            }
        ]
    }
}
```
## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami, które wyjaśniają sposób przekształcania danych w inny sposób:

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie Hive](transform-data-using-hadoop-hive.md)
* [Aktywność trzody chlewnej](transform-data-using-hadoop-pig.md)
* [Działanie MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działanie przesyłania strumieniowego Hadoop](transform-data-using-hadoop-streaming.md)
* [Działanie platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
