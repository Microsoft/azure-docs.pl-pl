---
title: Aktualizowanie modeli uczenia maszynowego przy użyciu usługi Azure Data Factory
description: W tym artykule opisano sposób tworzenia potoków predykcyjnych przy użyciu usługi Azure Data Factory i uczenia maszynowego
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409069"
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Aktualizowanie modeli usługi Azure Machine Learning za pomocą działania aktualizacji zasobów

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ten artykuł stanowi uzupełnienie głównego artykułu integracji usługi Azure Data Factory — Azure Machine Learning: [Tworzenie potoków predykcyjnych przy użyciu usługi Azure Machine Learning i usługi Azure Data Factory.](transform-data-using-machine-learning.md) Jeśli jeszcze tego nie zrobiłeś, zapoznaj się z głównym artykułem przed przeczytaniem tego artykułu.

## <a name="overview"></a>Omówienie
W ramach procesu operacjonalizacji modeli usługi Azure Machine Learning model jest szkolony i zapisywany. Następnie można go użyć do utworzenia predykcyjnej usługi sieci Web. Usługę sieci Web można następnie umuczynić w witrynach sieci Web, pulpitach nawigacyjnych i aplikacjach mobilnych.

Modele utworzone przy użyciu uczenia maszynowego zazwyczaj nie są statyczne. Jak nowe dane stają się dostępne lub gdy konsument interfejsu API ma własne dane modelu musi być przeszkolony. Szczegółowe informacje na temat możliwości ponownego przeszkolenia modelu w usłudze Azure Machine Learning można znaleźć w obszarze [Przekwalifikowanie modelu uczenia maszynowego.](../machine-learning/machine-learning-retrain-machine-learning-model.md)

Często może dojść do przekwalifikowania. Dzięki działaniu wykonywania wsadowego i działaniu aktualizuj zasoby można przeszkolić model usługi Azure Machine Learning i zaktualizować predykcyjną usługę sieci Web przy użyciu fabryki danych.

Poniższy obraz przedstawia relację między szkolenia i predykcyjnych usług sieci Web.

![Usługi sieci Web](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Działanie zasobów aktualizacji usługi Azure Machine Learning

Poniższy fragment kodu JSON definiuje działanie wsadowe usługi Azure Machine Learning.

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
| description                   | Tekst opisujący działanie.  | Nie       |
| type                          | W przypadku działania usługi Azure Machine Learning Update Resource typem działania jest **usługa AzureMLUpdateResource**. | Tak      |
| linkedServiceName             | Usługa linked Azure Machine Learning zawierająca właściwość updateResourceEndpoint. | Tak      |
| przeszkolonyModelName              | Nazwa modułu Model przeszkolony w eksperymencie usługi sieci Web do aktualizacji | Tak      |
| przeszkolonyModelLinkedServiceName | Nazwa połączonej usługi Usługi Azure Storage zawierającej plik ilearner, który jest przekazyany przez operację aktualizacji | Tak      |
| wyszkolonyModelFilePath          | Względna ścieżka pliku w trainedModelLinkedService do reprezentowania pliku ilearner, który jest przekazyny przez operację aktualizacji | Tak      |

## <a name="end-to-end-workflow"></a>Kompletny przepływ pracy

Cały proces operacjonalizacji ponownego trenowania modelu i aktualizowania predykcyjnych usług sieci Web obejmuje następujące kroki:

- Wywołać **szkolenia usługi sieci Web** przy użyciu **działania wykonywania wsadowego**. Wywoływanie szkoleniowej usługi sieci Web jest takie samo jak wywoływanie predykcyjnej usługi sieci Web opisanej w [obszarze Tworzenie potoków predykcyjnych przy użyciu działania Azure Machine Learning i Data Factory Batch Execution.](transform-data-using-machine-learning.md) Dane wyjściowe szkoleniowej usługi sieci Web jest plik iLearner, który służy do aktualizacji predykcyjnej usługi sieci Web.
- Wywołać **punkt końcowy zasobu aktualizacji** **predykcyjnej usługi sieci Web** przy użyciu działania Aktualizuj **zasób,** aby zaktualizować usługę sieci Web z nowo przeszkolonym modelem.

## <a name="azure-machine-learning-linked-service"></a>Usługa połączona usługi usługi Azure Machine Learning

Aby wyżej wymieniony kompleksowy przepływ pracy działał, musisz utworzyć dwie połączone usługi usługi Azure Machine Learning:

1. Usługa połączona usługi Azure Machine Learning z usługą sieci web szkolenia, ta połączona usługa jest używana przez działanie wykonywania wsadowego w taki sam sposób, jak to, co wspomniano w [tworzenie potoków predykcyjnych przy użyciu usługi Azure Machine Learning i działania wykonywania wsadowego fabryki danych.](transform-data-using-machine-learning.md) Różnica jest dane wyjściowe usługi sieci web szkolenia jest plik iLearner, który jest następnie używany przez działanie aktualizacji zasobów, aby zaktualizować predykcyjnej usługi sieci web.
2. Usługa połączona usługi Azure Machine Learning z punktem końcowym zasobu aktualizacji predykcyjnej usługi sieci web. Ta połączona usługa jest używana przez działanie aktualizuj zasobów do aktualizowania predykcyjnej usługi sieci web przy użyciu pliku iLearner zwróconego z powyższego kroku.

W przypadku drugiej połączonej usługi usługi Azure Machine Learning konfiguracja jest inna, gdy usługa azure machine learning web jest klasyczną usługą sieci Web lub nową usługą sieci Web. Różnice są omówione oddzielnie w poniższych sekcjach.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Usługa sieci Web to nowa usługa sieci Web usługi Azure Resource Manager

Jeśli usługa sieci web jest nowy typ usługi sieci web, która udostępnia punkt końcowy usługi Azure Resource Manager, nie trzeba dodawać drugi punkt końcowy **nie domyślne.** **UpdateResourceEndpoint** w połączonej usłudze jest w formacie:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Podczas wykonywania zapytań o usługę web w portalu [Azure Machine Learning Web Services Portal](https://services.azureml.net/)można uzyskać wartości dla posiadaczy miejsc w adresie URL.

Nowy typ punktu końcowego zasobu aktualizacji wymaga uwierzytelniania jednostkowego usługi. Aby użyć uwierzytelniania jednostki usługi, zarejestruj jednostkę aplikacji w usłudze Azure Active Directory (Azure AD) i przyznaj jej rolę **Współautor** lub **Właściciel** subskrypcji lub grupy zasobów, do której należy usługa sieci web. Zobacz, [jak utworzyć jednostkę usługi i przypisać uprawnienia do zarządzania zasobami platformy Azure](../active-directory/develop/howto-create-service-principal-portal.md). Zanotuj następujące wartości, których używasz do definiowania połączonej usługi:

- Identyfikator aplikacji
- Klucz aplikacji
- Identyfikator dzierżawy

Oto przykładowa definicja usługi połączonej:

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

Poniższy scenariusz zawiera więcej szczegółów. Ma przykład ponownego szkolenia i aktualizowania modeli studio usługi Azure Machine Learning z potoku usługi Azure Data Factory.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Przykład: Przekwalifikowanie i aktualizowanie modelu usługi Azure Machine Learning

Ta sekcja zawiera przykładowy potok, który używa **działania azure machine learning studio wsadowego do** ponownego uczenia modelu. Potok używa również **działania usługi Azure Machine Learning studio Update Resource,** aby zaktualizować model w usłudze sieci web oceniania. Sekcja zawiera również fragmenty kodu JSON dla wszystkich połączonych usług, zestawów danych i potoku w przykładzie.

### <a name="azure-blob-storage-linked-service"></a>Usługa połączona z magazynem obiektów Blob platformy Azure:
Usługa Azure Storage przechowuje następujące dane:

* danych szkoleniowych. Dane wejściowe dla usługi sieci web szkoleniowej usługi szkoleniowej usługi azure machine learning.
* iLearner. Dane wyjściowe z usługi sieci web szkoleniowej usługi szkoleniowej usługi azure machine learning. Ten plik jest również dane wejściowe do działania Aktualizuj zasób.

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

### <a name="linked-service-for-azure-machine-learning-studio-training-endpoint"></a>Połączona usługa dla punktu końcowego szkolenia studyjnego usługi Azure Machine Learning
Poniższy fragment kodu JSON definiuje usługę połączona usługi Azure Machine Learning, która wskazuje domyślny punkt końcowy szkoleniowej usługi sieci web.

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

W **studiu usługi Azure Machine Learning**wykonaj następujące czynności, aby uzyskać wartości dla **mlEndpoint** i **apiKey:**

1. W menu po lewej stronie kliknij polecenie **USŁUGI SIECI WEB.**
2. Kliknij **usługę sieci web szkoleniową** na liście usług sieci web.
3. Kliknij pozycję Kopiuj obok pola **tekstowego klucza interfejsu API.** Wklej klucz w schowku do edytora JSON fabryki danych.
4. W **studiu usługi Azure Machine Learning**kliknij łącze WYKONYWANIE **WSADOWE.**
5. Skopiuj **identyfikator URI żądania** z sekcji **Żądanie** i wklej go do edytora JSON fabryki danych.

### <a name="linked-service-for-azure-machine-learning-studio-updatable-scoring-endpoint"></a>Połączona usługa dla punktu końcowego oceniania usługi Azure Machine Learning studio z możliwością aktualizacji:
Poniższy fragment kodu JSON definiuje usługę połączona usługi Azure Machine Learning, która wskazuje aktualizowany punkt końcowy usługi sieci web oceniania.

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
Potok ma dwa działania: **AzureMLBatchExecution** i **AzureMLUpdateResource**. Działanie wykonywania wsadowego przyjmuje dane szkoleniowe jako dane wejściowe i tworzy plik iLearner jako dane wyjściowe. Następnie należy użyć tego pliku iLearner i zaktualizować predykcyjną usługę sieci web.

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
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób:

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktywność gałęzi](transform-data-using-hadoop-hive.md)
* [Aktywność świń](transform-data-using-hadoop-pig.md)
* [Działanie mapreduce](transform-data-using-hadoop-map-reduce.md)
* [Aktywność w serwisie Hadoop Streaming](transform-data-using-hadoop-streaming.md)
* [Aktywność iskierki](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
