---
title: Uczenie się z usługą Azure DataSets
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać zestawów danych w szkoleniu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.openlocfilehash: 401383f2d483836bf725051810d78167869f7b22
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79283500"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Uczenie się z zestawami danych w Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ten artykuł zawiera informacje o dwóch sposobach używania [Azure Machine Learning zestawów danych](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) w ramach szkolenia w zakresie zdalnego eksperymentu, które są uruchamiane bez obaw o parametry połączenia lub ścieżki danych.

- Opcja 1: Jeśli masz dane ze strukturą, Utwórz TabularDataset i użyj go bezpośrednio w skrypcie szkoleniowym.

- Opcja 2: Jeśli masz dane bez struktury, Utwórz FileDataset i zainstaluj lub Pobierz pliki do zdalnego obliczenia na potrzeby szkolenia.

Azure Machine Learning zestawy danych zapewniają bezproblemową integrację z produktami szkoleniowymi Azure Machine Learning, takimi jak [ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py), [szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py), [predrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py) i [Azure Machine Learning](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby tworzyć zestawy danych i uczenia się z nich, potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

* [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md).

* [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), który obejmuje pakiet usługi Azure DataSets.

> [!Note]
> Niektóre klasy zestawu danych mają zależności w pakiecie [Azure preprodukcyjnym](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . W przypadku użytkowników systemu Linux te klasy są obsługiwane tylko w następujących dystrybucjach: Red Hat Enterprise Linux, Ubuntu, Fedora i CentOS.

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>Opcja 1. Korzystanie z zestawów danych bezpośrednio w skryptach szkoleniowych

W tym przykładzie utworzysz [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) i użyjesz go jako bezpośredniego wejścia do `estimator` obiektu do szkolenia. 

### <a name="create-a-tabulardataset"></a>Utwórz TabularDataset

Poniższy kod tworzy Wyrejestrowanie TabularDataset z adresu URL sieci Web. Zestawy danych można także tworzyć z plików lokalnych lub ścieżek w magazynach danych. Dowiedz się więcej [na temat tworzenia zestawów danych](https://aka.ms/azureml/howto/createdatasets).

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>Dostęp do wejściowego zestawu danych w skrypcie szkoleniowym

Obiekty TabularDataset zapewniają możliwość ładowania danych do Pandas lub Spark Dataframe, dzięki czemu można współpracować ze znanymi bibliotekami przygotowania i uczenia danych. Aby skorzystać z tej możliwości, można przekazać TabularDataset jako dane wejściowe w konfiguracji szkoleniowej, a następnie pobrać ją w skrypcie.

Aby to zrobić, uzyskaj dostęp do wejściowego zestawu [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) danych za pomocą obiektu w skrypcie szkoleniowym [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) i użyj metody. 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>Konfigurowanie szacowania

Obiekt [szacowania](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) jest używany do przesyłania eksperymentu. Azure Machine Learning wstępnie skonfigurowany szacowania dla popularnych platform uczenia maszynowego, a także ogólny szacowania.

Ten kod tworzy generyczny obiekt szacowania, `est`który określa

* Katalog skryptów dla skryptów. Wszystkie pliki w tym katalogu są przekazywane do węzłów klastra w celu wykonania.
* Skrypt szkoleniowy *train_titanic. PR*.
* Wejściowy zestaw danych do szkolenia `titanic`,. `as_named_input()`jest wymagany, aby wejściowy zestaw danych mógł zostać odwoływany przez przypisaną nazwę w skrypcie szkoleniowym. 
* Element docelowy obliczeń dla eksperymentu.
* Definicja środowiska dla eksperymentu.

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)
```


## <a name="option-2--mount-files-to-a-remote-compute-target"></a>Opcja 2: Instalowanie plików na zdalnym miejscu docelowym obliczeń

Jeśli chcesz, aby pliki danych były dostępne w celu obliczeń na potrzeby szkoleń, użyj [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) do instalowania lub pobierania plików, do których się odwołuje.

### <a name="mount-vs-download"></a>Instalacja a pobieranie

Instalowanie lub pobieranie plików dowolnego formatu jest obsługiwane w przypadku zestawów danych utworzonych z usługi Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database i Azure Database for PostgreSQL. 

Podczas instalowania zestawu danych należy dołączyć pliki, do których odwołuje się zestaw danych, do katalogu (punktu instalacji) i udostępnić je w celu obliczenia. Instalowanie jest obsługiwane w przypadku obliczeń opartych na systemie Linux, w tym Azure Machine Learning obliczeń, maszyn wirtualnych i usługi HDInsight. Podczas pobierania zestawu danych wszystkie pliki, do których odwołuje się zestaw danych, zostaną pobrane do elementu docelowego obliczeń. Pobieranie jest obsługiwane dla wszystkich typów obliczeń. 

Jeśli skrypt przetwarza wszystkie pliki, do których odwołuje się zestaw danych, a dysk obliczeniowy może pasować do pełnego zestawu danych, zaleca się pobranie, aby uniknąć naliczania danych przesyłanych strumieniowo z usług magazynu. Jeśli rozmiar danych przekracza rozmiar dysku, pobieranie nie jest możliwe. W tym scenariuszu zalecamy zamontowanie, ponieważ tylko pliki danych używane przez skrypt są ładowane w czasie przetwarzania.

Poniższy kod jest instalowany `dataset` w katalogu tymczasowym w`mounted_path`

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

### <a name="create-a-filedataset"></a>Utwórz FileDataset

Poniższy przykład tworzy niezarejestrowane FileDataset z adresów URL sieci Web. Dowiedz się więcej [na temat tworzenia zestawów danych](https://aka.ms/azureml/howto/createdatasets) z innych źródeł.

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

### <a name="configure-the-estimator"></a>Konfigurowanie szacowania

Oprócz przekazywania zestawu danych przez `inputs` parametr w szacowania, można również przekazać zestaw danych za `script_params` pomocą i uzyskać ścieżkę danych (punkt instalacji) w skrypcie szkoleniowym za pośrednictwem argumentów. W ten sposób można zachować skrypt szkoleniowy niezależnie od platformy Azure-SDK. Innymi słowy, będzie można użyć tego samego skryptu szkoleniowego na potrzeby debugowania lokalnego i zdalnego szkolenia na dowolnej platformie w chmurze.

Obiekt [skryptu sklearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) szacowania jest używany do przesyłania przebiegów dla eksperymentów scikit-uczyć się. Dowiedz się więcej o szkoleniu z [skryptu sklearn szacowania](how-to-train-scikit-learn.md).

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>Pobierz dane w skrypcie szkoleniowym

Po przesłaniu przebiegu pliki danych, do których odwołuje się `mnist` zestaw danych, zostaną zainstalowane do obiektu docelowego obliczeń. Poniższy kod przedstawia sposób pobierania danych w skrypcie.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through script_params in estimator
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

## <a name="notebook-examples"></a>Przykłady notesu

[Notesy zestawu danych](https://aka.ms/dataset-tutorial) pokazują i rozszerzają się w oparciu o Koncepcje opisane w tym artykule.

## <a name="next-steps"></a>Następne kroki

* [Autouczenie modeli uczenia maszynowego](how-to-auto-train-remote.md) za pomocą TabularDatasets

* [Uczenie modeli klasyfikacji obrazów](https://aka.ms/filedataset-samplenotebook) przy użyciu FileDatasets

* [Uczenie z zestawami danych przy użyciu potoków](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)
