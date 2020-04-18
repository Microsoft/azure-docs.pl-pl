---
title: Uruchamianie, monitorowanie i anulowanie przebiegów szkoleniowych w pythonie
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchamiać, ustawiać stan eksperymentów uczenia maszynowego, oznaczać go i organizować.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 01/09/2020
ms.openlocfilehash: cdc739c7464b3deb87faaaabfd8d657ae8c28678
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617761"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Uruchamianie, monitorowanie i anulowanie przebiegów szkoleniowych w pythonie
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Zestaw [SDK usługi Azure Machine Learning dla języka Python,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) [interfejs wiersza polecenia uczenia maszynowego](reference-azure-machine-learning-cli.md)i [studio usługi Azure Machine Learning](https://ml.azure.com) zapewniają różne metody monitorowania, organizowania i zarządzania przebiegami do celów szkoleniowych i eksperymentalnych.

W tym artykule przedstawiono przykłady następujących zadań:

* Monitoruj wydajność uruchamiania.
* Anuluj lub kończy się niepowodzenie.
* Tworzenie przebiegów podrzędnych.
* Otaguj i znajdź przebiegi.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne będą następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* [Obszar roboczy usługi Azure Machine Learning](how-to-manage-workspace.md).

* Zestaw SDK usługi Azure Machine Learning dla języka Python (wersja 1.0.21 lub nowsza). Aby zainstalować lub zaktualizować do najnowszej wersji pakietu SDK, zobacz [Instalowanie lub aktualizowanie pakietu SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    Aby sprawdzić swoją wersję zestawu SDK usługi Azure Machine Learning, użyj następującego kodu:

    ```python
    print(azureml.core.VERSION)
    ```

* [Rozszerzenie interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) platformy Azure i interfejsu [wiersza polecenia dla usługi Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="start-a-run-and-its-logging-process"></a>Rozpoczynanie biegu i procesu rejestrowania

### <a name="using-the-sdk"></a>Używanie zestawu SDK

Skonfiguruj eksperyment, importując klasy [Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), [Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)i [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) z pakietu [azureml.core.](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py)

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Rozpocznij uruchamianie i jego [`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) proces rejestrowania za pomocą metody.

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>Korzystanie z interfejsu wiersza polecenia

Aby rozpocząć przebieg eksperymentu, należy wykonać następujące czynności:

1. W wierszu polecenia powłoki lub polecenia użyj interfejsu wiersza polecenia platformy Azure do uwierzytelniania w ramach subskrypcji platformy Azure:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

1. Dołącz konfigurację obszaru roboczego do folderu zawierającego skrypt szkolenia. Zamień `myworkspace` obszar roboczy usługi Azure Machine Learning. Zamień `myresourcegroup` grupę zasobów platformy Azure zawierającą obszar roboczy:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    To polecenie `.azureml` tworzy podkatalog zawierający przykładowe pliki środowiska runconfig i conda. Zawiera również `config.json` plik, który jest używany do komunikowania się z obszarem roboczym usługi Azure Machine Learning.

    Aby uzyskać więcej informacji, zobacz [az ml folder attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

2. Aby uruchomić, użyj następującego polecenia. Korzystając z tego polecenia, określ nazwę pliku runconfig (tekst przed \*.runconfig, jeśli patrzysz na system plików) względem parametru -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Polecenie `az ml folder attach` utworzyło `.azureml` podkatalog, który zawiera dwa przykładowe pliki runconfig.
    >
    > Jeśli masz skrypt Języka Python, który tworzy obiekt konfiguracji uruchamiania programowo, można użyć [RunConfig.save(),](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) aby zapisać go jako plik runconfig.
    >
    > Aby uzyskać więcej przykładowych plików [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml)runconfig, zobacz .

    Aby uzyskać więcej informacji, zobacz [az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

### <a name="using-azure-machine-learning-studio"></a>Korzystanie ze studia usługi Azure Machine Learning

Aby rozpocząć przesyłanie potoku w projektancie (wersja zapoznawcza), należy wykonać następujące kroki:

1. Ustaw domyślny cel obliczeniowy dla potoku.

1. Wybierz **pozycję Uruchom** u góry kanwy potoku.

1. Wybierz eksperyment, aby pogrupować przebiegi potoku.

## <a name="monitor-the-status-of-a-run"></a>Monitorowanie stanu biegu

### <a name="using-the-sdk"></a>Używanie zestawu SDK

Uzyskaj stan uruchomienia za [`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) pomocą metody.

```python
print(notebook_run.get_status())
```

Aby uzyskać identyfikator uruchomienia, czas wykonywania i dodatkowe szczegóły [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) dotyczące uruchamiania, należy użyć metody.

```python
print(notebook_run.get_details())
```

Po zakończeniu biegu, użyj [`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) metody, aby oznaczyć go jako zakończone.

```python
notebook_run.complete()
print(notebook_run.get_status())
```

Jeśli używasz wzorca `with...as` projektu Języka Python, uruchom automatycznie oznaczy się jako ukończony, gdy uruchomienie jest poza zakresem. Nie trzeba ręcznie oznaczać przebiegu jako ukończonego.

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>Korzystanie z interfejsu wiersza polecenia

1. Aby wyświetlić listę przebiegów eksperymentu, użyj następującego polecenia. Zamień `experiment` nazwę eksperymentu:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    To polecenie zwraca dokument JSON, który zawiera informacje o przebiegach dla tego eksperymentu.

    Aby uzyskać więcej informacji, zobacz [listę eksperymentów az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

2. Aby wyświetlić informacje dotyczące określonego przebiegu, należy użyć następującego polecenia. Wymień `runid` identyfikator biegu:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    To polecenie zwraca dokument JSON, który zawiera informacje o przebiegu.

    Aby uzyskać więcej informacji, zobacz [az ml run show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).


### <a name="using-azure-machine-learning-studio"></a>Korzystanie ze studia usługi Azure Machine Learning

Aby wyświetlić liczbę aktywnych przebiegów eksperymentu w studiu.

1. Przejdź do sekcji **Eksperymenty..** 

1. Wybierz eksperyment.

    Na stronie eksperymentu widać liczbę aktywnych obiektów docelowych obliczeń i czas trwania każdego uruchomienia. 

1. Wybierz określony numer biegu.

1. Na karcie **Dzienniki** można znaleźć dzienniki diagnostyczne i dzienniki błędów dla przebiegu potoku.


## <a name="cancel-or-fail-runs"></a>Anulowanie lub niepowodzenie przebiegów

Jeśli zauważysz błąd lub jeśli twój bieg trwa zbyt długo, możesz anulować bieg.

### <a name="using-the-sdk"></a>Używanie zestawu SDK

Aby anulować uruchomienie przy użyciu sdk, należy użyć [`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) metody:

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

Jeśli przebieg zostanie zakończy, ale zawiera błąd (na przykład użyto niepoprawnego [`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) skryptu szkoleniowego), można użyć metody, aby oznaczyć go jako nie powiodło się.

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>Korzystanie z interfejsu wiersza polecenia

Aby anulować uruchomienie przy użyciu interfejsu wiersza polecenia, użyj następującego polecenia. Wymień `runid` identyfikator biegu

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Aby uzyskać więcej informacji, zobacz [az ml run cancel](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

### <a name="using-azure-machine-learning-studio"></a>Korzystanie ze studia usługi Azure Machine Learning

Aby anulować uruchomienie w studio, wykonując następujące kroki:

1. Przejdź do uruchomionego potoku w sekcji **Eksperymenty** lub **Potoki.** 

1. Wybierz numer uruchomienia potoku, który chcesz anulować.

1. Na pasku narzędzi wybierz pozycję **Anuluj**


## <a name="create-child-runs"></a>Tworzenie przebiegów podrzędnych

Tworzenie przebiegów podrzędnych w celu grupowania powiązanych przebiegów, takich jak różne iteracje dostrajania hiperparametry.

> [!NOTE]
> Przebiegi podrzędne można tworzyć tylko przy użyciu SDK.

W tym przykładzie `hello_with_children.py` kodu używa skryptu do utworzenia partii pięć podrzędnych uruchamia z w ramach przesłane uruchomić przy użyciu [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) metody:

```python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Gdy wyjdą poza zakres, przebiegi podrzędne są automatycznie oznaczane jako ukończone.

Aby utworzyć wiele efektywnie [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) uruchamia wiele łóżek podrzędnych, należy użyć tej metody. Ponieważ każde utworzenie powoduje wywołanie sieciowe, tworzenie partii przebiegów jest bardziej wydajne niż tworzenie ich jeden po drugim.

### <a name="submit-child-runs"></a>Prześlij biegi podrzędne

Przebiegi podrzędne można również przesłać z biegu nadrzędnego. Dzięki temu można tworzyć hierarchie przebiegów nadrzędnych i podrzędnych. 

Możesz chcieć, aby twoje dziecko uruchamia, aby użyć innej konfiguracji uruchamiania niż uruchomienie nadrzędne. Na przykład można użyć mniej wydajnej konfiguracji opartej na procesorze CPU dla obiektu nadrzędnego, podczas korzystania z konfiguracji opartych na procesorze GPU dla dzieci. Innym wspólnym pragnieniem jest przekazanie każdemu dziecku różnych argumentów i danych. Aby dostosować przebieg podrzędny, `RunConfiguration` przekaż obiekt do `ScriptRunConfig` konstruktora podrzędnego. W tym przykładzie kodu, który `ScriptRunConfig` byłby częścią skryptu obiektu nadrzędnego:

- Tworzy `RunConfiguration` pobieranie nazwanego zasobu obliczeniowego`"gpu-compute"`
- Iteruje nad różnymi wartościami argumentów, które mają być przekazywane do obiektów podrzędnych `ScriptRunConfig`
- Tworzy i przesyła nowe uruchomienie podrzędne przy użyciu niestandardowego zasobu obliczeniowego i argumentu
- Blokuje, aż wszystkie elementy podrzędne będą kompletne

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig, RunConfiguration

run_config_for_aml_compute = RunConfiguration()
run_config_for_aml_compute.target = "gpu-compute"
run_config_for_aml_compute.environment.docker.enabled = True 

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], run_config = run_config_for_aml_compute)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

Aby utworzyć wiele uruchomień podrzędnych z identycznymi konfiguracjami, [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) argumentami i danymi wejściowymi, użyj tej metody. Ponieważ każde utworzenie powoduje wywołanie sieciowe, tworzenie partii przebiegów jest bardziej wydajne niż tworzenie ich jeden po drugim.

W ramach biegu podrzędnego można wyświetlić identyfikator biegu nadrzędnego:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Kwerenda podrzędna uruchamia

Aby zbadać podrzędne przebiegi określonego [`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) nadrzędnego, użyj tej metody. Argument ``recursive = True`` umożliwia wykonywanie zapytań o zagnieżdżone drzewo dzieci i wnuków.

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Oznaczanie i znajdowanie przebiegów

W usłudze Azure Machine Learning można używać właściwości i tagów, aby ułatwić organizowanie i wykonywanie zapytań o ważne informacje.

### <a name="add-properties-and-tags"></a>Dodawanie właściwości i znaczników

#### <a name="using-the-sdk"></a>Używanie zestawu SDK

Aby dodać metadane z wyszukuj do przebiegów, użyj tej [`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) metody. Na przykład następujący kod `"author"` dodaje właściwość do uruchomienia:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Właściwości są niezmienne, więc tworzą stały rekord do celów inspekcji. Poniższy przykład kodu powoduje błąd, ponieważ `"azureml-user"` firma `"author"` my już dodana jako wartość właściwości w poprzednim kodzie:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

W przeciwieństwie do właściwości tagi są modyfikowalne. Aby dodać możliwe do wyszukania i znaczące [`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) informacje dla konsumentów eksperymentu, użyj tej metody.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Można również dodać proste znaczniki ciągów. Gdy te znaczniki pojawiają się w słowniku tagów `None`jako klucze, mają wartość .

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>Korzystanie z interfejsu wiersza polecenia

> [!NOTE]
> Za pomocą interfejsu wiersza polecenia można dodawać lub aktualizować tylko znaczniki.

Aby dodać lub zaktualizować znacznik, użyj następującego polecenia:

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

Aby uzyskać więcej informacji, zobacz [az ml run update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).

### <a name="query-properties-and-tags"></a>Właściwości kwerendy i znaczniki

Można kwerendy uruchamia w ramach eksperymentu, aby zwrócić listę przebiegów, które pasują do określonych właściwości i tagów.

#### <a name="using-the-sdk"></a>Używanie zestawu SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>Korzystanie z interfejsu wiersza polecenia

Narzędzie cli platformy Azure obsługuje zapytania [JMESPath,](http://jmespath.org) które mogą służyć do filtrowania uruchomień na podstawie właściwości i tagów. Aby użyć kwerendy JMESPath z interfejsu wiersza polecenia platformy Azure, należy określić ją za pomocą parametru. `--query` Poniższe przykłady przedstawiają podstawowe zapytania przy użyciu właściwości i tagów:

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Aby uzyskać więcej informacji na temat wykonywania zapytań o wyniki interfejsu wiersza polecenia platformy Azure, zobacz [Kwerenda danych wyjściowych polecenia interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).

### <a name="using-azure-machine-learning-studio"></a>Korzystanie ze studia usługi Azure Machine Learning

1. Przejdź do sekcji **Potoki.**

1. Pasek wyszukiwania służy do filtrowania potoków przy użyciu znaczników, opisów, nazw eksperymentów i nazw osób przesyłanych.

## <a name="example-notebooks"></a>Przykładowe notesy

Następujące notesy demonstrują pojęcia w tym artykule:

* Aby dowiedzieć się więcej o interfejsach API rejestrowania, zobacz [notes interfejsu API rejestrowania](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb).

* Aby uzyskać więcej informacji na temat zarządzania przebiegami za pomocą zestawu SDK usługi Azure Machine Learning, zobacz [notes zarządzania przebiegami](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak rejestrować dane eksperymentów, zobacz [Rejestrowanie danych podczas przebiegów szkoleniowych.](how-to-track-experiments.md)
* Aby dowiedzieć się, jak monitorować zasoby i dzienniki z usługi Azure Machine Learning, zobacz [Monitorowanie usługi Azure Machine Learning](monitor-azure-machine-learning.md).