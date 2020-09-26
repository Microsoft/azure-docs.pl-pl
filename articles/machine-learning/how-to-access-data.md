---
title: Nawiązywanie połączenia z usługami Azure Storage
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać magazynów danych do bezpiecznego łączenia się z usługami Azure Storage podczas uczenia się z Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 07/22/2020
ms.custom: how-to, contperfq1, devx-track-python
ms.openlocfilehash: 639e4cde82fd8496c90f8ebf263dd42661a75748
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91296811"
---
# <a name="connect-to-azure-storage-services"></a>Nawiązywanie połączenia z usługami Azure Storage

W tym artykule dowiesz się, jak **nawiązać połączenie z usługami Azure Storage za pośrednictwem Azure Machine Learning magazynów danych**. Magazyny danych bezpiecznie łączą się z usługą Azure Storage bez konieczności podawania poświadczeń uwierzytelniania i integralności oryginalnego źródła. Przechowują one informacje o połączeniach, takie jak identyfikator subskrypcji i autoryzacja tokenu w [Key Vault](https://azure.microsoft.com/services/key-vault/) skojarzony z obszarem roboczym, dzięki czemu można bezpiecznie uzyskać dostęp do magazynu bez konieczności nawiązywania w nich kodu. Możesz użyć [Azure Machine Learning Python SDK](#python) lub [Azure Machine Learning Studio](how-to-connect-data-ui.md) do tworzenia i rejestrowania magazynów danych.

Jeśli wolisz tworzyć magazyny danych i zarządzać nimi przy użyciu rozszerzenia Azure Machine Learning VS Code; Aby dowiedzieć się więcej, odwiedź stronę [pomocy dotyczącej zarządzania zasobami vs Code](how-to-manage-resources-vscode.md#datastores) .

Magazyny danych można tworzyć na podstawie [tych rozwiązań usługi Azure Storage](#matrix). **W przypadku nieobsługiwanych rozwiązań magazynu**i zapisania kosztu ruchu wychodzącego w trakcie eksperymentów z systemem Azure należy [przenieść dane](#move) do obsługiwanego rozwiązania magazynu na potrzeby magazynowania.  

Aby zrozumieć, w jaki sposób magazyn danych mieści się w przepływie pracy ogólnego dostępu do danych Azure Machine Learning, zobacz artykuł dotyczący [bezpiecznego dostępu do danych](concept-data.md#data-workflow) .

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne będą następujące elementy:
- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

- Konto usługi Azure Storage z [obsługiwanym typem magazynu](#matrix).

- [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)lub dostęp do programu [Azure Machine Learning Studio](https://ml.azure.com/).

- Obszar roboczy usługi Azure Machine Learning.
  
  [Utwórz obszar roboczy Azure Machine Learning](how-to-manage-workspace.md) lub Użyj istniejącego z nich za pomocą zestawu SDK języka Python. 

    Zaimportuj `Workspace` klasę i i `Datastore` Załaduj informacje o subskrypcji z pliku `config.json` przy użyciu funkcji `from_config()` . Spowoduje to, że plik JSON jest domyślnie używany w bieżącym katalogu, ale można także określić parametr ścieżki, aby wskazać plik przy użyciu `from_config(path="your/file/path")` .

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

    Podczas tworzenia obszaru roboczego kontener obiektów blob platformy Azure i udział plików platformy Azure są automatycznie rejestrowane jako magazyny danych w obszarze roboczym. Są one nazwane `workspaceblobstore` i `workspacefilestore` , odpowiednio. Służy `workspaceblobstore` do przechowywania artefaktów obszaru roboczego i dzienników eksperymentów w usłudze Machine Learning. Jest również ustawiony jako **domyślny magazyn** danych i nie można go usunąć z obszaru roboczego. `workspacefilestore`Służy do przechowywania notesów i skryptów języka R autoryzowanych za pośrednictwem [wystąpienia obliczeniowego](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files).
    
    > [!NOTE]
    > Program Azure Machine Learning Designer utworzy automatycznie magazyn danych o nazwie **azureml_globaldatasets** po otwarciu przykładu na stronie głównej projektanta. Ten magazyn danych zawiera tylko przykładowe zestawy danych. **Nie** należy używać tego magazynu danych do uzyskiwania dostępu do poufnych informacji.

<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Obsługiwane typy usługi magazynu danych

Magazyny danych obsługują obecnie przechowywanie informacji o połączeniu z usługami magazynu wymienionymi w poniższej macierzy.

| &nbsp;Typ magazynu | &nbsp;Typ uwierzytelniania | [Usługa Azure &nbsp; Machine &nbsp; Learning Studio](https://ml.azure.com/) | [&nbsp;Zestaw SDK języka Python usługi Azure Machine &nbsp; Learning &nbsp;](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) |  [&nbsp; &nbsp; Interfejs wiersza polecenia usługi Azure Machine Learning](reference-azure-machine-learning-cli.md) | [&nbsp; &nbsp; &nbsp; Interfejs API REST usługi Azure Machine Learning](https://docs.microsoft.com/rest/api/azureml/) | VS Code
---|---|---|---|---|---|---
[&nbsp;Magazyn obiektów blob platformy Azure &nbsp;](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Klucz konta <br> Token SAS | ✓ | ✓ | ✓ |✓ |✓
[&nbsp;Udział plików platformy Azure &nbsp;](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Klucz konta <br> Token SAS | ✓ | ✓ | ✓ |✓|✓
[Azure &nbsp; Data Lake &nbsp; Storage gen &nbsp; 1](https://docs.microsoft.com/azure/data-lake-store/)| Jednostka usługi| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; Data Lake &nbsp; Storage gen &nbsp; 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Jednostka usługi| ✓ | ✓ | ✓ |✓|
[&nbsp; &nbsp; Baza danych SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| Uwierzytelnianie SQL <br>Jednostka usługi| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | Uwierzytelnianie SQL| ✓ | ✓ | ✓ |✓|
[Usługa Azure &nbsp; Database &nbsp; for &nbsp; MySQL](https://docs.microsoft.com/azure/mysql/overview) | Uwierzytelnianie SQL|  | ✓* | ✓* |✓*|
[&nbsp;System plików datakostek &nbsp;](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Brak uwierzytelniania | | ✓** | ✓ ** |✓** |

\* Baza danych MySQL jest obsługiwana tylko w przypadku potoku [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py&preserve-view=true)<br />
\*\*Datakostki są obsługiwane tylko dla [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py&preserve-view=true) potoków


### <a name="storage-guidance"></a>Wskazówki dotyczące magazynu

Zalecamy utworzenie magazynu danych dla [kontenera obiektów blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Magazyny w warstwie Standardowa i Premium są dostępne dla obiektów BLOB. Mimo że Usługa Premium Storage jest droższa, jego szybkość przepływności może zwiększyć szybkość przebiegów szkoleniowych, szczególnie w przypadku uczenia się z dużym zestawem danych. Aby uzyskać informacje o kosztach kont magazynu, zobacz [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) jest oparta na usłudze Azure Blob Storage i została zaprojektowana z myślą o analizie danych Big Data w przedsiębiorstwie. Podstawową częścią Data Lake Storage Gen2 jest dodanie [hierarchicznej przestrzeni nazw](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) do magazynu obiektów BLOB. Hierarchiczna przestrzeń nazw organizuje obiekty/pliki w hierarchię katalogów w celu zapewnienia wydajnego dostępu do danych.

## <a name="storage-access-and-permissions"></a>Dostęp do magazynu i uprawnienia

Aby zapewnić bezpieczne łączenie się z usługą Azure Storage, Azure Machine Learning wymaga uprawnień dostępu do odpowiedniego kontenera magazynu danych. Ten dostęp zależy od poświadczeń uwierzytelniania używanych do rejestracji magazynu danych. 

### <a name="virtual-network"></a>Sieć wirtualna 

Jeśli Twoje konto magazynu danych znajduje się w **sieci wirtualnej**, wymagane są dodatkowe czynności konfiguracyjne, aby zapewnić, że Azure Machine Learning ma dostęp do danych. Zapoznaj się z tematem [Korzystanie z programu Azure Machine Learning Studio w sieci wirtualnej platformy Azure](how-to-enable-studio-virtual-network.md) , aby upewnić się, że podczas tworzenia i rejestrowania magazynu danych są stosowane odpowiednie czynności konfiguracyjne.  

### <a name="access-validation"></a>Sprawdzanie poprawności dostępu

**W ramach początkowego procesu tworzenia i rejestracji magazynu**danych Azure Machine Learning automatycznie sprawdza, czy istnieje podstawowa usługa magazynu, oraz czy podany przez użytkownika podmiot zabezpieczeń (username, nazwa główna usługi lub token SAS) ma dostęp do określonego magazynu.

**Po utworzeniu magazynu**danych sprawdzanie poprawności jest wykonywane tylko w przypadku metod, które wymagają dostępu do źródłowego kontenera magazynu, a **nie** do pobierania obiektów magazynu danych. Na przykład sprawdzanie poprawności ma miejsce, jeśli chcesz pobrać pliki z magazynu danych; ale jeśli chcesz jedynie zmienić domyślny magazyn danych, walidacja nie następuje.

Aby uwierzytelnić dostęp do podstawowej usługi magazynu, możesz podać swój klucz konta, tokeny sygnatur dostępu współdzielonego (SAS) lub nazwę główną usługi w odpowiedniej `register_azure_*()` metodzie typu magazynu danych, który chcesz utworzyć. [Macierz typ magazynu](#matrix) zawiera listę obsługiwanych typów uwierzytelniania, które odpowiadają każdemu typowi magazynu danych.

Informacje na temat klucza konta, tokenu sygnatury dostępu współdzielonego i nazwy głównej usługi znajdują się w [Azure Portal](https://portal.azure.com).

* Jeśli planujesz użyć klucza konta lub tokenu sygnatury dostępu współdzielonego w celu uwierzytelnienia, wybierz pozycję **konta magazynu** w okienku po lewej stronie i wybierz konto magazynu, które chcesz zarejestrować. 
  * Na stronie **Przegląd** znajdują się takie informacje, jak nazwa konta, kontener i nazwa udziału plików. 
      1. W przypadku kluczy konta przejdź do pozycji **klucze dostępu** w okienku **Ustawienia** . 
      1. W przypadku tokenów SAS przejdź do obszaru **sygnatury dostępu współdzielonego** w okienku **Ustawienia** .

* Jeśli planujesz użyć jednostki usługi do uwierzytelniania, przejdź do **rejestracje aplikacji** i wybierz aplikację, której chcesz użyć. 
    * Odpowiadająca jej Strona **przeglądu** będzie zawierać wymagane informacje, takie jak identyfikator dzierżawy i identyfikator klienta.

> [!IMPORTANT]
> Ze względów bezpieczeństwa może zajść potrzeba zmiany kluczy dostępu dla konta usługi Azure Storage (klucza konta lub tokenu SAS). W takim przypadku należy zsynchronizować nowe poświadczenia z obszarem roboczym i magazynami danych, które są z nim połączone. Dowiedz się, jak [synchronizować zaktualizowane poświadczenia](how-to-change-storage-access-key.md). 

### <a name="permissions"></a>Uprawnienia

W przypadku kontenera obiektów blob platformy Azure i Azure Data Lake magazynu generacji 2 Upewnij się, że poświadczenia uwierzytelniania mają dostęp do **czytnika danych obiektu blob magazynu** . Dowiedz się więcej o [czytniku danych BLOB Storage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader). Token sygnatury dostępu współdzielonego konta domyślnie nie ma uprawnień. W przypadku dostępu do odczytu danych poświadczenia uwierzytelniania muszą mieć co najmniej uprawnienia do wyświetlania i odczytu dla kontenerów i obiektów. W przypadku dostępu do zapisu danych wymagane są również uprawnienia do zapisu i dodawania.

<a name="python"></a>

## <a name="create-and-register-datastores"></a>Tworzenie i rejestrowanie magazynów danych

Po zarejestrowaniu rozwiązania usługi Azure Storage jako magazynu danych automatycznie utworzysz i zarejestrujesz ten magazyn danych w określonym obszarze roboczym. Zapoznaj się z sekcją [uprawnień & dostępu do magazynu](#storage-access-and-permissions) , aby uzyskać wskazówki dotyczące scenariuszy sieci wirtualnych oraz znaleźć wymagane poświadczenia uwierzytelniania. 

W tej sekcji przedstawiono przykłady tworzenia i rejestrowania magazynu danych za pomocą zestawu SDK języka Python dla następujących typów magazynów. Parametry podane w tych przykładach są parametrami **wymaganymi** do utworzenia i zarejestrowania magazynu danych.

* [Kontener obiektów blob platformy Azure](#azure-blob-container)
* [Udział plików platformy Azure](#azure-file-share)
* [Azure Data Lake Storage generacja 2](#azure-data-lake-storage-generation-2)

 Aby utworzyć magazyny danych dla innych obsługiwanych usług magazynu, zapoznaj się z [dokumentacją referencyjną dotyczącą odpowiednich `register_azure_*` metod](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py&preserve-view=true#&preserve-view=truemethods).

Jeśli wolisz o niskim kodzie, zobacz [nawiązywanie połączenia z danymi za pomocą programu Azure Machine Learning Studio](how-to-connect-data-ui.md).

> [!NOTE]
> Nazwa magazynu danych powinna zawierać tylko małe litery, cyfry i znaki podkreślenia. 

### <a name="azure-blob-container"></a>Kontener obiektów blob platformy Azure

Aby zarejestrować kontener obiektów blob platformy Azure jako magazyn danych, użyj elementu [`register_azure_blob_container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

Poniższy kod powoduje utworzenie i zarejestrowanie `blob_datastore_name` magazynu danych w `ws` obszarze roboczym. Ten magazyn danych uzyskuje dostęp do `my-container-name` kontenera obiektów BLOB na `my-account-name` koncie magazynu przy użyciu podanego klucza dostępu do konta. Zapoznaj się z sekcją [uprawnień & dostępu do magazynu](#storage-access-and-permissions) , aby uzyskać wskazówki dotyczące scenariuszy sieci wirtualnych oraz znaleźć wymagane poświadczenia uwierzytelniania. 

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

### <a name="azure-file-share"></a>Udział plików platformy Azure

Aby zarejestrować udział plików platformy Azure jako magazyn danych, użyj [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) . 

Poniższy kod powoduje utworzenie i zarejestrowanie `file_datastore_name` magazynu danych w `ws` obszarze roboczym. Ten magazyn danych uzyskuje dostęp do `my-fileshare-name` udziału plików na `my-account-name` koncie magazynu przy użyciu podanego klucza dostępu do konta. Zapoznaj się z sekcją [uprawnień & dostępu do magazynu](#storage-access-and-permissions) , aby uzyskać wskazówki dotyczące scenariuszy sieci wirtualnych oraz znaleźć wymagane poświadczenia uwierzytelniania. 

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

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage generacja 2

W przypadku magazynu danych Azure Data Lake Storage Generation 2 (ADLS Gen 2) należy użyć [register_azure_data_lake_gen2 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) do zarejestrowania magazynu danych poświadczeń połączonego z magazynem usługi Azure datalake Generation 2 z [uprawnieniami nazw głównych](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).  

Aby można było korzystać z nazwy głównej usługi, należy [zarejestrować aplikację](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) i udzielić jej jednostce usługi z dostępem **czytnika danych obiektów blob magazynu** . Dowiedz się więcej [na temat kontroli dostępu skonfigurowanej do ADLS generacji 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Poniższy kod powoduje utworzenie i zarejestrowanie `adlsgen2_datastore_name` magazynu danych w `ws` obszarze roboczym. Ten magazyn danych uzyskuje dostęp do systemu plików `test` na `account_name` koncie magazynu przy użyciu podanych poświadczeń jednostki usługi. Zapoznaj się z sekcją [uprawnień & dostępu do magazynu](#storage-access-and-permissions) , aby uzyskać wskazówki dotyczące scenariuszy sieci wirtualnych oraz znaleźć wymagane poświadczenia uwierzytelniania. 

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

<a name="arm"></a>

## <a name="create-datastores-using-azure-resource-manager"></a>Tworzenie magazynów danych przy użyciu Azure Resource Manager

Istnieje kilka szablonów [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-datastore-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) , które mogą służyć do tworzenia magazynów danych.

Aby uzyskać informacje na temat korzystania z tych szablonów, zobacz [Tworzenie obszaru roboczego dla Azure Machine Learning za pomocą szablonu Azure Resource Manager](how-to-create-workspace-template.md).

<a name="train"></a>
## <a name="use-data-in-your-datastores"></a>Korzystanie z danych w twoich sklepach

Po utworzeniu magazynu danych [utwórz Azure Machine Learning zestaw danych](how-to-create-register-datasets.md) , który będzie współpracujący z danymi. Zestawy danych pakują dane do opóźnieniemego, który można z niego korzystać na potrzeby zadań uczenia maszynowego, takich jak szkolenie. Zapewniają one również możliwość [pobierania lub instalowania](how-to-train-with-datasets.md#mount-vs-download) plików dowolnego formatu z usług Azure Storage, takich jak Azure Blob Storage i ADLS Gen 2. Można ich również użyć do załadowania danych tabelarycznych do Pandas lub Spark Dataframe.

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Pobieranie magazynów danych z obszaru roboczego

Aby uzyskać określony magazyn danych zarejestrowany w bieżącym obszarze roboczym, użyj [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-workspace--datastore-name-) statycznej metody `Datastore` klasy:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Aby uzyskać listę magazynów danych zarejestrowanych w danym obszarze roboczym, możesz użyć [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedatastores) właściwości w obiekcie obszaru roboczego:

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
