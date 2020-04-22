---
title: Łączenie się z usługami magazynu platformy Azure
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak bezpiecznie łączyć się z usługami magazynu platformy Azure podczas szkolenia za pomocą usługi Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 4a2102f442fc176762b7d5d69f7b367a94633ef5
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758795"
---
# <a name="connect-to-azure-storage-services"></a>Łączenie się z usługami magazynu platformy Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak połączyć się z usługami magazynu platformy Azure za pośrednictwem magazynów danych usługi Azure Machine Learning. Magazyny danych przechowują informacje o połączeniach, takie jak identyfikator subskrypcji i autoryzacja tokenów w [magazynie kluczy](https://azure.microsoft.com/services/key-vault/) skojarzone z obszarem roboczym, dzięki czemu można bezpiecznie uzyskać dostęp do magazynu bez konieczności kodowania ich na stałe w skryptach.

Można tworzyć magazyny danych z [tych rozwiązań magazynu platformy Azure](#matrix). W przypadku nieobsługiwałych rozwiązań magazynu i aby zaoszczędzić koszt transferu danych wychodzących podczas eksperymentów uczenia maszynowego, zaleca się [przeniesienie danych](#move) do obsługiwanych rozwiązań magazynu platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

Będą potrzebne:
- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree).

- Konto magazynu platformy Azure z [kontenerem obiektów blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) lub [udziałem plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- Zestaw [SDK usługi Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)lub dostęp do studia usługi Azure Machine [Learning](https://ml.azure.com/).

- Obszar roboczy usługi Azure Machine Learning.
  
  Utwórz [obszar roboczy usługi Azure Machine Learning](how-to-manage-workspace.md) lub użyj istniejącego za pośrednictwem zestawu SDK języka Python. Zaimportuj `Workspace` i `Datastore` klasy i załadować informacje subskrypcji z pliku `config.json` przy użyciu funkcji `from_config()`. Spowoduje to domyślne wyszukanie pliku JSON w bieżącym katalogu, ale można również `from_config(path="your/file/path")`określić parametr ścieżki wskazujący plik za pomocą programu .

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Obsługiwane typy usług przechowywania danych

Magazyny danych obsługują obecnie przechowywanie informacji o połączeniu z usługami magazynu wymienionymi w poniższej macierzy.

| Typ&nbsp;magazynu | Typ&nbsp;uwierzytelniania | [Studio&nbsp;&nbsp;usługi Azure Machine Learning](https://ml.azure.com/) | [Zestaw&nbsp;&nbsp;SDK języka Python usługi Azure Machine Learning&nbsp;](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Narzędzie&nbsp;&nbsp;cli usługi Azure Machine Learning](reference-azure-machine-learning-cli.md) | [Interfejs&nbsp;&nbsp;API&nbsp; odpoczynku usługi Azure Machine Learning](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Magazyn&nbsp;obiektów&nbsp;blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Klucz konta <br> Token SAS | ✓ | ✓ | ✓ |✓
[Udział&nbsp;&nbsp;plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Klucz konta <br> Token SAS | ✓ | ✓ | ✓ |✓
[Usługa&nbsp;Azure&nbsp;Data&nbsp;Lake Storage Gen 1](https://docs.microsoft.com/azure/data-lake-store/)| Jednostka usługi| ✓ | ✓ | ✓ |✓
[Usługa&nbsp;Azure&nbsp;Data&nbsp;Lake Storage Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Jednostka usługi| ✓ | ✓ | ✓ |✓
[Baza&nbsp;&nbsp;danych SQL platformy Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| Uwierzytelnianie SQL <br>Jednostka usługi| ✓ | ✓ | ✓ |✓
[Usługa&nbsp;Azure PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | Uwierzytelnianie SQL| ✓ | ✓ | ✓ |✓
[Usługa&nbsp;&nbsp;Azure&nbsp;Database dla mysql](https://docs.microsoft.com/azure/mysql/overview) | Uwierzytelnianie SQL|  | ✓* | ✓* |✓*
[System plików&nbsp;&nbsp;Databricks](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Brak uwierzytelniania | | ✓** | ✓ ** |✓** 

*MySQL jest obsługiwany tylko dla potoku [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). <br>
**Databricks jest obsługiwany tylko dla rurociągu [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>Wskazówki dotyczące magazynu

Zaleca się utworzenie magazynu danych dla [kontenera obiektów blob platformy Azure.](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) Zarówno magazyn standardowy, jak i magazyn klasy premium są dostępne dla obiektów blob. Chociaż magazyn w warstwie Premium jest droższy, jego szybsze prędkości przepływności może poprawić szybkość przebiegów szkoleniowych, szczególnie jeśli trenujesz z dużym zestawem danych. Aby uzyskać informacje o kosztach kont magazynu, zobacz [kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[Usługa Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) jest oparta na magazynie obiektów Blob platformy Azure i zaprojektowana z myślą o analizie dużych zbiorów danych w przedsiębiorstwie. Podstawową częścią usługi Data Lake Storage Gen2 jest dodanie [hierarchicznego obszaru nazw](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) do magazynu obiektów Blob. Hierarchiczna przestrzeń nazw organizuje obiekty/pliki w hierarchię katalogów w celu uzyskania efektywnego dostępu do danych.

Podczas tworzenia obszaru roboczego kontener obiektów blob platformy Azure i udział plików platformy Azure są automatycznie rejestrowane w obszarze roboczym. Są one `workspaceblobstore` nazwane i `workspacefilestore`, odpowiednio. `workspaceblobstore`służy do przechowywania artefaktów obszaru roboczego i dzienników eksperymentów uczenia maszynowego. `workspacefilestore`służy do przechowywania notesów i skryptów języka R autoryzowanych za pośrednictwem [instancji obliczeniowej](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files). Kontener `workspaceblobstore` jest ustawiony jako domyślny magazyn danych.

> [!IMPORTANT]
> Projektant usługi Azure Machine Learning (wersja zapoznawcza) utworzy magazyn danych o nazwie **azureml_globaldatasets** automatycznie po otwarciu próbki na stronie głównej projektanta. Ten magazyn danych zawiera tylko przykładowe zestawy danych. Prosimy **nie** używać tego magazynu danych do jakiegokolwiek poufnego dostępu do danych.
> ![Automatycznie utworzony magazyn danych dla przykładowych zestawów danych projektanta](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Tworzenie i rejestrowanie magazynów danych

Podczas rejestrowania rozwiązania magazynu platformy Azure jako magazynu danych, automatycznie utworzyć i zarejestrować tego magazynu danych do określonego obszaru roboczego. Można tworzyć i rejestrować magazyny danych w obszarze roboczym przy użyciu zestawu SDK języka Python lub studia azure machine learning.

>[!IMPORTANT]
> W ramach początkowego procesu tworzenia i rejestrowania magazynu danych usługa Azure Machine Learning sprawdza, czy podstawowa usługa magazynu istnieje i że podmiot dostarczony przez użytkownika (nazwa użytkownika, jednostki usługi lub token sygnatury dostępu współdzielonego) ma dostęp do tego magazynu. W przypadku magazynów danych usługi Azure Data Lake Storage gen 1 i 2 jednak to sprawdzanie poprawności odbywa się później, gdy metody dostępu do danych, takie jak [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) lub [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) są wywoływane. 
<br><br>
Po utworzeniu magazynu danych ta weryfikacja jest wykonywana tylko dla metod, które wymagają dostępu do kontenera magazynu źródłowego, **a nie** za każdym razem, gdy są pobierane obiekty magazynu danych. Na przykład sprawdzanie poprawności odbywa się, jeśli chcesz pobrać pliki z magazynu danych; ale jeśli chcesz tylko zmienić domyślny magazyn danych, sprawdzanie poprawności nie nastąpi.

### <a name="python-sdk"></a>Zestaw SDK dla języka Python

Wszystkie metody rejestru znajdują [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) się w `register_azure_*`klasie i mają formularz .

Informacje potrzebne do wypełnienia `register()` metody można znaleźć w [witrynie Azure portal](https://portal.azure.com).
Wybierz **pozycję Konta magazynu** w lewym okienku i wybierz konto magazynu, które chcesz zarejestrować. Strona **Przegląd** zawiera informacje, takie jak nazwa konta, kontener i nazwa udziału plików. 

* W przypadku elementów uwierzytelniania, takich jak klucz konta lub token sygnatury dostępu współdzielonego, przejdź do **klawiszy programu Access** w okienku **Ustawienia.** 

* W przypadku elementów jednostkowych usług, takich jak identyfikator dzierżawy i identyfikator klienta, przejdź do **rejestracji aplikacji** i wybierz aplikację, której chcesz użyć. Jego **odpowiednia** strona Przegląd będzie zawierać te elementy.

> [!IMPORTANT]
> Jeśli twoje konto magazynu znajduje się w sieci wirtualnej, obsługiwane jest tylko tworzenie magazynów danych **za pośrednictwem zestawu SDK.**

Poniższe przykłady pokazują, jak zarejestrować kontener obiektów blob platformy Azure, udział plików platformy Azure i usługę Azure Data Lake Storage Generation 2 jako magazyn danych. W przypadku innych usług magazynowania należy zapoznać się z [dokumentacją referencyjną `register_azure_*` odpowiednich metod.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods)

#### <a name="blob-container"></a>Kontener obiektów blob

Aby zarejestrować kontener obiektów blob platformy [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)Azure jako magazyn danych, należy użyć programu .

Poniższy kod tworzy i `blob_datastore_name` rejestruje magazyn `ws` danych do obszaru roboczego. Ten magazyn danych `my-container-name` uzyskuje dostęp do `my-account-name` kontenera obiektu blob na koncie magazynu przy użyciu klucza dostępu do konta dostarczonego.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```
Jeśli kontener obiektu blob znajduje `skip_validation=True` się [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)w sieci wirtualnej, ustaw użycie programu .

#### <a name="file-share"></a>Udział plików

Aby zarejestrować udział plików platformy Azure [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)jako magazyn danych, użyj programu . 

Poniższy kod tworzy i `file_datastore_name` rejestruje magazyn `ws` danych do obszaru roboczego. Ten magazyn danych `my-fileshare-name` uzyskuje dostęp `my-account-name` do udziału plików na koncie magazynu przy użyciu klucza dostępu do podanego konta.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```
Jeśli udział plików znajduje się `skip_validation=True` w [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)sieci wirtualnej, ustaw użycie programu . 

#### <a name="azure-data-lake-storage-generation-2"></a>Generowanie 2 usługi Azure Data Lake

W przypadku magazynu danych usługi Azure Data Lake Generacji 2 (ADLS Gen 2) użyj [register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) do zarejestrowania magazynu danych poświadczeń połączonego z magazynem usługi Azure DataLake Gen 2 z [uprawnieniami jednostki usługi.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) Aby korzystać z jednostki usługi, należy [zarejestrować aplikację](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) i udzielić podmiotowi usługi odpowiedniego dostępu do danych. Dowiedz się więcej o [konfiguracji kontroli dostępu dla ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Poniższy kod tworzy i `adlsgen2_datastore_name` rejestruje magazyn `ws` danych do obszaru roboczego. Ten magazyn danych uzyskuje `test` dostęp `account_name` do systemu plików na koncie magazynu przy użyciu poświadczeń jednostki usługi dostarczone.

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```

### <a name="azure-machine-learning-studio"></a>Studio uczenia maszynowego Azure 

Utwórz nowy magazyn danych w kilku krokach w usłudze Azure Machine Learning studio:

> [!IMPORTANT]
> Jeśli twoje konto magazynu znajduje się w sieci wirtualnej, obsługiwane jest tylko tworzenie magazynów danych [za pośrednictwem zestawu SDK.](#python-sdk) 

1. Zaloguj się do [studia usługi Azure Machine Learning](https://ml.azure.com/).
1. Wybierz **pozycję Magazyny danych** w lewym okienku w obszarze **Zarządzaj**.
1. Wybierz **+ Nowy magazyn danych**.
1. Wypełnij formularz dla nowego magazynu danych. Formularz inteligentnie aktualizuje się na podstawie wybranych dla typu magazynu platformy Azure i typu uwierzytelniania.
  
Informacje potrzebne do wypełnienia formularza można znaleźć w [witrynie Azure portal](https://portal.azure.com). Wybierz **pozycję Konta magazynu** w lewym okienku i wybierz konto magazynu, które chcesz zarejestrować. Strona **Przegląd** zawiera informacje, takie jak nazwa konta, kontener i nazwa udziału plików. 

* W przypadku elementów uwierzytelniania, takich jak klucz konta lub token sygnatury dostępu współdzielonego, przejdź do **klawiszy programu Access** w okienku **Ustawienia.** 

* W przypadku elementów jednostkowych usług, takich jak identyfikator dzierżawy i identyfikator klienta, przejdź do **rejestracji aplikacji** i wybierz aplikację, której chcesz użyć. Jego **odpowiednia** strona Przegląd będzie zawierać te elementy. 

W poniższym przykładzie pokazano, jak wygląda formularz podczas tworzenia magazynu danych obiektów blob platformy Azure: 
    
![Formularz dla nowego magazynu danych](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Pobierz magazyny danych z obszaru roboczego

Aby uzyskać określony magazyn danych zarejestrowany w bieżącym [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) obszarze roboczym, użyj metody statycznej w `Datastore` klasie:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Aby uzyskać listę magazynów danych zarejestrowanych w danym obszarze [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) roboczym, można użyć właściwości na obiekcie obszaru roboczego:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Aby uzyskać domyślny magazyn danych obszaru roboczego, użyj tego wiersza:

```Python
datastore = ws.get_default_datastore()
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Przesyłanie i pobieranie danych

Metody [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) i opisane w poniższych przykładach są specyficzne dla i działają identycznie dla [azureblobdatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) i [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) klasy.

### <a name="upload"></a>Upload

Przekaż katalog lub pojedyncze pliki do magazynu danych przy użyciu zestawu SDK języka Python:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

Parametr `target_path` określa lokalizację w udziale plików (lub kontenera obiektów blob) do przekazania. Domyślnie `None`jest to , więc dane są przekazywane do katalogu głównego. Jeśli `overwrite=True`wszystkie istniejące `target_path` dane w jest zastępowany.

Można również przesłać listę poszczególnych plików do `upload_files()` magazynu danych za pomocą metody.

### <a name="download"></a>Pobierz

Pobieranie danych z magazynu danych do lokalnego systemu plików:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Parametr `target_path` jest lokalizacją katalogu lokalnego, do aby pobrać dane. Aby określić ścieżkę do folderu w udziale plików (lub kontenera obiektów blob) do pobrania, podaj tę ścieżkę do `prefix`programu . Jeśli `prefix` `None`tak, zostanie pobrana cała zawartość udziału plików (lub kontenera obiektów blob).

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Dostęp do danych podczas szkolenia

Aby wchodzić w interakcje z danymi w magazynach danych lub pakować dane do obiektu zużywalnej do zadań uczenia maszynowego, takich jak szkolenia, [utwórz zestaw danych usługi Azure Machine Learning.](how-to-create-register-datasets.md) Zestawy danych zapewniają funkcje, które ładują dane tabelaryczne do pandas lub Spark DataFrame. Zestawy danych umożliwiają również pobieranie lub instalowanie plików dowolnego formatu z magazynu obiektów Blob platformy Azure, usługi Azure Files, usługi Azure Data Lake Storage Gen1, usługi Azure Data Lake Storage Gen2, bazy danych SQL Azure i bazy danych azure dla postgreSQL. [Dowiedz się więcej o tym, jak trenować z zestawami danych](how-to-train-with-datasets.md).

### <a name="accessing-source-code-during-training"></a>Uzyskiwanie dostępu do kodu źródłowego podczas szkolenia

Usługa Azure Blob storage ma wyższe szybkości przepływności niż udział plików platformy Azure i będzie skalowany do dużej liczby zadań uruchomionych równolegle. Z tego powodu zaleca się skonfigurowanie przebiegów do używania magazynu obiektów Blob do przesyłania plików kodu źródłowego.

Poniższy przykład kodu określa w konfiguracji uruchamiania, który magazyn danych obiektów blob do użycia do transferu kodu źródłowego.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Dostęp do danych podczas oceniania

Usługa Azure Machine Learning udostępnia kilka sposobów korzystania z modeli do oceniania. Niektóre z tych metod nie zapewniają dostępu do magazynów danych. Poniższa tabela służy do zrozumienia, które metody umożliwiają dostęp do magazynów danych podczas oceniania:

| Metoda | Dostęp do magazynu danych | Opis |
| ----- | :-----: | ----- |
| [Przewidywanie wsadowe](how-to-use-parallel-run-step.md) | ✔ | Asynchronicznie twórz prognozy dotyczące dużych ilości danych. |
| [Usługa sieci Web](how-to-deploy-and-where.md) | &nbsp; | Wdrażanie modeli jako usługi sieci web. |
| [Moduł usługi Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Wdrażanie modeli na urządzeniach IoT Edge. |

W sytuacjach, gdy zestaw SDK nie zapewnia dostępu do magazynów danych, można utworzyć kod niestandardowy przy użyciu odpowiedniego narzędzia Azure SDK, aby uzyskać dostęp do danych. Na przykład [zestaw SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python) to biblioteka klienta, której można użyć do uzyskania dostępu do danych przechowywanych w obiektach blob lub plików.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Przenoszenie danych do obsługiwanych rozwiązań magazynu platformy Azure

Usługa Azure Machine Learning obsługuje uzyskiwanie dostępu do danych z magazynu obiektów Blob platformy Azure, plików Azure, usługi Azure Data Lake Storage Gen1, usługi Azure Data Lake Storage Gen2, bazy danych SQL azure i bazy danych azure dla postgreSQL. Jeśli używasz nieobsługiwał magazynu, zaleca się przeniesienie danych do obsługiwanych rozwiązań magazynu platformy Azure przy użyciu [usługi Azure Data Factory i tych kroków.](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool) Przenoszenie danych do obsługiwanego magazynu może pomóc w oszczędzaniu kosztów transferu danych wychodzących podczas eksperymentów uczenia maszynowego. 

Usługa Azure Data Factory zapewnia wydajny i odporny transfer danych z ponad 80 wstępnie utworzonymi łącznikami bez dodatkowych kosztów. Te łączniki obejmują usługi danych platformy Azure, lokalne źródła danych, Amazon S3 i Redshift oraz Google BigQuery.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie zestawu danych usługi Azure machine learning](how-to-create-register-datasets.md)
* [Szkolenie modelu](how-to-train-ml-models.md)
* [Wdrażanie modelu](how-to-deploy-and-where.md)
