---
title: Machine Learning yaml potoku
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
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 1c44f018d558b9426ba6271c0cbb1c2356a2a9b2
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478288"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Definiowanie potoków uczenia maszynowego w yaml

Dowiedz się, jak definiować potoki uczenia maszynowego w [yaml.](https://yaml.org/) W przypadku korzystania z rozszerzenia uczenia maszynowego dla interfejsu wiersza polecenia platformy Azure wiele poleceń związanych z potokiem oczekuje pliku YAML, który definiuje potok.

W poniższej tabeli wymieniono elementy, które są i nie są obecnie obsługiwane podczas definiowania potoku w pliku YAML:

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
| EstimatorStep (Krok szacowania) | Nie |

## <a name="pipeline-definition"></a>Definicja potoku

Definicja potoku używa następujących kluczy, które odpowiadają klasie [Pipelines:](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline)

| Klucz YAML | Opis |
| ----- | ----- |
| `name` | Opis potoku. |
| `parameters` | Parametry potoku. |
| `data_reference` | Definiuje sposób i miejsce, w którym dane powinny być udostępniane w uruchomieniu. |
| `default_compute` | Domyślny docelowy obiekt obliczeniowy, w którym są uruchamiane wszystkie kroki potoku. |
| `steps` | Kroki używane w potoku. |

## <a name="parameters"></a>Parametry

Sekcja `parameters` używa następujących kluczy, które odpowiadają klasie [PipelineParameter:](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter)

| Klucz YAML | Opis |
| ---- | ---- |
| `type` | Typ wartości parametru. Prawidłowe typy to `string` , , , lub `int` `float` `bool` `datapath` . |
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

W `data_references` sekcji użyto następujących kluczy, które odpowiadają [wnioskowi DataReference:](/python/api/azureml-core/azureml.data.data_reference.datareference)

| Klucz YAML | Opis |
| ----- | ----- |
| `datastore` | Magazyn danych, do których ma się odwoływać. |
| `path_on_datastore` | Ścieżka względna w magazynie kopii zapasowej dla odwołania do danych. |

Każde odwołanie do danych znajduje się w kluczu. Na przykład poniższy fragment kodu YAML definiuje odwołanie do danych przechowywane w kluczu o nazwie `employee_data` :

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

Kroki definiują środowisko obliczeniowe wraz z plikami do uruchomienia w środowisku. Aby zdefiniować typ kroku, użyj `type` klucza :

| Typ kroku | Opis |
| ----- | ----- |
| `AdlaStep` | Uruchamia skrypt U-SQL z Azure Data Lake Analytics. Odpowiada klasie [AdlaStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep) |
| `AzureBatchStep` | Uruchamia zadania przy użyciu Azure Batch. Odpowiada klasie [AzureBatchStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) |
| `DatabricsStep` | Dodaje notes databricks, skrypt języka Python lub kod JAR. Odpowiada klasie [DatabricksStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep) |
| `DataTransferStep` | Transferuje dane między opcjami magazynu. Odpowiada klasie [DataTransferStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) |
| `PythonScriptStep` | Uruchamia skrypt języka Python. Odpowiada klasie [PythonScriptStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) |
| `ParallelRunStep` | Uruchamia skrypt języka Python, aby przetwarzać duże ilości danych asynchronicznie i równolegle. Odpowiada klasie [ParallelRunStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) |

### <a name="adla-step"></a>Krok ADLA

| Klucz YAML | Opis |
| ----- | ----- |
| `script_name` | Nazwa skryptu U-SQL (względem `source_directory` ). |
| `compute` | Docelowy obiekt obliczeniowy usługi Azure Data Lake do użycia w tym kroku. |
| `parameters` | [Parametry](#parameters) potoku. |
| `inputs` | Danymi wejściowymi mogą być [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)lub [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `outputs` | Dane wyjściowe to [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) lub [OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `source_directory` | Katalog zawierający skrypt, zestawy itp. |
| `priority` | Wartość priorytetu do użycia dla bieżącego zadania. |
| `params` | Słownik par nazwa-wartość. |
| `degree_of_parallelism` | Stopień równoległości do użycia w tym zadaniu. |
| `runtime_version` | Wersja środowiska uruchomieniowego aparatu Data Lake Analytics uruchomieniowego. |
| `allow_reuse` | Określa, czy krok powinien ponownie używać poprzednich wyników po ponownym uruchomieniu z tym samymi ustawieniami. |

Poniższy przykład zawiera definicję kroku adla:

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
| `compute` | Ten Azure Batch docelowy obiekt obliczeniowy do użycia w tym kroku. |
| `inputs` | Danymi wejściowymi mogą być [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)lub [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `outputs` | Dane wyjściowe to [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) lub [OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `source_directory` | Katalog zawierający pliki binarne modułu, pliki wykonywalne, zestawy itp. |
| `executable` | Nazwa polecenia/pliku wykonywalnego, który zostanie wykonany w ramach tego zadania. |
| `create_pool` | Flaga logiczna wskazująca, czy utworzyć pulę przed uruchomieniem zadania. |
| `delete_batch_job_after_finish` | Flaga logiczna wskazująca, czy usunąć zadanie z konta usługi Batch po zakończeniu. |
| `delete_batch_pool_after_finish` | Flaga logiczna wskazująca, czy usunąć pulę po zakończeniu zadania. |
| `is_positive_exit_code_failure` | Flaga logiczna wskazująca, czy zadanie kończy się niepowodzeniem, jeśli zadanie zakończy się z kodem dodatnim. |
| `vm_image_urn` | Jeśli `create_pool` jest , a maszyna `True` wirtualna używa . `VirtualMachineConfiguration` |
| `pool_id` | Identyfikator puli, w której zostanie uruchomione zadanie. |
| `allow_reuse` | Określa, czy krok powinien ponownie używać poprzednich wyników po ponownym uruchomieniu z tym samymi ustawieniami. |

Poniższy przykład zawiera Azure Batch kroku:

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

### <a name="databricks-step"></a>Krok databricks

| Klucz YAML | Opis |
| ----- | ----- |
| `compute` | Docelowy Azure Databricks obliczeniowy do użycia w tym kroku. |
| `inputs` | Danymi wejściowymi mogą być [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)lub [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `outputs` | Dane wyjściowe to [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) lub [OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `run_name` | Nazwa tego uruchomienia w ucieku Databricks. |
| `source_directory` | Katalog zawierający skrypt i inne pliki. |
| `num_workers` | Statyczna liczba węzłów pracy dla klastra uruchamiania usługi Databricks. |
| `runconfig` | Ścieżka do `.runconfig` pliku. Ten plik jest reprezentacją YAML [klasy RunConfiguration.](/python/api/azureml-core/azureml.core.runconfiguration) Aby uzyskać więcej informacji na temat struktury tego pliku, zobaczrunconfigschema.js[ na stronie](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Określa, czy krok powinien ponownie używać poprzednich wyników po ponownym uruchomieniu z tym samymi ustawieniami. |

Poniższy przykład zawiera krok databricks:

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
| `compute` | Docelowy Azure Data Factory obliczeniowy do użycia w tym kroku. |
| `source_data_reference` | Połączenie wejściowe, które służy jako źródło operacji transferu danych. Obsługiwane wartości to [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)lub [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `destination_data_reference` | Połączenie wejściowe, które służy jako miejsce docelowe operacji transferu danych. Obsługiwane wartości to [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) i [OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `allow_reuse` | Określa, czy krok powinien ponownie używać poprzednich wyników po ponownym uruchomieniu z tym samymi ustawieniami. |

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
| `inputs` | Danymi wejściowymi mogą być [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)lub [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `outputs` | Dane wyjściowe mogą być: [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) lub [OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `script_name` | Nazwa skryptu języka Python (względem `source_directory` ). |
| `source_directory` | Katalog zawierający skrypt, środowisko Conda itp. |
| `runconfig` | Ścieżka do `.runconfig` pliku. Ten plik jest reprezentacją YAML [klasy RunConfiguration.](/python/api/azureml-core/azureml.core.runconfiguration) Aby uzyskać więcej informacji na temat struktury tego pliku, zobacz temat [runconfig.jsna stronie](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Określa, czy krok powinien ponownie używać poprzednich wyników po ponownym uruchomieniu z tym samymi ustawieniami. |

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

### <a name="parallel-run-step"></a>Krok uruchamiania równoległego

| Klucz YAML | Opis |
| ----- | ----- |
| `inputs` | Dane wejściowe mogą być [zestawem danych,](/python/api/azureml-core/azureml.core.dataset%28class%29) [elementem DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)lub [pipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `outputs` | Dane wyjściowe mogą być: [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) lub [OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `script_name` | Nazwa skryptu języka Python (względem `source_directory` ). |
| `source_directory` | Katalog zawierający skrypt, środowisko Conda itp. |
| `parallel_run_config` | Ścieżka do `parallel_run_config.yml` pliku. Ten plik jest reprezentacją YAML klasy [ParallelRunConfig.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig) |
| `allow_reuse` | Określa, czy krok powinien ponownie używać poprzednich wyników po ponownym uruchomieniu z tym samymi ustawieniami. |

Poniższy przykład zawiera krok przebiegów równoległych:

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
| `steps` | Sekwencja co najmniej jednej definicji PipelineStep. Należy pamiętać, że klucze jednego kroku stają się kluczami `destination` do obiektu w następnym `outputs` `source` `inputs` kroku.| 

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

Podczas definiowania harmonogramu dla potoku może on być wyzwalany przez magazyn danych lub cykliczny na podstawie przedziału czasu. Poniżej przedstawiono klucze używane do definiowania harmonogramu:

| Klucz YAML | Opis |
| ----- | ----- |
| `description` | Opis harmonogramu. |
| `recurrence` | Zawiera ustawienia cyklu, jeśli harmonogram jest cykliczny. |
| `pipeline_parameters` | Wszelkie parametry, które są wymagane przez potok. |
| `wait_for_provisioning` | Czy czekać na aprowizowanie harmonogramu do ukończenia. |
| `wait_timeout` | Liczba sekund oczekiwania przed przekierowywem. |
| `datastore_name` | Magazyn danych do monitorowania zmodyfikowanych/dodanych obiektów blob. |
| `polling_interval` | Jak długo (w minutach) między sondowaniem zmodyfikowanych/dodanych obiektów blob. Wartość domyślna: 5 minut. Obsługiwane tylko w przypadku harmonogramów magazynu danych. |
| `data_path_parameter_name` | Nazwa parametru potoku ścieżki danych, który ma zostać ustawiony na zmienioną ścieżkę obiektu blob. Obsługiwane tylko w przypadku harmonogramów magazynu danych. |
| `continue_on_step_failure` | Określa, czy kontynuować wykonywanie innych kroków w przesłanym potoku PipelineRun, jeśli krok zakończy się niepowodzeniem. Jeśli zostanie podany, program `continue_on_step_failure` zastąpi ustawienie potoku.
| `path_on_datastore` | Opcjonalny. Ścieżka w sklepie danych do monitorowania zmodyfikowanych/dodanych obiektów blob. Ścieżka znajduje się w kontenerze magazynu danych, więc rzeczywista ścieżka monitorów harmonogramu to `path_on_datastore` kontener/. Jeśli nie, kontener magazynu danych jest monitorowany. Dodatki/modyfikacje wprowadzone w podfolderze nie `path_on_datastore` są monitorowane. Obsługiwane tylko w przypadku harmonogramów magazynu danych. |

Poniższy przykład zawiera definicję harmonogramu wyzwalanej przez magazyn danych:

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

Podczas definiowania harmonogramu **cyklicznego** użyj następujących kluczy w obszarze `recurrence` :

| Klucz YAML | Opis |
| ----- | ----- |
| `frequency` | Jak często harmonogram powtarza się. Prawidłowe wartości to `"Minute"` , , , lub `"Hour"` `"Day"` `"Week"` `"Month"` . |
| `interval` | Jak często jest wyzjęty harmonogram. Wartość całkowita to liczba jednostek czasu oczekiwania na kolejny czas działania harmonogramu. |
| `start_time` | Godzina rozpoczęcia harmonogramu. Format ciągu wartości to `YYYY-MM-DDThh:mm:ss` . Jeśli nie podano czasu rozpoczęcia, pierwsze obciążenie jest uruchamiane natychmiast, a przyszłe obciążenia są uruchamiane zgodnie z harmonogramem. Jeśli czas rozpoczęcia jest w przeszłości, pierwsze obciążenie jest uruchamiane w następnym obliczonym czasie uruchamiania. |
| `time_zone` | Strefa czasowa dla czasu rozpoczęcia. Jeśli nie podano strefy czasowej, używany jest czas UTC. |
| `hours` | Jeśli wartość to lub , można określić co najmniej jedną liczbę całkowitą z od 0 do `frequency` `"Day"` 23 rozdzieloną przecinkami jako godziny dnia, w których powinien `"Week"` zostać uruchomiony potok. Można używać tylko lub `time_of_day` `hours` i `minutes` . |
| `minutes` | Jeśli wartość to lub , można określić co najmniej jedną liczbę całkowitą z od 0 do `frequency` 59 rozdzieloną przecinkami jako minuty godziny uruchomienia `"Day"` `"Week"` potoku. Można używać tylko lub `time_of_day` `hours` i `minutes` . |
| `time_of_day` | Jeśli wartość to lub , możesz określić czas `frequency` `"Day"` `"Week"` dnia, o który ma zostać uruchomiony harmonogram. Format ciągu wartości to `hh:mm` . Można używać tylko lub `time_of_day` `hours` i `minutes` . |
| `week_days` | Jeśli wartość to , można określić co najmniej jeden dzień, rozdzielając je przecinkami, kiedy `frequency` `"Week"` powinien zostać uruchomiony harmonogram. Prawidłowe wartości to `"Monday"` , , , , , i `"Tuesday"` `"Wednesday"` `"Thursday"` `"Friday"` `"Saturday"` `"Sunday"` . |

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

Dowiedz się, [jak używać rozszerzenia interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md).
