---
title: Trenuj przy użyciu zestawów danych uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak udostępnić dane lokalnym lub zdalnym obliczeniowym na trenowanie modelu przy użyciu Azure Machine Learning danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 0b2bb49863e07e6f06512e868ed12ecf00cc11c2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872393"
---
# <a name="train-models-with-azure-machine-learning-datasets"></a>Szkolenie modeli przy użyciu Azure Machine Learning danych 

Z tego artykułu dowiesz się, jak pracować z zestawami danych [Azure Machine Learning w](/python/api/azureml-core/azureml.core.dataset%28class%29) celu trenowania modeli uczenia maszynowego.  Zestawów danych można używać w lokalnym lub zdalnym docelowym obiektem obliczeniowym bez martwienia się o parametry połączenia lub ścieżki danych. 

Azure Machine Learning danych zapewniają bezproblemową integrację z Azure Machine Learning trenowania, takich jak [ScriptRunConfig,](/python/api/azureml-core/azureml.core.scriptrunconfig) [HyperDrive](/python/api/azureml-train-core/azureml.train.hyperdrive) [i Azure Machine Learning potoków.](./how-to-create-machine-learning-pipelines.md)

Jeśli nie chcesz jeszcze udostępnić danych do trenowania modelu, ale chcesz załadować dane do notesu w celu eksploracji danych, zobacz, jak eksplorować dane w [zestawie danych.](how-to-create-register-datasets.md#explore-data) 

## <a name="prerequisites"></a>Wymagania wstępne

Do tworzenia i trenowania przy użyciu zestawów danych potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* Obszar [Azure Machine Learning obszaru roboczego.](how-to-manage-workspace.md)

* Zainstalowany [Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/install) (>= 1.13.0), który zawiera pakiet `azureml-datasets` .

> [!Note]
> Niektóre klasy zestawu danych są zależne od [pakietu azureml-dataprep.](https://pypi.org/project/azureml-dataprep/) W przypadku użytkowników systemu Linux te klasy są obsługiwane tylko w następujących dystrybucjach: Red Hat Enterprise Linux, Ubuntu, Fedora i CentOS.

## <a name="consume-datasets-in-machine-learning-training-scripts"></a>Wykorzystanie zestawów danych w skryptach trenowania uczenia maszynowego

Jeśli masz dane ustrukturyzowane, które nie zostały jeszcze zarejestrowane jako zestaw danych, utwórz zestaw TabularDataset i użyj go bezpośrednio w skrypcie trenowania dla eksperymentu lokalnego lub zdalnego.

W tym przykładzie utworzysz niezarejestrowany element [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) i określisz go jako argument skryptu w obiekcie [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) do trenowania. Jeśli chcesz ponownie użyć tego zestawu TabularDataset w innych eksperymentach w obszarze roboczym, zobacz jak rejestrować zestawy [danych w obszarze roboczym.](how-to-create-register-datasets.md#register-datasets)

### <a name="create-a-tabulardataset"></a>Tworzenie zestawu tabularDataset

Poniższy kod tworzy niezarejestrowany zestaw TabularDataset z internetowego adresu URL.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

Obiekty TabularDataset zapewniają możliwość załadowania danych z zestawu TabularDataset do ramki danych pandas lub Spark, dzięki czemu można pracować ze znanymi bibliotekami przygotowywania i trenowania danych bez konieczności opuszczania notesu.

### <a name="access-dataset-in-training-script"></a>Uzyskiwanie dostępu do zestawu danych w skrypcie trenowania

Poniższy kod konfiguruje argument skryptu, który określisz podczas konfigurowania `--input-data` uruchomienia trenowania (zobacz następną sekcję). Gdy tabelarowy zestaw danych zostanie przekazany jako wartość argumentu, usługa Azure ML rozpozna ją jako identyfikator zestawu danych, którego następnie można użyć w celu uzyskania dostępu do zestawu danych w skrypcie trenowania (bez konieczności kodowania nazwy lub identyfikatora zestawu danych w skrypcie). Następnie używa metody do załadowania tego zestawu danych do ramki danych pandas w celu dalszej eksploracji i przygotowania danych [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) przed rozpoczęciem trenowania.

> [!Note]
> Jeśli oryginalne źródło danych zawiera ciągi NaN, puste ciągi lub wartości puste, podczas korzystania z funkcji te wartości są zastępowane `to_pandas_dataframe()` jako *wartość Null.*

Jeśli musisz załadować przygotowane dane do nowego zestawu danych z ramki danych pandas w pamięci, zapisz dane do pliku lokalnego, takiego jak parquet, i utwórz nowy zestaw danych na podstawie tego pliku. Dowiedz się więcej [na temat tworzenia zestawów danych.](how-to-create-register-datasets.md)

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>Konfigurowanie uruchomienia trenowania

Obiekt [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun) służy do konfigurowania i przesyłania przebiegów trenowania.

Ten kod tworzy obiekt ScriptRunConfig, `src` , który określa

* Katalog skryptów dla skryptów. Wszystkie pliki w tym katalogu są przekazywane do węzłów klastra w celu wykonania.
* Skrypt trenowania, *train_titanic.py.*
* Wejściowy zestaw danych do trenowania `titanic_ds` , jako argument skryptu. Usługa Azure ML rozpozna to jako odpowiedni identyfikator zestawu danych, gdy zostanie przekazany do skryptu.
* Docelowy obiekt obliczeniowy dla uruchomienia.
* Środowisko uruchomienia.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-data', titanic_ds.as_named_input('titanic')],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>Zainstaluj pliki na zdalnych docelowych wystąpieniach obliczeniowych

Jeśli masz dane bez struktury, utwórz zestaw [FileDataset](/python/api/azureml-core/azureml.data.filedataset) i zainstaluj lub pobierz pliki danych, aby udostępnić je do zdalnego docelowego obiektu obliczeniowego do trenowania. Dowiedz się, kiedy używać [instalacji, a](#mount-vs-download) kiedy pobierać do eksperymentów trenowania zdalnego. 

Poniższy przykład: 

* Tworzy wejściowy zestaw Danych Pliku `mnist_ds` dla danych szkoleniowych.
* Określa, gdzie zapisać wyniki trenowania i podniesieć te wyniki jako FileDataset.
* Zainstaluje wejściowy zestaw danych w docelowym obiektach obliczeniowych.

> [!Note]
> Jeśli używasz niestandardowego obrazu podstawowego platformy Docker, musisz zainstalować aplikację Fuse za pośrednictwem narzędzia jako zależność, aby instalacja zestawu `apt-get install -y fuse` danych działała. Dowiedz się, jak [utworzyć niestandardowy obraz kompilacji.](how-to-deploy-custom-docker-image.md#build-a-custom-base-image)

Aby uzyskać przykładowy notes, zobacz How to configure a training run with data input and output (Jak [skonfigurować przebieg trenowania przy użyciu danych wejściowych i wyjściowych).](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/scriptrun-with-data-input-output/how-to-use-scriptrun.ipynb)

### <a name="create-a-filedataset"></a>Tworzenie zestawu danych FileDataset

Poniższy przykład tworzy wyrejestrowany zestaw FileDataset z `mnist_data` internetowych adresów URL. Ten zestaw FileDataset to dane wejściowe dla przebiegów trenowania.

Dowiedz się więcej [na temat tworzenia zestawów danych z](how-to-create-register-datasets.md) innych źródeł.

```Python

from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]

mnist_ds = Dataset.File.from_files(path = web_paths)

```
### <a name="where-to-write-training-output"></a>Gdzie zapisywać dane wyjściowe trenowania

Możesz określić miejsce zapisu wyników trenowania za pomocą obiektu [OutputFileDatasetConfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig). 

Obiekty OutputFileDatasetConfig umożliwiają: 

* Zainstaluj lub przekaż dane wyjściowe uruchomienia do magazynu w chmurze, który określisz.
* Zapisz dane wyjściowe jako zestaw FileDataset dla następujących obsługiwanych typów magazynu:
    * Obiekt blob platformy Azure
    * Udział plików platformy Azure
    * Azure Data Lake Storage generacji 1 i 2
* Śledź pozyskane dane między przebiegami trenowania.

Poniższy kod określa, że wyniki trenowania powinny być zapisywane jako fileDataset w folderze w `outputdataset` domyślnym magazynze danych obiektów blob, `def_blob_store` . 

```python
from azureml.core import Workspace
from azureml.data import OutputFileDatasetConfig

ws = Workspace.from_config()

def_blob_store = ws.get_default_datastore()
output = OutputFileDatasetConfig(destination=(def_blob_store, 'sample/outputdataset'))
```

### <a name="configure-the-training-run"></a>Konfigurowanie uruchomienia trenowania

Zalecamy przekazanie zestawu danych jako argumentu podczas instalowanie za `arguments` pośrednictwem parametru `ScriptRunConfig` konstruktora. W ten sposób można uzyskać ścieżkę danych (punkt stawiania) w skrypcie trenowania za pośrednictwem argumentów. Dzięki temu będzie można używać tego samego skryptu trenowania na potrzeby debugowania lokalnego i zdalnego trenowania na dowolnej platformie w chmurze.

Poniższy przykład tworzy skrypt ScriptRunConfig, który przekazuje do zestawu danych FileDataset za pośrednictwem polecenia `arguments` . Po przesłaniu uruchomienia pliki danych, do których odnosi się zestaw danych, są instalowane do docelowego obiektu obliczeniowego, a wyniki trenowania są zapisywane w określonym folderze w `mnist_ds` `outputdataset` domyślnym sklepie danych.

```python
from azureml.core import ScriptRunConfig

input_data= mnist_ds.as_named_input('input').as_mount()# the dataset will be mounted on the remote compute 

src = ScriptRunConfig(source_directory=script_folder,
                      script='dummy_train.py',
                      arguments=[input_data, output],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="simple-training-script"></a>Prosty skrypt trenowania

Poniższy skrypt jest przesłany za pośrednictwem skryptu ScriptRunConfig. Odczytuje zestaw danych jako dane wejściowe i zapisuje plik w `mnist_ds ` `outputdataset` folderze w domyślnym obiekcie blob datastore, `def_blob_store` .

```Python
%%writefile $source_directory/dummy_train.py

# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License.
import sys
import os

print("*********************************************************")
print("Hello Azure ML!")

mounted_input_path = sys.argv[1]
mounted_output_path = sys.argv[2]

print("Argument 1: %s" % mounted_input_path)
print("Argument 2: %s" % mounted_output_path)
    
with open(mounted_input_path, 'r') as f:
    content = f.read()
    with open(os.path.join(mounted_output_path, 'output.csv'), 'w') as fw:
        fw.write(content)
```

## <a name="mount-vs-download"></a>Zainstaluj a pobierz

Instalowanie lub pobieranie plików w dowolnym formacie jest obsługiwane w przypadku zestawów danych utworzonych na podstawie usług Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database i Azure Database for PostgreSQL. 

Podczas instalacji **zestawu** danych dołączasz pliki, do których odwołuje się zestaw danych, do katalogu (punktu instalacji) i udostępnisz go w docelowym miejscu obliczeniowym. Instalowanie jest obsługiwane w przypadku obliczeń opartych na systemie Linux, w tym Azure Machine Learning obliczeniowych, maszyn wirtualnych i usługi HDInsight. 

Po **pobraniu** zestawu danych wszystkie pliki, do których odwołuje się zestaw danych, zostaną pobrane do docelowego obiektu obliczeniowego. Pobieranie jest obsługiwane dla wszystkich typów obliczeniowych. 

Jeśli skrypt przetwarza wszystkie pliki, do których odwołuje się zestaw danych, a dysk obliczeniowy może pasować do całego zestawu danych, zaleca się pobranie pliku w celu uniknięcia narzutu związanego z przesyłaniem strumieniowym danych z usług magazynu. Jeśli rozmiar danych przekracza rozmiar dysku obliczeniowego, pobieranie nie jest możliwe. W tym scenariuszu zalecamy instalowanie, ponieważ w czasie przetwarzania są ładowane tylko pliki danych używane przez skrypt.

Poniższy kod jest instalacji `dataset` w katalogu tymczasowym pod `mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="get-datasets-in-machine-learning-scripts"></a>Uzyskiwanie zestawów danych w skryptach uczenia maszynowego

Zarejestrowane zestawy danych są dostępne lokalnie i zdalnie w klastrach obliczeniowych, takich jak Azure Machine Learning obliczeniowe. Aby uzyskać dostęp do zarejestrowanego zestawu danych między eksperymentami, użyj następującego kodu, aby uzyskać dostęp do obszaru roboczego i pobrać zestaw danych, który był używany w wcześniej przesłanym uruchomieniu. Domyślnie metoda [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) w klasie zwraca najnowszą wersję zestawu danych zarejestrowanego `Dataset` w obszarze roboczym.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="access-source-code-during-training"></a>Uzyskiwanie dostępu do kodu źródłowego podczas trenowania

Usługa Azure Blob Storage ma większą szybkość przepływności niż udział plików platformy Azure i będzie skalowana do dużej liczby zadań uruchomionych równolegle. Z tego powodu zalecamy skonfigurowanie przebiegów do używania usługi Blob Storage do transferowania plików kodu źródłowego.

W poniższym przykładzie kodu określono w konfiguracji uruchamiania, który magazyn danych obiektów blob ma być transferem kodu źródłowego.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Przykłady notesów

+ Aby uzyskać dodatkowe przykłady i pojęcia dotyczące zestawów danych, zobacz [notesy zestawów danych](https://aka.ms/dataset-tutorial).
+ Zobacz, jak [parametryzować zestawy danych w potokach uczenia maszynowego.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* **Inicjowanie zestawu danych nie powiodło się: Uchybnianie** czasu oczekiwania na gotowość punktu instalacji: 
  * Jeśli nie masz żadnych reguł [](../virtual-network/network-security-groups-overview.md) sieciowej grupy zabezpieczeń dla ruchu wychodzącego i używasz programu , zaktualizuj i jego zależności jako najnowsze dla określonej wersji pomocniczej lub jeśli używasz ich w uruchomieniu, utwórz ponownie środowisko, aby jego najnowsza poprawka wraz z poprawką `azureml-sdk>=1.12.0` była `azureml-dataset-runtime` dostępna. 
  * Jeśli używasz programu `azureml-sdk<1.12.0` , uaktualnij program do najnowszej wersji.
  * Jeśli masz reguły sieciowej sieciowej organizacji ruchu wychodzącego, upewnij się, że istnieje reguła ruchu wychodzącego, która zezwala na cały ruch dla tagu usługi `AzureResourceMonitor` .

### <a name="overloaded-azurefile-storage"></a>Przeciążony magazyn AzureFile

Jeśli wystąpi `Unable to upload project files to working directory in AzureFile because the storage is overloaded` błąd, zastosuj następujące obejścia.

Jeśli używasz udziału plików dla innych obciążeń, takich jak transfer danych, zaleca się użycie obiektów blob, aby udział plików był bezpłatny do użycia podczas przesyłania przebiegów. Możesz również podzielić obciążenie między dwa różne obszary robocze.

### <a name="passing-data-as-input"></a>Przekazywanie danych jako danych wejściowych

*  **TypeError: FileNotFound:** Nie ma takiego pliku lub katalogu: Ten błąd występuje, jeśli podano ścieżkę pliku nie znajduje się plik. Musisz upewnić się, że sposób, w jaki odwoływuje się do pliku, jest spójny z tym, gdzie został zainstalowany zestaw danych na docelowym miejscu obliczeniowym. Aby zapewnić stan deterministyczny, zalecamy użycie ścieżki abstrakcyjnej podczas instalowanie zestawu danych do docelowego obiektu obliczeniowego. Na przykład w poniższym kodzie zainstalujemy zestaw danych w katalogu głównym systemu plików docelowego obiektu obliczeniowego, `/tmp` . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Jeśli nie dołączysz ukośnika "/", musisz dodać prefiks do katalogu roboczego, np. w docelowym miejscu obliczeniowym, aby wskazać miejsce, w którym zestaw danych ma zostać `/mnt/batch/.../tmp/dataset` zainstalowany.


## <a name="next-steps"></a>Następne kroki

* [Automatycznie trenuj modele uczenia maszynowego](how-to-configure-auto-train.md#data-source-and-format) przy użyciu zestawu Danych Tabelarównych.

* [Trenuj modele klasyfikacji obrazów](https://aka.ms/filedataset-samplenotebook) przy użyciu zestawu danych FileDataset.

* [Trenuj przy użyciu zestawów danych przy użyciu potoków](./how-to-create-machine-learning-pipelines.md).
