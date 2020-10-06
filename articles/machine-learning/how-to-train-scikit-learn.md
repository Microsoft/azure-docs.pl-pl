---
title: Uczenie scikit — uczenie modeli uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchamiać skrypty szkoleniowe scikit na Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: f0c923bcb7df930ed4b1380d487ededc6c160844
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743747"
---
# <a name="train-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Uczenie scikit — uczenie modeli na dużą skalę dzięki Azure Machine Learning

W tym artykule dowiesz się, jak uruchamiać skrypty szkoleniowe scikit z użyciem Azure Machine Learning.

Przykładowe skrypty w tym artykule służą do klasyfikowania obrazów kwiatów w formacie Iris w celu utworzenia modelu uczenia maszynowego na podstawie [zestawu danych Iris](https://archive.ics.uci.edu/ml/datasets/iris)scikit.

Bez względu na to, czy przeprowadzasz szkolenia z modelu uczenia maszynowego scikit — uczenie się od podstaw lub przenosisz istniejący model do chmury, możesz użyć Azure Machine Learning, aby skalować zadania szkoleniowe typu "open source" przy użyciu elastycznych zasobów obliczeniowych w chmurze. Możesz tworzyć, wdrażać, instalować i monitorować modele klasy produkcyjnej za pomocą Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w dowolnym z następujących środowisk:
 - Wystąpienie obliczeniowe Azure Machine Learning — nie jest wymagane pobieranie ani instalacja

    - Ukończ [Samouczek: Zainstaluj środowisko i obszar roboczy](tutorial-1st-experiment-sdk-setup.md)  , aby utworzyć dedykowany serwer notesu wstępnie załadowany z zestawem SDK i przykładowym repozytorium.
    - W folderze szkolenia przykładów na serwerze notesu Znajdź ukończony i rozwinięty Notes, przechodząc do tego katalogu: How to- **use-azure > ml-frameworks > scikit-Dowiedz się > uczenie-parametr-dostrajania-Deploy-with-skryptu sklearn** folder.

 - Własny serwer Jupyter Notebook

    - [Zainstaluj zestaw SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) (>= 1.13.0).
    - [Utwórz plik konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

Ta sekcja umożliwia skonfigurowanie eksperymentu szkoleniowego przez załadowanie wymaganych pakietów języka Python, zainicjowanie obszaru roboczego, zdefiniowanie środowiska szkoleniowego i przygotowanie skryptu szkoleniowego.

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

[Obszar roboczy Azure Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla usługi. Zapewnia ono scentralizowane miejsce do pracy ze wszystkimi tworzonymi artefaktami. W zestawie SDK języka Python można uzyskać dostęp do artefaktów obszaru roboczego przez utworzenie [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) obiektu.

Utwórz obiekt obszaru roboczego z `config.json` pliku utworzonego w [sekcji wymagania wstępne](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Przygotuj skrypty

W tym samouczku skrypt szkoleniowy **train_iris. PR** został już podany [tutaj](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py). W tym celu należy mieć możliwość wykonania dowolnego niestandardowego skryptu szkoleniowego i uruchomienia go z platformą Azure ML bez konieczności modyfikowania kodu.

Uwagi:
- W podanym skrypcie szkoleniowym przedstawiono sposób rejestrowania niektórych metryk w usłudze Azure ML przy użyciu `Run` obiektu w skrypcie.
- Dostarczony skrypt szkoleniowy używa przykładowych danych z  `iris = datasets.load_iris()` funkcji.  Aby korzystać z własnych danych i uzyskiwać do nich dostęp, zobacz [jak uczenie się z zestawami](how-to-train-with-datasets.md) danych, aby udostępnić dane podczas szkoleń.

### <a name="define-your-environment"></a>Definiowanie środowiska

Aby zdefiniować [środowisko](concept-environments.md) usługi Azure ml, które hermetyzuje zależności skryptu szkoleniowego, można zdefiniować środowisko niestandardowe lub użyć środowiska usługi Azure ml pod opieką.

#### <a name="use-a-curated-environment"></a>Korzystanie z nadzorowanego środowiska
Opcjonalnie usługa Azure ML udostępnia wstępnie utworzone, nadzorowane środowiska, jeśli nie chcesz definiować własnego środowiska. Aby uzyskać więcej informacji, zobacz [tutaj](resource-curated-environments.md).
Jeśli chcesz użyć środowiska nadzorowanego, możesz zamiast tego uruchomić następujące polecenie:

```python
sklearn_env = Environment.get(workspace=ws, name='AzureML-Tutorial')
```

#### <a name="create-a-custom-environment"></a>Tworzenie środowiska niestandardowego

Możesz również utworzyć własne środowisko niestandardowe. Zdefiniuj zależności Conda w pliku YAML; w tym przykładzie plik ma nazwę `conda_dependencies.yml` .

```yaml
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

sklearn_env = Environment.from_conda_specification(name='sklearn-env', file_path='conda_dependencies.yml')
```

Aby uzyskać więcej informacji na temat tworzenia i używania środowisk, zobacz [Tworzenie i używanie środowisk oprogramowania w programie Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Konfigurowanie i przesyłanie przebiegu szkoleniowego

### <a name="create-a-scriptrunconfig"></a>Utwórz ScriptRunConfig
Utwórz obiekt ScriptRunConfig, aby określić szczegóły konfiguracji zadania szkoleniowego, w tym skrypt szkoleniowy, środowisko, które ma być używane, oraz miejsce docelowe obliczeń do uruchomienia.
Wszystkie argumenty skryptu szkoleniowego zostaną przekazane za pośrednictwem wiersza polecenia, jeśli zostało to określone w `arguments` parametrze.

Poniższy kod spowoduje skonfigurowanie obiektu ScriptRunConfig na potrzeby przesyłania zadania do wykonania na komputerze lokalnym.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      environment=sklearn_env)
```

Jeśli chcesz zamiast tego uruchomić zadanie w klastrze zdalnym, możesz określić żądany obiekt docelowy obliczeń do `compute_target` parametru ScriptRunConfig.

```python
from azureml.core import ScriptRunConfig

compute_target = ws.compute_targets['<my-cluster-name>']
src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      compute_target=compute_target,
                      environment=sklearn_env)
```

### <a name="submit-your-run"></a>Prześlij swój przebieg
```python
from azureml.core import Experiment

run = Experiment(ws,'train-iris').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning uruchamia skrypty szkoleniowe, kopiując cały katalog źródłowy. Jeśli masz poufne dane, które nie mają być przekazywane, użyj [pliku. ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) lub nie dołączaj go do katalogu źródłowego. Zamiast tego Uzyskuj dostęp do danych za pomocą [zestawu danych](how-to-train-with-datasets.md)usługi Azure ml.

### <a name="what-happens-during-run-execution"></a>Co się stanie w trakcie wykonywania
Gdy przebieg jest wykonywany, przechodzi przez następujące etapy:

- **Przygotowywanie**: obraz platformy Docker jest tworzony zgodnie ze zdefiniowanym środowiskiem. Obraz zostanie przekazany do rejestru kontenerów obszaru roboczego i zapisany w pamięci podręcznej do późniejszego uruchomienia. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i mogą być przeglądane w celu monitorowania postępu. Jeśli zamiast tego zostanie określone środowisko nadzorowane, zostanie użyty obraz w pamięci podręcznej, w którym będzie można wykonać kopię zapasową środowiska nadzorowanego.

- **Skalowanie**: klaster próbuje skalować w górę, jeśli klaster Batch AI wymaga większej liczby węzłów do uruchomienia przebiegu, niż jest to obecnie dostępne.

- **Uruchomione**: wszystkie skrypty w folderze skryptów są przekazywane do obiektu docelowego obliczeń, magazyny danych są instalowane lub kopiowane i `script` wykonywane. Dane wyjściowe z stdout i folder **./Logs** są przesyłane strumieniowo do historii uruchamiania i mogą być używane do monitorowania przebiegu.

- **Przetwarzanie końcowe**: folder **./Outputs** przebiegu jest kopiowany do historii uruchamiania.

## <a name="save-and-register-the-model"></a>Zapisz i zarejestruj model

Po przeszkoleniu modelu możesz go zapisać i zarejestrować w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie modeli i ich wersji w obszarze roboczym w celu uproszczenia [zarządzania modelami i ich wdrażania](concept-model-management-and-deployment.md).

Dodaj następujący kod do skryptu szkoleniowego, train_iris. PR, aby zapisać model. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Zarejestruj model w obszarze roboczym przy użyciu następującego kodu. Przez określenie parametrów `model_framework` , `model_framework_version` , i `resource_configuration` , wdrożenie modelu No-Code jest niedostępne. Wdrożenie modelu bez kodu pozwala bezpośrednio wdrożyć model jako usługę sieci Web z zarejestrowanego modelu, a [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py&preserve-view=true) obiekt definiuje zasób obliczeniowy dla usługi sieci Web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Wdrożenie

Właśnie zarejestrowany model można wdrożyć w taki sam sposób jak każdy inny zarejestrowany model na platformie Azure ML. Wdrożenie How-to zawiera sekcję dotyczącą rejestrowania modeli, ale możesz przejść bezpośrednio do tworzenia celu [obliczeń](how-to-deploy-and-where.md#choose-a-compute-target) dla wdrożenia, ponieważ istnieje już zarejestrowany model.

### <a name="preview-no-code-model-deployment"></a>Przeglądania Wdrożenie modelu bez kodu

Zamiast tradycyjnej trasy wdrożenia można również użyć funkcji wdrażania bez kodu (wersja zapoznawcza) dla scikit — uczenie się. Nie jest obsługiwane wdrożenie modelu bez kodu dla wszystkich wbudowanych typów modeli scikit. Rejestrując model, jak pokazano powyżej z `model_framework` `model_framework_version` `resource_configuration` parametrami, i, można po prostu użyć [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) funkcji statycznej do wdrożenia modelu.

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
