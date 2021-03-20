---
title: 'Python: operacje systemu plików na Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Dowiedz się, jak używać zestawu SDK języka Python do pracy z systemem plików Data Lake Storage Gen1.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-python
ms.openlocfilehash: 8ca455a802b180163579f67104a61f455dd54f94
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92109225"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operacje systemu plików na Azure Data Lake Storage Gen1 przy użyciu języka Python
> [!div class="op_single_selector"]
> * [Zestaw SDK platformy .NET](data-lake-store-data-operations-net-sdk.md)
> * [Zestaw SDK Java](data-lake-store-get-started-java-sdk.md)
> * [Interfejs API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

W tym artykule dowiesz się, jak używać zestawu SDK języka Python do wykonywania operacji systemu plików na Azure Data Lake Storage Gen1. Aby uzyskać instrukcje dotyczące wykonywania operacji zarządzania kontem na Data Lake Storage Gen1 przy użyciu języka Python, zobacz [operacje zarządzania kontami na Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Język **Python**. Możesz pobrać środowisko Python [tutaj](https://www.python.org/downloads/). W tym artykule używany jest język Python 3.6.2.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Konto Azure Data Lake Storage Gen1**. Postępuj zgodnie z instrukcjami w punkcie wprowadzenie [do Azure Data Lake Storage Gen1 przy użyciu Azure Portal](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>Instalacja modułów

Aby można było korzystać z Data Lake Storage Gen1 przy użyciu języka Python, należy zainstalować trzy moduły.

* Moduł `azure-mgmt-resource`, który obejmuje moduły platformy Azure dla usługi Active Directory itp.
* `azure-mgmt-datalake-store`Moduł, który obejmuje operacje zarządzania kontem Azure Data Lake Storage Gen1. Aby uzyskać więcej informacji na temat tego modułu, zobacz artykuł dotyczący [zarządzania modułem Azure-zarządzanie-datalake-Store](/python/api/azure-mgmt-datalake-store/).
* `azure-datalake-store`Moduł, który obejmuje operacje systemu plików Azure Data Lake Storage Gen1. Aby uzyskać więcej informacji na temat tego modułu, zobacz artykuł [Azure-datalake-Store — dokumentacja modułu systemowego](/python/api/azure-datalake-store/azure.datalake.store.core/).

Użyj następujących poleceń, aby zainstalować moduły.

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

1. W wybranym środowisku IDE utwórz nową aplikację w języku Python, na przykład **mysample.py**.

2. Dodaj następujące wiersze, aby zaimportować wymagane moduły

   ```python
   ## Use this only for Azure AD service-to-service authentication
   from azure.common.credentials import ServicePrincipalCredentials

   ## Use this only for Azure AD end-user authentication
   from azure.common.credentials import UserPassCredentials

   ## Use this only for Azure AD multi-factor authentication
   from msrestazure.azure_active_directory import AADTokenCredentials

   ## Required for Azure Data Lake Storage Gen1 account management
   from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
   from azure.mgmt.datalake.store.models import DataLakeStoreAccount

   ## Required for Azure Data Lake Storage Gen1 filesystem management
   from azure.datalake.store import core, lib, multithread

   ## Common Azure imports
   from azure.mgmt.resource.resources import ResourceManagementClient
   from azure.mgmt.resource.resources.models import ResourceGroup

   ## Use these as needed for your application
   import logging, getpass, pprint, uuid, time
   ```

3. Zapisz zmiany w aplikacji mysample.py.

## <a name="authentication"></a>Authentication

W tej sekcji omówione zostaną różne sposoby uwierzytelniania w usłudze Azure AD. Dostępne opcje:

* W przypadku uwierzytelniania użytkowników końcowych w aplikacji zobacz [uwierzytelnianie użytkowników końcowych za pomocą Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-end-user-authenticate-python.md).
* Aby uzyskać uwierzytelnianie między usługami dla aplikacji, zobacz Uwierzytelnianie między usługami i [Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Tworzenie klienta systemu plików

Poniższy fragment kodu tworzy najpierw klienta konta Data Lake Storage Gen1. Używa obiektu Client do tworzenia konta Data Lake Storage Gen1. Na koniec fragment kodu tworzy obiekt klienta systemu plików.

```python
## Declare variables
subscriptionId = 'FILL-IN-HERE'
adlsAccountName = 'FILL-IN-HERE'

## Create a filesystem client object
adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)
```

## <a name="create-a-directory"></a>Tworzenie katalogu

```python
## Create a directory
adlsFileSystemClient.mkdir('/mysampledirectory')
```

## <a name="upload-a-file"></a>Przekazywanie pliku

```python
## Upload a file
multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)
```


## <a name="download-a-file"></a>Pobieranie pliku

```python
## Download a file
multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)
```

## <a name="delete-a-directory"></a>Usuwanie katalogu

```python
## Delete a directory
adlsFileSystemClient.rm('/mysampledirectory', recursive=True)
```

## <a name="next-steps"></a>Następne kroki
* [Operacje zarządzania kontem na Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Zobacz też

* [Informacje dotyczące Azure Data Lake Storage Gen1 języka Python (FileSystem)](/python/api/azure-datalake-store/azure.datalake.store.core)
* [Aplikacje do obsługi dużych ilości danych open source zgodne z Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)