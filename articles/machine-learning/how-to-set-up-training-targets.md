---
title: Prześlij przebieg szkolenia do elementu docelowego obliczeń
titleSuffix: Azure Machine Learning
description: Uczenie modelu uczenia maszynowego w różnych środowiskach szkoleniowych (cele obliczeniowe). Można łatwo przełączać się między środowiskami szkoleniowymi. Zacznij szkolenie lokalnie. Jeśli konieczne jest skalowanie w poziomie, przełącz się do elementu docelowego obliczeń opartego na chmurze.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 8b07d19ca88a2d680a4f9efbb85fcf60b895a2b3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907602"
---
# <a name="submit-a-training-run-to-a-compute-target"></a>Prześlij przebieg szkolenia do elementu docelowego obliczeń

W tym artykule dowiesz się, jak używać różnych środowisk szkoleniowych ([obiektów docelowych obliczeń](concept-compute-target.md)) do uczenia modelu uczenia maszynowego.

Podczas szkolenia często rozpoczyna się na komputerze lokalnym, a następnie uruchamia ten skrypt szkoleniowy na innym miejscu docelowym obliczeń. Za pomocą Azure Machine Learning można uruchamiać skrypty na różnych obiektach docelowych obliczeń bez konieczności zmiany skryptu szkoleniowego.

Wystarczy zdefiniować środowisko dla każdego obiektu docelowego obliczeń w ramach **konfiguracji uruchamiania skryptu**.  Następnie, gdy chcesz uruchomić eksperyment szkoleniowy w innym miejscu docelowym obliczeń, określ konfigurację uruchamiania dla tego obliczenia.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj
* [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
* [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md),`ws`
* Obiekt docelowy obliczeń, `my_compute_target` .  Utwórz obiekt docelowy obliczeń przy użyciu:
  * [Zestaw SDK dla języka Python](how-to-create-attach-compute-sdk.md) 
  * [Studio uczenia maszynowego Azure](how-to-create-attach-compute-studio.md)

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Co to jest konfiguracja uruchomienia skryptu?

Możesz przesłać eksperyment szkoleniowy z obiektem [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) .  Ten obiekt obejmuje:

* **source_directory**: Katalog źródłowy zawierający skrypt szkoleniowy
* **skrypt**: Identyfikowanie skryptu szkoleniowego
* **run_config**: [Konfiguracja uruchamiania](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py&preserve-view=true), która z kolei definiuje, gdzie nastąpi szkolenie. W obszarze `run_config` Określ obiekt docelowy obliczeń i środowisko, które ma być używane podczas uruchamiania skryptu szkoleniowego.  

## <a name="whats-an-environment"></a>Co to jest środowisko?

[Środowiska](concept-environments.md) Azure Machine Learning są hermetyzacją środowiska, w którym odbywa się szkolenie uczenia maszynowego. Określają one pakiety języka Python, zmienne środowiskowe i ustawienia oprogramowania wokół skryptów szkoleń i oceniania. Określają one również czasy wykonywania (Python, Spark lub Docker).  

Środowiska są określone w  `run_config` obiekcie wewnątrz `ScriptRunConfig` .

## <a name="train-your-model"></a><a id="submit"></a>Szkolenie modelu

Wzorzec kodu do przesyłania przebiegu szkoleniowego jest taki sam dla wszystkich typów obiektów docelowych obliczeń:

1. Utwórz eksperyment do uruchomienia
1. Utwórz środowisko, w którym będzie uruchamiany skrypt
1. Utwórz konfigurację uruchamiania skryptu, która odwołuje się do celu i środowiska obliczeniowego
1. Prześlij przebieg
1. Poczekaj na zakończenie przebiegu

Możesz też:

* Prześlij eksperyment z `Estimator` obiektem, jak pokazano w [pouczeniu modeli ml z szacowania](how-to-train-ml-models.md).
* Prześlij przebieg dla [strojenia parametru](how-to-tune-hyperparameters.md).
* Prześlij eksperyment za pośrednictwem [rozszerzenia vs Code](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

## <a name="create-an-experiment"></a>Tworzenie eksperymentu

Utwórz eksperyment w obszarze roboczym.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'

experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="create-an-environment"></a>Tworzenie środowiska

Środowiska nadzorowane zawierają kolekcje pakietów języka Python i są domyślnie dostępne w obszarze roboczym. Te środowiska są obsługiwane przez buforowane obrazy platformy Docker, co zmniejsza koszt przygotowania do uruchomienia. W przypadku zdalnego celu obliczeń można użyć jednego z tych popularnych środowisk nadzorowanych, aby zacząć od:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
my_environment = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Aby uzyskać więcej informacji i szczegółowe informacje o środowiskach, zobacz [tworzenie & używanie środowisk oprogramowania w programie Azure Machine Learning](how-to-use-environments.md).
  
### <a name="local-compute-target"></a>Lokalne miejsce docelowe obliczeń

Jeśli obiektem docelowym obliczeń jest komputer **lokalny**, użytkownik jest odpowiedzialny za zapewnienie, że wszystkie niezbędne pakiety są dostępne w środowisku języka Python, w którym działa skrypt.  Użyj, `python.user_managed_dependencies` Aby użyć bieżącego środowiska Python (lub języka Python w określonej ścieżce).

```python
from azureml.core import Environment

# Editing a run configuration property on-fly.
my_environment = Environment("user-managed-env")

my_environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#my_environment.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-script-run-configuration"></a>Utwórz konfigurację uruchamiania skryptu

Teraz, gdy masz element docelowy obliczeń ( `compute_target` ) i środowisko ( `my_environment` ), Utwórz konfigurację uruchamiania skryptu, która uruchamia skrypt szkoleniowy ( `train.py` ) znajdujący się w `project_folder` katalogu:

```python
from azureml.core import ScriptRunConfig

script_run_config = ScriptRunConfig(source_directory=project_folder, script='train.py')

# Set compute target
script_run_config.run_config.target = my_compute_target

# Set environment.   If you don't do this, a default environment will be created.
script_run_config.run_config.environment = my_environment
```

Możesz również ustawić strukturę dla uruchomienia.

* Dla klastra HDI:
    ```python
    src.run_config.framework = "pyspark"
    ```

* Dla zdalnej maszyny wirtualnej:
    ```python
    src.run_config.framework = "python"
    ```

## <a name="submit-the-experiment"></a>Przesyłanie eksperymentu

```python
run = experiment.submit(config=script_run_config)
```

> [!IMPORTANT]
> Po przesłaniu przebiegu szkoleniowego tworzona jest migawka katalogu zawierającego skrypty szkoleniowe i wysyłane do obiektu docelowego obliczeń. Jest on również przechowywany w ramach eksperymentu w obszarze roboczym. Jeśli zmienisz pliki i prześlesz ponownie uruchomienie, zostaną przekazane tylko zmienione pliki.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Aby uzyskać więcej informacji na [temat migawek, zobacz](concept-azure-machine-learning-architecture.md#snapshots)snapshots.


<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Śledzenie i integracja usługi git

Po rozpoczęciu szkolenia w przypadku, gdy katalog źródłowy jest lokalnym repozytorium git, informacje o repozytorium są przechowywane w historii uruchamiania. Aby uzyskać więcej informacji, zobacz Integracja z usługą [git dla Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Przykłady notesu

Zobacz te notesy, aby poznać przykłady szkoleń z różnymi obiektami docelowymi obliczeń:
* [Jak używać — Azure/szkolenia](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Samouczki/IMG-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

* [Samouczek: uczenie modelu](tutorial-train-models-with-aml.md) używa zarządzanego obiektu docelowego obliczeń do uczenia modelu.
* Dowiedz się, jak [efektywnie dostrajać parametry](how-to-tune-hyperparameters.md) do tworzenia lepszych modeli.? Widok = Azure-ml-PR&Preserve-View = true)
* Po uzyskaniu przeszkolonego modelu Dowiedz się, [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).
* Wyświetl odwołanie do zestawu SDK [klasy RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py&preserve-view=true) .
* [Używanie Azure Machine Learning z sieciami wirtualnymi platformy Azure](how-to-enable-virtual-network.md)