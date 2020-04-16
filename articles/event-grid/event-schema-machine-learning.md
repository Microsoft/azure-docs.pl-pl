---
title: Usługa Azure Machine Learning jako źródło siatki zdarzeń
description: W tym artykule opisano właściwości, które są dostarczane dla zdarzeń workspace usługi Machine Learning z usługą Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: 7d9af420c7e2b47d2aeb4a8bf42ee138a605b305
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393273"
---
# <a name="azure-machine-learning-as-an-event-grid-source"></a>Usługa Azure Machine Learning jako źródło siatki zdarzeń

Ten artykuł zawiera właściwości i schemat dla zdarzeń obszaru roboczego uczenia maszynowego. Aby zapoznać się ze schematem zdarzeń, zobacz [Schemat zdarzeń usługi Azure Event Grid](event-schema.md).

## <a name="event-grid-event-schema"></a>Schemat zdarzenia w siatce zdarzeń

### <a name="available-event-types"></a>Dostępne typy zdarzeń

Usługa Azure Machine Learning emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.MachineLearningServices.ModelZarejestrowany | Wywoływane, gdy nowa wersja modelu lub modelu została pomyślnie zarejestrowana. |
| Microsoft.MachineLearningServices.ModelDeployed | Wywoływane, gdy modele zostały pomyślnie wdrożone w punkcie końcowym. |
| Microsoft.MachineLearningServices.RunUpełnione | Wywoływane po pomyślnym zakończeniu uruchomienia. |
| Microsoft.MachineLearningServices.DatasetDriftDetected | Wywoływane, gdy monitor dryfu zestawu danych wykrywa dryf. |
| Microsoft.MachineLearningServices.RunStatusZmienił się | Wywoływane, gdy stan uruchomienia zmienia się na "nie powiodło się". |

### <a name="the-contents-of-an-event-response"></a>Zawartość odpowiedzi na zdarzenie

Po wyzwoleniu zdarzenia usługa Event Grid wysyła dane o tym zdarzeniu do subskrybowania punktu końcowego.

Ta sekcja zawiera przykład tego, jak te dane będą wyglądać dla każdego zdarzenia.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Zdarzenie Microsoft.MachineLearningServices.ModelZarejestrowane zdarzenie

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "eventType": "Microsoft.MachineLearningServices.ModelRegistered",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Zdarzenie Microsoft.MachineLearningServices.ModelDeployed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "eventType": "Microsoft.MachineLearningServices.ModelDeployed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Zdarzenie Microsoft.MachineLearningServices.RunCompleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Zdarzenie Microsoft.MachineLearningServices.DatasetDriftDetected

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "eventType": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesrunstatuschanged-event"></a>Zdarzenie Microsoft.MachineLearningServices.RunStatusZmienił się

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    },
   "RunStatus": "failed"
   },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie jest zapisywalne. Ta wartość jest podawana przez usługę Event Grid. |
| Temat | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| Eventtype | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas, w której zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzenia magazynu obiektów blob. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości dla każdego typu zdarzenia:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft.MachineLearningServices.ModelZarejestrowany

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| ModelName | ciąg | Nazwa modelu, który został zarejestrowany. |
| Wersja modelu | ciąg | Wersja modelu, który został zarejestrowany. |
| Znaczniki modelu | obiekt | Tagi modelu, który został zarejestrowany. |
| Właściwości modelu | obiekt | Właściwości modelu, który został zarejestrowany. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft.MachineLearningServices.ModelDeployed

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| ServiceName | ciąg | Nazwa wdrożonej usługi. |
| Typ kompetysty usługi | ciąg | Typ obliczeniowy (np. ACI, AKS) wdrożonej usługi. |
  | Identyfikatory modelu | ciąg | Oddzielona przecinkami lista identyfikatorów modelu. Identyfikatory modeli wdrożonych w usłudze. |
| Znaczniki usług | obiekt | Tagi wdrożonej usługi. |
| Właściwości usługi | obiekt | Właściwości wdrożonej usługi. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft.MachineLearningServices.RunUpełnione

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Identyfikator eksperymentu | ciąg | Identyfikator eksperymentu, do którego należy przebieg. |
| Nazwa eksperymentu | ciąg | Nazwa eksperymentu, do którego należy przebieg. |
| Nijid | ciąg | Identyfikator run, który został ukończony. |
| Rodzaj uruchamiania | ciąg | Typ uruchomienia ukończonego przebiegu. |
| RunTags (RunTags) | obiekt | Tagi ukończonego przebiegu. |
| Właściwości RunProperties | obiekt | Właściwości ukończonego przebiegu. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft.MachineLearningServices.DatasetDriftDetected

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Identyfikator datadriftId | ciąg | Identyfikator monitora dryfu danych, który wyzwolił zdarzenie. |
| DataDriftName (Nazwa danych) | ciąg | Nazwa monitora dryfu danych, który wyzwolił zdarzenie. |
| Nijid | ciąg | Identyfikator run, który wykrył dryf danych. |
| Zestaw danych bazowych | ciąg | Identyfikator podstawowego zestawu danych używany do wykrywania dryfu. |
| Obiekt Docelowy Zestaw danych | ciąg | Identyfikator docelowego zestawu danych używanego do wykrywania dryfu. |
| DriftCoefficient | double | Wynik współczynnika, który wyzwolił zdarzenie. |
| StartTime | datetime | Czas rozpoczęcia docelowego zestawu danych szeregów czasowych, które doprowadziły do wykrywania dryfu.  |
| EndTime | datetime | Czas zakończenia docelowego zestawu danych szeregów czasowych, które doprowadziły do wykrywania dryfu. |

### <a name="microsoftmachinelearningservicesrunstatuschanged"></a>Microsoft.MachineLearningServices.RunStatusZmienił się

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Identyfikator eksperymentu | ciąg | Identyfikator eksperymentu, do którego należy przebieg. |
| Nazwa eksperymentu | ciąg | Nazwa eksperymentu, do którego należy przebieg. |
| Nijid | ciąg | Identyfikator run, który został ukończony. |
| Rodzaj uruchamiania | ciąg | Typ uruchomienia ukończonego przebiegu. |
| RunTags (RunTags) | obiekt | Tagi ukończonego przebiegu. |
| Właściwości RunProperties | obiekt | Właściwości ukończonego przebiegu. |
| Stan uruchomienia | ciąg | Stan Uruchom. |

## <a name="tutorials-and-how-tos"></a>Samouczki i poradniki
| Tytuł | Opis |
| ----- | ----- |
| [Korzystanie ze zdarzeń usługi Azure Machine Learning](../machine-learning/concept-event-grid-integration.md) | Omówienie integracji usługi Azure Machine Learning z usługą Event Grid. |

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać wprowadzenie do usługi Azure Event Grid, zobacz [Co to jest siatka zdarzeń?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md)
* Aby zapoznać się z wprowadzeniem do korzystania z usługi Azure Event Grid za pomocą usługi Azure Machine Learning, zobacz [Korzystanie ze zdarzeń usługi Azure Machine Learning](/azure/machine-learning/service/concept-event-grid-integration)
* Na przykład przy użyciu usługi Azure Event Grid z usługą Azure Machine Learning zobacz [Tworzenie przepływów pracy uczenia maszynowego opartych na zdarzeniach](/azure/machine-learning/service/how-to-use-event-grid)
