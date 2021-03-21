---
title: Uczenie z zestawami danych uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak udostępnić dane dla lokalnego lub zdalnego obliczenia dla szkolenia modeli przy użyciu zestawów danych Azure Machine Learning.
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
ms.openlocfilehash: 15bad877be00e143ce6f6956a4e1f23378c275c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521785"
---
# <a name="train-models-with-azure-machine-learning-datasets"></a>Uczenie modeli za pomocą zestawów danych Azure Machine Learning 

W tym artykule dowiesz się, jak korzystać z [Azure Machine Learning zestawów danych](/python/api/azureml-core/azureml.core.dataset%28class%29) w celu uczenia modeli uczenia maszynowego.  Możesz użyć zestawów danych w lokalnym lub zdalnym miejscu docelowym, bez obaw o parametry połączenia lub ścieżki danych. 

Zestawy danych Azure Machine Learning zapewniają bezproblemową integrację z funkcjami szkolenia Azure Machine Learning, takimi jak [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig), [predrive](/python/api/azureml-train-core/azureml.train.hyperdrive) i [Azure Machine Learning Pipelines](./how-to-create-machine-learning-pipelines.md).

Jeśli nie masz gotowości do udostępnienia danych do szkolenia modeli, ale chcesz załadować dane do notesu w celu eksplorowania danych, zobacz jak [eksplorować dane w zestawie](how-to-create-register-datasets.md#explore-data)danych. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby tworzyć zestawy danych i uczenia się z nich, potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

* [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md).

* [Zestaw Azure Machine Learning SDK dla języka Python jest zainstalowany](/python/api/overview/azure/ml/install) (>= 1.13.0), który obejmuje `azureml-datasets` pakiet.

> [!Note]
> Niektóre klasy zestawu danych mają zależności w pakiecie [Azure preprodukcyjnym](/python/api/azureml-dataprep/) . W przypadku użytkowników systemu Linux te klasy są obsługiwane tylko w następujących dystrybucjach: Red Hat Enterprise Linux, Ubuntu, Fedora i CentOS.

## <a name="consume-datasets-in-machine-learning-training-scripts"></a>Korzystanie z zestawów danych w skryptach szkoleń dotyczących uczenia maszynowego

Jeśli masz dane strukturalne, które nie zostały jeszcze zarejestrowane jako zestaw danych, Utwórz TabularDataset i użyj go bezpośrednio w skrypcie szkoleniowym dla lokalnego lub zdalnego eksperymentu.

W tym przykładzie utworzysz niezarejestrowaną [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) i określisz ją jako argument skryptu w obiekcie ScriptRunConfig do szkolenia. Jeśli chcesz ponownie użyć tego TabularDataset z innymi eksperymentami w Twoim obszarze roboczym, zobacz [jak zarejestrować zestawy danych w obszarze roboczym](how-to-create-register-datasets.md#register-datasets).

### <a name="create-a-tabulardataset"></a>Utwórz TabularDataset

Poniższy kod tworzy Wyrejestrowanie TabularDataset z adresu URL sieci Web.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

Obiekty TabularDataset zapewniają możliwość ładowania danych w TabularDataset do Dataframe Pandas lub Spark, dzięki czemu możesz współpracować ze znanymi bibliotekami przygotowywania i uczenia danych bez konieczności opuszczania notesu.

### <a name="access-dataset-in-training-script"></a>Dostęp do zestawu danych w skrypcie szkoleniowym

Poniższy kod służy do konfigurowania argumentu skryptu `--input-data` , który zostanie określony podczas konfigurowania przebiegu szkoleniowego (zobacz następną sekcję). Gdy tabelaryczny zestaw danych jest przenoszona jako wartość argumentu, usługa Azure ML rozwiąże to identyfikator zestawu danych, którego można następnie użyć do uzyskania dostępu do zestawu danych w skrypcie szkoleniowym (bez konieczności umieszczaj nazwy lub identyfikatora zestawu danych w skrypcie). Następnie używa [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) metody do ładowania tego zestawu danych do Pandas Dataframe w celu dalszej eksploracji i przygotowania danych przed szkoleniem.

> [!Note]
> Jeśli oryginalne źródło danych zawiera wartość NaN, puste ciągi lub puste wartości, w przypadku użycia `to_pandas_dataframe()` wartości te są zastępowane wartością *null* .

Jeśli zachodzi potrzeba załadowania przygotowanego danych do nowego zestawu danych z Pandas Dataframe w pamięci, Zapisz dane w lokalnym pliku, takim jak Parquet, i Utwórz nowy zestaw danych z tego pliku. Dowiedz się więcej [na temat tworzenia zestawów danych](how-to-create-register-datasets.md).

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

### <a name="configure-the-training-run"></a>Konfigurowanie przebiegu szkoleniowego

Obiekt [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun) służy do konfigurowania i przesyłania przebiegu szkoleniowego.

Ten kod tworzy obiekt ScriptRunConfig, `src` który określa

* Katalog skryptów dla skryptów. Wszystkie pliki w tym katalogu są przekazywane do węzłów klastra w celu wykonania.
* Skrypt szkoleniowy *train_titanic. PR*.
* Wejściowy zestaw danych dla szkolenia, `titanic_ds` jako argument skryptu. Usługa Azure ML rozwiąże to z odpowiednim IDENTYFIKATORem zestawu danych, gdy zostanie on przesłany do skryptu.
* Obiekt docelowy obliczeń dla przebiegu.
* Środowisko dla uruchomienia.

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

## <a name="mount-files-to-remote-compute-targets"></a>Instaluj pliki do zdalnych obiektów docelowych obliczeń

Jeśli masz dane bez struktury, Utwórz [FileDataset](/python/api/azureml-core/azureml.data.filedataset) i zainstaluj lub Pobierz pliki danych, aby udostępnić je dla zdalnego celu obliczeń na potrzeby szkolenia. Informacje o tym, kiedy należy użyć [instalacji i pobrania](#mount-vs-download) na potrzeby zdalnego eksperymentów szkoleniowych. 

Poniższy przykład tworzy FileDataset i instaluje zestaw danych do elementu docelowego obliczeń przez przekazanie go jako argumentu do skryptu szkoleniowego. 

> [!Note]
> W przypadku korzystania z niestandardowego obrazu platformy Docker należy zainstalować bezpiecznik za pośrednictwem programu `apt-get install -y fuse` jako zależność instalacji zestawu danych do pracy. Dowiedz się [, jak utworzyć niestandardowy obraz kompilacji](how-to-deploy-custom-docker-image.md#build-a-custom-base-image).

### <a name="create-a-filedataset"></a>Utwórz FileDataset

Poniższy przykład tworzy niezarejestrowane FileDataset z adresów URL sieci Web. Dowiedz się więcej [na temat tworzenia zestawów danych](how-to-create-register-datasets.md) z innych źródeł.

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

### <a name="configure-the-training-run"></a>Konfigurowanie przebiegu szkoleniowego

Zalecamy przekazanie zestawu danych jako argumentu podczas instalowania za pośrednictwem `arguments` parametru `ScriptRunConfig` konstruktora. Wykonanie tej czynności spowoduje uzyskanie ścieżki danych (punktu instalacji) w skrypcie szkoleniowym przez argumenty. Dzięki temu będzie można użyć tego samego skryptu szkoleniowego na potrzeby debugowania lokalnego i zdalnego szkolenia na dowolnej platformie w chmurze.

Poniższy przykład tworzy ScriptRunConfig, który przekazuje w FileDataset za pośrednictwem `arguments` . Po przesłaniu przebiegu pliki danych, do których odwołuje się zestaw danych, `mnist_ds` zostaną zainstalowane do obiektu docelowego obliczeń.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_mnist.py',
                      # the dataset will be mounted on the remote compute and the mounted path passed as an argument to the script
                      arguments=['--data-folder', mnist_ds.as_mount(), '--regularization', 0.5],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-data-in-your-training-script"></a>Pobieranie danych w skrypcie szkoleniowym

Poniższy kod przedstawia sposób pobierania danych w skrypcie.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through `arguments` in the ScriptRunConfig
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="mount-vs-download"></a>Zainstaluj i Pobierz

Instalowanie lub pobieranie plików dowolnego formatu jest obsługiwane w przypadku zestawów danych utworzonych z usługi Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database i Azure Database for PostgreSQL. 

Podczas **instalowania** zestawu danych należy dołączyć pliki, do których odwołuje się zestaw danych, do katalogu (punktu instalacji) i udostępnić je w celu obliczenia. Instalowanie jest obsługiwane w przypadku obliczeń opartych na systemie Linux, w tym Azure Machine Learning obliczeń, maszyn wirtualnych i usługi HDInsight. 

Podczas **pobierania** zestawu danych wszystkie pliki, do których odwołuje się zestaw danych, zostaną pobrane do elementu docelowego obliczeń. Pobieranie jest obsługiwane dla wszystkich typów obliczeń. 

Jeśli skrypt przetwarza wszystkie pliki, do których odwołuje się zestaw danych, a dysk obliczeniowy może pasować do pełnego zestawu danych, zaleca się pobranie, aby uniknąć naliczania danych przesyłanych strumieniowo z usług magazynu. Jeśli rozmiar danych przekracza rozmiar dysku, pobieranie nie jest możliwe. W tym scenariuszu zalecamy zamontowanie, ponieważ tylko pliki danych używane przez skrypt są ładowane w czasie przetwarzania.

Poniższy kod jest instalowany w `dataset` katalogu tymczasowym w `mounted_path`

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

## <a name="get-datasets-in-machine-learning-scripts"></a>Pobieranie zestawów danych w skryptach uczenia maszynowego

Zarejestrowane zestawy danych są dostępne lokalnie i zdalnie w klastrach obliczeniowych, takich jak Azure Machine Learning COMPUTE. Aby uzyskać dostęp do zarejestrowanego zestawu danych w ramach eksperymentów, użyj poniższego kodu, aby uzyskać dostęp do obszaru roboczego i uzyskać zestaw danych, który został użyty w wcześniej przesłanym przebiegu. Domyślnie [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) Metoda `Dataset` klasy zwraca najnowszą wersję zestawu danych, który jest zarejestrowany w obszarze roboczym.

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

## <a name="access-source-code-during-training"></a>Dostęp do kodu źródłowego podczas szkolenia

Usługa Azure Blob Storage ma większe szybkości przepływności niż udział plików platformy Azure i skalowanie na dużą liczbę zadań uruchomionych równolegle. Z tego powodu zalecamy skonfigurowanie przebiegów do korzystania z usługi BLOB Storage na potrzeby przesyłania plików kodu źródłowego.

Poniższy przykład kodu określa w konfiguracji uruchamiania, która magazyn danych obiektów BLOB ma być używana do transferów kodu źródłowego.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Przykłady notesu

+ [Notesy zestawu danych](https://aka.ms/dataset-tutorial) pokazują i rozszerzają się w oparciu o Koncepcje opisane w tym artykule.
+ Zobacz, jak [parametrize zestawy danych w potokach ml](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* **Nie można zainicjować zestawu danych: upłynął limit czasu oczekiwania na gotowość punktu instalacji**: 
  * Jeśli nie masz żadnych reguł [sieciowej grupy zabezpieczeń](../virtual-network/network-security-groups-overview.md) dla ruchu wychodzącego i korzystasz z usługi `azureml-sdk>=1.12.0` , zaktualizuj `azureml-dataset-runtime` i jej zależności jako najnowsze dla konkretnej wersji pomocniczej lub jeśli używasz go w przebiegu, Utwórz ponownie środowisko, aby mogło ono mieć najnowszą poprawkę z poprawkami. 
  * Jeśli używasz programu `azureml-sdk<1.12.0` , przeprowadź uaktualnienie do najnowszej wersji.
  * Jeśli istnieją reguły sieciowej grupy zabezpieczeń wychodzącego, upewnij się, że istnieje reguła ruchu wychodzącego zezwalająca na cały ruch dla tagu usługi `AzureResourceMonitor` .

### <a name="overloaded-azurefile-storage"></a>Przeciążony magazyn AzureFile

Jeśli wystąpi błąd `Unable to upload project files to working directory in AzureFile because the storage is overloaded` , zastosuj następujące obejścia.

Jeśli używasz udziału plików dla innych obciążeń, takich jak transfer danych, zalecenie polega na użyciu obiektów blob, dzięki czemu udział plików jest bezpłatny do użycia na potrzeby przesyłania przebiegów. Obciążenie można także podzielić między dwa różne obszary robocze.

### <a name="passing-data-as-input"></a>Przekazywanie danych jako danych wejściowych

*  **TypeError: FileNotFound: Brak pliku lub katalogu**: ten błąd występuje, gdy podano ścieżkę do pliku, w którym znajduje się plik. Należy upewnić się, że sposób odwoływania się do pliku jest zgodny z miejscem, w którym został zainstalowany zestaw danych na obiekcie docelowym obliczeń. Aby zapewnić jednoznaczny stan, zalecamy użycie ścieżki abstrakcyjnej podczas instalowania zestawu danych do obiektu docelowego obliczeń. Na przykład, w poniższym kodzie instalujemy zestaw danych w katalogu głównym systemu plików obiektu docelowego obliczeń `/tmp` . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Jeśli nie dołączysz wiodącego ukośnika "/", musisz utworzyć prefiks katalogu roboczego, np. `/mnt/batch/.../tmp/dataset` w elemencie docelowym obliczeń, aby wskazać, gdzie ma być zainstalowany zestaw danych.


## <a name="next-steps"></a>Następne kroki

* [Funkcja autouczenie modeli uczenia maszynowego](how-to-auto-train-remote.md) z TabularDatasets.

* [Uczenie modeli klasyfikacji obrazów](https://aka.ms/filedataset-samplenotebook) przy użyciu FileDatasets.

* [Uczenie się z zestawami danych przy użyciu potoków](./how-to-create-machine-learning-pipelines.md).