---
title: Uczenie i wdrażanie modelu nauki wzmacniania (wersja zapoznawcza).
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać uczenia Azure Machine Learning (wersja zapoznawcza) w celu uczenia agenta RL w celu odtworzenia Pong.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: peterlu
author: peterclu
ms.date: 05/05/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 6221b36263b55f54faef18d6596f97c5b3798d3d
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541717"
---
# <a name="reinforcement-learning-preview-with-azure-machine-learning"></a>Uczenie wzmacniające (wersja zapoznawcza) dzięki Azure Machine Learning



> [!NOTE]
> Azure Machine Learning uczenie wzmacniania jest obecnie funkcją w wersji zapoznawczej. W tej chwili obsługiwane są tylko struktury Ray i RLlib.

W tym artykule dowiesz się, jak szkolić agenta uczenia wzmacniania (RL) w celu odtworzenia Pong gry wideo. W celu zarządzania złożonością dystrybuowanych zadań RL będzie używana biblioteka języka Python [RLlib](https://ray.readthedocs.io/en/master/rllib.html) typu open source z usługą Azure Machine Learning.

W tym artykule dowiesz się, jak:
> [!div class="checklist"]
> * Konfigurowanie eksperymentu
> * Definiowanie węzłów głównych i procesów roboczych
> * Tworzenie RL szacowania
> * Przesyłanie eksperymentu w celu uruchomienia przebiegu
> * Wyświetlanie wyników

Ten artykuł jest oparty na [przykładu RLlib Pong](https://aka.ms/azureml-rl-pong) , który znajduje się w [repozytorium GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md)notesu Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w dowolnym z następujących środowisk. Zalecamy wypróbowanie Azure Machine Learning wystąpienia obliczeniowego w celu uzyskania najszybszego uruchomienia. Przykładowe notesy wzmacniania są dostępne do szybkiego klonowania i uruchamiania na Azure Machine Learning wystąpienia obliczeniowe.

 - Wystąpienie obliczeniowe usługi Azure Machine Learning

     - Dowiedz się, jak sklonować przykładowe notesy w [samouczku: Instalacja środowiska i obszaru roboczego](tutorial-1st-experiment-sdk-setup.md).
         - Klonowanie folderu " **jak do użycia"** zamiast **samouczków**
     - Uruchom Notes konfiguracji sieci wirtualnej znajdujący się w programie, `/how-to-use-azureml/reinforcement-learning/setup/devenv_setup.ipynb` Aby otworzyć porty sieciowe używane do uczenia rozproszonego wzmacniania.
     - Uruchamianie przykładowego notesu `/how-to-use-azureml/reinforcement-learning/atari-on-distributed-compute/pong_rllib.ipynb`
 
 - Własny serwer Jupyter Notebook

    - Zainstaluj [zestaw SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).
    - Zainstaluj [zestaw SDK Azure Machine Learning RL](https://docs.microsoft.com/python/api/azureml-contrib-reinforcementlearning/?view=azure-ml-py&preserve-view=true): `pip install --upgrade azureml-contrib-reinforcementlearning`
    - Utwórz [plik konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).
    - Uruchom [Notes konfiguracji](https://aka.ms/azure-rl-env-setup) sieci wirtualnej, aby otworzyć porty sieciowe używane do uczenia rozproszonego wzmacniania.


## <a name="how-to-train-a-pong-playing-agent"></a>Jak szkolić agenta pong

Uczenie wzmacniające (RL) to podejście do uczenia maszynowego, które uczy się. Chociaż inne techniki uczenia maszynowego uczyją się, jak pasywnie przejmować dane wejściowe i znajdować w nim wzorce, RL korzysta z **agentów szkoleniowych** , aby aktywnie podejmować decyzje i uczyć się od ich rezultatów.

Agenci szkoleń uczyją się grać Pong w **symulowanym środowisku**. Agenci szkoleń podejmują decyzję dotyczącą każdej ramki gry, aby przenieść paddle w górę, w dół lub w miejscu. Sprawdza stan gry (obraz RGB ekranu), aby podjąć decyzję.

RL korzysta z **nagradzania** , aby poinformować agenta o pomyślnym zakończeniu decyzji. W tym środowisku Agent uzyskuje dodatnie wynagrodzenie, gdy ocenia punkt i negatywną opłatą, gdy punkt jest naliczany względem tego środowiska. W przypadku wielu iteracji Agent szkoleniowy uczy się wybrać akcję na podstawie jej bieżącego stanu, która jest optymalizowana pod kątem sumy przewidywanych przyszłych korzyści.

Typowym sposobem jest użycie modelu **sieci głębokiej neuronowych Network** (DNN) w celu przeprowadzenia tej optymalizacji w RL. Początkowo Agent uczenia będzie działać źle, ale każda gra będzie generować dodatkowe przykłady w celu dalszej poprawy modelu.

Szkolenie kończy się, gdy Agent osiągnie średnią ocenę nagrody wynoszącą 18 w epoki szkoleniowej. Oznacza to, że Agent korzystniejsze niż swój przeciwnik przez średnią z co najmniej 18 punktów w zakresie dopasowań do 21.

Proces iterowania przez symulację i przeszkolenie DNN jest w sposób obliczeniowy kosztowny i wymaga dużej ilości danych. Jednym ze sposobów poprawy wydajności zadań RL jest to, że **przekształcają działa** tak, aby wielu agentów szkoleniowych mógł działać i uczyć się jednocześnie. Zarządzanie rozproszonym środowiskiem RL może jednak być złożonym przedsięwzięciem.

Azure Machine Learning udostępnia strukturę do zarządzania tymi złożonością w celu skalowania obciążeń RL.

## <a name="set-up-the-environment"></a>Konfigurowanie środowiska

Skonfiguruj lokalne środowisko RL przez załadowanie wymaganych pakietów języka Python, zainicjowanie obszaru roboczego, utworzenie eksperymentu i określenie skonfigurowanej sieci wirtualnej.

### <a name="import-libraries"></a>Importowanie bibliotek

Zaimportuj niezbędne pakiety języka Python, aby uruchomić resztę tego przykładu.

```python
# Azure ML Core imports
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
from azureml.core.runconfig import EnvironmentDefinition
from azureml.widgets import RunDetails
from azureml.tensorboard import Tensorboard

# Azure ML Reinforcement Learning imports
from azureml.contrib.train.rl import ReinforcementLearningEstimator, Ray
from azureml.contrib.train.rl import WorkerConfiguration
```

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

[Obszar roboczy Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla Azure Machine Learning. Zapewnia ono scentralizowane miejsce do pracy ze wszystkimi tworzonymi artefaktami.

Zainicjuj obiekt obszaru roboczego z `config.json` pliku utworzonego w [sekcji wymagania wstępne](#prerequisites). Jeśli wykonujesz ten kod w Azure Machine Learning wystąpienia obliczeniowego, plik konfiguracyjny został już utworzony.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-reinforcement-learning-experiment"></a>Utwórz eksperyment nauki wzmacniania

Utwórz [eksperyment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true) do śledzenia przebiegu nauki wzmacniania. W Azure Machine Learning eksperymenty są logicznymi kolekcjami powiązanych prób w celu organizowania dzienników przebiegów, historii, danych wyjściowych i nie tylko.

```python
experiment_name='rllib-pong-multi-node'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="specify-a-virtual-network"></a>Określ sieć wirtualną

W przypadku zadań RL, które korzystają z wielu obiektów docelowych obliczeń, należy określić sieć wirtualną z otwartymi portami, które umożliwiają węzłom roboczym i węzłom głównym komunikowanie się ze sobą. Sieć wirtualna może znajdować się w dowolnej grupie zasobów, ale powinna znajdować się w tym samym regionie, w którym znajduje się obszar roboczy. Aby uzyskać więcej informacji na temat konfigurowania sieci wirtualnej, zobacz [Notes konfiguracji obszaru roboczego](https://aka.ms/azure-rl-env-setup) , który można znaleźć w sekcji wymagania wstępne. Tutaj należy określić nazwę sieci wirtualnej w grupie zasobów.

```python
vnet = 'your_vnet'
```

## <a name="define-head-and-worker-compute-targets"></a>Zdefiniuj cele obliczeniowe dla elementu głównego i procesu roboczego

Ten przykład używa oddzielnych elementów docelowych obliczeń dla węzłów głowy i procesów roboczych. Te ustawienia umożliwiają skalowanie zasobów obliczeniowych w górę i w dół w zależności od oczekiwanego obciążenia. Ustaw liczbę węzłów i rozmiar każdego węzła w zależności od potrzeb eksperymentu.

### <a name="head-computing-target"></a>Miejsce docelowe obliczeń

W tym przykładzie zastosowano klaster główny wyposażony w procesor GPU w celu zoptymalizowania wydajności uczenia głębokiego. Węzeł główny pociąga za siebie sieć neuronowychą używaną przez agenta w celu podejmowania decyzji. Węzeł główny zbiera również punkty danych z węzłów procesu roboczego w celu dalszej uczenia sieci neuronowych.

Obliczenia główne używają pojedynczej [ `STANDARD_NC6` maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/nc-series) . Ma 6 procesorów wirtualnych, co oznacza, że może dystrybuować pracę na 6 roboczych procesorów CPU.


```python
from azureml.core.compute import AmlCompute, ComputeTarget

# choose a name for the Ray head cluster
head_compute_name = 'head-gpu'
head_compute_min_nodes = 0
head_compute_max_nodes = 2

# This example uses GPU VM. For using CPU VM, set SKU to STANDARD_D2_V2
head_vm_size = 'STANDARD_NC6'

if head_compute_name in ws.compute_targets:
    head_compute_target = ws.compute_targets[head_compute_name]
    if head_compute_target and type(head_compute_target) is AmlCompute:
        print(f'found head compute target. just use it {head_compute_name}')
else:
    print('creating a new head compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = head_vm_size,
                                                                min_nodes = head_compute_min_nodes, 
                                                                max_nodes = head_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    head_compute_target = ComputeTarget.create(ws, head_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    head_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(head_compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="worker-computing-cluster"></a>Klaster obliczeniowy procesu roboczego

W tym przykładzie zastosowano cztery [ `STANDARD_D2_V2` maszyny wirtualne](https://docs.microsoft.com/azure/virtual-machines/nc-series) dla elementu docelowego obliczeń procesu roboczego. Każdy węzeł procesu roboczego ma 2 dostępne procesory CPU w sumie 8 dostępnych procesorów CPU do zrównoleglanie pracy.

Procesory GPU nie są niezbędne dla węzłów procesu roboczego, ponieważ nie wykonują uczenia głębokiego. Pracownicy uruchamiają symulacje gier i zbierają dane.

```python
# choose a name for your Ray worker cluster
worker_compute_name = 'worker-cpu'
worker_compute_min_nodes = 0 
worker_compute_max_nodes = 4

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
worker_vm_size = 'STANDARD_D2_V2'

# Create the compute target if it hasn't been created already
if worker_compute_name in ws.compute_targets:
    worker_compute_target = ws.compute_targets[worker_compute_name]
    if worker_compute_target and type(worker_compute_target) is AmlCompute:
        print(f'found worker compute target. just use it {worker_compute_name}')
else:
    print('creating a new worker compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = worker_vm_size,
                                                                min_nodes = worker_compute_min_nodes, 
                                                                max_nodes = worker_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    worker_compute_target = ComputeTarget.create(ws, worker_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    worker_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(worker_compute_target.get_status().serialize())
```

## <a name="create-a-reinforcement-learning-estimator"></a>Utwórz szacowania uczenia wzmacniania

W tej sekcji dowiesz się, jak za pomocą [ReinforcementLearningEstimator](https://docs.microsoft.com/python/api/azureml-contrib-reinforcementlearning/azureml.contrib.train.rl.reinforcementlearningestimator?view=azure-ml-py&preserve-view=true) przesłać zadanie szkoleniowe do Azure Machine Learning.

Azure Machine Learning używa klas szacowania do hermetyzacji informacji o konfiguracji uruchamiania. Pozwala to łatwo określić sposób konfigurowania wykonywania skryptu. 

### <a name="define-a-worker-configuration"></a>Zdefiniuj konfigurację procesu roboczego

Obiekt WorkerConfiguration informuje Azure Machine Learning jak zainicjować klaster procesów roboczych, który będzie uruchamiał skrypt wprowadzania.

```python
# Pip packages we will use for both head and worker
pip_packages=["ray[rllib]==0.8.3"] # Latest version of Ray has fixes for isses related to object transfers

# Specify the Ray worker configuration
worker_conf = WorkerConfiguration(
    
    # Azure ML compute cluster to run Ray workers
    compute_target=worker_compute_target, 
    
    # Number of worker nodes
    node_count=4,
    
    # GPU
    use_gpu=False, 
    
    # PIP packages to use
    pip_packages=pip_packages
)
```

### <a name="define-script-parameters"></a>Definiuj parametry skryptu

Skrypt wejścia `pong_rllib.py` akceptuje listę parametrów, które definiują sposób wykonywania zadania szkoleniowego. Przekazywanie tych parametrów za pomocą szacowania jako warstwy hermetyzacji ułatwia zmienianie parametrów skryptów i uruchamianie konfiguracji niezależnie od siebie.

Określenie poprawnej wartości `num_workers` spowoduje, że będzie to najbardziej przetwarzanie równoległee wysiłki. Ustaw liczbę procesów roboczych na taką samą jak liczba dostępnych procesorów CPU. Na potrzeby tego przykładu można obliczyć w następujący sposób:

Węzeł główny jest [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) z 6 procesorów wirtualnych vCPU. Klaster roboczy to 4 [Standard_D2_V2 maszyny wirtualne](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs#dv2-series) z 2 procesorami CPU, w sumie 8 procesorów CPU. Należy jednak odjąć 1 procesor od liczby procesów roboczych, ponieważ 1 musi być dedykowany dla roli węzła głównego. 6 procesorów CPU + 8 procesorów CPU = 13 równoczesnych procesów roboczych. Azure Machine Learning używa klastrów głównych i procesów roboczych w celu rozróżnienia zasobów obliczeniowych. Jednak usługa ray nie rozróżnia między kierownikiem a pracownikami, a wszystkie procesory CPU są dostępne dla wykonywania wątku roboczego.


```python
training_algorithm = "IMPALA"
rl_environment = "PongNoFrameskip-v4"

# Training script parameters
script_params = {
    
    # Training algorithm, IMPALA in this case
    "--run": training_algorithm,
    
    # Environment, Pong in this case
    "--env": rl_environment,
    
    # Add additional single quotes at the both ends of string values as we have spaces in the 
    # string parameters, outermost quotes are not passed to scripts as they are not actually part of string
    # Number of GPUs
    # Number of ray workers
    "--config": '\'{"num_gpus": 1, "num_workers": 13}\'',
    
    # Target episode reward mean to stop the training
    # Total training time in seconds
    "--stop": '\'{"episode_reward_mean": 18, "time_total_s": 3600}\'',
}
```

### <a name="define-the-reinforcement-learning-estimator"></a>Definiowanie szacowania uczenia wzmacniania

Użyj listy parametrów i obiektu konfiguracji procesu roboczego, aby utworzyć szacowania.

```python
# RL estimator
rl_estimator = ReinforcementLearningEstimator(
    
    # Location of source files
    source_directory='files',
    
    # Python script file
    entry_script="pong_rllib.py",
    
    # Parameters to pass to the script file
    # Defined above.
    script_params=script_params,
    
    # The Azure ML compute target set up for Ray head nodes
    compute_target=head_compute_target,
    
    # Pip packages
    pip_packages=pip_packages,
    
    # GPU usage
    use_gpu=True,
    
    # RL framework. Currently must be Ray.
    rl_framework=Ray(),
    
    # Ray worker configuration defined above.
    worker_configuration=worker_conf,
    
    # How long to wait for whole cluster to start
    cluster_coordination_timeout_seconds=3600,
    
    # Maximum time for the whole Ray job to run
    # This will cut off the run after an hour
    max_run_duration_seconds=3600,
    
    # Allow the docker container Ray runs in to make full use
    # of the shared memory available from the host OS.
    shm_size=24*1024*1024*1024
)
```

### <a name="entry-script"></a>Skrypt wprowadzania

[Skrypt wpisu](https://aka.ms/azure-rl-pong-script) `pong_rllib.py` pociąga za neuronowych sieć przy użyciu [środowiska OpenAI treningów](https://github.com/openai/gym/) `PongNoFrameSkip-v4` . OpenAI gyms to standardowe interfejsy do testowania algorytmów uczenia wzmacniania w klasycznych grach Atari.

W tym przykładzie używamy algorytmu szkoleniowego znanego jako [Impala](https://arxiv.org/abs/1802.01561) (architektura uczenia aktora). IMPALA parallelizes każdy aktor uczenia się do skalowania w wielu węzłach obliczeniowych bez utraty szybkości ani stabilności.

[Dostrajanie promienia](https://ray.readthedocs.io/en/latest/tune.html) organizuje zadania procesu roboczego Impala.

```python
import ray
import ray.tune as tune
from ray.rllib import train

import os
import sys

from azureml.core import Run
from utils import callbacks

DEFAULT_RAY_ADDRESS = 'localhost:6379'

if __name__ == "__main__":

    # Parse arguments
    train_parser = train.create_parser()

    args = train_parser.parse_args()
    print("Algorithm config:", args.config)

    if args.ray_address is None:
        args.ray_address = DEFAULT_RAY_ADDRESS

    ray.init(address=args.ray_address)

    tune.run(run_or_experiment=args.run,
             config={
                 "env": args.env,
                 "num_gpus": args.config["num_gpus"],
                 "num_workers": args.config["num_workers"],
                 "callbacks": {"on_train_result": callbacks.on_train_result},
                 "sample_batch_size": 50,
                 "train_batch_size": 1000,
                 "num_sgd_iter": 2,
                 "num_data_loader_buffers": 2,
                 "model": {
                    "dim": 42
                 },
             },
             stop=args.stop,
             local_dir='./logs')
```

### <a name="logging-callback-function"></a>Rejestrowanie funkcji wywołania zwrotnego


Skrypt wejścia używa funkcji narzędzia, aby zdefiniować [niestandardową funkcję wywołania zwrotnego RLlib](https://docs.ray.io/en/latest/rllib-training.html#callbacks-and-custom-metrics) do rejestrowania metryk w obszarze roboczym Azure Machine Learning. Dowiedz się, jak wyświetlać te metryki w sekcji [monitorowanie i wyświetlanie wyników](#monitor-and-view-results) .

```python
'''RLlib callbacks module:
    Common callback methods to be passed to RLlib trainer.
'''
from azureml.core import Run

def on_train_result(info):
    '''Callback on train result to record metrics returned by trainer.
    '''
    run = Run.get_context()
    run.log(
        name='episode_reward_mean',
        value=info["result"]["episode_reward_mean"])
    run.log(
        name='episodes_total',
        value=info["result"]["episodes_total"])
```

## <a name="submit-a-run"></a>Prześlij przebieg

[Uruchomienie](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) obsługuje historię uruchamiania zadań wykonywanych w toku lub ukończonych. 

```python
run = exp.submit(config=rl_estimator)
```
> [!NOTE]
> Wykonanie przebiegu może potrwać od 30 do 45 minut.

## <a name="monitor-and-view-results"></a>Monitorowanie i wyświetlanie wyników

Użyj widżetu Azure Machine Learning Jupyter, aby wyświetlić stan przebiegów w czasie rzeczywistym. W tym przykładzie widżet pokazuje dwa uruchomienia podrzędne: jeden dla kierownika i jeden dla pracowników. 

```python
from azureml.widgets import RunDetails

RunDetails(run).show()
run.wait_for_completion()
```

1. Zaczekaj na załadowanie elementu widget.
1. Wybierz pozycję Uruchom na liście uruchomień.

Wybierz **pozycję kliknij tutaj, aby zobaczyć przebieg uruchamiania w programie Azure Machine Learning Studio** , aby uzyskać dodatkowe informacje na temat uruchamiania w programie Studio. Możesz uzyskać dostęp do tych informacji, gdy przebieg jest w toku lub po jego zakończeniu.

![Wykres liniowy pokazujący, jak widżet szczegóły uruchamiania](./media/how-to-use-reinforcement-learning/pong-run-details-widget.png)

Wykres **episode_reward_mean** pokazuje średnią liczbę punktów ocenionych dla każdej epoki szkoleniowej. Można zobaczyć, że Agent szkoleniowy początkowo wykonał słabo, tracąc jego dopasowania bez oceniania pojedynczego punktu (pokazywanego przez reward_mean-21). W ciągu 100 iteracji Agent szkoleniowy mógł nauczyć się przeciwnika komputera przez średnią 18 punktów.

W przypadku przeglądania dzienników przebiegu podrzędnego można zobaczyć wyniki oceny zapisane w pliku driver_log.txt. Może być konieczne odczekanie kilku minut, zanim te metryki staną się dostępne na stronie uruchomienia.

W krótkim obszarze wiesz już, jak skonfigurować wiele zasobów obliczeniowych, aby szkolić agenta uczenia wzmocnić do Pong.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób uczenia agenta nauki wzmacniania przy użyciu agenta uczenia IMPALA. Aby zapoznać się z dodatkowymi przykładami, przejdź do [repozytorium usługi Uczenie w usłudze GitHub Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md).
