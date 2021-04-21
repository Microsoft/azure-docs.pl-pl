---
title: Tworzenie zestawów danych usługi Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć Azure Machine Learning danych w celu uzyskiwania dostępu do danych w przypadku przebiegów eksperymentów uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, data4ml
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: f47d610a24de2cfc8f1131f61afc8c8173a34376
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786625"
---
# <a name="create-azure-machine-learning-datasets"></a>Tworzenie zestawów danych usługi Azure Machine Learning

Z tego artykułu dowiesz się, jak tworzyć zestawy Azure Machine Learning danych w celu uzyskiwania dostępu do danych dla eksperymentów lokalnych lub zdalnych przy użyciu zestawu SDK Azure Machine Learning Python. Aby dowiedzieć się, gdzie zestawy danych Azure Machine Learning przepływie pracy dostępu do danych, zobacz artykuł [Bezpieczny dostęp do](concept-data.md#data-workflow) danych.

Tworząc zestaw danych, tworzysz odwołanie do lokalizacji źródła danych wraz z kopią jego metadanych. Ponieważ dane pozostają w istniejącej lokalizacji, nie są naliczane żadne dodatkowe opłaty za magazyn i nie ryzykowne jest integralność źródeł danych. Zestawy danych są również oceniane z rozmachem, co pomaga w szybkościach wydajności przepływu pracy. Zestawy danych można tworzyć z magazynów danych, publicznych adresów URL [i Azure Open Datasets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).

Aby uzyskać środowisko z małą jakością kodu, [Azure Machine Learning zestawy danych za pomocą Azure Machine Learning studio.](how-to-connect-data-ui.md#create-datasets)

Za Azure Machine Learning zestawy danych można:

* Zachowaj pojedynczą kopię danych w magazynie, do których odwołują się zestawy danych.

* Bezproblemowy dostęp do danych podczas trenowania modelu bez martwienia się o parametry połączenia lub ścieżki danych. [Dowiedz się więcej na temat trenowania przy użyciu zestawów danych.](how-to-train-with-datasets.md)

* Udostępnianie danych i współpraca z innymi użytkownikami.

## <a name="prerequisites"></a>Wymagania wstępne

Do tworzenia zestawów danych i pracy z nich potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning.](https://aka.ms/AMLFree)

* Obszar [Azure Machine Learning obszaru roboczego.](how-to-manage-workspace.md)

* Zainstalowany [Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/install), który zawiera pakiet azureml-datasets.

    * Utwórz wystąpienie [Azure Machine Learning obliczeniowego](how-to-create-manage-compute-instance.md), które jest w pełni skonfigurowanym i zarządzanym środowiskiem projektowym, które obejmuje zintegrowane notesy i już zainstalowany zestaw SDK.

    **OR**

    * Pracuj nad własnym notesem Jupyter i zainstaluj zestaw SDK samodzielnie, [samodzielnie, instaluj te instrukcje.](/python/api/overview/azure/ml/install)

> [!NOTE]
> Niektóre klasy zestawów danych są zależne od [pakietu azureml-dataprep,](https://pypi.org/project/azureml-dataprep/) który jest zgodny tylko z 64-bitowym środowiskiem Python. Dla użytkowników systemu Linux te klasy są obsługiwane tylko w następujących dystrybucjach: Red Hat Enterprise Linux (7, 8), Ubuntu (14.04, 16.04, 18.04), Fedora (27, 28), Debian (8, 9) i CentOS (7). Jeśli używasz nieobsługiwanych dystrybucji, postępuj zgodnie [](/dotnet/core/install/linux) z tym przewodnikiem, aby zainstalować program .NET Core 2.1, aby kontynuować. 

## <a name="compute-size-guidance"></a>Wskazówki dotyczące rozmiaru obliczeniowego

Podczas tworzenia zestawu danych przejrzyj moc obliczeniową i rozmiar danych w pamięci. Rozmiar danych w magazynie nie jest taki sam jak rozmiar danych w ramce danych. Na przykład dane w plikach CSV można rozszerzyć do 10 razy w ramce danych, dzięki czemu plik CSV o rozmiarze 1 GB może stać się 10 GB w ramce danych. 

Jeśli dane są skompresowane, można je rozszerzyć. 20 GB względnie rozrzedowanych danych przechowywanych w skompresowanym formacie Parquet może zostać rozszerzonych do ok. 800 GB w pamięci. Ponieważ pliki Parquet przechowują dane w formacie kolumnowym, jeśli potrzebujesz tylko połowy kolumn, wystarczy załadować tylko ok. 400 GB w pamięci.

[Dowiedz się więcej na temat optymalizacji przetwarzania danych w Azure Machine Learning](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Typy zestawów danych

Istnieją dwa typy zestawów danych w zależności od sposobu, w jaki użytkownicy z nich korzystać podczas trenowania. FileDatasets i TabularDatasets. Oba typy mogą być używane w Azure Machine Learning przepływach pracy trenowania obejmujących estymatory, automl, hyperDrive i potoki. 

### <a name="filedataset"></a>FileDataset

Zestaw [FileDataset odwołuje](/python/api/azureml-core/azureml.data.file_dataset.filedataset) się do jednego lub wielu plików w magazynach danych lub publicznych adresach URL. Jeśli dane są już oczyszczone i gotowe do użycia [](how-to-train-with-datasets.md#mount-vs-download) w eksperymentach szkoleniowych, możesz pobrać lub zainstalować pliki do obliczeń jako obiekt FileDataset. 

Zalecamy używanie zestawu danych FileDatasets dla przepływów pracy uczenia maszynowego, ponieważ pliki źródłowe mogą być w dowolnym formacie, co umożliwia korzystanie z szerszego zakresu scenariuszy uczenia maszynowego, w tym uczenia głębokiego.

Utwórz zestaw FileDataset przy użyciu [zestawu SDK języka Python](#create-a-filedataset) lub [Azure Machine Learning studio](how-to-connect-data-ui.md#create-datasets) .
### <a name="tabulardataset"></a>TabularDataset

[TabularDataset reprezentuje](/python/api/azureml-core/azureml.data.tabulardataset) dane w formacie tabelaryjnym przez analizowanie dostarczonego pliku lub listy plików. Dzięki temu można zmaterializować dane w ramce danych biblioteki pandas lub Spark, aby można było pracować ze znanymi bibliotekami przygotowywania i trenowania danych bez konieczności opuszczania notesu. Obiekt można utworzyć na podstawie plików `TabularDataset` csv, tsv, [parquet,](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) [jsonl](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none--invalid-lines--error---encoding--utf8--)i z wyników [zapytania SQL](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-).

Za pomocą funkcji TabularDatasets można określić sygnaturę czasową z kolumny w danych lub z miejsca, w którym są przechowywane dane wzorca ścieżki, aby włączyć cechę szeregów czasu. Ta specyfikacja umożliwia łatwe i wydajne filtrowanie według czasu. Aby uzyskać przykład, zobacz [tabelarykową demonstrację](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)interfejsu API szeregów czasu z danymi pogodowymi NOAA .

Utwórz zestaw TabularDataset przy użyciu [zestawu SDK języka Python](#create-a-tabulardataset) [lub Azure Machine Learning studio](how-to-connect-data-ui.md#create-datasets).

>[!NOTE]
> [Zautomatyzowane przepływy](concept-automated-ml.md) pracy uczenia maszynowego generowane za pośrednictwem Azure Machine Learning studio obecnie obsługują tylko zestawy danych TabularDataset. 

## <a name="access-datasets-in-a-virtual-network"></a>Uzyskiwanie dostępu do zestawów danych w sieci wirtualnej

Jeśli obszar roboczy znajduje się w sieci wirtualnej, musisz skonfigurować zestaw danych, aby pominąć walidację. Aby uzyskać więcej informacji na temat używania magazynów danych i zestawów danych w sieci wirtualnej, zobacz Zabezpieczanie obszaru roboczego [i skojarzonych zasobów.](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)

<a name="datasets-sdk"></a>

## <a name="create-datasets-from-datastores"></a>Tworzenie zestawów danych z magazynów danych

Aby dane były dostępne dla Azure Machine Learning, zestawy danych muszą być tworzone na podstawie ścieżek w Azure Machine Learning [danych](how-to-access-data.md) lub internetowych adresów URL. 

> [!TIP] 
> Zestawy danych można tworzyć bezpośrednio z adresów URL magazynu z dostępem do danych opartych na tożsamościach. Aby dowiedzieć się więcej, zobacz Nawiązywanie połączenia z magazynem przy użyciu dostępu [do danych opartych na tożsamościach (wersja zapoznawcza)](how-to-identity-based-data-access.md)<br><br>
Ta funkcja jest eksperymentalną [funkcją w](/python/api/overview/azure/ml/#stable-vs-experimental) wersji zapoznawczej i może ulec zmianie w dowolnym momencie. 

 
Aby utworzyć zestawy danych z magazynu danych przy użyciu zestawu SDK języka Python:

1. Sprawdź, czy masz dostęp do podstawowej usługi magazynu zarejestrowanego magazynu danych `contributor` `owner` Azure Machine Learning magazynu danych. [Sprawdź uprawnienia konta magazynu w Azure Portal](../role-based-access-control/check-access.md).

1. Utwórz zestaw danych, odwołując się do ścieżek w sklepie danych. Zestaw danych można utworzyć z wielu ścieżek w wielu magazynach danych. Nie ma żadnego twardego ograniczenia liczby plików lub rozmiaru danych, na podstawie których można utworzyć zestaw danych. 

> [!NOTE]
> Dla każdej ścieżki danych do usługi magazynu zostanie wysłanych kilka żądań w celu sprawdzenia, czy wskazuje ona plik, czy folder. Ten narzut może prowadzić do obniżonej wydajności lub awarii. Zestaw danych odwołujący się do jednego folderu zawierającego 1000 plików jest traktowany jako odwołujący się do jednej ścieżki danych. Zalecamy utworzenie zestawu danych odwołującego się do mniej niż 100 ścieżek w magazynach danych w celu uzyskania optymalnej wydajności.

### <a name="create-a-filedataset"></a>Tworzenie zestawu danych FileDataset

Użyj metody [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true-) w klasie , aby `FileDatasetFactory` załadować pliki w dowolnym formacie i utworzyć niezarejestrowany zestaw FileDataset. 

Jeśli magazyn znajduje się za siecią wirtualną lub zaporą, ustaw parametr `validate=False` w `from_files()` metodzie . Pozwala to pominąć początkowy krok weryfikacji i zapewnić możliwość utworzenia zestawu danych z tych bezpiecznych plików. Dowiedz się więcej na temat [używania magazynów danych i zestawów danych w sieci wirtualnej.](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
Aby ponownie używać zestawów danych i udostępniać je w ramach eksperymentu w obszarze roboczym, [zarejestruj zestaw danych](#register-datasets). 

> [!TIP] 
> Przekaż pliki z katalogu lokalnego i utwórz zestaw FileDataset w jednej metodzie z metodą w publicznej [wersji zapoznawczej, upload_directory()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-). Ta metoda jest [eksperymentalną funkcją w](/python/api/overview/azure/ml/#stable-vs-experimental) wersji zapoznawczej i może ulec zmianie w dowolnym momencie. 
> 
>  Ta metoda służy do przekazywania danych do magazynu źródłowego, co w efekcie wiąże się z kosztami magazynu. 

### <a name="create-a-tabulardataset"></a>Tworzenie zestawu danych TabularDataset

Użyj metody w klasie , aby odczytać pliki w formacie CSV lub TSV oraz utworzyć [`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) `TabularDatasetFactory` niezarejestrowany zestaw danych TabularDataset. Aby odczytać w plikach z formatu parquet, użyj [`from_parquet_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) metody . Jeśli odczytujesz z wielu plików, wyniki zostaną zagregowane w jedną reprezentację tabelaryjną. 

Zapoznaj się z [dokumentacją referencyjną funkcji TabularDatasetFactory,](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) aby uzyskać informacje o obsługiwanych formatach plików, a także o składni i wzorcach projektowych. 

Jeśli magazyn znajduje się za siecią wirtualną lub zaporą, ustaw parametr `validate=False` w `from_delimited_files()` metodzie . Pozwala to pominąć początkowy krok weryfikacji i zapewnić możliwość utworzenia zestawu danych z tych bezpiecznych plików. Dowiedz się więcej na temat [używania magazynów danych i zestawów danych w sieci wirtualnej.](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)

Poniższy kod pobiera istniejący obszar roboczy i żądany magazyn danych według nazwy. Następnie przekazuje lokalizacje magazynu danych i plików do parametru `path` , aby utworzyć nowy zestaw danych TabularDataset. `weather_ds`

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```
### <a name="set-data-schema"></a>Ustawianie schematu danych

Domyślnie podczas tworzenia zestawu TabularDataset typy danych kolumn są wywnioskowane automatycznie. Jeśli wywnioskowane typy nie są zgodne z oczekiwaniami, możesz zaktualizować schemat zestawu danych, określając typy kolumn za pomocą następującego kodu. Parametr ma `infer_column_type` zastosowanie tylko do zestawów danych utworzonych na podstawie plików rozdzielonych. [Dowiedz się więcej o obsługiwanych typach danych.](/python/api/azureml-core/azureml.data.dataset_factory.datatype)


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(Indeks)|PassengerId|Przeżył|Pclass|Nazwa|Seks|Wiek|SibSp|Parch|Bilet|Taryfy|Kabiny|Rozpoczęła
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Fałsz|3|Braund, Mr. Owen Harris|mężczyzna|22,0|1|0|A/5 21171|7.2500||S
1|2|Prawda|1|Cumings, Mrs. John Bradley (Th...|kobieta|38.0|1|0|KOMPUTER 17599|71.2833|C85|C
2|3|Prawda|3|Heikkinen, Miss. Laina|kobieta|26,0|0|0|SIA/O2. 3101282|7.9250||S

Aby ponownie używać zestawów danych i udostępniać je między eksperymentami w obszarze roboczym, [zarejestruj zestaw danych](#register-datasets).

## <a name="wrangle-data"></a>Wrangle data
Po utworzeniu i [zarejestrowaniu zestawu](#register-datasets) danych możesz załadować go do notesu w celu rozmieszczania i eksploracji danych [przed](#explore-data) trenowaniem modelu. 

Jeśli nie musisz ćwiczać ani eksplorować danych, zobacz, jak korzystać z zestawów danych w skryptach trenowania w celu przesyłania eksperymentów uczenia maszynowego, zobacz Train with datasets (Trenowanie przy użyciu [zestawów danych).](how-to-train-with-datasets.md)

### <a name="filter-datasets-preview"></a>Filtrowanie zestawów danych (wersja zapoznawcza)

Możliwości filtrowania zależą od typu posiadanych zestawów danych. 
> [!IMPORTANT]
> Filtrowanie zestawów danych za pomocą metody w wersji zapoznawczej, [`filter()`](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) jest [eksperymentalną funkcją w](/python/api/overview/azure/ml/#stable-vs-experimental) wersji zapoznawczej i może ulec zmianie w dowolnym momencie. 
> 
**W przypadku metod TabularDatasets** można zachować lub usunąć kolumny przy [użyciu metod keep_columns()](/python/api/azureml-core/azureml.data.tabulardataset#keep-columns-columns--validate-false-) [i drop_columns().](/python/api/azureml-core/azureml.data.tabulardataset#drop-columns-columns-)

Aby odfiltrować wiersze według określonej wartości kolumny w tabularDataset, użyj metody [filter()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) (wersja zapoznawcza). 

Poniższe przykłady zwracają niezarejestrowany zestaw danych na podstawie określonych wyrażeń.

```python
# TabularDataset that only contains records where the age column value is greater than 15
tabular_dataset = tabular_dataset.filter(tabular_dataset['age'] > 15)

# TabularDataset that contains records where the name column value contains 'Bri' and the age column value is greater than 15
tabular_dataset = tabular_dataset.filter((tabular_dataset['name'].contains('Bri')) & (tabular_dataset['age'] > 15))
```

**W zestawach FileDataset każdy** wiersz odpowiada ścieżce pliku, więc filtrowanie według wartości kolumny nie jest pomocne. Można jednak filtrować [wiersze według](/python/api/azureml-core/azureml.data.filedataset#filter-expression-) metadanych, takich jak CreationTime, Size itp.

Poniższe przykłady zwracają niezarejestrowany zestaw danych na podstawie określonych wyrażeń.

```python
# FileDataset that only contains files where Size is less than 100000
file_dataset = file_dataset.filter(file_dataset.file_metadata['Size'] < 100000)

# FileDataset that only contains files that were either created prior to Jan 1, 2020 or where 
file_dataset = file_dataset.filter((file_dataset.file_metadata['CreatedTime'] < datetime(2020,1,1)) | (file_dataset.file_metadata['CanSeek'] == False))
```

**Zestawy danych z etykietami** utworzone na [podstawie projektów etykietowania danych](how-to-create-labeling-projects.md) są szczególnymi przypadekami. Te zestawy danych to typ TabularDataset, który składa się z plików obrazów. W przypadku tego typu zestawów danych można filtrować [obrazy ()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) według metadanych i według wartości kolumn, takich `label` jak i `image_details` .

```python
# Dataset that only contains records where the label column value is dog
labeled_dataset = labeled_dataset.filter(labeled_dataset['label'] == 'dog')

# Dataset that only contains records where the label and isCrowd columns are True and where the file size is larger than 100000
labeled_dataset = labeled_dataset.filter((labeled_dataset['label']['isCrowd'] == True) & (labeled_dataset.file_metadata['Size'] > 100000))
```

### <a name="partition-data-preview"></a>Partycjonowanie danych (wersja zapoznawcza)

Zestaw danych można podzielić na partycje, uwzględniając `partitions_format` parametr podczas tworzenia zestawu danych TabularDataset lub FileDataset. 

> [!IMPORTANT]
> Tworzenie partycji zestawu danych jest [eksperymentalną możliwością](/python/api/overview/azure/ml/#stable-vs-experimental) w wersji zapoznawczej i może ulec zmianie w dowolnym momencie. 

Podczas partycjonowania zestawu danych informacje o partycji każdej ścieżki pliku są wyodrębnione do kolumn na podstawie określonego formatu. Format powinien rozpoczynać się od pozycji pierwszego klucza partycji do końca ścieżki pliku. 

Na przykład, biorąc pod uwagę ścieżkę, w której partycja jest według nazwy działu i czasu; element tworzy kolumnę ciągu "Dział" z wartością "Accounts" i kolumną daty/godziny `../Accounts/2019/01/01/data.jsonl` `partition_format='/{Department}/{PartitionDate:yyyy/MM/dd}/data.jsonl'` "PartitionDate" z wartością `2019-01-01` .

Jeśli dane mają już istniejące partycje i chcesz zachować ten format, uwzględnij parametr w metodzie, aby `partitioned_format` [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true--partition-format-none-) utworzyć zestaw FileDataset. 

Aby utworzyć zestaw TabularDataset, który zachowuje istniejące partycje, uwzględnij parametr w metodzie `partitioned_format` [from_parquet_files()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) lub [from_delimited_files().](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false--empty-as-string-false--encoding--utf8--)

Poniższy przykład:
* Tworzy zestaw FileDataset na bazie plików partycjonowanych.
* Pobiera klucze partycji
* Tworzy nowy, indeksowany zestaw danych FileDataset przy użyciu
 
```Python

file_dataset = Dataset.File.from_files(data_paths, partition_format = '{userid}/*.wav')
ds.register(name='speech_dataset')

# access partition_keys
indexes = file_dataset.partition_keys # ['userid']

# get all partition key value pairs should return [{'userid': 'user1'}, {'userid': 'user2'}]
partitions = file_dataset.get_partition_key_values()


partitions = file_dataset.get_partition_key_values(['userid'])
# return [{'userid': 'user1'}, {'userid': 'user2'}]

# filter API, this will only download data from user1/ folder
new_file_dataset = file_dataset.filter(ds['userid'] == 'user1').download()
```

Możesz również utworzyć nową strukturę partycji dla tabularDatasets za pomocą [metody partitions_by().](/python/api/azureml-core/azureml.data.tabulardataset#partition-by-partition-keys--target--name-none--show-progress-true--partition-as-file-dataset-false-)

```Python

 dataset = Dataset.get_by_name('test') # indexed by country, state, partition_date

# call partition_by locally
new_dataset = ds.partition_by(name="repartitioned_ds", partition_keys=['country'], target=DataPath(datastore, "repartition"))
partition_keys = new_dataset.partition_keys # ['country']
```

>[!IMPORTANT]
> Partycje TabularDataset można również stosować w potokach Azure Machine Learning jako dane wejściowe do metody ParallelRunStep w wielu aplikacjach modeli. Zobacz przykład w dokumentacji [akceleratora wielu modeli.](https://github.com/microsoft/solution-accelerator-many-models/blob/master/01_Data_Preparation.ipynb)

## <a name="explore-data"></a>Eksplorowanie danych

Po zakończeniu rozmieszczania danych możesz zarejestrować [](#register-datasets) zestaw danych, a następnie załadować go do notesu w celu eksploracji danych przed trenowaniem modelu.

W przypadku zestawów FileDataset  można  zainstalować lub pobrać zestaw danych i zastosować biblioteki języka Python, które są zwykle używane do eksploracji danych. [Dowiedz się więcej na temat instalacji i pobierania](how-to-train-with-datasets.md#mount-vs-download).

```python
# download the dataset 
dataset.download(target_path='.', overwrite=False) 

# mount dataset to the temp directory at `mounted_path`

import tempfile
mounted_path = tempfile.mkdtemp()
mount_context = dataset.mount(mounted_path)

mount_context.start()
```

W przypadku zestawu TabularDatasets [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) użyj metody , aby wyświetlić dane w ramce danych. 

```python
# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(Indeks)|PassengerId|Przeżył|Pclass|Nazwa|Seks|Wiek|SibSp|Parch|Bilet|Taryfy|Kabiny|Rozpoczęła
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Fałsz|3|Braund, Mr. Owen Harris|mężczyzna|22,0|1|0|A/5 21171|7.2500||S
1|2|Prawda|1|Cumings, Mrs. John Bradley (Th...|kobieta|38.0|1|0|PC 17599|71.2833|C85|C
2|3|Prawda|3|Heikkinen, Miss. Laina|kobieta|26,0|0|0|SIA/O2. 3101282|7.9250||S

## <a name="create-a-dataset-from-pandas-dataframe"></a>Tworzenie zestawu danych na pomocą ramki danych pandas

Aby utworzyć element TabularDataset z ramki danych pandas w pamięci, zapisz dane w pliku lokalnym, na przykład csv, i utwórz zestaw danych na jego pomocą. Poniższy kod demonstruje ten przepływ pracy.

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)

# upload the local file to a datastore on the cloud

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')

# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(path = [(datastore, ('data/prepared.csv'))])
```

> [!TIP]
> Utwórz i zarejestruj element TabularDataset z ramki danych platformy Spark lub pandas w pamięci przy użyciu jednej metody z metodami publicznej wersji zapoznawczej [`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) i [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) . Te metody rejestrowania są [eksperymentalnymi](/python/api/overview/azure/ml/#stable-vs-experimental) funkcjami w wersji zapoznawczej i mogą ulec zmianie w dowolnym momencie. 
> 
>  Metody te przekażą dane do magazynu źródłowego, co spowoduje naliczenie kosztów magazynowania. 

## <a name="register-datasets"></a>Rejestrowanie zestawów danych

Aby ukończyć proces tworzenia, zarejestruj zestawy danych w obszarze roboczym. Użyj metody , aby zarejestrować zestawy danych w obszarze roboczym w celu udostępnienia ich innym osobom i ponownego użycia w eksperymentach [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) w obszarze roboczym:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Tworzenie zestawów danych przy użyciu Azure Resource Manager

Istnieje wiele szablonów na stronie [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-dataset-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) , za pomocą których można tworzyć zestawy danych.

Aby uzyskać informacje na temat korzystania z tych szablonów, zobacz Tworzenie obszaru [roboczego dla](how-to-create-workspace-template.md)Azure Resource Manager szablonu usługi Azure Machine Learning .


## <a name="create-datasets-from-azure-open-datasets"></a>Tworzenie zestawów danych na Azure Open Datasets

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) to publiczne zestawy danych, których można używać do dodawania funkcji specyficznych dla scenariusza do rozwiązań uczenia maszynowego w celu dokładniejszego użycia modeli. Zestawy danych obejmują dane z domeny publicznej dotyczące pogody, spisu ludności, dni wolnych, bezpieczeństwa publicznego i lokalizacji, które ułatwiają trenowanie modeli uczenia maszynowego i wzbogacanie rozwiązań predykcyjnych. Open Datasets są w chmurze na Microsoft Azure i są zawarte zarówno w zestawie SDK, jak i w studio.

Dowiedz się, jak [tworzyć Azure Machine Learning danych z Azure Open Datasets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md). 

## <a name="train-with-datasets"></a>Szkolenie przy użyciu zestawów danych

Używaj zestawów danych w eksperymentach uczenia maszynowego do trenowania modeli uczenia maszynowego. [Dowiedz się więcej o tym, jak trenować przy użyciu zestawów danych.](how-to-train-with-datasets.md)

## <a name="version-datasets"></a>Zestawy danych wersji

Nowy zestaw danych można zarejestrować pod taką samą nazwą, tworząc nową wersję. Wersja zestawu danych to sposób na zakładkę stanu danych, aby można było zastosować określoną wersję zestawu danych do eksperymentowania lub przyszłego odtwarzania. Dowiedz się więcej o [wersjach zestawu danych.](how-to-version-track-datasets.md)
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

## <a name="next-steps"></a>Następne kroki

* Dowiedz [się, jak trenować przy użyciu zestawów danych.](how-to-train-with-datasets.md)
* Użyj zautomatyzowanego uczenia maszynowego [do trenowania za pomocą zestawu TabularDatasets.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* Aby uzyskać więcej przykładów szkoleniowych dotyczących zestawów danych, zobacz [przykładowe notesy](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
