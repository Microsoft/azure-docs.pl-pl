---
title: Uczenie modelu przy użyciu niestandardowego obrazu platformy Docker
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak korzystać z własnych obrazów platformy Docker lub z nich nadzorowanych firmy Microsoft, aby uczenie modeli w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 10/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: ea282cb0fcf25f7ac5f17bb9e2f693d189320078
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889165"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>Uczenie modelu przy użyciu niestandardowego obrazu platformy Docker

W tym artykule dowiesz się, jak używać niestandardowego obrazu platformy Docker podczas szkoleń modeli przy użyciu Azure Machine Learning. Przykładowe skrypty w tym artykule służą do klasyfikowania obrazów PET przez utworzenie sieci neuronowych splotowych. 

Azure Machine Learning udostępnia domyślny obraz podstawowy platformy Docker. Możesz również użyć środowiska Azure Machine Learning, aby określić inny obraz podstawowy, taki jak jeden z obsługiwanych [Azure Machine Learning obrazów podstawowych](https://github.com/Azure/AzureML-Containers) lub własny [obraz niestandardowy](how-to-deploy-custom-docker-image.md#create-a-custom-base-image). Niestandardowe obrazy podstawowe umożliwiają ścisłe zarządzanie zależnościami i zachowanie ściślejszej kontroli nad wersjami składników w przypadku uruchamiania zadań szkoleniowych.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom kod w dowolnym z następujących środowisk:

* Wystąpienie obliczeniowe Azure Machine Learning (bez konieczności pobierania lub instalacji):
  * Ukończ samouczek [Konfiguracja środowiska i obszaru roboczego](tutorial-1st-experiment-sdk-setup.md) , aby utworzyć dedykowany serwer notesu wstępnie załadowany z zestawem SDK i przykładowym repozytorium.
  * W repozytorium Azure Machine Learning [przykładów](https://github.com/Azure/azureml-examples)Znajdź ukończony Notes, przechodząc do katalogu **notesy**  >  **fastai**  >  **uczenie — zwierzęta domowe-resnet34. ipynb** . 
* Twój własny serwer Jupyter Notebook:
  * Utwórz [plik konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).
  * Zainstaluj [zestaw SDK Azure Machine Learning](/python/api/overview/azure/ml/install). 
  * Utwórz [Rejestr kontenerów platformy Azure](../container-registry/index.yml) lub inny rejestr platformy Docker, który jest dostępny w Internecie.

## <a name="set-up-a-training-experiment"></a>Konfigurowanie eksperymentu szkoleniowego

W tej sekcji skonfigurujesz eksperyment szkoleniowy przez zainicjowanie obszaru roboczego, zdefiniowanie środowiska i skonfigurowanie obiektu docelowego obliczeń.

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

[Obszar roboczy Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Zapewnia to centralne miejsce do pracy ze wszystkimi tworzonymi artefaktami. W zestawie SDK języka Python można uzyskać dostęp do artefaktów obszaru roboczego przez utworzenie [`Workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) obiektu.

Utwórz `Workspace` obiekt na podstawie config.jsw pliku, który został utworzony jako [warunek wstępny](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>Definiowanie środowiska

Utwórz `Environment` obiekt i Włącz platformę Docker.

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

Określony podstawowy obraz w poniższym kodzie obsługuje bibliotekę fast.ai, która umożliwia dystrybuowanie funkcji uczenia głębokiego. Aby uzyskać więcej informacji, zobacz [repozytorium Fast.AI Docker Hub](https://hub.docker.com/u/fastdotai). 

Jeśli używasz niestandardowego obrazu platformy Docker, być może masz już skonfigurowane środowisko języka Python. W takim przypadku Ustaw `user_managed_dependencies` flagę na, aby `True` użyć wbudowanego środowiska Python obrazu niestandardowego. Domyślnie Azure Machine Learning kompiluje środowisko Conda z określonymi zależnościami. Usługa uruchamia skrypt w tym środowisku zamiast korzystać z bibliotek języka Python zainstalowanych na obrazie podstawowym.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>Korzystanie z prywatnego rejestru kontenerów (opcjonalnie)

Aby użyć obrazu z prywatnego rejestru kontenerów, który nie znajduje się w obszarze roboczym, użyj, `docker.base_image_registry` Aby określić adres repozytorium i nazwę użytkownika i hasło:

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>Użyj niestandardowego pliku dockerfile (opcjonalnie)

Istnieje również możliwość użycia niestandardowej pliku dockerfile. Użyj tej metody, jeśli chcesz zainstalować pakiety inne niż środowisko Python jako zależności. Pamiętaj, aby ustawić podstawowy obraz `None` .

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
> Azure Machine Learning obsługuje tylko obrazy platformy Docker, które udostępniają następujące oprogramowanie:
> * Ubuntu 16,04 lub nowszy.
> * Conda 4.5. # lub nowszej.
> * Python 3.6 +.

Aby uzyskać więcej informacji na temat tworzenia środowisk Azure Machine Learning i zarządzania nimi, zobacz [Tworzenie i używanie środowisk oprogramowania](how-to-use-environments.md). 

### <a name="create-or-attach-a-compute-target"></a>Utwórz lub Dołącz obiekt docelowy obliczeń

Należy utworzyć [obiekt docelowy obliczeń](concept-azure-machine-learning-architecture.md#compute-targets) do uczenia modelu. W tym samouczku utworzysz `AmlCompute` swój zasób obliczeniowy szkoleniowej.

Tworzenie `AmlCompute` trwa kilka minut. Jeśli `AmlCompute` zasób znajduje się już w obszarze roboczym, ten kod pomija proces tworzenia.

Podobnie jak w przypadku innych usług platformy Azure, istnieją ograniczenia dotyczące niektórych zasobów (na przykład `AmlCompute` ) skojarzonych z usługą Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [domyślne limity i sposób żądania wyższego limitu przydziału](how-to-manage-quotas.md).

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

Na potrzeby tego samouczka Użyj skryptu szkoleniowego *Train.py* w witrynie [GitHub](https://github.com/Azure/azureml-examples/blob/main/workflows/train/fastai/pets/src/train.py). W tym celu można wykonać dowolny skrypt niestandardowego szkolenia i uruchomić go, tak jak w przypadku Azure Machine Learning.

Utwórz `ScriptRunConfig` zasób, aby skonfigurować zadanie do uruchamiania w żądanym [miejscu docelowym obliczeń](how-to-set-up-training-targets.md).

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>Prześlij swoje zadanie szkoleniowe

W przypadku przesyłania przebiegu szkoleniowego przy użyciu `ScriptRunConfig` obiektu `submit` Metoda zwraca obiekt typu `ScriptRun` . Zwrócony `ScriptRun` obiekt daje programistyczny dostęp do informacji o przebiegu szkoleniowym. 

```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-fastai').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning uruchamia skrypty szkoleniowe, kopiując cały katalog źródłowy. Jeśli masz poufne dane, które nie mają być przekazywane, użyj [pliku. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) lub nie dodawaj go do katalogu źródłowego. Zamiast tego można uzyskiwać dostęp do danych przy użyciu usługi [datastore](/python/api/azureml-core/azureml.data).

## <a name="next-steps"></a>Następne kroki
W tym artykule przeszkolony model przy użyciu niestandardowego obrazu platformy Docker. Zobacz następujące artykuły, aby dowiedzieć się więcej na temat Azure Machine Learning:
* [Śledź pomiary uruchamiania](how-to-track-experiments.md) podczas szkoleń.
* [Wdróż model](how-to-deploy-custom-docker-image.md) przy użyciu niestandardowego obrazu platformy Docker.
