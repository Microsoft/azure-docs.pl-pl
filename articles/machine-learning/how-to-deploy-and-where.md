---
title: Jak i gdzie wdrażać modele
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak i gdzie wdrażać modele Azure Machine Learning, w tym Azure Container Instances, Kubernetes usługi Azure, Azure IoT Edge i programowalne pola tablic bram.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: d7502414f6476cafcc85bbefd28a4ec463f62099
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751684"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Wdrażanie modeli za pomocą usługi Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak wdrożyć model uczenia maszynowego jako usługę sieci Web w chmurze platformy Azure lub Azure IoT Edge urządzeń.

Przepływ pracy wygląda podobnie niezależnie od tego, gdzie wdrażasz model:

1. Zarejestrowanie modelu.
1. Przygotuj konfigurację wnioskowania
1. Przygotowywanie skryptu wprowadzania (chyba że jest używane [wdrożenie bez kodu](how-to-deploy-no-code-deployment.md))
1. Wdrożenie modelu na docelowym obiekcie obliczeniowym.
1. Przetestuj wdrożony model, nazywany również usługą sieci Web.

Aby uzyskać więcej informacji na temat pojęć związanych z przepływem pracy wdrożenia, zobacz artykuł [Zarządzanie, wdrażanie i monitorowanie modeli przy użyciu Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).
- Model. Jeśli nie masz przeszkolonego modelu, możesz użyć modelu i plików zależności, które są dostępne w [tym samouczku](https://aka.ms/azml-deploy-cloud).
- [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning](reference-azure-machine-learning-cli.md).

# <a name="python"></a>[Python](#tab/python)

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).
- Model. Jeśli nie masz przeszkolonego modelu, możesz użyć modelu i plików zależności, które są dostępne w [tym samouczku](https://aka.ms/azml-deploy-cloud).
- [Zestaw SDK (Azure Machine Learning Software Development Kit) dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

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

Aby uzyskać więcej informacji na temat używania zestawu SDK do łączenia się z obszarem roboczym, zobacz dokumentację [zestawu Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) .


---


## <a name="register-your-model"></a><a id="registermodel"></a> Zarejestruj model

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

Zarejestrowany model to logiczny kontener dla co najmniej jednego pliku, który składa się z modelu. Na przykład jeśli masz model, który jest przechowywany w wielu plikach, możesz zarejestrować je jako jeden model w obszarze roboczym. Po zarejestrowaniu plików można pobrać lub wdrożyć zarejestrowany model i odebrać wszystkie zarejestrowane pliki.

> [!TIP]
> Po zarejestrowaniu modelu podajesz ścieżkę do lokalizacji w chmurze (z przebiegu szkoleniowego) lub katalogu lokalnego. Ta ścieżka służy tylko do lokalizowania plików do przekazania w ramach procesu rejestracji. Nie musi być zgodna ze ścieżką używaną w skrypcie wprowadzania. Aby uzyskać więcej informacji, zobacz [Lokalizowanie plików modelu w skrypcie wprowadzania](how-to-deploy-advanced-entry-script.md#load-registered-models).

Modele uczenia maszynowego są rejestrowane w obszarze roboczym Azure Machine Learning. Model może pochodzić z Azure Machine Learning lub w innym miejscu. Podczas rejestrowania modelu można opcjonalnie dostarczyć metadane dotyczące modelu. `tags` `properties` Do filtrowania modeli można używać słowników i, które są stosowane do rejestracji modelu.

W poniższych przykładach pokazano, jak zarejestrować model.

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


Zarejestrowany model to logiczny kontener dla co najmniej jednego pliku, który składa się z modelu. Na przykład jeśli masz model, który jest przechowywany w wielu plikach, możesz zarejestrować je jako jeden model w obszarze roboczym. Po zarejestrowaniu plików można pobrać lub wdrożyć zarejestrowany model i odebrać wszystkie zarejestrowane pliki.

> [!TIP]
> Po zarejestrowaniu modelu podajesz ścieżkę do lokalizacji w chmurze (z przebiegu szkoleniowego) lub katalogu lokalnego. Ta ścieżka służy tylko do lokalizowania plików do przekazania w ramach procesu rejestracji. Nie musi być zgodna ze ścieżką używaną w skrypcie wprowadzania. Aby uzyskać więcej informacji, zobacz [Lokalizowanie plików modelu w skrypcie wprowadzania](how-to-deploy-advanced-entry-script.md#load-registered-models).

Modele uczenia maszynowego są rejestrowane w obszarze roboczym Azure Machine Learning. Model może pochodzić z Azure Machine Learning lub w innym miejscu. Podczas rejestrowania modelu można opcjonalnie dostarczyć metadane dotyczące modelu. `tags` `properties` Do filtrowania modeli można używać słowników i, które są stosowane do rejestracji modelu.

W poniższych przykładach pokazano, jak zarejestrować model.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Zarejestruj model na podstawie przebiegu szkolenia z usługi Azure ML

  Gdy korzystasz z zestawu SDK do uczenia modelu, możesz otrzymać obiekt [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) lub obiekt [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) , w zależności od tego, jak został przeszkolony model. Każdy obiekt może służyć do rejestrowania modelu utworzonego przez uruchomienie eksperymentu.

  + Zarejestruj model na podstawie `azureml.core.Run` obiektu:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path`Parametr odnosi się do lokalizacji w chmurze modelu. W tym przykładzie jest używana ścieżka pojedynczego pliku. Aby uwzględnić w rejestracji modelu wiele plików, ustaw `model_path` ścieżkę folderu zawierającego pliki. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [uruchamiania. register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) .

  + Zarejestruj model na podstawie `azureml.train.automl.run.AutoMLRun` obiektu:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    W tym przykładzie `metric` `iteration` Parametry i nie są określone, więc iteracja z najlepszą metryką podstawową zostanie zarejestrowana. `model_id`Wartość zwracana z przebiegu jest używana zamiast nazwy modelu.

    Aby uzyskać więcej informacji, zobacz dokumentację [AutoMLRun. register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) .


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

Aby uzyskać więcej informacji, zobacz dokumentację [klasy model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Aby uzyskać więcej informacji na temat pracy z modelami przeszkolonymi poza Azure Machine Learning, zobacz [jak wdrożyć istniejący model](how-to-deploy-existing-model.md).

---

## <a name="define-an-entry-script"></a>Zdefiniuj skrypt wpisu

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Definiowanie konfiguracji wnioskowania

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

Następujące polecenie pokazuje, jak wdrożyć model przy użyciu interfejsu wiersza polecenia:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

W tym przykładzie konfiguracja określa następujące ustawienia:

* Że model wymaga języka Python
* [Skrypt wejścia](#define-an-entry-script), który jest używany do obsługi żądań sieci Web wysyłanych do wdrożonej usługi
* Plik Conda opisujący pakiety języka Python, które są zbędne do wnioskowania

Aby uzyskać informacje na temat używania niestandardowego obrazu platformy Docker z konfiguracją wnioskowania, zobacz [jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md).

# <a name="python"></a>[Python](#tab/python)

Konfiguracja wnioskowania opisuje sposób konfigurowania usługi sieci Web zawierającej model. Jest on używany później podczas wdrażania modelu.

Konfiguracja wnioskowania używa środowisk Azure Machine Learning do definiowania zależności oprogramowania potrzebnych do wdrożenia. Środowiska umożliwiają tworzenie i ponowne używanie zależności oprogramowania wymaganych do szkolenia i wdrażania, a także zarządzanie nimi. Środowisko można utworzyć na podstawie niestandardowych plików zależności lub użyć jednego z nadzorowanych środowisk Azure Machine Learning. Następujący YAML jest przykładem pliku zależności Conda dla wnioskowania. Należy zauważyć, że należy wskazać wartość domyślną usługi Azure Wersja >= 1.0.45 jako zależność PIP, ponieważ zawiera ona funkcje wymagane do hostowania modelu jako usługi sieci Web. Jeśli chcesz użyć automatycznej generacji schematu, skrypt wejściowy musi również zaimportować `inference-schema` pakiety.

```YAML

name: project_environment
dependencies:
- python=3.6.2
- scikit-learn=0.22.1
- pip:
 # You must list azureml-defaults as a pip dependency
 - azureml-defaults>=1.0.45
 - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Jeśli zależność jest dostępna zarówno w Conda, jak i PIP (od PyPi), firma Microsoft zaleca korzystanie z wersji Conda, ponieważ pakiety Conda zwykle są dostarczane z wstępnie skompilowanymi plikami binarnymi, które sprawiają, że instalacja jest bardziej niezawodna.
>
> Aby uzyskać więcej informacji, zobacz [Omówienie Conda i PIP](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Aby sprawdzić, czy zależność jest dostępna za pomocą Conda, użyj `conda search <package-name>` polecenia lub użyj indeksów pakietów w [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) i [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) .

Możesz użyć pliku zależności, aby utworzyć obiekt środowiska i zapisać go w obszarze roboczym do użycia w przyszłości:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                                file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

Szczegółowe omówienie używania i dostosowywania środowisk Python przy użyciu Azure Machine Learning można znaleźć w temacie [tworzenie & używanie środowisk oprogramowania w Azure Machine Learning](how-to-use-environments.md).

Aby uzyskać informacje na temat używania niestandardowego obrazu platformy Docker z konfiguracją wnioskowania, zobacz [jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md).


Poniższy przykład ilustruje ładowanie środowiska z obszaru roboczego, a następnie używanie go z konfiguracją wnioskowania:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=myenv)
```

Aby uzyskać więcej informacji o środowiskach, zobacz [Tworzenie środowisk i zarządzanie nimi na potrzeby szkolenia i wdrażania](how-to-use-environments.md).

Aby uzyskać więcej informacji na temat konfiguracji wnioskowania, zobacz dokumentację klasy [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

---

## <a name="choose-a-compute-target"></a>Wybierz element docelowy obliczeń

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Zdefiniuj konfigurację wdrożenia

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

Opcje dostępne dla konfiguracji wdrożenia różnią się w zależności od wybranego elementu docelowego obliczeń.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Więcej informacji można znaleźć w dokumentacji [AZ ml model Deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) .

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



## <a name="deploy-your-model"></a>Wdrażanie modelu

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

Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)i usługi sieci [Web](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

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


### <a name="batch-inference"></a><a id="azuremlcompute"></a> Wnioskowanie partii
Azure Machine Learning obiekty docelowe obliczeń są tworzone i zarządzane przez Azure Machine Learning. Mogą one służyć do prognozowania partii z potoków Azure Machine Learning.

Aby zapoznać się z przewodnikiem dotyczącym wnioskowania partii przy użyciu obliczeń Azure Machine Learning, zobacz [jak uruchamiać przewidywania wsadowe](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge wnioskowanie
Obsługa wdrażania na brzegu jest w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [wdrażanie Azure Machine Learning jako moduł IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).

## <a name="delete-resources"></a>Usuwanie zasobów

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

Aby usunąć wdrożoną usługę sieci Web, użyj programu `az ml service <name of webservice>` .

Aby usunąć zarejestrowany model z obszaru roboczego, użyj `az ml model delete <model id>`

Przeczytaj więcej [na temat usuwania usługi sieci Web](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) i [usuwania modelu](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete).

# <a name="python"></a>[Python](#tab/python)

Aby usunąć wdrożoną usługę sieci Web, użyj programu `service.delete()` .
Aby usunąć zarejestrowany model, użyj `model.delete()` .

Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą usługi [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) i [model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

---


## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z niepowodzeniem wdrożenia](how-to-troubleshoot-deployment.md)
* [Wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Tworzenie aplikacji klienckich w celu korzystania z usług sieci Web](how-to-consume-web-service.md)
* [Aktualizowanie usługi internetowej](how-to-deploy-update-web-service.md)
* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Zabezpieczanie usługi internetowej za pomocą usługi Azure Machine Learning przy użyciu protokołu TLS](how-to-secure-web-service.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Tworzenie alertów zdarzeń i wyzwalaczy na potrzeby wdrożeń modeli](how-to-use-event-grid.md)

