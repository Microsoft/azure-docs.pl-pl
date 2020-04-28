---
title: Aktualizowanie modeli uczenia maszynowego przy użyciu Azure Data Factory
description: Zawiera opis sposobu tworzenia potoku predykcyjnego przy użyciu Azure Data Factory i uczenia maszynowego
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 4488c174ba5ff35ec2709d7c1b9f3093b4ee90a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81409069"
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Aktualizowanie modeli usługi Azure Machine Learning za pomocą działania aktualizacji zasobów

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ten artykuł stanowi uzupełnienie głównego artykułu z integracją Azure Data Factory Azure Machine Learning: [Tworzenie potoków predykcyjnych za pomocą Azure Machine Learning i Azure Data Factory](transform-data-using-machine-learning.md). Jeśli jeszcze tego nie zrobiono, zapoznaj się z głównym artykułem przed przeczytaniem tego artykułu.

## <a name="overview"></a>Omówienie
W ramach procesu operacjonalizowania modeli Azure Machine Learning model jest szkolony i zapisywany. Następnie należy użyć jej do utworzenia predykcyjnej usługi sieci Web. Usługę sieci Web można następnie wykorzystać w witrynach sieci Web, pulpitach nawigacyjnych i aplikacjach mobilnych.

Modele tworzone przy użyciu Machine Learning nie są zwykle statyczne. Ponieważ nowe dane staną się dostępne lub gdy użytkownik interfejsu API ma własne dane, należy ponownie przeszkolić model. Aby uzyskać szczegółowe informacje o tym, jak można ponownie przeprowadzić uczenie modelu w Azure Machine Learning, należy zapoznać się z [modelem Machine Learning](../machine-learning/machine-learning-retrain-machine-learning-model.md) .

Przeszkolenie może odbywać się często. Za pomocą działania wykonywania wsadowego i aktualizowania aktywności zasobów można operacjonalizować model Azure Machine Learning przeszkolenie i aktualizację usługi sieci Web predykcyjnej przy użyciu Data Factory.

Na poniższej ilustracji przedstawiono relacje między szkoleniem i predykcyjnymi usługami sieci Web.

![Usługi sieci Web](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Azure Machine Learning aktualizowania działania zasobu

Poniższy fragment kodu JSON definiuje działanie wykonywania wsadowego Azure Machine Learning.

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

| Właściwość                      | Opis                              | Wymagany |
| :---------------------------- | :--------------------------------------- | :------- |
| name                          | Nazwa działania w potoku     | Tak      |
| description                   | Tekst opisujący działanie działania.  | Nie       |
| type                          | W przypadku działania dotyczącego Azure Machine Learning aktualizowania zasobów typem działania jest **AzureMLUpdateResource**. | Tak      |
| linkedServiceName             | Azure Machine Learning połączona usługa, która zawiera właściwość właściwości updateresourceendpoint. | Tak      |
| trainedModelName              | Nazwa modułu przeszkolonego modelu w eksperymentie usługi sieci Web do zaktualizowania | Tak      |
| trainedModelLinkedServiceName | Nazwa połączonej usługi Azure Storage przechowująca plik ilearner, który jest przekazywany przez operację aktualizacji | Tak      |
| trainedModelFilePath          | Względna ścieżka pliku w trainedModelLinkedService do reprezentowania pliku ilearner, który jest przekazywany przez operację aktualizacji | Tak      |

## <a name="end-to-end-workflow"></a>Kompletny przepływ pracy

Cały proces operacjonalizowania ponownego szkolenia modelu i aktualizowania predykcyjnych usług sieci Web obejmuje następujące kroki:

- Wywołaj **usługę szkoleniową sieci Web** przy użyciu **działania wykonywania wsadowego**. Wywoływanie usługi sieci Web szkoleniowej jest takie samo jak wywołanie usługi sieci Web predykcyjnej opisanej w temacie [Tworzenie potoków predykcyjnych przy użyciu Azure Machine Learning i Data Factory działania wykonywania wsadowego partii](transform-data-using-machine-learning.md). Dane wyjściowe usługi sieci Web szkoleniowej to plik iLearner, którego można użyć do zaktualizowania predykcyjnej usługi sieci Web.
- Wywołaj **punkt końcowy zasobu aktualizacji** **predykcyjnej usługi sieci Web** przy użyciu **działania Aktualizuj zasób** w celu zaktualizowania usługi sieci Web przy użyciu nowo nauczonego modelu.

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning połączona usługa

Aby można było korzystać z powyższego przepływu pracy, należy utworzyć dwie Azure Machine Learning połączone usługi:

1. Azure Machine Learning połączona usługa do usługi sieci Web szkoleń, ta połączona usługa jest używana przez działanie wykonywania wsadowego w taki sam sposób jak w przypadku [tworzenia potoków predykcyjnych za pomocą Azure Machine Learning i Data Factory działania wykonywania wsadowego](transform-data-using-machine-learning.md). Różnica polega na tym, że usługa sieci Web szkoleniowej to plik iLearner, który następnie jest używany przez aktualizację działania zasobów do aktualizowania predykcyjnej usługi sieci Web.
2. Azure Machine Learning połączona usługa do punktu końcowego zasobu aktualizacji predykcyjnej usługi sieci Web. Ta połączona usługa jest używana przez aktualizację działania zasobów do aktualizowania predykcyjnej usługi sieci Web przy użyciu pliku iLearner zwróconego z poprzedniego kroku.

W przypadku drugiej Azure Machine Learning połączonej usługi konfiguracja jest różna, gdy usługa sieci Web Azure Machine Learning jest klasyczną usługą sieci Web lub nową usługą sieci Web. Różnice są omawiane osobno w poniższych sekcjach.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Usługa sieci Web to nowa usługa sieci Web Azure Resource Manager

Jeśli usługa sieci Web jest nowym typem usługi sieci Web, która uwidacznia punkt końcowy Azure Resource Manager, nie trzeba dodawać drugiego punktu końcowego **innego niż domyślny** . **Właściwości updateresourceendpoint** w połączonej usłudze ma format:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

W przypadku wysyłania zapytań do usługi sieci Web w [portalu usług sieci web Azure Machine Learning](https://services.azureml.net/)można uzyskać wartości dla posiadaczy umieszczania w adresie URL.

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

Poniższy scenariusz zawiera więcej szczegółów. Przykładem do ponownego szkolenia i aktualizowania modeli Azure Machine Learning Studio z potoku Azure Data Factory.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Przykład: ponowne uczenie i aktualizowanie modelu Azure Machine Learning

Ta sekcja zawiera przykładowy potok, który używa **działania wykonywania wsadowego Azure Machine Learning Studio** do ponownego uczenia modelu. Potok używa również **działania Azure Machine Learning Studio Update Resource** do zaktualizowania modelu w usłudze sieci Web oceniania. Sekcja zawiera również fragmenty kodu JSON dla wszystkich połączonych usług, zestawów danych i potoków w przykładzie.

### <a name="azure-blob-storage-linked-service"></a>Połączona usługa Azure Blob Storage:
Usługa Azure Storage przechowuje następujące dane:

* dane szkoleniowe. Dane wejściowe dla usługi sieci Web szkolenia Azure Machine Learning Studio.
* plik iLearner. Dane wyjściowe usługi sieci Web szkolenia Azure Machine Learning Studio. Ten plik jest również wejściem do działania Aktualizuj zasób.

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

### <a name="linked-service-for-azure-machine-learning-studio-training-endpoint"></a>Połączona usługa dla punktu końcowego szkolenia Azure Machine Learning Studio
Poniższy fragment kodu JSON definiuje Azure Machine Learning połączoną usługę, która wskazuje domyślny punkt końcowy usługi sieci Web szkoleniowej.

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

W programie **Azure Machine Learning Studio**wykonaj następujące czynności, aby uzyskać wartości dla **mlEndpoint** i **apiKey**:

1. W menu po lewej stronie kliknij pozycję **usługi sieci Web** .
2. Kliknij pozycję **szkoleniowa usługa sieci Web** na liście usług sieci Web.
3. Kliknij przycisk Kopiuj obok pola tekstowego **klucz interfejsu API** . Wklej klucz w schowku do edytora Data Factory JSON.
4. W **Azure Machine Learning Studio**kliknij link **wykonywania wsadowego** .
5. Skopiuj **Identyfikator URI żądania** z sekcji **żądania** i wklej go do edytora Data Factory JSON.

### <a name="linked-service-for-azure-machine-learning-studio-updatable-scoring-endpoint"></a>Połączona usługa dla programu Azure Machine Learning Studio — punkt końcowy oceniania:
Poniższy fragment kodu JSON definiuje Azure Machine Learning połączoną usługę, która wskazuje na aktualizowalny punkt końcowy usługi sieci Web oceniania.

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
