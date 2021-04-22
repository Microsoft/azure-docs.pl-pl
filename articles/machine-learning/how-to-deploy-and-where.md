---
title: Jak wdrażać modele uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak i gdzie wdrażać modele uczenia maszynowego. Wdrażanie w Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge i FPGA.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 03/25/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli, contperf-fy21q2
adobe-target: true
ms.openlocfilehash: f2128949090ce0ec2aa4ed66eb476384d662953a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872645"
---
# <a name="deploy-machine-learning-models-to-azure"></a>Wdrażanie modeli uczenia maszynowego na platformie Azure

Dowiedz się, jak wdrożyć model uczenia maszynowego lub uczenia głębokiego jako usługę internetową w chmurze platformy Azure. Można również wdrażać na Azure IoT Edge urządzeniach.

Przepływ pracy wygląda podobnie niezależnie od tego, gdzie wdrażasz model:

1. Zarejestruj model (opcjonalnie zobacz poniżej).
1. Przygotuj konfigurację wnioskowania (chyba że [korzystasz z wdrożenia bez użycia kodu).](./how-to-deploy-no-code-deployment.md)
1. Przygotuj skrypt wejściowy (chyba że [korzystasz z wdrożenia bez kodu).](./how-to-deploy-no-code-deployment.md)
1. Wybierz docelowy obiekt obliczeniowy.
1. Wdrożenie modelu na docelowym obiekcie obliczeniowym.
1. Przetestuj wynikową usługę internetową.

Aby uzyskać więcej informacji na temat pojęć związanych z przepływem pracy wdrażania uczenia maszynowego, zobacz [Manage, deploy, and monitor models with Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie Azure Machine Learning roboczego.](how-to-manage-workspace.md)
- Model. Jeśli nie masz wytrenowany model, możesz użyć plików modelu i zależności podanych w [tym samouczku.](https://aka.ms/azml-deploy-cloud)
- Rozszerzenie [interfejsu wiersza polecenia platformy Azure dla Machine Learning service](reference-azure-machine-learning-cli.md).

# <a name="python"></a>[Python](#tab/python)

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie Azure Machine Learning roboczego.](how-to-manage-workspace.md)
- Model. Jeśli nie masz wytrenowany model, możesz użyć plików modelu i zależności podanych w [tym samouczku.](https://aka.ms/azml-deploy-cloud)
- Zestaw [Azure Machine Learning software development kit (SDK) dla języka Python.](/python/api/overview/azure/ml/intro)

---

## <a name="connect-to-your-workspace"></a>Nawiązywanie połączenia z obszarem roboczym

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

Postępuj zgodnie z instrukcjami w dokumentacji interfejsu wiersza polecenia platformy [Azure, aby zmienić kontekst subskrypcji.](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)

Następnie wykonaj:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

, aby wyświetlić obszary robocze, do których masz dostęp.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Aby uzyskać więcej informacji na temat nawiązywania połączenia z obszarem roboczym przy użyciu zestawu SDK, zobacz dokumentację Azure Machine Learning [SDK dla języka Python.](/python/api/overview/azure/ml/intro#workspace)


---


## <a name="register-your-model-optional"></a><a id="registermodel"></a> Rejestrowanie modelu (opcjonalnie)

Zarejestrowany model jest kontenerem logicznym dla co najmniej jednego pliku, który stanowi model. Jeśli na przykład masz model przechowywany w wielu plikach, możesz zarejestrować je jako pojedynczy model w obszarze roboczym. Po zarejestrowaniu plików możesz pobrać lub wdrożyć zarejestrowany model i odebrać wszystkie zarejestrowane pliki.

> [!TIP] 
> Rejestrowanie modelu na potrzeby śledzenia wersji jest zalecane, ale nie jest wymagane. Jeśli wolisz kontynuować bez rejestrowania modelu, musisz określić katalog źródłowy w pliku [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) [ lubinferenceconfig.jsi](./reference-azure-machine-learning-cli.md#inference-configuration-schema) upewnić się, że model znajduje się w tym katalogu źródłowym.

> [!TIP]
> Podczas rejestrowania modelu należy podać ścieżkę lokalizacji w chmurze (z uruchomienia szkoleniowego) lub katalogu lokalnego. Ta ścieżka ma na celu po prostu zlokalizowanie plików do przekazania w ramach procesu rejestracji. Nie musi odpowiadać ścieżce użytej w skrypcie wejściowym. Aby uzyskać więcej informacji, zobacz [Lokalizowanie plików modelu w skrypcie wejściowym](./how-to-deploy-advanced-entry-script.md#load-registered-models).

> [!IMPORTANT]
> W przypadku korzystania z opcji Filtruj według na stronie Modele programu Azure Machine Learning Studio zamiast używania opcji klienci powinni używać `Tags` `TagName : TagValue` opcji `TagName=TagValue` (bez spacji)

W poniższych przykładach pokazano, jak zarejestrować model.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Rejestrowanie modelu z przebiegu trenowania usługi Azure ML

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

Parametr `--asset-path` odwołuje się do lokalizacji modelu w chmurze. W tym przykładzie używana jest ścieżka pojedynczego pliku. Aby uwzględnić wiele plików w rejestracji modelu, ustaw ścieżkę `--asset-path` folderu zawierającego pliki.

### <a name="register-a-model-from-a-local-file"></a>Rejestrowanie modelu z pliku lokalnego

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Aby uwzględnić wiele plików w rejestracji modelu, ustaw ścieżkę `-p` folderu zawierającego pliki.

Aby uzyskać więcej informacji na `az ml model register` temat programu , zapoznaj się z [dokumentacją referencyjną](/cli/azure/ml/model).

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Rejestrowanie modelu z przebiegu trenowania usługi Azure ML

  Gdy używasz zestawu SDK do trenowania modelu, możesz otrzymać obiekt [Run](/python/api/azureml-core/azureml.core.run.run) lub [AutoMLRun,](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) w zależności od tego, jak wytrenowany model. Każdy obiekt może służyć do rejestrowania modelu utworzonego przez przebieg eksperymentu.

  + Rejestrowanie modelu z `azureml.core.Run` obiektu:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    Parametr `model_path` odwołuje się do lokalizacji modelu w chmurze. W tym przykładzie używana jest ścieżka pojedynczego pliku. Aby uwzględnić wiele plików w rejestracji modelu, ustaw ścieżkę `model_path` folderu zawierającego pliki. Aby uzyskać więcej informacji, zobacz [Run.register_model](/python/api/azureml-core/azureml.core.run.run#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) dokumentacji.

  + Rejestrowanie modelu z `azureml.train.automl.run.AutoMLRun` obiektu:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    W tym przykładzie parametry i nie są określone, więc zostanie zarejestrowana iteracja z najlepszą `metric` `iteration` metryką podstawową. Wartość `model_id` zwrócona z uruchomienia jest używana zamiast nazwy modelu.

    Aby uzyskać więcej informacji, zobacz [AutoMLRun.register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) dokumentacji.

    Aby wdrożyć zarejestrowany model z usługi , zalecamy wykonanie tej pracy za pomocą przycisku wdrażania jednym kliknięciem w `AutoMLRun` [usłudze Azure Machine Learning Studio.](how-to-use-automated-ml-for-ml-models.md#deploy-your-model) 
### <a name="register-a-model-from-a-local-file"></a>Rejestrowanie modelu z pliku lokalnego

Możesz zarejestrować model, podając ścieżkę lokalną modelu. Możesz podać ścieżkę folderu lub pojedynczego pliku. Za pomocą tej metody można rejestrować modele wytrenowane za pomocą Azure Machine Learning, a następnie pobierać. Za pomocą tej metody można również rejestrować modele wytrenowane poza Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Korzystanie z zestawu SDK i środowiska ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Aby uwzględnić wiele plików w rejestracji modelu, ustaw ścieżkę `model_path` folderu zawierającego pliki.

Aby uzyskać więcej informacji, zobacz dokumentację klasy [Model](/python/api/azureml-core/azureml.core.model.model).

Aby uzyskać więcej informacji na temat pracy z modelami wytrenowanymi poza Azure Machine Learning, zobacz [Jak wdrożyć istniejący model.](how-to-deploy-existing-model.md)

---

## <a name="define-an-entry-script"></a>Definiowanie skryptu wpisu

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Definiowanie konfiguracji wnioskowania


Konfiguracja wnioskowania opisuje sposób konfigurowania usługi internetowej zawierającej model. Będzie on używany później podczas wdrażania modelu.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

Minimalną konfigurację wnioskowania można zapisywać w następujący sposób:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": "./working_dir",
    "environment": {
    "docker": {
        "arguments": [],
        "baseDockerfile": null,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "enabled": false,
        "sharedVolumes": true,
        "shmSize": null
    },
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "name": "my-deploy-env",
    "python": {
        "baseCondaEnvironment": null,
        "condaDependencies": {
            "channels": [
                "conda-forge",
                "pytorch"
            ],
            "dependencies": [
                "python=3.6.2",
                "torchvision"
                {
                    "pip": [
                        "azureml-defaults",
                        "azureml-telemetry",
                        "scikit-learn==0.22.1",
                        "inference-schema[numpy-support]"
                    ]
                }
            ],
            "name": "project_environment"
        },
        "condaDependenciesFile": null,
        "interpreterPath": "python",
        "userManagedDependencies": false
    },
    "version": "1"
}
```

Oznacza to, że wdrożenie uczenia maszynowego będzie używać pliku w katalogu do przetwarzania żądań przychodzących i że będzie używać obrazu platformy Docker z pakietami języka Python określonymi w `score.py` `./working_dir` `project_environment` środowisku.

[Zapoznaj się z tym artykułem,](./reference-azure-machine-learning-cli.md#inference-configuration-schema) aby uzyskać bardziej szczegółowe omówienie konfiguracji wnioskowania. 

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano:

1. ładowanie środowiska [curated z](resource-curated-environments.md) obszaru roboczego
1. Klonowanie środowiska
1. Określanie `scikit-learn` jako zależności.
1. Używanie środowiska do tworzenia aplikacji InferenceConfig

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


env = Environment.get(workspace, "AzureML-Minimal").clone(env_name)

for pip_package in ["scikit-learn"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=env)
```

Aby uzyskać więcej informacji na temat środowisk, zobacz Create and manage environments for training and deployment (Tworzenie środowisk do [trenowania i wdrażania oraz zarządzanie nimi).](how-to-use-environments.md)

Aby uzyskać więcej informacji na temat konfiguracji wnioskowania, zobacz [dokumentację klasy InferenceConfig.](/python/api/azureml-core/azureml.core.model.inferenceconfig)

---

> [!TIP] 
> Aby uzyskać informacje na temat używania niestandardowego obrazu platformy Docker z konfiguracją wnioskowania, zobacz Jak wdrożyć [model przy użyciu niestandardowego obrazu platformy Docker.](how-to-deploy-custom-docker-image.md)

## <a name="choose-a-compute-target"></a>Wybieranie docelowego obiektu obliczeniowego

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Definiowanie konfiguracji wdrożenia

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

Opcje dostępne dla konfiguracji wdrożenia różnią się w zależności od wybranego docelowego obiektu obliczeniowego.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Aby uzyskać więcej informacji, zobacz [ten temat](./reference-azure-machine-learning-cli.md#deployment-configuration-schema)referencyjny.

# <a name="python"></a>[Python](#tab/python)

Przed wdrożeniem modelu należy zdefiniować konfigurację wdrożenia. *Konfiguracja wdrożenia jest specyficzna dla docelowego obiektu obliczeniowego, który będzie hostować usługę internetową.* Na przykład podczas lokalnego wdrażania modelu należy określić port, na którym usługa akceptuje żądania. Konfiguracja wdrożenia nie jest częścią skryptu wprowadzania. Służy do definiowania właściwości docelowego obiektu obliczeniowego, który będzie hostować model i skrypt wejściowy.

Może być również konieczne utworzenie zasobu obliczeniowego, jeśli na przykład nie masz jeszcze wystąpienia usługi Azure Kubernetes Service (AKS) skojarzonego z obszarem roboczym.

W poniższej tabeli przedstawiono przykład tworzenia konfiguracji wdrożenia dla każdego docelowego obiektu obliczeniowego:

| Docelowy zasób obliczeniowy | Przykład konfiguracji wdrożenia |
| ----- | ----- |
| Lokalne | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Klasy dla lokalnych, Azure Container Instances i usług sieci Web AKS można zaimportować z `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

---

## <a name="deploy-your-machine-learning-model"></a>Wdrażanie modelu uczenia maszynowego

Teraz możesz wdrożyć model. 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

### <a name="using-a-registered-model"></a>Korzystanie z zarejestrowanego modelu

Jeśli model został zarejestrowany w obszarze roboczym Azure Machine Learning, zastąp "mymodel:1" nazwą modelu i jego numerem wersji.

```azurecli-interactive
az ml model deploy -n tutorial -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Korzystanie z modelu lokalnego

Jeśli nie chcesz rejestrować modelu, możesz przekazać parametr "sourceDirectory" w pliku inferenceconfig.jsw celu określenia katalogu lokalnego, z którego ma być pełnić model.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json --name my_deploy
```

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano wdrożenie lokalne. Składnia będzie się różnić w zależności od docelowego obiektu obliczeniowego wybranego w poprzednim kroku.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Aby uzyskać więcej informacji, zobacz dokumentację dla usług [LocalWebservice,](/python/api/azureml-core/azureml.core.webservice.local.localwebservice) [Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)i [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice).

---

### <a name="understanding-service-state"></a>Informacje o stanie usługi

Podczas wdrażania modelu stan usługi może ulec zmianie podczas pełnego wdrożenia.

W poniższej tabeli opisano różne stany usług:

| Stan usługi internetowej | Opis | Stan końcowy?
| ----- | ----- | ----- |
| Przejście | Usługa jest w trakcie wdrażania. | Nie |
| Nieprawidłowy | Usługa została wdrożona, ale jest obecnie nieosiągalna.  | Nie |
| Nieplanowane | W tej chwili nie można wdrożyć usługi ze względu na brak zasobów. | Nie |
| Niepowodzenie | Wdrożenie usługi nie powiodło się z powodu błędu lub awarii. | Tak |
| Dobra kondycja | Usługa jest w dobrej kondycji, a punkt końcowy jest dostępny. | Tak |

> [!TIP]
> Podczas wdrażania obrazy platformy Docker dla docelowych obiektów obliczeniowych są budowane i ładowane z Azure Container Registry (ACR). Domyślnie program Azure Machine Learning usługę ACR, która używa *podstawowej* warstwy usług. Zmiana usługi ACR dla obszaru roboczego na warstwę Standardowa lub Premium może skrócić czas kompilowania i wdrażania obrazów w docelowych obszarach obliczeniowych. Aby uzyskać więcej informacji, [zobacz Azure Container Registry usługi .](../container-registry/container-registry-skus.md)

> [!NOTE]
> Jeśli wdrażasz model do usługi Azure Kubernetes Service (AKS), zalecamy włączenie Azure Monitor [dla](../azure-monitor/containers/container-insights-enable-existing-clusters.md) tego klastra. Pomoże to zrozumieć ogólną kondycję klastra i użycie zasobów. Przydatne mogą być również następujące zasoby:
>
> * [Sprawdź, Resource Health zdarzenia wpływające na klaster usługi AKS](../aks/aks-resource-health.md)
> * [Azure Kubernetes Service diagnostyki](../aks/concepts-diagnostics.md)
>
> Jeśli próbujesz wdrożyć model w klastrze w złej kondycji lub przeciążeniu, oczekiwane jest, że wystąpią problemy. Jeśli potrzebujesz pomocy w rozwiązywaniu problemów z klastrem usługi AKS, skontaktuj się z pomocą techniczną usługi AKS.

### <a name="batch-inference"></a><a id="azuremlcompute"></a> Wnioskowanie wsadowe
Azure Machine Learning obliczeniowe są tworzone i zarządzane przez usługę Azure Machine Learning. Można ich używać do przewidywania wsadowego Azure Machine Learning potoków.

Aby uzyskać wskazówki dotyczące wnioskowania wsadowego za pomocą Azure Machine Learning Compute, zobacz [How to run batch predictions](tutorial-pipeline-batch-scoring-classification.md)(Jak uruchamiać przewidywania partii).

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge wnioskowania
Obsługa wdrażania na brzegu sieci jest w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz Deploy Azure Machine Learning as an IoT Edge module (Wdrażanie IoT Edge [jako moduł).](../iot-edge/tutorial-deploy-machine-learning.md)

## <a name="delete-resources"></a>Usuwanie zasobów

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

Aby usunąć wdrożoną usługę internetową, `az ml service <name of webservice>` użyj .

Aby usunąć zarejestrowany model z obszaru roboczego, użyj `az ml model delete <model id>`

Dowiedz się więcej [o usuwaniu usługi internetowej](/cli/azure/ml/service#az_ml_service_delete) i [usuwaniu modelu.](/cli/azure/ml/model#az_ml_model_delete)

# <a name="python"></a>[Python](#tab/python)

Aby usunąć wdrożoną usługę internetową, użyj programu `service.delete()` .
Aby usunąć zarejestrowany model, `model.delete()` użyj .

Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą plików [WebService.delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--) [i Model.delete().](/python/api/azureml-core/azureml.core.model.model#delete--)

---

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z niepowodzeniem wdrożenia](how-to-troubleshoot-deployment.md)
* [Wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Tworzenie aplikacji klienckich do obsługi usług internetowych](how-to-consume-web-service.md)
* [Aktualizowanie usługi internetowej](how-to-deploy-update-web-service.md)
* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Wdrażanie zautomatyzowanego uczenia maszynowego jednym kliknięciem w Azure Machine Learning studio](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)
* [Zabezpieczanie usługi internetowej za pomocą usługi Azure Machine Learning przy użyciu protokołu TLS](how-to-secure-web-service.md)
* [Monitorowanie modeli Azure Machine Learning za pomocą Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Tworzenie alertów zdarzeń i wyzwalaczy dla wdrożeń modelu](how-to-use-event-grid.md)