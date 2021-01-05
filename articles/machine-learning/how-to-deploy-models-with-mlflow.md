---
title: Wdrażanie eksperymentów ML z MLflow
titleSuffix: Azure Machine Learning
description: Skonfiguruj MLflow z Azure Machine Learning, aby wdrożyć modele sieci w postaci usługi internetowej.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: b905b050752e2a6b7acd11e82420c0b0203dfcd1
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882198"
---
# <a name="deploy-mlflow-models-with-azure-machine-learning-preview"></a>Wdrażanie modeli MLflow za pomocą Azure Machine Learning (wersja zapoznawcza)

W tym artykule dowiesz się, jak wdrożyć model [MLflow](https://www.mlflow.org) jako usługę sieci Web Azure Machine Learning, dzięki czemu możesz korzystać z funkcji zarządzania modelami Azure Machine Learning i ich wykrywania oraz stosować je do modeli produkcyjnych.

Azure Machine Learning oferuje konfiguracje wdrożenia dla:
* Azure Container Instance (ACI), który jest odpowiednim wyborem do szybkiego wdrożenia w ramach tworzenia i testowania.
* Usługa Azure Kubernetes Service (AKS), która jest zalecana w przypadku skalowalnych wdrożeń produkcyjnych.

MLflow to biblioteka typu open source służąca do zarządzania cyklem życia eksperymentów uczenia maszynowego. Integracja z Azure Machine Learning pozwala na rozszerzenie tego zarządzania ponad fazę szkolenia modelowego na fazę wdrożenia modelu produkcyjnego.

>[!NOTE]
> Jako Biblioteka open source MLflow często zmienia się. W związku z tym funkcje udostępnione w ramach integracji Azure Machine Learning i MLflow powinny być traktowane jako wersja zapoznawcza i nie są w pełni obsługiwane przez firmę Microsoft.

Na poniższym diagramie przedstawiono, że za pomocą interfejsu API wdrażania MLflow i Azure Machine Learning można wdrażać modele utworzone przy użyciu popularnych platform, takich jak PyTorch, Tensorflow, scikit — uczenie się, jak Azure Machine Learning usługi sieci Web i zarządzać nimi w obszarze roboczym. 

![ Wdrażanie modeli mlflow za pomocą usługi Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

> [!TIP]
> Informacje przedstawione w tym dokumencie są przeznaczone głównie dla analityków danych i deweloperów, którzy chcą wdrożyć model MLflow w punkcie końcowym usługi sieci Web Azure Machine Learning. Jeśli jesteś administratorem zainteresowani monitorowaniem użycia zasobów i zdarzeń z Azure Machine Learning, takich jak przydziały, ukończone przebiegi szkoleniowe lub wdrożone wdrożenia modelu, zobacz [Azure Machine Learning monitorowania](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Model uczenia maszynowego. Jeśli nie masz nauczonego modelu, Znajdź przykładowy Notes, który najlepiej pasuje do scenariusza obliczeń w [tym repozytorium](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) , i postępuj zgodnie z instrukcjami. 
* [Skonfiguruj identyfikator URI śledzenia MLflow, aby połączyć Azure Machine Learning](how-to-use-mlflow.md#track-local-runs).
* Zainstaluj pakiet `azureml-mlflow`. 
    * Ten pakiet automatycznie łączy `azureml-core` [zestaw SDK języka Python Azure Machine Learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py), który zapewnia łączność z MLflow w celu uzyskania dostępu do obszaru roboczego.
* Zobacz, które [uprawnienia dostępu należy wykonać, aby wykonywać operacje MLflow w Twoim obszarze roboczym](how-to-assign-roles.md#mlflow-operations). 

## <a name="deploy-to-aci"></a>Wdrażanie w usłudze ACI

Aby wdrożyć model MLflow w usłudze sieci Web Azure Machine Learning, należy skonfigurować model przy użyciu [identyfikatora URI śledzenia MLflow w celu nawiązania połączenia z Azure Machine Learning](how-to-use-mlflow.md). 

Skonfiguruj konfigurację wdrożenia za pomocą metody [deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Możesz również dodać tagi i opisy, aby pomóc w śledzeniu usługi sieci Web.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Następnie zarejestruj i Wdróż model w jednym kroku przy użyciu metody [wdrażania](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) MLflow dla Azure Machine Learning. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

## <a name="deploy-to-aks"></a>Wdrażanie w usłudze AKS

Aby wdrożyć model MLflow w usłudze sieci Web Azure Machine Learning, należy skonfigurować model przy użyciu [identyfikatora URI śledzenia MLflow w celu nawiązania połączenia z Azure Machine Learning](how-to-use-mlflow.md). 

Aby wdrożyć AKS, należy najpierw utworzyć klaster AKS. Utwórz klaster AKS przy użyciu metody [ComputeTarget. Create ()](/python/api/azureml-core/azureml.core.computetarget?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-workspace--name--provisioning-configuration-) . Utworzenie nowego klastra może potrwać 20-25 minut.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Skonfiguruj konfigurację wdrożenia za pomocą metody [deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Możesz również dodać tagi i opisy, aby pomóc w śledzeniu usługi sieci Web.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

Następnie zarejestruj i Wdróż model w jednym kroku przy użyciu metody [wdrażania](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) MLflow dla Azure Machine Learning. 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

Wdrożenie usługi może potrwać kilka minut.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie planujesz używać wdrożonej usługi sieci Web, użyj, `service.delete()` Aby usunąć ją z notesu.  Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą usługi [WebService. Delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--).

## <a name="example-notebooks"></a>Przykładowe notesy

[MLflow z notesami Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) prezentuje i rozwijają się w oparciu o koncepcje przedstawione w tym artykule.

> [!NOTE]
> Repozytorium oparte na społeczności przykłady korzystające z usługi mlflow można znaleźć pod adresem https://github.com/Azure/azureml-examples .

## <a name="next-steps"></a>Następne kroki

* [Zarządzaj modelami](concept-model-management-and-deployment.md).
* Monitoruj modele produkcyjne pod kątem [dryfowania danych](./how-to-enable-data-collection.md).
* [Śledzenie przebiegów Azure Databricks z MLflow](how-to-use-mlflow-azure-databricks.md).

