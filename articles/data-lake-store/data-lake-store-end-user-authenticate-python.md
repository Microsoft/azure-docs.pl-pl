---
title: Uwierzytelnianie użytkowników końcowych — Python z Data Lake Storage Gen1 platformy Azure
description: Dowiedz się, jak uzyskać uwierzytelnianie użytkowników końcowych za pomocą Azure Data Lake Storage Gen1 przy użyciu Azure Active Directory przy użyciu języka Python
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref, devx-track-python
ms.openlocfilehash: c7ee58dec4e8bc04eb07695378e0d4dc973b6705
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109089"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Uwierzytelnianie użytkowników końcowych za pomocą Azure Data Lake Storage Gen1 przy użyciu języka Python
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-end-user-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-end-user-authenticate-rest-api.md)
>
>

Ten artykuł zawiera informacje na temat używania zestawu SDK języka Python do uwierzytelniania użytkowników końcowych przy użyciu Azure Data Lake Storage Gen1. Uwierzytelnianie użytkowników końcowych można podzielić na dwie kategorie:

* Uwierzytelnianie użytkowników końcowych bez uwierzytelniania wieloskładnikowego
* Uwierzytelnianie użytkowników końcowych przy użyciu uwierzytelniania wieloskładnikowego

Obie te opcje zostały omówione w tym artykule. Aby uzyskać uwierzytelnianie między usługami i Data Lake Storage Gen1 przy użyciu języka Python, zobacz Uwierzytelnianie między usługami i [Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Język **Python**. Możesz pobrać środowisko Python [tutaj](https://www.python.org/downloads/). W tym artykule używany jest język Python 3.6.2.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Utwórz Azure Active Directory aplikację "native"**. Należy wykonać czynności opisane w temacie [uwierzytelnianie użytkowników końcowych z Data Lake Storage Gen1 przy użyciu Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalacja modułów

Aby można było korzystać z Data Lake Storage Gen1 przy użyciu języka Python, należy zainstalować trzy moduły.

* Moduł `azure-mgmt-resource`, który obejmuje moduły platformy Azure dla usługi Active Directory itp.
* `azure-mgmt-datalake-store`Moduł, który obejmuje operacje zarządzania kontem Azure Data Lake Storage Gen1. Aby uzyskać więcej informacji na temat tego modułu, zobacz [Azure Data Lake Storage Gen1 informacje dotyczące modułu zarządzania](/python/api/azure-mgmt-datalake-store/).
* `azure-datalake-store`Moduł, który obejmuje operacje systemu plików Azure Data Lake Storage Gen1. Aby uzyskać więcej informacji na temat tego modułu, zobacz artykuł dotyczący [modułu systemu plików Azure-datalake-Store](/python/api/azure-datalake-store/azure.datalake.store.core/).

Użyj następujących poleceń, aby zainstalować moduły.

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

1. W wybranym środowisku IDE Utwórz nową aplikację w języku Python, na przykład **MySample.py**.

2. Dodaj następujący fragment kodu, aby zaimportować wymagane moduły

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. Zapisz zmiany w aplikacji mysample.py.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Uwierzytelnianie użytkowników końcowych przy użyciu uwierzytelniania wieloskładnikowego

### <a name="for-account-management"></a>Zarządzanie kontami

Poniższy fragment kodu służy do uwierzytelniania w usłudze Azure AD na potrzeby operacji zarządzania kontami na koncie Data Lake Storage Gen1. Poniższego fragmentu kodu można użyć do uwierzytelniania aplikacji za pomocą uwierzytelniania wieloskładnikowego. Podaj poniższe wartości dla istniejącej aplikacji **natywnej** usługi Azure AD.

```python
authority_host_url = "https://login.microsoftonline.com"
tenant = "FILL-IN-HERE"
authority_url = authority_host_url + '/' + tenant
client_id = 'FILL-IN-HERE'
redirect = 'urn:ietf:wg:oauth:2.0:oob'
RESOURCE = 'https://management.core.windows.net/'

context = adal.AuthenticationContext(authority_url)
code = context.acquire_user_code(RESOURCE, client_id)
print(code['message'])
mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)
```

### <a name="for-filesystem-operations"></a>Dla operacji systemu plików

Służy do uwierzytelniania za pomocą usługi Azure AD dla operacji systemu plików na koncie Data Lake Storage Gen1. Poniższego fragmentu kodu można użyć do uwierzytelniania aplikacji za pomocą uwierzytelniania wieloskładnikowego. Podaj poniższe wartości dla istniejącej aplikacji **natywnej** usługi Azure AD.

```console
adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')
```

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Uwierzytelnianie użytkowników końcowych bez uwierzytelniania wieloskładnikowego

Jest to przestarzałe. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie platformy Azure przy użyciu zestawu SDK języka Python](/azure/python/python-sdk-azure-authenticate).

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób użycia uwierzytelniania użytkownika końcowego do uwierzytelniania za pomocą Azure Data Lake Storage Gen1 przy użyciu języka Python. Teraz możesz zapoznać się z następującymi artykułami, które mówią, jak używać języka Python do pracy z Azure Data Lake Storage Gen1.

* [Operacje zarządzania kontem na Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-get-started-python.md)
* [Operacje na danych na Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-data-operations-python.md)