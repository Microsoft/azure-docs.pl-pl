---
title: YAML potoku Machine Learning
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zdefiniować potok uczenia maszynowego przy użyciu pliku YAML. Definicje potoku YAML są używane z rozszerzeniem uczenia maszynowego dla interfejsu wiersza polecenia platformy Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: nilsp
author: NilsPohlmann
ms.date: 07/31/2020
ms.custom: devx-track-python
ms.openlocfilehash: e2b5a3322f633ca8301357c2186d78d3ac437ae2
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521972"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Definiowanie potoków uczenia maszynowego w YAML

Dowiedz się, jak definiować potoki uczenia maszynowego w [YAML](https://yaml.org/). W przypadku korzystania z rozszerzenia Uczenie maszynowe dla interfejsu wiersza polecenia platformy Azure wiele poleceń związanych z potokiem oczekuje pliku YAML, który definiuje potok.

W poniższej tabeli przedstawiono, co jest i nie jest obecnie obsługiwane podczas definiowania potoku w YAML:

| Typ kroku | Obsługiwane? |
| ----- | :-----: |
| PythonScriptStep | Tak |
| ParallelRunStep | Tak |
| AdlaStep | Tak |
| AzureBatchStep | Tak |
| DatabricksStep | Tak |
| DataTransferStep | Tak |
| AutoMLStep | Nie |
| HyperDriveStep | Nie |
| ModuleStep | Tak |
| MPIStep | Nie |
| EstimatorStep | Nie |

## <a name="pipeline-definition"></a>Definicja potoku

Definicja potoku używa następujących kluczy, które odpowiadają klasie [potoków](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline) :

| Klucz YAML | Opis |
| ----- | ----- |
| `name` | Opis potoku. |
| `parameters` | Parametrów do potoku. |
| `data_reference` | Definiuje sposób i miejsce, w którym dane powinny być dostępne w przebiegu. |
| `default_compute` | Domyślny element docelowy obliczeń, w którym wykonywane są wszystkie kroki w potoku. |
| `steps` | Kroki używane w potoku. |

## <a name="parameters"></a>Parametry

`parameters`Sekcja używa następujących kluczy, które odpowiadają klasie [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter) :

| Klucz YAML | Opis |
| ---- | ---- |
| `type` | Typ wartości parametru. Prawidłowe typy to `string` , `int` , `float` , `bool` lub `datapath` . |
| `default` | Wartość domyślna. |

Każdy parametr ma nazwę. Na przykład poniższy fragment kodu YAML definiuje trzy parametry o nazwach `NumIterationsParameter` , `DataPathParameter` i `NodeCountParameter` :

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

`data_references`Sekcja używa następujących kluczy, które odnoszą się do [DataReference](/python/api/azureml-core/azureml.data.data_reference.datareference):

| Klucz YAML | Opis |
| ----- | ----- |
| `datastore` | Magazyn danych do odwołania. |
| `path_on_datastore` | Ścieżka względna w magazynie zapasowym dla odwołania do danych. |

Każde odwołanie do danych jest zawarte w kluczu. Na przykład poniższy fragment kodu YAML definiuje odwołanie do danych przechowywane w kluczu o nazwie `employee_data` :

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

Kroki definiują środowisko obliczeniowe, a także pliki do uruchomienia w środowisku. Aby zdefiniować typ kroku, użyj `type` klucza:

| Typ kroku | Opis |
| ----- | ----- |
| `AdlaStep` | Uruchamia skrypt U-SQL z Azure Data Lake Analytics. Odpowiada klasie [AdlaStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep) . |
| `AzureBatchStep` | Uruchamia zadania przy użyciu Azure Batch. Odpowiada klasie [AzureBatchStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) . |
| `DatabricsStep` | Dodaje Notes, skrypt w języku Python lub JAR. Odpowiada klasie [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep) . |
| `DataTransferStep` | Przesyła dane między opcjami magazynu. Odpowiada klasie [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) . |
| `PythonScriptStep` | Uruchamia skrypt języka Python. Odpowiada klasie [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) . |
| `ParallelRunStep` | Uruchamia skrypt języka Python w celu asynchronicznego i równoległego przetwarzania dużych ilości danych. Odpowiada klasie [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) . |

### <a name="adla-step"></a>ADLA — krok

| Klucz YAML | Opis |
| ----- | ----- |
| `script_name` | Nazwa skryptu U-SQL (względem `source_directory` ). |
| `compute_target` | Obiekt docelowy obliczeń Azure Data Lake do użycia w tym kroku. |
| `parameters` | [Parametry](#parameters) do potoku. |
| `inputs` | Dane wejściowe mogą być [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [DataSet](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)lub [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `outputs` | Wynikami może być [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) lub [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `source_directory` | Katalog zawierający skrypt, zestawy itp. |
| `priority` | Wartość priorytetu, która ma być używana w bieżącym zadaniu. |
| `params` | Słownik par nazwa-wartość. |
| `degree_of_parallelism` | Stopień równoległości dla tego zadania. |
| `runtime_version` | Wersja środowiska uruchomieniowego aparatu Data Lake Analytics. |
| `allow_reuse` | Określa, czy krok ma ponownie używać poprzednich wyników po ponownym uruchomieniu z tymi samymi ustawieniami. |

Poniższy przykład zawiera definicję kroku ADLA:

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

### <a name="azure-batch-step"></a>Azure Batch krok

| Klucz YAML | Opis |
| ----- | ----- |
| `compute_target` | Obiekt docelowy obliczeń Azure Batch do użycia w tym kroku. |
| `inputs` | Dane wejściowe mogą być [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [DataSet](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)lub [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `outputs` | Wynikami może być [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) lub [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `source_directory` | Katalog zawierający pliki binarne modułu, pliki wykonywalne, zestawy itp. |
| `executable` | Nazwa polecenia/pliku wykonywalnego, który zostanie uruchomiony w ramach tego zadania. |
| `create_pool` | Flaga logiczna wskazująca, czy należy utworzyć pulę przed uruchomieniem zadania. |
| `delete_batch_job_after_finish` | Flagę logiczną, aby wskazać, czy zadanie ma zostać usunięte z konta usługi Batch po zakończeniu. |
| `delete_batch_pool_after_finish` | Flaga logiczna wskazująca, czy należy usunąć pulę po zakończeniu zadania. |
| `is_positive_exit_code_failure` | Flaga logiczna wskazująca, czy zadanie kończy się niepowodzeniem, jeśli zadanie zostanie zakończone z kodem pozytywnym. |
| `vm_image_urn` | Jeśli `create_pool` jest `True` , a maszyna wirtualna używa `VirtualMachineConfiguration` . |
| `pool_id` | Identyfikator puli, w której zostanie uruchomione zadanie. |
| `allow_reuse` | Określa, czy krok ma ponownie używać poprzednich wyników po ponownym uruchomieniu z tymi samymi ustawieniami. |

Poniższy przykład zawiera definicję Azure Batch krok:

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

### <a name="databricks-step"></a>Krok z datakostki

| Klucz YAML | Opis |
| ----- | ----- |
| `compute_target` | Obiekt docelowy obliczeń Azure Databricks do użycia w tym kroku. |
| `inputs` | Dane wejściowe mogą być [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [DataSet](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)lub [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `outputs` | Wynikami może być [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) lub [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `run_name` | Nazwa w datakostkach dla tego przebiegu. |
| `source_directory` | Katalog zawierający skrypt i inne pliki. |
| `num_workers` | Statyczna liczba procesów roboczych dla elementów datakostks uruchamia klaster. |
| `runconfig` | Ścieżka do `.runconfig` pliku. Ten plik jest reprezentacją YAML klasy [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration) . Aby uzyskać więcej informacji na temat struktury tego pliku, zobacz [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Określa, czy krok ma ponownie używać poprzednich wyników po ponownym uruchomieniu z tymi samymi ustawieniami. |

Poniższy przykład zawiera krok datakostki:

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
| `compute_target` | Obiekt docelowy obliczeń Azure Data Factory do użycia w tym kroku. |
| `source_data_reference` | Połączenie wejściowe, które służy jako źródło operacji transferu danych. Obsługiwane wartości to [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [DataSet](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)lub [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `destination_data_reference` | Połączenie wejściowe służące jako miejsce docelowe operacji transferu danych. Obsługiwane wartości to [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) i [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `allow_reuse` | Określa, czy krok ma ponownie używać poprzednich wyników po ponownym uruchomieniu z tymi samymi ustawieniami. |

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

### <a name="python-script-step"></a>Krok skryptu języka Python

| Klucz YAML | Opis |
| ----- | ----- |
| `inputs` | Dane wejściowe mogą być [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [DataSet](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)lub [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `outputs` | Wynikami może być [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) lub [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `script_name` | Nazwa skryptu języka Python (względem `source_directory` ). |
| `source_directory` | Katalog zawierający skrypt, środowisko Conda itp. |
| `runconfig` | Ścieżka do `.runconfig` pliku. Ten plik jest reprezentacją YAML klasy [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration) . Aby uzyskać więcej informacji na temat struktury tego pliku, zobacz [runconfig.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Określa, czy krok ma ponownie używać poprzednich wyników po ponownym uruchomieniu z tymi samymi ustawieniami. |

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

### <a name="parallel-run-step"></a>Krok przebiegu równoległego

| Klucz YAML | Opis |
| ----- | ----- |
| `inputs` | Dane wejściowe mogą być [zestawami danych](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)lub [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `outputs` | Wynikami może być [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) lub [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `script_name` | Nazwa skryptu języka Python (względem `source_directory` ). |
| `source_directory` | Katalog zawierający skrypt, środowisko Conda itp. |
| `parallel_run_config` | Ścieżka do `parallel_run_config.yml` pliku. Ten plik jest reprezentacją YAML klasy [ParallelRunConfig](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig) . |
| `allow_reuse` | Określa, czy krok ma ponownie używać poprzednich wyników po ponownym uruchomieniu z tymi samymi ustawieniami. |

Poniższy przykład zawiera krok przebiegu równoległego:

```yaml
pipeline:
    description: SamplePipelineFromYaml
    default_compute: cpu-cluster
    data_references:
        MyMinistInput:
            dataset_name: mnist_sample_data
    parameters:
        PipelineParamTimeout:
            type: int
            default: 600
    steps:        
        Step1:
            parallel_run_config: "yaml/parallel_run_config.yml"
            type: "ParallelRunStep"
            name: "parallel-run-step-1"
            allow_reuse: True
            arguments:
            - "--progress_update_timeout"
            - parameter:timeout_parameter
            - "--side_input"
            - side_input:SideInputData
            parameters:
                timeout_parameter:
                    source: PipelineParamTimeout
            inputs:
                InputData:
                    source: MyMinistInput
            side_inputs:
                SideInputData:
                    source: Output4
                    bind_mode: mount
            outputs:
                OutputDataStep2:
                    destination: Output5
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="pipeline-with-multiple-steps"></a>Potok z wieloma krokami 

| Klucz YAML | Opis |
| ----- | ----- |
| `steps` | Sekwencja co najmniej jednej definicji PipelineStep. Należy zauważyć, że `destination` klucze jednego kroku `outputs` stają się `source` kluczami do `inputs` następnego kroku.| 

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

Podczas definiowania harmonogramu dla potoku może to być magazyn danych — wyzwolony lub cykliczny na podstawie interwału czasu. Poniżej przedstawiono klucze służące do definiowania harmonogramu:

| Klucz YAML | Opis |
| ----- | ----- |
| `description` | Opis harmonogramu. |
| `recurrence` | Zawiera ustawienia cyklu, jeśli harmonogram jest cykliczny. |
| `pipeline_parameters` | Wszystkie parametry, które są wymagane przez potok. |
| `wait_for_provisioning` | Określa, czy czekać na zakończenie aprowizacji harmonogramu. |
| `wait_timeout` | Liczba sekund oczekiwania przed upływem limitu czasu. |
| `datastore_name` | Magazyn danych do monitorowania dla zmodyfikowanych/dodanych obiektów BLOB. |
| `polling_interval` | Czas trwania sondowania dla zmodyfikowanych/dodanych obiektów BLOB w minutach. Wartość domyślna: 5 minut. Obsługiwane tylko w przypadku harmonogramów magazynu danych. |
| `data_path_parameter_name` | Nazwa parametru potoku ścieżki danych do ustawienia ze zmienioną ścieżką obiektu BLOB. Obsługiwane tylko w przypadku harmonogramów magazynu danych. |
| `continue_on_step_failure` | Czy kontynuować wykonywanie innych kroków w przesłanych PipelineRun w przypadku niepowodzenia kroku. Jeśli ta opcja jest określona, zastąpi `continue_on_step_failure` ustawienie potoku.
| `path_on_datastore` | Opcjonalny. Ścieżka do magazynu danych do monitorowania dla zmodyfikowanych/dodanych obiektów BLOB. Ścieżka znajduje się w kontenerze dla magazynu danych, więc rzeczywista ścieżka monitorów harmonogramu to Container/ `path_on_datastore` . Jeśli nie, kontener magazynu danych jest monitorowany. Dodatki/modyfikacje wprowadzone w podfolderze `path_on_datastore` nie są monitorowane. Obsługiwane tylko w przypadku harmonogramów magazynu danych. |

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

Podczas definiowania **harmonogramu cyklicznego** Użyj następujących kluczy w obszarze `recurrence` :

| Klucz YAML | Opis |
| ----- | ----- |
| `frequency` | Częstotliwość powtarzania harmonogramu. Prawidłowe wartości to `"Minute"` , `"Hour"` , `"Day"` , `"Week"` lub `"Month"` . |
| `interval` | Jak często wyzwalany jest harmonogram. Wartość całkowita to liczba jednostek czasu oczekiwania do momentu ponownego uruchomienia harmonogramu. |
| `start_time` | Godzina rozpoczęcia harmonogramu. Format ciągu wartości to `YYYY-MM-DDThh:mm:ss` . Jeśli nie podano czasu rozpoczęcia, pierwsze obciążenie jest uruchamiane natychmiast, a przyszłe obciążenia są uruchamiane zgodnie z harmonogramem. Jeśli czas rozpoczęcia jest w przeszłości, pierwsze obciążenie zostanie uruchomione przy następnym obliczonym czasie wykonywania. |
| `time_zone` | Strefa czasowa godziny rozpoczęcia. Jeśli nie podano strefy czasowej, używany jest czas UTC. |
| `hours` | Jeśli `frequency` jest `"Day"` lub `"Week"` , możesz określić co najmniej jedną liczbę całkowitą z zakresu od 0 do 23, rozdzieloną przecinkami, jako godziny dnia, w którym ma zostać uruchomiony potok. Tylko `time_of_day` lub `hours` i `minutes` mogą być używane. |
| `minutes` | Jeśli `frequency` jest `"Day"` lub `"Week"` , można określić co najmniej jedną liczbę całkowitą z przedziału od 0 do 59, oddzieloną przecinkami, jako minuty godziny, kiedy potok powinien zostać uruchomiony. Tylko `time_of_day` lub `hours` i `minutes` mogą być używane. |
| `time_of_day` | Jeśli `frequency` jest `"Day"` lub `"Week"` , możesz określić godzinę, o której ma być uruchamiany harmonogram. Format ciągu wartości to `hh:mm` . Tylko `time_of_day` lub `hours` i `minutes` mogą być używane. |
| `week_days` | Jeśli `frequency` jest `"Week"` , możesz określić jeden lub więcej dni rozdzielonych przecinkami, gdy harmonogram powinien zostać uruchomiony. Prawidłowe wartości to `"Monday"` , `"Tuesday"` , `"Wednesday"` ,,, `"Thursday"` `"Friday"` `"Saturday"` i `"Sunday"` . |

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

Dowiedz się [, jak używać rozszerzenia interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md).