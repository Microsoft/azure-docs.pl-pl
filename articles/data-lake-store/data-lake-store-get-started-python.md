---
title: Zarządzanie kontem Azure Data Lake Storage Gen1 przy użyciu języka Python
description: Dowiedz się, jak używać zestawu SDK języka Python do Azure Data Lake Storage Gen1 operacji zarządzania kontami.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-python
ms.openlocfilehash: 57414828394e7f241cd33b7d56190a3ce9a7eab2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87876004"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operacje zarządzania kontem na Azure Data Lake Storage Gen1 przy użyciu języka Python
> [!div class="op_single_selector"]
> * [Zestaw SDK platformy .NET](data-lake-store-get-started-net-sdk.md)
> * [Interfejs API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Dowiedz się, jak używać zestawu SDK języka Python do Azure Data Lake Storage Gen1 do wykonywania podstawowych operacji zarządzania kontami, takich jak tworzenie konta Data Lake Storage Gen1, wyświetlanie listy kont Data Lake Storage Gen1 itd. Aby uzyskać instrukcje dotyczące wykonywania operacji systemu plików na Data Lake Storage Gen1 przy użyciu języka Python, zobacz [operacje systemu plików na Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Język **Python**. Możesz pobrać środowisko Python [tutaj](https://www.python.org/downloads/). W tym artykule używany jest język Python 3.6.2.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Grupa zasobów platformy Azure**. Aby uzyskać instrukcje, zobacz [Tworzenie grupy zasobów platformy Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

## <a name="install-the-modules"></a>Instalacja modułów

Aby można było korzystać z Data Lake Storage Gen1 przy użyciu języka Python, należy zainstalować trzy moduły.

* Moduł `azure-mgmt-resource`, który obejmuje moduły platformy Azure dla usługi Active Directory itp.
* `azure-mgmt-datalake-store`Moduł, który obejmuje operacje zarządzania kontem Azure Data Lake Storage Gen1. Aby uzyskać więcej informacji na temat tego modułu, zobacz [Azure Data Lake Storage Gen1 informacje dotyczące modułu zarządzania](/python/api/azure-mgmt-datalake-store/).
* `azure-datalake-store`Moduł, który obejmuje operacje systemu plików Azure Data Lake Storage Gen1. Aby uzyskać więcej informacji na temat tego modułu, zobacz artykuł dotyczący [modułu systemu plików Azure-datalake-Store](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/).

Użyj następujących poleceń, aby zainstalować moduły.

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

1. W wybranym środowisku IDE utwórz nową aplikację w języku Python, na przykład **mysample.py**.

2. Dodaj następujący fragment kodu, aby zaimportować wymagane moduły

    ```python
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Zapisz zmiany w aplikacji mysample.py.

## <a name="authentication"></a>Uwierzytelnianie

W tej sekcji omówione zostaną różne sposoby uwierzytelniania w usłudze Azure AD. Dostępne opcje:

* W przypadku uwierzytelniania użytkowników końcowych w aplikacji zobacz [uwierzytelnianie użytkowników końcowych za pomocą Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-end-user-authenticate-python.md).
* Aby uzyskać uwierzytelnianie między usługami dla aplikacji, zobacz Uwierzytelnianie między usługami i [Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Utwórz konto klienta i Data Lake Storage Gen1

Poniższy fragment kodu tworzy najpierw klienta konta Data Lake Storage Gen1. Używa obiektu Client do tworzenia konta Data Lake Storage Gen1. Na koniec fragment kodu tworzy obiekt klienta systemu plików.

```python
## Declare variables
subscriptionId = 'FILL-IN-HERE'
adlsAccountName = 'FILL-IN-HERE'
resourceGroup = 'FILL-IN-HERE'
location = 'eastus2'

## Create Data Lake Storage Gen1 account management client object
adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

## Create a Data Lake Storage Gen1 account
adlsAcctResult = adlsAcctClient.account.create(
    resourceGroup,
    adlsAccountName,
    DataLakeStoreAccount(
        location=location
    )
).wait()
```

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Wyświetlanie listy kont Data Lake Storage Gen1

```python
## List the existing Data Lake Storage Gen1 accounts
result_list_response = adlsAcctClient.account.list()
result_list = list(result_list_response)
for items in result_list:
    print(items)
```

## <a name="delete-the-data-lake-storage-gen1-account"></a>Usuwanie konta Data Lake Storage Gen1

```python
## Delete an existing Data Lake Storage Gen1 account
adlsAcctClient.account.delete(adlsAccountName)
```
    

## <a name="next-steps"></a>Następne kroki
* [Operacje systemu plików na Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Zobacz też

* [Azure-datalake — Dokumentacja języka Python (system plików)](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core)
* [Aplikacje do obsługi dużych ilości danych open source zgodne z Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
