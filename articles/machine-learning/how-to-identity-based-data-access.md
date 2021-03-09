---
title: Dostęp do danych na podstawie tożsamości do usług magazynu na platformie Azure
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak korzystać z dostępu do danych opartych na tożsamościach w celu łączenia się z usługami magazynu na platformie Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: 68d07481e228b1d1b2f4571a783f925add261cff
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520017"
---
# <a name="connect-to-storage-with-identity-based-data-access-preview"></a>Łączenie z magazynem za pomocą dostępu do danych opartych na tożsamościach (wersja zapoznawcza)

>[!IMPORTANT]
> Funkcje przedstawione w tym artykule są w wersji zapoznawczej i powinny być uznawane za [eksperymentalne](/python/api/overview/azure/ml/#stable-vs-experimental) funkcje w wersji zapoznawczej, które mogą ulec zmianie w dowolnym momencie.

W tym artykule dowiesz się, jak nawiązać połączenie z usługami magazynu na platformie Azure z dostępem do danych opartymi na tożsamościach i Azure Machine Learning magazynami dostępu za pośrednictwem [Azure Machine Learning zestawu SDK języka Python](/python/api/overview/azure/ml/intro).  

Zwykle magazyny danych wykorzystują dostęp oparty na poświadczeniach, aby potwierdzić, że masz uprawnienia dostępu do usługi magazynu. Przechowują one informacje o połączeniach, takie jak identyfikator subskrypcji i autoryzacja tokenu, w [Key Vault](https://azure.microsoft.com/services/key-vault/) , które są skojarzone z obszarem roboczym. Gdy tworzysz magazyn danych, który korzysta z dostępu opartego na tożsamościach, logowanie do platformy Azure ([token Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)) służy do potwierdzenia, że masz uprawnienia dostępu do usługi magazynu. W tym scenariuszu poświadczenia uwierzytelniania nie są zapisywane i tylko informacje o kontach magazynu są przechowywane w magazynie danych. 

Aby utworzyć magazyny danych korzystające z uwierzytelniania opartego na poświadczeniach, takich jak klucze dostępu lub jednostki usługi, zobacz [nawiązywanie połączenia z usługami magazynu na platformie Azure](how-to-access-data.md).

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Dostęp do danych opartych na tożsamościach w Azure Machine Learning

Istnieją dwa obszary, w których można stosować dostęp do danych opartych na tożsamościach w Azure Machine Learning. Szczególnie, podczas pracy z danymi poufnymi i potrzebują bardziej szczegółowych informacji na temat zarządzania dostępem do danych. 

1. Uzyskiwanie dostępu do usług magazynu.
1. Uczenie modeli uczenia maszynowego z danymi prywatnymi.

### <a name="accessing-storage-services"></a>Uzyskiwanie dostępu do usług magazynu

Można nawiązać połączenie z usługami magazynu za pośrednictwem dostępu do danych opartych na tożsamościach przy użyciu Azure Machine Learning magazynów obiektów lub [Azure Machine Learning zestawów danych](how-to-create-register-datasets.md). 

Zwykle poświadczenia uwierzytelniania są przechowywane w magazynie danych, który jest używany do upewnienia się, że masz uprawnienia dostępu do usługi magazynu. Gdy te poświadczenia są zarejestrowane w magazynach danych, każdy użytkownik z rolą *czytelnika* obszaru roboczego może je pobrać — co może stanowić zagrożenie dla niektórych organizacji. [Dowiedz się więcej o roli *czytelnik* obszaru roboczego](how-to-assign-roles.md#default-roles). 

W przypadku korzystania z dostępu do danych opartych na tożsamościach Azure Machine Learning będzie monitowany o token Azure Active Directory na potrzeby uwierzytelniania dostępu do danych, a nie do przechowywania swoich poświadczeń w magazynie danych. Dzięki czemu można zarządzać dostępem do danych na poziomie magazynu i zachować poufne poświadczenia. 

Takie samo zachowanie jest stosowane w przypadku,

* [Utwórz zestaw danych bezpośrednio z adresów URL magazynu](#use-data-in-storage). 
* Interaktywna współpraca z danymi za pośrednictwem notesu Jupyter na komputerze lokalnym lub [wystąpieniu obliczeniowym](concept-compute-instance.md).

> [!NOTE]
> Poświadczenia przechowywane przy użyciu uwierzytelniania opartego na poświadczeniach obejmują: Identyfikator subskrypcji, tokeny sygnatury dostępu współdzielonego (SAS), klucze dostępu do magazynu i informacje o jednostce usługi, takie jak identyfikator klienta i identyfikator dzierżawy.

### <a name="model-training-on-private-data"></a>Modelowanie szkoleń dotyczących danych prywatnych

Niektóre scenariusze uczenia maszynowego obejmują modele szkoleniowe z danymi prywatnymi. W takich przypadkach analitykowie danych muszą uruchamiać szkoleniowe przepływy pracy bez narażenia na poufne dane wejściowe. W tym scenariuszu zarządzana tożsamość obliczeń szkoleniowych jest używana do uwierzytelniania dostępu do danych. W ten sposób administratorzy magazynu mogą udzielić dostępu **czytnika danych obiektów BLOB** do zarządzanej tożsamości używanej przez obliczenia szkoleniowe do uruchomienia zadania szkoleniowego zamiast poszczególnych analityków danych. Dowiedz się, jak [skonfigurować tożsamość zarządzaną w ramach obliczeń](how-to-create-attach-compute-cluster.md#managed-identity).


## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

- Konto usługi Azure Storage z obsługiwanym typem magazynu. Następujące typy magazynów są obsługiwane w wersji zapoznawczej. 
    - [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake Gen 1](../data-lake-store/index.yml)
    - [Azure Data Lake Gen 2](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL database](../azure-sql/database/sql-database-paas-overview.md)

- [Zestaw Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/install).

- Obszar roboczy usługi Azure Machine Learning.
  
  [Utwórz obszar roboczy Azure Machine Learning](how-to-manage-workspace.md) lub Użyj [istniejącego z nich za pomocą zestawu SDK języka Python](how-to-manage-workspace.md#connect-to-a-workspace). 

## <a name="storage-access-permissions"></a>Uprawnienia dostępu do magazynu

Aby zapewnić bezpieczne łączenie się z usługą magazynu na platformie Azure, Azure Machine Learning wymaga uprawnień dostępu do odpowiedniego magazynu danych.

Dostęp do danych opartych na tożsamościach obsługuje tylko połączenia z następującymi usługami magazynu:

* Azure Blob Storage
* Azure Data Lake generacja 1
* Azure Data Lake generacja 2
* Baza danych Azure SQL Database

Aby uzyskać dostęp do tych usług magazynu, należy dysponować minimalnym dostępem **czytnika danych obiektów blob magazynu** . Dowiedz się więcej o [czytniku danych BLOB Storage](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Tylko właściciele konta magazynu mogą [zmieniać poziom dostępu za pośrednictwem Azure Portal](../storage/common/storage-auth-aad-rbac-portal.md).

W przypadku szkolenia modelu w zdalnym miejscu docelowym obliczeń tożsamość obliczeniowa musi być przyznana z co najmniej rolą **czytnika danych BLOB magazynu** z usługi magazynu. Dowiedz się, jak [skonfigurować zarządzaną tożsamość przy obliczaniu](how-to-create-attach-compute-cluster.md#managed-identity).

## <a name="work-with-virtual-networks"></a>Współpraca z sieciami wirtualnymi

Domyślnie Azure Machine Learning nie może komunikować się z kontem magazynu, które znajduje się za zaporą lub w sieci wirtualnej.

Konta magazynu można skonfigurować tak, aby zezwalały na dostęp tylko z określonych sieci wirtualnych, co wymaga dodatkowych konfiguracji, aby zapewnić, że dane nie są wyciekami poza siecią. Takie zachowanie jest takie samo w przypadku dostępu do danych opartych na poświadczeniach, zobacz, [jakie konfiguracje są potrzebne i jak zastosować je do scenariuszy sieci wirtualnych](how-to-access-data.md#virtual-network). 

## <a name="create-and-register-datastores"></a>Tworzenie i rejestrowanie magazynów danych

Po zarejestrowaniu usługi magazynu na platformie Azure jako magazynu danych automatycznie utworzysz i zarejestrujesz ten magazyn danych w określonym obszarze roboczym. Zapoznaj się z tymi sekcjami: [uprawnienia dostępu do magazynu](#storage-access-permissions) dla wskazówek dotyczących wymaganych typów uprawnień i [Pracuj z siecią wirtualną](#work-with-virtual-networks) , aby uzyskać szczegółowe informacje na temat łączenia się z magazynem danych za sieciami wirtualnymi.

W poniższym kodzie Zwróć uwagę na brak parametrów uwierzytelniania, takich jak `sas_token` ,, `account_key` lub nazwa `subscription_id` główna usługi `client_id` . To pominięcie wskazuje, że Azure Machine Learning ma używać dostępu do danych na podstawie tożsamości do uwierzytelniania. Ponieważ tworzenie magazynów danych zwykle odbywa się interaktywnie w notesie lub za pośrednictwem programu Studio, token Azure Active Directory jest używany do uwierzytelniania dostępu do danych.

> [!NOTE]
> Nazwy magazynów danych powinny zawierać tylko małe litery, cyfry i znaki podkreślenia. 

### <a name="azure-blob-container"></a>Kontener obiektów blob platformy Azure

Aby zarejestrować kontener obiektów blob platformy Azure jako magazyn danych, użyj elementu [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

Poniższy kod tworzy i rejestruje magazyn danych `credentialless_blob` w `ws` obszarze roboczym i przypisuje go do zmiennej `blob_datastore` . Ten magazyn danych uzyskuje dostęp do `my_container_name` kontenera obiektów BLOB na `my-account-name` koncie magazynu.

```Python
# create blob datastore without credentials
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-generation-1"></a>Azure Data Lake Storage generacja 1

W przypadku magazynu danych Azure Data Lake Storage Generation 1 (ADLS Gen 1) Użyj [register_azure_data_lake ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) do zarejestrowania magazynu obiektów, który nawiązuje połączenie z magazynem usługi Azure datalake Generation 1.

Poniższy kod tworzy i rejestruje magazyn danych `credentialless_adls1` w `workspace` obszarze roboczym i przypisuje go do zmiennej `adls_dstore` . Ten magazyn danych uzyskuje dostęp do `adls_storage` konta magazynu Azure Data Lake Store.

```Python
# create adls gen1 without credentials
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage generacja 2

W przypadku magazynu danych Azure Data Lake Storage Generation 2 (ADLS Gen 2) Użyj [register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) do zarejestrowania magazynu obiektów, który nawiązuje połączenie z magazynem usługi Azure datalake Gen 2.

Poniższy kod tworzy i rejestruje magazyn danych `credentialless_adls2` w `ws` obszarze roboczym i przypisuje go do zmiennej `adls2_dstore` . Ten magazyn danych uzyskuje dostęp do systemu plików `tabular` na `myadls2` koncie magazynu.  

```python
# createn adls2 datastore without credentials
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>Korzystanie z danych w magazynie

[Zestawy danych Azure Machine Learning](how-to-create-register-datasets.md) są zalecanym sposobem współpracy z danymi w magazynie przy użyciu Azure Machine Learning. 

Zestawy danych pakują dane do opóźnieniemego, który można z niego korzystać na potrzeby zadań uczenia maszynowego, takich jak szkolenie. Ponadto dzięki zestawom danych można [pobierać lub instalować](how-to-train-with-datasets.md#mount-vs-download) pliki dowolnego formatu z usług Azure Storage, takich jak Azure Blob Storage i Azure Data jeziors, do obiektu docelowego obliczeń.


**Aby utworzyć zestawy danych z dostępem opartym na tożsamościach**, dostępne są następujące opcje. Ten typ tworzenia zestawu danych wykorzystuje token Azure Active Directory na potrzeby uwierzytelniania dostępu do danych. 

*  Ścieżki odniesienia z magazynów danych, które również korzystają z dostępu opartego na tożsamościach. 
<br>W poniższym przykładzie `blob_datastore` został utworzony wcześniej przy użyciu dostępu do danych na podstawie tożsamości.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Pomijaj tworzenie magazynu danych i twórz zbiory bezpośrednio z adresów URL magazynu. Obecnie ta funkcja obsługuje tylko obiekty blob platformy Azure i Azure Data Lake Storage generacji 1 i 2.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

**Jednak podczas przesyłania zadania szkoleniowego, które korzysta z zestawu danych utworzonego z dostępem do danych opartych na tożsamości**, zarządzana tożsamość obliczeń szkoleniowych jest używana do uwierzytelniania dostępu do danych, a nie do tokenu Azure Active Directory. W tym scenariuszu upewnij się, że zarządzana tożsamość obliczeń jest przyznana z co najmniej rolą **czytnika danych obiektów blob magazynu** z usługi Storage. Dowiedz się, jak [skonfigurować zarządzaną tożsamość przy obliczaniu](how-to-create-attach-compute-cluster.md#managed-identity). 

## <a name="next-steps"></a>Następne kroki

* [Utwórz zestaw danych usługi Azure Machine Learning](how-to-create-register-datasets.md).
* [Uczenie się z zestawami danych](how-to-train-with-datasets.md).
* [Utwórz magazyn danych z dostępem opartym na kluczach](how-to-access-data.md).
