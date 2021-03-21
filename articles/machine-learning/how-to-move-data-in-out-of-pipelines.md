---
title: Przeniesienie danych w potokach ML
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak Azure Machine Learning potoki pozyskiwania danych oraz jak zarządzać i przenosić dane między etapami potoku.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 02/26/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, devx-track-python, data4ml
ms.openlocfilehash: a4d1d1c4f4d6354d0206bf598a0622112dc99453
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518708"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Przenoszenie danych do kroków potoku uczenia maszynowego i między nimi (Python)

Ten artykuł zawiera kod służący do importowania, przekształcania i przemieszczania danych między krokami w potoku Azure Machine Learning. Aby zapoznać się z omówieniem sposobu działania danych w Azure Machine Learning, zobacz [dostęp do danych w usługach Azure Storage](how-to-access-data.md). Aby uzyskać informacje na temat korzyści i struktury potoków Azure Machine Learning, zobacz [co to są potoki Azure Machine Learning?](concept-ml-pipelines.md).

W tym artykule przedstawiono, jak:

- Użyj `Dataset` obiektów dla wstępnie istniejących danych
- Dostęp do danych w ramach kroków
- Podziel `Dataset` dane na podzbiory, takie jak szkolenia i podzbiory walidacji
- Tworzenie `OutputFileDatasetConfig` obiektów do przeniesienia danych do następnego kroku potoku
- Użyj `OutputFileDatasetConfig` obiektów jako danych wejściowych dla kroków potoku
- Utwórz nowe `Dataset` obiekty z `OutputFileDatasetConfig` wisƒh, aby zachować

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne będą następujące elementy:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

- [Zestaw Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/intro)lub dostęp do programu [Azure Machine Learning Studio](https://ml.azure.com/).

- Obszar roboczy usługi Azure Machine Learning.
  
  [Utwórz obszar roboczy Azure Machine Learning](how-to-manage-workspace.md) lub Użyj istniejącego z nich za pomocą zestawu SDK języka Python. Zaimportuj `Workspace` klasę i i `Datastore` Załaduj informacje o subskrypcji z pliku `config.json` przy użyciu funkcji `from_config()` . Ta funkcja szuka domyślnego pliku JSON w bieżącym katalogu, ale można także określić parametr ścieżki, aby wskazać plik przy użyciu `from_config(path="your/file/path")` .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Niektóre istniejące wcześniej dane. W tym artykule krótko przedstawiono użycie [kontenera obiektów blob platformy Azure](../storage/blobs/storage-blobs-overview.md).

- Opcjonalnie: istniejący potok uczenia maszynowego, taki jak opisany w temacie [Tworzenie i uruchamianie potoków uczenia maszynowego z zestawem SDK Azure Machine Learning](./how-to-create-machine-learning-pipelines.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>Użyj `Dataset` obiektów dla wstępnie istniejących danych 

Preferowanym sposobem pozyskiwania danych do potoku jest użycie obiektu [zestawu danych](/python/api/azureml-core/azureml.core.dataset%28class%29) . `Dataset` obiekty reprezentują dane trwałe dostępne w obszarze roboczym.

Istnieje wiele sposobów tworzenia i rejestrowania `Dataset` obiektów. Tabelaryczne zestawy danych to dane, które są dostępne w jednym lub kilku plikach. Zestawy danych plików to dane binarne (takie jak obrazy) lub dla analizowanych danych. Najprostszym sposobem tworzenia `Dataset` obiektów jest korzystanie z istniejących bloków BLOB w magazynie obszarów roboczych lub publicznych adresów URL:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

datastore_path = [
    DataPath(datastore, 'animals/dog/1.jpg'),
    DataPath(datastore, 'animals/dog/2.jpg'),
    DataPath(datastore, 'animals/cat/*.jpg')
]
cats_dogs_dataset = Dataset.File.from_files(path=datastore_path)
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

## <a name="use-outputfiledatasetconfig-for-intermediate-data"></a>Użyj `OutputFileDatasetConfig` dla danych pośrednich

Gdy `Dataset` obiekty reprezentują tylko trwałe dane, [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig) można używać obiektów do tymczasowego wyprowadzania danych z kroków potoku **i** trwałych danych wyjściowych. `OutputFileDatasetConfig` obsługuje zapisywanie danych do magazynu obiektów blob, plików adlsgen1 lub adlsgen2. Obsługuje zarówno tryb instalacji, jak i tryb przekazywania. W trybie instalacji pliki zapisane w zainstalowanym katalogu są trwale przechowywane, gdy plik jest zamknięty. W trybie przekazywania pliki zapisywane w katalogu wyjściowym są przekazywane na końcu zadania. Jeśli zadanie nie powiedzie się lub zostanie anulowane, katalog wyjściowy nie zostanie przekazany.

 `OutputFileDatasetConfig` domyślne zachowanie obiektu to zapis w domyślnym magazynie danych obszaru roboczego. Przekaż swoje `OutputFileDatasetConfig` obiekty do `PythonScriptStep` `arguments` parametru.

```python
from azureml.data import OutputFileDatasetConfig
dataprep_output = OutputFileDatasetConfig()
input_dataset = Dataset.get_by_name(workspace, 'raw_data')

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
```

Po `OutputFileDatasetConfig` zakończeniu przebiegu można przekazać zawartość obiektu. W takim przypadku należy użyć `as_upload()` funkcji wraz z `OutputFileDatasetConfig` obiektem i określić, czy zastąpić istniejące pliki w miejscu docelowym. 

```python
#get blob datastore already registered with the workspace
blob_store= ws.datastores['my_blob_store']
OutputFileDatasetConfig(name="clean_data", destination=(blob_store, 'outputdataset')).as_upload(overwrite=False)
```

> [!NOTE]
> Współbieżne operacje zapisu w usłudze zakończą `OutputFileDatasetConfig` się niepowodzeniem. Nie należy próbować korzystać z jednej `OutputFileDatasetConfig` współbieżności. Nie należy udostępniać pojedynczego `OutputFileDatasetConfig` w sytuacji przetwarzania wieloprocesowego, na przykład w przypadku korzystania z szkoleń rozproszonych. 

### <a name="use-outputfiledatasetconfig-as-outputs-of-a-training-step"></a>Użyj `OutputFileDatasetConfig` jako wyjścia kroku szkoleniowego

W kroku `PythonScriptStep` potoku możesz pobrać dostępne ścieżki wyjściowe, używając argumentów programu. Jeśli ten krok jest pierwszy i powoduje inicjowanie danych wyjściowych, należy utworzyć katalog w określonej ścieżce. Następnie można napisać wszystkie pliki, które mają być zawarte w `OutputFileDatasetConfig` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

### <a name="read-outputfiledatasetconfig-as-inputs-to-non-initial-steps"></a>Odczytaj `OutputFileDatasetConfig` jako dane wejściowe do kroków niepoczątkowych

Po przepisaniu przez początkowy krok potoku danych do `OutputFileDatasetConfig` ścieżki i stanie się on wyjściem tego kroku, można go użyć jako danych wejściowych w późniejszym kroku. 

W poniższym kodzie: 

* `step1_output_data` wskazuje, że dane wyjściowe PythonScriptStep `step1` są zapisywane w magazynie danych ADLS generacji 2 `my_adlsgen2` w trybie dostępu do przekazywania. Dowiedz się więcej na temat sposobu [konfigurowania uprawnień roli](how-to-access-data.md#azure-data-lake-storage-generation-2) w celu zapisywania danych z powrotem do magazynu ADLS generacji 2. 

* Po `step1` zakończeniu i dane wyjściowe są zapisywane w miejscu docelowym wskazywanym przez `step1_output_data` , a następnie step2 jest gotowy do użycia `step1_output_data` jako dane wejściowe. 

```python
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']
step1_output_data = OutputFileDatasetConfig(name="processed_data", destination=(datastore, "mypath/{run-id}/{output-name}")).as_upload()

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data.as_input()]

)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

## <a name="register-outputfiledatasetconfig-objects-for-reuse"></a>Rejestrowanie `OutputFileDatasetConfig` obiektów do ponownego użycia

Jeśli chcesz, aby były `OutputFileDatasetConfig` dostępne dłużej niż w czasie trwania eksperymentu, zarejestruj je w obszarze roboczym, aby udostępniać i ponownie używać w ramach eksperymentów.

```python
step1_output_ds = step1_output_data.register_on_complete(name='processed_data', 
                                                         description = 'files from step1`)
```

## <a name="delete-outputfiledatasetconfig-contents-when-no-longer-needed"></a>Usuń `OutputFileDatasetConfig` zawartość, gdy nie jest już potrzebne

Platforma Azure nie usuwa automatycznie danych pośrednich, które są zapisywane z `OutputFileDatasetConfig` . Aby uniknąć opłat za magazyn w przypadku dużych ilości niepotrzebnych danych, należy:

* Programowe usuwanie danych pośrednich na końcu uruchomienia potoku, gdy nie jest już potrzebne
* Używanie usługi BLOB Storage z krótkoterminowymi zasadami magazynowania dla danych pośrednich (zobacz [Optymalizacja kosztów dzięki automatyzowaniu warstw dostępu BLOB Storage platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)) 
* Regularnie Przeglądaj i usuwaj dane, które nie są już potrzebne

Aby uzyskać więcej informacji, zobacz [Planowanie i zarządzanie kosztami Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie zestawu danych usługi Azure Machine Learning](how-to-create-register-datasets.md)
* [Tworzenie i uruchamianie potoków uczenia maszynowego za pomocą zestawu SDK Azure Machine Learning](./how-to-create-machine-learning-pipelines.md)