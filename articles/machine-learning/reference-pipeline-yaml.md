---
title: Potok uczenia maszynowego YAML
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zdefiniować potok uczenia maszynowego przy użyciu pliku YAML. Definicje potoku YAML są używane z rozszerzeniem uczenia maszynowego dla interfejsu wiersza polecenia platformy Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 11/11/2019
ms.openlocfilehash: 40e6d7f3d9c28708c5adec26ddc3c0463e75adc0
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529709"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Definiowanie potoków uczenia maszynowego w yaml

Dowiedz się, jak zdefiniować potoki uczenia maszynowego w [yaml](https://yaml.org/). Podczas korzystania z rozszerzenia uczenia maszynowego dla interfejsu wiersza polecenia platformy Azure wiele poleceń związanych z potokiem oczekuje pliku YAML, który definiuje potok.

W poniższej tabeli wymieniono, co jest i nie jest obecnie obsługiwane podczas definiowania potoku w yaml:

| Typ kroku | Obsługiwane? |
| ----- | :-----: |
| PythonScriptStep (Krok języka Pythona) | Tak |
| AdlaStep (AdlaStep) | Tak |
| AzureBatchStep | Tak |
| DatabricksStep (Krok databricks) | Tak |
| Przepływ danych | Tak |
| AutoMLStep | Nie |
| HyperDriveStep | Nie |
| Krok modułu | Tak |
| Krok MPIStep | Nie |
| EstimatorStep (EstimatorStep) | Nie |

## <a name="pipeline-definition"></a>Definicja rurociągu

Definicja potoku używa następujących kluczy, które odpowiadają [Pipelines](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py) klasy:

| Klucz YAML | Opis |
| ----- | ----- |
| `name` | Opis potoku. |
| `parameters` | Parametr(-y) do potoku. |
| `data_reference` | Określa, jak i gdzie dane powinny być udostępniane w przebiegu. |
| `default_compute` | Domyślny obiekt docelowy obliczeń, w którym przebiegają wszystkie kroki w potoku. |
| `steps` | Kroki używane w potoku. |

## <a name="parameters"></a>Parametry

Sekcja `parameters` używa następujących kluczy, które odpowiadają [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py) klasy:

| Klucz YAML | Opis |
| ---- | ---- |
| `type` | Typ wartości parametru. Prawidłowe `string`typy `float` `bool`to `datapath`, `int`, , , lub . |
| `default` | Wartość domyślna. |

Każdy parametr jest nazwany. Na przykład poniższy fragment kodu YAML definiuje `NumIterationsParameter`trzy `DataPathParameter`parametry `NodeCountParameter`o nazwie , i :

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>Opis danych

W `data_references` sekcji użyto następujących kluczy, które odpowiadają [datareference:](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)

| Klucz YAML | Opis |
| ----- | ----- |
| `datastore` | Magazyn danych do odwołania. |
| `path_on_datastore` | Ścieżka względna w magazynie zapasowym dla odwołania do danych. |

Każde odwołanie do danych znajduje się w kluczu. Na przykład następujący fragment kodu YAML definiuje odwołanie do danych `employee_data`przechowywane w kluczu o nazwie:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>Kroki

Kroki definiują środowisko obliczeniowe wraz z plikami do uruchomienia w środowisku. Aby zdefiniować typ kroku, `type` użyj klawisza:

| Typ kroku | Opis |
| ----- | ----- |
| `AdlaStep` | Uruchamia skrypt U-SQL za pomocą usługi Azure Data Lake Analytics. Odpowiada klasie [AdlaStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py) |
| `AzureBatchStep` | Uruchamia zadania przy użyciu usługi Azure Batch. Odpowiada klasie [AzureBatchStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py) |
| `DatabricsStep` | Dodaje notes Databricks, skrypt Pythona lub JAR. Odpowiada [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py) klasy. |
| `DataTransferStep` | Przesyła dane między opcjami przechowywania. Odpowiada klasie [DataTransferStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) |
| `PythonScriptStep` | Uruchamia skrypt języka Python. Odpowiada klasie [PythonScriptStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) |

### <a name="adla-step"></a>Krok ADLA

| Klucz YAML | Opis |
| ----- | ----- |
| `script_name` | Nazwa skryptu U-SQL (względem `source_directory`). |
| `compute_target` | Obiekt docelowy obliczeń usługi Azure Data Lake do użycia w tym kroku. |
| `parameters` | [Parametry](#parameters) do potoku. |
| `inputs` | Dane wejściowe mogą być [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)lub [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Dane wyjściowe mogą być [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) lub [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Katalog zawierający skrypt, zestawy itp. |
| `priority` | Wartość priorytetu do użycia dla bieżącego zadania. |
| `params` | Słownik par nazwa-wartość. |
| `degree_of_parallelism` | Stopień równoległości do użycia w tym zadaniu. |
| `runtime_version` | Wersja wykonawcza aparatu usługi Data Lake Analytics. |
| `allow_reuse` | Określa, czy krok powinien ponownie używać poprzednich wyników po ponownym uruchomieniu z tymi samymi ustawieniami. |

Poniższy przykład zawiera definicję kroku usługi ADLA:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AdlaStep"
            name: "MyAdlaStep"
            script_name: "sample_script.usql"
            source_directory: "D:\\scripts\\Adla"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Krok wsadowy platformy Azure

| Klucz YAML | Opis |
| ----- | ----- |
| `compute_target` | Obiekt docelowy obliczeń usługi Azure Batch do użycia w tym kroku. |
| `inputs` | Dane wejściowe mogą być [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)lub [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Dane wyjściowe mogą być [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) lub [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Katalog zawierający pliki binarne modułu, plik wykonywalny, zestawy itp. |
| `executable` | Nazwa polecenia/pliku wykonywalnego, które zostanie uruchomiony jako część tego zadania. |
| `create_pool` | Flaga logiczna wskazująca, czy utworzyć pulę przed uruchomieniem zadania. |
| `delete_batch_job_after_finish` | Flaga logiczna wskazująca, czy zadanie ma być usuwane z konta usługi Batch po jego zakończeniu. |
| `delete_batch_pool_after_finish` | Flaga logiczna wskazująca, czy usunąć pulę po zakończeniu zadania. |
| `is_positive_exit_code_failure` | Flaga logiczna, aby wskazać, czy zadanie zakończy się niepowodzeniem, jeśli zadanie zakończy działanie z kodem dodatnim. |
| `vm_image_urn` | Jeśli `create_pool` `True`jest , a `VirtualMachineConfiguration`maszyna wirtualna używa . |
| `pool_id` | Identyfikator puli, w której zostanie uruchomione zadanie. |
| `allow_reuse` | Określa, czy krok powinien ponownie używać poprzednich wyników po ponownym uruchomieniu z tymi samymi ustawieniami. |

Poniższy przykład zawiera definicję kroku usługi Azure Batch:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AzureBatchStep"
            name: "MyAzureBatchStep"
            pool_id: "MyPoolName"
            create_pool: true
            executable: "azurebatch.cmd"
            source_directory: "D:\\scripts\\AureBatch"
            allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>Krok Databricks

| Klucz YAML | Opis |
| ----- | ----- |
| `compute_target` | Obiekt docelowy obliczeń usługi Azure Databricks do użycia w tym kroku. |
| `inputs` | Dane wejściowe mogą być [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)lub [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Dane wyjściowe mogą być [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) lub [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `run_name` | Nazwa w Databricks dla tego uruchomienia. |
| `source_directory` | Katalog zawierający skrypt i inne pliki. |
| `num_workers` | Statyczna liczba pracowników dla klastra uruchamiania databricks. |
| `runconfig` | Ścieżka do `.runconfig` pliku. Ten plik jest reprezentacją YAML klasy [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) Aby uzyskać więcej informacji na temat struktury tego pliku, zobacz [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Określa, czy krok powinien ponownie używać poprzednich wyników po ponownym uruchomieniu z tymi samymi ustawieniami. |

Poniższy przykład zawiera krok Databricks:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DatabricksStep"
            name: "MyDatabrickStep"
            run_name: "DatabricksRun"
            python_script_name: "train-db-local.py"
            source_directory: "D:\\scripts\\Databricks"
            num_workers: 1
            allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>Krok transferu danych

| Klucz YAML | Opis |
| ----- | ----- |
| `compute_target` | Obiekt docelowy obliczeń usługi Azure Data Factory do użycia w tym kroku. |
| `source_data_reference` | Połączenie wejściowe, które służy jako źródło operacji transferu danych. Obsługiwane wartości to [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)lub [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `destination_data_reference` | Połączenie wejściowe, które służy jako miejsce docelowe operacji transferu danych. Obsługiwane wartości to [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) i [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `allow_reuse` | Określa, czy krok powinien ponownie używać poprzednich wyników po ponownym uruchomieniu z tymi samymi ustawieniami. |

Poniższy przykład zawiera krok transferu danych:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DataTransferStep"
            name: "MyDataTransferStep"
            adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Krok skryptu Języka Python

| Klucz YAML | Opis |
| ----- | ----- |
| `inputs` | Dane wejściowe mogą być [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)lub [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Dane wyjściowe mogą być [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) lub [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `script_name` | Nazwa skryptu Pythona (względem `source_directory`). |
| `source_directory` | Katalog zawierający skrypt, środowisko Conda itp. |
| `runconfig` | Ścieżka do `.runconfig` pliku. Ten plik jest reprezentacją YAML klasy [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) Aby uzyskać więcej informacji na temat struktury tego pliku, zobacz [runconfig.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Określa, czy krok powinien ponownie używać poprzednich wyników po ponownym uruchomieniu z tymi samymi ustawieniami. |

Poniższy przykład zawiera krok skryptu języka Python:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "PythonScriptStep"
            name: "MyPythonScriptStep"
            script_name: "train.py"
            allow_reuse: True
            source_directory: "D:\\scripts\\PythonScript"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="pipeline-with-multiple-steps"></a>Potok z wieloma krokami 

| Klucz YAML | Opis |
| ----- | ----- |
| `steps` | Sekwencja co najmniej jednej definicji PipelineStep. Należy pamiętać, że `destination` jeden `outputs` krok staje `inputs` się kluczem do .| 

```yaml
pipeline:
    name: SamplePipelineFromYAML
    description: Sample multistep YAML pipeline
    data_references:
        TitanicDS:
            dataset_name: 'titanic_ds'
            bind_mode: download
    default_compute: cpu-cluster
    steps:
        Dataprep:
            type: "PythonScriptStep"
            name: "DataPrep Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "prep.py"
            arguments:
            - '--train_path'
            - output:train_path
            - '--test_path'
            - output:test_path
            allow_reuse: True
            inputs:
                titanic_ds:
                    source: TitanicDS
                    bind_mode: download
            outputs:
                train_path:
                    destination: train_csv
                    datastore: workspaceblobstore
                test_path:
                    destination: test_csv
        Training:
            type: "PythonScriptStep"
            name: "Training Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "train.py"
            arguments:
            - "--train_path"
            - input:train_path
            - "--test_path"
            - input:test_path
            inputs:
                train_path:
                    source: train_csv
                    bind_mode: download
                test_path:
                    source: test_csv
                    bind_mode: download

```

## <a name="schedules"></a>Harmonogramy

Podczas definiowania harmonogramu dla potoku, może być wyzwalane przez magazyn danych lub cykliczne na podstawie przedziału czasu. Poniżej znajdują się klucze używane do definiowania harmonogramu:

| Klucz YAML | Opis |
| ----- | ----- |
| `description` | Opis harmonogramu. |
| `recurrence` | Zawiera ustawienia cyklu, jeśli harmonogram jest cykliczny. |
| `pipeline_parameters` | Wszystkie parametry, które są wymagane przez potok. |
| `wait_for_provisioning` | Czy czekać na inicjowanie obsługi administracyjnej harmonogramu, aby zakończyć. |
| `wait_timeout` | Liczba sekund oczekiwania przed przesuniem czasu. |
| `datastore_name` | Magazyn danych do monitorowania zmodyfikowanych/dodanych obiektów blob. |
| `polling_interval` | Jak długo, w minutach, między sondowania zmodyfikowanych/dodanych obiektów blob. Wartość domyślna: 5 minut. Obsługiwane tylko dla harmonogramów magazynu danych. |
| `data_path_parameter_name` | Nazwa parametru potoku ścieżki danych ustawionej ze zmienioną ścieżką obiektu blob. Obsługiwane tylko dla harmonogramów magazynu danych. |
| `continue_on_step_failure` | Czy kontynuować wykonywanie innych kroków w przesłane PipelineRun, jeśli krok nie powiedzie się. Jeśli pod warunkiem, zastąpi `continue_on_step_failure` ustawienie potoku.
| `path_on_datastore` | Element opcjonalny. Ścieżka w magazynie danych do monitorowania zmodyfikowanych/dodanych obiektów blob. Ścieżka znajduje się pod kontenerem dla magazynu danych, więc rzeczywista`path_on_datastore`ścieżka monitorów harmonogramu jest kontener/ . Jeśli brak, kontener magazynu danych jest monitorowany. Uzupełnienia/modyfikacje dokonane w podfolderze nie `path_on_datastore` są monitorowane. Obsługiwane tylko dla harmonogramów magazynu danych. |

Poniższy przykład zawiera definicję harmonogramu wyzwalanego przez magazyn danych:

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

Podczas definiowania **harmonogramu cyklicznego** `recurrence`użyj następujących kluczy w obszarze:

| Klucz YAML | Opis |
| ----- | ----- |
| `frequency` | Jak często harmonogram powtarza się. Prawidłowe `"Minute"`wartości `"Hour"` `"Day"`to `"Week"`, `"Month"`, , , lub . |
| `interval` | Jak często uruchamia się harmonogram. Wartość całkowita to liczba jednostek czasu oczekiwania na ponowne odpalenie harmonogramu. |
| `start_time` | Godzina rozpoczęcia dla harmonogramu. Formatem ciągu wartości `YYYY-MM-DDThh:mm:ss`jest . Jeśli nie podano czasu rozpoczęcia, pierwsze obciążenie jest uruchamiane natychmiast, a przyszłe obciążenia są uruchamiane na podstawie harmonogramu. Jeśli czas rozpoczęcia jest w przeszłości, pierwsze obciążenie jest uruchamiane w następnym obliczonym czasie wykonywania. |
| `time_zone` | Strefa czasowa dla czasu rozpoczęcia. Jeśli nie podano strefy czasowej, używana jest utc. |
| `hours` | Jeśli `frequency` `"Day"` jest `"Week"`lub , można określić jedną lub więcej liczby całkowitych od 0 do 23, oddzielone przecinkami, jako godziny dnia, w którym potok powinien być uruchomiony. Tylko `time_of_day` `hours` lub `minutes` i może być używany. |
| `minutes` | Jeśli `frequency` `"Day"` jest `"Week"`lub , można określić jedną lub więcej liczby całkowitych od 0 do 59, oddzielone przecinkami, jako minuty godziny, kiedy potok powinien działać. Tylko `time_of_day` `hours` lub `minutes` i może być używany. |
| `time_of_day` | Jeśli `frequency` `"Day"` jest `"Week"`lub , można określić porę dnia dla harmonogramu do uruchomienia. Formatem ciągu wartości `hh:mm`jest . Tylko `time_of_day` `hours` lub `minutes` i może być używany. |
| `week_days` | Jeśli `frequency` `"Week"`tak, można określić jeden lub więcej dni, oddzielonych przecinkami, kiedy harmonogram powinien zostać uruchomiony. Prawidłowe `"Monday"`wartości `"Tuesday"` `"Wednesday"`to `"Thursday"` `"Friday"`, `"Saturday"`, `"Sunday"`, , , i . |

Poniższy przykład zawiera definicję harmonogramu cyklicznego:

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [korzystać z rozszerzenia interfejsu wiersza polecenia dla usługi Azure Machine Learning](reference-azure-machine-learning-cli.md).
