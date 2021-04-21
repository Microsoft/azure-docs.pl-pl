---
title: Szkolenie modeli Keras uczenia głębokiego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak trenować i rejestrować model głębokiej klasyfikacji sieci neuronowej Keras uruchomiony na platformie TensorFlow przy użyciu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 555ec90bbd73cee401f6f35aa04598792d2f24f4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817150"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>Trenowanie modeli Keras na dużą skalę za pomocą Azure Machine Learning

W tym artykule dowiesz się, jak uruchamiać skrypty trenowania keras za pomocą Azure Machine Learning.

Przykładowy kod w tym artykule pokazuje, jak trenować i rejestrować model klasyfikacji Keras zbudowany przy użyciu zaplecza TensorFlow z Azure Machine Learning. Używa ona popularnego [interfejsu zestaw danych MNIST](http://yann.lecun.com/exdb/mnist/) do klasyfikowania ręcznie napisanych cyfr przy użyciu głębokiej sieci neuronowej (DNN), która została zbudowana przy użyciu [biblioteki Keras języka Python](https://keras.io) działającej na [platformie TensorFlow.](https://www.tensorflow.org/overview)

Keras to interfejs API sieci neuronowej wysokiego poziomu, który może działać na innych popularnych platformach sieci DNN, aby uprościć opracowywanie. Dzięki Azure Machine Learning można szybko skalować zadania szkoleniowe przy użyciu elastycznych zasobów obliczeniowych w chmurze. Możesz również śledzić przebiegi trenowania, wersjonarować modele, wdrażać modele i nie tylko.

Bez względu na to, czy tworzysz model Keras od podstaw, czy wprowadzasz istniejący model do chmury, usługa Azure Machine Learning może pomóc w tworzeniu modeli gotowych do produkcji.

> [!NOTE]
> Jeśli używasz interfejsu API Keras **tf.keras** wbudowanego w tensorFlow, a nie autonomicznego pakietu Keras, zamiast tego zapoznaj się z tematem Train TensorFlow models (Trenuj [modele TensorFlow).](how-to-train-tensorflow.md)

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w jednym z tych środowisk:

- Azure Machine Learning wystąpienia obliczeniowego — nie trzeba pobierać ani instalowania

     - Ukończ [samouczek: konfigurowanie środowiska i obszaru roboczego,](tutorial-1st-experiment-sdk-setup.md) aby utworzyć dedykowany serwer notesu wstępnie załadowany z zestawem SDK i przykładowym repozytorium.
    - W folderze samples na serwerze notesów znajdź ukończony i rozwinięty notes, przechodząc do tego katalogu: **how-to-use-azureml > ml-frameworks > keras > train-hyperparameter-tune-deploy-with-keras.**

 - Twój własny Jupyter Notebook serwera

    - [Zainstaluj zestaw SDK Azure Machine Learning](/python/api/overview/azure/ml/install) (>= 1.15.0).
    - [Utwórz plik konfiguracji obszaru roboczego.](how-to-configure-environment.md#workspace)
    - [Pobieranie plików przykładowego skryptu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras) `keras_mnist.py` I `utils.py`

    Ukończoną wersję Jupyter Notebook tego przewodnika można [również](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) znaleźć na stronie przykładów usługi GitHub. Notes zawiera rozwinięte sekcje dotyczące inteligentnego dostrajania hiperparametrów, wdrażania modelu i widżetów notesu.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

Ta sekcja konfiguruje eksperyment trenowania przez załadowanie wymaganych pakietów języka Python, zainicjowanie obszaru roboczego, utworzenie zestawu FileDataset dla wejściowych danych szkoleniowych, utworzenie docelowego obiektu obliczeniowego i zdefiniowanie środowiska szkoleniowego.

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

Możesz użyć metody , aby zarejestrować zestaw danych w obszarze roboczym, aby można było je udostępniać innym osobom, używać ich ponownie w różnych eksperymentach i określać je za pomocą nazwy w skrypcie `register()` trenowania.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

### <a name="create-a-compute-target"></a>Tworzenie docelowego obiektu obliczeniowego

Utwórz docelowy obiekt obliczeniowy dla zadania szkoleniowego do uruchomienia. W tym przykładzie utworzysz klaster obliczeniowy z włączoną Azure Machine Learning GPU.

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

Zdefiniuj [środowisko usługi](concept-environments.md) Azure ML, które hermetyzuje zależności skryptu trenowania.

Najpierw zdefiniuj zależności conda w pliku YAML. W tym przykładzie plik ma nazwę `conda_dependencies.yml` .

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

Utwórz środowisko usługi Azure ML na pomocą tej specyfikacji środowiska Conda. Środowisko zostanie spakowane w kontenerze platformy Docker w czasie wykonywania.

Domyślnie, jeśli nie określono obrazu podstawowego, usługa Azure ML użyje obrazu procesora CPU `azureml.core.environment.DEFAULT_CPU_IMAGE` jako obrazu podstawowego. Ponieważ w tym przykładzie trenowanie jest uruchamiane w klastrze procesora GPU, należy określić obraz podstawowy procesora GPU, który ma niezbędne sterowniki i zależności procesora GPU. Usługa Azure ML utrzymuje zestaw podstawowych obrazów opublikowanych w witrynie Microsoft Container Registry (MCR), których można użyć. Aby uzyskać więcej informacji, zobacz repozytorium GitHub [Azure/AzureML-Containers.](https://github.com/Azure/AzureML-Containers)

```python
keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

Aby uzyskać więcej informacji na temat tworzenia i używania środowisk, zobacz [Tworzenie i używanie środowisk oprogramowania w programie Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Konfigurowanie i przesyłanie przebiegów trenowania

### <a name="create-a-scriptrunconfig"></a>Tworzenie pliku ScriptRunConfig
Najpierw pobierz dane z magazynu danych obszaru roboczego przy użyciu `Dataset` klasy .

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

Utwórz obiekt ScriptRunConfig, aby określić szczegóły konfiguracji zadania trenowania, w tym skrypt trenowania, środowisko do użycia i docelowy obiekt obliczeniowy do uruchomienia.

Wszelkie argumenty skryptu trenowania zostaną przekazane za pośrednictwem wiersza polecenia, jeśli zostaną określone w `arguments` parametrze . Element DatasetConsumptionConfig dla naszego zestawu FileDataset jest przekazywany jako argument do skryptu trenowania dla `--data-folder` argumentu . Usługa Azure ML rozpozna ten element DatasetConsumptionConfig jako punkt instalacji zapasowego magazynu danych, do którego można uzyskać dostęp za pomocą skryptu trenowania.

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

Aby uzyskać więcej informacji na temat konfigurowania zadań za pomocą polecenia ScriptRunConfig, zobacz Configure and submit training runs (Konfigurowanie [i przesyłanie przebiegów trenowania).](how-to-set-up-training-targets.md)

> [!WARNING]
> Jeśli wcześniej do konfigurowania zadań trenowania interfejsu Keras używano narzędzia do szacowania TensorFlow, należy pamiętać, że narzędzia do szacowania zostały wycofane od wersji 1.19.0 zestawu SDK. W przypadku zestawu Azure ML SDK >= 1.15.0 skrypt ScriptRunConfig jest zalecanym sposobem konfigurowania zadań szkoleniowych, w tym tych korzystających ze platform uczenia głębokiego. Typowe pytania dotyczące migracji można znaleźć w przewodniku [po migracji narzędzia do szacowania do pliku ScriptRunConfig.](how-to-migrate-from-estimators-to-scriptrunconfig.md)

### <a name="submit-your-run"></a>Przesyłanie uruchomienia

Obiekt [Run udostępnia](/python/api/azureml-core/azureml.core.run%28class%29) interfejs historii uruchamiania podczas działania zadania i po jego zakończeniu.

```Python
run = Experiment(workspace=ws, name='Tutorial-Keras-Minst').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Co się dzieje podczas wykonywania uruchomienia
Przebieg jest wykonywany przez następujące etapy:

- **Przygotowywanie:** obraz platformy Docker jest tworzony zgodnie ze zdefiniowanym środowiskiem. Obraz jest przekazywany do rejestru kontenerów obszaru roboczego i buforowany do późniejszego uruchomień. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i można je wyświetlać w celu monitorowania postępu. Jeśli zamiast tego zostanie określone środowisko z curated, zostanie użyty buforowany obraz zapasowy tego środowiska.

- **Skalowanie:** klaster próbuje skalować w górę, jeśli klaster Batch AI wymaga wykonania uruchomienia większej liczby węzłów niż jest obecnie dostępnych.

- **Uruchamianie:** wszystkie skrypty w folderze script są przekazywane do docelowego obiektu obliczeniowego, magazyny danych są instalowane lub kopiowane i `script` wykonywane. Dane wyjściowe ze strumienia stdout i folderu **./logs** są przesyłane strumieniowo do historii uruchamiania i mogą służyć do monitorowania uruchomienia.

- **Przetwarzanie po** przetworzeniu: folder **./outputs** uruchomienia jest kopiowany do historii uruchamiania.

## <a name="register-the-model"></a>Rejestrowanie modelu

Po wytrenowania modelu możesz zarejestrować go w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie i przechowywanie wersji modeli w obszarze roboczym w celu uproszczenia [zarządzania modelami i ich wdrażania.](concept-model-management-and-deployment.md)

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> How-to wdrożenia zawiera sekcję na temat rejestrowania [](how-to-deploy-and-where.md#choose-a-compute-target) modeli, ale możesz przejść bezpośrednio do tworzenia docelowego obiektu obliczeniowego dla wdrożenia, ponieważ masz już zarejestrowany model.

Możesz również pobrać lokalną kopię modelu. Może to być przydatne do wykonywania dodatkowych prac walidacji modelu lokalnie. W skrypcie trenowania obiekt saver TensorFlow utrwala model w folderze lokalnym (lokalnym dla `keras_mnist.py` docelowego obiektu obliczeniowego). Możesz użyć obiektu Run, aby pobrać kopię z historii uruchamiania.

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

W tym artykule wytrenowaliśmy i zarejestrowaliśmy model Keras na Azure Machine Learning. Aby dowiedzieć się, jak wdrożyć model, przejdź do naszego artykułu na temat wdrażania modelu.

* [Jak i gdzie wdrażać modele](how-to-deploy-and-where.md)
* [Śledzenie metryk przebiegów podczas trenowania](how-to-log-view-metrics.md)
* [Dostrajanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie przeszkolonego modelu](how-to-deploy-and-where.md)
* [Architektura referencyjna dla trenowania rozproszonego uczenia głębokiego na platformie Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
