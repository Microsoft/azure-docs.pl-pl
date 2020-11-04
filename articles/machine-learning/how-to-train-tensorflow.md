---
title: Uczenie i wdrażanie modelu TensorFlow
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchamiać skrypty szkoleniowe TensorFlow na dużą skalę przy użyciu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: deedfacc4ff9caa7a8d8e4559cb29b8c34c2868a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314472"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>Uczenie modeli TensorFlow na dużą skalę za pomocą Azure Machine Learning

W tym artykule dowiesz się, jak uruchamiać skrypty szkoleniowe [TensorFlow](https://www.tensorflow.org/overview) na dużą skalę przy użyciu Azure Machine Learning.

Ten przykład pociąga za siebie i rejestruje model TensorFlow do klasyfikowania cyfr pisanych ręcznie przy użyciu sieci głębokiej neuronowych (DNN).

Bez względu na to, czy tworzysz model TensorFlow z podstaw, czy przenosisz [istniejący model](how-to-deploy-existing-model.md) do chmury, możesz użyć Azure Machine Learning, aby skalować zadania szkoleniowe typu "open source" w celu kompilowania, wdrażania, obsługi i monitorowania modeli klasy produkcyjnej.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w dowolnym z następujących środowisk:

 - Wystąpienie obliczeniowe Azure Machine Learning — nie jest wymagane pobieranie ani instalacja

     - Ukończ [Samouczek: Zainstaluj środowisko i obszar roboczy](tutorial-1st-experiment-sdk-setup.md) , aby utworzyć dedykowany serwer notesu wstępnie załadowany z zestawem SDK i przykładowym repozytorium.
    - W folderze przykłady głębokiego uczenia na serwerze notesu Znajdź ukończony i rozwinięty Notes, przechodząc do tego katalogu: **How to-use-azure > ml-frameworks > tensorflow > uczenie-parametr-dostrajania-Deploy-with-tensorflow** . 
 
 - Własny serwer Jupyter Notebook

    - [Zainstaluj zestaw SDK Azure Machine Learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.15.0).
    - [Utwórz plik konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).
    - [Pobierz pliki](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow) `tf_mnist.py` przykładowego skryptu lub `utils.py`
     
    Ukończoną [wersję Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) tego przewodnika można również znaleźć na stronie przykładów usługi GitHub. Notes obejmuje rozwinięte sekcje obejmujące dostrajanie inteligentnego parametru, wdrożenie modelu i widżety notesu.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

Ta sekcja umożliwia skonfigurowanie eksperymentu szkoleniowego przez załadowanie wymaganych pakietów języka Python, zainicjowanie obszaru roboczego, utworzenie obiektu docelowego obliczeń i zdefiniowanie środowiska szkoleniowego.

### <a name="import-packages"></a>Importowanie pakietów

Najpierw zaimportuj niezbędne biblioteki języka Python.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

[Obszar roboczy Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Zapewnia ono scentralizowane miejsce do pracy ze wszystkimi tworzonymi artefaktami. W zestawie SDK języka Python można uzyskać dostęp do artefaktów obszaru roboczego przez utworzenie [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) obiektu.

Utwórz obiekt obszaru roboczego z `config.json` pliku utworzonego w [sekcji wymagania wstępne](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>Utwórz plik DataSet

`FileDataset`Obiekt odwołuje się do co najmniej jednego pliku w magazynie danych obszaru roboczego lub publicznych adresów URL. Pliki mogą być w dowolnym formacie, a Klasa oferuje możliwość pobierania lub instalowania plików do obliczeń. Tworząc `FileDataset` , Utwórz odwołanie do lokalizacji źródła danych. Jeśli zastosowano jakiekolwiek przekształcenia do zestawu danych, zostaną one zapisane również w zestawie danych. Dane pozostają w istniejącej lokalizacji, więc nie są naliczane żadne dodatkowe koszty związane z magazynem. Aby uzyskać więcej informacji, [Zobacz przewodnik po tym](./how-to-create-register-datasets.md) `Dataset` pakiecie.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Użyj `register()` metody, aby zarejestrować zestaw danych w obszarze roboczym, aby można było udostępnić go innym osobom, ponownie używać w różnych eksperymentach i określać nazwę w skrypcie szkoleniowym.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>Tworzenie obiektu docelowego obliczeń

Utwórz obiekt docelowy obliczeń dla zadania TensorFlow. W tym przykładzie należy utworzyć klaster obliczeniowy z obsługą procesora GPU Azure Machine Learning.

```Python
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Aby uzyskać więcej informacji na temat obiektów docelowych obliczeń, zobacz artykuł [co to jest Target COMPUTE](concept-compute-target.md) .

### <a name="define-your-environment"></a>Definiowanie środowiska

Aby zdefiniować [środowisko](concept-environments.md) usługi Azure ml, które hermetyzuje zależności skryptu szkoleniowego, można zdefiniować środowisko niestandardowe lub użyć środowiska zanadzorowanego przez usługę Azure ml.

#### <a name="use-a-curated-environment"></a>Korzystanie z nadzorowanego środowiska
Usługa Azure ML udostępnia wstępnie utworzone, nadzorowane środowiska, jeśli nie chcesz definiować własnego środowiska. Platforma Azure ML ma kilka środowisk nadzorowanych procesora CPU i procesorów GPU dla TensorFlow odpowiadających różnym wersjom TensorFlow. Aby uzyskać więcej informacji, zobacz [tutaj](resource-curated-environments.md).

Jeśli chcesz użyć środowiska nadzorowanego, możesz zamiast tego uruchomić następujące polecenie:

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

Aby wyświetlić pakiety zawarte w środowisku nadzorowanym, można napisać zależności Conda do dysku:
```python
tf_env.save_to_directory(path=curated_env_name)
```

Upewnij się, że środowisko nadzorowane zawiera wszystkie zależności wymagane przez skrypt szkoleniowy. W przeciwnym razie trzeba będzie zmodyfikować środowisko, aby uwzględnić brakujące zależności. Należy pamiętać, że jeśli środowisko jest modyfikowane, konieczne będzie podanie nowej nazwy, ponieważ prefiks "Azure" jest zarezerwowany dla środowisk nadzorowanych. W przypadku zmodyfikowania pliku YAML zależności Conda można utworzyć nowe środowisko na podstawie nowej nazwy, np.:
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

Jeśli zamiast tego zmodyfikowano obiekt środowiska nadzorowanego bezpośrednio, można sklonować to środowisko przy użyciu nowej nazwy:
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
```

#### <a name="create-a-custom-environment"></a>Tworzenie środowiska niestandardowego

Możesz również utworzyć własne środowisko usługi Azure ML, które hermetyzuje zależności skryptu szkoleniowego.

Najpierw Zdefiniuj zależności Conda w pliku YAML; w tym przykładzie plik ma nazwę `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
```

Utwórz środowisko usługi Azure ML na podstawie tej specyfikacji środowiska Conda. Środowisko zostanie spakowane w kontenerze platformy Docker w czasie wykonywania.

Domyślnie jeśli nie określono obrazu podstawowego, platforma Azure ML będzie używać obrazu procesora `azureml.core.environment.DEFAULT_CPU_IMAGE` jako obrazu podstawowego. Ponieważ w tym przykładzie działa szkolenie w klastrze GPU, należy określić podstawowy obraz procesora GPU, który ma niezbędne sterowniki procesora GPU i zależności. Usługa Azure ML obsługuje zestaw obrazów podstawowych opublikowanych w witrynie Microsoft Container Registry (MCR), których można użyć, aby uzyskać więcej informacji, zobacz repozytorium [Azure/Azure-Containers](https://github.com/Azure/AzureML-Containers) GitHub.

```python
from azureml.core import Environment

tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Opcjonalnie możesz bezpośrednio przechwycić wszystkie zależności w niestandardowym obrazie Docker lub pliku dockerfile i utworzyć środowisko z tego środowiska. Aby uzyskać więcej informacji, zobacz [uczenie się z obrazem niestandardowym](how-to-train-with-custom-image.md).

Aby uzyskać więcej informacji na temat tworzenia i używania środowisk, zobacz [Tworzenie i używanie środowisk oprogramowania w programie Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Konfigurowanie i przesyłanie przebiegu szkoleniowego

### <a name="create-a-scriptrunconfig"></a>Utwórz ScriptRunConfig

Utwórz obiekt [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) , aby określić szczegóły konfiguracji zadania szkoleniowego, w tym skrypt szkoleniowy, środowisko, które ma być używane, oraz miejsce docelowe obliczeń do uruchomienia. Wszystkie argumenty skryptu szkoleniowego zostaną przekazane za pośrednictwem wiersza polecenia, jeśli zostało to określone w `arguments` parametrze.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 64,
        '--first-layer-neurons', 256,
        '--second-layer-neurons', 128,
        '--learning-rate', 0.01]

src = ScriptRunConfig(source_directory=script_folder,
                      script='tf_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
```

> [!WARNING]
> Azure Machine Learning uruchamia skrypty szkoleniowe, kopiując cały katalog źródłowy. Jeśli masz poufne dane, które nie mają być przekazywane, użyj [pliku. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) lub nie dołączaj go do katalogu źródłowego. Zamiast tego Uzyskuj dostęp do danych za pomocą [zestawu danych](how-to-train-with-datasets.md)usługi Azure ml.

Aby uzyskać więcej informacji na temat konfigurowania zadań przy użyciu usługi ScriptRunConfig, zobacz [Konfigurowanie i przesyłanie przebiegów szkoleniowych](how-to-set-up-training-targets.md).

> [!WARNING]
> Jeśli wcześniej używasz TensorFlow szacowania do konfigurowania zadań szkoleniowych TensorFlow, pamiętaj, że szacowania będzie przestarzałe w przyszłych wydaniach zestawu SDK usługi Azure ML. W przypadku zestawu Azure ML SDK >= 1.15.0, ScriptRunConfig jest zalecanym sposobem konfigurowania zadań szkoleniowych, w tym za pomocą platform DL.

### <a name="submit-a-run"></a>Prześlij przebieg

[Obiekt Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) udostępnia interfejs do historii uruchamiania, gdy zadanie jest uruchomione i po jego zakończeniu.

```Python
run = Experiment(workspace=ws, name='tf-mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>Co się stanie w trakcie wykonywania
Gdy przebieg jest wykonywany, przechodzi przez następujące etapy:

- **Przygotowywanie** : obraz platformy Docker jest tworzony zgodnie ze zdefiniowanym środowiskiem. Obraz zostanie przekazany do rejestru kontenerów obszaru roboczego i zapisany w pamięci podręcznej do późniejszego uruchomienia. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i mogą być przeglądane w celu monitorowania postępu. Jeśli zamiast tego zostanie określone środowisko nadzorowane, zostanie użyty obraz w pamięci podręcznej, w którym będzie można wykonać kopię zapasową środowiska nadzorowanego.

- **Skalowanie** : klaster próbuje skalować w górę, jeśli klaster Batch AI wymaga większej liczby węzłów do uruchomienia przebiegu, niż jest to obecnie dostępne.

- **Uruchomione** : wszystkie skrypty w folderze skryptów są przekazywane do obiektu docelowego obliczeń, magazyny danych są instalowane lub kopiowane i `script` wykonywane. Dane wyjściowe z stdout i folder **./Logs** są przesyłane strumieniowo do historii uruchamiania i mogą być używane do monitorowania przebiegu.

- **Przetwarzanie końcowe** : folder **./Outputs** przebiegu jest kopiowany do historii uruchamiania.

## <a name="register-or-download-a-model"></a>Rejestrowanie lub pobieranie modelu

Po przeszkoleniu modelu możesz zarejestrować go w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie modeli i ich wersji w obszarze roboczym w celu uproszczenia [zarządzania modelami i ich wdrażania](concept-model-management-and-deployment.md). Opcjonalne: przez określenie parametrów `model_framework` , `model_framework_version` , i `resource_configuration` , wdrożenie modelu No-Code jest niedostępne. Dzięki temu można bezpośrednio wdrożyć model jako usługę sieci Web z zarejestrowanego modelu, a `ResourceConfiguration` obiekt definiuje zasób obliczeniowy dla usługi sieci Web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Możesz również pobrać lokalną kopię modelu przy użyciu obiektu Run. W skrypcie szkoleniowym `tf_mnist.py` obiekt wygaszacza TensorFlow zachowuje model do folderu lokalnego (lokalnie dla elementu docelowego obliczeń). Aby pobrać kopię, można użyć obiektu Run.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>Trenowanie rozproszone

Azure Machine Learning obsługuje także wielowęzłowe zadania TensorFlow, dzięki czemu można skalować obciążenia szkoleniowe. Możesz łatwo uruchomić rozproszone zadania TensorFlow, a platforma Azure ML będzie zarządzać aranżacją.

Usługa Uczenie maszynowe Azure obsługuje uruchamianie rozdystrybuowanych zadań TensorFlow z wbudowanym interfejsem API szkolenia rozproszonego Horovod i TensorFlow.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) to "open source", która umożliwia zredukowanie architektury dla szkoleń rozproszonych opracowanych przez Uber. Oferuje łatwą ścieżkę do pisania kodu rozłożonego TensorFlow na potrzeby szkoleń.

Twój kod szkoleniowy musi być instrumentem Horovod na potrzeby szkolenia rozłożonego. Aby uzyskać więcej informacji na temat korzystania z programu Horovod z TensorFlow, zapoznaj się z dokumentacją Horovod:

Aby uzyskać więcej informacji na temat używania Horovod z TensorFlow, zapoznaj się z dokumentacją Horovod:

* [Horovod z TensorFlow](https://github.com/horovod/horovod/blob/master/docs/tensorflow.rst)
* [Horovod z interfejsem API Keras TensorFlow](https://github.com/horovod/horovod/blob/master/docs/keras.rst)

Ponadto upewnij się, że środowisko szkoleniowe obejmuje pakiet **horovod** . Jeśli używasz środowiska nadzorowanego przez TensorFlow, horovod jest już dołączone jako jedna z zależności. Jeśli używasz własnego środowiska, upewnij się, że zależność horovod jest uwzględniona, na przykład:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
  - horovod==0.19.5
```

Aby można było wykonać zadanie rozproszone przy użyciu MPI/Horovod na platformie Azure ML, należy określić [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py) do `distributed_job_config` parametru konstruktora ScriptRunConfig. Poniższy kod spowoduje skonfigurowanie zadania rozproszonego 2-węzłowego, na którym uruchomiono jeden proces na węzeł. Jeśli chcesz również uruchomić wiele procesów na węzeł (tj. Jeśli jednostka SKU klastra ma wiele procesorów GPU), należy dodatkowo określić `process_count_per_node` parametr w MpiConfiguration (wartość domyślna to `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='tf_horovod_word2vec.py',
                      arguments=['--input_data', dataset.as_mount()],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Aby zapoznać się z pełnym samouczkiem dotyczącym uruchamiania rozproszonego TensorFlow z Horovod na platformie Azure ML, zobacz [Distributed TensorFlow with Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/distributed-tensorflow-with-horovod).

### <a name="tfdistribute"></a>TF. Dystrybuuj

Jeśli używasz [natywnego TensorFlow rozproszonego](https://www.tensorflow.org/guide/distributed_training) w kodzie szkoleniowym, np. interfejsie API TensorFlow 2. x `tf.distribute.Strategy` , możesz również uruchomić zadanie rozproszone za pośrednictwem platformy Azure ml. 

W tym celu należy określić [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?preserve-view=true&view=azure-ml-py) do `distributed_job_config` parametru konstruktora ScriptRunConfig. Jeśli używasz programu `tf.distribute.experimental.MultiWorkerMirroredStrategy` , określ wartość `worker_count` w polu TensorflowConfiguration, odpowiadającą liczbie węzłów zadania szkoleniowego.

```python
import os
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import TensorflowConfiguration

distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=0)

model_path = os.path.join("./outputs", "keras-model")

src = ScriptRunConfig(source_directory=source_dir,
                      script='train.py',
                      arguments=["--epochs", 30, "--model-dir", model_path],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=distr_config)
```

W TensorFlow, `TF_CONFIG` zmienna środowiskowa jest wymagana do szkolenia na wielu komputerach. Platforma Azure ML skonfiguruje i ustawi `TF_CONFIG` zmienną odpowiednio dla każdego procesu roboczego przed wykonaniem skryptu szkoleniowego. Możesz uzyskać dostęp `TF_CONFIG` z poziomu skryptu szkoleniowego, jeśli musisz za pośrednictwem programu `os.environ['TF_CONFIG']` .

Przykładowa struktura `TF_CONFIG` zestawu na głównym węźle procesu roboczego:
```JSON
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

Jeśli Twój skrypt szkoleniowy używa strategii serwera parametrów do szkolenia rozproszonego, np. dla starszej wersji TensorFlow 1. x, należy również określić liczbę serwerów parametrów do użycia w zadaniu, np. `distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)` .

## <a name="deploy-a-tensorflow-model"></a>Wdrażanie modelu TensorFlow

Wdrożenie How-to zawiera sekcję dotyczącą rejestrowania modeli, ale możesz przejść bezpośrednio do tworzenia celu [obliczeń](how-to-deploy-and-where.md#choose-a-compute-target) dla wdrożenia, ponieważ istnieje już zarejestrowany model.

### <a name="preview-no-code-model-deployment"></a>Przeglądania Wdrożenie modelu bez kodu

Zamiast tradycyjnej trasy wdrożenia można również użyć funkcji wdrażania bez kodu (wersja zapoznawcza) dla TensorFlow. Rejestrując model, jak pokazano powyżej z `model_framework` `model_framework_version` `resource_configuration` parametrami, i, można po prostu użyć `deploy()` funkcji statycznej do wdrożenia modelu.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Pełne [Omówienie](how-to-deploy-and-where.md) wdrażania w Azure Machine Learning.

## <a name="next-steps"></a>Następne kroki

W tym artykule został przeszkolony i zarejestrowany model TensorFlow oraz zapoznaj się z opcjami wdrażania. Zapoznaj się z innymi artykułami, aby dowiedzieć się więcej na temat Azure Machine Learning.

* [Śledzenie metryk uruchamiania podczas szkolenia](how-to-track-experiments.md)
* [Dostrajanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Architektura referencyjna na potrzeby rozproszonego szkolenia uczenia głębokiego na platformie Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)