---
title: Nawiązywanie połączenia z usługami Azure Storage
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać magazynów danych do bezpiecznego łączenia się z usługami Azure Storage podczas uczenia się z Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: d0f37d32b7d2306865d435bc68ea12c9bb95651c
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434714"
---
# <a name="connect-to-azure-storage-services"></a>Nawiązywanie połączenia z usługami Azure Storage
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak nawiązać połączenie z usługami Azure Storage za pośrednictwem Azure Machine Learning magazynów danych. Magazyny danych przechowują informacje o połączeniu, takie jak identyfikator subskrypcji i autoryzacja tokenu w [Key Vault](https://azure.microsoft.com/services/key-vault/) skojarzonych z obszarem roboczym, dzięki czemu można bezpiecznie uzyskać dostęp do magazynu bez konieczności nawiązywania ich w skryptach. Aby zrozumieć, w jaki sposób magazyn danych mieści się w przepływie pracy ogólnego dostępu do danych Azure Machine Learning, zobacz artykuł dotyczący [bezpiecznego dostępu do danych](concept-data.md#data-workflow) .

Magazyny danych można tworzyć na podstawie [tych rozwiązań usługi Azure Storage](#matrix). W przypadku nieobsługiwanych rozwiązań magazynu i zapisania kosztów ruchu wychodzącego w trakcie eksperymentów z uczeniem maszynowym zalecamy [przeniesienie danych](#move) do obsługiwanych rozwiązań usługi Azure Storage. 

## <a name="prerequisites"></a>Wymagania wstępne

Będą potrzebne:
- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

- Konto usługi Azure Storage z [kontenerem obiektów blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) lub [udziałem plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)lub dostęp do programu [Azure Machine Learning Studio](https://ml.azure.com/).

- Obszar roboczy usługi Azure Machine Learning.
  
  [Utwórz obszar roboczy Azure Machine Learning](how-to-manage-workspace.md) lub Użyj istniejącego z nich za pomocą zestawu SDK języka Python. Zaimportuj `Workspace` klasę i i `Datastore` Załaduj informacje o subskrypcji z pliku `config.json` przy użyciu funkcji `from_config()` . Spowoduje to, że plik JSON jest domyślnie używany w bieżącym katalogu, ale można także określić parametr ścieżki, aby wskazać plik przy użyciu `from_config(path="your/file/path")` .

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Obsługiwane typy usługi magazynu danych

Magazyny danych obsługują obecnie przechowywanie informacji o połączeniu z usługami magazynu wymienionymi w poniższej macierzy.

| &nbsp;Typ magazynu | &nbsp;Typ uwierzytelniania | [Usługa Azure &nbsp; Machine &nbsp; Learning Studio](https://ml.azure.com/) | [&nbsp;Zestaw SDK języka Python usługi Azure Machine &nbsp; Learning &nbsp;](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [&nbsp; &nbsp; Interfejs wiersza polecenia usługi Azure Machine Learning](reference-azure-machine-learning-cli.md) | [&nbsp; &nbsp; &nbsp; Interfejs API REST usługi Azure Machine Learning](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[&nbsp;Magazyn obiektów blob platformy Azure &nbsp;](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Klucz konta <br> Token SAS | ✓ | ✓ | ✓ |✓
[&nbsp;Udział plików platformy Azure &nbsp;](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Klucz konta <br> Token SAS | ✓ | ✓ | ✓ |✓
[Azure &nbsp; Data Lake &nbsp; Storage gen &nbsp; 1](https://docs.microsoft.com/azure/data-lake-store/)| Jednostka usługi| ✓ | ✓ | ✓ |✓
[Azure &nbsp; Data Lake &nbsp; Storage gen &nbsp; 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Jednostka usługi| ✓ | ✓ | ✓ |✓
[&nbsp; &nbsp; Baza danych SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| Uwierzytelnianie SQL <br>Jednostka usługi| ✓ | ✓ | ✓ |✓
[Azure &nbsp; PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | Uwierzytelnianie SQL| ✓ | ✓ | ✓ |✓
[Usługa Azure &nbsp; Database &nbsp; for &nbsp; MySQL](https://docs.microsoft.com/azure/mysql/overview) | Uwierzytelnianie SQL|  | ✓* | ✓* |✓*
[&nbsp;System plików datakostek &nbsp;](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Brak uwierzytelniania | | ✓** | ✓ ** |✓** 

* Baza danych MySQL jest obsługiwana tylko w przypadku potoku [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). <br>
* * Datakostki są obsługiwane tylko dla [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) potoków

### <a name="storage-guidance"></a>Wskazówki dotyczące magazynu

Zalecamy utworzenie magazynu danych dla [kontenera obiektów blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Magazyny w warstwie Standardowa i Premium są dostępne dla obiektów BLOB. Mimo że Usługa Premium Storage jest droższa, jego szybkość przepływności może zwiększyć szybkość przebiegów szkoleniowych, szczególnie w przypadku uczenia się z dużym zestawem danych. Aby uzyskać informacje o kosztach kont magazynu, zobacz [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) jest oparta na usłudze Azure Blob Storage i została zaprojektowana z myślą o analizie danych Big Data w przedsiębiorstwie. Podstawową częścią Data Lake Storage Gen2 jest dodanie [hierarchicznej przestrzeni nazw](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) do magazynu obiektów BLOB. Hierarchiczna przestrzeń nazw organizuje obiekty/pliki w hierarchię katalogów w celu zapewnienia wydajnego dostępu do danych.

Podczas tworzenia obszaru roboczego kontener obiektów blob platformy Azure i udział plików platformy Azure są automatycznie rejestrowane w obszarze roboczym. Są one nazwane `workspaceblobstore` i `workspacefilestore` , odpowiednio. `workspaceblobstore`służy do przechowywania artefaktów obszaru roboczego i dzienników eksperymentów w usłudze Machine Learning. `workspacefilestore`służy do przechowywania notesów i skryptów języka R autoryzowanych za pośrednictwem [wystąpienia obliczeniowego](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files). `workspaceblobstore`Kontener jest ustawiony jako domyślny magazyn danych.

> [!IMPORTANT]
> Program Azure Machine Learning Designer (wersja zapoznawcza) utworzy magazyn danych o nazwie **azureml_globaldatasets** automatycznie po otwarciu przykładu na stronie głównej projektanta. Ten magazyn danych zawiera tylko przykładowe zestawy danych. **Nie** należy używać tego magazynu danych do uzyskiwania dostępu do poufnych informacji.
> ![Tworzony przez siebie magazyn danych z przykładowymi zestawami DataSet](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Tworzenie i rejestrowanie magazynów danych

Po zarejestrowaniu rozwiązania usługi Azure Storage jako magazynu danych automatycznie utworzysz i zarejestrujesz ten magazyn danych w określonym obszarze roboczym. Magazyny danych można tworzyć i rejestrować w obszarze roboczym przy użyciu [zestawu SDK języka Python](#python-sdk) lub [Azure Machine Learning Studio](#azure-machine-learning-studio).

>[!IMPORTANT]
> W ramach początkowego procesu tworzenia i rejestrowania magazynu danych Azure Machine Learning sprawdza, czy istnieje podstawowa usługa magazynu oraz czy podano do niego dostęp podmiot zabezpieczeń (nazwa użytkownika, główna usługa lub token SAS). Jednak w przypadku Azure Data Lake Storage generacji 1 i 2 magazynów danych sprawdzanie poprawności odbywa się później, gdy metody dostępu do danych takie jak [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) lub [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) są wywoływane. 
<br><br>
Po utworzeniu magazynu danych sprawdzanie poprawności jest wykonywane tylko w przypadku metod, które wymagają dostępu do źródłowego kontenera magazynu, a **nie** do pobierania obiektów magazynu danych. Na przykład sprawdzanie poprawności ma miejsce, jeśli chcesz pobrać pliki z magazynu danych; ale jeśli chcesz jedynie zmienić domyślny magazyn danych, walidacja nie następuje.

### <a name="python-sdk"></a>Zestaw SDK dla języka Python

Wszystkie metody rejestrowania znajdują się w [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) klasie i mają postać `register_azure_*` .
> [!IMPORTANT]
> Jeśli konto magazynu znajduje się w sieci wirtualnej, obsługiwane jest tylko tworzenie magazynów danych **za pośrednictwem zestawu SDK** .

Możesz znaleźć informacje potrzebne do wypełnienia `register_azure_*()` metody na [Azure Portal](https://portal.azure.com).

* Jeśli planujesz użyć klucza konta lub tokenu sygnatury dostępu współdzielonego w celu uwierzytelnienia, wybierz pozycję **konta magazynu** w okienku po lewej stronie i wybierz konto magazynu, które chcesz zarejestrować. 
  * Na stronie **Przegląd** znajdują się takie informacje, jak nazwa konta, kontener i nazwa udziału plików. 
      1. W przypadku kluczy konta przejdź do pozycji **klucze dostępu** w okienku **Ustawienia** . 
      1. W przypadku tokenów SAS przejdź do obszaru **sygnatury dostępu współdzielonego** w okienku **Ustawienia** .

* Jeśli planujesz używać zasady usługi do uwierzytelniania, przejdź do **rejestracje aplikacji** i wybierz aplikację, której chcesz użyć. 
    * Odpowiadająca jej Strona **przeglądu** będzie zawierać wymagane informacje, takie jak identyfikator dzierżawy i identyfikator klienta.

W poniższych przykładach pokazano, jak zarejestrować kontener obiektów blob platformy Azure, udział plików platformy Azure i Azure Data Lake Storage generacja 2 jako magazyn danych. Parametry podane w tych przykładach są parametrami **wymaganymi** do utworzenia i zarejestrowania magazynu danych. 

Aby utworzyć magazyny danych dla innych usług magazynu i zapoznać się z opcjonalnymi parametrami tych metod, zobacz [dokumentację referencyjną dotyczącą odpowiednich `register_azure_*` metod](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

#### <a name="blob-container"></a>Kontener obiektów blob

Aby zarejestrować kontener obiektów blob platformy Azure jako magazyn danych, użyj elementu [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

Poniższy kod powoduje utworzenie i zarejestrowanie `blob_datastore_name` magazynu danych w `ws` obszarze roboczym. Ten magazyn danych uzyskuje dostęp do `my-container-name` kontenera obiektów BLOB na `my-account-name` koncie magazynu przy użyciu podanego klucza dostępu do konta.

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
Jeśli kontener obiektów BLOB znajduje się w sieci wirtualnej, należy uwzględnić parametr `skip_validation=True` w [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) metodzie. 

#### <a name="file-share"></a>Udział plików

Aby zarejestrować udział plików platformy Azure jako magazyn danych, użyj [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) . 

Poniższy kod powoduje utworzenie i zarejestrowanie `file_datastore_name` magazynu danych w `ws` obszarze roboczym. Ten magazyn danych uzyskuje dostęp do `my-fileshare-name` udziału plików na `my-account-name` koncie magazynu przy użyciu podanego klucza dostępu do konta.

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
Jeśli udział plików znajduje się w sieci wirtualnej, należy uwzględnić parametr `skip_validation=True` w [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) metodzie. 

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage generacja 2

W przypadku magazynu danych Azure Data Lake Storage Generation 2 (ADLS Gen 2) należy użyć [register_azure_data_lake_gen2 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) do zarejestrowania magazynu danych poświadczeń połączonego z magazynem usługi Azure datalake Generation 2 z [uprawnieniami nazw głównych](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). Aby można było korzystać z jednostki usługi, należy [zarejestrować aplikację](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) i przyznać jednostce usługi dostęp do *czytnika danych obiektu blob magazynu* . Dowiedz się więcej [na temat kontroli dostępu skonfigurowanej do ADLS generacji 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Aby można było korzystać z jednostki usługi, należy [zarejestrować aplikację](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) i przyznać jednostce usługi dostęp do odpowiednich danych. Dowiedz się więcej [na temat kontroli dostępu skonfigurowanej do ADLS generacji 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Poniższy kod powoduje utworzenie i zarejestrowanie `adlsgen2_datastore_name` magazynu danych w `ws` obszarze roboczym. Ten magazyn danych uzyskuje dostęp do systemu plików `test` na `account_name` koncie magazynu przy użyciu podanych poświadczeń jednostki usługi.

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

Utwórz nowy magazyn danych w kilku krokach w programie Azure Machine Learning Studio:

> [!IMPORTANT]
> Jeśli konto magazynu znajduje się w sieci wirtualnej, obsługiwane jest tylko tworzenie magazynów danych [za pośrednictwem zestawu SDK](#python-sdk) . 

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com/).
1. Wybierz pozycję **magazyny** danych w lewym okienku w obszarze **Zarządzaj**.
1. Wybierz pozycję **+ nowy magazyn**danych.
1. Wypełnij formularz dla nowego magazynu danych. Formularz jest inteligentnie aktualizowany na podstawie wybranych opcji typu usługi Azure Storage i typu uwierzytelniania.
  
Informacje potrzebne do wypełnienia formularza można znaleźć na [Azure Portal](https://portal.azure.com). W okienku po lewej stronie wybierz pozycję **konta magazynu** , a następnie wybierz konto magazynu, które chcesz zarejestrować. Na stronie **Przegląd** znajdują się takie informacje, jak nazwa konta, kontener i nazwa udziału plików. 

* W przypadku elementów uwierzytelniania, takich jak klucz konta lub token SAS, przejdź do pozycji **klucze dostępu** w okienku **Ustawienia** . 

* W przypadku elementów nazwy głównej usługi, takich jak identyfikator dzierżawy i identyfikator klienta, przejdź do **rejestracje aplikacji** i wybierz aplikację, której chcesz użyć. Odpowiadająca jej Strona **przeglądu** będzie zawierać te elementy. 

Poniższy przykład pokazuje, jak wygląda formularz podczas tworzenia magazynu danych obiektów blob platformy Azure: 
    
![Formularz dla nowego magazynu danych](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Pobieranie magazynów danych z obszaru roboczego

Aby uzyskać określony magazyn danych zarejestrowany w bieżącym obszarze roboczym, użyj [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) statycznej metody `Datastore` klasy:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Aby uzyskać listę magazynów danych zarejestrowanych w danym obszarze roboczym, możesz użyć [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) właściwości w obiekcie obszaru roboczego:

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
Możesz również zmienić domyślny magazyn danych przy użyciu następującego kodu. Ta możliwość jest obsługiwana tylko za pośrednictwem zestawu SDK. 

```Python
 ws.set_default_datastore(new_default_datastore)
```
<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Przekazywanie i pobieranie danych

[`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-)Metody i [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) opisane w poniższych przykładach są specyficzne dla i działają identycznie dla klas [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) i [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

> [!NOTE]
> Przekazywanie do magazynów danych AzureDataLakeGen2 nie jest w tej chwili obsługiwane.

### <a name="upload"></a>Upload

Przekaż katalog lub pojedyncze pliki do magazynu danych przy użyciu zestawu SDK języka Python:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path`Parametr określa lokalizację w udziale plików (lub kontenerze obiektów BLOB) do przekazania. Wartość domyślna to `None` , więc dane są przekazywane do katalogu głównego. Jeśli `overwrite=True` wszystkie istniejące dane `target_path` są zastępowane.

Możesz również przekazać listę pojedynczych plików do magazynu danych za pomocą `upload_files()` metody.

### <a name="download"></a>Pobierz

Pobierz dane z magazynu danych do lokalnego systemu plików:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path`Parametr jest lokalizacją katalogu lokalnego, do którego mają zostać pobrane dane. Aby określić ścieżkę do folderu w udziale plików (lub kontenerze obiektów BLOB) do pobrania, podaj tę ścieżkę `prefix` . Jeśli `prefix` ma wartość `None` , zostanie pobrana cała zawartość udziału plików (lub kontenera obiektów BLOB).

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Uzyskiwanie dostępu do danych podczas szkoleń

Aby móc korzystać z danych w twoich sklepach datastores lub spakować dane do obiektu, którego można używać do wykonywania zadań uczenia maszynowego, takich jak szkolenie, [utwórz Azure Machine Learning zestaw danych](how-to-create-register-datasets.md). Zestawy danych udostępniają funkcje, które ładują dane tabelaryczne do Pandas lub Spark Dataframe. Zestawy danych zapewniają również możliwość pobierania lub instalowania plików dowolnego formatu z usługi Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database i Azure Database for PostgreSQL. [Dowiedz się więcej na temat uczenia się z zestawami danych](how-to-train-with-datasets.md).

### <a name="accessing-source-code-during-training"></a>Uzyskiwanie dostępu do kodu źródłowego podczas szkolenia

Usługa Azure Blob Storage ma większe szybkości przepływności niż udział plików platformy Azure i skalowanie na dużą liczbę zadań uruchomionych równolegle. Z tego powodu zalecamy skonfigurowanie przebiegów do korzystania z usługi BLOB Storage na potrzeby przesyłania plików kodu źródłowego.

Poniższy przykład kodu określa w konfiguracji uruchamiania, która magazyn danych obiektów BLOB ma być używana do transferów kodu źródłowego.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Uzyskiwanie dostępu do danych podczas oceniania

Azure Machine Learning oferuje kilka sposobów na korzystanie z modeli do oceniania. Niektóre z tych metod nie zapewniają dostępu do magazynów danych. Skorzystaj z poniższej tabeli, aby zrozumieć, które metody umożliwiają dostęp do magazynów danych podczas oceniania:

| Metoda | Dostęp do magazynu danych | Opis |
| ----- | :-----: | ----- |
| [Przewidywanie wsadowe](how-to-use-parallel-run-step.md) | ✔ | Asynchronicznie twórz prognozy dotyczące dużych ilości danych. |
| [Usługa sieci Web](how-to-deploy-and-where.md) | &nbsp; | Wdróż modele jako usługę sieci Web. |
| [Moduł Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Wdróż modele na IoT Edge urządzeniach. |

W sytuacjach, w których zestaw SDK nie zapewnia dostępu do magazynów danych, może być możliwe utworzenie niestandardowego kodu przy użyciu odpowiedniego zestawu Azure SDK, aby uzyskać dostęp do tego programu. Na przykład [zestaw SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python) jest biblioteką kliencką, za pomocą której można uzyskać dostęp do danych przechowywanych w obiektach Blob lub plikach.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Przenoszenie danych do obsługiwanych rozwiązań usługi Azure Storage

Azure Machine Learning obsługuje dostęp do danych z usługi Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database i Azure Database for PostgreSQL. Jeśli używasz nieobsługiwanego magazynu, zalecamy przeniesienie danych do obsługiwanych rozwiązań usługi Azure Storage przy użyciu [Azure Data Factory i tych kroków](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool). Przeniesienie danych do obsługiwanego magazynu może pomóc w zapisaniu kosztów ruchu wychodzącego w trakcie eksperymentów z uczeniem maszynowym. 

Azure Data Factory zapewnia wydajny i odporny na transfer danych z ponad 80 wstępnie skompilowanych łączników bez dodatkowych kosztów. Te łączniki obejmują usługi danych platformy Azure, lokalne źródła danych, Amazon S3 i RedShift oraz Google BigQuery.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie zestawu danych usługi Azure Machine Learning](how-to-create-register-datasets.md)
* [Szkolenie modelu](how-to-train-ml-models.md)
* [Wdrażanie modelu](how-to-deploy-and-where.md)
