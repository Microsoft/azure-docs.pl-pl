---
title: Przeniesienie danych w potokach ML
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak Azure Machine Learning potoki pozyskiwania danych oraz jak zarządzać i przenosić dane między etapami potoku.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 08/20/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, devx-track-python, data4ml
ms.openlocfilehash: 8a5663df590e0f617f8049f0201e6e508351c755
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027581"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Przenoszenie danych do kroków potoku uczenia maszynowego i między nimi (Python)



Ten artykuł zawiera kod służący do importowania, przekształcania i przemieszczania danych między krokami w potoku Azure Machine Learning. Aby zapoznać się z omówieniem sposobu działania danych w Azure Machine Learning, zobacz [dostęp do danych w usługach Azure Storage](how-to-access-data.md). Aby uzyskać informacje na temat korzyści i struktury potoków Azure Machine Learning, zobacz [co to są potoki Azure Machine Learning?](concept-ml-pipelines.md).

W tym artykule przedstawiono, jak:

- Użyj `Dataset` obiektów dla wstępnie istniejących danych
- Dostęp do danych w ramach kroków
- Podziel `Dataset` dane na podzbiory, takie jak szkolenia i podzbiory walidacji
- Tworzenie `PipelineData` obiektów do przeniesienia danych do następnego kroku potoku
- Użyj `PipelineData` obiektów jako danych wejściowych dla kroków potoku
- Utwórz nowe `Dataset` obiekty `PipelineData` , które chcesz zachować

> [!TIP]
> Udoskonalone środowisko przekazywania danych tymczasowych między etapami potoku i utrwalanie danych po uruchomieniu potoków jest dostępne w publicznych klasach wersji zapoznawczych  [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) i [`OutputTabularDatasetConfig`](/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig?preserve-view=true&view=azure-ml-py) .  Te klasy to [eksperymentalne](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#&preserve-view=truestable-vs-experimental) funkcje w wersji zapoznawczej i mogą ulec zmianie w dowolnym momencie.


## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne będą następujące elementy:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

- [Zestaw Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)lub dostęp do programu [Azure Machine Learning Studio](https://ml.azure.com/).

- Obszar roboczy usługi Azure Machine Learning.
  
  [Utwórz obszar roboczy Azure Machine Learning](how-to-manage-workspace.md) lub Użyj istniejącego z nich za pomocą zestawu SDK języka Python. Zaimportuj `Workspace` klasę i i `Datastore` Załaduj informacje o subskrypcji z pliku `config.json` przy użyciu funkcji `from_config()` . Ta funkcja szuka domyślnego pliku JSON w bieżącym katalogu, ale można także określić parametr ścieżki, aby wskazać plik przy użyciu `from_config(path="your/file/path")` .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Niektóre istniejące wcześniej dane. W tym artykule krótko przedstawiono użycie [kontenera obiektów blob platformy Azure](../storage/blobs/storage-blobs-overview.md).

- Opcjonalnie: istniejący potok uczenia maszynowego, taki jak opisany w temacie [Tworzenie i uruchamianie potoków uczenia maszynowego z zestawem SDK Azure Machine Learning](how-to-create-your-first-pipeline.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>Użyj `Dataset` obiektów dla wstępnie istniejących danych 

Preferowanym sposobem pozyskiwania danych do potoku jest użycie obiektu [zestawu danych](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py) . `Dataset` obiekty reprezentują dane trwałe dostępne w obszarze roboczym.

Istnieje wiele sposobów tworzenia i rejestrowania `Dataset` obiektów. Tabelaryczne zestawy danych to dane, które są dostępne w jednym lub kilku plikach. Zestawy danych plików to dane binarne (takie jak obrazy) lub dla analizowanych danych. Najprostszym sposobem tworzenia `Dataset` obiektów jest korzystanie z istniejących bloków BLOB w magazynie obszarów roboczych lub publicznych adresów URL:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Aby uzyskać więcej opcji tworzenia zestawów danych z różnymi opcjami i z różnych źródeł, zarejestrowanie ich i przeglądanie w interfejsie użytkownika Azure Machine Learning, zrozumienie, jak rozmiar danych współdziała z pojemnością obliczeniową, i przechowywanie ich wersji, zobacz [Create Azure Machine Learning DataSets](how-to-create-register-datasets.md). 

### <a name="pass-datasets-to-your-script"></a>Przekazywanie zestawów danych do skryptu

Aby przekazać ścieżkę zestawu danych do skryptu, użyj `Dataset` `as_named_input()` metody obiektu. Można przekazać `DatasetConsumptionConfig` obiekt wyników do skryptu jako argument lub, przy użyciu `inputs` argumentu do skryptu potoku, można pobrać zestaw danych przy użyciu `Run.get_context().input_datasets[]` .

Po utworzeniu nazwanego wejścia możesz wybrać jego tryb dostępu: `as_mount()` lub `as_download()` . Jeśli skrypt przetwarza wszystkie pliki w zestawie danych, a dysk w zasobie obliczeniowym jest wystarczająco duży dla zestawu danych, lepszym wyborem będzie tryb dostępu do pobierania. Tryb dostępu do pobierania pozwoli uniknąć obciążenia przesyłania strumieniowego danych w czasie wykonywania. Jeśli skrypt uzyskuje dostęp do podzbioru zestawu danych lub jest zbyt duży dla obliczeń, użyj trybu dostępu do instalacji. Aby uzyskać więcej informacji, zapoznaj się z tematem [Instalowanie i pobieranie plików.](./how-to-train-with-datasets.md#mount-vs-download)

Aby przekazać zestaw danych do etapu potoku:

1. Użyj `TabularDataset.as_named_input()` lub `FileDataset.as_named_input()` (brak "na końcu"), aby utworzyć `DatasetConsumptionConfig` obiekt
1. Użyj `as_mount()` lub, `as_download()` Aby ustawić tryb dostępu
1. Przekaż zestawy danych do etapów potoku przy użyciu `arguments` albo `inputs` argumentu

Poniższy fragment kodu przedstawia wspólny wzorzec łączenia tych kroków w `PythonScriptStep` konstruktorze: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_input('iris').as_mount()]
)
```

> [!NOTE]
> Należy zastąpić wartości dla wszystkich tych argumentów (to oznacza,,, `"train_data"` `"train.py"` `cluster` i `iris_dataset` ) własnymi danymi. Powyższy fragment kodu pokazuje formę wywołania i nie jest częścią przykładu firmy Microsoft. 

Można również użyć metod, takich jak `random_split()` i, `take_sample()` Aby utworzyć wiele danych wejściowych lub zmniejszyć ilość przesyłanych do etapu potoku:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_input('train').as_download(), test.as_named_input('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Dostęp do zestawów danych w skrypcie

Nazwane dane wejściowe do skryptu kroku potoku są dostępne jako słownik w `Run` obiekcie. Pobierz aktywny `Run` obiekt za pomocą `Run.get_context()` , a następnie Pobierz słownik nazwanych wejść przy użyciu `input_datasets` . Jeśli przeszedł `DatasetConsumptionConfig` Obiekt przy użyciu `arguments` argumentu zamiast `inputs` argumentu, uzyskaj dostęp do danych przy użyciu `ArgParser` kodu. Obie techniki przedstawiono w poniższym fragmencie kodu.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_input('train').as_download()]
    inputs=[test.as_named_input('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

Przeniesiona wartość będzie ścieżką do plików zestawu danych.

Istnieje również możliwość bezpośredniego dostępu do zarejestrowanych danych `Dataset` . Ponieważ zarejestrowane zestawy danych są trwałe i udostępniane w obszarze roboczym, można je pobrać bezpośrednio:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

> [!NOTE]
> Poprzednie fragmenty kodu pokazują formę wywołań i nie są częścią przykładu firmy Microsoft. Należy zamienić różne argumenty na wartości z własnego projektu.

## <a name="use-pipelinedata-for-intermediate-data"></a>Użyj `PipelineData` dla danych pośrednich

Gdy `Dataset` obiekty reprezentują dane trwałe, obiekty [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) są używane dla danych tymczasowych, które są wyprowadzane z kroków potoku. Ponieważ cykl życia `PipelineData` obiektu jest dłuższy niż pojedynczy krok potoku, można je zdefiniować w skrypcie definicji potoku. Podczas tworzenia `PipelineData` obiektu należy podać nazwę i magazyn danych, w których będą znajdować się dane. Przekaż swoje `PipelineData` obiekty do `PythonScriptStep` _obu_ `arguments` `outputs` argumentów i:

```python

default_datastore = workspace.get_default_datastore()
dataprep_output = PipelineData("clean_data", datastore=default_datastore)

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=["--output-path", dataprep_output]
    inputs=[Dataset.get_by_name(workspace, 'raw_data')],
    outputs=[dataprep_output]
)

```

Można utworzyć `PipelineData` Obiekt przy użyciu trybu dostępu, który zapewnia natychmiastowe przekazywanie. W takim przypadku podczas tworzenia `PipelineData` należy ustawić `upload_mode` do `"upload"` i użyć `output_path_on_compute` argumentu, aby określić ścieżkę, do której będą zapisywane dane:

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

> [!NOTE]
> Poprzednie fragmenty kodu pokazują formę wywołań i nie są częścią przykładu firmy Microsoft. Należy zamienić różne argumenty na wartości z własnego projektu.

> [!TIP]
> Udoskonalone środowisko przekazywania pośrednich danych między etapami potoku jest dostępne z publiczną klasą zapoznawczą [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) . Aby zapoznać się z przykładem kodu przy użyciu `OutputFileDatasetConfig` , zobacz jak [utworzyć dwa etap potoku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).


### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Użyj `PipelineData` jako wyjścia kroku szkoleniowego
W ramach potoku `PythonScriptStep` można pobrać dostępne ścieżki wyjściowe przy użyciu argumentów programu. Jeśli ten krok jest pierwszy i spowoduje zainicjowanie danych wyjściowych, należy utworzyć katalog w określonej ścieżce. Następnie można napisać wszystkie pliki, które mają być zawarte w `PipelineData` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

Jeśli został utworzony `PipelineData` z `is_directory` argumentem ustawionym na `True` , wystarczy wykonać `os.makedirs()` wywołanie, a następnie będzie można napisać dowolne pliki do ścieżki. Aby uzyskać więcej informacji, zobacz dokumentację referencyjną [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) .


### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Odczytaj `PipelineData` jako dane wejściowe do kroków niepoczątkowych

Po przepisaniu przez początkowy krok potoku danych do `PipelineData` ścieżki i stanie się on wyjściem tego kroku, można go użyć jako danych wejściowych w późniejszym kroku:

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data],
    inputs=[],
    outputs=[step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data],
    inputs=[step1_output_data]
)
pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

Wartość `PipelineData` wejściowa jest ścieżką do poprzednich danych wyjściowych. 

> [!NOTE]
> Poprzednie fragmenty kodu pokazują formę wywołań i nie są częścią przykładu firmy Microsoft. Należy zamienić różne argumenty na wartości z własnego projektu.

> [!TIP]
> Udoskonalone środowisko przekazywania pośrednich danych między etapami potoku jest dostępne z publiczną klasą zapoznawczą [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) . Aby zapoznać się z przykładem kodu przy użyciu `OutputFileDatasetConfig` , zobacz jak [utworzyć dwa etap potoku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).

Jeśli tak, jak pokazano wcześniej, pierwszy krok został napisany pojedynczym plikiem, a jego używanie może wyglądać następująco: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()
with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>Konwertuj `PipelineData` obiekty na `Dataset` s

Jeśli chcesz, aby były `PipelineData` dostępne dłużej niż czas trwania przebiegu, użyj `as_dataset()` funkcji, aby przekonwertować ją na `Dataset` . Następnie możesz zarejestrować się `Dataset` , tworząc jako obywatela pierwszej klasy w Twoim obszarze roboczym. Ponieważ `PipelineData` obiekt będzie miał inną ścieżkę przy każdym uruchomieniu potoku, zdecydowanie zaleca się, `create_new_version` Aby `True` podczas rejestrowania `Dataset` utworzonego na podstawie `PipelineData` obiektu był ustawiony na wartość.

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)

```
> [!TIP]
> Udoskonalone środowisko utrwalania danych pośrednich poza uruchomionymi potokami jest dostępne z klasą publicznej wersji zapoznawczej [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) . Aby zapoznać się z przykładem kodu przy użyciu `OutputFileDatasetConfig` , zobacz jak [utworzyć dwa etap potoku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie zestawu danych usługi Azure Machine Learning](how-to-create-register-datasets.md)
* [Tworzenie i uruchamianie potoków uczenia maszynowego za pomocą zestawu SDK Azure Machine Learning](how-to-create-your-first-pipeline.md)