---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 624824f5b6b8f7154ccd7b50da49f3f4bb179bb9
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542804"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).
- Model. Jeśli nie masz przeszkolonego modelu, możesz użyć modelu i plików zależności, które są dostępne w [tym samouczku](https://aka.ms/azml-deploy-cloud).
- [Zestaw SDK (Azure Machine Learning Software Development Kit) dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)


## <a name="connect-to-your-workspace"></a>Nawiązywanie połączenia z obszarem roboczym

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Aby uzyskać więcej informacji na temat używania zestawu SDK do łączenia się z obszarem roboczym, zobacz dokumentację [zestawu Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) .

## <a name="register-your-model"></a><a id="registermodel"></a>Zarejestruj model

Zarejestrowany model to logiczny kontener dla co najmniej jednego pliku, który składa się z modelu. Na przykład jeśli masz model, który jest przechowywany w wielu plikach, możesz zarejestrować je jako jeden model w obszarze roboczym. Po zarejestrowaniu plików można pobrać lub wdrożyć zarejestrowany model i odebrać wszystkie zarejestrowane pliki.

> [!TIP]
> Po zarejestrowaniu modelu podajesz ścieżkę do lokalizacji w chmurze (z przebiegu szkoleniowego) lub katalogu lokalnego. Ta ścieżka służy tylko do lokalizowania plików do przekazania w ramach procesu rejestracji. Nie musi być zgodna ze ścieżką używaną w skrypcie wprowadzania. Aby uzyskać więcej informacji, zobacz [Lokalizowanie plików modelu w skrypcie wprowadzania](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models).

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

[!INCLUDE [trusted models](machine-learning-service-trusted-model.md)]

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

Aby uzyskać więcej informacji na temat pracy z modelami przeszkolonymi poza Azure Machine Learning, zobacz [jak wdrożyć istniejący model](../articles/machine-learning/how-to-deploy-existing-model.md).


## <a name="define-an-entry-script"></a>Zdefiniuj skrypt wpisu

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inference-configuration"></a>Definiowanie konfiguracji wnioskowania

Konfiguracja wnioskowania opisuje sposób konfigurowania usługi sieci Web zawierającej model. Jest on używany później podczas wdrażania modelu.

Konfiguracja wnioskowania używa środowisk Azure Machine Learning do definiowania zależności oprogramowania potrzebnych do wdrożenia. Środowiska umożliwiają tworzenie i ponowne używanie zależności oprogramowania wymaganych do szkolenia i wdrażania, a także zarządzanie nimi. Środowisko można utworzyć na podstawie niestandardowych plików zależności lub użyć jednego z nadzorowanych środowisk Azure Machine Learning. Następujący YAML jest przykładem pliku zależności Conda dla wnioskowania. Należy zauważyć, że należy wskazać wartość domyślną usługi Azure Wersja >= 1.0.45 jako zależność PIP, ponieważ zawiera ona funkcje wymagane do hostowania modelu jako usługi sieci Web. Jeśli chcesz użyć automatycznej generacji schematu, skrypt wejściowy musi również zaimportować `inference-schema` pakiety.

```YAML

name: project_environment
dependencies:
    - python=3.6.2
    - scikit-learn=0.20.0
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

Szczegółowe omówienie używania i dostosowywania środowisk Python przy użyciu Azure Machine Learning można znaleźć w temacie [tworzenie & używanie środowisk oprogramowania w Azure Machine Learning](../articles/machine-learning/how-to-use-environments.md)

Aby uzyskać informacje na temat używania niestandardowego obrazu platformy Docker z konfiguracją wnioskowania, zobacz [jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](../articles/machine-learning/how-to-deploy-custom-docker-image.md).


Poniższy przykład ilustruje ładowanie środowiska z obszaru roboczego, a następnie używanie go z konfiguracją wnioskowania:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=myenv)
```

Aby uzyskać więcej informacji o środowiskach, zobacz [Tworzenie środowisk i zarządzanie nimi na potrzeby szkolenia i wdrażania](../articles/machine-learning/how-to-use-environments.md).

Aby uzyskać więcej informacji na temat konfiguracji wnioskowania, zobacz dokumentację klasy [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

## <a name="choose-a-compute-target"></a>Wybierz element docelowy obliczeń


[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]



## <a name="define-a-deployment-configuration"></a>Zdefiniuj konfigurację wdrożenia

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


## <a name="deploy-your-model"></a>Wdrażanie modelu

Teraz można przystąpić do wdrażania modelu. W poniższym przykładzie pokazano lokalne wdrożenie. Składnia różni się w zależności od celu obliczeń wybranego w poprzednim kroku.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)i usługi sieci [Web](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).


## <a name="delete-resources"></a>Usuwanie zasobów

Aby usunąć wdrożoną usługę sieci Web, użyj programu `service.delete()` .
Aby usunąć zarejestrowany model, użyj `model.delete()` .

Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą usługi [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) i [model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

