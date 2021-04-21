---
title: Szkolenie modeli uczenia maszynowego scikit-learn
titleSuffix: Azure Machine Learning
description: Dowiedz się, Azure Machine Learning skalować w zewnątrz zadanie szkoleniowe scikit-learn przy użyciu zasobów obliczeniowych w chmurze elastycznej.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ef64d94ed3e860895bcc81a1429008205a1a8acb
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817111"
---
# <a name="train-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Trenowanie modeli scikit-learn na dużą skalę przy użyciu Azure Machine Learning

W tym artykule dowiesz się, jak uruchamiać skrypty szkoleniowe scikit-learn za pomocą Azure Machine Learning.

Przykładowe skrypty w tym artykule służą do klasyfikowania obrazów irysów w celu zbudowania modelu uczenia maszynowego na podstawie zestawu danych [irysów biblioteki](https://archive.ics.uci.edu/ml/datasets/iris)scikit-learn.

Bez względu na to, czy trenujesz model uczenia maszynowego scikit-learn od podstaw, czy wprowadzasz istniejący model do chmury, możesz użyć usługi Azure Machine Learning do skalowania zadań szkoleniowych typu open source przy użyciu elastycznych zasobów obliczeniowych w chmurze. Modele klasy produkcyjnej można tworzyć, wdrażać, wersjonarować i monitorować za pomocą Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom ten kod w jednym z tych środowisk:
 - Azure Machine Learning wystąpienia obliczeniowego — nie trzeba pobierać ani instalowania

    - Ukończ [samouczek: konfigurowanie środowiska i](tutorial-1st-experiment-sdk-setup.md)  obszaru roboczego, aby utworzyć dedykowany serwer notesów wstępnie załadowany z zestawem SDK i przykładowym repozytorium.
    - W folderze szkoleniowym przykładów na serwerze notesów znajdź ukończony i rozwinięty notes, przechodząc do tego katalogu: **how-to-use-azureml > ml-frameworks > scikit-learn > train-hyperparameter-tune-deploy-with-sklearn.**

 - Twój własny Jupyter Notebook serwera

    - [Zainstaluj zestaw SDK Azure Machine Learning](/python/api/overview/azure/ml/install) (>= 1.13.0).
    - [Utwórz plik konfiguracji obszaru roboczego.](how-to-configure-environment.md#workspace)

## <a name="set-up-the-experiment"></a>Konfigurowanie eksperymentu

W tej sekcji konfiguruje się eksperyment trenowania, ładując wymagane pakiety języka Python, inicjując obszar roboczy, definiując środowisko szkoleniowe i przygotowując skrypt trenowania.

### <a name="initialize-a-workspace"></a>Inicjowanie obszaru roboczego

Obszar [Azure Machine Learning jest](concept-workspace.md) zasobem najwyższego poziomu dla usługi. Zapewnia scentralizowane miejsce do pracy ze wszystkimi artefaktami, które tworzysz. W zestawie SDK języka Python możesz uzyskać dostęp do artefaktów obszaru roboczego, tworząc [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) obiekt .

Utwórz obiekt obszaru roboczego na `config.json` podstawie pliku utworzonego w [sekcji wymagań wstępnych.](#prerequisites)

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Przygotowywanie skryptów

W tym samouczku skrypt trenowania **train_iris.py** jest już dostarczany [tutaj.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py) W praktyce powinno być możliwe wykonywanie dowolnego niestandardowego skryptu trenowania bez konieczności modyfikowania kodu i uruchamianie go w usłudze Azure ML.

Uwagi:
- Podany skrypt trenowania pokazuje, jak rejestrować niektóre metryki w uruchomieniu usługi Azure ML przy użyciu `Run` obiektu w skrypcie.
- Podany skrypt trenowania używa przykładowych danych z  `iris = datasets.load_iris()` funkcji .  Aby korzystać z własnych danych i uzyskać do nich dostęp, zobacz, jak trenować przy użyciu [zestawów danych,](how-to-train-with-datasets.md) aby udostępnić dane podczas trenowania.

### <a name="define-your-environment"></a>Definiowanie środowiska

Aby zdefiniować środowisko usługi Azure [ML,](concept-environments.md) które hermetyzuje zależności skryptu trenowania, możesz zdefiniować środowisko niestandardowe lub użyć środowiska i środowiska wywłaszczonego usługi Azure ML.

#### <a name="use-a-curated-environment"></a>Korzystanie ze środowiska z curated
Opcjonalnie usługa Azure ML udostępnia wstępnie utworzone, curated środowiska, jeśli nie chcesz definiować własnego środowiska. Aby uzyskać więcej informacji, zobacz [tutaj](resource-curated-environments.md).
Jeśli chcesz używać środowiska z curated, możesz zamiast tego uruchomić następujące polecenie:

```python
from azureml.core import Environment

sklearn_env = Environment.get(workspace=ws, name='AzureML-Tutorial')
```

#### <a name="create-a-custom-environment"></a>Tworzenie środowiska niestandardowego

Możesz również utworzyć własne środowisko niestandardowe. Zdefiniuj zależności conda w pliku YAML; W tym przykładzie plik ma nazwę `conda_dependencies.yml` .

```yaml
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

Utwórz środowisko usługi Azure ML na pomocą tej specyfikacji środowiska Conda. Środowisko zostanie spakowane w kontenerze platformy Docker w czasie wykonywania.
```python
from azureml.core import Environment

sklearn_env = Environment.from_conda_specification(name='sklearn-env', file_path='conda_dependencies.yml')
```

Aby uzyskać więcej informacji na temat tworzenia i używania środowisk, zobacz [Tworzenie i używanie środowisk oprogramowania w programie Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Konfigurowanie i przesyłanie przebiegów trenowania

### <a name="create-a-scriptrunconfig"></a>Tworzenie pliku ScriptRunConfig
Utwórz obiekt ScriptRunConfig, aby określić szczegóły konfiguracji zadania trenowania, w tym skrypt trenowania, środowisko do użycia i docelowy obiekt obliczeniowy do uruchomienia.
Wszelkie argumenty skryptu trenowania zostaną przekazane za pośrednictwem wiersza polecenia, jeśli zostaną określone w `arguments` parametrze .

Poniższy kod skonfiguruje obiekt ScriptRunConfig do przesyłania zadania do wykonania na komputerze lokalnym.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      environment=sklearn_env)
```

Jeśli zamiast tego chcesz uruchomić zadanie w klastrze zdalnym, możesz określić żądany docelowy obiekt obliczeniowy dla `compute_target` parametru ScriptRunConfig.

```python
from azureml.core import ScriptRunConfig

compute_target = ws.compute_targets['<my-cluster-name>']
src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      compute_target=compute_target,
                      environment=sklearn_env)
```

### <a name="submit-your-run"></a>Przesyłanie uruchomienia
```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-TrainIRIS').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning uruchamia skrypty szkoleniowe, kopiując cały katalog źródłowy. Jeśli masz poufne dane, których nie chcesz przekazywać, użyj pliku [.ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) lub nie dołączaj ich do katalogu źródłowego . Zamiast tego należy uzyskać dostęp do danych przy użyciu zestawu danych usługi Azure [ML.](how-to-train-with-datasets.md)

### <a name="what-happens-during-run-execution"></a>Co się dzieje podczas wykonywania uruchomienia
Przebieg jest wykonywany przez następujące etapy:

- **Przygotowywanie:** obraz platformy Docker jest tworzony zgodnie ze zdefiniowanym środowiskiem. Obraz jest przekazywany do rejestru kontenerów obszaru roboczego i buforowany do późniejszego uruchomień. Dzienniki są również przesyłane strumieniowo do historii uruchamiania i można je wyświetlać w celu monitorowania postępu. Jeśli zamiast tego zostanie określone środowisko z curated, zostanie użyty buforowany obraz zapasowy tego środowiska.

- **Skalowanie:** klaster próbuje skalować w górę, jeśli klaster Batch AI wymaga wykonania uruchomienia większej liczby węzłów niż jest obecnie dostępnych.

- **Uruchamianie:** wszystkie skrypty w folderze script są przekazywane do docelowego obiektu obliczeniowego, magazyny danych są instalowane lub kopiowane i `script` wykonywane. Dane wyjściowe z stdout i **folderu ./logs** są przesyłane strumieniowo do historii uruchamiania i mogą służyć do monitorowania uruchomienia.

- **Przetwarzanie po:** folder **./outputs** uruchomienia jest kopiowany do historii uruchamiania.

## <a name="save-and-register-the-model"></a>Zapisywanie i rejestrowanie modelu

Po wytrenowania modelu możesz go zapisać i zarejestrować w obszarze roboczym. Rejestracja modelu umożliwia przechowywanie i przechowywanie wersji modeli w obszarze roboczym w celu uproszczenia [zarządzania modelami i ich wdrażania.](concept-model-management-and-deployment.md)

Dodaj następujący kod do skryptu trenowania, train_iris.py, aby zapisać model. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Zarejestruj model w obszarze roboczym przy użyciu następującego kodu. Dzięki określeniu parametrów , i wdrożenie modelu bez `model_framework` `model_framework_version` kodu staje się `resource_configuration` dostępne. Wdrożenie modelu bez kodu umożliwia bezpośrednie wdrożenie modelu jako usługi internetowej z zarejestrowanego modelu, a obiekt definiuje zasób obliczeniowy [`ResourceConfiguration`](/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration) dla usługi internetowej.

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

Właśnie zarejestrowany model można wdrożyć dokładnie tak samo jak każdy inny zarejestrowany model w usłudze Azure ML. How-to wdrożenia zawiera sekcję na temat rejestrowania [](how-to-deploy-and-where.md#choose-a-compute-target) modeli, ale możesz przejść bezpośrednio do tworzenia docelowego obiektu obliczeniowego dla wdrożenia, ponieważ masz już zarejestrowany model.

### <a name="preview-no-code-model-deployment"></a>(Wersja zapoznawcza) Wdrażanie modelu bez kodu

Zamiast tradycyjnej trasy wdrażania można również użyć funkcji wdrażania bez użycia kodu (wersja zapoznawcza) dla biblioteki scikit-learn. Wdrażanie modelu bez kodu jest obsługiwane dla wszystkich wbudowanych typów modeli scikit-learn. Rejestrując model w sposób pokazany powyżej przy użyciu parametrów , i , możesz po prostu użyć funkcji statycznej do `model_framework` `model_framework_version` wdrożenia `resource_configuration` [`deploy()`](/python/api/azureml-core/azureml.core.model%28class%29#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) modelu.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

UWAGA: Te zależności są zawarte we wstępnie skon skontekstowym kontenerze wnioskowania scikit-learn.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

Pełne [how-to covers](how-to-deploy-and-where.md) deployment in Azure Machine Learning in greater depth.


## <a name="next-steps"></a>Następne kroki

W tym artykule wytrenowaliśmy i zarejestrowaliśmy model scikit-learn oraz poznaliśmy opcje wdrażania. Zobacz inne artykuły, aby dowiedzieć się więcej na temat Azure Machine Learning.

* [Śledzenie metryk przebiegów podczas trenowania](how-to-log-view-metrics.md)
* [Dostrajanie hiperparametrów](how-to-tune-hyperparameters.md)
