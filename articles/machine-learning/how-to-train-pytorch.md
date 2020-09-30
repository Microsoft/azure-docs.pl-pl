---
title: Uczenie modeli PyTorch uczenia głębokiego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchamiać skrypty szkoleniowe PyTorch na skalę przedsiębiorstwa przy użyciu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 99f249c9eba0e3d59fd687ac2c3886d037d1ff20
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91532775"
---
# <a name="train-pytorch-models-at-scale-with-azure-machine-learning"></a>Uczenie modeli PyTorch na dużą skalę za pomocą Azure Machine Learning

W tym artykule dowiesz się, jak uruchamiać skrypty szkoleniowe [PyTorch](https://pytorch.org/) na skalę przedsiębiorstwa przy użyciu Azure Machine Learning.

Przykładowe skrypty w tym artykule służą do klasyfikowania obrazów kurczaka i Turcja w celu utworzenia sieci DNN (głębokie uczenie neuronowych Network) na podstawie [samouczka](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)dotyczącego uczenia przenoszonego przez PyTorch. 

Bez względu na to, czy szkolenia mają być szkoleniowe ze względu na uczenie głębokie, czy do chmury, możesz użyć Azure Machine Learning, aby skalować zadania szkoleniowe typu "open source" przy użyciu elastycznych zasobów obliczeniowych w chmurze. Możesz tworzyć, wdrażać, instalować i monitorować modele klasy produkcyjnej za pomocą Azure Machine Learning. 

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w dowolnym z następujących środowisk:

- Wystąpienie obliczeniowe Azure Machine Learning — nie jest wymagane pobieranie ani instalacja

    - Ukończ [Samouczek: Zainstaluj środowisko i obszar roboczy](tutorial-1st-experiment-sdk-setup.md) , aby utworzyć dedykowany serwer notesu wstępnie załadowany z zestawem SDK i przykładowym repozytorium.
    - W folderze przykłady głębokiego uczenia na serwerze notesu Znajdź ukończony i rozwinięty Notes, przechodząc do tego katalogu: **How to-use-azure > ml-frameworks > pytorch > uczenie-parametr-dostrajania-Deploy-with-pytorch** . 
 
 - Własny serwer Jupyter Notebook
    - [Zainstaluj zestaw SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) (>= 1.15.0).
    - [Utwórz plik konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).
    - [Pobierz pliki przykładowego skryptu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Ukończoną [wersję Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) tego przewodnika można również znaleźć na stronie przykładów usługi GitHub. Notes obejmuje rozwinięte sekcje obejmujące dostrajanie inteligentnego parametru, wdrożenie modelu i widżety notesu.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

Ta sekcja umożliwia skonfigurowanie eksperymentu szkoleniowego przez załadowanie wymaganych pakietów języka Python, zainicjowanie obszaru roboczego, utworzenie obiektu docelowego obliczeń i zdefiniowanie środowiska szkoleniowego.

### <a name="import-packages"></a>Importowanie pakietów

Najpierw zaimportuj niezbędne biblioteki języka Python.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

[Obszar roboczy Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Zapewnia ono scentralizowane miejsce do pracy ze wszystkimi tworzonymi artefaktami. W zestawie SDK języka Python można uzyskać dostęp do artefaktów obszaru roboczego przez utworzenie [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) obiektu.

Utwórz obiekt obszaru roboczego z `config.json` pliku utworzonego w [sekcji wymagania wstępne](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="get-the-data"></a>Pobieranie danych

Zestaw danych składa się z około 120 obrazów szkoleniowych przeznaczonych dla indyków i kurcząt, z obrazami walidacji 100 dla każdej klasy. Będziemy pobierać i wyodrębniać zestaw danych w ramach naszego scenariusza szkoleniowego `pytorch_train.py` . Obrazy są podzbiorem [zestawu danych Open images](https://storage.googleapis.com/openimages/web/index.html)w wersji 5.

### <a name="prepare-training-script"></a>Przygotuj skrypt szkoleniowy

W tym samouczku skrypt szkoleniowy `pytorch_train.py` został już podany. W tym celu możesz wykonać dowolny niestandardowy skrypt szkoleniowy, tak jak jest, i uruchomić go za pomocą Azure Machine Learning.

Utwórz folder dla skryptów szkoleniowych.

```python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)
shutil.copy('pytorch_train.py', project_folder)
```

### <a name="create-a-compute-target"></a>Tworzenie obiektu docelowego obliczeń

Utwórz obiekt docelowy obliczeń dla zadania PyTorch. W tym przykładzie należy utworzyć klaster obliczeniowy z obsługą procesora GPU Azure Machine Learning.

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

Aby zdefiniować [środowisko](concept-environments.md) usługi Azure ml, które hermetyzuje zależności skryptu szkoleniowego, można zdefiniować środowisko niestandardowe lub użyć środowiska usługi Azure ml pod opieką.

#### <a name="create-a-custom-environment"></a>Tworzenie środowiska niestandardowego

Zdefiniuj środowisko Azure ML, które hermetyzuje zależności skryptu szkoleniowego.

Najpierw Zdefiniuj zależności Conda w pliku YAML; w tym przykładzie plik ma nazwę `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - future==0.17.1
  - pillow
```

Utwórz środowisko usługi Azure ML na podstawie tej specyfikacji środowiska Conda. Środowisko zostanie spakowane w kontenerze platformy Docker w czasie wykonywania.

Domyślnie jeśli nie określono obrazu podstawowego, platforma Azure ML będzie używać obrazu procesora `azureml.core.runconfig.DEFAULT_CPU_IMAGE` jako obrazu podstawowego. Ponieważ w tym przykładzie działa szkolenie w klastrze GPU, należy określić podstawowy obraz procesora GPU, który ma niezbędne sterowniki procesora GPU i zależności. Usługa Azure ML obsługuje zestaw obrazów podstawowych opublikowanych w witrynie Microsoft Container Registry (MCR), których można użyć, aby uzyskać więcej informacji, zobacz repozytorium [Azure/Azure-Containers](https://github.com/Azure/AzureML-Containers) GitHub.

```python
from azureml.core import Environment

pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
pytorch_env.docker.enabled = True
pytorch_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Opcjonalnie możesz bezpośrednio przechwycić wszystkie zależności w niestandardowym obrazie Docker lub pliku dockerfile i utworzyć środowisko z tego środowiska. Aby uzyskać więcej informacji, zobacz [uczenie się z obrazem niestandardowym](how-to-train-with-custom-image.md).

Aby uzyskać więcej informacji na temat tworzenia i używania środowisk, zobacz [Tworzenie i używanie środowisk oprogramowania w programie Azure Machine Learning](how-to-use-environments.md).

#### <a name="use-a-curated-environment"></a>Korzystanie z nadzorowanego środowiska
Opcjonalnie usługa Azure ML udostępnia wstępnie utworzone, nadzorowane środowiska, jeśli nie chcesz tworzyć własnego obrazu. Platforma Azure ML ma kilka środowisk nadzorowanych procesora CPU i procesorów GPU dla PyTorch odpowiadających różnym wersjom PyTorch. Aby uzyskać więcej informacji, zobacz [tutaj](resource-curated-environments.md).

Jeśli chcesz użyć środowiska nadzorowanego, możesz zamiast tego uruchomić następujące polecenie:

```python
curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
```

Aby wyświetlić pakiety zawarte w środowisku nadzorowanym, można napisać zależności Conda do dysku:
```python
pytorch_env.save_to_directory(path=curated_env_name)
```

Upewnij się, że środowisko nadzorowane zawiera wszystkie zależności wymagane przez skrypt szkoleniowy. W przeciwnym razie trzeba będzie zmodyfikować środowisko, aby uwzględnić brakujące zależności. Należy pamiętać, że jeśli środowisko jest modyfikowane, konieczne będzie podanie nowej nazwy, ponieważ prefiks "Azure" jest zarezerwowany dla środowisk nadzorowanych. W przypadku zmodyfikowania pliku YAML zależności Conda można utworzyć nowe środowisko na podstawie nowej nazwy, np.:
```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')
```

Jeśli zamiast tego zmodyfikowano obiekt środowiska nadzorowanego bezpośrednio, można sklonować to środowisko przy użyciu nowej nazwy:
```python
pytorch_env = pytorch_env.clone(new_name='pytorch-1.6-gpu')
```

## <a name="configure-and-submit-your-training-run"></a>Konfigurowanie i przesyłanie przebiegu szkoleniowego

### <a name="create-a-scriptrunconfig"></a>Utwórz ScriptRunConfig

Utwórz obiekt [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) , aby określić szczegóły konfiguracji zadania szkoleniowego, w tym skrypt szkoleniowy, środowisko, które ma być używane, oraz miejsce docelowe obliczeń do uruchomienia. Wszystkie argumenty skryptu szkoleniowego zostaną przekazane za pośrednictwem wiersza polecenia, jeśli zostało to określone w `arguments` parametrze. 

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_train.py',
                      arguments=['--num_epochs', 30, '--output_dir', './outputs'],
                      compute_target=compute_target,
                      environment=pytorch_env)
```

> [!WARNING]
> Azure Machine Learning uruchamia skrypty szkoleniowe, kopiując cały katalog źródłowy. Jeśli masz poufne dane, które nie mają być przekazywane, użyj [pliku. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) lub nie dołączaj go do katalogu źródłowego. Zamiast tego Uzyskuj dostęp do danych za pomocą [zestawu danych](how-to-train-with-datasets.md)usługi Azure ml.

Aby uzyskać więcej informacji na temat konfigurowania zadań przy użyciu usługi ScriptRunConfig, zobacz [Konfigurowanie i przesyłanie przebiegów szkoleniowych](how-to-set-up-training-targets.md).

> [!WARNING]
> Jeśli wcześniej używasz PyTorch szacowania do konfigurowania zadań szkoleniowych PyTorch, pamiętaj, że szacowania będzie przestarzałe w przyszłych wydaniach zestawu SDK usługi Azure ML. W przypadku zestawu Azure ML SDK >= 1.15.0, ScriptRunConfig jest zalecanym sposobem konfigurowania zadań szkoleniowych, w tym za pomocą platform DL.

## <a name="submit-your-run"></a>Prześlij swój przebieg

[Obiekt Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) udostępnia interfejs do historii uruchamiania, gdy zadanie jest uruchomione i po jego zakończeniu.

```Python
run = Experiment(ws, name='pytorch-birds').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Co się stanie w trakcie wykonywania
Gdy przebieg jest wykonywany, przechodzi przez następujące etapy:

- **Przygotowywanie**: obraz platformy Docker jest tworzony zgodnie ze zdefiniowanym środowiskiem. Obraz zostanie przekazany do rejestru kontenerów obszaru roboczego i zapisany w pamięci podręcznej do późniejszego uruchomienia. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i mogą być przeglądane w celu monitorowania postępu. Jeśli zamiast tego zostanie określone środowisko nadzorowane, zostanie użyty obraz w pamięci podręcznej, w którym będzie można wykonać kopię zapasową środowiska nadzorowanego.

- **Skalowanie**: klaster próbuje skalować w górę, jeśli klaster Batch AI wymaga większej liczby węzłów do uruchomienia przebiegu, niż jest to obecnie dostępne.

- **Uruchomione**: wszystkie skrypty w folderze skryptów są przekazywane do obiektu docelowego obliczeń, magazyny danych są instalowane lub kopiowane i `script` wykonywane. Dane wyjściowe z stdout i folder **./Logs** są przesyłane strumieniowo do historii uruchamiania i mogą być używane do monitorowania przebiegu.

- **Przetwarzanie końcowe**: folder **./Outputs** przebiegu jest kopiowany do historii uruchamiania.

## <a name="register-or-download-a-model"></a>Rejestrowanie lub pobieranie modelu

Po przeszkoleniu modelu możesz zarejestrować go w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie modeli i ich wersji w obszarze roboczym w celu uproszczenia [zarządzania modelami i ich wdrażania](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pytorch-birds', model_path='outputs/model.pt')
```

> [!TIP]
> Wdrożenie How-to zawiera sekcję dotyczącą rejestrowania modeli, ale możesz przejść bezpośrednio do tworzenia celu [obliczeń](how-to-deploy-and-where.md#choose-a-compute-target) dla wdrożenia, ponieważ istnieje już zarejestrowany model.

Możesz również pobrać lokalną kopię modelu przy użyciu obiektu Run. W skrypcie szkoleniowym `pytorch_train.py` obiekt PyTorch Save utrzymuje model do folderu lokalnego (lokalnie dla elementu docelowego obliczeń). Aby pobrać kopię, można użyć obiektu Run.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

# Download the model from run history
run.download_file(name='outputs/model.pt', output_file_path='./model/model.pt'), 
```

## <a name="distributed-training"></a>Trenowanie rozproszone

Azure Machine Learning obsługuje także wielowęzłowe zadania PyTorch, dzięki czemu można skalować obciążenia szkoleniowe. Możesz łatwo uruchomić rozproszone zadania PyTorch, a platforma Azure ML będzie zarządzać aranżacją.

Usługa Azure ML obsługuje uruchamianie rozproszonych zadań PyTorch z wbudowanym modułem DistributedDataParallel Horovod i PyTorch.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) to "open source", która umożliwia zredukowanie architektury dla szkoleń rozproszonych opracowanych przez Uber. Oferuje łatwą ścieżkę do pisania kodu rozłożonego PyTorch na potrzeby szkoleń.

Twój kod szkoleniowy musi być instrumentem Horovod na potrzeby szkolenia rozłożonego. Aby uzyskać więcej informacji na temat używania Horovod z PyTorch, zobacz [dokumentację Horovod](https://horovod.readthedocs.io/en/stable/pytorch.html).

Ponadto upewnij się, że środowisko szkoleniowe obejmuje pakiet **horovod** . Jeśli używasz środowiska nadzorowanego przez PyTorch, horovod jest już dołączone jako jedna z zależności. Jeśli używasz własnego środowiska, upewnij się, że zależność horovod jest uwzględniona, na przykład:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - horovod==0.19.5
```

Aby można było wykonać zadanie rozproszone przy użyciu MPI/Horovod na platformie Azure ML, należy określić [MpiConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py&preserve-view=true) do `distributed_job_config` parametru konstruktora ScriptRunConfig. Poniższy kod spowoduje skonfigurowanie zadania rozproszonego 2-węzłowego, na którym uruchomiono jeden proces na węzeł. Jeśli chcesz również uruchomić wiele procesów na węzeł (tj. Jeśli jednostka SKU klastra ma wiele procesorów GPU), należy dodatkowo określić `process_count_per_node` parametr w MpiConfiguration (wartość domyślna to `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_horovod_mnist.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Aby zapoznać się z pełnym samouczkiem dotyczącym uruchamiania rozproszonego PyTorch z Horovod na platformie Azure ML, zobacz [Distributed PyTorch with Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-horovod).

### <a name="distributeddataparallel"></a>DistributedDataParallel
Jeśli używasz wbudowanego modułu [DistributedDataParallel](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html) PyTorch, który jest skompilowany przy użyciu pakietu **Torch. Distributed** w kodzie szkoleniowym, możesz również uruchomić zadanie rozproszone za pośrednictwem platformy Azure ml.

Aby uruchomić rozproszone zadanie PyTorch z DistributedDataParallel, należy określić [PyTorchConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?view=azure-ml-py&preserve-view=true) do `distributed_job_config` parametru konstruktora ScriptRunConfig. Aby użyć zaplecza NCCL dla Torch. Distributed, określ `communication_backend='Nccl'` wartość w PyTorchConfiguration. Poniższy kod spowoduje skonfigurowanie zadania rozproszonego 2-węzłowego. Zaplecze NCCL jest zalecanym zapleczem do szkolenia rozproszonego procesora GPU PyTorch.

W przypadku zadań rozproszonych PyTorch skonfigurowanych za pośrednictwem PyTorchConfiguration, platforma Azure ML ustawi następujące zmienne środowiskowe w węzłach obiektu docelowego obliczeń:

* `AZ_BATCHAI_PYTORCH_INIT_METHOD`: Adres URL dla inicjalizacji udostępnionego systemu plików dla grupy procesów
* `AZ_BATCHAI_TASK_INDEX`: globalna ranga procesu roboczego

Możesz określić te zmienne środowiskowe do odpowiednich argumentów skryptu szkoleniowego za pośrednictwem `arguments` parametru ScriptRunConfig.

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

args = ['--dist-backend', 'nccl',
        '--dist-url', '$AZ_BATCHAI_PYTORCH_INIT_METHOD',
        '--rank', '$AZ_BATCHAI_TASK_INDEX',
        '--world-size', 2]

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=PyTorchConfiguration(communication_backend='Nccl', node_count=2))
```

Jeśli zamiast tego chcesz użyć zaplecza Gloo do szkolenia rozproszonego, określ `communication_backend='Gloo'` zamiast tego. Zaplecze Gloo jest zalecane na potrzeby rozproszonego szkolenia procesora CPU.

Aby zapoznać się z pełnym samouczkiem dotyczącym uruchamiania rozproszonego PyTorch na platformie Azure ML, zobacz [Distributed PyTorch with DistributedDataParallel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-nccl-gloo).

## <a name="export-to-onnx"></a>Eksportuj do ONNX

Aby zoptymalizować wnioskowanie za pomocą [środowiska uruchomieniowego ONNX](concept-onnx.md), przekonwertuj swój przeszkolony model PyTorch na format ONNX. Wnioskowanie lub ocenianie modelu to faza, w której wdrożony model jest używany do prognozowania, najczęściej dotyczący danych produkcyjnych. Przykład można znaleźć w [samouczku](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) .

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano przeszkolone i zarejestrowano uczenie głębokie, neuronowych sieci przy użyciu PyTorch na Azure Machine Learning. Aby dowiedzieć się, jak wdrożyć model, przejdź do naszego artykułu wdrożenia modelu.

* [Jak i gdzie wdrażać modele](how-to-deploy-and-where.md)
* [Śledzenie metryk uruchamiania podczas szkolenia](how-to-track-experiments.md)
* [Dostrajanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie przeszkolonego modelu](how-to-deploy-and-where.md)
* [Architektura referencyjna na potrzeby rozproszonego szkolenia uczenia głębokiego na platformie Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
