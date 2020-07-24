---
title: Uczenie modeli Keras uczenia głębokiego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak nauczyć i zarejestrować model klasyfikacji sieci Keras głębokiej neuronowych uruchomiony na TensorFlow przy użyciu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 61e92d33cf4ec54881ab7794e503cfaeb6397e8d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87060752"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Uczenie i rejestrowanie modelu klasyfikacji Keras przy użyciu Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule przedstawiono sposób uczenia i rejestrowania modelu klasyfikacji Keras opartego na TensorFlow przy użyciu Azure Machine Learning. Używa popularnego [zestawu danych mnist ręcznie](http://yann.lecun.com/exdb/mnist/) do klasyfikowania cyfr pisanych ręcznie przy użyciu sieci głębokiej neuronowych (DNN) utworzonej przy użyciu [biblioteki języka Python Keras](https://keras.io) działającej w oparciu o [TensorFlow](https://www.tensorflow.org/overview).

Keras to wysoki poziom interfejsu API sieci neuronowych, który może uruchamiać inne popularne platformy DNN, aby uprościć programowanie. Dzięki Azure Machine Learning można szybko skalować zadania szkoleniowe za pomocą elastycznych zasobów obliczeniowych w chmurze. Możesz również śledzić przebiegi szkoleniowe, modele wersji, wdrażać modele i wiele innych.

Bez względu na to, czy tworzysz model Keras z podstaw, czy przenosisz istniejący model do chmury, Azure Machine Learning może pomóc w tworzeniu modeli gotowych do produkcji.

Zapoznaj się z [artykułem koncepcyjnym](concept-deep-learning-vs-machine-learning.md) , aby uzyskać informacje dotyczące różnic między uczeniem maszynowym i uczeniem głębokiego.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w dowolnym z następujących środowisk:

- Wystąpienie obliczeniowe Azure Machine Learning — nie jest wymagane pobieranie ani instalacja

     - Ukończ [Samouczek: Zainstaluj środowisko i obszar roboczy](tutorial-1st-experiment-sdk-setup.md) , aby utworzyć dedykowany serwer notesu wstępnie załadowany z zestawem SDK i przykładowym repozytorium.
    - W folderze Samples na serwerze notesu Znajdź ukończony i rozwinięty Notes, przechodząc do tego katalogu: How to- **use-azure > Training-with-Learning-uczenie > uczenie** ----------keras.

 - Własny serwer Jupyter Notebook

    - [Zainstaluj zestaw SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Utwórz plik konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).
    - [Pobierz pliki](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` przykładowego skryptu lub`utils.py`

    Ukończoną [wersję Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) tego przewodnika można również znaleźć na stronie przykładów usługi GitHub. Notes obejmuje rozwinięte sekcje obejmujące dostrajanie inteligentnego parametru, wdrożenie modelu i widżety notesu.

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

Ta sekcja umożliwia skonfigurowanie eksperymentu szkoleniowego przez załadowanie wymaganych pakietów języka Python, zainicjowanie obszaru roboczego, utworzenie eksperymentu i przekazanie danych szkoleniowych i skryptów szkoleniowych.

### <a name="import-packages"></a>Importowanie pakietów

Najpierw zaimportuj niezbędne biblioteki języka Python.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

[Obszar roboczy Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Zapewnia ono scentralizowane miejsce do pracy ze wszystkimi tworzonymi artefaktami. W zestawie SDK języka Python można uzyskać dostęp do artefaktów obszaru roboczego przez utworzenie [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) obiektu.

Utwórz obiekt obszaru roboczego z `config.json` pliku utworzonego w [sekcji wymagania wstępne](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Utwórz eksperyment o nazwie "keras-mnist ręcznie" w obszarze roboczym.

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
### <a name="create-a-file-dataset"></a>Utwórz plik DataSet

`FileDataset`Obiekt odwołuje się do co najmniej jednego pliku w magazynie danych obszaru roboczego lub publicznych adresów URL. Pliki mogą być w dowolnym formacie, a Klasa oferuje możliwość pobierania lub instalowania plików do obliczeń. Tworząc `FileDataset` , Utwórz odwołanie do lokalizacji źródła danych. Jeśli zastosowano jakiekolwiek przekształcenia do zestawu danych, zostaną one zapisane również w zestawie danych. Dane pozostają w istniejącej lokalizacji, więc nie są naliczane żadne dodatkowe koszty związane z magazynem. Aby uzyskać więcej informacji, [Zobacz przewodnik po tym](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) `Dataset` pakiecie.

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

Użyj `register()` metody, aby zarejestrować zestaw danych w obszarze roboczym, aby można było udostępnić go innym osobom, ponownie używać w różnych eksperymentach i określać nazwę w skrypcie szkoleniowym.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

## <a name="create-a-compute-target"></a>Tworzenie obiektu docelowego obliczeń

Utwórz obiekt docelowy obliczeń dla zadania TensorFlow. W tym przykładzie należy utworzyć klaster obliczeniowy z obsługą procesora GPU Azure Machine Learning.

```Python
cluster_name = "gpucluster"

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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Tworzenie TensorFlow szacowania i importowanie Keras

[TensorFlow szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) zapewnia prosty sposób uruchamiania zadań szkolenia TensorFlow na serwerze docelowym obliczeń. Ponieważ Keras działa na TensorFlow, można użyć szacowania TensorFlow i zaimportować bibliotekę Keras przy użyciu `pip_packages` argumentu.

Najpierw Pobierz dane z magazynu danych obszaru roboczego przy użyciu `Dataset` klasy.

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

TensorFlow szacowania jest implementowane za pomocą klasy generycznej [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) , która może służyć do obsługi dowolnej struktury. Ponadto Utwórz słownik `script_params` zawierający ustawienia DNN. Aby uzyskać więcej informacji o modelach szkoleniowych przy użyciu generycznej szacowania, zobacz [uczenie modeli Azure Machine Learning przy](how-to-train-ml-models.md) użyciu usługi szacowania

```python
from azureml.train.dnn import TensorFlow

script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Prześlij przebieg

[Obiekt Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) udostępnia interfejs do historii uruchamiania, gdy zadanie jest uruchomione i po jego zakończeniu.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Gdy przebieg jest wykonywany, przechodzi przez następujące etapy:

- **Przygotowywanie**: obraz platformy Docker jest tworzony zgodnie z TensorFlow szacowania. Obraz zostanie przekazany do rejestru kontenerów obszaru roboczego i zapisany w pamięci podręcznej do późniejszego uruchomienia. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i mogą być przeglądane w celu monitorowania postępu.

- **Skalowanie**: klaster próbuje skalować w górę, jeśli klaster Batch AI wymaga większej liczby węzłów do uruchomienia przebiegu, niż jest to obecnie dostępne.

- **Uruchamianie**: wszystkie skrypty w folderze skryptów są przekazywane do obiektu docelowego obliczeń, magazyny danych są instalowane lub kopiowane, a entry_script jest wykonywane. Dane wyjściowe z stdout i folder./Logs są przesyłane strumieniowo do historii uruchamiania i mogą być używane do monitorowania przebiegu.

- **Przetwarzanie końcowe**: folder./Outputs przebiegu jest kopiowany do historii uruchamiania.

## <a name="register-the-model"></a>Rejestrowanie modelu

Po przeszkoleniu modelu DNN można zarejestrować go w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie modeli i ich wersji w obszarze roboczym w celu uproszczenia [zarządzania modelami i ich wdrażania](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

> [!TIP]
> Właśnie zarejestrowany model jest wdrażany w taki sam sposób jak każdy inny zarejestrowany model w Azure Machine Learning, niezależnie od tego, który szacowania używany do szkoleń. Wdrożenie How-to zawiera sekcję dotyczącą rejestrowania modeli, ale możesz przejść bezpośrednio do tworzenia celu [obliczeń](how-to-deploy-and-where.md#choose-a-compute-target) dla wdrożenia, ponieważ istnieje już zarejestrowany model.

Możesz również pobrać lokalną kopię modelu. Może to być przydatne do wykonywania dodatkowych czynności związanych z walidacją modelu lokalnie. W skrypcie szkoleniowym `mnist-keras.py` obiekt wygaszacza TensorFlow zachowuje model do folderu lokalnego (lokalnie dla elementu docelowego obliczeń). Za pomocą obiektu Run można pobrać kopię z magazynu danych.

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

> [!div class="nextstepaction"]
> [Jak i gdzie wdrażać modele](how-to-deploy-and-where.md)
* [Śledzenie metryk uruchamiania podczas szkolenia](how-to-track-experiments.md)
* [Dostrajanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Wdrażanie przeszkolonego modelu](how-to-deploy-and-where.md)
* [Architektura referencyjna na potrzeby rozproszonego szkolenia uczenia głębokiego na platformie Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
