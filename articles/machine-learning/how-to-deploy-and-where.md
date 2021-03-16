---
title: Jak wdrożyć modele uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak i gdzie wdrażać modele uczenia maszynowego. Wdróż do Azure Container Instances, usługi Azure Kubernetes, Azure IoT Edge i FPGA.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 01/13/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
adobe-target: true
ms.openlocfilehash: ed397e9f8db721a6baa641fc958af0dda570ce57
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103561944"
---
# <a name="deploy-machine-learning-models-to-azure"></a>Wdrażanie modeli uczenia maszynowego na platformie Azure

Dowiedz się, jak wdrożyć model uczenia maszynowego lub uczenia głębokiego w ramach usługi sieci Web w chmurze platformy Azure. Możesz również wdrożyć na Azure IoT Edge urządzeń.

Przepływ pracy wygląda podobnie niezależnie od tego, gdzie wdrażasz model:

1. Zarejestruj model (opcjonalnie, patrz poniżej).
1. Przygotuj konfigurację wnioskowania (jeśli nie jest używane [wdrożenie bez kodu](./how-to-deploy-no-code-deployment.md)).
1. Przygotuj skrypt wejścia (chyba że jest używany do [wdrożenia bez kodu](./how-to-deploy-no-code-deployment.md)).
1. Wybierz element docelowy obliczeń.
1. Wdrożenie modelu na docelowym obiekcie obliczeniowym.
1. Przetestuj utworzoną usługę sieci Web.

Aby uzyskać więcej informacji na temat pojęć związanych z przepływem pracy wdrożenia uczenia maszynowego, zobacz artykuł [Zarządzanie, wdrażanie i monitorowanie modeli przy użyciu Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).
- Model. Jeśli nie masz przeszkolonego modelu, możesz użyć modelu i plików zależności, które są dostępne w [tym samouczku](https://aka.ms/azml-deploy-cloud).
- [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning](reference-azure-machine-learning-cli.md).

# <a name="python"></a>[Python](#tab/python)

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).
- Model. Jeśli nie masz przeszkolonego modelu, możesz użyć modelu i plików zależności, które są dostępne w [tym samouczku](https://aka.ms/azml-deploy-cloud).
- [Zestaw SDK (Azure Machine Learning Software Development Kit) dla języka Python](/python/api/overview/azure/ml/intro).

---

## <a name="connect-to-your-workspace"></a>Nawiązywanie połączenia z obszarem roboczym

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

Postępuj zgodnie z instrukcjami w dokumentacji interfejsu wiersza polecenia platformy Azure, aby [ustawić kontekst subskrypcji](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

Następnie wykonaj następujące czynności:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

Aby wyświetlić obszary robocze, do których masz dostęp.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Aby uzyskać więcej informacji na temat używania zestawu SDK do łączenia się z obszarem roboczym, zobacz dokumentację [zestawu Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/intro#workspace) .


---


## <a name="register-your-model-optional"></a><a id="registermodel"></a> Zarejestruj model (opcjonalnie)

Zarejestrowany model to logiczny kontener dla co najmniej jednego pliku, który składa się z modelu. Na przykład jeśli masz model, który jest przechowywany w wielu plikach, możesz zarejestrować je jako jeden model w obszarze roboczym. Po zarejestrowaniu plików można pobrać lub wdrożyć zarejestrowany model i odebrać wszystkie zarejestrowane pliki.

> [!TIP] 
> Zarejestrowanie modelu śledzenia wersji jest zalecane, ale nie jest wymagane. Jeśli wolisz korzystać bez rejestrowania modelu, musisz określić katalog źródłowy w [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) lub [inferenceconfig.jsna](./reference-azure-machine-learning-cli.md#inference-configuration-schema) i upewnić się, że Twój model znajduje się w tym katalogu źródłowym.

> [!TIP]
> Po zarejestrowaniu modelu podajesz ścieżkę do lokalizacji w chmurze (z przebiegu szkoleniowego) lub katalogu lokalnego. Ta ścieżka służy tylko do lokalizowania plików do przekazania w ramach procesu rejestracji. Nie musi być zgodna ze ścieżką używaną w skrypcie wprowadzania. Aby uzyskać więcej informacji, zobacz [Lokalizowanie plików modelu w skrypcie wprowadzania](./how-to-deploy-advanced-entry-script.md#load-registered-models).

> [!IMPORTANT]
> Korzystając z opcji Filtruj według `Tags` na stronie modele Azure Machine Learning Studio, zamiast korzystać z `TagName : TagValue` klientów należy używać `TagName=TagValue` (bez spacji)

W poniższych przykładach pokazano, jak zarejestrować model.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Zarejestruj model na podstawie przebiegu szkolenia z usługi Azure ML

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

`--asset-path`Parametr odnosi się do lokalizacji w chmurze modelu. W tym przykładzie jest używana ścieżka pojedynczego pliku. Aby uwzględnić w rejestracji modelu wiele plików, ustaw `--asset-path` ścieżkę folderu zawierającego pliki.

### <a name="register-a-model-from-a-local-file"></a>Rejestrowanie modelu z pliku lokalnego

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Aby uwzględnić w rejestracji modelu wiele plików, ustaw `-p` ścieżkę folderu zawierającego pliki.

Aby uzyskać więcej informacji na temat `az ml model register` , zapoznaj się z [dokumentacją referencyjną](/cli/azure/ext/azure-cli-ml/ml/model).

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Zarejestruj model na podstawie przebiegu szkolenia z usługi Azure ML

  Gdy korzystasz z zestawu SDK do uczenia modelu, możesz otrzymać obiekt [Run](/python/api/azureml-core/azureml.core.run.run) lub obiekt [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) , w zależności od tego, jak został przeszkolony model. Każdy obiekt może służyć do rejestrowania modelu utworzonego przez uruchomienie eksperymentu.

  + Zarejestruj model na podstawie `azureml.core.Run` obiektu:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path`Parametr odnosi się do lokalizacji w chmurze modelu. W tym przykładzie jest używana ścieżka pojedynczego pliku. Aby uwzględnić w rejestracji modelu wiele plików, ustaw `model_path` ścieżkę folderu zawierającego pliki. Aby uzyskać więcej informacji, zobacz dokumentację [Run.register_model](/python/api/azureml-core/azureml.core.run.run#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) .

  + Zarejestruj model na podstawie `azureml.train.automl.run.AutoMLRun` obiektu:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    W tym przykładzie `metric` `iteration` Parametry i nie są określone, więc iteracja z najlepszą metryką podstawową zostanie zarejestrowana. `model_id`Wartość zwracana z przebiegu jest używana zamiast nazwy modelu.

    Aby uzyskać więcej informacji, zobacz dokumentację [AutoMLRun.register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) .

    W celu wdrożenia zarejestrowanego modelu z programu `AutoMLRun` zalecamy użycie [przycisku Wdróż po jednym kliknięciu w usłudze Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md#deploy-your-model). 
### <a name="register-a-model-from-a-local-file"></a>Rejestrowanie modelu z pliku lokalnego

Model można zarejestrować, podając ścieżkę lokalną modelu. Można podać ścieżkę do folderu lub pojedynczego pliku. Za pomocą tej metody można rejestrować modele przeszkolone przy użyciu Azure Machine Learning, a następnie pobrać. Tej metody można również użyć do rejestrowania modeli przeszkolonych poza Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Korzystanie z zestawu SDK i ONNX**

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

  Aby uwzględnić w rejestracji modelu wiele plików, ustaw `model_path` ścieżkę folderu zawierającego pliki.

Aby uzyskać więcej informacji, zobacz dokumentację [klasy model](/python/api/azureml-core/azureml.core.model.model).

Aby uzyskać więcej informacji na temat pracy z modelami przeszkolonymi poza Azure Machine Learning, zobacz [jak wdrożyć istniejący model](how-to-deploy-existing-model.md).

---

## <a name="define-an-entry-script"></a>Zdefiniuj skrypt wpisu

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Definiowanie konfiguracji wnioskowania


Konfiguracja wnioskowania opisuje sposób konfigurowania usługi sieci Web zawierającej model. Jest on używany później podczas wdrażania modelu.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

Minimalną konfigurację wnioskowania można napisać jako:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": "./working_dir"
}
```

Oznacza to, że wdrożenie uczenia maszynowego będzie używać pliku `score.py` w `./working_dir` katalogu do przetwarzania żądań przychodzących.

Zapoznaj się z [tym artykułem](./reference-azure-machine-learning-cli.md#inference-configuration-schema) , aby uzyskać bardziej szczegółowe omówienie konfiguracji wnioskowania. 

# <a name="python"></a>[Python](#tab/python)

Poniższy przykład ilustruje:

1. Ładowanie [środowiska nadzorowanego](resource-curated-environments.md) z obszaru roboczego
1. Klonowanie środowiska
1. Określanie `scikit-learn` jako zależność.
1. Tworzenie InferenceConfig przy użyciu środowiska

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


env = Environment.get(workspace, "AzureML-Minimal").clone(env_name)

for pip_package in ["scikit-learn"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=env)
```

Aby uzyskać więcej informacji o środowiskach, zobacz [Tworzenie środowisk i zarządzanie nimi na potrzeby szkolenia i wdrażania](how-to-use-environments.md).

Aby uzyskać więcej informacji na temat konfiguracji wnioskowania, zobacz dokumentację klasy [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) .

---

> [!TIP] 
> Aby uzyskać informacje na temat używania niestandardowego obrazu platformy Docker z konfiguracją wnioskowania, zobacz [jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md).

## <a name="choose-a-compute-target"></a>Wybierz element docelowy obliczeń

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Zdefiniuj konfigurację wdrożenia

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

Opcje dostępne dla konfiguracji wdrożenia różnią się w zależności od wybranego elementu docelowego obliczeń.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Aby uzyskać więcej informacji, zobacz [to odwołanie](./reference-azure-machine-learning-cli.md#deployment-configuration-schema).

# <a name="python"></a>[Python](#tab/python)

Przed wdrożeniem modelu należy zdefiniować konfigurację wdrożenia. *Konfiguracja wdrożenia jest specyficzna dla elementu docelowego obliczeń, który będzie hostować usługę sieci Web.* Na przykład podczas lokalnego wdrażania modelu należy określić port, w którym usługa akceptuje żądania. Konfiguracja wdrożenia nie jest częścią skryptu wejścia. Służy do definiowania właściwości obiektu docelowego obliczeń, który będzie hostować model i skrypt wejścia.

Może być również konieczne utworzenie zasobu obliczeniowego, jeśli na przykład nie masz jeszcze wystąpienia usługi Azure Kubernetes Service (AKS) skojarzonego z Twoim obszarem roboczym.

Poniższa tabela zawiera przykład tworzenia konfiguracji wdrożenia dla każdego obiektu docelowego obliczeń:

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

## <a name="deploy-your-machine-learning-model"></a>Wdróż model uczenia maszynowego

Teraz można przystąpić do wdrażania modelu. 

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

### <a name="using-a-registered-model"></a>Korzystanie z zarejestrowanego modelu

Jeśli model został zarejestrowany w obszarze roboczym Azure Machine Learning, Zastąp ciąg "webmodeling: 1" nazwą Twojego modelu i numerem wersji.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Korzystanie z modelu lokalnego

Jeśli wolisz nie rejestrować modelu, możesz przekazać parametr "sourceDirectory" w inferenceconfig.js, aby określić katalog lokalny, który ma być obsługiwany przez model.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano lokalne wdrożenie. Składnia różni się w zależności od celu obliczeń wybranego w poprzednim kroku.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [LocalWebservice](/python/api/azureml-core/azureml.core.webservice.local.localwebservice), [model. deploy ()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)i usługi sieci [Web](/python/api/azureml-core/azureml.core.webservice.webservice).

---

### <a name="understanding-service-state"></a>Informacje o stanie usługi

Podczas wdrażania modelu może zostać wyświetlona zmiana stanu usługi podczas jej pełnego wdrożenia.

W poniższej tabeli opisano różne stany usług:

| Stan usługi WebService | Opis | Końcowy stan?
| ----- | ----- | ----- |
| Przechodzenie | Usługa jest w trakcie wdrażania. | Nie |
| Nieprawidłowy | Usługa została wdrożona, ale jest obecnie nieosiągalna.  | Nie |
| Unschedulable | Nie można teraz wdrożyć usługi z powodu braku zasobów. | Nie |
| Niepowodzenie | Wdrożenie usługi nie powiodło się z powodu błędu lub awarii. | Tak |
| Dobra kondycja | Usługa jest w dobrej kondycji, a punkt końcowy jest dostępny. | Tak |

> [!TIP]
> Podczas wdrażania obrazy platformy Docker dla obiektów docelowych obliczeń są kompilowane i ładowane z Azure Container Registry (ACR). Domyślnie Azure Machine Learning tworzy ACR, który korzysta z warstwy usługi *podstawowa* . Zmiana ACR dla obszaru roboczego na warstwę Standardowa lub Premium może skrócić czas potrzebny do kompilowania i wdrażania obrazów w celach obliczeniowych. Aby uzyskać więcej informacji, zobacz [Azure Container Registry warstwy usług](../container-registry/container-registry-skus.md).

> [!NOTE]
> W przypadku wdrażania modelu w usłudze Azure Kubernetes Service (AKS) zaleca się włączenie [Azure monitor](../azure-monitor/containers/container-insights-enable-existing-clusters.md) dla tego klastra. Ułatwi to zrozumienie ogólnej kondycji klastra i użycia zasobów. Przydatne może być również znalezienie następujących zasobów:
>
> * [Sprawdzaj, czy Resource Health zdarzenia mające wpływ na klaster AKS](../aks/aks-resource-health.md)
> * [Diagnostyka usługi Azure Kubernetes](../aks/concepts-diagnostics.md)
>
> Jeśli próbujesz wdrożyć model w złej kondycji lub przeciążonym klastrze, oczekiwano problemów. Jeśli potrzebujesz pomocy w rozwiązywaniu problemów z klastrem AKS, skontaktuj się z pomocą techniczną AKS.

### <a name="batch-inference"></a><a id="azuremlcompute"></a> Wnioskowanie partii
Azure Machine Learning obiekty docelowe obliczeń są tworzone i zarządzane przez Azure Machine Learning. Mogą one służyć do prognozowania partii z potoków Azure Machine Learning.

Aby zapoznać się z przewodnikiem dotyczącym wnioskowania partii przy użyciu obliczeń Azure Machine Learning, zobacz [jak uruchamiać przewidywania wsadowe](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge wnioskowanie
Obsługa wdrażania na brzegu jest w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [wdrażanie Azure Machine Learning jako moduł IoT Edge](../iot-edge/tutorial-deploy-machine-learning.md).

## <a name="delete-resources"></a>Usuwanie zasobów

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

Aby usunąć wdrożoną usługę sieci Web, użyj programu `az ml service <name of webservice>` .

Aby usunąć zarejestrowany model z obszaru roboczego, użyj `az ml model delete <model id>`

Przeczytaj więcej [na temat usuwania usługi sieci Web](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) i [usuwania modelu](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete).

# <a name="python"></a>[Python](#tab/python)

Aby usunąć wdrożoną usługę sieci Web, użyj programu `service.delete()` .
Aby usunąć zarejestrowany model, użyj `model.delete()` .

Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą usługi [WebService. Delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--) i [model. Delete ()](/python/api/azureml-core/azureml.core.model.model#delete--).

---

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z niepowodzeniem wdrożenia](how-to-troubleshoot-deployment.md)
* [Wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Tworzenie aplikacji klienckich w celu korzystania z usług sieci Web](how-to-consume-web-service.md)
* [Aktualizowanie usługi internetowej](how-to-deploy-update-web-service.md)
* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Wdrożenie jednego kliknięcia dla zautomatyzowanych przebiegów ML w Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)
* [Zabezpieczanie usługi internetowej za pomocą usługi Azure Machine Learning przy użyciu protokołu TLS](how-to-secure-web-service.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Tworzenie alertów zdarzeń i wyzwalaczy na potrzeby wdrożeń modeli](how-to-use-event-grid.md)