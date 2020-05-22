---
title: MLflow śledzenie dla eksperymentów ML
titleSuffix: Azure Machine Learning
description: Skonfiguruj MLflow z Azure Machine Learning, aby rejestrować metryki i artefakty z modeli ML utworzonych w klastrach datas, środowisku lokalnym lub środowisku maszyny wirtualnej.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 95567a177635dc7d7ed03404487e62c76db8bdac
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779125"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>Śledzenie metryk modeli przy użyciu MLflow i Azure Machine Learning (wersja zapoznawcza)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule pokazano, jak włączyć śledzenie MLflow identyfikatorów URI i interfejsów API rejestrowania, wspólnie znane jako [śledzenie MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), aby połączyć eksperymenty MLflow i Azure Machine Learning. Dzięki temu można śledzić i rejestrować metryki i artefakty eksperymentów w [obszarze roboczym Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Jeśli już używasz śledzenia MLflow do eksperymentów, obszar roboczy zapewnia scentralizowaną, bezpieczną i skalowalną lokalizację do przechowywania metryk i modeli szkoleniowych.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[MLflow](https://www.mlflow.org) to biblioteka typu open source służąca do zarządzania cyklem życia eksperymentów uczenia maszynowego. Śledzenie MLFlow jest składnikiem MLflow, który rejestruje i śledzi przebiegi szkoleniowe i artefakty modelu, niezależnie od środowiska eksperymentu — lokalnie na komputerze, na zdalnym serwerze docelowym obliczeń, maszynie wirtualnej lub klastrze Azure Databricks. 

Na poniższym diagramie przedstawiono, że śledzenie MLflow umożliwia śledzenie metryk uruchamiania i modeli magazynu eksperymentu w obszarze roboczym Azure Machine Learning.

![mlflow z diagramem usługi Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Informacje przedstawione w tym dokumencie są przeznaczone głównie dla analityków danych i deweloperów, którzy chcą monitorować proces szkolenia modelu. Jeśli jesteś administratorem zainteresowani monitorowaniem użycia zasobów i zdarzeń z Azure Machine Learning, takich jak przydziały, ukończone przebiegi szkoleniowe lub wdrożone wdrożenia modelu, zobacz [Azure Machine Learning monitorowania](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Porównanie klientów MLflow i Azure Machine Learning

 Poniższa tabela zawiera podsumowanie różnych klientów, którzy mogą używać Azure Machine Learning i ich możliwości funkcji.

 Śledzenie MLflow oferuje funkcje rejestrowania metryk i magazynu artefaktów, które są dostępne tylko w innym przypadku za pośrednictwem [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).


| | &nbsp;Śledzenie MLflow <!--& Deployment--> | Azure Machine Learning Python SDK |  Interfejs wiersza polecenia Azure Machine Learning | Studio uczenia maszynowego Azure|
|---|---|---|---|---|
| Zarządzanie obszarem roboczym |   | ✓ | ✓ | ✓ |
| Korzystanie z magazynów danych  |   | ✓ | ✓ | |
| Metryki dzienników      | ✓ | ✓ |   | |
| Przekaż artefakty | ✓ | ✓ |   | |
| Wyświetlanie metryk     | ✓ | ✓ | ✓ | ✓ |
| Zarządzanie obliczeniami   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>Wymagania wstępne

* [Zainstaluj MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Zainstaluj zestaw sdk Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) na komputerze lokalnym, zestaw SDK zapewnia łączność z MLflow w celu uzyskania dostępu do obszaru roboczego.
* [Utwórz obszar roboczy usługi Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Śledź uruchomienia lokalne

Śledzenie MLflow za pomocą Azure Machine Learning umożliwia przechowywanie zarejestrowanych metryk i artefaktów z lokalnych przebiegów w obszarze roboczym Azure Machine Learning.

Zainstaluj `azureml-mlflow` pakiet, aby używać śledzenia MLflow z Azure Machine Learningami na eksperymentach lokalnie uruchamianych w Jupyter notebook lub edytorze kodu.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>Przestrzeń nazw Azure. contrib często zmienia się, ponieważ usprawniamy działanie usługi. W związku z tym wszystkie elementy w tej przestrzeni nazw powinny być traktowane jako wersja zapoznawcza i nie są w pełni obsługiwane przez firmę Microsoft.

Zaimportuj `mlflow` klasy i, [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) Aby uzyskać dostęp do śledzenia identyfikatorów URI MLflow i skonfigurować obszar roboczy.

W poniższym kodzie `get_mlflow_tracking_uri()` Metoda przypisuje unikatowy adres URI śledzenia do obszaru roboczego, `ws` a następnie wskazuje na ten `set_tracking_uri()` adres identyfikator URI śledzenia MLflow.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>Identyfikator URI śledzenia jest prawidłowy do godziny lub mniej. Jeśli ponownie uruchomisz skrypt po pewnym czasie bezczynności, użyj interfejsu API get_mlflow_tracking_uri, aby uzyskać nowy identyfikator URI.

Ustaw nazwę eksperymentu MLflow z `set_experiment()` i zacznij korzystać z szkolenia w systemie `start_run()` . Następnie użyj, `log_metric()` Aby aktywować interfejs API rejestrowania MLflow i rozpocząć rejestrowanie metryk przebiegu szkoleniowego.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Śledź zdalne uruchomienia

Śledzenie MLflow za pomocą Azure Machine Learning umożliwia przechowywanie zarejestrowanych metryk i artefaktów z zdalnych przebiegów w obszarze roboczym Azure Machine Learning.

Zdalne uruchomienia umożliwiają uczenie modeli przy większej liczbie zaawansowanych obliczeń, takich jak maszyny wirtualne obsługujące procesor GPU lub klastry środowisko obliczeniowe usługi Machine Learning. Zapoznaj się z tematem [Konfigurowanie elementów docelowych obliczeń dla szkolenia modeli](how-to-set-up-training-targets.md) , aby poznać różne opcje obliczeń.

Skonfiguruj środowisko uruchomieniowe obliczeniowe i szkoleniowe za pomocą [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) klasy. Dołącz `mlflow` i `azureml-mlflow` PIP pakiety w sekcji środowiska [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) . Następnie Utwórz [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) z użyciem obliczeń zdalnych jako element docelowy obliczeń.

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-mlflow'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

W skrypcie szkoleniowym zaimportuj, `mlflow` Aby korzystać z interfejsów API rejestrowania MLflow i rozpocząć rejestrowanie metryk przebiegu.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

W przypadku konfiguracji przebiegu obliczeń i szkoleń Użyj `Experiment.submit('train.py')` metody do przesyłania przebiegu. Ta metoda automatycznie ustawia identyfikator URI śledzenia MLflow i kieruje rejestrowanie z MLflow do obszaru roboczego.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Śledź uruchomienia Azure Databricks

Śledzenie MLflow za pomocą Azure Machine Learning umożliwia przechowywanie zarejestrowanych metryk i artefaktów z Azure Databricks uruchomionych w obszarze roboczym Azure Machine Learning.

Aby uruchamiać eksperymenty Mlflow z Azure Databricks, musisz najpierw utworzyć [Azure Databricks obszar roboczy i klaster](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Upewnij się, że w klastrze została zainstalowana Biblioteka usługi *Azure mlflow* z poziomu programu PyPi, aby zapewnić, że klaster ma dostęp do wymaganych funkcji i klas.

W tym miejscu zaimportuj Notes eksperymentu, dołącz go do klastra Azure Databricks i uruchom eksperyment. 

### <a name="install-libraries"></a>Zainstaluj biblioteki

Aby zainstalować biblioteki w klastrze, przejdź do karty **biblioteki** , a następnie kliknij pozycję **Instaluj nowe** .

 ![mlflow z diagramem usługi Azure Machine Learning](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

W polu **pakiet** wpisz polecenie Azure-mlflow, a następnie kliknij przycisk Instaluj. Powtórz ten krok w razie potrzeby, aby zainstalować inne dodatkowe pakiety w klastrze dla eksperymentu.

 ![mlflow z diagramem usługi Azure Machine Learning](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Konfigurowanie notesu i obszaru roboczego

Po skonfigurowaniu klastra zaimportuj Notes eksperymentu, otwórz go i Dołącz do niego klaster.

Poniższy kod powinien znajdować się w notesie eksperymentu. Ten kod pobiera szczegóły subskrypcji platformy Azure w celu utworzenia wystąpienia Twojego obszaru roboczego. Ten kod zakłada, że masz istniejącą grupę zasobów i obszar roboczy Azure Machine Learning, w przeciwnym razie można [je utworzyć](how-to-manage-workspace.md). 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)
```

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Łączenie Azure Databricks i Azure Machine Learning obszarów roboczych

Na [Azure Portal](https://ms.portal.azure.com)można połączyć obszar roboczy Azure DATABRICKS (ADB) z nowym lub istniejącym obszarem roboczym Azure Machine Learning. Aby to zrobić, przejdź do obszaru roboczego ADB i wybierz przycisk **połącz Azure Machine Learning obszar roboczy** w prawym dolnym rogu. Łączenie obszarów roboczych umożliwia śledzenie danych eksperymentów w obszarze roboczym Azure Machine Learning. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Łączenie śledzenia MLflow z obszarem roboczym

Po utworzeniu wystąpienia obszaru roboczego ustaw identyfikator URI śledzenia MLflow. Dzięki temu można połączyć śledzenie MLflow z obszarem roboczym Azure Machine Learning. Po nawiązaniu połączenia wszystkie eksperymenty będą użytkowane w usłudze Managed Azure Machine Learning Tracking.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Bezpośrednio ustaw Śledzenie MLflow w notesie

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

W skrypcie szkoleniowym zaimportuj mlflow do używania interfejsów API rejestrowania MLflow i Rozpocznij rejestrowanie metryk uruchamiania. Poniższy przykład rejestruje metrykę utraty epoki. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Automatyzowanie ustawienia śledzenia MLflow

Zamiast ręcznie skonfigurować identyfikator URI śledzenia w każdej kolejnej sesji notesu eksperymentu w klastrach, należy to zrobić automatycznie przy użyciu tego [Azure Machine Learning śledzenie skryptu inicjowania klastra](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md).

Po poprawnym skonfigurowaniu można zobaczyć dane śledzenia usługi MLflow w interfejsie API REST Azure Machine Learning i wszystkich klientach, a także w Azure Databricks za pośrednictwem interfejsu użytkownika MLflow lub przy użyciu klienta MLflow.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Wyświetlanie metryk i artefaktów w obszarze roboczym

Metryki i artefakty z rejestrowania MLflow są przechowywane w Twoim obszarze roboczym. Aby wyświetlić je w dowolnym momencie, przejdź do obszaru roboczego i Znajdź eksperyment według nazwy w obszarze roboczym w programie [Azure Machine Learning Studio](https://ml.azure.com).  Lub uruchom poniższy kod. 

```python
run.get_metrics()
ws.get_details()
```

<!-- ## Deploy MLflow models as a web service

Deploying your MLflow experiments as an Azure Machine Learning web service allows you to leverage the Azure Machine Learning model management and data drift detection capabilities and apply them to your production models.

The following diagram demonstrates that with the MLflow deploy API you can deploy your existing MLflow models as an Azure Machine Learning web service, despite their frameworks--PyTorch, Tensorflow, scikit-learn, ONNX, etc., and manage your production models in your workspace.

![mlflow with azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### Log your model

Before you can deploy, be sure that your model is saved so you can reference it and its path location for deployment. In your training script, there should be code similar to the following [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) method, that saves your model to the specified outputs directory. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Include the `conda_env` parameter to pass a dictionary representation of the dependencies and environment this model should be run in.

### Retrieve model from previous run

To retrieve the run, you need the run ID and the path in run history of where the model was saved. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### Deploy the model

Use the Azure Machine Learning SDK to deploy the model as a web service.

First, specify the deployment configuration. Azure Container Instance (ACI) is a suitable choice for a quick dev-test deployment, while Azure Kubernetes Service (AKS) is suitable for scalable production deployments.

#### Deploy to ACI

Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster. Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

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
Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')


Then, deploy the image by using the Azure Machine Learning SDK [deploy()](Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice
(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

The service deployment can take several minutes.

## Clean up resources

If you don't plan to use the logged metrics and artifacts in your workspace, the ability to delete them individually is currently unavailable. Instead, delete the resource group that contains the storage account and workspace, so you don't incur any charges:

1. In the Azure portal, select **Resource groups** on the far left.

   ![Delete in the Azure portal](./media/how-to-use-mlflow/delete-resources.png)

1. From the list, select the resource group you created.

1. Select **Delete resource group**.

1. Enter the resource group name. Then select **Delete**.

 -->

 ## <a name="example-notebooks"></a>Przykładowe notesy

[MLflow z usługami Azure ml notesy](https://aka.ms/azureml-mlflow-examples) pokazują i rozszerzają w oparciu o koncepcje przedstawione w tym artykule.

## <a name="next-steps"></a>Następne kroki
* [Zarządzaj modelami](concept-model-management-and-deployment.md).
* Monitoruj modele produkcyjne pod kątem [dryfowania danych](how-to-monitor-data-drift.md).
