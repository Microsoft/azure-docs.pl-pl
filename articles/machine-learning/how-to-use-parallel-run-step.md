---
title: Uruchamianie prognoz wsadowych dotyczących danych Big Data
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uzyskać informacje o liczbie asynchronicznie dla dużych ilości danych przy użyciu ParallelRunStep w Azure Machine Learning. Program ParallelRunStep udostępnia funkcje przetwarzania równoległego z usługi Box i optymalizuje w celu zapewnienia wysokiej przepływności, odporności i zapamiętania w przypadku dużych ilości danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: vaidyas
author: vaidya-s
ms.date: 01/15/2020
ms.custom: Ignite2019
ms.openlocfilehash: 3d283d1094336b928869aa281b4a640d7a62dd94
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79477191"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Uruchamiaj wnioskowanie wsadowe dla dużych ilości danych za pomocą Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak przetwarzać duże ilości danych asynchronicznie i równolegle przy użyciu Azure Machine Learning. Opisana tutaj funkcja ParallelRunStep jest dostępna w publicznej wersji zapoznawczej. Jest to wysoka wydajność i Wysoka przepływność na potrzeby generowania wnioskowania i przetwarzania danych. Zapewnia asynchroniczne funkcje z pola.

Dzięki ParallelRunStep jest to proste skalowanie w trybie offline do dużych klastrów maszyn na terabajtach danych produkcyjnych, co poprawia produktywność i zoptymalizowany koszt.

Ten artykuł zawiera informacje o następujących zadaniach:

> * Utwórz zdalny zasób obliczeniowy.
> * Napisz niestandardowy skrypt wnioskowania.
> * Utwórz [potok uczenia maszynowego](concept-ml-pipelines.md) , aby zarejestrować wstępnie szkolony model klasyfikacji obrazów oparty na zestawie danych [mnist ręcznie](https://publicdataset.azurewebsites.net/dataDetail/mnist/) . 
> * Użyj modelu, aby uruchomić wnioskowanie wsadowe na przykładowych obrazach dostępnych na koncie usługi Azure Blob Storage. 

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

* Aby zapoznać się z przewodnikiem Szybki Start, Wypełnij [samouczek Instalatora](tutorial-1st-experiment-sdk-setup.md) , jeśli nie masz jeszcze obszaru roboczego Azure Machine Learning lub maszyny wirtualnej notesu. 

* Aby zarządzać własnym środowiskiem i zależnościami, zobacz [Przewodnik](how-to-configure-environment.md) po konfigurowaniu własnego środowiska. Uruchom `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps` w środowisku, aby pobrać wymagane zależności.

## <a name="set-up-machine-learning-resources"></a>Konfigurowanie zasobów uczenia maszynowego

Poniższe akcje umożliwiają skonfigurowanie zasobów potrzebnych do uruchomienia potoku wnioskowania partii:

- Utwórz magazyn danych, który wskazuje na kontener obiektów blob, który zawiera obrazy do wnioskowania.
- Skonfiguruj odwołania do danych jako dane wejściowe i wyjściowe dla kroku potoku wnioskowania o partie partii.
- Skonfiguruj klaster obliczeniowy do uruchamiania kroku wnioskowania o partie.

### <a name="create-a-datastore-with-sample-images"></a>Tworzenie magazynu danych z przykładowymi obrazami

Pobierz zestaw ocen MNIST ręcznie z publicznego kontenera `sampledata` obiektów BLOB na koncie o nazwie. `pipelinedata` Utwórz magazyn danych o nazwie `mnist_datastore`, który wskazuje na ten kontener. W poniższym wywołaniu `register_azure_blob_container`, ustawiając `overwrite` flagę w celu `True` zastępowanie wszelkich magazynów danych, które zostały utworzone wcześniej przy użyciu tej nazwy. 

Możesz zmienić ten krok, aby wskazywał kontener obiektów blob, dostarczając własne wartości dla `datastore_name`, `container_name`i. `account_name`

```python
from azureml.core import Datastore
from azureml.core import Workspace

# Load workspace authorization details from config.json
ws = Workspace.from_config()

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

Następnie określ domyślny magazyn danych obszaru roboczego jako dane wyjściowe. Zostanie ona użyta do wnioskowania danych wyjściowych.

Podczas tworzenia obszaru roboczego [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) i  [Magazyn obiektów BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)są domyślnie dołączone do obszaru roboczego. Azure Files jest domyślnym magazynem danych dla obszaru roboczego, ale można również użyć magazynu obiektów BLOB jako magazynu danych. Aby uzyskać więcej informacji, zobacz [Opcje usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-inputs-and-outputs"></a>Konfiguruj dane wejściowe i wyjściowe

Teraz musisz skonfigurować dane wejściowe i wyjściowe, w tym:

- Katalog zawierający obrazy wejściowe.
- Katalog, w którym jest przechowywany wstępnie szkolony model.
- Katalog, który zawiera etykiety.
- Katalog dla danych wyjściowych.

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)jest klasą do eksplorowania, przekształcania i zarządzania danymi w Azure Machine Learning. Ta klasa ma dwa typy: [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) i [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py). W tym przykładzie użyjesz `FileDataset` jako danych wejściowych kroku potoku wnioskowania o partie. 

> [!NOTE] 
> `FileDataset`Obsługa w wnioskach wsadowych jest obecnie ograniczona do magazynu obiektów blob platformy Azure. 

Możesz również odwoływać się do innych zestawów danych w skrypcie wnioskowania niestandardowego. Na przykład można użyć go, aby uzyskać dostęp do etykiet w skrypcie w celu etykietowania obrazów przy `Dataset.register` użyciu `Dataset.get_by_name`i.

Aby uzyskać więcej informacji na temat Azure Machine Learning zestawów danych, zobacz [Tworzenie zestawów danych i uzyskiwanie do nich dostępu (wersja zapoznawcza)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)obiekty służą do transferowania danych pośrednich między etapami potoku. W tym przykładzie używa się go do wyprowadzania danych.

```python
from azureml.core.dataset import Dataset

mnist_ds_name = 'mnist_sample_data'

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
registered_mnist_ds = input_mnist_ds.register(ws, mnist_ds_name, create_new_version=True)
named_mnist_ds = registered_mnist_ds.as_named_input(mnist_ds_name)

output_dir = PipelineData(name="inferences", 
                          datastore=def_data_store, 
                          output_path_on_compute="mnist/results")
```

### <a name="set-up-a-compute-target"></a>Konfigurowanie celu obliczeń

W Azure Machine Learning, *obliczenia* (lub *element docelowy obliczeń*) odnoszą się do maszyn lub klastrów wykonujących kroki obliczeniowe w potoku uczenia maszynowego. Uruchom następujący kod, aby utworzyć obiekt docelowy [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) opartego na procesorach.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
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

Następnie zarejestruj model w obszarze roboczym, aby był dostępny dla zdalnego zasobu obliczeń.

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
>Poniższy kod jest tylko przykładem, który używa [przykładowego notesu](https://aka.ms/batch-inference-notebooks) . Musisz utworzyć własny skrypt dla danego scenariusza.

Skrypt *musi zawierać* dwie funkcje:
- `init()`: Ta funkcja jest używana do dowolnych kosztownych lub wspólnych przygotowań do późniejszego wnioskowania. Na przykład użyj go do załadowania modelu do obiektu globalnego. Ta funkcja zostanie wywołana tylko raz na początku procesu.
-  `run(mini_batch)`: Funkcja zostanie uruchomiona dla każdego `mini_batch` wystąpienia.
    -  `mini_batch`: Krok przebieg równoległy wywoła metodę Run i przekaże element list lub Pandas Dataframe jako argument do metody. Każdy wpis w min_batch będzie ścieżką pliku, jeśli dane wejściowe to FileDataset, Pandas Dataframe, jeśli dane wejściowe to TabularDataset.
    -  `response`: Metoda Run () powinna zwracać element Pandas Dataframe lub tablicę. W przypadku append_row output_action te zwrócone elementy są dołączane do wspólnego pliku wyjściowego. W przypadku summary_only zawartość elementów jest ignorowana. Dla wszystkich akcji wyjściowych każdy zwrócony element wyjściowy wskazuje jeden udany przebieg elementu wejściowego w danych wejściowych. Należy upewnić się, że w wyniku uruchomienia są zawarte wystarczające dane, aby zamapować dane wejściowe w wyniku uruchomienia. Dane wyjściowe uruchamiania będą zapisywane w pliku wyjściowym i nie będą gwarantowane w kolejności, dlatego należy użyć pewnego klucza w danych wyjściowych, aby zamapować go na dane wejściowe.

```python
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://aka.ms/batch-inference-notebooks)
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

### <a name="how-to-access-other-files-in-source-directory-in-entry_script"></a>Jak uzyskać dostęp do innych plików w katalogu źródłowym w entry_script

Jeśli masz inny plik lub folder w tym samym katalogu co skrypt wejściowy, możesz odwoływać się do niego, wyszukując bieżący katalog roboczy.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>Kompiluj i uruchamiaj potok zawierający ParallelRunStep

Teraz masz wszystko, co jest potrzebne do skompilowania potoku.

### <a name="prepare-the-run-environment"></a>Przygotuj środowisko uruchomieniowe

Najpierw określ zależności dla skryptu. Ten obiekt jest używany później podczas tworzenia kroku potoku.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
batch_env.spark.precache_packages = False
```

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>Określ parametry dla etapu potoku wnioskowania dotyczącego partii

`ParallelRunConfig`jest to główna konfiguracja nowo wprowadzonego wystąpienia wnioskowania `ParallelRunStep` o partie w ramach potoku Azure Machine Learning. Służy do zawijania skryptu i konfigurowania niezbędnych parametrów, w tym wszystkich następujących parametrów:
- `entry_script`: Skrypt użytkownika jako ścieżka do pliku lokalnego, która będzie uruchamiana równolegle na wielu węzłach. Jeśli `source_directory` jest obecny, należy użyć ścieżki względnej. W przeciwnym razie użyj dowolnej ścieżki dostępnej na komputerze.
- `mini_batch_size`: Rozmiar mini-Batch przeszedł do pojedynczego `run()` wywołania. (opcjonalnie; wartość domyślna to `10` pliki dla FileDataset i `1MB` TabularDataset).
    - Dla `FileDataset`, jest to liczba plików o minimalnej wartości `1`. Można połączyć wiele plików w jedną minimalną partię.
    - W `TabularDataset`przypadku, jest to rozmiar danych. Przykładowe wartości to `1024`, `1024KB`, `10MB`, i `1GB`. Zalecana wartość to `1MB`. Mini-Batch z `TabularDataset` nigdy nie będzie przekraczać granic plików. Jeśli na przykład pliki CSV mają różne rozmiary, najmniejszy plik to 100 KB, a największy to 10 MB. Jeśli ustawisz `mini_batch_size = 1MB`, pliki o rozmiarze mniejszym niż 1 MB będą traktowane jako jedna mini-Batch. Pliki o rozmiarze większym niż 1 MB zostaną podzielone na wiele kart Mini-Part.
- `error_threshold`: Liczba błędów rekordu `TabularDataset` i błędów plików dla `FileDataset` , które powinny zostać zignorowane podczas przetwarzania. Jeśli liczba błędów dla całego danych wejściowych spadnie powyżej tej wartości, zadanie zostanie przerwane. Próg błędu dotyczy całego danych wejściowych, a nie dla pojedynczych partii, które są wysyłane do `run()` metody. Zakresem jest `[-1, int.max]`. Część `-1` wskazuje, że wszystkie błędy zostaną zignorowane podczas przetwarzania.
- `output_action`: Jedna z następujących wartości wskazuje, w jaki sposób dane wyjściowe będą zorganizowane:
    - `summary_only`: Skrypt użytkownika będzie przechowywał dane wyjściowe. `ParallelRunStep`będzie używać danych wyjściowych tylko dla obliczeń progu błędu.
    - `append_row`: Dla wszystkich plików wejściowych w folderze wyjściowym zostanie utworzony tylko jeden plik, aby dołączyć wszystkie dane wyjściowe rozdzielone wierszami. Nazwa pliku będzie `parallel_run_step.txt`.
- `source_directory`: Ścieżki do folderów zawierających wszystkie pliki do wykonania na obiekcie docelowym obliczeń (opcjonalnie).
- `compute_target`: Obsługiwane `AmlCompute` są tylko.
- `node_count`: Liczba węzłów obliczeniowych, które mają być używane do uruchamiania skryptu użytkownika.
- `process_count_per_node`: Liczba procesów na węzeł.
- `environment`: Definicja środowiska języka Python. Można skonfigurować go do korzystania z istniejącego środowiska języka Python lub do skonfigurowania tymczasowego środowiska dla eksperymentu. Definicja jest również odpowiedzialna za ustawianie wymaganych zależności aplikacji (opcjonalnie).
- `logging_level`: Poziom szczegółowości dziennika. Wartości zwiększające poziom szczegółowości to: `WARNING`, `INFO`, i `DEBUG`. (opcjonalnie; wartość domyślna to `INFO`)
- `run_invocation_timeout`: Limit `run()` czasu wywołania metody (w sekundach). (opcjonalnie; wartość domyślna to `60`)

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory=scripts_folder,
    entry_script="digit_identification.py",
    mini_batch_size="5",
    error_threshold=10,
    output_action="append_row",
    environment=batch_env,
    compute_target=compute_target,
    node_count=4)
```

### <a name="create-the-pipeline-step"></a>Tworzenie kroku potoku

Utwórz krok potoku przy użyciu skryptu, konfiguracji środowiska i parametrów. Określ miejsce docelowe obliczeń, które zostało już dołączone do obszaru roboczego jako element docelowy wykonywania skryptu. Użyj `ParallelRunStep` do utworzenia kroku potoku wnioskowania partii, który przyjmuje wszystkie następujące parametry:
- `name`: Nazwa kroku z następującymi ograniczeniami nazewnictwa: Unique, 3-32 znaków i wyrażenie regularne ^\[a-z\]([-a-Z0-9] * [a-Z0-9])? $.
- `models`: Co najmniej zero nazw modeli jest już zarejestrowanych w rejestrze modelu Azure Machine Learning.
- `parallel_run_config`: `ParallelRunConfig` Obiekt, zgodnie z definicją wcześniejszą.
- `inputs`: Co najmniej jeden Azure Machine Learning zestaw danych z jednym typem.
- `output`: `PipelineData` Obiekt, który odpowiada katalogowi wyjściowemu.
- `arguments`: Lista argumentów przenoszona do skryptu użytkownika (opcjonalnie).
- `allow_reuse`: Czy krok ma ponownie używać poprzednich wyników w przypadku uruchamiania z tymi samymi ustawieniami/danymi wejściowymi. Jeśli ten parametr ma `False`wartość, nowy przebieg będzie zawsze generowany dla tego kroku podczas wykonywania potoku. (opcjonalnie; wartość domyślna to `True`).

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="batch-mnist",
    models=[model],
    parallel_run_config=parallel_run_config,
    inputs=[named_mnist_ds],
    output=output_dir,
    arguments=[],
    allow_reuse=True
)
```

>[!Note]
> Powyższy krok zależy od `azureml-contrib-pipeline-steps`, zgodnie z opisem w sekcji [wymagania wstępne](#prerequisites). 

### <a name="submit-the-pipeline"></a>Prześlij potok

Teraz uruchom potok. Najpierw Utwórz [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) obiekt za pomocą odwołania do obszaru roboczego i utworzonego etapu potoku. `steps` Parametr jest tablicą kroków. W takim przypadku istnieje tylko jeden krok oceniania partii. Aby utworzyć potoki, które mają wiele kroków, należy umieścić kroki w kolejności w tej tablicy.

Następnie użyj `Experiment.submit()` funkcji, aby przesłać potok do wykonania.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-parallel-run-job"></a>Monitorowanie zadania przebiegu równoległego

Ukończenie zadania wnioskowania partii może zająć dużo czasu. Ten przykład monitoruje postęp przy użyciu widżetu Jupyter. Postęp zadania można także zarządzać przy użyciu:

* Azure Machine Learning Studio. 
* Dane wyjściowe konsoli z [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) obiektu.

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>Następne kroki

Aby zobaczyć, jak to działa, wypróbuj w [notesie wnioskowania o przetwarzanie wsadowe](https://aka.ms/batch-inference-notebooks). 

Wskazówki dotyczące debugowania i rozwiązywania problemów z programem ParallelRunStep można znaleźć w [przewodniku krok po kroku](how-to-debug-parallel-run-step.md).

Wskazówki dotyczące debugowania i rozwiązywania problemów z potokami znajdują się w [przewodniku krok po kroku](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

