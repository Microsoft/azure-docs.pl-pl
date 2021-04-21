---
title: Trenowanie modelu przy użyciu niestandardowego obrazu platformy Docker
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać własnych obrazów platformy Docker lub obrazów firmy Microsoft w celu trenowania modeli w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 10/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 953d43f93635e25da008515afd9baf9a9e9b7afa
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817075"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>Trenowanie modelu przy użyciu niestandardowego obrazu platformy Docker

W tym artykule dowiesz się, jak używać niestandardowego obrazu platformy Docker podczas trenowania modeli za pomocą Azure Machine Learning. Użyjesz przykładowych skryptów w tym artykule, aby sklasyfikować obrazy zwierząt, tworząc spętliwną sieć neuronową. 

Azure Machine Learning domyślny obraz podstawowy platformy Docker. Można również użyć Azure Machine Learning, aby określić inny obraz podstawowy, taki jak jeden z utrzymywanych obrazów Azure Machine Learning [podstawowych](https://github.com/Azure/AzureML-Containers) lub własny [obraz niestandardowy.](how-to-deploy-custom-docker-image.md#create-a-custom-base-image) Niestandardowe obrazy podstawowe umożliwiają ścisłe zarządzanie zależnościami i ściślejszą kontrolę nad wersjami składników podczas uruchamiania zadań szkoleniowych.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom kod w jednym z tych środowisk:

* Azure Machine Learning wystąpienia obliczeniowego (nie trzeba pobierać ani instalowania):
  * Ukończ [samouczek Konfigurowanie środowiska i obszaru roboczego,](tutorial-1st-experiment-sdk-setup.md) aby utworzyć dedykowany serwer notesu wstępnie załadowany przy użyciu zestawu SDK i przykładowego repozytorium.
  * W repozytorium Azure Machine Learning [przykładów](https://github.com/Azure/azureml-examples)znajdź ukończony notes, przechodząc do katalogu  >  **fastai**  >  **train-pets-resnet34.ipynb** notesów. 
* Twój własny Jupyter Notebook serwera:
  * Utwórz plik [konfiguracji obszaru roboczego.](how-to-configure-environment.md#workspace)
  * Zainstaluj zestaw [SDK Azure Machine Learning .](/python/api/overview/azure/ml/install) 
  * Utwórz rejestr [kontenerów platformy Azure](../container-registry/index.yml) lub inny rejestr platformy Docker dostępny w Internecie.

## <a name="set-up-a-training-experiment"></a>Konfigurowanie eksperymentu trenowania

W tej sekcji skonfigurujesz eksperyment szkoleniowy, inicjując obszar roboczy, definiując środowisko i konfigurując docelowy obiekt obliczeniowy.

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

Obszar [Azure Machine Learning roboczy](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Zapewnia scentralizowane miejsce do pracy ze wszystkimi artefaktami, które tworzysz. W zestawie SDK języka Python możesz uzyskać dostęp do artefaktów obszaru roboczego, tworząc [`Workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) obiekt .

Utwórz obiekt `Workspace` na podstawie config.jspliku, który został utworzony jako [wymaganie wstępne.](#prerequisites)

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>Definiowanie środowiska

Utwórz obiekt `Environment` i włącz usługę Docker.

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

Określony obraz podstawowy w poniższym kodzie obsługuje bibliotekę fast.ai, która umożliwia korzystanie z funkcji rozproszonego uczenia głębokiego. Aby uzyskać więcej informacji, zobacz [fast.ai Docker Hub repozytorium .](https://hub.docker.com/u/fastdotai) 

Jeśli używasz niestandardowego obrazu platformy Docker, być może masz już prawidłowo skonfigurowane środowisko języka Python. W takim przypadku ustaw flagę na , aby użyć wbudowanego środowiska języka `user_managed_dependencies` `True` Python obrazu niestandardowego. Domyślnie program Azure Machine Learning środowisko Conda z określonymi zależnościami. Usługa uruchamia skrypt w tym środowisku, zamiast używać jakichkolwiek bibliotek języka Python zainstalowanych w obrazie bazowym.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>Używanie prywatnego rejestru kontenerów (opcjonalnie)

Aby użyć obrazu z prywatnego rejestru kontenerów, który nie znajduje się w obszarze roboczym, użyj , aby określić adres repozytorium oraz nazwę użytkownika `docker.base_image_registry` i hasło:

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>Używanie niestandardowego pliku Dockerfile (opcjonalnie)

Można również użyć niestandardowego pliku Dockerfile. Użyj tej metody, jeśli musisz zainstalować pakiety inne niż python jako zależności. Pamiętaj, aby ustawić obraz podstawowy na `None` wartość .

```python 
# Specify Docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set the base image to None, because the image is defined by Dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

>[!IMPORTANT]
> Azure Machine Learning obsługuje tylko obrazy platformy Docker, które zapewniają następujące oprogramowanie:
> * Ubuntu 16.04 lub więcej.
> * Conda 4.5.# lub więcej.
> * Python 3.6+.

Aby uzyskać więcej informacji na temat tworzenia środowisk Azure Machine Learning zarządzania nimi, zobacz [Tworzenie środowisk oprogramowania i korzystanie z nich.](how-to-use-environments.md) 

### <a name="create-or-attach-a-compute-target"></a>Tworzenie lub dołączanie docelowego obiektu obliczeniowego

Musisz utworzyć docelowy obiekt [obliczeniowy na](concept-azure-machine-learning-architecture.md#compute-targets) potrzeby trenowania modelu. W tym samouczku utworzysz `AmlCompute` jako zasób obliczeniowy trenowania.

Tworzenie ciągu `AmlCompute` zajmuje kilka minut. Jeśli `AmlCompute` zasób znajduje się już w obszarze roboczym, ten kod pomija proces tworzenia.

Podobnie jak w przypadku innych usług platformy Azure, istnieją limity niektórych zasobów (na przykład `AmlCompute` ) skojarzonych z Azure Machine Learning service. Aby uzyskać więcej informacji, zobacz [Limity domyślne i jak zażądać wyższego limitu przydziału.](how-to-manage-quotas.md)

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your cluster.
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # Create the cluster.
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# Use get_status() to get a detailed status for the current AmlCompute.
print(compute_target.get_status().serialize())
```

## <a name="configure-your-training-job"></a>Konfigurowanie zadania szkoleniowego

W tym samouczku użyj skryptu *szkoleniowego,* który train.py witrynie [GitHub.](https://github.com/Azure/azureml-examples/blob/main/workflows/train/fastai/pets/src/train.py) W praktyce możesz użyć dowolnego niestandardowego skryptu trenowania i uruchomić go w dowolnej Azure Machine Learning.

Utwórz `ScriptRunConfig` zasób, aby skonfigurować zadanie do uruchamiania na żądanym docelowym [zasobie obliczeniowym.](how-to-set-up-training-targets.md)

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>Przesyłanie zadania szkoleniowego

Po przesłaniu uruchomienia trenowania przy użyciu `ScriptRunConfig` obiektu metoda zwraca obiekt typu `submit` `ScriptRun` . Zwrócony `ScriptRun` obiekt zapewnia programowy dostęp do informacji o przebiegu trenowania. 

```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-fastai').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning uruchamia skrypty trenowania, kopiując cały katalog źródłowy. Jeśli masz poufne dane, których nie chcesz przekazywać, użyj pliku [.ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) lub nie dołączaj go do katalogu źródłowego. Zamiast tego należy uzyskać dostęp do danych przy użyciu [magazynu danych](/python/api/azureml-core/azureml.data).

## <a name="next-steps"></a>Następne kroki
W tym artykule wytrenowaliśmy model przy użyciu niestandardowego obrazu platformy Docker. Zapoznaj się z innymi artykułami, aby dowiedzieć się więcej o Azure Machine Learning:
* [Śledzenie metryk przebiegów podczas](how-to-log-view-metrics.md) trenowania.
* [Wdrażanie modelu przy](how-to-deploy-custom-docker-image.md) użyciu niestandardowego obrazu platformy Docker.
