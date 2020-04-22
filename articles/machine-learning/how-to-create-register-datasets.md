---
title: Tworzenie zestawów danych usługi Azure Machine Learning w celu uzyskania dostępu do danych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć zestawy danych usługi Azure Machine Learning, aby uzyskać dostęp do danych w przypadku przebiegów eksperymentu uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 02/10/2020
ms.openlocfilehash: cc7a8df80e719173c7818055ab8771ddd7f73691
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682771"
---
# <a name="create-azure-machine-learning-datasets"></a>Tworzenie zestawów danych usługi Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak utworzyć zestawy danych usługi Azure Machine Learning, aby uzyskać dostęp do danych dla eksperymentów lokalnych lub zdalnych.

Za pomocą zestawów danych usługi Azure Machine Learning można:

* Przechowuj pojedynczą kopię danych w magazynie, do których odwołują się zestawy danych.

* Bezproblemowy dostęp do danych podczas szkolenia modelu bez martwienia się o parametry połączenia lub ścieżki danych.

* Udostępniaj dane i współpracuj z innymi użytkownikami.

## <a name="prerequisites"></a>Wymagania wstępne
' Aby tworzyć zestawy danych i pracować z zestawami danych, potrzebujesz:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree).

* [Obszar roboczy usługi Azure Machine Learning](how-to-manage-workspace.md).

* [Zainstalowano zestaw SDK usługi Azure Machine Learning dla języka Python,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)który zawiera pakiet zestawów danych azureml.

> [!NOTE]
> Niektóre klasy zestawów danych mają zależności w pakiecie [azureml-dataprep.](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) Dla użytkowników systemu Linux te klasy są obsługiwane tylko w następujących dystrybucjach: Red Hat Enterprise Linux, Ubuntu, Fedora i CentOS.

## <a name="compute-size-guidance"></a>Wskazówki dotyczące rozmiaru obliczeń

Podczas tworzenia zestawu danych przejrzyj moc obliczeniową i rozmiar danych w pamięci. Rozmiar danych w magazynie nie jest taki sam jak rozmiar danych w ramce danych. Na przykład dane w plikach CSV mogą rozszerzać się do 10x w ramce danych, więc plik CSV o rozmiarze 1 GB może stać się 10 GB w ramce danych. 

Głównym czynnikiem jest to, jak duży jest zestaw danych w pamięci, czyli jako dataframe. Zalecamy, aby rozmiar obliczeń i moc obliczeniowa zawierały 2 x rozmiar pamięci RAM. Jeśli więc twoja rama danych ma 10 GB, chcesz mieć cel obliczeniowy z ponad 20 GB pamięci RAM, aby zapewnić, że ramka danych może wygodnie zmieścić się w pamięci i zostać przetworzona. Jeśli dane są skompresowane, można je dalej rozszerzać; 20 GB stosunkowo rzadkich danych przechowywanych w skompresowanym formacie parkietu może rozwinąć się do ~ 800 GB w pamięci. Ponieważ pliki Parkietu przechowują dane w formacie kolumnowym, jeśli potrzebujesz tylko połowy kolumn, wystarczy załadować ~400 GB w pamięci.
 
Jeśli używasz Pandas, nie ma powodu, aby mieć więcej niż 1 vCPU, ponieważ to wszystko będzie używać. Można łatwo zrównać z wieloma procesorami wirtualnymi w jednym wystąpieniu/węźle obliczeniowym usługi Azure Machine Learning za `import pandas as pd` pośrednictwem `import modin.pandas as pd`modin i Dask/Ray i skalować w poziomie do dużego klastra, po prostu zmieniając na . 
 
Jeśli nie możesz uzyskać wystarczająco dużej wirtualnej dla danych, masz dwie opcje: użyj struktury, takiej jak Spark lub Dask, aby wykonać przetwarzanie danych "z pamięci", czyli dataframe jest ładowany do partycji RAM przez partycję i przetwarzany, a ostateczny wynik jest zbierany na końcu. Jeśli jest to zbyt wolne, Spark lub Dask umożliwiają skalowanie w poziomie do klastra, który nadal może być używany interaktywnie. 

## <a name="dataset-types"></a>Typy zestawów danych

Istnieją dwa typy zestawów danych, w zależności od sposobu, w jaki użytkownicy zużywają je w szkoleniach:

* [Zestaw danych tabelarykowanych](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) reprezentuje dane w formacie tabelarycznym, analizując podany plik lub listę plików. Zapewnia to możliwość zmaterializować dane w Pandas lub Spark DataFrame. `TabularDataset` Obiekt można utworzyć z plików csv, tsv, .parquet, .jsonl i z wyników kwerendy SQL. Aby uzyskać pełną listę, zobacz [TabularDatasetFactory klasy](https://aka.ms/tabulardataset-api-reference).

* [Klasa FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) odwołuje się do pojedynczych lub wielu plików w magazynach danych lub publicznych adresach URL. Za pomocą tej metody można pobrać lub zainstalować pliki do obliczeń jako FileDataset obiektu. Pliki mogą być w dowolnym formacie, co umożliwia szerszy zakres scenariuszy uczenia maszynowego, w tym uczenia głębokiego.

Aby dowiedzieć się więcej o nadchodzących zmianach interfejsu API, zobacz [Powiadomienie o zmianie interfejsu API zestawu danych](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Tworzenie zestawów danych

Tworząc zestaw danych, można utworzyć odwołanie do lokalizacji źródła danych wraz z kopią jego metadanych. Ponieważ dane pozostają w istniejącej lokalizacji, nie ponosisz żadnych dodatkowych kosztów magazynowania. Można tworzyć `TabularDataset` zarówno `FileDataset` i zestawy danych przy użyciu https://ml.azure.comzestawów SDK Języka Python lub w .

Aby dane były dostępne dla usługi Azure Machine Learning, zestawy danych muszą być tworzone na podstawie ścieżek w [magazynach danych platformy Azure](how-to-access-data.md) lub publicznych adresów URL sieci Web. 

### <a name="use-the-sdk"></a>Korzystanie z SDK

Aby utworzyć zestawy danych z [magazynu danych platformy Azure](how-to-access-data.md) przy użyciu zestawu SDK języka Python:

1. Sprawdź, czy `contributor` `owner` masz lub masz dostęp do zarejestrowanego magazynu danych platformy Azure.

2. Utwórz zestaw danych, odwołując się do ścieżek w magazynie danych.
> [!Note]
> Można utworzyć zestaw danych z wielu ścieżek w wielu magazynach danych. Nie ma twardego limitu liczby plików lub rozmiaru danych, z których można utworzyć zestaw danych. Jednak dla każdej ścieżki danych kilka żądań zostanie wysłanych do usługi magazynu, aby sprawdzić, czy wskazuje plik lub folder. To obciążenie może prowadzić do obniżenia wydajności lub awarii. Zestaw danych odwołujący się do jednego folderu z 1000 plikami wewnątrz jest uważany za odwołujący się do jednej ścieżki danych. Firma Microsoft zaleca tworzenie zestawu danych odwołujących się do mniej niż 100 ścieżek w magazynach danych w celu uzyskania optymalnej wydajności.

#### <a name="create-a-tabulardataset"></a>Tworzenie zestawu tabelaryczne dane

[`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-) Metoda w `TabularDatasetFactory` klasie służy do odczytywania plików w formacie csv lub .tsv oraz do tworzenia niezarejestrowanego zestawu tabelaryczne. Jeśli czytasz z wielu plików, wyniki zostaną zagregowane w jedną reprezentację tabelaryczne. 

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Domyślnie podczas tworzenia zestawu danych tabelarycznego typy danych kolumn są wywnioskowane automatycznie. Jeśli wywnioskowane typy nie są zgodne z twoimi oczekiwaniami, można określić typy kolumn przy użyciu następującego kodu. Parametr `infer_column_type` ma zastosowanie tylko do zestawów danych utworzonych z plików rozdzielanych. Możesz również [dowiedzieć się więcej o obsługiwanych typach danych](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py).

> [!IMPORTANT] 
> Jeśli magazyn znajduje się za siecią wirtualną lub zaporą, obsługiwane jest tylko tworzenie zestawu danych za pośrednictwem zestawu SDK. Aby utworzyć zestaw danych, należy uwzględnić `infer_column_types=False` parametry `from_delimited_files()` `validate=False` i metodę. Spowoduje to ominięcie początkowego sprawdzania poprawności i zapewnia, że można utworzyć zestaw danych z tych bezpiecznych plików. 

```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |OsobowyId|Przeżył|Pclass (klasa pclass)|Nazwa|Seks|Wiek|SibSp (sibsp)|Parch (parch)|Bilet|Taryfy|Kabiny|Rozpoczęła
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Pan Owen Harris|mężczyzna|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Pani John Bradley (Florence Briggs Th ...|kobieta|38.0|1|0|Szt.|71.2833|C85|C
2|3|True|3|Heikkinen, Miss. Laina (Laina)|kobieta|26.0|0|0|STON/O2. 3101282|7.9250||S


Aby utworzyć zestaw danych z ramki danych pandas w pamięci, zapisz je w pliku lokalnym, takim jak plik csv, i utwórz zestaw danych z tego pliku. Poniższy kod demonstruje ten przepływ pracy.

```python
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required
from azureml.core import Workspace, Dataset

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')
# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(datastore.path('data/prepared.csv'))
```

Użyj [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-) metody w `TabularDatasetFactory` klasie do odczytu z usługi Azure SQL Database:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

W tabelaryczne zestawy danych można określić sygnaturę czasową z kolumny w danych lub z dowolnego miejsca, w której są przechowywane dane wzorca ścieżki, aby włączyć cechę szeregów czasowych. Ta specyfikacja pozwala na łatwe i wydajne filtrowanie według czasu.

Użyj [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) metody w`TabularDataset` klasie, aby określić kolumnę sygnatury czasowej i włączyć filtrowanie według czasu. Aby uzyskać więcej informacji, zobacz [tabelaryczne demo interfejsu API związane z szeregami czasowymi z danymi pogodowymi NOAA](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

```Python
# create a TabularDataset with time series trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with time-series-trait-specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```

#### <a name="create-a-filedataset"></a>Tworzenie zestawu danych plików

Użyj [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) metody w `FileDatasetFactory` klasie, aby załadować pliki w dowolnym formacie i utworzyć niezarejestrowany zestaw danych filedata. Jeśli magazyn znajduje się za siecią wirtualną `validate =False` lub `from_files()` zaporą, ustaw parametr w metodzie. Spowoduje to ominięcie początkowego kroku sprawdzania poprawności i zapewnia, że można utworzyć zestaw danych z tych bezpiecznych plików.

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>W Internecie 
Poniższe kroki i animacje pokazują, jak utworzyć zestaw https://ml.azure.comdanych w studiu usługi Azure Machine Learning.

![Tworzenie zestawu danych za pomocą interfejsu użytkownika](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Aby utworzyć zestaw danych w studio:
1. Zaloguj się https://ml.azure.comw .
1. Wybierz **pozycję Zestawy danych** w sekcji **Zasoby** lewego okienka. 
1. Wybierz **pozycję Utwórz zestaw danych,** aby wybrać źródło zestawu danych. To źródło może być pliki lokalne, magazyn danych lub publiczne adresy URL.
1. Wybierz **opcję Tabelaryczny** lub **Plik** dla typu zestawu danych.
1. Wybierz **przycisk Dalej,** aby otworzyć formularz **Datastore i wybór pliku.** W tym formularzu należy wybrać miejsce przechowywania zestawu danych po utworzeniu, a także wybrać pliki danych, które mają być używane dla zestawu danych. 
1. Wybierz **przycisk Dalej,** aby wypełnić **ustawienia i podgląd** oraz formularze **schematu;** są inteligentnie wypełniane na podstawie typu pliku i można dodatkowo skonfigurować zestaw danych przed utworzeniem w tych formularzach. 
1. Wybierz **przycisk Dalej,** aby przejrzeć formularz **Potwierdź szczegóły.** Sprawdź wybrane opcje i utwórz opcjonalny profil danych dla zestawu danych. Dowiedz się więcej o [profilowaniu danych](how-to-use-automated-ml-for-ml-models.md#profile). 
1. Wybierz **pozycję Utwórz,** aby zakończyć tworzenie zestawu danych.

## <a name="register-datasets"></a>Rejestrowanie zestawów danych

Aby zakończyć proces tworzenia, zarejestruj zestawy danych w obszarze roboczym. Użyj [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) tej metody, aby zarejestrować zestawy danych w obszarze roboczym, aby udostępnić je innym osobom i ponownie wykorzystać je w różnych eksperymentach:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Zestawy danych utworzone za pośrednictwem usługi Azure Machine Learning studio są automatycznie rejestrowane w obszarze roboczym.

## <a name="create-datasets-with-azure-open-datasets"></a>Tworzenie zestawów danych za pomocą zestawów danych Azure Open

[Zestawy danych azure open](https://azure.microsoft.com/services/open-datasets/) są wyselekcjonowane publicznych zestawów danych, których można użyć do dodawania funkcji specyficznych dla scenariusza do rozwiązań uczenia maszynowego dla bardziej dokładnych modeli. Zestawy danych obejmują dane z domeny publicznej dotyczące pogody, spisu ludności, świąt, bezpieczeństwa publicznego i lokalizacji, które pomagają szkolić modele uczenia maszynowego i wzbogacać rozwiązania predykcyjne. Otwarte zestawy danych znajdują się w chmurze na platformie Microsoft Azure i są uwzględniane zarówno w zestawie SDK, jak i w interfejsie użytkownika obszaru roboczego.

### <a name="use-the-sdk"></a>Korzystanie z SDK

Aby utworzyć zestawy danych za pomocą zestawów danych Azure Open z zestawu SDK, upewnij się, że pakiet został zainstalowany za pomocą `pip install azureml-opendatasets`. Każdy zestaw danych dyskretnych jest reprezentowany przez własną klasę w zestawie SDK, a niektóre klasy są dostępne jako `TabularDataset`, `FileDataset`, lub oba. Pełna lista klas znajduje się w [dokumentacji referencyjnej.](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)

Można pobrać niektóre klasy jako `TabularDataset` lub `FileDataset`, co pozwala na manipulowanie i/lub pobieranie plików bezpośrednio. Inne klasy można uzyskać **only** zestaw danych `get_tabular_dataset()` tylko `get_file_dataset()` przy użyciu jednej z lub funkcji. Poniższy przykład kodu zawiera kilka przykładów tych typów klas.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Podczas rejestrowania zestawu danych utworzonego z otwartych zestawów danych żadne dane nie są natychmiast pobierane, ale dane będą dostępne później, gdy zażądasz (na przykład podczas szkolenia) z centralnej lokalizacji magazynu.

### <a name="use-the-ui"></a>Korzystanie z interfejsu użytkownika

Można również tworzyć zestawy danych z klas Otwartych zestawów danych za pośrednictwem interfejsu użytkownika. W obszarze roboczym wybierz kartę **Zestawy danych** w obszarze **Zasoby**. Z menu rozwijanego **Tworzenie zestawu danych** wybierz polecenie Z otwartych **zestawów danych**.

![Otwórz zestaw danych z interfejsem użytkownika](./media/how-to-create-register-datasets/open-datasets-1.png)

Wybierz zestaw danych, zaznaczając jego kafelek. (Możesz filtrować za pomocą paska wyszukiwania). Wybierz **pozycję Dalej**.

![Wybieranie zestawu danych](./media/how-to-create-register-datasets/open-datasets-2.png)

Wybierz nazwę, pod którą ma się zarejestrować zestaw danych, i opcjonalnie filtruj dane przy użyciu dostępnych filtrów. W takim przypadku dla zestawu danych dni wolnych od pracy filtrujesz okres do jednego roku i kod kraju tylko do STANÓW Zjednoczonych. Wybierz pozycję **Utwórz**.

![Ustawianie params zestawu danych i tworzenie zestawu danych](./media/how-to-create-register-datasets/open-datasets-3.png)

Zestaw danych jest teraz dostępny w obszarze roboczym w obszarze **Zestawy danych**. Można go używać w taki sam sposób, jak inne utworzone zestawy danych.

## <a name="version-datasets"></a>Zestawy danych wersji

Nowy zestaw danych o tej samej nazwie można zarejestrować, tworząc nową wersję. Wersja zestawu danych to sposób do zakładki stanu danych, dzięki czemu można zastosować określoną wersję zestawu danych do eksperymentowania lub przyszłej reprodukcji. Dowiedz się więcej o [wersjach zestawów danych](how-to-version-track-datasets.md).
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="access-datasets-in-your-script"></a>Dostęp do zestawów danych w skrypcie

Zarejestrowane zestawy danych są dostępne lokalnie i zdalnie w klastrach obliczeniowych, takich jak obliczenia usługi Azure Machine Learning. Aby uzyskać dostęp do zarejestrowanego zestawu danych w eksperymentach, użyj następującego kodu, aby uzyskać dostęp do obszaru roboczego i zarejestrowanego zestawu danych według nazwy. Domyślnie metoda [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) w `Dataset` klasie zwraca najnowszą wersję zestawu danych, który jest zarejestrowany w obszarze roboczym.

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

## <a name="next-steps"></a>Następne kroki

* Dowiedz [się, jak trenować z zestawami danych](how-to-train-with-datasets.md).
* Użyj automatycznego uczenia maszynowego, aby [trenować za pomocą zestawów danych tabular.](https://aka.ms/automl-dataset)
* Aby uzyskać więcej przykładów szkolenia zestawu danych, zobacz [przykładowe notesy](https://aka.ms/dataset-tutorial).
