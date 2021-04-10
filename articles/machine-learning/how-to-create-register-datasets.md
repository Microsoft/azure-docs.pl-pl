---
title: Tworzenie zestawów danych usługi Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć zestawy danych Azure Machine Learning, aby uzyskać dostęp do Twoich operacji w przypadku przebiegów eksperymentów usługi Machine Learning.
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
ms.openlocfilehash: 81779d942b31f940d579de623ecb39c35d3a8b14
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642152"
---
# <a name="create-azure-machine-learning-datasets"></a>Tworzenie zestawów danych usługi Azure Machine Learning

W tym artykule opisano sposób tworzenia Azure Machine Learning zestawów danych w celu uzyskania dostępu do danych dla eksperymentów lokalnych lub zdalnych za pomocą Azure Machine Learning SDK języka Python. Aby dowiedzieć się, gdzie zestawy danych mieszczą się w przepływie pracy ogólnego dostępu do danych Azure Machine Learning, zobacz artykuł dotyczący [bezpiecznego dostępu do danych](concept-data.md#data-workflow) .

Tworząc zestaw danych, tworzysz odwołanie do lokalizacji źródła danych wraz z kopią jego metadanych. Ponieważ dane pozostają w istniejącej lokalizacji, nie ponosisz żadnych dodatkowych kosztów magazynu i nie ryzykują integralności źródeł danych. Ponadto zestawy danych są opóźnieniem oceniane, co ułatwia szybkość działania przepływu pracy. Zestawy danych mogą być tworzone z magazynów danych, publicznych adresów URL i [otwartych zestawów danych platformy Azure](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).

W przypadku środowiska z niską ilością kodu [utwórz Azure Machine Learning zestawy danych za pomocą programu Azure Machine Learning Studio.](how-to-connect-data-ui.md#create-datasets)

Za pomocą Azure Machine Learning zestawów danych można:

* Przechowywanie pojedynczej kopii danych w magazynie, do której odwołują się zestawy danych.

* Bezproblemowo Uzyskuj dostęp do danych podczas uczenia modelowego bez obaw o parametry połączenia lub ścieżki danych. [Dowiedz się więcej na temat uczenia się z zestawami danych](how-to-train-with-datasets.md).

* Udostępnianie danych i współpraca z innymi użytkownikami.

## <a name="prerequisites"></a>Wymagania wstępne

Aby tworzyć zestawy danych i korzystać z nich, potrzebne są:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

* [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md).

* [Zestaw Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/install), który obejmuje pakiet usługi Azure DataSets.

    * Utwórz [wystąpienie obliczeniowe Azure Machine Learning](how-to-create-manage-compute-instance.md), które jest w pełni skonfigurowane i zarządzane środowisko programistyczne, które zawiera zintegrowane notesy oraz już zainstalowany zestaw SDK.

    **OR**

    * Pracuj nad własnym notesem Jupyter i samodzielnie Instaluj zestaw SDK, korzystając z [tych instrukcji](/python/api/overview/azure/ml/install).

> [!NOTE]
> Niektóre klasy zestawu danych mają zależności w pakiecie [Azure preprodukcyjnym](https://pypi.org/project/azureml-dataprep/) , który jest zgodny z 64-bitowym językiem Python. W przypadku użytkowników systemu Linux te klasy są obsługiwane tylko w następujących dystrybucjach: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) i CentOS (7). Jeśli używasz nieobsługiwanego dystrybucje, Skorzystaj z [tego przewodnika](/dotnet/core/install/linux) , aby zainstalować program .net Core 2,1, aby kontynuować. 

## <a name="compute-size-guidance"></a>Wskazówki dotyczące rozmiaru obliczeń

Podczas tworzenia zestawu danych Przejrzyj moc obliczeniową obliczeniową i rozmiar danych w pamięci. Rozmiar danych w magazynie nie jest taki sam jak rozmiar danych w ramce Dataframe. Na przykład dane w plikach CSV można rozszerzyć o do 10X w ramce Dataframe, dzięki czemu plik CSV o pojemności 1 GB może być 10 GB w ramce Dataframe. 

Jeśli dane są skompresowane, można zwiększyć ich rozmiar. 20 GB stosunkowo rozrzedzonych danych przechowywanych w skompresowanym formacie Parquet można zwiększyć do ~ 800 GB w pamięci. Ponieważ pliki Parquet przechowują dane w formacie kolumnowym, w przypadku, gdy potrzebna jest tylko połowa kolumn, wystarczy załadować ~ 400 GB w pamięci.

[Dowiedz się więcej na temat optymalizowania przetwarzania danych w Azure Machine Learning](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Typy zestawów danych

Istnieją dwa typy zestawów danych, w zależności od tego, jak użytkownicy zużywają je w szkoleniu. FileDatasets i TabularDatasets. Oba typy mogą być używane w Azure Machine Learning szkoleń dotyczących przepływów pracy obejmujących, szacowania, AutoML, moje napędy i potoki. 

### <a name="filedataset"></a>FileDataset

[FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset) odwołuje się do jednego lub wielu plików w magazynach danych lub publicznych adresach URL. Jeśli dane zostały już oczyszczone i gotowe do użycia w eksperymentach szkoleniowych, możesz [pobrać lub zainstalować](how-to-train-with-datasets.md#mount-vs-download) pliki do obliczeń jako obiekt FileDataset. 

Zalecamy FileDatasets dla przepływów pracy usługi Machine Learning, ponieważ pliki źródłowe mogą być w dowolnym formacie, co pozwala na szersze scenariusze uczenia maszynowego, w tym uczenie głębokie.

Utwórz FileDataset za pomocą [zestawu SDK języka Python](#create-a-filedataset) lub [Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets) .
### <a name="tabulardataset"></a>TabularDataset

[TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) reprezentuje dane w formacie tabelarycznym przez analizowanie dostarczonego pliku lub listy plików. Zapewnia to możliwość zmaterializowania danych w Pandas lub Spark Dataframe, dzięki czemu możesz współpracować ze znanymi bibliotekami przygotowywania i uczenia danych bez konieczności opuszczania Twojego notesu. Można utworzyć `TabularDataset` obiekt z plików CSV,. tsv,. Parquet,. JSON i z [wyników zapytania SQL](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-).

Za pomocą TabularDatasets można określić sygnaturę czasową z kolumny w danych lub z dowolnego miejsca, w którym są przechowywane dane wzorca ścieżki, aby włączyć cechę szeregów czasowych. Ta specyfikacja umożliwia łatwe i wydajne filtrowanie według czasu. Aby zapoznać się z przykładem, zobacz [Tabelaryczny pokaz interfejsu API związany z szeregiem czasowym z danymi pogody NOAA](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

Utwórz TabularDataset za pomocą [zestawu SDK języka Python](#create-a-tabulardataset) lub [Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets).

>[!NOTE]
> [Automatyczne](concept-automated-ml.md) przepływy pracy z użyciem ml wygenerowane za pośrednictwem Azure Machine Learning Studio obsługują obecnie tylko TabularDatasets. 

## <a name="access-datasets-in-a-virtual-network"></a>Dostęp do zestawów danych w sieci wirtualnej

Jeśli obszar roboczy znajduje się w sieci wirtualnej, musisz skonfigurować zestaw danych, aby pominąć walidację. Aby uzyskać więcej informacji na temat sposobu korzystania z magazynów danych i ich zestawów w sieci wirtualnej, zobacz temat [Zabezpieczanie obszaru roboczego i skojarzonych zasobów](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

<a name="datasets-sdk"></a>

## <a name="create-datasets-from-datastores"></a>Tworzenie zestawów danych z magazynów danych

Aby dane były dostępne dla Azure Machine Learning, zestawy danych muszą zostać utworzone na podstawie ścieżek w [Azure Machine Learning magazynach](how-to-access-data.md) danych lub adresach URL sieci Web. 

> [!TIP] 
> Zestawy danych można tworzyć bezpośrednio z adresów URL magazynu, w których jest oparty na tożsamościach. Dowiedz się więcej na temat [łączenia z magazynem z dostępem do danych opartych na tożsamości (wersja zapoznawcza)](how-to-identity-based-data-access.md)<br><br>
Ta funkcja jest [eksperymentalną](/python/api/overview/azure/ml/#stable-vs-experimental) funkcją w wersji zapoznawczej i może ulec zmianie w dowolnym momencie. 

 
Aby utworzyć zestawy danych z magazynu danych za pomocą zestawu SDK języka Python:

1. Sprawdź, czy masz `contributor` lub `owner` dostęp do źródłowej usługi magazynu dla zarejestrowanego Azure Machine Learning magazynu danych. [Sprawdź uprawnienia konta magazynu w Azure Portal](../role-based-access-control/check-access.md).

1. Utwórz zestaw danych, odwołując się do ścieżek w magazynie danych. Można utworzyć zestaw danych z wielu ścieżek w wielu magazynach. Nie ma żadnego sztywnego limitu liczby plików lub rozmiaru danych, z których można utworzyć zestaw danych. 

> [!NOTE]
> Dla każdej ścieżki danych do usługi magazynu wysyłane są kilka żądań, aby sprawdzić, czy wskazuje ona plik lub folder. To obciążenie może prowadzić do obniżenia wydajności lub niepowodzenia. Zestaw danych odwołujący się do jednego folderu z plikami 1000 wewnątrz jest traktowany jako odwołujący się do jednej ścieżki danych. Zalecamy utworzenie zestawu danych, do którego odwołuje się mniej niż 100 ścieżek w magazynach datastores w celu uzyskania optymalnej wydajności.

### <a name="create-a-filedataset"></a>Utwórz FileDataset

Użyj [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true-) metody `FileDatasetFactory` klasy, aby załadować pliki w dowolnym formacie i utworzyć niezarejestrowane FileDataset. 

Jeśli magazyn znajduje się za siecią wirtualną lub zaporą, należy ustawić parametr `validate=False` w `from_files()` metodzie. Pomija początkowy krok walidacji i gwarantuje, że można utworzyć zestaw danych z tych bezpiecznych plików. Dowiedz się więcej o sposobach [używania magazynów danych i zestawów w sieci wirtualnej](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
Aby ponownie wykorzystać i udostępnić zestawy danych w ramach eksperymentu w obszarze roboczym, [zarejestruj zestaw danych](#register-datasets). 

> [!TIP] 
> Przekaż pliki z katalogu lokalnego i Utwórz FileDataset w jednej metodzie przy użyciu metody publicznej wersji zapoznawczej, [upload_directory ()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-). Ta metoda jest [eksperymentalną](/python/api/overview/azure/ml/#stable-vs-experimental) funkcją w wersji zapoznawczej i może ulec zmianie w dowolnym momencie. 
> 
>  Ta metoda służy do przekazywania danych do magazynu bazowego, a w związku z tym koszty związane z magazynowaniem. 

### <a name="create-a-tabulardataset"></a>Utwórz TabularDataset

Użyj [`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) metody `TabularDatasetFactory` klasy, aby odczytać pliki w formacie CSV lub TSV oraz utworzyć niezarejestrowane TabularDataset. W przypadku odczytywania z wielu plików wyniki zostaną zagregowane w jednej reprezentacji tabelarycznej. 

Jeśli magazyn znajduje się za siecią wirtualną lub zaporą, należy ustawić parametr `validate=False` w `from_delimited_files()` metodzie. Pomija początkowy krok walidacji i gwarantuje, że można utworzyć zestaw danych z tych bezpiecznych plików. Dowiedz się więcej o sposobach używania [magazynów danych i zestawów w sieci wirtualnej](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

Poniższy kod pobiera istniejący obszar roboczy i żądany magazyn danych według nazwy. Następnie przekazuje magazyn danych i lokalizacje plików do `path` parametru, aby utworzyć nowy TabularDataset `weather_ds` .

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
### <a name="set-data-schema"></a>Ustaw schemat danych

Domyślnie podczas tworzenia TabularDataset typy danych kolumny są wywnioskowane automatycznie. Jeśli wywnioskowane typy nie pasują do oczekiwań, można zaktualizować schemat zestawu danych, określając typy kolumn o następującym kodzie. Ten parametr `infer_column_type` ma zastosowanie tylko w przypadku zestawów danych utworzonych na podstawie rozdzielanych plików. [Dowiedz się więcej na temat obsługiwanych typów danych](/python/api/azureml-core/azureml.data.dataset_factory.datatype).


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|Indeks|PassengerId|Ocalałe|Pclass|Nazwa|Biciu|Wiek|SibSp|Parch|Bilet|Bezprzewodow|Kabin|Zaokrętowanie
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Fałsz|3|Braund, Mr. Owen Harris|mężczyzna|22,0|1|0|A/5 21171|7,2500||S
1|2|Prawda|1|Cumings, Pani. Jan Bradley (Florencji Briggs th...|kobieta|38,0|1|0|KOMPUTER 17599|71,2833|C85|C
2|3|Prawda|3|Heikkinen, chybień. Laina|kobieta|26,0|0|0|STON/O2. 3101282|7,9250||S

Aby ponownie użyć zestawów danych i udostępnić je w ramach eksperymentów w obszarze roboczym, [zarejestruj ten element dataset](#register-datasets).

## <a name="wrangle-data"></a>Wrangle dane
Po utworzeniu i [zarejestrowaniu](#register-datasets) zestawu danych możesz załadować go do notesu w celu przetwarzanie danych i [eksploracji](#explore-data) przed modelem szkoleń. 

Jeśli nie musisz wykonywać żadnych przetwarzanie związanych z danymi lub eksploracji, zobacz jak korzystać z zestawów danych w skryptach szkoleniowych na potrzeby przesyłania eksperymentów ML w [połączeniu z zestawami danych](how-to-train-with-datasets.md).

### <a name="filter-datasets-preview"></a>Filtruj zestawy danych (wersja zapoznawcza)
Możliwości filtrowania są zależne od typu posiadanego zestawu danych. 
> [!IMPORTANT]
> Filtrowanie zestawów danych za pomocą metody publicznej wersji zapoznawczej [`filter()`](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) to funkcja [eksperymentalnej](/python/api/overview/azure/ml/#stable-vs-experimental) wersji zapoznawczej i może ulec zmianie w dowolnym momencie. 
> 
**W przypadku TabularDatasets** można zachować lub usunąć kolumny z metodami [keep_columns ()](/python/api/azureml-core/azureml.data.tabulardataset#keep-columns-columns--validate-false-) i [drop_columns ()](/python/api/azureml-core/azureml.data.tabulardataset#drop-columns-columns-) .

Aby odfiltrować wiersze według określonej wartości kolumny w TabularDataset, użyj metody [Filter ()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) (wersja zapoznawcza). 

Poniższe przykłady zwracają niezarejestrowanego zestawu danych na podstawie określonych wyrażeń.

```python
# TabularDataset that only contains records where the age column value is greater than 15
tabular_dataset = tabular_dataset.filter(tabular_dataset['age'] > 15)

# TabularDataset that contains records where the name column value contains 'Bri' and the age column value is greater than 15
tabular_dataset = tabular_dataset.filter((tabular_dataset['name'].contains('Bri')) & (tabular_dataset['age'] > 15))
```

**W FileDatasets** każdy wiersz odpowiada ścieżce pliku, więc filtrowanie według wartości kolumny nie jest pomocne. Można jednak [filtrować ()](/python/api/azureml-core/azureml.data.filedataset#filter-expression-) wiersze na podstawie metadanych, takich jak, CreationTime, size itd.

Poniższe przykłady zwracają niezarejestrowanego zestawu danych na podstawie określonych wyrażeń.

```python
# FileDataset that only contains files where Size is less than 100000
file_dataset = file_dataset.filter(file_dataset.file_metadata['Size'] < 100000)

# FileDataset that only contains files that were either created prior to Jan 1, 2020 or where 
file_dataset = file_dataset.filter((file_dataset.file_metadata['CreatedTime'] < datetime(2020,1,1)) | (file_dataset.file_metadata['CanSeek'] == False))
```

**Zestawy danych z etykietami** utworzone na podstawie [projektów etykietowania z danymi](how-to-create-labeling-projects.md) są szczególnym przypadkiem. Te zestawy danych są typu TabularDataset składającego się z plików obrazów. Dla tych typów zestawów danych można [filtrować ()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) obrazy według metadanych oraz wartości kolumn takich jak `label` i `image_details` .

```python
# Dataset that only contains records where the label column value is dog
labeled_dataset = labeled_dataset.filter(labeled_dataset['label'] == 'dog')

# Dataset that only contains records where the label and isCrowd columns are True and where the file size is larger than 100000
labeled_dataset = labeled_dataset.filter((labeled_dataset['label']['isCrowd'] == True) & (labeled_dataset.file_metadata['Size'] > 100000))
```

## <a name="explore-data"></a>Eksplorowanie danych

Po zakończeniu przetwarzanie danych możesz [zarejestrować](#register-datasets) zestaw danych, a następnie załadować go do notesu w celu przeprowadzenia eksploracji danych przed modelem.

W przypadku FileDatasets można **zainstalować** lub **pobrać** zestaw danych oraz zastosować biblioteki języka Python, które zwykle są używane do eksploracji danych. [Dowiedz się więcej o instalacji i pobieraniu](how-to-train-with-datasets.md#mount-vs-download).

```python
# download the dataset 
dataset.download(target_path='.', overwrite=False) 

# mount dataset to the temp directory at `mounted_path`

import tempfile
mounted_path = tempfile.mkdtemp()
mount_context = dataset.mount(mounted_path)

mount_context.start()
```

W przypadku TabularDatasets Użyj [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) metody, aby wyświetlić dane w ramce Dataframe. 

```python
# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|Indeks|PassengerId|Ocalałe|Pclass|Nazwa|Biciu|Wiek|SibSp|Parch|Bilet|Bezprzewodow|Kabin|Zaokrętowanie
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Fałsz|3|Braund, Mr. Owen Harris|mężczyzna|22,0|1|0|A/5 21171|7,2500||S
1|2|Prawda|1|Cumings, Pani. Jan Bradley (Florencji Briggs th...|kobieta|38,0|1|0|KOMPUTER 17599|71,2833|C85|C
2|3|Prawda|3|Heikkinen, chybień. Laina|kobieta|26,0|0|0|STON/O2. 3101282|7,9250||S

## <a name="create-a-dataset-from-pandas-dataframe"></a>Tworzenie zestawu danych z Pandas Dataframe

Aby utworzyć TabularDataset z ramki datapandas Dataframe, Zapisz dane w lokalnym pliku, takim jak wolumin CSV, i Utwórz zestaw danych z tego pliku. Poniższy kod demonstruje ten przepływ pracy.

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
> Utwórz i zarejestruj TabularDataset z tabeli w pamięci Spark lub Pandas danych za pomocą jednej metody z publicznymi metodami w wersji zapoznawczej [`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) i [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) . Te metody rejestracji to [eksperymentalne](/python/api/overview/azure/ml/#stable-vs-experimental) funkcje w wersji zapoznawczej i mogą ulec zmianie w dowolnym momencie. 
> 
>  Metody te umożliwiają przekazywanie danych do magazynu bazowego, a w związku z tym koszty magazynowania. 

## <a name="register-datasets"></a>Rejestrowanie zestawów danych

Aby ukończyć proces tworzenia, zarejestruj zestawy danych w obszarze roboczym. Użyj [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) metody, aby zarejestrować zestawy danych w obszarze roboczym, aby udostępnić je innym osobom i ponownie wykorzystać je w ramach eksperymentów w obszarze roboczym:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Tworzenie zestawów danych przy użyciu Azure Resource Manager

Istnieje wiele szablonów [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-dataset-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) , których można użyć do tworzenia zestawów danych.

Aby uzyskać informacje na temat korzystania z tych szablonów, zobacz [Tworzenie obszaru roboczego dla Azure Machine Learning za pomocą szablonu Azure Resource Manager](how-to-create-workspace-template.md).


## <a name="create-datasets-from-azure-open-datasets"></a>Tworzenie zestawów danych na podstawie otwartych zestawów danych platformy Azure

[Otwarte zestawy danych platformy Azure](https://azure.microsoft.com/services/open-datasets/) mają nadzorowane zestawy danych, których można użyć do dodawania funkcji specyficznych dla scenariusza do rozwiązań uczenia maszynowego w celu uzyskania dokładniejszych modeli. Zestawy danych obejmują dane z domeny publicznej na potrzeby pogodowych, spisu, świąt, bezpieczeństwa publicznego i lokalizacji, które ułatwiają uczenie modeli uczenia maszynowego i wzbogacanie rozwiązań predykcyjnych. Otwarte zestawy danych znajdują się w chmurze w Microsoft Azure i znajdują się w zestawie SDK i Studio.

Dowiedz się, jak tworzyć [zestawy danych Azure Machine Learning z poziomu otwartych zestawów danych platformy Azure](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md). 

## <a name="train-with-datasets"></a>Szkolenie przy użyciu zestawów danych

Używaj Twoich zestawów danych w eksperymentach uczenia maszynowego do uczenia modeli ML. [Dowiedz się więcej na temat uczenia się z zestawami danych](how-to-train-with-datasets.md).

## <a name="version-datasets"></a>Zestawy danych wersji

Nowy zestaw danych można zarejestrować pod tą samą nazwą, tworząc nową wersję. Wersja zestawu danych to sposób tworzenia zakładek stanu danych, dzięki czemu można zastosować określoną wersję zestawu danych na potrzeby eksperymentowania lub kopiowania w przyszłości. Dowiedz się więcej o [wersjach zestawu danych](how-to-version-track-datasets.md).
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

* Dowiedz się [, jak uczenie się z zestawami danych](how-to-train-with-datasets.md).
* Używaj automatycznej uczenia maszynowego do [uczenia się z TabularDatasets](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).
* Aby uzyskać więcej przykładów szkoleniowych dotyczących zestawu danych, zobacz [przykładowe notesy](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
