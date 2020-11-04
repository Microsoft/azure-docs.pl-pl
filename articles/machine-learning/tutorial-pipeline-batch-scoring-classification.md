---
title: 'Samouczek: potoki ML do oceniania partii'
titleSuffix: Azure Machine Learning
description: W tym samouczku utworzysz potok uczenia maszynowego w celu przeprowadzenia oceny wsadowej na modelu klasyfikacji obrazu. Azure Machine Learning pozwala skupić się na uczeniu maszynowym zamiast infrastruktury i automatyzacji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: lobrien
ms.author: laobri
ms.reviewer: laobri
ms.date: 10/13/2020
ms.custom: contperfq4, devx-track-python
ms.openlocfilehash: f7d1cffb44914535fe218980c750270ebba14445
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309458"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Samouczek: Tworzenie potoku Azure Machine Learning na potrzeby oceniania partii



W tym zaawansowanym samouczku dowiesz się, jak utworzyć [potok Azure Machine Learning](concept-ml-pipelines.md) , aby uruchomić zadanie oceniania partii. Potoki uczenia maszynowego optymalizują przepływ pracy z szybkością, przenośnością i wielokrotnym użyciem, dzięki czemu możesz skupić się na uczeniu maszynowym zamiast infrastruktury i automatyzacji. Po skompilowaniu i opublikowaniu potoku należy skonfigurować punkt końcowy REST, za pomocą którego można wyzwolić potok z dowolnej biblioteki HTTP na dowolnej platformie. 

W przykładzie zastosowano [splotowych model](https://arxiv.org/abs/1512.00567) sieci neuronowych, który został wdrożony w Tensorflow do klasyfikowania obrazów bez etykiet. 

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Konfigurowanie obszaru roboczego 
> * Pobieranie i przechowywanie przykładowych danych
> * Tworzenie obiektów DataSet do pobierania i wyprowadzania danych
> * Pobieranie, przygotowywanie i rejestrowanie modelu w obszarze roboczym
> * Udostępnianie elementów docelowych obliczeń i Tworzenie skryptu oceniania
> * Użyj `ParallelRunStep` klasy do asynchronicznego oceniania partii
> * Kompilowanie, uruchamianie i publikowanie potoku
> * Włączanie punktu końcowego REST dla potoku

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz jeszcze Azure Machine Learning obszaru roboczego lub notesu maszyny wirtualnej, Ukończ [część 1 samouczka instalacji](tutorial-1st-experiment-sdk-setup.md).
* Po zakończeniu pracy z samouczkiem Instalatora Użyj tego samego serwera notesu, aby otworzyć Notes */Machine-Learning-Pipelines-Advanced/tutorial-Pipeline-Batch-Scoring-Classification. ipynb* .

Jeśli chcesz uruchomić samouczek instalacji we własnym [środowisku lokalnym](how-to-configure-environment.md#local), możesz uzyskać dostęp do samouczka w witrynie [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials). Uruchom, `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` Aby pobrać wymagane pakiety.

## <a name="configure-workspace-and-create-a-datastore"></a>Konfigurowanie obszaru roboczego i tworzenie magazynu danych

Utwórz obiekt obszaru roboczego z istniejącego obszaru roboczego Azure Machine Learning.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

> [!IMPORTANT]
> Ten fragment kodu oczekuje, że Konfiguracja obszaru roboczego ma zostać zapisana w bieżącym katalogu lub jego elemencie nadrzędnym. Aby uzyskać więcej informacji na temat tworzenia obszaru roboczego, zobacz [Tworzenie obszarów roboczych i zarządzanie nimi Azure Machine Learning](how-to-manage-workspace.md). Aby uzyskać więcej informacji na temat zapisywania konfiguracji do pliku, zobacz [Tworzenie pliku konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).

## <a name="create-a-datastore-for-sample-images"></a>Tworzenie magazynu danych dla przykładowych obrazów

Na `pipelinedata` koncie Pobierz przykład danych z publicznej oceny ImageNet z `sampledata` publicznego kontenera obiektów BLOB. Wywołaj, `register_azure_blob_container()` Aby udostępnić dane dla obszaru roboczego pod nazwą `images_datastore` . Następnie należy ustawić domyślny magazyn danych obszaru roboczego jako wyjściowy. Użyj wyjściowego magazynu danych do oceny danych wyjściowych w potoku.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do danych, zobacz [jak uzyskać dostęp do danych](./how-to-access-data.md).

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-dataset-objects"></a>Tworzenie obiektów DataSet

Podczas kompilowania potoków `Dataset` obiekty są używane do odczytywania danych z magazynów elementów obszaru roboczego, a `PipelineData` obiekty są używane do przesyłania danych pośrednich między etapami potoku.

> [!Important]
> Przykład wsadowego oceniania w tym samouczku używa tylko jednego kroku potoku. W przypadku użycia, które mają wiele kroków, typowy przepływ obejmuje następujące kroki:
>
> 1. Używaj `Dataset` obiektów jako danych *wejściowych* do pobierania danych pierwotnych, wykonywania niektórych transformacji, a następnie *wyprowadzania* `PipelineData` obiektu.
>
> 2. Użyj `PipelineData` *obiektu wyjściowego* w poprzednim kroku jako *obiektu wejściowego*. Powtórz tę czynność dla kolejnych kroków.

W tym scenariuszu utworzysz `Dataset` obiekty odpowiadające katalogom magazynu danych zarówno dla obrazów wejściowych, jak i etykiet klasyfikacji (wartości y-test). Tworzony jest również `PipelineData` obiekt dla danych wyjściowych oceniania partii.

```python
from azureml.core.dataset import Dataset
from azureml.pipeline.core import PipelineData

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/"))
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

Zarejestruj zestawy danych w obszarze roboczym, jeśli chcesz użyć go później. Ta czynność jest opcjonalna.

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
```

## <a name="download-and-register-the-model"></a>Pobieranie i rejestrowanie modelu

Pobierz Tensorflow model, aby użyć go do oceniania partii w potoku. Najpierw Utwórz katalog lokalny, w którym jest przechowywany model. Następnie pobierz i Wyodrębnij model.

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

Następnie zarejestruj model w obszarze roboczym, aby można było łatwo pobrać model w procesie potoku. W `register()` funkcji statycznej `model_name` parametr jest kluczem używanym do lokalizowania modelu w całym zestawie SDK.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>Tworzenie i dołączanie zdalnego celu obliczeń

Potoki usługi Machine Learning nie mogą być uruchamiane lokalnie, dlatego można je uruchamiać w zasobach w chmurze lub *zdalnych celach obliczeniowych*. Zdalny obiekt docelowy obliczeń to wirtualne środowisko obliczeniowe wielokrotnego użytku, w którym można uruchamiać eksperymenty i przepływy pracy uczenia maszynowego. 

Uruchom następujący kod, aby utworzyć obiekt docelowy z obsługą procesora GPU [`AmlCompute`](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?preserve-view=true&view=azure-ml-py) , a następnie dołącz go do obszaru roboczego. Aby uzyskać więcej informacji na temat obiektów docelowych obliczeń, zobacz [artykuł koncepcyjny](./concept-compute-target.md).


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>Napisz skrypt oceniania

Aby wykonać ocenianie, Utwórz skrypt oceniania partii o nazwie `batch_scoring.py` , a następnie zapisz go w bieżącym katalogu. Skrypt pobiera obrazy wejściowe, stosuje model klasyfikacji, a następnie wyprowadza przewidywania do pliku wyników.

`batch_scoring.py`Skrypt przyjmuje następujące parametry, które `ParallelRunStep` zostały przesłane później:

- `--model_name`: Nazwa używanego modelu.
- `--labels_dir`: Lokalizacja `labels.txt` pliku.

Infrastruktura potoku używa `ArgumentParser` klasy do przekazywania parametrów do kroków potoku. Na przykład w poniższym kodzie jako pierwszy argument `--model_name` jest przyznany identyfikator właściwości `model_name` . W `init()` funkcji `Model.get_model_path(args.model_name)` służy do uzyskiwania dostępu do tej właściwości.


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3

from azureml.core import Run
from azureml.core.model import Model
from azureml.core.dataset import Dataset

slim = tf.contrib.slim

image_size = 299
num_channel = 3


def get_class_label_dict(labels_dir):
    label = []
    labels_path = os.path.join(labels_dir, 'labels.txt')
    proto_as_ascii_lines = tf.gfile.GFile(labels_path).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


def init():
    global g_tf_sess, probabilities, label_dict, input_images

    parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
    parser.add_argument('--model_name', dest="model_name", required=True)
    parser.add_argument('--labels_dir', dest="labels_dir", required=True)
    args, _ = parser.parse_known_args()

    label_dict = get_class_label_dict(args.labels_dir)
    classes_num = len(label_dict)

    with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
        input_images = tf.placeholder(tf.float32, [1, image_size, image_size, num_channel])
        logits, _ = inception_v3.inception_v3(input_images,
                                              num_classes=classes_num,
                                              is_training=False)
        probabilities = tf.argmax(logits, 1)

    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    g_tf_sess = tf.Session(config=config)
    g_tf_sess.run(tf.global_variables_initializer())
    g_tf_sess.run(tf.local_variables_initializer())

    model_path = Model.get_model_path(args.model_name)
    saver = tf.train.Saver()
    saver.restore(g_tf_sess, model_path)


def file_to_tensor(file_path):
    image_string = tf.read_file(file_path)
    image = tf.image.decode_image(image_string, channels=3)

    image.set_shape([None, None, None])
    image = tf.image.resize_images(image, [image_size, image_size])
    image = tf.divide(tf.subtract(image, [0]), [255])
    image.set_shape([image_size, image_size, num_channel])
    return image


def run(mini_batch):
    result_list = []
    for file_path in mini_batch:
        test_image = file_to_tensor(file_path)
        out = g_tf_sess.run(test_image)
        result = g_tf_sess.run(probabilities, feed_dict={input_images: [out]})
        result_list.append(os.path.basename(file_path) + ": " + label_dict[result[0]])
    return result_list
```

> [!TIP]
> Potok w tym samouczku ma tylko jeden krok i zapisuje dane wyjściowe do pliku. W przypadku potoków wieloetapowych można również `ArgumentParser` zdefiniować katalog do zapisywania danych wyjściowych do kolejnych kroków. Przykład przekazywania danych między wieloma krokami potoku przy użyciu `ArgumentParser` wzorca projektowego można znaleźć w [notesie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-the-pipeline"></a>Kompilowanie potoku

Przed uruchomieniem potoku Utwórz obiekt, który definiuje środowisko Python i tworzy zależności `batch_scoring.py` wymagane przez skrypt. Główna zależność wymagana to Tensorflow, ale również instalacja `azureml-core` i `azureml-dataprep[fuse]` wymagane przez ParallelRunStep. Należy również określić obsługę platformy Docker i platformy Docker-GPU.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.15.2",
                                            "azureml-core", "azureml-dataprep[fuse]"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>Utwórz konfigurację, aby otoczyć skrypt

Utwórz krok potoku przy użyciu skryptu, konfiguracji środowiska i parametrów. Określ miejsce docelowe obliczeń, które zostało już dołączone do obszaru roboczego.

```python
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    environment=env,
    entry_script="batch_scoring.py",
    source_directory=".",
    output_action="append_row",
    mini_batch_size="20",
    error_threshold=1,
    compute_target=compute_target,
    process_count_per_node=2,
    node_count=1
)
```

### <a name="create-the-pipeline-step"></a>Tworzenie kroku potoku

Krok potoku to obiekt, który hermetyzuje wszystko, czego potrzebujesz do uruchomienia potoku, w tym:

* Ustawienia środowiska i zależności
* Zasób obliczeniowy, na którym ma zostać uruchomiony potok
* Dane wejściowe i wyjściowe oraz dowolne parametry niestandardowe
* Odwołanie do skryptu lub logiki zestawu SDK do uruchomienia w ramach tego kroku

Wiele klas dziedziczy z klasy nadrzędnej [`PipelineStep`](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?preserve-view=true&view=azure-ml-py) . Możesz wybrać klasy do użycia określonych platform lub stosów, aby skompilować krok. W tym przykładzie użyto `ParallelRunStep` klasy do zdefiniowania logiki krokowej przy użyciu niestandardowego skryptu języka Python. Jeśli argument skryptu jest danymi wejściowymi do kroku lub danych wyjściowych kroku, argument musi być zdefiniowany *zarówno* w `arguments` tablicy, *jak i* w `input` lub jako `output` parametr. 

W scenariuszach, w których występuje więcej niż jeden krok, odwołanie do obiektu w `outputs` tablicy jest dostępne jako *dane wejściowe* dla kolejnego kroku potoku.

```python
from azureml.pipeline.steps import ParallelRunStep
from datetime import datetime

parallel_step_name = "batchscoring-" + datetime.now().strftime("%Y%m%d%H%M")

label_config = label_ds.as_named_input("labels_input")

batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--model_name", "inception",
               "--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
    allow_reuse=False
)
```

Aby zapoznać się z listą wszystkich klas, których można użyć dla różnych typów etapów, zobacz [pakiet kroków](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py).

## <a name="submit-the-pipeline"></a>Prześlij potok

Teraz uruchom potok. Najpierw Utwórz `Pipeline` obiekt za pomocą odwołania do obszaru roboczego i utworzonego etapu potoku. `steps`Parametr jest tablicą kroków. W takim przypadku istnieje tylko jeden krok oceniania partii. Aby utworzyć potoki, które mają wiele kroków, należy umieścić kroki w kolejności w tej tablicy.

Następnie użyj funkcji, `Experiment.submit()` Aby przesłać potok do wykonania. `wait_for_completion`Funkcja wyprowadza dzienniki podczas procesu kompilacji potoku. Możesz użyć dzienników, aby zobaczyć bieżący postęp.

> [!IMPORTANT]
> Pierwsze uruchomienie potoku trwa około *15 minut*. Wszystkie zależności muszą zostać pobrane, zostanie utworzony obraz platformy Docker, a środowisko Python jest inicjowane i tworzone. Ponowne uruchomienie potoku trwa znacznie mniej czasu, ponieważ te zasoby są ponownie używane zamiast tworzenia. Jednak łączny czas wykonywania potoku zależy od obciążenia skryptów i procesów uruchomionych w każdym kroku potoku.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Pobierz i Przejrzyj dane wyjściowe

Uruchom następujący kod, aby pobrać plik wyjściowy, który został utworzony ze `batch_scoring.py` skryptu. Następnie przejrzyj wyniki oceniania.

```python
import pandas as pd

batch_run = next(pipeline_run.get_children())
batch_output = batch_run.get_output_data("scores")
batch_output.download(local_path="inception_results")

for root, dirs, files in os.walk("inception_results"):
    for file in files:
        if file.endswith("parallel_run_step.txt"):
            result_file = os.path.join(root, file)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10)
```

## <a name="publish-and-run-from-a-rest-endpoint"></a>Publikowanie i uruchamianie z punktu końcowego REST

Uruchom następujący kod, aby opublikować potok w obszarze roboczym. W obszarze roboczym programu Azure Machine Learning Studio można zobaczyć metadane dla potoku, w tym historię uruchamiania i czas trwania. Potok można również uruchomić ręcznie z poziomu programu Studio.

Opublikowanie potoku umożliwia punkt końcowy REST, którego można użyć do uruchomienia potoku z dowolnej biblioteki HTTP na dowolnej platformie.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Aby można było uruchomić potok z punktu końcowego REST, potrzebny jest nagłówek uwierzytelniania typu "OAuth2". W poniższym przykładzie użyto uwierzytelniania interaktywnego (w celach ilustracyjnych), ale w przypadku większości scenariuszy produkcyjnych, które wymagają automatycznego lub bezobsługowego uwierzytelniania, należy użyć uwierzytelniania nazwy głównej usługi zgodnie [z opisem w tym artykule](how-to-setup-authentication.md).

Uwierzytelnianie jednostki usługi polega na utworzeniu *rejestracji aplikacji* w *Azure Active Directory*. Najpierw należy wygenerować klucz tajny klienta, a następnie przyznać roli głównej usługi *dostęp* do obszaru roboczego uczenia maszynowego. Użyj [`ServicePrincipalAuthentication`](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?preserve-view=true&view=azure-ml-py) klasy, aby zarządzać przepływem uwierzytelniania. 

Oba [`InteractiveLoginAuthentication`](/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?preserve-view=true&view=azure-ml-py) i `ServicePrincipalAuthentication` dziedziczą z `AbstractAuthentication` . W obu przypadkach należy użyć [`get_authentication_header()`](/python/api/azureml-core/azureml.core.authentication.abstractauthentication?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-authentication-header--) funkcji w taki sam sposób, aby można było pobrać nagłówek:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Pobierz adres URL REST z `endpoint` Właściwości opublikowanego obiektu potoku. Możesz również znaleźć adres URL REST w obszarze roboczym w programie Azure Machine Learning Studio. 

Kompiluj żądanie HTTP POST do punktu końcowego. Określ w żądaniu nagłówek uwierzytelniania. Dodaj obiekt ładunku JSON, który ma nazwę eksperymentu.

Wykonaj żądanie, aby wyzwolić uruchomienie. Dołącz kod, aby uzyskać dostęp do `Id` klucza z słownika odpowiedzi, aby uzyskać wartość identyfikatora uruchomienia.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"process_count_per_node": 6}})
run_id = response.json()["Id"]
```

Użyj identyfikatora uruchomienia, aby monitorować stan nowego przebiegu. Nowy przebieg zajmie jeszcze 10-15 min. 

Nowy przebieg będzie wyglądał podobnie do potoku, który został uruchomiony wcześniej w samouczku. Możesz zrezygnować z wyświetlania pełnych danych wyjściowych.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Nie Dokończ tej sekcji, jeśli planujesz uruchamiać inne samouczki Azure Machine Learning.

### <a name="stop-the-compute-instance"></a>Zatrzymaj wystąpienie obliczeniowe

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Usuń wszystko

Jeśli nie planujesz używać utworzonych zasobów, usuń je, aby uniknąć naliczania opłat.

1. W Azure Portal w menu po lewej stronie wybierz pozycję **grupy zasobów**.
1. Z listy grup zasobów wybierz utworzoną grupę zasobów.
1. Wybierz pozycję **Usuń grupę zasobów**.
1. Wpisz nazwę grupy zasobów. Następnie wybierz pozycję **Usuń**.

Możesz też zachować grupę zasobów i usunąć jeden obszar roboczy. Wyświetl właściwości obszaru roboczego, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku potoki uczenia maszynowego zostały wykonane następujące zadania:

> [!div class="checklist"]
> * Utworzono potok z zależnościami środowiska do uruchomienia na zdalnym zasobie obliczeniowym procesora GPU.
> * Utworzono skrypt oceniania służący do uruchamiania prognoz wsadowych przy użyciu przedniego Tensorflow modelu.
> * Opublikowano potok i włączył go do uruchamiania z punktu końcowego REST.

Aby uzyskać więcej przykładów tworzenia potoków przy użyciu zestawu SDK usługi Machine Learning, zobacz [repozytorium notesu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).
