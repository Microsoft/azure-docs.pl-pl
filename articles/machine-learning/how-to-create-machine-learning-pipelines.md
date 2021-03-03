---
title: Tworzenie i uruchamianie potoków ML
titleSuffix: Azure Machine Learning
description: Tworzenie i uruchamianie potoków uczenia maszynowego w celu tworzenia i zarządzania przepływami pracy, które łączą fazy uczenia maszynowego (ML).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: nilsp
author: NilsPohlmann
ms.date: 03/02/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 75d241840ecfc8520989342d9def8186de922c0d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691862"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Tworzenie i uruchamianie potoków uczenia maszynowego za pomocą zestawu SDK Azure Machine Learning

Ten artykuł zawiera informacje na temat tworzenia i uruchamiania [potoków uczenia maszynowego](concept-ml-pipelines.md) przy użyciu [zestawu SDK Azure Machine Learning](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py). Użyj **potoków ml** , aby utworzyć przepływ pracy, który będzie łączyć różne fazy ml. Następnie opublikuj ten potok na potrzeby późniejszego dostępu lub udostępniania innym osobom. Śledź potoki ML, aby zobaczyć, jak model działa w świecie rzeczywistym i wykrywać dryfowanie danych. Potoki ML doskonale nadają się do scenariuszy wsadowych oceniania, przy użyciu różnych obliczeń, ponownej realizacji czynności zamiast ich uruchamiania i udostępniania przepływów pracy za pomocą innych elementów.

Ten artykuł nie jest samouczkiem. Aby uzyskać wskazówki dotyczące tworzenia pierwszego potoku, zobacz [Samouczek: Tworzenie potoku Azure Machine Learning na potrzeby oceniania partii](tutorial-pipeline-batch-scoring-classification.md) lub [Używanie zautomatyzowanej ml w potoku Azure Machine Learning w języku Python](how-to-use-automlstep-in-pipelines.md). 

Chociaż możesz użyć innego rodzaju potoku o nazwie [potoku platformy Azure](/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2fmachine-learning%2fservice%2fcontext%2fml-context&preserve-view=true&tabs=yaml&view=azure-devops) do automatyzacji wykonywania zadań w usłudze ml, ten typ potoku nie jest przechowywany w obszarze roboczym. [Porównaj te różne potoki](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Utworzone potoki ML są widoczne dla członków [obszaru roboczego](how-to-manage-workspace.md)Azure Machine Learning. 

Potoki ML są wykonywane na obiektach docelowych obliczeń (zobacz [co to są obiekty docelowe obliczeń w Azure Machine Learning](./concept-compute-target.md)). Potoki mogą odczytywać i zapisywać dane w i z obsługiwanych lokalizacji [usługi Azure Storage](../storage/index.yml) .

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz [obszar roboczy Azure Machine Learning](how-to-manage-workspace.md) , aby pomieścić wszystkie zasoby potoku.

* [Skonfiguruj środowisko programistyczne](how-to-configure-environment.md) , aby zainstalować zestaw SDK Azure Machine Learning, lub Użyj [wystąpienia obliczeniowego Azure Machine Learning](concept-compute-instance.md) z już zainstalowanym zestawem SDK.

Zacznij od dołączenia obszaru roboczego:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

## <a name="set-up-machine-learning-resources"></a>Konfigurowanie zasobów uczenia maszynowego

Utwórz zasoby wymagane do uruchomienia potoku ML:

* Skonfiguruj magazyn danych, który jest używany do uzyskiwania dostępu do wymaganych przez kroki potoku.

* Skonfiguruj obiekt w taki `Dataset` sposób, aby wskazywał trwałe dane, które znajdują się w, lub jest dostępny w magazynie danych. Skonfiguruj `OutputFileDatasetConfig` obiekt dla danych tymczasowych przesyłanych między krokami potoku. 

* Skonfiguruj [cele obliczeń](concept-azure-machine-learning-architecture.md#compute-targets) , na których będą uruchamiane kroki potoku.

### <a name="set-up-a-datastore"></a>Konfigurowanie magazynu danych

Magazyn danych przechowuje dane dla potoku do uzyskania dostępu. Każdy obszar roboczy ma domyślny magazyn danych. Możesz zarejestrować więcej magazynów danych. 

Podczas tworzenia obszaru roboczego [Azure Files](../storage/files/storage-files-introduction.md) i [Magazyn obiektów blob platformy Azure](../storage/blobs/storage-blobs-introduction.md) są dołączone do obszaru roboczego. W celu nawiązania połączenia z magazynem obiektów blob platformy Azure jest zarejestrowany domyślny magazyn danych. Aby dowiedzieć się więcej, zobacz [Decydowanie o tym, kiedy używać Azure Files, obiektów blob platformy Azure lub dysków platformy Azure](../storage/common/storage-introduction.md). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Kroki zwykle wykorzystują dane i generują dane wyjściowe. Krok może tworzyć dane, takie jak model, katalog z modelem i zależnymi plikami lub dane tymczasowe. Te dane są następnie dostępne dla innych kroków w dalszej części potoku. Aby dowiedzieć się więcej na temat łączenia potoku z danymi, zobacz artykuły, [jak uzyskać dostęp do danych](how-to-access-data.md) i [jak rejestrować zbiory](how-to-create-register-datasets.md). 

### <a name="configure-data-with-dataset-and-outputfiledatasetconfig-objects"></a>Konfigurowanie danych za `Dataset` pomocą `OutputFileDatasetConfig` obiektów i

Preferowanym sposobem zapewnienia danych do potoku jest obiekt [DataSet](/python/api/azureml-core/azureml.core.dataset.Dataset) . `Dataset`Obiekt wskazuje dane, które znajdują się w lub są dostępne z magazynu danych lub w adresie URL sieci Web. `Dataset`Klasa jest abstrakcyjna, dlatego utworzysz wystąpienie `FileDataset` (odwołujące się do jednego lub kilku plików) lub `TabularDataset` utworzone przez z jednego lub kilku plików z rozdzielonymi kolumnami danych.

Tworzysz `Dataset` metody using, takie jak [from_files](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-files-path--validate-true-) lub [from_delimited_files](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-).

```python
from azureml.core import Dataset

my_dataset = Dataset.File.from_files([(def_blob_store, 'train-images/')])
```

Dane pośrednie (lub dane wyjściowe kroku) są reprezentowane przez obiekt [OutputFileDatasetConfig](/python/api/azureml-pipeline-core/azureml.data.output_dataset_config.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) . `output_data1` jest tworzony jako dane wyjściowe kroku. Opcjonalnie te dane mogą być rejestrowane jako zestaw danych przez wywołanie `register_on_complete` . Jeśli utworzysz `OutputFileDatasetConfig` w jednym kroku i użyjesz go jako dane wejściowe do innego kroku, taka zależność między etapami tworzy niejawną kolejność wykonywania w potoku.

`OutputFileDatasetConfig` obiekty zwracają katalog, a domyślnie zapisuje dane wyjściowe do domyślnego magazynu danych obszaru roboczego.

```python
from azureml.data import OutputFileDatasetConfig

output_data1 = OutputFileDatasetConfig(destination = (datastore, 'outputdataset/{run-id}'))
output_data_dataset = output_data1.register_on_complete(name = 'prepared_output_data')

```

> [!IMPORTANT]
> Dane pośrednie przechowywane przy użyciu `OutputFileDatasetConfig` nie są automatycznie usuwane przez platformę Azure.
> Należy programowo usunąć dane pośrednie na końcu uruchomienia potoku, użyć magazynu danych z krótkimi zasadami przechowywania danych lub regularnie czyścić ręcznie.

> [!TIP]
> Przekazuj tylko te pliki, które są istotne dla danego zadania. Wszelkie zmiany w plikach w katalogu danych będą widoczne jako powód ponownego uruchomienia tego kroku przy następnym uruchomieniu potoku, nawet jeśli zostanie podane ponowne użycie. 

## <a name="set-up-a-compute-target"></a>Konfigurowanie celu obliczeń


W Azure Machine Learning termin __obliczeniowy__ (lub __element docelowy obliczeń__) odnosi się do maszyn lub klastrów wykonujących kroki obliczeniowe w potoku uczenia maszynowego.   Zobacz [elementy docelowe obliczeń dla szkolenia modelu](concept-compute-target.md#train) , aby uzyskać pełną listę elementów docelowych obliczeń i [utworzyć cele obliczeniowe](how-to-create-attach-compute-studio.md) na potrzeby tworzenia i dołączania ich do obszaru roboczego.   Proces tworzenia i dołączania obiektu docelowego obliczeń jest taki sam, jak w przypadku szkolenia modelu lub uruchamiania kroku potoku. Po utworzeniu i dołączeniu obiektu docelowego obliczeń Użyj `ComputeTarget` obiektu w [kroku potoku](#steps).

> [!IMPORTANT]
> Wykonywanie operacji zarządzania na docelowych obiektach obliczeniowych nie jest obsługiwane z poziomu zadań zdalnych. Potoki uczenia maszynowego są przesyłane jako zadania zdalne, dlatego nie należy używać operacji zarządzania na docelowych obiektach obliczeniowych z wnętrza potoku.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning obliczeń

Można utworzyć Azure Machine Learning obliczeń do uruchamiania kroków. Kod dla innych obiektów docelowych obliczeń jest podobny, z nieco różnymi parametrami, w zależności od typu. 

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

## <a name="configure-the-training-runs-environment"></a>Konfigurowanie środowiska pracy szkoleniowej

Następnym krokiem jest upewnienie się, że uruchomienie szkolenia zdalnego ma wszystkie zależności wymagane przez kroki szkoleniowe. Zależności i kontekst środowiska uruchomieniowego są ustawiane przez utworzenie i skonfigurowanie `RunConfiguration` obiektu. 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import Environment 

aml_run_config = RunConfiguration()
# `compute_target` as defined in "Azure Machine Learning compute" section above
aml_run_config.target = compute_target

USE_CURATED_ENV = True
if USE_CURATED_ENV :
    curated_environment = Environment.get(workspace=ws, name="AzureML-Tutorial")
    aml_run_config.environment = curated_environment
else:
    aml_run_config.environment.python.user_managed_dependencies = False
    
    # Add some packages relied on by data prep step
    aml_run_config.environment.python.conda_dependencies = CondaDependencies.create(
        conda_packages=['pandas','scikit-learn'], 
        pip_packages=['azureml-sdk', 'azureml-dataprep[fuse,pandas]'], 
        pin_sdk_version=False)
```

W powyższym kodzie przedstawiono dwie opcje obsługi zależności. Jak przedstawiono w programie `USE_CURATED_ENV = True` , konfiguracja jest oparta na środowisku nadzorowanym. Środowiska nadzorowane są "prebaked" z typowymi bibliotekami zależnymi i mogą być szybsze do przełączenia do trybu online. Środowiska nadzorowane zawierają wstępnie skompilowane obrazy platformy Docker w [programie Microsoft Container Registry](https://hub.docker.com/publishers/microsoftowner). Aby uzyskać więcej informacji, zobacz [Azure Machine Learning środowisku nadzorowanym](resource-curated-environments.md).

Ścieżka wykonana, jeśli zmieni `USE_CURATED_ENV` się, aby `False` wyświetlić wzorzec jawnego ustawiania zależności. W tym scenariuszu nowy niestandardowy obraz platformy Docker zostanie utworzony i zarejestrowany w Azure Container Registry w ramach grupy zasobów (zobacz [wprowadzenie do prywatnych rejestrów kontenerów platformy Docker na platformie Azure](../container-registry/container-registry-intro.md)). Kompilowanie i rejestrowanie tego obrazu może potrwać kilka minut.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>Konstruowanie kroków potoku

Po utworzeniu zasobu i środowiska obliczeniowego można przystąpić do definiowania kroków potoku. Istnieje wiele wbudowanych kroków dostępnych za pośrednictwem zestawu SDK Azure Machine Learning, jak widać w [dokumentacji referencyjnej `azureml.pipeline.steps` pakietu](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py). Najbardziej elastyczną klasą jest [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?preserve-view=true&view=azure-ml-py), która uruchamia skrypt języka Python.

```python
from azureml.pipeline.steps import PythonScriptStep
dataprep_source_dir = "./dataprep_src"
entry_point = "prepare.py"
# `my_dataset` as defined above
ds_input = my_dataset.as_named_input('input1')

# `output_data1`, `compute_target`, `aml_run_config` as defined above
data_prep_step = PythonScriptStep(
    script_name=entry_point,
    source_directory=dataprep_source_dir,
    arguments=["--input", ds_input.as_download(), "--output", output_data1],
    compute_target=compute_target,
    runconfig=aml_run_config,
    allow_reuse=True
)
```

Powyższy kod przedstawia typowy krok początkowy potoku. Kod przygotowywania danych znajduje się w podkatalogu (w tym przykładzie `"prepare.py"` w katalogu `"./dataprep.src"` ). W ramach procesu tworzenia potoku ten katalog jest spakowany i przekazywany do programu, `compute_target` a krok uruchamia skrypt określony jako wartość dla `script_name` .

`arguments`Wartości określają dane wejściowe i wyjściowe kroku. W powyższym przykładzie dane linii bazowej są `my_dataset` zestawem danych. Odpowiednie dane zostaną pobrane do zasobu obliczeniowego, ponieważ kod określa go jako `as_download()` . Skrypt `prepare.py` wykonuje wszelkie zadania przekształcania danych, które są odpowiednie do zadania w ręku i wyprowadza dane do `output_data1` typu `OutputFileDatasetConfig` . Aby uzyskać więcej informacji, zobacz temat [przeniesienie danych do i między etapami potoku (Python) i między nimi](how-to-move-data-in-out-of-pipelines.md). Krok zostanie uruchomiony na maszynie zdefiniowanej przez `compute_target` program przy użyciu konfiguracji `aml_run_config` . 

Ponowne użycie poprzednich wyników ( `allow_reuse` ) jest kluczem w przypadku używania potoków w środowisku współpracy, ponieważ wyeliminowanie niepotrzebnych ponownych prób zapewnia elastyczność. Ponowne użycie jest zachowaniem domyślnym, gdy script_name, dane wejściowe i parametry kroku pozostają takie same. Gdy ponowne użycie jest dozwolone, wyniki z poprzedniego przebiegu są natychmiast wysyłane do następnego kroku. Jeśli `allow_reuse` jest ustawiona na `False` , nowy przebieg będzie zawsze generowany dla tego kroku podczas wykonywania potoku.

Istnieje możliwość utworzenia potoku z jednym krokiem, ale prawie zawsze będzie można podzielić cały proces do kilku kroków. Na przykład można wykonać kroki przygotowywania danych, szkoleń, porównywania modeli i wdrażania. Przykładowo może to stanowić, że po `data_prep_step` powyższym przewidzianym powyżej następnym krokiem może być szkolenie:

```python
train_source_dir = "./train_src"
train_entry_point = "train.py"

training_results = OutputFileDatasetConfig(name = "training_results",
    destination = def_blob_store)

    
train_step = PythonScriptStep(
    script_name=train_entry_point,
    source_directory=train_source_dir,
    arguments=["--prepped_data", output_data1.as_input(), "--training_results", training_results],
    compute_target=compute_target,
    runconfig=aml_run_config,
    allow_reuse=True
)
```

Powyższy kod jest podobny do kodu w kroku przygotowywania danych. Kod szkoleniowy znajduje się w katalogu innym niż kod przygotowywania danych. `OutputFileDatasetConfig`Dane wyjściowe kroku przygotowywania danych `output_data1` są używane jako _dane wejściowe_ kroku szkolenia. Nowy `OutputFileDatasetConfig` obiekt `training_results` jest tworzony w celu przechowywania wyników dla kolejnego kroku porównania lub wdrożenia. 

Aby zapoznać się z innymi przykładami kodu, zobacz jak [utworzyć dwa etapowe potoku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb) i [jak zapisywać dane z powrotem do magazynów danych po zakończeniu wykonywania](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/scriptrun-with-data-input-output/how-to-use-scriptrun.ipynb).

Po zdefiniowaniu kroków można skompilować potok za pomocą niektórych lub wszystkich tych kroków.

> [!NOTE]
> Podczas definiowania kroków lub kompilowania potoku nie są przekazywane żadne pliki ani dane do Azure Machine Learning. Pliki są przekazywane po wywołaniu [eksperymentu. Submit ()](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-).

```python
# list of steps to run (`compare_step` definition not shown)
compare_models = [data_prep_step, train_step, compare_step]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compare_models])
```

### <a name="use-a-dataset"></a>Korzystanie z zestawu danych 

Zestawy danych utworzone z usługi Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database i Azure Database for PostgreSQL mogą być używane jako dane wejściowe do dowolnego etapu potoku. Możesz zapisać dane wyjściowe w [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?preserve-view=true&view=azure-ml-py), [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?preserve-view=true&view=azure-ml-py)lub, jeśli chcesz zapisać danych do określonego magazynu datastore, użyj [OutputFileDatasetConfig](/python/api/azureml-pipeline-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py). 

> [!IMPORTANT]
> Zapisywanie danych wyjściowych z powrotem do magazynu danych za pomocą `OutputFileDatasetConfig` jest obsługiwane tylko w przypadku obiektów blob platformy Azure, udziałów plików platformy Azure, ADLS generacji 1 i generacji 2 danych. 

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Następnie można pobrać zestaw danych w potoku przy użyciu słownika [Run.input_datasets](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=trueinput-datasets) .

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

Wiersz `Run.get_context()` jest wyróżniony. Ta funkcja pobiera `Run` reprezentującą bieżące przebiegi eksperymentalne. W powyższym przykładzie używamy go do pobrania zarejestrowanego zestawu danych. Innym typowym zastosowaniem `Run` obiektu jest pobranie samego eksperymentu i obszaru roboczego, w którym znajduje się eksperyment: 

```python
# Within a PythonScriptStep

ws = Run.get_context().experiment.workspace
```

Aby uzyskać bardziej szczegółowe informacje, w tym alternatywne sposoby przekazywania danych i uzyskiwania do nich dostępu, zobacz [przeniesienie danych do i między etapami potoku (Python)](how-to-move-data-in-out-of-pipelines.md).

## <a name="caching--reuse"></a>Buforowanie & ponownie używane  

Aby zoptymalizować i dostosować zachowanie potoków, można wykonać kilka czynności związanych z buforowaniem i wielokrotnym użyciem. Można na przykład wybrać następujące opcje:
+ Wyłącz **domyślne ponowne użycie kroku Uruchom dane wyjściowe** przez ustawienie `allow_reuse=False` podczas [definiowania kroku](/python/api/azureml-pipeline-steps/?preserve-view=true&view=azure-ml-py). Ponowne użycie jest kluczem przy użyciu potoków w środowisku współpracy, ponieważ wyeliminowanie niepotrzebnych uruchomień zapewnia elastyczność. Można jednak zrezygnować z ponownego użycia.
+ **Wymuś ponowne wygenerowanie danych wyjściowych dla wszystkich kroków w przebiegu** z `pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Domyślnie `allow_reuse` dla kroków jest włączone, a `source_directory` określone w definicji kroku są skrótem. Tak więc, jeśli skrypt dla danego kroku pozostaje taki sam ( `script_name` , dane wejściowe i parametry), a żadne inne elementy w programie ` source_directory` nie zmieniły się, dane wyjściowe przebiegu poprzedniego kroku są ponownie używane, zadanie nie zostanie przesłane do obliczenia, a wyniki z poprzedniego uruchomienia są natychmiast dostępne w następnym kroku.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="submit-the-pipeline"></a>Prześlij potok

Gdy przesyłasz potok, Azure Machine Learning sprawdza zależności dla każdego kroku i przekazuje migawkę podanego katalogu źródłowego. Jeśli katalog źródłowy nie zostanie określony, zostanie przekazany bieżący katalog lokalny. Migawka jest również przechowywana w ramach eksperymentu w obszarze roboczym.

> [!IMPORTANT]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
>
> Aby uzyskać więcej informacji, zobacz [migawki](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Podczas pierwszego uruchomienia potoku Azure Machine Learning:

* Pobiera migawkę projektu do obiektu docelowego obliczeń z magazynu obiektów BLOB skojarzonego z obszarem roboczym.
* Kompiluje obraz platformy Docker odpowiadający każdemu krokowi w potoku.
* Pobiera obraz platformy Docker dla każdego kroku do obiektu docelowego obliczeń z rejestru kontenerów.
* Konfiguruje dostęp `Dataset` do `OutputFileDatasetConfig` obiektów i. W przypadku `as_mount()` trybu dostępu Odmów jest używany do zapewnienia dostępu wirtualnego. Jeśli instalacja nie jest obsługiwana lub jeśli użytkownik określił dostęp jako `as_upload()` , dane są kopiowane do elementu docelowego obliczeń.

* Uruchamia krok w elemencie docelowym obliczeń określonym w definicji kroku. 
* Tworzy artefakty, takie jak dzienniki, stdout i stderr, metryki i dane wyjściowe określone przez krok. Te artefakty są następnie przekazywane i przechowywane w domyślnym magazynie danych użytkownika.

![Diagram uruchamiania eksperymentu jako potoku](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Aby uzyskać więcej informacji, zobacz informacje o [klasie eksperymentów](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py) .

## <a name="use-pipeline-parameters-for-arguments-that-change-at-inference-time"></a>Użyj parametrów potoku dla argumentów, które zmieniają się w czasie wnioskowania

Czasami argumenty poszczególnych kroków w potoku odnoszą się do okresu opracowywania i uczenia: takich jak szybkość uczenia i czas pędu, lub ścieżki do plików danych lub konfiguracji. Po wdrożeniu modelu, należy wykonać dynamiczne przekazywanie argumentów, na których jest inferencing (to oznacza, że zapytanie skompilowano model do odpowiedzi!). Należy wykonać te typy argumentów parametrów potoku. Aby to zrobić w języku Python, użyj `azureml.pipeline.core.PipelineParameter` klasy, jak pokazano w poniższym fragmencie kodu:

```python
from azureml.pipeline.core import PipelineParameter

pipeline_param = PipelineParameter(name="pipeline_arg", default_value="default_val")
train_step = PythonScriptStep(script_name="train.py",
                            arguments=["--param1", pipeline_param],
                            target=compute_target,
                            source_directory=project_folder)
```

### <a name="how-python-environments-work-with-pipeline-parameters"></a>Jak działają środowiska Python z parametrami potoku

Jak opisano wcześniej w temacie [Konfigurowanie środowiska szkolenia](#configure-the-training-runs-environment), stanu środowiska i zależności biblioteki języka Python, należy określić za pomocą `Environment` obiektu. Ogólnie rzecz biorąc, można określić istniejący element, `Environment` odwołując się do jego nazwy i, opcjonalnie, wersji:

```python
aml_run_config = RunConfiguration()
aml_run_config.environment.name = 'MyEnvironment'
aml_run_config.environment.version = '1.0'
```

Jeśli jednak zdecydujesz się używać `PipelineParameter` obiektów do dynamicznego ustawiania zmiennych w czasie wykonywania dla kroków potoku, nie możesz użyć tej techniki, aby odwoływać się do istniejącej `Environment` . Zamiast tego, jeśli chcesz używać `PipelineParameter` obiektów, musisz ustawić `environment` pole na `RunConfiguration` `Environment` obiekt. Jest odpowiedzialny za zagwarantowanie, że taki element `Environment` ma swoje zależności od poprawnego zestawu zewnętrznych języka Python.


## <a name="view-results-of-a-pipeline"></a>Wyświetlanie wyników potoku

Zobacz listę wszystkich potoków i ich szczegóły uruchamiania w programie Studio:

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com).

1. [Wyświetlanie obszaru roboczego](how-to-manage-workspace.md#view).

1. Po lewej stronie wybierz pozycję **potoki** , aby wyświetlić wszystkie uruchomienia potoków.
 ![Lista potoków uczenia maszynowego](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Wybierz konkretny potok, aby wyświetlić wyniki przebiegu.

### <a name="git-tracking-and-integration"></a>Śledzenie i integracja usługi git

Po rozpoczęciu szkolenia w przypadku, gdy katalog źródłowy jest lokalnym repozytorium git, informacje o repozytorium są przechowywane w historii uruchamiania. Aby uzyskać więcej informacji, zobacz Integracja z usługą [git dla Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="next-steps"></a>Następne kroki

- Aby udostępnić potok współpracownikom lub klientom, zobacz [Publikowanie potoków uczenia maszynowego](how-to-deploy-pipelines.md)
- Korzystaj z [tych notesów Jupyter w witrynie GitHub](https://aka.ms/aml-pipeline-readme) , aby dowiedzieć się więcej o potokach uczenia maszynowego
- Zapoznaj się z informacjami dotyczącymi zestawu SDK dla [potoków usługi Azure Core](/python/api/azureml-pipeline-core/?preserve-view=true&view=azure-ml-py) i pakietu z instrukcjami dotyczącymi [potoków usługi Azure](/python/api/azureml-pipeline-steps/?preserve-view=true&view=azure-ml-py)
- Zobacz [instrukcje](how-to-debug-pipelines.md) dotyczące debugowania i rozwiązywania problemów z potokami =
- Instrukcję uruchamiania notesów znajdziesz w artykule [Use Jupyter notebooks to explore this service](samples-notebooks.md) (Eksplorowanie tej usługi za pomocą notesów Jupyter).