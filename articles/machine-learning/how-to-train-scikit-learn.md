---
title: Uczenie scikit — uczenie modeli uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchamiać skrypty szkoleniowe scikit na Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 07/24/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 4221ed6a927d0c589407dc38b5371ad8a65d2174
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88054395"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Kompiluj scikit — Poznaj modele na dużą skalę dzięki Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak uruchamiać skrypty szkoleniowe scikit z użyciem Azure Machine Learning.

Przykładowe skrypty w tym artykule służą do klasyfikowania obrazów kwiatów w formacie Iris w celu utworzenia modelu uczenia maszynowego na podstawie [zestawu danych Iris](https://archive.ics.uci.edu/ml/datasets/iris)scikit.

Bez względu na to, czy przeprowadzasz szkolenia z modelu uczenia maszynowego scikit — uczenie się od podstaw lub przenosisz istniejący model do chmury, możesz użyć Azure Machine Learning, aby skalować zadania szkoleniowe typu "open source" przy użyciu elastycznych zasobów obliczeniowych w chmurze. Możesz tworzyć, wdrażać, instalować i monitorować modele klasy produkcyjnej za pomocą Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w dowolnym z następujących środowisk:
 - Wystąpienie obliczeniowe Azure Machine Learning — nie jest wymagane pobieranie ani instalacja

    - Ukończ [Samouczek: Zainstaluj środowisko i obszar roboczy](tutorial-1st-experiment-sdk-setup.md) , aby utworzyć dedykowany serwer notesu wstępnie załadowany z zestawem SDK i przykładowym repozytorium.
    - W folderze uczenie przykładów na serwerze notesu Znajdź ukończony i rozwinięty Notes, przechodząc do tego katalogu: How to- **use-azure > ml-frameworks > scikit-uczenie > uczenie > uczenie-parametr-dostrajania-Deploy-with-skryptu sklearn** folder.

 - Własny serwer Jupyter Notebook

    - [Zainstaluj zestaw SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Utwórz plik konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

Ta sekcja umożliwia skonfigurowanie eksperymentu szkoleniowego przez załadowanie wymaganych pakietów języka Python, zainicjowanie obszaru roboczego, utworzenie eksperymentu i przekazanie danych szkoleniowych i skryptów szkoleniowych.

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

[Obszar roboczy Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Zapewnia ono scentralizowane miejsce do pracy ze wszystkimi tworzonymi artefaktami. W zestawie SDK języka Python można uzyskać dostęp do artefaktów obszaru roboczego przez utworzenie [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) obiektu.

Utwórz obiekt obszaru roboczego z `config.json` pliku utworzonego w [sekcji wymagania wstępne](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```


### <a name="prepare-scripts"></a>Przygotuj skrypty

W tym samouczku skrypt szkoleniowy **train_iris. PR** został już podany [tutaj](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py). W tym celu należy mieć możliwość wykonania dowolnego niestandardowego skryptu szkoleniowego i uruchomienia go z platformą Azure ML bez konieczności modyfikowania kodu.

Uwagi:
- W podanym skrypcie szkoleniowym przedstawiono sposób rejestrowania niektórych metryk w usłudze Azure ML przy użyciu `Run` obiektu w skrypcie.
- Dostarczony skrypt szkoleniowy używa przykładowych danych z `iris = datasets.load_iris()` funkcji.  W przypadku własnych danych może być konieczne wykonanie kroków takich jak [przekazywanie zestawu danych i skryptów](how-to-train-keras.md#data-upload) w celu udostępnienia danych podczas szkoleń.

### <a name="define-your-environment"></a>Zdefiniuj swoje środowisko.

#### <a name="create-a-custom-environment"></a>Utwórz środowisko niestandardowe.

Twórz środowisko Conda (sklearn-ENV. yml).
Aby napisać środowisko Conda z notesu, możesz dodać wiersz ```%%writefile sklearn-env.yml``` w górnej części komórki.

```yaml
name: sklearn-training-env
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

Utwórz środowisko usługi Azure ML na podstawie tej specyfikacji środowiska Conda. Środowisko zostanie spakowane w kontenerze platformy Docker w czasie wykonywania.
```python
from azureml.core import Environment

myenv = Environment.from_conda_specification(name = "myenv", file_path = "sklearn-env.yml")
myenv.docker.enabled = True
```

#### <a name="use-a-curated-environment"></a>Korzystanie z nadzorowanego środowiska
Usługa Azure ML udostępnia wstępnie przygotowane, nadzorowane środowiska kontenerów, jeśli nie chcesz tworzyć własnych obrazów. Aby uzyskać więcej informacji, zobacz [tutaj](resource-curated-environments.md).
Jeśli chcesz użyć środowiska nadzorowanego, możesz zamiast tego uruchomić następujące polecenie:

```python
env = Environment.get(workspace=ws, name="AzureML-Tutorial")
```

### <a name="create-a-scriptrunconfig"></a>Utwórz ScriptRunConfig

Ta ScriptRunConfig prześle zadanie do wykonania na lokalnym elemencie docelowym obliczeń.

```python
from azureml.core import ScriptRunConfig

sklearnconfig = ScriptRunConfig(source_directory='.', script='train_iris.py')
sklearnconfig.run_config.environment = myenv
```

Jeśli chcesz przesłać do klastra zdalnego, możesz zmienić run_config. Target na żądany element docelowy obliczeń.

### <a name="submit-your-run"></a>Prześlij swój przebieg
```python
from azureml.core import Experiment

run = Experiment(ws,'train-sklearn').submit(config=sklearnconfig)
run.wait_for_completion(show_output=True)

```

> [!WARNING]
> Azure Machine Learning uruchamia skrypty szkoleniowe, kopiując cały katalog źródłowy. Jeśli masz poufne dane, które nie mają być przekazywane, użyj [pliku. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) lub nie dołączaj go do katalogu źródłowego. Zamiast tego można uzyskiwać dostęp do danych przy użyciu usługi [datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py).

Aby uzyskać więcej informacji na temat dostosowywania środowiska języka Python, zobacz [Tworzenie środowisk i zarządzanie nimi na potrzeby szkolenia i wdrażania](how-to-use-environments.md). 

## <a name="what-happens-during-run-execution"></a>Co się stanie w trakcie wykonywania
Gdy przebieg jest wykonywany, przechodzi przez następujące etapy:

- **Przygotowywanie**: obraz platformy Docker jest tworzony zgodnie z TensorFlow szacowania. Obraz zostanie przekazany do rejestru kontenerów obszaru roboczego i zapisany w pamięci podręcznej do późniejszego uruchomienia. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i mogą być przeglądane w celu monitorowania postępu.

- **Skalowanie**: klaster próbuje skalować w górę, jeśli klaster Batch AI wymaga większej liczby węzłów do uruchomienia przebiegu, niż jest to obecnie dostępne.

- **Uruchamianie**: wszystkie skrypty w folderze skryptów są przekazywane do obiektu docelowego obliczeń, magazyny danych są instalowane lub kopiowane, a entry_script jest wykonywane. Dane wyjściowe z stdout i folder./Logs są przesyłane strumieniowo do historii uruchamiania i mogą być używane do monitorowania przebiegu.

- **Przetwarzanie końcowe**: folder./Outputs przebiegu jest kopiowany do historii uruchamiania.

## <a name="save-and-register-the-model"></a>Zapisz i zarejestruj model

Po przeszkoleniu modelu możesz go zapisać i zarejestrować w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie modeli i ich wersji w obszarze roboczym w celu uproszczenia [zarządzania modelami i ich wdrażania](concept-model-management-and-deployment.md).

Dodaj następujący kod do skryptu szkoleniowego, train_iris. PR, aby zapisać model. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Zarejestruj model w obszarze roboczym przy użyciu następującego kodu. Przez określenie parametrów `model_framework` , `model_framework_version` , i `resource_configuration` , wdrożenie modelu No-Code jest niedostępne. Wdrożenie modelu bez kodu pozwala bezpośrednio wdrożyć model jako usługę sieci Web z zarejestrowanego modelu, a [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) obiekt definiuje zasób obliczeniowy dla usługi sieci Web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>wdrażania

Właśnie zarejestrowany model można wdrożyć w taki sam sposób jak każdy inny zarejestrowany model w Azure Machine Learning, niezależnie od tego, który szacowania używany do uczenia się. Wdrożenie How-to zawiera sekcję dotyczącą rejestrowania modeli, ale możesz przejść bezpośrednio do tworzenia celu [obliczeń](how-to-deploy-and-where.md#choose-a-compute-target) dla wdrożenia, ponieważ istnieje już zarejestrowany model.

### <a name="preview-no-code-model-deployment"></a>Przeglądania Wdrożenie modelu bez kodu

Zamiast tradycyjnej trasy wdrożenia można również użyć funkcji wdrażania bez kodu (wersja zapoznawcza) dla scikit — uczenie się. Nie jest obsługiwane wdrożenie modelu bez kodu dla wszystkich wbudowanych typów modeli scikit. Rejestrując model, jak pokazano powyżej z `model_framework` `model_framework_version` `resource_configuration` parametrami, i, można po prostu użyć [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) funkcji statycznej do wdrożenia modelu.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

Uwaga: te zależności są zawarte we wstępnie skompilowanym kontenerze wnioskowania scikit.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

Pełne [Omówienie](how-to-deploy-and-where.md) wdrażania w Azure Machine Learning.


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono przeszkolony i zarejestrowany model uczenia scikit oraz informacje o opcjach wdrażania. Zapoznaj się z innymi artykułami, aby dowiedzieć się więcej na temat Azure Machine Learning.

* [Śledzenie metryk uruchamiania podczas szkolenia](how-to-track-experiments.md)
* [Dostrajanie hiperparametrów](how-to-tune-hyperparameters.md)
* [Architektura referencyjna na potrzeby rozproszonego szkolenia uczenia głębokiego na platformie Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
