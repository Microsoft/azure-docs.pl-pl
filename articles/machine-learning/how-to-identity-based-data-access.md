---
title: Dostęp do danych na podstawie tożsamości do usług magazynu na platformie Azure
titleSuffix: Machine Learning
description: Dowiedz się, jak korzystać z dostępu do danych opartych na tożsamościach w celu łączenia się z usługami Storage na platformie Azure przy użyciu Azure Machine Learning magazynów i Machine Learning zestawu SDK języka Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: a46f54bd037dcf8d71ba3fbafb2ba0fd961a32cc
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210656"
---
# <a name="connect-to-storage-by-using-identity-based-data-access-preview"></a>Nawiązywanie połączenia z magazynem przy użyciu dostępu do danych opartych na tożsamościach (wersja zapoznawcza)

>[!IMPORTANT]
> Funkcje przedstawione w tym artykule są w wersji zapoznawczej. Powinny one być uznawane za [eksperymentalne](/python/api/overview/azure/ml/#stable-vs-experimental) funkcje w wersji zapoznawczej, które mogą ulec zmianie w dowolnym momencie.

W tym artykule dowiesz się, jak nawiązać połączenie z usługami magazynu na platformie Azure przy użyciu dostępu do danych opartych na tożsamościach i Azure Machine Learning magazynów zasobów przy użyciu [zestawu SDK Azure Machine Learning dla języka Python](/python/api/overview/azure/ml/intro).  

Zwykle magazyny danych wykorzystują dostęp oparty na poświadczeniach, aby potwierdzić, że masz uprawnienia dostępu do usługi magazynu. Przechowują one informacje o połączeniach, takie jak identyfikator subskrypcji i autoryzacja tokenu, w [magazynie kluczy](https://azure.microsoft.com/services/key-vault/) skojarzonym z obszarem roboczym. Gdy tworzysz magazyn danych, który korzysta z dostępu opartego na tożsamościach, Twoje konto platformy Azure ([Azure Active Directory token](../active-directory/fundamentals/active-directory-whatis.md)) służy do potwierdzenia, że masz uprawnienia dostępu do usługi magazynu. W tym scenariuszu poświadczenia uwierzytelniania nie są zapisywane. Tylko informacje o koncie magazynu są przechowywane w magazynie danych. 

Aby utworzyć magazyny danych korzystające z uwierzytelniania opartego na poświadczeniach, takich jak klucze dostępu lub jednostki usługi, zobacz [nawiązywanie połączenia z usługami magazynu na platformie Azure](how-to-access-data.md).

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Dostęp do danych opartych na tożsamościach w Azure Machine Learning

Istnieją dwa scenariusze, w których można stosować dostęp do danych opartych na tożsamościach w Azure Machine Learning. Te scenariusze najlepiej pasują do dostępu opartego na tożsamości podczas pracy z danymi poufnymi i potrzebują bardziej szczegółowego zarządzania dostępem do danych:

- Uzyskiwanie dostępu do usług magazynu
- Uczenie modeli uczenia maszynowego z danymi prywatnymi

### <a name="accessing-storage-services"></a>Uzyskiwanie dostępu do usług magazynu

Można nawiązać połączenie z usługami magazynu za pośrednictwem dostępu do danych opartych na tożsamościach przy użyciu Azure Machine Learning magazynów obiektów lub [Azure Machine Learning zestawów danych](how-to-create-register-datasets.md). 

Poświadczenia uwierzytelniania są zwykle przechowywane w magazynie danych, który jest używany do upewnienia się, że masz uprawnienia dostępu do usługi magazynu. Gdy te poświadczenia są rejestrowane za pośrednictwem magazynów danych, każdy użytkownik z rolą czytelnika obszaru roboczego może je pobrać. Ta Skala dostępu może stanowić problem z zabezpieczeniami niektórych organizacji. [Dowiedz się więcej o roli czytelnik obszaru roboczego.](how-to-assign-roles.md#default-roles) 

W przypadku korzystania z dostępu do danych opartych na tożsamościach Azure Machine Learning będzie monitowany o token Azure Active Directory na potrzeby uwierzytelniania dostępu do danych, a nie do przechowywania Twoich poświadczeń w magazynie danych. Takie podejście umożliwia zarządzanie dostępem do danych na poziomie magazynu i pozostawia poufne poświadczenia. 

Takie samo zachowanie jest stosowane, gdy:

* [Utwórz zestaw danych bezpośrednio z adresów URL magazynu](#use-data-in-storage). 
* Interaktywna współpraca z danymi za pośrednictwem Jupyter Notebook na komputerze lokalnym lub [wystąpieniu obliczeniowym](concept-compute-instance.md).

> [!NOTE]
> Poświadczenia przechowywane przez uwierzytelnianie oparte na poświadczeniach obejmują identyfikatory subskrypcji, tokeny sygnatury dostępu współdzielonego (SAS) i klucz dostępu do magazynu oraz informacje o jednostce usługi, takie jak identyfikatory klientów i identyfikatory dzierżawców.

### <a name="model-training-on-private-data"></a>Modelowanie szkoleń dotyczących danych prywatnych

Niektóre scenariusze uczenia maszynowego obejmują modele szkoleniowe z danymi prywatnymi. W takich przypadkach analitykowie danych muszą uruchamiać szkoleniowe przepływy pracy bez ujawniania poufnych danych wejściowych. W tym scenariuszu zarządzana tożsamość obliczeń szkoleniowych jest używana do uwierzytelniania dostępu do danych. Takie podejście pozwala administratorom magazynu udzielić dostępu do czytnika danych obiektów blob magazynu do zarządzanej tożsamości używanej przez obliczenia szkoleniowe do uruchomienia zadania szkoleniowego. Indywidualni analitykom danych nie trzeba udzielać dostępu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie tożsamości zarządzanej w klastrze obliczeniowym](how-to-create-attach-compute-cluster.md#managed-identity).


## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

- Konto usługi Azure Storage z obsługiwanym typem magazynu. Te typy magazynów są obsługiwane w wersji zapoznawczej: 
    - [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md)
    - [Usługa Azure Data Lake Storage 1. generacji](../data-lake-store/index.yml)
    - [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)

- [Zestaw Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/install).

- Obszar roboczy usługi Azure Machine Learning.
  
  [Utwórz obszar roboczy Azure Machine Learning](how-to-manage-workspace.md) lub Użyj [istniejącego z nich za pomocą zestawu SDK języka Python](how-to-manage-workspace.md#connect-to-a-workspace). 

## <a name="storage-access-permissions"></a>Uprawnienia dostępu do magazynu

Aby zapewnić bezpieczne łączenie się z usługą magazynu na platformie Azure, Azure Machine Learning wymaga uprawnień dostępu do odpowiedniego magazynu danych.

Dostęp do danych opartych na tożsamościach obsługuje połączenia tylko z następującymi usługami magazynu:

* Azure Blob Storage
* Usługa Azure Data Lake Storage 1. generacji
* Usługa Azure Data Lake Storage 2. generacji
* Azure SQL Database

Aby uzyskać dostęp do tych usług magazynu, musisz mieć co najmniej dostęp do [czytnika danych obiektów blob magazynu](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) . Tylko właściciele konta magazynu mogą [zmieniać poziom dostępu za pośrednictwem Azure Portal](../storage/common/storage-auth-aad-rbac-portal.md).

Jeśli planujesz model na zdalnym miejscu docelowym obliczeń, tożsamość obliczeniowa musi mieć co najmniej rolę czytnika danych obiektów blob magazynu z usługi Storage. Dowiedz się, jak [skonfigurować zarządzaną tożsamość w klastrze obliczeniowym](how-to-create-attach-compute-cluster.md#managed-identity).

## <a name="work-with-virtual-networks"></a>Współpraca z sieciami wirtualnymi

Domyślnie Azure Machine Learning nie może komunikować się z kontem magazynu, które znajduje się za zaporą lub w sieci wirtualnej.

Konta magazynu można skonfigurować tak, aby zezwalały na dostęp tylko z określonych sieci wirtualnych. Ta konfiguracja wymaga wykonania dodatkowych czynności w celu zapewnienia, że dane nie są wyciekami poza siecią. Takie zachowanie jest takie samo w przypadku dostępu do danych opartych na poświadczeniach. Aby uzyskać więcej informacji, zobacz [jak skonfigurować scenariusze sieci wirtualnej](how-to-access-data.md#virtual-network). 

## <a name="create-and-register-datastores"></a>Tworzenie i rejestrowanie magazynów danych

Po zarejestrowaniu usługi magazynu na platformie Azure jako magazynu danych automatycznie utworzysz i zarejestrujesz ten magazyn danych w określonym obszarze roboczym. Zobacz [uprawnienia dostępu do magazynu](#storage-access-permissions) w celu uzyskania wskazówek dotyczących wymaganych typów uprawnień. Zobacz [Praca z sieciami wirtualnymi](#work-with-virtual-networks) , aby uzyskać szczegółowe informacje na temat łączenia się z magazynem danych za sieciami wirtualnymi.

W poniższym kodzie Zwróć uwagę na brak parametrów uwierzytelniania, takich jak `sas_token` , `account_key` , `subscription_id` i nazwy głównej usługi `client_id` . To pominięcie wskazuje, że Azure Machine Learning będzie używać dostępu do danych na podstawie tożsamości na potrzeby uwierzytelniania. Tworzenie magazynów danych zwykle odbywa się interaktywnie w notesie lub za pośrednictwem programu Studio. Token Azure Active Directory jest używany do uwierzytelniania dostępu do danych.

> [!NOTE]
> Nazwy magazynów danych powinny zawierać tylko małe litery, cyfry i znaki podkreślenia. 

### <a name="azure-blob-container"></a>Kontener obiektów blob platformy Azure

Aby zarejestrować kontener obiektów blob platformy Azure jako magazyn danych, użyj elementu [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

Poniższy kod tworzy magazyn danych `credentialless_blob` , rejestruje go w `ws` obszarze roboczym i przypisuje go do `blob_datastore` zmiennej. Ten magazyn danych uzyskuje dostęp do `my_container_name` kontenera obiektów BLOB na `my-account-name` koncie magazynu.

```Python
# Create blob datastore without credentials.
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-gen1"></a>Usługa Azure Data Lake Storage 1. generacji

Użyj [register_azure_data_lake ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) , aby zarejestrować magazyn danych, który łączy się z Azure Data Lake Storage Gen1.

Poniższy kod tworzy magazyn danych `credentialless_adls1` , rejestruje go w `workspace` obszarze roboczym i przypisuje go do `adls_dstore` zmiennej. Ten magazyn danych uzyskuje dostęp do `adls_storage` konta Azure Data Lake Storage.

```Python
# Create Azure Data Lake Storage Gen1 datastore without credentials.
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-gen2"></a>Usługa Azure Data Lake Storage 2. generacji

Użyj [register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) , aby zarejestrować magazyn danych, który łączy się z Azure Data Lake Storage Gen2.

Poniższy kod tworzy magazyn danych `credentialless_adls2` , rejestruje go w `ws` obszarze roboczym i przypisuje go do `adls2_dstore` zmiennej. Ten magazyn danych uzyskuje dostęp do systemu plików `tabular` na `myadls2` koncie magazynu.  

```python
# Create Azure Data Lake Storage Gen2 datastore without credentials.
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>Korzystanie z danych w magazynie

Zalecamy używanie [Azure Machine Learning zestawów](how-to-create-register-datasets.md) danych podczas pracy z danymi w magazynie przy użyciu Azure Machine Learning. 

Zestawy danych przepakują dane do opóźnieniemego, który umożliwia wykonywanie zadań uczenia maszynowego, takich jak szkolenie. Ponadto z zestawami danych można [pobierać lub instalować](how-to-train-with-datasets.md#mount-vs-download) pliki dowolnego formatu z usług Azure Storage, takich jak Azure Blob Storage i Azure Data Lake Storage do obiektu docelowego obliczeń.


Aby utworzyć zestawy danych z dostępem opartym na tożsamościach, dostępne są następujące opcje. Ten typ tworzenia zestawu danych używa tokenu Azure Active Directory na potrzeby uwierzytelniania dostępu do danych. 

*  Ścieżki odniesienia z magazynów danych, które również korzystają z dostępu opartego na tożsamościach. 
<br>W poniższym przykładzie `blob_datastore` już istnieje i używa dostępu do danych opartego na tożsamości.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Pomijaj tworzenie magazynu danych i twórz zbiory bezpośrednio z adresów URL magazynu. Ta funkcja obsługuje obecnie tylko obiekty blob platformy Azure i Azure Data Lake Storage Gen1 i Gen2.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

Podczas przesyłania zadania szkoleniowego, które wykorzystuje zestaw danych utworzony z dostępem do danych opartych na tożsamości, zarządzana tożsamość obliczeń szkoleniowych jest używana do uwierzytelniania dostępu do danych. Token Azure Active Directory nie jest używany. W tym scenariuszu upewnij się, że zarządzana tożsamość obliczeń jest przyznana co najmniej roli czytnika danych obiektów blob magazynu z usługi Storage. Aby uzyskać więcej informacji, zobacz [Konfigurowanie tożsamości zarządzanej w klastrach obliczeniowych](how-to-create-attach-compute-cluster.md#managed-identity). 

## <a name="next-steps"></a>Następne kroki

* [Tworzenie zestawu danych Azure Machine Learning](how-to-create-register-datasets.md)
* [Szkolenie przy użyciu zestawów danych](how-to-train-with-datasets.md)
* [Tworzenie magazynu danych z dostępem opartym na kluczach](how-to-access-data.md)
