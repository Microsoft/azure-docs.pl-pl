---
title: Uczenie modeli Keras uczenia głębokiego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak nauczyć i zarejestrować model klasyfikacji sieci Keras głębokiej neuronowych uruchomiony na TensorFlow przy użyciu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: a7d55c6e550000d2dd6c2930d95086ec433c246b
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93361101"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>Uczenie modeli Keras na dużą skalę za pomocą Azure Machine Learning

W tym artykule dowiesz się, jak uruchamiać skrypty szkoleniowe Keras przy użyciu Azure Machine Learning.

Przykładowy kod w tym artykule przedstawia sposób uczenia i rejestrowania Kerasego modelu klasyfikacji utworzonego przy użyciu zaplecza TensorFlow z Azure Machine Learning. Używa popularnego [zestawu danych mnist ręcznie](http://yann.lecun.com/exdb/mnist/) do klasyfikowania cyfr pisanych ręcznie przy użyciu sieci głębokiej neuronowych (DNN) utworzonej przy użyciu [biblioteki języka Python Keras](https://keras.io) działającej w oparciu o [TensorFlow](https://www.tensorflow.org/overview).

Keras to wysoki poziom interfejsu API sieci neuronowych, który może uruchamiać inne popularne platformy DNN, aby uprościć programowanie. Dzięki Azure Machine Learning można szybko skalować zadania szkoleniowe za pomocą elastycznych zasobów obliczeniowych w chmurze. Możesz również śledzić przebiegi szkoleniowe, modele wersji, wdrażać modele i wiele innych.

Bez względu na to, czy tworzysz model Keras z podstaw, czy przenosisz istniejący model do chmury, Azure Machine Learning może pomóc w tworzeniu modeli gotowych do produkcji.

> [!NOTE]
> Jeśli używasz interfejsu API Keras **TF. Keras** wbudowanego w TensorFlow i nie jest to autonomiczny pakiet Keras, zapoznaj się z tematem, aby [przeszkolić modele TensorFlow](how-to-train-tensorflow.md).

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w dowolnym z następujących środowisk:

- Wystąpienie obliczeniowe Azure Machine Learning — nie jest wymagane pobieranie ani instalacja

     - Ukończ [Samouczek: Zainstaluj środowisko i obszar roboczy](tutorial-1st-experiment-sdk-setup.md) , aby utworzyć dedykowany serwer notesu wstępnie załadowany z zestawem SDK i przykładowym repozytorium.
    - W folderze Samples na serwerze notesu Znajdź ukończony i rozwinięty Notes, przechodząc do tego katalogu: **How to-use-azure > ml-frameworks > keras > uczenie-parametr-dostrajania-Deploy-with-keras** folder.

 - Własny serwer Jupyter Notebook

    - [Zainstaluj zestaw SDK Azure Machine Learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.15.0).
    - [Utwórz plik konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).
    - [Pobierz pliki](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras) `keras_mnist.py` przykładowego skryptu lub `utils.py`

    Ukończoną [wersję Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) tego przewodnika można również znaleźć na stronie przykładów usługi GitHub. Notes obejmuje rozwinięte sekcje obejmujące dostrajanie inteligentnego parametru, wdrożenie modelu i widżety notesu.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

Ta sekcja służy do konfigurowania eksperymentu szkoleniowego przez załadowanie wymaganych pakietów języka Python, zainicjowanie obszaru roboczego, utworzenie FileDataset dla danych szkolenia danych wejściowych, utworzenie obiektu docelowego obliczeń i zdefiniowanie środowiska szkoleniowego.

### <a name="import-packages"></a>Importowanie pakietów

Najpierw zaimportuj niezbędne biblioteki języka Python.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Environment
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

Możesz użyć `register()` metody, aby zarejestrować zestaw danych w obszarze roboczym, aby można było udostępnić go innym osobom, ponownie używać w różnych eksperymentach i określać nazwę w skrypcie szkoleniowym.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

### <a name="create-a-compute-target"></a>Tworzenie obiektu docelowego obliczeń

Utwórz obiekt docelowy obliczeń na potrzeby zadania szkoleniowego. W tym przykładzie należy utworzyć klaster obliczeniowy z obsługą procesora GPU Azure Machine Learning.

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

Zdefiniuj [środowisko](concept-environments.md) Azure ml, które hermetyzuje zależności skryptu szkoleniowego.

Najpierw Zdefiniuj zależności Conda w pliku YAML; w tym przykładzie plik ma nazwę `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.0.0
  - keras<=2.3.1
  - matplotlib
```

Utwórz środowisko usługi Azure ML na podstawie tej specyfikacji środowiska Conda. Środowisko zostanie spakowane w kontenerze platformy Docker w czasie wykonywania.

Domyślnie jeśli nie określono obrazu podstawowego, platforma Azure ML będzie używać obrazu procesora `azureml.core.environment.DEFAULT_CPU_IMAGE` jako obrazu podstawowego. Ponieważ w tym przykładzie działa szkolenie w klastrze GPU, należy określić podstawowy obraz procesora GPU, który ma niezbędne sterowniki procesora GPU i zależności. Usługa Azure ML obsługuje zestaw obrazów podstawowych opublikowanych w witrynie Microsoft Container Registry (MCR), których można użyć, aby uzyskać więcej informacji, zobacz repozytorium [Azure/Azure-Containers](https://github.com/Azure/AzureML-Containers) GitHub.

```python
keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

Aby uzyskać więcej informacji na temat tworzenia i używania środowisk, zobacz [Tworzenie i używanie środowisk oprogramowania w programie Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Konfigurowanie i przesyłanie przebiegu szkoleniowego

### <a name="create-a-scriptrunconfig"></a>Utwórz ScriptRunConfig
Najpierw Pobierz dane z magazynu danych obszaru roboczego przy użyciu `Dataset` klasy.

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

Utwórz obiekt ScriptRunConfig, aby określić szczegóły konfiguracji zadania szkoleniowego, w tym skrypt szkoleniowy, środowisko, które ma być używane, oraz miejsce docelowe obliczeń do uruchomienia.

Wszystkie argumenty skryptu szkoleniowego zostaną przekazane za pośrednictwem wiersza polecenia, jeśli zostało to określone w `arguments` parametrze. DatasetConsumptionConfig dla naszego FileDataset jest przenoszona jako argument do skryptu szkoleniowego dla `--data-folder` argumentu. Usługa Azure ML rozwiąże ten DatasetConsumptionConfig z punktem instalacji magazynu danych zapasowych, do którego można uzyskać dostęp za pomocą skryptu szkoleniowego.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 50,
        '--first-layer-neurons', 300,
        '--second-layer-neurons', 100,
        '--learning-rate', 0.001]

src = ScriptRunConfig(source_directory=script_folder,
                      script='keras_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=keras_env)
```

Aby uzyskać więcej informacji na temat konfigurowania zadań przy użyciu usługi ScriptRunConfig, zobacz [Konfigurowanie i przesyłanie przebiegów szkoleniowych](how-to-set-up-training-targets.md).

> [!WARNING]
> Jeśli wcześniej używasz TensorFlow szacowania do konfigurowania zadań szkoleniowych Keras, pamiętaj, że szacowania będzie przestarzałe w przyszłych wydaniach zestawu SDK usługi Azure ML. W przypadku zestawu Azure ML SDK >= 1.15.0, ScriptRunConfig jest zalecanym sposobem konfigurowania zadań szkoleniowych, w tym za pomocą platform DL.

### <a name="submit-your-run"></a>Prześlij swój przebieg

[Obiekt Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) udostępnia interfejs do historii uruchamiania, gdy zadanie jest uruchomione i po jego zakończeniu.

```Python
run = Experiment(workspace=ws, name='keras-mnist').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Co się stanie w trakcie wykonywania
Gdy przebieg jest wykonywany, przechodzi przez następujące etapy:

- **Przygotowywanie** : obraz platformy Docker jest tworzony zgodnie ze zdefiniowanym środowiskiem. Obraz zostanie przekazany do rejestru kontenerów obszaru roboczego i zapisany w pamięci podręcznej do późniejszego uruchomienia. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i mogą być przeglądane w celu monitorowania postępu. Jeśli zamiast tego zostanie określone środowisko nadzorowane, zostanie użyty obraz w pamięci podręcznej, w którym będzie można wykonać kopię zapasową środowiska nadzorowanego.

- **Skalowanie** : klaster próbuje skalować w górę, jeśli klaster Batch AI wymaga większej liczby węzłów do uruchomienia przebiegu, niż jest to obecnie dostępne.

- **Uruchomione** : wszystkie skrypty w folderze skryptów są przekazywane do obiektu docelowego obliczeń, magazyny danych są instalowane lub kopiowane i `script` wykonywane. Dane wyjściowe z stdout i folder **./Logs** są przesyłane strumieniowo do historii uruchamiania i mogą być używane do monitorowania przebiegu.

- **Przetwarzanie końcowe** : folder **./Outputs** przebiegu jest kopiowany do historii uruchamiania.

## <a name="register-the-model"></a>Rejestrowanie modelu

Po przeszkoleniu modelu możesz zarejestrować go w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie modeli i ich wersji w obszarze roboczym w celu uproszczenia [zarządzania modelami i ich wdrażania](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> Wdrożenie How-to zawiera sekcję dotyczącą rejestrowania modeli, ale możesz przejść bezpośrednio do tworzenia celu [obliczeń](how-to-deploy-and-where.md#choose-a-compute-target) dla wdrożenia, ponieważ istnieje już zarejestrowany model.

Możesz również pobrać lokalną kopię modelu. Może to być przydatne do wykonywania dodatkowych czynności związanych z walidacją modelu lokalnie. W skrypcie szkoleniowym `keras_mnist.py` obiekt wygaszacza TensorFlow zachowuje model do folderu lokalnego (lokalnie dla elementu docelowego obliczeń). Za pomocą obiektu Run można pobrać kopię z historii uruchamiania.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Następne kroki

W tym artykule został przeszkolony i zarejestrowany model Keras na Azure Machine Learning. Aby dowiedzieć się, jak wdrożyć model, przejdź do naszego artykułu wdrożenia modelu.

* [Jak i gdzie wdrażać modele](how-to-deploy-and-where.md)
* [Śledzenie metryk uruchamiania podczas szkolenia](how-to-track-experiments.md)
* [Dostrajanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie przeszkolonego modelu](how-to-deploy-and-where.md)
* [Architektura referencyjna na potrzeby rozproszonego szkolenia uczenia głębokiego na platformie Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
