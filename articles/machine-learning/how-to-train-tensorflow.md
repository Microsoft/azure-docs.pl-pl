---
title: Trenowanie i wdrażanie modelu TensorFlow
titleSuffix: Azure Machine Learning
description: Dowiedz się, Azure Machine Learning skalowanie zadania szkoleniowego TensorFlow przy użyciu elastycznych zasobów obliczeniowych w chmurze.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 0eac999fa53679ef67c2a322bd8cc7841197d493
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819146"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>Trenowanie modeli TensorFlow na dużą skalę za pomocą Azure Machine Learning

W tym artykule dowiesz się, jak uruchamiać [skrypty trenowania tensorFlow](https://www.tensorflow.org/overview) na dużą skalę przy użyciu Azure Machine Learning.

Ten przykład szkoli i rejestruje model TensorFlow w celu klasyfikowania cyfr odręcznych przy użyciu głębokiej sieci neuronowej (DNN).

Bez względu na to, czy tworzysz model TensorFlow od podstaw, czy wprowadzasz istniejący [model](how-to-deploy-existing-model.md) do chmury, możesz użyć usługi Azure Machine Learning do skalowania zadań szkoleniowych typu open source w celu tworzenia, wdrażania, wersji i monitorowania modeli klasy produkcyjnej.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w jednym z tych środowisk:

 - Azure Machine Learning wystąpienia obliczeniowego — nie trzeba pobierać ani instalowania

     - Ukończ [samouczek: konfigurowanie środowiska i obszaru roboczego,](tutorial-1st-experiment-sdk-setup.md) aby utworzyć dedykowany serwer notesu wstępnie załadowany z zestawem SDK i przykładowym repozytorium.
    - W folderze uczenia głębokiego przykładów na serwerze notesów znajdź ukończony i rozwinięty notes, przechodząc do tego katalogu: **how-to-use-azureml > ml-frameworks > tensorflow > train-hyperparameter-tune-deploy-with-tensorflow.** 
 
 - Twój własny Jupyter Notebook serwera

    - [Zainstaluj zestaw SDK Azure Machine Learning](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Utwórz plik konfiguracji obszaru roboczego.](how-to-configure-environment.md#workspace)
    - [Pobieranie plików przykładowego skryptu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow) `tf_mnist.py` I `utils.py`
     
    Ukończoną wersję tego przewodnika [Jupyter Notebook można](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) również znaleźć na stronie przykładów usługi GitHub. Notes zawiera rozwinięte sekcje dotyczące inteligentnego dostrajania hiperparametrów, wdrażania modelu i widżetów notesu.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

W tej sekcji konfiguruje się eksperyment trenowania przez załadowanie wymaganych pakietów języka Python, zainicjowanie obszaru roboczego, utworzenie docelowego obiektu obliczeniowego i zdefiniowanie środowiska szkoleniowego.

### <a name="import-packages"></a>Importowanie pakietów

Najpierw zaimportuj niezbędne biblioteki języka Python.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core import Environment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

Obszar [Azure Machine Learning jest](concept-workspace.md) zasobem najwyższego poziomu dla usługi. Zapewnia scentralizowane miejsce do pracy ze wszystkimi artefaktami, które tworzysz. W zestawie SDK języka Python możesz uzyskać dostęp do artefaktów obszaru roboczego, tworząc [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) obiekt .

Utwórz obiekt obszaru roboczego na `config.json` podstawie pliku utworzonego w [sekcji wymagań wstępnych.](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>Tworzenie zestawu danych pliku

Obiekt odwołuje się do jednego lub wielu plików w twoim magazynze danych obszaru `FileDataset` roboczego lub publicznych adresach URL. Pliki mogą być w dowolnym formacie, a klasa zapewnia możliwość pobrania lub instalacji plików w obliczeniach. Tworząc , `FileDataset` tworzysz odwołanie do lokalizacji źródła danych. Jeśli do zestawu danych zastosowano przekształcenia, będą one również przechowywane w zestawie danych. Dane pozostają w istniejącej lokalizacji, więc nie są ponoszone żadne dodatkowe koszty magazynowania. Więcej informacji [można znaleźć](./how-to-create-register-datasets.md) w przewodniku po `Dataset` pakiecie.

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

Użyj metody , aby zarejestrować zestaw danych w obszarze roboczym, aby można było je udostępniać innym osobom, używać ich ponownie w różnych eksperymentach i przydzielić je nazwy w `register()` skrypcie trenowania.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>Tworzenie docelowego obiektu obliczeniowego

Utwórz docelowy obiekt obliczeniowy dla zadania tensorFlow, które ma być uruchamiane. W tym przykładzie utworzysz klaster obliczeniowy z włączoną Azure Machine Learning GPU.

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

Aby uzyskać więcej informacji na temat docelowych obiektów obliczeniowych, zobacz [artykuł What is a compute target (Co to jest docelowy obiekt obliczeniowy).](concept-compute-target.md)

### <a name="define-your-environment"></a>Definiowanie środowiska

Aby zdefiniować środowisko usługi Azure [ML,](concept-environments.md) które hermetyzuje zależności skryptu trenowania, możesz zdefiniować środowisko niestandardowe lub użyć środowiska z możliwością uczenia maszynowego platformy Azure.

#### <a name="use-a-curated-environment"></a>Korzystanie ze środowiska z curated
Jeśli nie chcesz definiować własnego środowiska, usługa Azure ML udostępnia wstępnie utworzone, wytyczone środowiska. Usługa Azure ML ma kilka środowisk procesora CPU i procesora GPU dla platformy TensorFlow odpowiadających różnym wersji platformy TensorFlow. Aby uzyskać więcej informacji, zobacz [tutaj](resource-curated-environments.md).

Jeśli chcesz używać środowiska z curated, możesz zamiast tego uruchomić następujące polecenie:

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

Aby wyświetlić pakiety zawarte w środowisku curated, możesz zapisać zależności conda na dysku:
```python
tf_env.save_to_directory(path=curated_env_name)
```

Upewnij się, że środowisko curated zawiera wszystkie zależności wymagane przez skrypt trenowania. Jeśli nie, musisz zmodyfikować środowisko, aby uwzględnić brakujące zależności. Pamiętaj, że jeśli środowisko zostanie zmodyfikowane, musisz nadać mu nową nazwę, ponieważ prefiks "AzureML" jest zarezerwowany dla środowisk wyedytowanych. Jeśli zmodyfikowano plik YAML zależności conda, możesz na jego pomocą utworzyć nowe środowisko o nowej nazwie, np.:
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

Jeśli zamiast tego zmodyfikowaliśmy bezpośrednio obiekt środowiska, możesz sklonować to środowisko z nową nazwą:
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
```

#### <a name="create-a-custom-environment"></a>Tworzenie środowiska niestandardowego

Możesz również utworzyć własne środowisko usługi Azure ML, które hermetyzuje zależności skryptu trenowania.

Najpierw zdefiniuj zależności conda w pliku YAML. W tym przykładzie plik ma nazwę `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
```

Utwórz środowisko usługi Azure ML na pomocą tej specyfikacji środowiska Conda. Środowisko zostanie spakowane w kontenerze platformy Docker w czasie wykonywania.

Domyślnie, jeśli nie określono obrazu podstawowego, usługa Azure ML użyje obrazu procesora CPU `azureml.core.environment.DEFAULT_CPU_IMAGE` jako obrazu podstawowego. Ponieważ w tym przykładzie trenowanie jest uruchamiane w klastrze procesora GPU, należy określić obraz podstawowy procesora GPU, który ma niezbędne sterowniki i zależności procesora GPU. Usługa Azure ML utrzymuje zestaw podstawowych obrazów opublikowanych w witrynie Microsoft Container Registry (MCR), których można użyć. Aby uzyskać więcej informacji, zobacz repozytorium GitHub [Azure/AzureML-Containers.](https://github.com/Azure/AzureML-Containers)

```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Opcjonalnie możesz po prostu przechwycić wszystkie zależności bezpośrednio w niestandardowym obrazie platformy Docker lub pliku Dockerfile i utworzyć na jego pomocą środowisko. Aby uzyskać więcej informacji, zobacz [Train with custom image (Trenuj przy użyciu obrazu niestandardowego).](how-to-train-with-custom-image.md)

Aby uzyskać więcej informacji na temat tworzenia i używania środowisk, zobacz [Tworzenie i używanie środowisk oprogramowania w programie Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Konfigurowanie i przesyłanie przebiegów trenowania

### <a name="create-a-scriptrunconfig"></a>Tworzenie pliku ScriptRunConfig

Utwórz obiekt [ScriptRunConfig,](/python/api/azureml-core/azureml.core.scriptrunconfig) aby określić szczegóły konfiguracji zadania trenowania, w tym skrypt trenowania, środowisko do użycia i docelowy obiekt obliczeniowy do uruchomienia. Wszelkie argumenty skryptu trenowania zostaną przekazane za pośrednictwem wiersza polecenia, jeśli zostaną określone w `arguments` parametrze .

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
> Azure Machine Learning uruchamia skrypty szkoleniowe, kopiując cały katalog źródłowy. Jeśli masz poufne dane, których nie chcesz przekazywać, użyj pliku [.ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) lub nie dołączaj ich do katalogu źródłowego . Zamiast tego należy uzyskać dostęp do danych przy użyciu zestawu danych usługi Azure [ML.](how-to-train-with-datasets.md)

Aby uzyskać więcej informacji na temat konfigurowania zadań za pomocą polecenia ScriptRunConfig, zobacz Configure and submit training runs (Konfigurowanie [i przesyłanie przebiegów trenowania).](how-to-set-up-training-targets.md)

> [!WARNING]
> Jeśli wcześniej do konfigurowania zadań szkoleniowych tensorFlow używano narzędzia do szacowania TensorFlow, należy pamiętać, że narzędzia do szacowania zostały wycofane od wersji 1.19.0 zestawu SDK. W przypadku zestawu Azure ML SDK >= 1.15.0 skrypt ScriptRunConfig jest zalecanym sposobem konfigurowania zadań szkoleniowych, w tym zadań korzystających ze platform uczenia głębokiego. Typowe pytania dotyczące migracji można znaleźć w [przewodniku po migracji narzędzia do szacowania do pliku ScriptRunConfig.](how-to-migrate-from-estimators-to-scriptrunconfig.md)

### <a name="submit-a-run"></a>Przesyłanie uruchomienia

Obiekt [Run udostępnia](/python/api/azureml-core/azureml.core.run%28class%29) interfejs historii uruchamiania, gdy zadanie jest uruchomione i po jego zakończeniu.

```Python
run = Experiment(workspace=ws, name='Tutorial-TF-Mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>Co się dzieje podczas wykonywania uruchomienia
Przebieg jest wykonywany przez następujące etapy:

- **Przygotowywanie:** obraz platformy Docker jest tworzony zgodnie ze zdefiniowanym środowiskiem. Obraz jest przekazywany do rejestru kontenerów obszaru roboczego i buforowany do późniejszego uruchomień. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i można je wyświetlać w celu monitorowania postępu. Jeśli zamiast tego zostanie określone środowisko z curated, zostanie użyty buforowany obraz zapasowy tego środowiska.

- **Skalowanie:** klaster próbuje skalować w górę, jeśli klaster Batch AI wymaga wykonania uruchomienia większej liczby węzłów niż jest obecnie dostępnych.

- **Uruchamianie:** wszystkie skrypty w folderze script są przekazywane do docelowego obiektu obliczeniowego, magazyny danych są instalowane lub kopiowane i `script` wykonywane. Dane wyjściowe ze strumienia stdout i folderu **./logs** są przesyłane strumieniowo do historii uruchamiania i mogą służyć do monitorowania uruchomienia.

- **Przetwarzanie po** przetworzeniu: folder **./outputs** uruchomienia jest kopiowany do historii uruchamiania.

## <a name="register-or-download-a-model"></a>Rejestrowanie lub pobieranie modelu

Po wytrenowania modelu możesz zarejestrować go w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie i przechowywanie wersji modeli w obszarze roboczym w celu uproszczenia [zarządzania modelami i ich wdrażania.](concept-model-management-and-deployment.md) Opcjonalnie: dzięki określeniu parametrów , i wdrożenie modelu bez `model_framework` `model_framework_version` kodu stanie się `resource_configuration` dostępne. Dzięki temu można bezpośrednio wdrożyć model jako usługę internetową z zarejestrowanego modelu, a obiekt definiuje zasób obliczeniowy `ResourceConfiguration` dla usługi internetowej.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Możesz również pobrać lokalną kopię modelu przy użyciu obiektu Run. W skrypcie trenowania obiekt saver TensorFlow utrwala model w folderze lokalnym `tf_mnist.py` (lokalnym dla docelowego obiektu obliczeniowego). Aby pobrać kopię, możesz użyć obiektu Run.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>Trenowanie rozproszone

Azure Machine Learning obsługuje również wielowęzłowe rozproszone zadania TensorFlow, dzięki czemu można skalować obciążenia szkoleniowe. Możesz łatwo uruchamiać rozproszone zadania TensorFlow, a usługa Azure ML będzie zarządzać aranżacją za Ciebie.

Usługa Azure ML obsługuje uruchamianie rozproszonych zadań TensorFlow przy użyciu wbudowanego rozproszonego interfejsu API trenowania Horovod i TensorFlow.

### <a name="horovod"></a>Horovod
[Horovod to](https://github.com/uber/horovod) wszystkie struktury redukcji typu open source do trenowania rozproszonego opracowane przez usługę Uber. Oferuje łatwą ścieżkę do pisania rozproszonego kodu TensorFlow do trenowania.

Kod trenowania będzie musiał zostać instrumentowany przy użyciu narzędzia Horovod w celu trenowania rozproszonego. Aby uzyskać więcej informacji na temat korzystania z horovod z platformą TensorFlow, zapoznaj się z dokumentacją horovod:

Aby uzyskać więcej informacji na temat korzystania z horovod z platformą TensorFlow, zapoznaj się z dokumentacją horovod:

* [Układ Horovod z przepływem TensorFlow](https://github.com/horovod/horovod/blob/master/docs/tensorflow.rst)
* [Układ Horovod z interfejsem API Keras platformy TensorFlow](https://github.com/horovod/horovod/blob/master/docs/keras.rst)

Ponadto upewnij się, że środowisko szkoleniowe zawiera **pakiet horovod.** Jeśli używasz środowiska wsadowego TensorFlow, horovod jest już uwzględniony jako jedna z zależności. Jeśli używasz własnego środowiska, upewnij się, że uwzględniono zależność horovod, na przykład:

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

Aby wykonać zadanie rozproszone przy użyciu funkcji MPI/Horovod w usłudze Azure ML, należy określić parametr [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration) dla parametru `distributed_job_config` konstruktora ScriptRunConfig. Poniższy kod skonfiguruje 2-węzłowe zadanie rozproszone z uruchomionym jednym procesem na węzeł. Jeśli chcesz również uruchomić wiele procesów na węzeł (tj. jeśli twoja jednostka SKU klastra ma wiele procesorów GPU), dodatkowo określ parametr w konfiguracji `process_count_per_node` MpiConfiguration (wartość domyślna to `1` ).

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

Aby uzyskać pełny samouczek dotyczący uruchamiania rozproszonego przepływu TensorFlow z platformą Horovod w usłudze Azure ML, zobacz [Distributed TensorFlow with Horovod (Rozproszony przepływ TensorFlow z platformą Horovod).](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/distributed-tensorflow-with-horovod)

### <a name="tfdistribute"></a>tf.distribute

Jeśli używasz natywnej rozproszonej platformy [TensorFlow](https://www.tensorflow.org/guide/distributed_training) w kodzie trenowania, np. interfejsu API platformy TensorFlow 2.x, możesz również uruchomić zadanie rozproszone za pośrednictwem usługi `tf.distribute.Strategy` Azure ML. 

W tym celu określ parametr [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration) `distributed_job_config` na parametr konstruktora ScriptRunConfig. Jeśli używasz funkcji , określ wartość w konfiguracji `tf.distribute.experimental.MultiWorkerMirroredStrategy` TensorflowConfiguration odpowiadającą liczbie węzłów `worker_count` dla zadania szkoleniowego.

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

W programie TensorFlow `TF_CONFIG` zmienna środowiskowa jest wymagana do trenowania na wielu maszynach. Usługa Azure ML skonfiguruje i ustawi zmienną odpowiednio dla każdego procesu `TF_CONFIG` roboczego przed wykonaniem skryptu trenowania. Jeśli musisz użyć polecenia , możesz uzyskać dostęp za pomocą skryptu `TF_CONFIG` `os.environ['TF_CONFIG']` trenowania.

Przykładowa struktura `TF_CONFIG` zestawu w węźle głównym procesu roboczego:
```JSON
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

Jeśli skrypt trenowania używa strategii serwera parametrów na potrzeby trenowania rozproszonego, tj. dla starszej wersji rozwiązania TensorFlow 1.x, należy również określić liczbę serwerów parametrów do użycia w zadaniu, np. `distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)` .

## <a name="deploy-a-tensorflow-model"></a>Wdrażanie modelu TensorFlow

How-to wdrożenia zawiera sekcję na temat rejestrowania [](how-to-deploy-and-where.md#choose-a-compute-target) modeli, ale możesz przejść bezpośrednio do tworzenia docelowego obiektu obliczeniowego dla wdrożenia, ponieważ masz już zarejestrowany model.

### <a name="preview-no-code-model-deployment"></a>(Wersja zapoznawcza) Wdrażanie modelu bez kodu

Zamiast tradycyjnej trasy wdrażania można również użyć funkcji wdrażania bez użycia kodu (wersja zapoznawcza) dla rozwiązania TensorFlow. Rejestrując model w sposób pokazany powyżej przy użyciu parametrów , i , możesz po prostu użyć funkcji statycznej do `model_framework` `model_framework_version` wdrożenia `resource_configuration` `deploy()` modelu.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Pełne [how-to covers](how-to-deploy-and-where.md) deployment in Azure Machine Learning in greater depth.

## <a name="next-steps"></a>Następne kroki

W tym artykule wytrenowaliśmy i zarejestrowaliśmy model TensorFlow oraz poznaliśmy opcje wdrażania. Zobacz inne artykuły, aby dowiedzieć się więcej na temat Azure Machine Learning.

* [Śledzenie metryk przebiegów podczas trenowania](how-to-log-view-metrics.md)
* [Dostrajanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Architektura referencyjna dla trenowania rozproszonego uczenia głębokiego na platformie Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
