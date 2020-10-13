---
title: Uczenie modelu przy użyciu niestandardowego obrazu platformy Docker
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uczenie modeli przy użyciu niestandardowych obrazów platformy Docker w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 8239d037d6bd68638998cbb36c47c7dac4bce30d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537620"
---
# <a name="train-a-model-using-a-custom-docker-image"></a>Uczenie modelu przy użyciu niestandardowego obrazu platformy Docker

W tym artykule dowiesz się, jak używać niestandardowego obrazu platformy Docker podczas uczenia modeli przy użyciu Azure Machine Learning. 

Przykładowe skrypty w tym artykule służą do klasyfikowania obrazów PET przez utworzenie sieci neuronowych splotowych. 

Mimo że Azure Machine Learning udostępnia domyślny obraz podstawowy platformy Docker, można także użyć środowiska Azure Machine Learning do określenia określonego obrazu podstawowego, takiego jak jeden z zestawu obsługiwanych [obrazów podstawowych platformy Azure ml](https://github.com/Azure/AzureML-Containers) lub własnego [obrazu niestandardowego](how-to-deploy-custom-docker-image.md#create-a-custom-base-image). Niestandardowe obrazy podstawowe umożliwiają dokładne zarządzanie zależnościami i utrzymuje ściślejszą kontrolę nad wersjami składników podczas wykonywania zadań szkoleniowych. 

## <a name="prerequisites"></a>Wymagania wstępne 
Uruchom ten kod w dowolnym z następujących środowisk:
* Wystąpienie obliczeniowe Azure Machine Learning — nie jest wymagane pobieranie ani instalacja
    * Ukończ [Samouczek: Zainstaluj środowisko i obszar roboczy](tutorial-1st-experiment-sdk-setup.md) , aby utworzyć dedykowany serwer notesu wstępnie załadowany z zestawem SDK i przykładowym repozytorium.
    * W repozytorium Azure Machine Learning [przykładów](https://github.com/Azure/azureml-examples)Znajdź ukończony Notes, przechodząc do tego katalogu: How to- **use-Azure > ml-Frameworks > fastai > pociąg-with-Custom-Docker** 

* Własny serwer Jupyter Notebook
    * Utwórz [plik konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).
    * [Zestaw SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true). 
    * [Azure Container Registry](/azure/container-registry) lub innych rejestrów platformy Docker, które są dostępne w Internecie.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu 
Ta sekcja umożliwia skonfigurowanie eksperymentu szkoleniowego przez zainicjowanie obszaru roboczego, utworzenie eksperymentu i przekazanie danych szkoleniowych i skryptów szkoleniowych.

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego
[Obszar roboczy Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Zapewnia ono scentralizowane miejsce do pracy ze wszystkimi tworzonymi artefaktami. W zestawie SDK języka Python można uzyskać dostęp do artefaktów obszaru roboczego przez utworzenie [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) obiektu.

Utwórz obiekt obszaru roboczego z `config.json` pliku utworzonego w [sekcji wymagania wstępne](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Przygotuj skrypty
W tym samouczku **Train.py** skrypt szkoleniowy jest dostępny [tutaj](https://github.com/Azure/azureml-examples/blob/main/code/models/fastai/pets-resnet34/train.py). W tym celu możesz wykonać dowolny niestandardowy skrypt szkoleniowy, tak jak jest, i uruchomić go za pomocą Azure Machine Learning.

### <a name="define-your-environment"></a>Definiowanie środowiska
Utwórz obiekt środowiska i Włącz platformę Docker. 

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

Określony obraz podstawowy poniżej obsługuje bibliotekę fast.ai, która umożliwia obsługę rozproszonych funkcji uczenia głębokiego. Aby uzyskać więcej informacji, zobacz [Fast.AI DockerHub](https://hub.docker.com/u/fastdotai). 

Jeśli używasz niestandardowego obrazu platformy Docker, być może środowisko języka Python zostało już skonfigurowane prawidłowo. W takim przypadku należy ustawić `user_managed_dependencies` flagę na wartość true, aby użyć wbudowanego środowiska Python obrazu niestandardowego. Domyślnie platforma Azure ML utworzy środowisko Conda z określonymi zależnościami i wykona przebieg w tym środowisku, zamiast korzystać z bibliotek języka Python zainstalowanych na podstawowym obrazie.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

Aby użyć obrazu z prywatnego rejestru kontenerów, który nie znajduje się w obszarze roboczym, musisz użyć, `docker.base_image_registry` Aby określić adres repozytorium oraz nazwę użytkownika i hasło:

```python
# Set the container registry information
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

Istnieje również możliwość użycia niestandardowej pliku dockerfile. Użyj tej metody, jeśli chcesz zainstalować pakiety inne niż w języku Python jako zależności i pamiętaj, aby ustawić obraz podstawowy na brak.

```python 
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

Aby uzyskać więcej informacji na temat tworzenia środowisk Azure ML i zarządzania nimi, zobacz [tworzenie & korzystanie ze środowisk oprogramowania](how-to-use-environments.md). 

### <a name="create-or-attach-existing-amlcompute"></a>Utwórz lub Dołącz istniejące AmlCompute
Należy utworzyć [obiekt docelowy obliczeń](concept-azure-machine-learning-architecture.md#compute-targets) do uczenia modelu. W tym samouczku utworzysz AmlCompute jako zasób obliczeniowy szkoleniowej.

Tworzenie AmlCompute trwa około 5 minut. Jeśli AmlCompute o tej nazwie znajduje się już w obszarze roboczym, ten kod pominie proces tworzenia.

Podobnie jak w przypadku innych usług platformy Azure, istnieją ograniczenia dotyczące niektórych zasobów (np. AmlCompute) skojarzonych z usługą Azure Machine Learning. Przeczytaj [ten artykuł](how-to-manage-quotas.md) zgodnie z domyślnymi limitami i zażądaj więcej przydziału. 

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current AmlCompute
print(compute_target.get_status().serialize())
```

### <a name="create-a-scriptrunconfig"></a>Utwórz ScriptRunConfig
Ta ScriptRunConfig skonfiguruje zadanie do wykonania na żądanym [miejscu docelowym obliczeń](how-to-set-up-training-targets.md).

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

### <a name="submit-your-run"></a>Prześlij swój przebieg
Po przesłaniu przebiegu szkoleniowego przy użyciu obiektu ScriptRunConfig Metoda Submit zwraca obiekt typu ScriptRun. Zwrócony obiekt ScriptRun umożliwia programistyczny dostęp do informacji o przebiegu szkoleniowym. 

```python
from azureml.core import Experiment

run = Experiment(ws,'fastai-custom-image').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning uruchamia skrypty szkoleniowe, kopiując cały katalog źródłowy. Jeśli masz poufne dane, które nie mają być przekazywane, użyj [pliku. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) lub nie dołączaj go do katalogu źródłowego. Zamiast tego można uzyskiwać dostęp do danych przy użyciu usługi [datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true).

## <a name="next-steps"></a>Następne kroki
W tym artykule przeszkolony model przy użyciu niestandardowego obrazu platformy Docker. Zapoznaj się z innymi artykułami, aby dowiedzieć się więcej na temat Azure Machine Learning.
* [Śledzenie metryk uruchamiania](how-to-track-experiments.md) podczas szkolenia
* [Wdróż model](how-to-deploy-custom-docker-image.md) przy użyciu niestandardowego obrazu platformy Docker.
