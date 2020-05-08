---
title: Twórz, uruchamiaj, & Śledź potoki ML
titleSuffix: Azure Machine Learning
description: Utwórz i uruchom potok uczenia maszynowego za pomocą zestawu Azure Machine Learning SDK dla języka Python. Przy użyciu potoków ML można tworzyć przepływy pracy, które łączą fazy uczenia maszynowego (ML) i zarządzać nimi. Te fazy obejmują Przygotowywanie danych, szkolenia modeli, wdrażanie modeli i wnioskowanie/ocenianie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: 006a2f5775fc053feda1efd568660f29c81fe599
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872217"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Tworzenie i uruchamianie potoków uczenia maszynowego za pomocą zestawu SDK Azure Machine Learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ten artykuł zawiera informacje na temat tworzenia, publikowania, uruchamiania i śledzenia [potoku uczenia maszynowego](concept-ml-pipelines.md) przy użyciu [zestawu SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Użyj **potoków ml** , aby utworzyć przepływ pracy, który jest połączony z różnymi etapami ml, a następnie opublikuj ten potok w obszarze roboczym Azure Machine Learning, aby uzyskać dostęp do nich później lub udostępniać innym osobom.  Potoki ML doskonale nadają się do scenariuszy wsadowych oceniania, przy użyciu różnych obliczeń, ponownej realizacji czynności zamiast uruchamiania ich, a także udostępniania przepływów pracy ML innym osobom.

Chociaż możesz użyć innego rodzaju potoku o nazwie [potoku platformy Azure](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) do automatyzacji wykonywania zadań w usłudze ml, ten typ potoku nigdy nie jest przechowywany w obszarze roboczym. [Porównaj te różne potoki](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Każda faza potoku ML, taka jak przygotowanie danych i szkolenia modeli, może obejmować jeden lub więcej kroków.

Utworzone potoki ML są widoczne dla członków [obszaru roboczego](how-to-manage-workspace.md)Azure Machine Learning. 

Potoki ML wykorzystują zdalne cele obliczeniowe do obliczeń i przechowywania danych pośrednich i końcowych skojarzonych z tym potokiem. Mogą odczytywać i zapisywać dane w i z obsługiwanych lokalizacji [usługi Azure Storage](https://docs.microsoft.com/azure/storage/) .

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz [obszar roboczy Azure Machine Learning](how-to-manage-workspace.md) , aby pomieścić wszystkie zasoby potoku.

* [Skonfiguruj środowisko programistyczne](how-to-configure-environment.md) , aby zainstalować zestaw SDK Azure Machine Learning, lub Użyj [wystąpienia obliczeniowego Azure Machine Learning (wersja zapoznawcza)](concept-compute-instance.md) z już zainstalowanym zestawem SDK.

Zacznij od dołączenia obszaru roboczego:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

## <a name="set-up-machine-learning-resources"></a>Konfigurowanie zasobów uczenia maszynowego

Utwórz zasoby wymagane do uruchomienia potoku ML:

* Skonfiguruj magazyn danych, który jest używany do uzyskiwania dostępu do wymaganych przez kroki potoku.

* Skonfiguruj `Dataset` obiekt w taki sposób, aby wskazywał trwałe dane, które znajdują się w, lub jest dostępny w magazynie danych. Skonfiguruj `PipelineData` obiekt dla danych tymczasowych przesyłanych między krokami potoku. 

* Skonfiguruj [cele obliczeń](concept-azure-machine-learning-architecture.md#compute-targets) , na których będą uruchamiane kroki potoku.

### <a name="set-up-a-datastore"></a>Konfigurowanie magazynu danych

Magazyn danych przechowuje dane dla potoku do uzyskania dostępu. Każdy obszar roboczy ma domyślny magazyn danych. Możesz zarejestrować dodatkowe magazyny danych. 

Podczas tworzenia obszaru roboczego [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) i [Magazyn obiektów blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) są dołączone do obszaru roboczego. W celu nawiązania połączenia z magazynem obiektów blob platformy Azure jest zarejestrowany domyślny magazyn danych. Aby dowiedzieć się więcej, zobacz [Decydowanie o tym, kiedy używać Azure Files, obiektów blob platformy Azure lub dysków platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Przekaż pliki danych lub katalogi do magazynu datastore, aby były dostępne z potoków. Ten przykład używa magazynu obiektów BLOB jako magazynu danych:

```python
def_blob_store.upload_files(
    ["iris.csv"],
    target_path="train-dataset",
    overwrite=True)
```

Potok składa się z jednego lub kilku kroków. Krok to jednostka uruchamiana na obiekcie docelowym obliczeń. Kroki mogą zużywać źródła danych i generować "pośrednie" dane. Krok może tworzyć dane, takie jak model, katalog z modelem i zależnymi plikami lub dane tymczasowe. Te dane są następnie dostępne dla innych kroków w dalszej części potoku.

Aby dowiedzieć się więcej na temat łączenia potoku z danymi, zobacz artykuły, [jak uzyskać dostęp do danych](how-to-access-data.md) i [jak rejestrować zbiory](how-to-create-register-datasets.md). 

### <a name="configure-data-using-dataset-and-pipelinedata-objects"></a>Konfigurowanie danych przy `Dataset` użyciu `PipelineData` obiektów i

Właśnie utworzono źródło danych, do którego można się odwoływać w potoku jako dane wejściowe do kroku. Preferowanym sposobem zapewnienia danych do potoku jest obiekt [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.Dataset) . `Dataset` Obiekt wskazuje dane, które znajdują się w lub są dostępne z magazynu danych lub w adresie URL sieci Web. `Dataset` Klasa jest abstrakcyjna, dlatego utworzysz wystąpienie `FileDataset` (odwołujące się do jednego lub kilku plików) lub `TabularDataset` utworzone przez z jednego lub kilku plików z rozdzielonymi kolumnami danych.

`Dataset`obiekty obsługują przechowywanie wersji, diffe i statystyki podsumowujące. `Dataset`s są opóźnieniem oceniane (na przykład generatory języka Python) i wydajnym podzbiorem ich przez dzielenie lub filtrowanie. 

Tworzysz metody `Dataset` using, takie jak [from_file](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) lub [from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-).

```python
from azureml.core import Dataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/iris.csv')])
```

Dane pośrednie (lub dane wyjściowe kroku) są reprezentowane przez obiekt [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) . `output_data1`jest tworzony jako dane wyjściowe kroku i używany jako dane wejściowe jednego lub kilku przyszłych kroków. `PipelineData`wprowadza zależność danych między krokami i tworzy niejawną kolejność wykonywania w potoku. Ten obiekt będzie później używany podczas tworzenia kroków potoku.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

Więcej szczegółów i przykładowy kod służący do pracy z zestawami danych i danymi potoku można znaleźć w sekcji [przeniesienie danych do i między etapami potoku (Python)](how-to-move-data-in-out-of-pipelines.md).

## <a name="set-up-a-compute-target"></a>Konfigurowanie celu obliczeń

W Azure Machine Learning termin __obliczeniowy__ (lub __element docelowy obliczeń__) odnosi się do maszyn lub klastrów wykonujących kroki obliczeniowe w potoku uczenia maszynowego.   Zobacz [cele obliczeń dla szkolenia modelu](how-to-set-up-training-targets.md) , aby uzyskać pełną listę elementów docelowych obliczeń oraz sposób tworzenia i dołączania ich do obszaru roboczego.  Proces tworzenia i dołączania obiektu docelowego obliczeń jest taki sam, niezależnie od tego, czy jest to szkoleniowy model, czy też uruchamiany jest krok potoku. Po utworzeniu i dołączeniu obiektu docelowego obliczeń Użyj `ComputeTarget` obiektu w [kroku potoku](#steps).

> [!IMPORTANT]
> Wykonywanie operacji zarządzania na obiektach docelowych obliczeń nie jest obsługiwane w ramach zadań zdalnych. Potoki uczenia maszynowego są przesyłane jako zadania zdalne, dlatego nie należy używać operacji zarządzania na obiektach docelowych obliczeń z wnętrza potoku.

Poniżej przedstawiono przykłady tworzenia i dołączania obiektów docelowych obliczeń dla:

* Środowisko obliczeniowe usługi Azure Machine Learning
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning obliczeń

Można utworzyć Azure Machine Learning obliczeń do uruchamiania kroków.

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

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks jest środowiskiem opartym na Apache Spark w chmurze platformy Azure. Może służyć jako obiekt docelowy obliczeń z potokiem Azure Machine Learning.

Utwórz obszar roboczy Azure Databricks, zanim go użyjesz. Aby utworzyć zasób obszaru roboczego, zobacz dokument [Uruchamianie zadania Spark na Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) .

Aby dołączyć Azure Databricks jako element docelowy obliczeń, podaj następujące informacje:

* __Nazwa obliczeniowa datakostek__: nazwa, która ma zostać przypisana do tego zasobu obliczeniowego.
* __Nazwa obszaru roboczego elementów datakostki__: Nazwa obszaru roboczego Azure Databricks.
* __Token dostępu do datakostki__: token dostępu używany do uwierzytelniania w Azure Databricks. Aby wygenerować token dostępu, zobacz dokument [uwierzytelniania](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html) .

Poniższy kod ilustruje sposób dołączania Azure Databricks jako obiektu docelowego obliczeń przy użyciu zestawu SDK Azure Machine Learning (__obszar roboczy datakostki musi znajdować się w tej samej subskrypcji co obszar roboczy AML__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Aby zapoznać się z bardziej szczegółowym przykładem, zobacz [przykładowy Notes](https://aka.ms/pl-databricks) w witrynie GitHub.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics to platforma analizy danych Big Data w chmurze platformy Azure. Może służyć jako obiekt docelowy obliczeń z potokiem Azure Machine Learning.

Utwórz konto Azure Data Lake Analytics przed użyciem go. Aby utworzyć ten zasób, zapoznaj się z dokumentem [wprowadzenie do Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) .

Aby dołączyć Data Lake Analytics jako obiekt docelowy obliczeń, należy użyć zestawu SDK Azure Machine Learning i podać następujące informacje:

* __Nazwa obliczania__: nazwa, która ma zostać przypisana do tego zasobu obliczeniowego.
* __Grupa zasobów__: Grupa zasobów, która zawiera konto Data Lake Analytics.
* __Nazwa konta__: nazwa konta Data Lake Analytics.

Poniższy kod ilustruje sposób dołączania Data Lake Analytics jako obiekt docelowy obliczeń:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Aby zapoznać się z bardziej szczegółowym przykładem, zobacz [przykładowy Notes](https://aka.ms/pl-adla) w witrynie GitHub.

> [!TIP]
> Potoki Azure Machine Learning mogą korzystać tylko z danych przechowywanych w domyślnym magazynie danych konta Data Lake Analytics. Jeśli dane, które mają być używane, należą do magazynu innego niż domyślny, można użyć programu [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) do skopiowania danych przed szkoleniem.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>Konstruowanie kroków potoku

Po utworzeniu i dołączeniu obiektu docelowego obliczeń do obszaru roboczego możesz zdefiniować krok potoku. Istnieje wiele wbudowanych kroków dostępnych za pośrednictwem zestawu SDK Azure Machine Learning. Najbardziej podstawową procedurą jest [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), która uruchamia skrypt języka Python w określonym obiekcie docelowym obliczeń:

```python
from azureml.pipeline.steps import PythonScriptStep

ds_input = my_dataset.as_named_input('input1')

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", ds_input.as_download(), "--output", output_data1],
    inputs=[ds_input],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder,
    allow_reuse=True
)
```

Ponowne użycie poprzednich wyników (`allow_reuse`) jest kluczem w przypadku używania potoków w środowisku współpracy, ponieważ wyeliminowanie niepotrzebnych ponownych prób zapewnia elastyczność. Ponowne użycie jest zachowaniem domyślnym, gdy script_name, dane wejściowe i parametry kroku pozostają takie same. Gdy dane wyjściowe kroku są ponownie używane, zadanie nie zostanie przesłane do obliczenia, a wyniki z poprzedniego przebiegu są natychmiast dostępne dla uruchomienia następnego kroku. Jeśli `allow_reuse` jest ustawiona na wartość false, nowy przebieg będzie zawsze generowany dla tego kroku podczas wykonywania potoku. 

Po zdefiniowaniu kroków można skompilować potok za pomocą niektórych lub wszystkich tych kroków.

> [!NOTE]
> Podczas definiowania kroków lub kompilowania potoku nie są przekazywane żadne pliki ani dane do Azure Machine Learning.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

Poniższy przykład używa utworzonego wcześniej elementu docelowego obliczeń Azure Databricks: 

```python
from azureml.pipeline.steps import DatabricksStep

dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

### <a name="use-a-dataset"></a>Korzystanie z zestawu danych 

Zestawy danych utworzone z usługi Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database i Azure Database for PostgreSQL mogą być używane jako dane wejściowe do dowolnego etapu potoku. Możesz zapisać dane wyjściowe w [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py), [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)lub, jeśli chcesz zapisać danych do określonego magazynu datastore, użyj [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py). 

> [!IMPORTANT]
> Zapisywanie danych wyjściowych z powrotem do magazynu danych za pomocą PipelineData jest obsługiwane tylko w przypadku obiektów blob platformy Azure i magazynów danych usługi Azure File Share. Ta funkcja nie jest obsługiwana w przypadku [magazynów danych ADLS generacji 2](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) .

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Następnie można pobrać zestaw danych w potoku przy użyciu słownika [Run. input_datasets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets) .

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
* Konfiguruje dostęp `Dataset` do `PipelineData` obiektów i. W przypadku `as_mount()` trybu dostępu jako bezpiecznik służy do zapewnienia dostępu wirtualnego. Jeśli instalacja nie jest obsługiwana lub jeśli użytkownik określił dostęp jako `as_download()`, dane są kopiowane do elementu docelowego obliczeń.
* Uruchamia krok w elemencie docelowym obliczeń określonym w definicji kroku. 
* Tworzy artefakty, takie jak dzienniki, stdout i stderr, metryki i dane wyjściowe określone przez krok. Te artefakty są następnie przekazywane i przechowywane w domyślnym magazynie danych użytkownika.

![Diagram uruchamiania eksperymentu jako potoku](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Aby uzyskać więcej informacji, zobacz informacje o [klasie eksperymentów](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) .

### <a name="view-results-of-a-pipeline"></a>Wyświetlanie wyników potoku

Zobacz listę wszystkich potoków i ich szczegóły uruchamiania w programie Studio:

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com).

1. [Wyświetlanie obszaru roboczego](how-to-manage-workspace.md#view).

1. Po lewej stronie wybierz pozycję **potoki** , aby wyświetlić wszystkie uruchomienia potoków.
 ![Lista potoków uczenia maszynowego](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Wybierz konkretny potok, aby wyświetlić wyniki przebiegu.

## <a name="git-tracking-and-integration"></a>Śledzenie i integracja usługi git

Po rozpoczęciu szkolenia w przypadku, gdy katalog źródłowy jest lokalnym repozytorium git, informacje o repozytorium są przechowywane w historii uruchamiania. Aby uzyskać więcej informacji, zobacz Integracja z usługą [git dla Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="publish-a-pipeline"></a>Publikowanie potoku

Można opublikować potok, aby uruchomić go z innymi danymi wejściowymi później. W przypadku punktu końcowego REST już opublikowanego potoku w celu zaakceptowania parametrów należy Sparametryzuj potok przed opublikowaniem.

1. Aby utworzyć parametr potoku, użyj obiektu [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) z wartością domyślną.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Dodaj ten `PipelineParameter` obiekt jako parametr do dowolnego z kroków w potoku w następujący sposób:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Opublikuj ten potok, który zostanie zaakceptowany przez wywoływany parametr.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Uruchom opublikowany potok

Wszystkie opublikowane potoki mają punkt końcowy REST. Ten punkt końcowy wywołuje przebieg potoku z systemów zewnętrznych, takich jak klienci spoza języka Python. Ten punkt końcowy włącza "zarządzaną powtarzalność" w ramach oceniania wsadowego i scenariuszy ponownego szkolenia.

Aby wywołać poprzedni potok, należy uzyskać token nagłówka uwierzytelniania Azure Active Directory, zgodnie z opisem w dokumentacji dotyczącej [klasy AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) lub uzyskać więcej szczegółów dotyczących [uwierzytelniania w Azure Machine Learning](https://aka.ms/pl-restep-auth) notesie.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Tworzenie punktu końcowego potoku z wersją

Można utworzyć punkt końcowy potoku z wieloma opublikowanymi potokami. Ten element może być używany jak opublikowany potok, ale zapewnia stały punkt końcowy REST podczas iteracji i aktualizacji potoków ML.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PublishedPipeline.get(workspace="ws", name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>Przesyłanie zadania do punktu końcowego potoku

Można przesłać zadanie do domyślnej wersji punktu końcowego potoku:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

Możesz również przesłać zadanie do określonej wersji:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

Tę samą funkcję można wykonać przy użyciu interfejsu API REST:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>Używanie opublikowanych potoków w Studio

Możesz również uruchomić opublikowany potok z programu Studio:

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com).

1. [Wyświetlanie obszaru roboczego](how-to-manage-workspace.md#view).

1. Po lewej stronie wybierz pozycję **punkty końcowe**.

1. W górnej części wybierz pozycję **punkty końcowe potoku**.
 ![Lista opublikowanych potoków uczenia maszynowego](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Wybierz konkretny potok, który ma zostać uruchomiony, zużyty lub przejrzany wyniki poprzednich przebiegów punktu końcowego potoku.

### <a name="disable-a-published-pipeline"></a>Wyłącz opublikowany potok

Aby ukryć potok z listy opublikowanych potoków, należy go wyłączyć w programie Studio lub z zestawu SDK:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Można włączyć ją ponownie za pomocą `p.enable()`. Aby uzyskać więcej informacji, zobacz [PublishedPipeline klasy](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py) Reference.

## <a name="caching--reuse"></a>Buforowanie & ponownie używane  

Aby zoptymalizować i dostosować zachowanie potoków, można wykonać kilka czynności związanych z buforowaniem i wielokrotnym użyciem. Można na przykład wybrać następujące opcje:
+ Wyłącz **domyślne ponowne użycie kroku Uruchom dane wyjściowe** przez ustawienie `allow_reuse=False` podczas [definiowania kroku](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). Ponowne użycie jest kluczem przy użyciu potoków w środowisku współpracy, ponieważ wyeliminowanie niepotrzebnych uruchomień zapewnia elastyczność. Można jednak zrezygnować z ponownego użycia.
+ **Wymuś ponowne wygenerowanie danych wyjściowych dla wszystkich kroków w przebiegu** z`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Domyślnie dla kroków `allow_reuse` jest włączone, a `source_directory` określone w definicji kroku są skrótem. Tak więc, jeśli skrypt dla danego kroku pozostaje taki sam (`script_name`, dane wejściowe i parametry), a żadne inne elementy w programie` source_directory` nie zmieniły się, dane wyjściowe przebiegu poprzedniego kroku są ponownie używane, zadanie nie zostanie przesłane do obliczenia, a wyniki z poprzedniego uruchomienia są natychmiast dostępne w następnym kroku.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="next-steps"></a>Następne kroki

- [Te notesy Jupyter w usłudze GitHub](https://aka.ms/aml-pipeline-readme) umożliwiają dalsze Eksplorowanie potoków usługi Machine Learning.
- Zapoznaj się z dokumentacją zestawu SDK dla pakietu [Azure-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) oraz pakietem [kroków potoków usługi Azure](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .
- Zobacz Porady [dotyczące debugowania](how-to-debug-pipelines.md) i rozwiązywania problemów z potokami.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
