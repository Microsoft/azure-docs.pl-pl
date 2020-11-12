---
title: Konfigurowanie przebiegu szkoleniowego
titleSuffix: Azure Machine Learning
description: Uczenie modelu uczenia maszynowego w różnych środowiskach szkoleniowych (cele obliczeniowe). Można łatwo przełączać się między środowiskami szkoleniowymi.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: cb10eb0f89ce37bc484c8570995ebaa098c696f1
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541304"
---
# <a name="configure-and-submit-training-runs"></a>Konfigurowanie i przesyłanie przebiegów trenowania

W tym artykule dowiesz się, jak skonfigurować i przesłać Azure Machine Learning uruchomienia do uczenia modeli.

W przypadku szkolenia często należy zacząć pracę na komputerze lokalnym, a następnie skalować w poziomie do klastra opartego na chmurze. Za pomocą Azure Machine Learning można uruchamiać skrypty na różnych obiektach docelowych obliczeń bez konieczności zmiany skryptu szkoleniowego.

Wystarczy zdefiniować środowisko dla każdego obiektu docelowego obliczeń w ramach **konfiguracji uruchamiania skryptu**.  Następnie, gdy chcesz uruchomić eksperyment szkoleniowy w innym miejscu docelowym obliczeń, określ konfigurację uruchamiania dla tego obliczenia.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj
* [Zestaw Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.13.0)
* [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md),`ws`
* Obiekt docelowy obliczeń, `my_compute_target` .  [Tworzenie obiektu docelowego obliczeń](how-to-create-attach-compute-studio.md) 

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Co to jest konfiguracja uruchomienia skryptu?
[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) służy do konfigurowania informacji niezbędnych do przesłania szkolenia w ramach eksperymentu.

Możesz przesłać eksperyment szkoleniowy z obiektem ScriptRunConfig.  Ten obiekt obejmuje:

* **source_directory** : Katalog źródłowy zawierający skrypt szkoleniowy
* **skrypt** : skrypt szkoleniowy do uruchomienia
* **compute_target** : element docelowy obliczeń do uruchomienia
* **środowisko** : środowisko, które ma być używane podczas uruchamiania skryptu
* i kilka dodatkowych konfigurowalnych opcji (zobacz [dokumentację referencyjną](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) , aby uzyskać więcej informacji)

## <a name="train-your-model"></a><a id="submit"></a>Szkolenie modelu

Wzorzec kodu do przesyłania przebiegu szkoleniowego jest taki sam dla wszystkich typów obiektów docelowych obliczeń:

1. Utwórz eksperyment do uruchomienia
1. Utwórz środowisko, w którym będzie uruchamiany skrypt
1. Utwórz element ScriptRunConfig, który określa miejsce docelowe i środowisko obliczeniowe
1. Prześlij przebieg
1. Poczekaj na zakończenie przebiegu

Możesz też:

* Prześlij przebieg dla [strojenia parametru](how-to-tune-hyperparameters.md).
* Prześlij eksperyment za pośrednictwem [rozszerzenia vs Code](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

## <a name="create-an-experiment"></a>Tworzenie eksperymentu

Utwórz eksperyment w obszarze roboczym.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>Wybierz element docelowy obliczeń

Wybierz miejsce docelowe obliczeń, w którym będzie uruchamiany skrypt szkoleniowy. Jeśli w ScriptRunConfig nie określono elementu docelowego obliczeń, lub jeśli `compute_target='local'` platforma Azure ml wykona skrypt lokalnie. 

W przykładowym kodzie w tym artykule przyjęto założenie, że obiekt docelowy obliczeń został już utworzony `my_compute_target` z sekcji "wymagania wstępne".

## <a name="create-an-environment"></a>Tworzenie środowiska
[Środowiska](concept-environments.md) Azure Machine Learning są hermetyzacją środowiska, w którym odbywa się szkolenie uczenia maszynowego. Określają one pakiety języka Python, obraz platformy Docker, zmienne środowiskowe i ustawienia oprogramowania wokół skryptów szkoleń i oceniania. Określają one również środowiska uruchomieniowe (Python, Spark lub Docker).

Można zdefiniować własne środowisko lub użyć środowiska zanadzorowanego przez usługę Azure ML. [Środowiska nadzorowane](./how-to-use-environments.md#use-a-curated-environment) są wstępnie zdefiniowanymi środowiskami, które są domyślnie dostępne w obszarze roboczym. Te środowiska są obsługiwane przez buforowane obrazy platformy Docker, co zmniejsza koszt przygotowania do uruchomienia. Zapoznaj się z pełną listą dostępnych środowisk nadzorowanych w [Azure Machine Learning środowiskach nadzorowanych](./resource-curated-environments.md) .

W przypadku zdalnego celu obliczeń można użyć jednego z tych popularnych środowisk nadzorowanych, aby zacząć od:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Aby uzyskać więcej informacji i szczegółowe informacje o środowiskach, zobacz [tworzenie & używanie środowisk oprogramowania w programie Azure Machine Learning](how-to-use-environments.md).
  
### <a name="local-compute-target"></a><a name="local"></a>Lokalne miejsce docelowe obliczeń

Jeśli obiektem docelowym obliczeń jest komputer **lokalny** , użytkownik jest odpowiedzialny za zapewnienie, że wszystkie niezbędne pakiety są dostępne w środowisku języka Python, w którym działa skrypt.  Użyj, `python.user_managed_dependencies` Aby użyć bieżącego środowiska Python (lub języka Python w określonej ścieżce).

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>Utwórz konfigurację uruchamiania skryptu

Teraz, gdy masz element docelowy obliczeń ( `my_compute_target` ) i środowisko ( `myenv` ), Utwórz konfigurację uruchamiania skryptu, która uruchamia skrypt szkoleniowy ( `train.py` ) znajdujący się w `project_folder` katalogu:

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='train.py',
                      compute_target=my_compute_target,
                      environment=myenv)

# Set compute target
# Skip this if you are running on your local computer
script_run_config.run_config.target = my_compute_target
```

Jeśli nie określisz środowiska, zostanie utworzone środowisko domyślne.

Jeśli masz argumenty wiersza polecenia, które chcesz przekazać do skryptu szkoleniowego, możesz je określić za pomocą **`arguments`** parametru konstruktora ScriptRunConfig, np. `arguments=['--arg1', arg1_val, '--arg2', arg2_val]` .

Aby zastąpić domyślny maksymalny czas dozwolony dla uruchomienia, można to zrobić za pomocą **`max_run_duration_seconds`** parametru. System podejmie próbę automatycznego anulowania przebiegu, jeśli trwa dłużej niż ta wartość.

### <a name="specify-a-distributed-job-configuration"></a>Określ konfigurację zadania rozproszonego
Jeśli chcesz uruchomić zadanie szkolenia rozproszonego, podaj do parametru konfigurację rozproszoną dla określonego zadania **`distributed_job_config`** . Obsługiwane typy konfiguracji to [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py), [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?preserve-view=true&view=azure-ml-py)i [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py). 

Aby uzyskać więcej informacji i przykłady uruchamiania rozdystrybuowanych zadań Horovod, TensorFlow i PyTorch, zobacz:

* [Szkolenie modeli TensorFlow](./how-to-train-tensorflow.md#distributed-training)
* [Szkolenie modeli PyTorch](./how-to-train-pytorch.md#distributed-training)

## <a name="submit-the-experiment"></a>Przesyłanie eksperymentu

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Po przesłaniu przebiegu szkoleniowego tworzona jest migawka katalogu zawierającego skrypty szkoleniowe i wysyłane do obiektu docelowego obliczeń. Jest on również przechowywany w ramach eksperymentu w obszarze roboczym. Jeśli zmienisz pliki i prześlesz ponownie uruchomienie, zostaną przekazane tylko zmienione pliki.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Aby uzyskać więcej informacji na [temat migawek, zobacz](concept-azure-machine-learning-architecture.md#snapshots)snapshots.

> [!IMPORTANT]
> **Foldery specjalne** Dwa foldery, dane *wyjściowe* i *dzienniki* , otrzymują specjalne traktowanie według Azure Machine Learning. Podczas szkolenia podczas pisania plików do folderów o nazwie dane *wyjściowe* i *dzienniki* , które są względne dla katalogu głównego ( `./outputs` i `./logs` odpowiednio), pliki zostaną automatycznie przekazane do historii uruchamiania, dzięki czemu będziesz mieć do nich dostęp po zakończeniu przebiegu.
>
> Aby tworzyć artefakty podczas szkoleń (takich jak pliki modelu, punkty kontrolne, pliki danych lub rysunki), Zapisz je w `./outputs` folderze.
>
> Podobnie można napisać wszystkie dzienniki z poziomu szkolenia szkoleniowego do `./logs` folderu. Aby korzystać z [integracji TensorBoard](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) Azure Machine Learning upewnij się, że piszesz dzienniki TensorBoard w tym folderze. Gdy przebieg jest w toku, będzie można uruchamiać TensorBoard i przesyłać strumieniowo te dzienniki.  Później będzie można przywrócić dzienniki z dowolnego poprzedniego przebiegu.
>
> Na przykład, aby pobrać plik zapisany *w folderze* Outputs na komputer lokalny po uruchomieniu szkolenia zdalnego: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Śledzenie i integracja usługi git

Po rozpoczęciu szkolenia w przypadku, gdy katalog źródłowy jest lokalnym repozytorium git, informacje o repozytorium są przechowywane w historii uruchamiania. Aby uzyskać więcej informacji, zobacz Integracja z usługą [git dla Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Przykłady notesu

Zobacz te notesy, aby zapoznać się z przykładami konfigurowania przebiegów w różnych scenariuszach szkoleniowych:
* [Szkolenie dotyczące różnych obiektów docelowych obliczeń](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Szkolenia przy użyciu platform ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [Samouczki/IMG-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

* [Samouczek: uczenie modelu](tutorial-train-models-with-aml.md) używa zarządzanego obiektu docelowego obliczeń do uczenia modelu.
* Zobacz, jak szkolić modele przy użyciu określonych platform ML, takich jak [Scikit-](how-to-train-scikit-learn.md)Learning, [TensorFlow](how-to-train-tensorflow.md)i [PyTorch](how-to-train-pytorch.md).
* Dowiedz się, jak [efektywnie dostrajać parametry](how-to-tune-hyperparameters.md) , aby tworzyć lepsze modele.
* Po uzyskaniu przeszkolonego modelu Dowiedz się, [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).
* Wyświetl odwołanie do zestawu SDK [klasy ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) .
* [Używanie Azure Machine Learning z sieciami wirtualnymi platformy Azure](./how-to-network-security-overview.md)