---
title: Szkolenie modeli PyTorch uczenia głębokiego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchamiać skrypty szkoleniowe PyTorch w skali przedsiębiorstwa przy użyciu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 01/14/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5a955424f3fb91a38e9061966ed742922913f1c4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819164"
---
# <a name="train-pytorch-models-at-scale-with-azure-machine-learning"></a>Trenowanie modeli PyTorch na dużą skalę przy użyciu Azure Machine Learning

W tym artykule dowiesz się, jak uruchamiać skrypty szkoleniowe [PyTorch](https://pytorch.org/) w skali przedsiębiorstwa przy użyciu Azure Machine Learning.

Przykładowe skrypty w tym artykule służą do klasyfikowania obrazów węzłom i obrazami w [](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)celu tworzenia sieci neuronowej uczenia głębokiego (DNN) na podstawie samouczka uczenia transferowego firmy PyTorch. Uczenie transferowe to technika, która stosuje wiedzę zdobytą podczas rozwiązywania jednego problemu do innego, ale powiązanego problemu. To skrót procesu trenowania przez wymaganie mniejszej mocy obliczeniowej niż w przypadku trenowania od podstaw. Zobacz artykuł [uczenie głębokie a uczenie maszynowe,](./concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) aby dowiedzieć się więcej na temat uczenia transferowego.

Bez względu na to, czy trenujesz model uczenia głębokiego PyTorch od podstaw, czy też wprowadzasz istniejący model do chmury, możesz użyć usługi Azure Machine Learning do skalowania zadań szkoleniowych typu open source przy użyciu elastycznych zasobów obliczeniowych w chmurze. Modele klasy produkcyjnej można tworzyć, wdrażać, wersjonarować i monitorować za pomocą Azure Machine Learning. 

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w jednym z tych środowisk:

- Azure Machine Learning wystąpienia obliczeniowego — nie trzeba pobierać ani instalowania

    - Ukończ [samouczek: konfigurowanie środowiska i](tutorial-1st-experiment-sdk-setup.md) obszaru roboczego, aby utworzyć dedykowany serwer notesów wstępnie załadowany z zestawem SDK i przykładowym repozytorium.
    - W folderze uczenia głębokiego przykładów na serwerze notesów znajdź ukończony i rozwinięty notes, przechodząc do tego katalogu: **how-to-use-azureml > ml-frameworks > pytorch > train-hyperparameter-tune-deploy-with-pytorch.** 
 
 - Twój własny Jupyter Notebook serwera
    - [Zainstaluj Azure Machine Learning SDK](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Utwórz plik konfiguracji obszaru roboczego.](how-to-configure-environment.md#workspace)
    - [Pobieranie plików przykładowego skryptu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Ukończoną wersję Jupyter Notebook tego przewodnika można [również](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) znaleźć na stronie przykładów usługi GitHub. Notes zawiera rozwinięte sekcje dotyczące inteligentnego dostrajania hiperparametrów, wdrażania modelu i widżetów notesu.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

W tej sekcji konfiguruje się eksperyment trenowania przez załadowanie wymaganych pakietów języka Python, zainicjowanie obszaru roboczego, utworzenie docelowego obiektu obliczeniowego i zdefiniowanie środowiska szkoleniowego.

### <a name="import-packages"></a>Importowanie pakietów

Najpierw zaimportuj niezbędne biblioteki języka Python.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment
from azureml.core import Environment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

Obszar [Azure Machine Learning roboczy](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Zapewnia scentralizowane miejsce do pracy ze wszystkimi artefaktami, które tworzysz. W zestawie SDK języka Python możesz uzyskać dostęp do artefaktów obszaru roboczego, tworząc [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) obiekt .

Utwórz obiekt obszaru roboczego na `config.json` podstawie pliku utworzonego w [sekcji wymagań wstępnych.](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="get-the-data"></a>Pobieranie danych

Zestaw danych składa się z około 120 obrazów treningowych, z których każdy zawiera 100 obrazów weryfikacji dla każdej klasy. Pobierzemy i wyodrębnimy zestaw danych w ramach naszego skryptu trenowania `pytorch_train.py` . Obrazy są podzbiorem [zestawu danych Open Images w wersji 5.](https://storage.googleapis.com/openimages/web/index.html)

### <a name="prepare-training-script"></a>Przygotowywanie skryptu trenowania

W tym samouczku skrypt trenowania, `pytorch_train.py` , jest już podany. W praktyce możesz użyć dowolnego niestandardowego skryptu trenowania w dowolnej Azure Machine Learning.

Utwórz folder dla skryptów trenowania.

```python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)
shutil.copy('pytorch_train.py', project_folder)
```

### <a name="create-a-compute-target"></a>Tworzenie docelowego obiektu obliczeniowego

Utwórz docelowy obiekt obliczeniowy dla zadania PyTorch, które ma być uruchamiane. W tym przykładzie utworzysz klaster obliczeniowy z włączoną Azure Machine Learning GPU.

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

Aby uzyskać więcej informacji na temat docelowych obiektów obliczeniowych, zobacz [artykuł Co to jest docelowy obiekt obliczeniowy.](concept-compute-target.md)

### <a name="define-your-environment"></a>Definiowanie środowiska

Aby zdefiniować środowisko usługi Azure [ML,](concept-environments.md) które hermetyzuje zależności skryptu trenowania, możesz zdefiniować środowisko niestandardowe lub użyć środowiska z możliwością uczenia maszynowego platformy Azure.

#### <a name="use-a-curated-environment"></a>Korzystanie ze środowiska z curated
Jeśli nie chcesz definiować własnego środowiska, usługa Azure ML udostępnia wstępnie utworzone, wytyczone środowiska. Usługa Azure ML ma kilka środowisk procesora CPU i procesora GPU dla platformy PyTorch odpowiadających różnym wersjiom platformy PyTorch. Aby uzyskać więcej informacji, zobacz [tutaj](resource-curated-environments.md).

Jeśli chcesz użyć środowiska z curated, możesz zamiast tego uruchomić następujące polecenie:

```python
curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
```

Aby wyświetlić pakiety zawarte w środowisku curated, możesz zapisać zależności conda na dysku:
```python
pytorch_env.save_to_directory(path=curated_env_name)
```

Upewnij się, że środowisko wsadowe zawiera wszystkie zależności wymagane przez skrypt trenowania. Jeśli nie, musisz zmodyfikować środowisko, aby uwzględnić brakujące zależności. Pamiętaj, że jeśli środowisko zostanie zmodyfikowane, musisz nadać mu nową nazwę, ponieważ prefiks "AzureML" jest zarezerwowany dla środowisk wsadowych. Jeśli zmodyfikowano plik YAML zależności conda, możesz na jego pomocą utworzyć nowe środowisko o nowej nazwie, np.:
```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')
```

Jeśli zamiast tego obiekt środowiska zmodyfikował bezpośrednio, możesz sklonować to środowisko z nową nazwą:
```python
pytorch_env = pytorch_env.clone(new_name='pytorch-1.6-gpu')
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
  - torch==1.6.0
  - torchvision==0.7.0
  - future==0.17.1
  - pillow
```

Utwórz środowisko usługi Azure ML na pomocą tej specyfikacji środowiska conda. Środowisko zostanie spakowane w kontenerze platformy Docker w czasie wykonywania.

Domyślnie, jeśli nie określono obrazu podstawowego, usługa Azure ML użyje obrazu procesora CPU `azureml.core.environment.DEFAULT_CPU_IMAGE` jako obrazu podstawowego. Ponieważ w tym przykładzie trenowanie jest uruchamiane w klastrze procesorów GPU, należy określić obraz podstawowy procesora GPU, który ma niezbędne sterowniki i zależności procesora GPU. Usługa Azure ML utrzymuje zestaw podstawowych obrazów opublikowanych w witrynie Microsoft Container Registry (MCR), których można użyć. Aby uzyskać więcej informacji, zobacz repozytorium GitHub [Azure/AzureML-Containers.](https://github.com/Azure/AzureML-Containers)

```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
pytorch_env.docker.enabled = True
pytorch_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Opcjonalnie możesz po prostu przechwycić wszystkie zależności bezpośrednio w niestandardowym obrazie platformy Docker lub pliku Dockerfile i utworzyć na jego pomocą środowisko. Aby uzyskać więcej informacji, zobacz [Train with custom image (Trenuj przy użyciu obrazu niestandardowego).](how-to-train-with-custom-image.md)

Aby uzyskać więcej informacji na temat tworzenia i używania środowisk, zobacz [Tworzenie i używanie środowisk oprogramowania w programie Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Konfigurowanie i przesyłanie przebiegów trenowania

### <a name="create-a-scriptrunconfig"></a>Tworzenie pliku ScriptRunConfig

Utwórz obiekt [ScriptRunConfig,](/python/api/azureml-core/azureml.core.scriptrunconfig) aby określić szczegóły konfiguracji zadania trenowania, w tym skrypt trenowania, środowisko do użycia i docelowy obiekt obliczeniowy do uruchomienia. Wszelkie argumenty skryptu trenowania zostaną przekazane za pośrednictwem wiersza polecenia, jeśli zostaną określone w `arguments` parametrze . 

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_train.py',
                      arguments=['--num_epochs', 30, '--output_dir', './outputs'],
                      compute_target=compute_target,
                      environment=pytorch_env)
```

> [!WARNING]
> Azure Machine Learning uruchamia skrypty trenowania, kopiując cały katalog źródłowy. Jeśli masz poufne dane, których nie chcesz przekazywać, użyj pliku [.ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) lub nie dołączaj go do katalogu źródłowego . Zamiast tego uzyskaj dostęp do danych przy użyciu zestawu danych usługi Azure [ML.](how-to-train-with-datasets.md)

Aby uzyskać więcej informacji na temat konfigurowania zadań za pomocą polecenia ScriptRunConfig, zobacz Configure and submit training runs (Konfigurowanie [i przesyłanie przebiegów trenowania).](how-to-set-up-training-targets.md)

> [!WARNING]
> Jeśli wcześniej do konfigurowania zadań szkoleniowych PyTorch używano narzędzia do szacowania PyTorch, należy pamiętać, że narzędzia do szacowania zostały wycofane od wersji 1.19.0 zestawu SDK. W przypadku zestawu Azure ML SDK >= 1.15.0 skrypt ScriptRunConfig jest zalecanym sposobem konfigurowania zadań szkoleniowych, w tym tych korzystających ze platform uczenia głębokiego. Typowe pytania dotyczące migracji można znaleźć w przewodniku [po migracji narzędzia do szacowania do pliku ScriptRunConfig.](how-to-migrate-from-estimators-to-scriptrunconfig.md)

## <a name="submit-your-run"></a>Przesyłanie uruchomienia

Obiekt [Run udostępnia](/python/api/azureml-core/azureml.core.run%28class%29) interfejs historii uruchamiania podczas działania zadania i po jego zakończeniu.

```Python
run = Experiment(ws, name='Tutorial-pytorch-birds').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Co się dzieje podczas wykonywania uruchomienia
Przebieg jest wykonywany przez następujące etapy:

- **Przygotowywanie:** obraz platformy Docker jest tworzony zgodnie ze zdefiniowanym środowiskiem. Obraz jest przekazywany do rejestru kontenerów obszaru roboczego i buforowany do późniejszego uruchomień. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i można je wyświetlać w celu monitorowania postępu. Jeśli zamiast tego zostanie określone środowisko z curated, zostanie użyty buforowany obraz zapasowy tego środowiska.

- **Skalowanie:** klaster próbuje skalować w górę, jeśli klaster Batch AI wymaga wykonania uruchomienia większej liczby węzłów niż jest obecnie dostępnych.

- **Uruchamianie:** wszystkie skrypty w folderze script są przekazywane do docelowego obiektu obliczeniowego, magazyny danych są instalowane lub kopiowane i `script` wykonywane. Dane wyjściowe z stdout i **folderu ./logs** są przesyłane strumieniowo do historii uruchamiania i mogą służyć do monitorowania uruchomienia.

- **Przetwarzanie po:** folder **./outputs** uruchomienia jest kopiowany do historii uruchamiania.

## <a name="register-or-download-a-model"></a>Rejestrowanie lub pobieranie modelu

Po wytrenowania modelu możesz zarejestrować go w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie i przechowywanie wersji modeli w obszarze roboczym w celu uproszczenia [zarządzania modelami i ich wdrażania.](concept-model-management-and-deployment.md)

```Python
model = run.register_model(model_name='pytorch-birds', model_path='outputs/model.pt')
```

> [!TIP]
> How-to deployment (Jak to zrobić) zawiera sekcję na temat rejestrowania modeli, ale możesz przejść bezpośrednio do tworzenia docelowego obiektu obliczeniowego dla wdrożenia, ponieważ masz już zarejestrowany model. [](how-to-deploy-and-where.md#choose-a-compute-target)

Możesz również pobrać lokalną kopię modelu przy użyciu obiektu Run. W skrypcie trenowania obiekt zapisu PyTorch utrwala model w folderze lokalnym (lokalnym dla `pytorch_train.py` docelowego obiektu obliczeniowego). Aby pobrać kopię, możesz użyć obiektu Run.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

# Download the model from run history
run.download_file(name='outputs/model.pt', output_file_path='./model/model.pt'), 
```

## <a name="distributed-training"></a>Trenowanie rozproszone

Azure Machine Learning obsługuje również wielowęzłowe rozproszone zadania PyTorch, dzięki czemu można skalować obciążenia szkoleniowe. Możesz łatwo uruchamiać rozproszone zadania PyTorch, a usługa Azure ML będzie zarządzać aranżacją za Ciebie.

Usługa Azure ML obsługuje uruchamianie rozproszonych zadań PyTorch za pomocą wbudowanego modułu Horovod i PyTorch DistributedDataParallel.

### <a name="horovod"></a>Horovod
[Horovod to](https://github.com/uber/horovod) cała struktury redukcji typu open source do trenowania rozproszonego opracowana przez firmę Uber. Oferuje łatwą ścieżkę do pisania rozproszonego kodu PyTorch do trenowania.

Kod trenowania będzie musiał zostać instrumentowany przy użyciu narzędzia Horovod w celu trenowania rozproszonego. Aby uzyskać więcej informacji na temat korzystania z horovod z platformą PyTorch, zobacz [dokumentację horovod.](https://horovod.readthedocs.io/en/stable/pytorch.html)

Ponadto upewnij się, że środowisko szkoleniowe zawiera **pakiet horovod.** Jeśli używasz środowiska curated PyTorch, horovod jest już uwzględniony jako jedna z zależności. Jeśli używasz własnego środowiska, upewnij się, że uwzględniono zależność horovod, na przykład:

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

Aby wykonać zadanie rozproszone przy użyciu formatu MPI/Horovod w usłudze Azure ML, należy określić parametr [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration) dla parametru `distributed_job_config` konstruktora ScriptRunConfig. Poniższy kod skonfiguruje zadanie rozproszone z 2 węzłami, które uruchamia jeden proces na węzeł. Jeśli chcesz również uruchomić wiele procesów na węzeł (tj. jeśli twoja jednostka SKU klastra ma wiele procesorów GPU), dodatkowo określ parametr w parametrze `process_count_per_node` MpiConfiguration (wartość domyślna to `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_horovod_mnist.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Aby uzyskać pełny samouczek dotyczący uruchamiania rozproszonej platformy PyTorch z platformą Horovod w usłudze Azure ML, zobacz [Distributed PyTorch with Horovod (Rozproszona platforma PyTorch z platformą Horovod).](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-horovod)

### <a name="distributeddataparallel"></a>DistributedDataParallel
Jeśli używasz wbudowanego modułu [DistributedDataParallel](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html) platformy PyTorch, który jest zbudowany przy użyciu pakietu **torch.distributed** w kodzie szkoleniowym, możesz również uruchomić zadanie rozproszone za pośrednictwem usługi Azure ML.

Aby uruchomić rozproszone zadanie PyTorch w usłudze Azure ML, masz dwie opcje:
1. Uruchamianie poszczególnych procesów: określ łączną liczbę procesów roboczych, które chcesz uruchomić, a usługa Azure ML obsłuży uruchamianie każdego procesu.
2. Uruchamianie poszczególnych węzłów `torch.distributed.launch` za pomocą polecenia : podaj `torch.distributed.launch` polecenie, które chcesz uruchomić w każdym węźle. Narzędzie uruchamiania torch będzie obsługiwać uruchamianie procesów roboczych w każdym węźle.

Nie ma żadnych podstawowych różnic między tymi opcjami uruchamiania. Zależy to w dużej mierze od preferencji użytkownika lub konwencji platform/bibliotek zbudowanych na podstawie bibliotek vanilla PyTorch (takich jak Lightning lub Hugging Face).

#### <a name="per-process-launch"></a>Uruchamianie procesów
Aby użyć tej opcji do uruchomienia rozproszonego zadania PyTorch, wykonaj następujące czynności:
1. Określanie skryptu trenowania i argumentów
2. Utwórz [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) i określ wartość `process_count` , a także `node_count` . Wartość `process_count` odpowiada łącznej liczbie procesów, które chcesz uruchomić dla zadania. Zwykle powinno to być równe liczbie procesorów GPU na węzeł pomnożonej przez liczbę węzłów. Jeśli `process_count` nie zostanie określony, usługa Azure ML domyślnie uruchamia jeden proces na węzeł.

Usługa Azure ML ustawi następujące zmienne środowiskowe:
* `MASTER_ADDR` - Adres IP maszyny, która będzie hostować proces z rangą 0.
* `MASTER_PORT` — bezpłatny port na maszynie, która będzie hostować proces z rangą 0.
* `NODE_RANK` — ranga węzła do trenowania wielowęzłowego. Możliwe wartości to od 0 do (łączna liczba węzłów — 1).
* `WORLD_SIZE` — łączna liczba procesów. Powinno to być równe całkowitej liczbie urządzeń (GPU) używanych do trenowania rozproszonego.
* `RANK` - Ranga (globalna) bieżącego procesu. Możliwe wartości to od 0 do (rozmiar świata — 1).
* `LOCAL_RANK` - Lokalna (względna) ranga procesu w węźle. Możliwe wartości to od 0 do (liczba procesów w węźle — 1).

Ponieważ wymagane zmienne środowiskowe zostaną ustawione przez usługę Azure [](https://pytorch.org/docs/stable/distributed.html#environment-variable-initialization) ML, możesz użyć domyślnej metody inicjowania zmiennej środowiskowej, aby zainicjować grupę procesów w kodzie szkoleniowym.

Poniższy fragment kodu konfiguruje zadanie PyTorch z 2 węzłami i 2 procesami na węzeł:
```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(process_count=4, node_count=2)

src = ScriptRunConfig(
  source_directory='./src',
  script='train.py',
  arguments=['--epochs', 25],
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(src)
```

> [!WARNING]
> Aby użyć tej opcji do trenowania wielu procesów na węzeł, należy użyć zestawu SDK języka Python usługi Azure ML >= 1.22.0, jak wprowadzono w wersji `process_count` 1.22.0.

> [!TIP]
> Jeśli skrypt trenowania przekazuje informacje takie jak lokalna ranga lub ranga jako argumenty skryptu, możesz odwoływać się do zmiennych środowiskowych w argumentach: `arguments=['--epochs', 50, '--local_rank', $LOCAL_RANK]` .

#### <a name="per-node-launch-with-torchdistributedlaunch"></a>Uruchamianie na węźle za pomocą `torch.distributed.launch`
PyTorch udostępnia narzędzie do uruchamiania w programie [torch.distributed.launch,](https://pytorch.org/docs/stable/distributed.html#launch-utility) które umożliwia użytkownikom uruchamianie wielu procesów w węźle. Moduł `torch.distributed.launch` spowoduje zduplikowanie wielu procesów trenowania w każdym z węzłów.

Poniższe kroki pokazują, jak skonfigurować zadanie PyTorch z uruchamianiem dla węzłów w usłudze Azure ML, co pozwoli osiągnąć odpowiednik uruchomienia następującego polecenia:

```shell
python -m torch.distributed.launch --nproc_per_node <num processes per node> \
  --nnodes <num nodes> --node_rank $NODE_RANK --master_addr $MASTER_ADDR \
  --master_port $MASTER_PORT --use_env \
  <your training script> <your script arguments>
```

1. Podaj `torch.distributed.launch` polecenie dla `command` parametru `ScriptRunConfig` konstruktora. Usługa Azure ML uruchamia to polecenie w każdym węźle klastra trenowania. `--nproc_per_node` powinna być mniejsza niż lub równa liczbie procesorów GPU dostępnych w każdym węźle. `MASTER_ADDR`Wszystkie wartości , i są ustawiane przez usługę Azure ML, więc wystarczy odwołać się do zmiennych `MASTER_PORT` `NODE_RANK` środowiskowych w poleceniu . Usługa Azure ML ustawia wartość 6105, ale jeśli chcesz, możesz przekazać inną wartość `MASTER_PORT` `--master_port` do `torch.distributed.launch` argumentu polecenia. (Narzędzie uruchamiania zresetuje zmienne środowiskowe).
2. Utwórz i `PyTorchConfiguration` określ wartość `node_count` . Nie trzeba ustawiać wartości , ponieważ usługa Azure ML domyślnie uruchamia jeden proces na węzeł, co spowoduje uruchomienie `process_count` określonego polecenia uruchamiania.

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
distr_config = PyTorchConfiguration(node_count=2)
launch_cmd = "python -m torch.distributed.launch --nproc_per_node 2 --nnodes 2 --node_rank $NODE_RANK --master_addr $MASTER_ADDR --master_port $MASTER_PORT --use_env train.py --epochs 50".split()

src = ScriptRunConfig(
  source_directory='./src',
  command=launch_cmd,
  compute_target=compute_target,
  environment=pytorch_env,
  distributed_job_config=distr_config,
)

run = Experiment(ws, 'experiment_name').submit(src)
```

Aby uzyskać pełny samouczek dotyczący uruchamiania rozproszonego programu PyTorch w usłudze Azure ML, zobacz [Distributed PyTorch with DistributedDataParallel (Rozproszona platforma PyTorch z zestawem DistributedDataParallel).](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-distributeddataparallel)

### <a name="troubleshooting"></a>Rozwiązywanie problemów

* **Horovod** został zamknięty: W większości przypadków, jeśli napotkasz błąd "AbortedError: Horovod has shut down" (AbortedError: horovod został zamknięty), wystąpił podstawowy wyjątek w jednym z procesów, który spowodował zamknięcie tej procedury. Każda ranga w zadaniu MPI ma dedykowany plik dziennika w usłudze Azure Machine Learning. Te dzienniki mają nazwę `70_driver_logs`. W przypadku trenowania rozproszonego nazwy dzienników mają sufiks `_rank`, ułatwiający ich odróżnienie. Aby znaleźć dokładny błąd, który spowodował zamknięcie horovod, przejdź przez wszystkie pliki dziennika i poszukaj na końcu driver_log `Traceback` plików. Jeden z tych plików zapewni rzeczywisty wyjątek bazowy. 

## <a name="export-to-onnx"></a>Eksportowanie do ONNX

Aby zoptymalizować wnioskowanie za pomocą [środowiska uruchomieniowego ONNX,](concept-onnx.md)przekonwertuj wytrenowany model PyTorch na format ONNX. Wnioskowanie, czyli ocenianie modelu, to faza, w której wdrożony model jest używany do przewidywania, najczęściej w przypadku danych produkcyjnych. Przykład [można znaleźć](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) w samouczku.

## <a name="next-steps"></a>Następne kroki

W tym artykule wytrenowaliśmy i zarejestrowaliśmy sieć neuronową uczenia głębokiego przy użyciu usługi PyTorch na platformie Azure Machine Learning. Aby dowiedzieć się, jak wdrożyć model, przejdź do naszego artykułu na temat wdrażania modelu.

* [Jak i gdzie wdrażać modele](how-to-deploy-and-where.md)
* [Śledzenie metryk przebiegów podczas trenowania](how-to-log-view-metrics.md)
* [Dostrajanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie przeszkolonego modelu](how-to-deploy-and-where.md)
* [Architektura referencyjna dla trenowania rozproszonego uczenia głębokiego na platformie Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
