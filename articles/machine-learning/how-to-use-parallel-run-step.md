---
title: Uruchamianie prognoz wsadowych dotyczących danych Big Data
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uzyskać informacje o liczbie asynchronicznie dla dużych ilości danych przy użyciu ParallelRunStep w Azure Machine Learning. Program ParallelRunStep udostępnia funkcje przetwarzania równoległego z usługi Box i optymalizuje w celu zapewnienia wysokiej przepływności, odporności i zapamiętania w przypadku dużych ilości danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: jmartens, larryfr
ms.author: tracych
author: tracychms
ms.date: 10/13/2020
ms.custom: Build2020, devx-track-python
ms.openlocfilehash: 1e55a8e8dd78a6093c875cd20dd8b240816e7426
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92055107"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Uruchamiaj wnioskowanie wsadowe dla dużych ilości danych za pomocą Azure Machine Learning


W tym artykule opisano, jak uruchamiać równolegle modele usługi Azure Machine Learning, co umożliwia szybką ocenę dużych ilości danych. 

Inferencing z dużymi zestawami danych lub skomplikowanymi modelami mogą być czasochłonne. `ParallelRunStep`Klasa umożliwia przeprowadzenie przetwarzania równolegle, co może spowodować szybsze uzyskanie wyników. Nawet w przypadku uruchamiania pojedynczej oceny jest dość duża szybkość, wiele scenariuszy (wykrywanie obiektów, przetwarzanie wideo, przetwarzanie języka naturalnego itp.) obejmuje uruchamianie wielu ocen. 

Dzięki `ParallelRunStep` programowi można skalować wnioskowanie wsadowe do dużych klastrów maszyn. Takie klastry mogą obsługiwać terabajty danych ze strukturą lub bez struktury dzięki ulepszonej produktywności i zoptymalizowanym kosztom.

> [!NOTE]
> ParallelRunStep jest przeznaczony do obsługi obciążeń równoległych zaskakująco, może być również używany do uczenia wielu modeli jednocześnie lub przetwarzania dużych ilości danych.

Ten artykuł zawiera informacje o następujących zadaniach:

> 1. Konfigurowanie zasobów uczenia maszynowego.
> 1. Skonfiguruj dane wejściowe i wyjściowe danych wnioskowania partii.
> 1. Przygotuj wstępnie szkolony model klasyfikacji obrazów oparty na [mnist ręcznie](https://publicdataset.azurewebsites.net/dataDetail/mnist/) zestawie danych. 
> 1.  Napisz skrypt wnioskowania.
> 1. Utwórz [potok uczenia maszynowego](concept-ml-pipelines.md) zawierający ParallelRunStep i uruchom wnioskowanie wsadowe na obrazach testów mnist ręcznie. 
> 1. Prześlij ponownie żądanie wnioskowania partii o nowe dane wejściowe i parametry. 
> 1. Przejrzyj wyniki.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

* Aby zapoznać się z przewodnikiem Szybki Start, Wypełnij [samouczek Instalatora](tutorial-1st-experiment-sdk-setup.md) , jeśli nie masz jeszcze obszaru roboczego Azure Machine Learning. 

* Aby zarządzać własnym środowiskiem i zależnościami, zobacz [Przewodnik](how-to-configure-environment.md) po konfigurowaniu własnego środowiska lokalnego.

## <a name="set-up-machine-learning-resources"></a>Konfigurowanie zasobów uczenia maszynowego

Poniższe akcje umożliwiają skonfigurowanie zasobów uczenia maszynowego, które są potrzebne do uruchomienia potoku wnioskowania w usłudze Batch:

- Połącz z obszarem roboczym.
- Utwórz lub Dołącz istniejący zasób obliczeniowy.

### <a name="configure-workspace"></a>Konfigurowanie obszaru roboczego

Utwórz obiekt obszaru roboczego na podstawie istniejącego obszaru roboczego. `Workspace.from_config()` odczytuje config.jsw pliku i ładuje szczegóły do obiektu o nazwie WS.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

> [!IMPORTANT]
> Ten fragment kodu oczekuje, że Konfiguracja obszaru roboczego ma zostać zapisana w bieżącym katalogu lub jego elemencie nadrzędnym. Aby uzyskać więcej informacji na temat tworzenia obszaru roboczego, zobacz [Tworzenie obszarów roboczych i zarządzanie nimi Azure Machine Learning](how-to-manage-workspace.md). Aby uzyskać więcej informacji na temat zapisywania konfiguracji do pliku, zobacz [Tworzenie pliku konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).

### <a name="create-a-compute-target"></a>Tworzenie obiektu docelowego obliczeń

W Azure Machine Learning, *obliczenia* (lub *element docelowy obliczeń*) odnoszą się do maszyn lub klastrów wykonujących kroki obliczeniowe w potoku uczenia maszynowego. Uruchom następujący kod, aby utworzyć obiekt docelowy [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py&preserve-view=true) opartego na procesorach.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

## <a name="configure-inputs-and-output"></a>Konfigurowanie danych wejściowych i wyjściowych

### <a name="create-a-datastore-with-sample-images"></a>Tworzenie magazynu danych z przykładowymi obrazami

Pobierz zestaw ocen MNIST ręcznie z publicznego kontenera obiektów BLOB `sampledata` na koncie o nazwie `pipelinedata` . Utwórz magazyn danych o nazwie `mnist_datastore` , który wskazuje na ten kontener. W poniższym wywołaniu `register_azure_blob_container` , ustawiając `overwrite` flagę w celu `True` zastępowanie wszelkich magazynów danych, które zostały utworzone wcześniej przy użyciu tej nazwy. 

Możesz zmienić ten krok, aby wskazywał kontener obiektów blob, dostarczając własne wartości dla `datastore_name` , `container_name` i `account_name` .

```python
from azureml.core import Datastore
from azureml.core import Workspace

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

Następnie określ domyślny magazyn danych obszaru roboczego jako dane wyjściowe. Zostanie ona użyta do wnioskowania danych wyjściowych.

Podczas tworzenia obszaru roboczego [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)   i [Magazyn obiektów BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)   są domyślnie dołączone do obszaru roboczego. Azure Files jest domyślnym magazynem danych dla obszaru roboczego, ale można również użyć magazynu obiektów BLOB jako magazynu danych. Aby uzyskać więcej informacji, zobacz [Opcje usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="create-the-data-inputs"></a>Tworzenie danych wejściowych

Dane wejściowe dotyczące wnioskowania wsadowego są danymi, które mają być partycjonowane na potrzeby przetwarzania równoległego. Potok wnioskowania partii akceptuje dane wejściowe za pomocą [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py&preserve-view=true) .

`Dataset` służy do eksplorowania i przekształcania danych oraz zarządzania nimi w Azure Machine Learning. Istnieją dwa typy: [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) i [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py&preserve-view=true) . W tym przykładzie użyjesz `FileDataset` jako danych wejściowych. `FileDataset` zapewnia możliwość pobierania lub instalowania plików na potrzeby obliczeń. Utworzenie zestawu danych powoduje utworzenie odwołania do lokalizacji źródła danych. Jeśli zastosowano jakiekolwiek przekształcenia PodUstawienia do zestawu danych, zostaną one również zapisane w zestawie danych. Dane pozostają w istniejącej lokalizacji, więc nie są naliczane żadne dodatkowe koszty związane z magazynem.

Aby uzyskać więcej informacji na temat Azure Machine Learning zestawów danych, zobacz [Tworzenie zestawów danych i uzyskiwanie do nich dostępu (wersja zapoznawcza)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

```python
from azureml.core.dataset import Dataset

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
```

Aby można było używać dynamicznych danych wejściowych podczas uruchamiania potoku wnioskowania partii, możesz zdefiniować dane wejściowe `Dataset` jako [`PipelineParameter`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py&preserve-view=true) . Możesz określić zestaw danych wejściowych za każdym razem, gdy ponownie zostanie uruchomiony potok wnioskowania o partie partii.

```python
from azureml.data.dataset_consumption_config import DatasetConsumptionConfig
from azureml.pipeline.core import PipelineParameter

pipeline_param = PipelineParameter(name="mnist_param", default_value=input_mnist_ds)
input_mnist_ds_consumption = DatasetConsumptionConfig("minist_param_config", pipeline_param).as_mount()
```

### <a name="create-the-output"></a>Tworzenie danych wyjściowych

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) obiekty służą do transferowania danych pośrednich między etapami potoku. W tym przykładzie używa się go do wnioskowania danych wyjściowych.

```python
from azureml.pipeline.core import Pipeline, PipelineData

output_dir = PipelineData(name="inferences", datastore=def_data_store)
```

## <a name="prepare-the-model"></a>Przygotuj model

[Pobierz model klasyfikacji wstępnie nauczonego obrazu](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz), a następnie wyodrębnij go do `models` katalogu.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

Następnie zarejestruj model w obszarze roboczym, aby był dostępny dla zasobu obliczeniowego.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Napisz skrypt wnioskowania

>[!Warning]
>Poniższy kod jest tylko przykładem, który używa [przykładowego notesu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run) . Musisz utworzyć własny skrypt dla danego scenariusza.

Skrypt *musi zawierać* dwie funkcje:
- `init()`: Ta funkcja jest używana do dowolnych kosztownych lub wspólnych przygotowań do późniejszego wnioskowania. Na przykład użyj go do załadowania modelu do obiektu globalnego. Ta funkcja zostanie wywołana tylko raz na początku procesu.
-  `run(mini_batch)`: Funkcja zostanie uruchomiona dla każdego `mini_batch` wystąpienia.
    -  `mini_batch`: `ParallelRunStep` spowoduje wywołanie metody Run i przekazanie listy lub Pandas `DataFrame` jako argumentu do metody. Każdy wpis w mini_batch będzie ścieżką pliku, jeśli dane wejściowe to, `FileDataset` lub Pandas, `DataFrame` Jeśli dane wejściowe to `TabularDataset` .
    -  `response`: Metoda Run () powinna zwracać element Pandas `DataFrame` lub tablicę. W przypadku append_row output_action te zwrócone elementy są dołączane do wspólnego pliku wyjściowego. W przypadku summary_only zawartość elementów jest ignorowana. Dla wszystkich akcji wyjściowych każdy zwrócony element wyjściowy wskazuje jeden udany przebieg elementu wejściowego w danych wejściowych. Upewnij się, że wystarczająca ilość danych jest uwzględniona w wyniku uruchomienia, aby zamapować dane wejściowe w celu uruchomienia wyniku. Dane wyjściowe uruchamiania będą zapisywane w pliku wyjściowym i nie będą gwarantowane w kolejności, dlatego należy użyć pewnego klucza w danych wyjściowych, aby zamapować go na dane wejściowe.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Jeśli masz inny plik lub folder w tym samym katalogu, co skrypt wnioskowania, możesz odwoływać się do niego, wyszukując bieżący katalog roboczy.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>Kompiluj i uruchamiaj potok zawierający ParallelRunStep

Teraz masz wszystko, czego potrzebujesz: dane wejściowe, model, dane wyjściowe i skrypt wnioskowania. Utwórzmy potok wnioskowania partii zawierający ParallelRunStep.

### <a name="prepare-the-environment"></a>Przygotowywanie środowiska

Najpierw określ zależności dla skryptu. Umożliwia to zainstalowanie pakietów PIP oraz skonfigurowanie środowiska.

Zawsze dołączaj platformy **Azure — rdzeń** i **Azure-DataSet-Runtime [Pandas, bezpiecznik]** na liście pakietów PIP. Jeśli używasz niestandardowego obrazu platformy Docker (user_managed_dependencies = true), należy również zainstalować Conda.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.15.2", "pillow", 
                                                          "azureml-core", "azureml-dataset-runtime[pandas, fuse]"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="specify-the-parameters-using-parallelrunconfig"></a>Określanie parametrów przy użyciu ParallelRunConfig

`ParallelRunConfig` jest to główna konfiguracja `ParallelRunStep` wystąpienia w potoku Azure Machine Learning. Służy do zawijania skryptu i konfigurowania niezbędnych parametrów, w tym wszystkich następujących wpisów:
- `entry_script`: Skrypt użytkownika jako ścieżka do pliku lokalnego, która będzie uruchamiana równolegle na wielu węzłach. Jeśli `source_directory` jest obecny, należy użyć ścieżki względnej. W przeciwnym razie użyj dowolnej ścieżki dostępnej na komputerze.
- `mini_batch_size`: Rozmiar mini-Batch przeszedł do pojedynczego `run()` wywołania. (opcjonalnie; wartość domyślna to `10` pliki dla `FileDataset` i `1MB` dla `TabularDataset` .)
    - Dla `FileDataset` , jest to liczba plików o minimalnej wartości `1` . Można połączyć wiele plików w jedną minimalną partię.
    - W przypadku `TabularDataset` , jest to rozmiar danych. Przykładowe wartości to `1024` , `1024KB` , `10MB` , i `1GB` . Zalecana wartość to `1MB` . Mini-Batch z `TabularDataset` nigdy nie będzie przekraczać granic plików. Jeśli na przykład pliki CSV mają różne rozmiary, najmniejszy plik to 100 KB, a największy to 10 MB. Jeśli ustawisz `mini_batch_size = 1MB` , pliki o rozmiarze mniejszym niż 1 MB będą traktowane jako jedna mini-Batch. Pliki o rozmiarze większym niż 1 MB zostaną podzielone na wiele kart Mini-Part.
- `error_threshold`: Liczba błędów rekordu `TabularDataset` i błędów plików dla `FileDataset` , które powinny zostać zignorowane podczas przetwarzania. Jeśli liczba błędów dla całego danych wejściowych spadnie powyżej tej wartości, zadanie zostanie przerwane. Próg błędu dotyczy całego danych wejściowych, a nie dla pojedynczego elementu mini-Batch wysyłanego do `run()` metody. Zakresem jest `[-1, int.max]` . `-1`Część wskazuje, że wszystkie błędy zostaną zignorowane podczas przetwarzania.
- `output_action`: Jedna z następujących wartości wskazuje, w jaki sposób dane wyjściowe będą zorganizowane:
    - `summary_only`: Skrypt użytkownika będzie przechowywał dane wyjściowe. `ParallelRunStep` będzie używać danych wyjściowych tylko dla obliczeń progu błędu.
    - `append_row`: W przypadku wszystkich danych wejściowych w folderze wyjściowym zostanie utworzony tylko jeden plik, aby dołączyć wszystkie dane wyjściowe rozdzielone wierszami.
- `append_row_file_name`: Aby dostosować nazwę pliku wyjściowego dla append_row output_action (opcjonalnie wartość domyślna to `parallel_run_step.txt` ).
- `source_directory`: Ścieżki do folderów zawierających wszystkie pliki do wykonania na obiekcie docelowym obliczeń (opcjonalnie).
- `compute_target`: `AmlCompute` Obsługiwane są tylko.
- `node_count`: Liczba węzłów obliczeniowych, które mają być używane do uruchamiania skryptu użytkownika.
- `process_count_per_node`: Liczba procesów na węzeł. Najlepszym rozwiązaniem jest ustawienie liczby procesorów GPU lub CPU jednego węzła (opcjonalnie; wartość domyślna to `1` ).
- `environment`: Definicja środowiska języka Python. Można skonfigurować go tak, aby korzystał z istniejącego środowiska Python lub skonfigurować środowisko tymczasowe. Definicja jest również odpowiedzialna za ustawianie wymaganych zależności aplikacji (opcjonalnie).
- `logging_level`: Poziom szczegółowości dziennika. Wartości zwiększające poziom szczegółowości to: `WARNING` , `INFO` , i `DEBUG` . (opcjonalnie; wartość domyślna to `INFO` )
- `run_invocation_timeout`: `run()` Limit czasu wywołania metody (w sekundach). (opcjonalnie; wartość domyślna to `60` )
- `run_max_try`: Maksymalna liczba prób `run()` dla typu mini-Batch. A `run()` nie powiodło się, jeśli wystąpił wyjątek lub nie jest zwracany, gdy `run_invocation_timeout` zostanie osiągnięty błąd (opcjonalnie; wartość domyślna to `3` ). 

Można określić,,,, `mini_batch_size` `node_count` i tak `process_count_per_node` `logging_level` `run_invocation_timeout` `run_max_try` `PipelineParameter` , aby po ponownym przesłaniu uruchomienia potoku można dostosować wartości parametrów. W tym przykładzie użyto `PipelineParameter` dla i, `mini_batch_size` `Process_count_per_node` a następnie zmienisz te wartości w przypadku ponownego przesłania przebiegu później. 

W tym przykładzie przyjęto założenie, że używasz `digit_identification.py` skryptu, który został omówiony wcześniej. Jeśli używasz własnego skryptu, `source_directory` `entry_script` odpowiednio zmień parametry i.

```python
from azureml.pipeline.core import PipelineParameter
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory='.',
    entry_script="digit_identification.py",
    mini_batch_size=PipelineParameter(name="batch_size_param", default_value="5"),
    error_threshold=10,
    output_action="append_row",
    append_row_file_name="mnist_outputs.txt",
    environment=batch_env,
    compute_target=compute_target,
    process_count_per_node=PipelineParameter(name="process_count_param", default_value=2),
    node_count=2)
```

### <a name="create-the-parallelrunstep"></a>Tworzenie ParallelRunStep

Utwórz ParallelRunStep za pomocą skryptu, konfiguracji środowiska i parametrów. Określ miejsce docelowe obliczeń, które zostało już dołączone do obszaru roboczego jako element docelowy wykonywania skryptu wnioskowania. Użyj `ParallelRunStep` do utworzenia kroku potoku wnioskowania partii, który przyjmuje wszystkie następujące parametry:
- `name`: Nazwa kroku z następującymi ograniczeniami nazewnictwa: Unique, 3-32 znaków i wyrażenie regularne ^ \[ a-z \] ([-a-Z0-9] * [a-Z0-9])? $.
- `parallel_run_config`: `ParallelRunConfig` Obiekt, zgodnie z definicją wcześniejszą.
- `inputs`: Co najmniej jeden zestaw danych z jednym Azure Machine Learning typem, który ma być podzielony na partycje na potrzeby przetwarzania równoległego.
- `side_inputs`: Co najmniej jeden z danych referencyjnych lub zestawów DataSet używany jako dane wejściowe bez konieczności partycjonowania.
- `output`: `PipelineData` Obiekt, który odpowiada katalogowi wyjściowemu.
- `arguments`: Lista argumentów przenoszona do skryptu użytkownika. Użyj unknown_args, aby pobrać je w skrypcie wprowadzania (opcjonalnie).
- `allow_reuse`: Czy krok ma ponownie używać poprzednich wyników w przypadku uruchamiania z tymi samymi ustawieniami/danymi wejściowymi. Jeśli ten parametr ma wartość `False` , nowy przebieg będzie zawsze generowany dla tego kroku podczas wykonywania potoku. (opcjonalnie; wartość domyślna to `True` ).

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```
### <a name="create-and-run-the-pipeline"></a>Tworzenie i uruchamianie potoku

Teraz uruchom potok. Najpierw Utwórz [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py&preserve-view=true) obiekt za pomocą odwołania do obszaru roboczego i utworzonego etapu potoku. `steps`Parametr jest tablicą kroków. W takim przypadku istnieje tylko jeden krok na wnioskowanie partii. Aby utworzyć potoki, które mają wiele kroków, należy umieścić kroki w kolejności w tej tablicy.

Następnie użyj funkcji, `Experiment.submit()` Aby przesłać potok do wykonania.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
experiment = Experiment(ws, 'digit_identification')
pipeline_run = experiment.submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>Monitorowanie zadania wnioskowania partii

Ukończenie zadania wnioskowania partii może zająć dużo czasu. Ten przykład monitoruje postęp przy użyciu widżetu Jupyter. Możesz również monitorować postęp zadania przy użyciu:

* Azure Machine Learning Studio. 
* Dane wyjściowe konsoli z [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py&preserve-view=true) obiektu.

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="resubmit-a-run-with-new-data-inputs-and-parameters"></a>Prześlij ponownie przebieg z nowymi danymi wejściowymi i parametrami

Ze względu na to, że dane wejściowe i kilka konfiguracji są skonfigurowane jako `PipelineParameter` , można ponownie przesłać wnioskowanie o partii z innym zestawem danych i dostosować parametry bez konieczności tworzenia zupełnie nowego potoku. Będziesz korzystać z tego samego magazynu danych, ale tylko jeden obraz jako dane wejściowe.

```python
path_on_datastore = mnist_blob.path('mnist/0.png')
single_image_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)

pipeline_run_2 = experiment.submit(pipeline, 
                                   pipeline_parameters={"mnist_param": single_image_ds, 
                                                        "batch_size_param": "1",
                                                        "process_count_param": 1}
)

pipeline_run_2.wait_for_completion(show_output=True)
```
## <a name="view-the-results"></a>Wyświetlanie wyników

Wyniki z powyższych przebiegów są zapisywane do `DataStore` określonych w `PipelineData` obiekcie jako dane wyjściowe, które w tym przypadku nazywa się *wnioskami*. Wyniki są przechowywane w domyślnym kontenerze obiektów blob, można przechodzić do konta magazynu i przeglądać Eksplorator usługi Storage, ścieżka pliku to Azure-elementu BlobStore-*GUID*/azureml/*RunId* / *OUTPUT_DIR*.

Możesz również pobrać te dane, aby wyświetlić wyniki. Poniżej znajduje się przykładowy kod umożliwiający wyświetlenie pierwszych 10 wierszy.

```python
import pandas as pd
import tempfile

batch_run = pipeline_run.find_step_run(parallelrun_step.name)[0]
batch_output = batch_run.get_output_data(output_dir.name)

target_dir = tempfile.mkdtemp()
batch_output.download(local_path=target_dir)
result_file = os.path.join(target_dir, batch_output.path_on_datastore, parallel_run_config.append_row_file_name)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10) 
```

## <a name="next-steps"></a>Następne kroki

Aby zobaczyć, jak to działa, wypróbuj w [notesie wnioskowania o przetwarzanie wsadowe](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run). 

Wskazówki dotyczące debugowania i rozwiązywania problemów z programem ParallelRunStep można znaleźć w [przewodniku krok po kroku](how-to-debug-parallel-run-step.md).

Wskazówki dotyczące debugowania i rozwiązywania problemów z potokami znajdują się w [przewodniku krok po kroku](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

