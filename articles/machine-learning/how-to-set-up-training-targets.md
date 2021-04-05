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
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: f38fe7d847754247f8c1510527b3ffe026c20be5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518504"
---
# <a name="configure-and-submit-training-runs"></a>Konfigurowanie i przesyłanie przebiegów trenowania

W tym artykule dowiesz się, jak skonfigurować i przesłać Azure Machine Learning uruchomienia do uczenia modeli. Fragmenty kodu wyjaśniają kluczowe części konfiguracji i przedłożenie skryptu szkoleniowego.  Następnie skorzystaj z jednego z [przykładowych notesów](#notebooks) , aby znaleźć kompletne, kompleksowe Przykłady pracy.

W przypadku szkolenia często należy zacząć pracę na komputerze lokalnym, a następnie skalować w poziomie do klastra opartego na chmurze. Za pomocą Azure Machine Learning można uruchamiać skrypty na różnych obiektach docelowych obliczeń bez konieczności zmiany skryptu szkoleniowego.

Wystarczy zdefiniować środowisko dla każdego obiektu docelowego obliczeń w ramach **konfiguracji uruchamiania skryptu**.  Następnie, gdy chcesz uruchomić eksperyment szkoleniowy w innym miejscu docelowym obliczeń, określ konfigurację uruchamiania dla tego obliczenia.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj
* [Zestaw Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/install) (>= 1.13.0)
* [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md),`ws`
* Obiekt docelowy obliczeń, `my_compute_target` .  [Tworzenie obiektu docelowego obliczeń](how-to-create-attach-compute-studio.md) 

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Co to jest konfiguracja uruchomienia skryptu?
[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) służy do konfigurowania informacji niezbędnych do przesłania szkolenia w ramach eksperymentu.

Możesz przesłać eksperyment szkoleniowy z obiektem ScriptRunConfig.  Ten obiekt obejmuje:

* **source_directory**: Katalog źródłowy zawierający skrypt szkoleniowy
* **skrypt**: skrypt szkoleniowy do uruchomienia
* **compute_target**: element docelowy obliczeń do uruchomienia
* **środowisko**: środowisko, które ma być używane podczas uruchamiania skryptu
* i kilka dodatkowych konfigurowalnych opcji (zobacz [dokumentację referencyjną](/python/api/azureml-core/azureml.core.scriptrunconfig) , aby uzyskać więcej informacji)

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

>[!Note]
>Azure Databricks nie jest obsługiwane jako element docelowy obliczeń dla szkolenia modeli. Azure Databricks służy do przygotowywania i wdrażania danych. 

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

Jeśli obiektem docelowym obliczeń jest komputer **lokalny**, użytkownik jest odpowiedzialny za zapewnienie, że wszystkie niezbędne pakiety są dostępne w środowisku języka Python, w którym działa skrypt.  Użyj, `python.user_managed_dependencies` Aby użyć bieżącego środowiska Python (lub języka Python w określonej ścieżce).

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
Jeśli chcesz uruchomić zadanie szkolenia rozproszonego, podaj do parametru konfigurację rozproszoną dla określonego zadania **`distributed_job_config`** . Obsługiwane typy konfiguracji to [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration), [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration)i [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration). 

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
> **Foldery specjalne** Dwa foldery, dane *wyjściowe* i *dzienniki*, otrzymują specjalne traktowanie według Azure Machine Learning. Podczas szkolenia podczas pisania plików do folderów o nazwie dane *wyjściowe* i *dzienniki* , które są względne dla katalogu głównego ( `./outputs` i `./logs` odpowiednio), pliki zostaną automatycznie przekazane do historii uruchamiania, dzięki czemu będziesz mieć do nich dostęp po zakończeniu przebiegu.
>
> Aby tworzyć artefakty podczas szkoleń (takich jak pliki modelu, punkty kontrolne, pliki danych lub rysunki), Zapisz je w `./outputs` folderze.
>
> Podobnie można napisać wszystkie dzienniki z poziomu szkolenia szkoleniowego do `./logs` folderu. Aby korzystać z [integracji TensorBoard](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) Azure Machine Learning upewnij się, że piszesz dzienniki TensorBoard w tym folderze. Gdy przebieg jest w toku, będzie można uruchamiać TensorBoard i przesyłać strumieniowo te dzienniki.  Później będzie można przywrócić dzienniki z dowolnego poprzedniego przebiegu.
>
> Na przykład, aby pobrać plik zapisany *w folderze* Outputs na komputer lokalny po uruchomieniu szkolenia zdalnego: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Śledzenie i integracja usługi git

Po rozpoczęciu szkolenia w przypadku, gdy katalog źródłowy jest lokalnym repozytorium git, informacje o repozytorium są przechowywane w historii uruchamiania. Aby uzyskać więcej informacji, zobacz Integracja z usługą [git dla Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a><a name="notebooks"></a>Przykłady notesu

Zobacz te notesy, aby zapoznać się z przykładami konfigurowania przebiegów w różnych scenariuszach szkoleniowych:
* [Szkolenie dotyczące różnych obiektów docelowych obliczeń](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Szkolenia przy użyciu platform ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [Samouczki/IMG-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* **Przebieg kończy się `jwt.exceptions.DecodeError` niepowodzeniem z**: dokładny komunikat o błędzie: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` . 
    
    Rozważ uaktualnienie do najnowszej wersji platformy Azure Core: `pip install -U azureml-core` .
    
    Jeśli używasz tego problemu w przypadku uruchomień lokalnych, sprawdź wersję programu PyJWT zainstalowaną w środowisku, w którym uruchamiasz program. Obsługiwane wersje programu PyJWT są < 2.0.0. Odinstaluj PyJWT ze środowiska, jeśli wersja jest >= 2.0.0. Możesz sprawdzić wersję programu PyJWT, odinstalować i zainstalować odpowiednią wersję w następujący sposób:
    1. Uruchom powłokę poleceń, Aktywuj środowisko Conda, w którym zainstalowano usługę Azure Core.
    2. Wprowadź `pip freeze` i Wyszukaj `PyJWT` , jeśli znaleziono, wyświetlana wersja powinna być < 2.0.0
    3. Jeśli wyświetlana wersja nie jest obsługiwaną wersją, `pip uninstall PyJWT` w powłoce poleceń i wprowadź y w celu potwierdzenia.
    4. Zainstaluj przy użyciu polecenia `pip install 'PyJWT<2.0.0'`
    
    W przypadku przesyłania środowiska utworzonego przez użytkownika z uruchomionym programem należy rozważyć użycie najnowszej wersji rdzenia Azure w tym środowisku. Wersje >= 1.18.0 z platformy Azure-Core już PyJWT < 2.0.0. Jeśli potrzebujesz użyć wersji programu Azure Core < 1.18.0 w przesłanym środowisku, upewnij się, że określono PyJWT < 2.0.0 w zależnościach PIP.


 * **ModuleErrors (Brak modułu o nazwie)**: Jeśli korzystasz z programu ModuleErrors podczas przesyłania eksperymentów na platformie Azure ml, skrypt szkoleniowy oczekuje, że pakiet zostanie zainstalowany, ale nie zostanie dodany. Po podaniu nazwy pakietu usługa Azure ML instaluje pakiet w środowisku używanym do pracy z szkoleniiem.

    Jeśli używasz szacowania do przesyłania eksperymentów, możesz określić nazwę pakietu za pośrednictwem `pip_packages` lub `conda_packages` parametru w szacowania, na podstawie którego źródła chcesz zainstalować pakiet. Można również określić plik yml ze wszystkimi zależnościami przy użyciu `conda_dependencies_file` lub wyświetlić wszystkie wymagania dotyczące PIP w pliku txt przy użyciu `pip_requirements_file` parametru. Jeśli masz własny obiekt środowiska usługi Azure ML, który chcesz przesłonić domyślny obraz używany przez szacowania, możesz określić to środowisko za pośrednictwem `environment` parametru konstruktora szacowania.
    
    Platforma Azure ML obsługuje obrazy platformy Docker i ich zawartość można zobaczyć w [kontenerach usługi Azure](https://github.com/Azure/AzureML-Containers).
    Zależności specyficzne dla platformy są wymienione w odpowiedniej dokumentacji platformy:
    *  [Chainer](/python/api/azureml-train-core/azureml.train.dnn.chainer#remarks)
    * [PyTorch](/python/api/azureml-train-core/azureml.train.dnn.pytorch#remarks)
    * [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow#remarks)
    *  [Skryptu sklearn](/python/api/azureml-train-core/azureml.train.sklearn.sklearn#remarks)
    
    > [!Note]
    > Jeśli uważasz, że określony pakiet jest wystarczająco powszechny do dodania do obrazów i środowisk konserwowanych platformy Azure, zgłoś problem w usłudze GitHub w [kontenerach usługi Azure](https://github.com/Azure/AzureML-Containers)ml. 
 
* **NameError (nazwa niezdefiniowana), AttributeError (obiekt nie ma atrybutu)**: ten wyjątek powinien pochodzić ze skryptów szkoleniowych. Można przyjrzeć się plikom dziennika z Azure Portal, aby uzyskać więcej informacji na temat konkretnej nazwy niezdefiniowanej lub błędu atrybutu. Korzystając z zestawu SDK, można `run.get_details()` sprawdzić komunikat o błędzie. Spowoduje to wyświetlenie listy wszystkich plików dziennika wygenerowanych dla danego przebiegu. Upewnij się, że zapoznaj się z skryptem szkoleniowym i usuń błąd przed ponownym przesłaniem uruchomienia. 


* **Usuwanie przebiegu lub eksperymentu**: eksperymenty można archiwizować przy użyciu metody [eksperyment. Archive](/python/api/azureml-core/azureml.core.experiment%28class%29#archive--) lub w widoku karty eksperymenty w programie Azure Machine Learning Studio Client za pośrednictwem przycisku "Archiwizuj eksperyment". Ta akcja ukrywa eksperyment z zapytań i widoków list, ale nie usuwa go.

    Trwałe usuwanie pojedynczych eksperymentów lub przebiegów obecnie nie jest obsługiwane. Aby uzyskać więcej informacji na temat usuwania zasobów obszaru roboczego, zobacz [Eksportowanie lub usuwanie danych obszaru roboczego usługi Machine Learning](how-to-export-delete-data.md).

* **Dokument metryki jest zbyt duży**: Azure Machine Learning ma wewnętrzne limity rozmiaru obiektów metryk, które mogą być rejestrowane jednocześnie z poziomu przebiegu szkoleniowego. Jeśli w trakcie rejestrowania metryki z wartościami listy wystąpi błąd „Dokument metryki jest za duży”, spróbuj podzielić listę na mniejsze fragmenty, na przykład:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Wewnętrznie usługa Azure ML łączy bloki z tą samą nazwą metryki w listę ciągłą.

* **Rozpoczęcie wykonywania obliczeń trwa długo**: obrazy platformy Docker dla obiektów docelowych obliczeń są ładowane z Azure Container Registry (ACR). Domyślnie Azure Machine Learning tworzy ACR, który korzysta z warstwy usługi *podstawowa* . Zmiana ACR dla obszaru roboczego na warstwę Standardowa lub Premium może skrócić czas potrzebny do kompilowania i ładowania obrazów. Aby uzyskać więcej informacji, zobacz [Azure Container Registry warstwy usług](../container-registry/container-registry-skus.md).

## <a name="next-steps"></a>Następne kroki

* [Samouczek: uczenie modelu](tutorial-train-models-with-aml.md) używa zarządzanego obiektu docelowego obliczeń do uczenia modelu.
* Zobacz, jak szkolić modele przy użyciu określonych platform ML, takich jak [Scikit-](how-to-train-scikit-learn.md)Learning, [TensorFlow](how-to-train-tensorflow.md)i [PyTorch](how-to-train-pytorch.md).
* Dowiedz się, jak [efektywnie dostrajać parametry](how-to-tune-hyperparameters.md) , aby tworzyć lepsze modele.
* Po uzyskaniu przeszkolonego modelu Dowiedz się, [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).
* Wyświetl odwołanie do zestawu SDK [klasy ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) .
* [Używanie Azure Machine Learning z sieciami wirtualnymi platformy Azure](./how-to-network-security-overview.md)
