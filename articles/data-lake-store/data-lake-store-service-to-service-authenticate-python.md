---
title: Python — uwierzytelnianie między usługami — Data Lake Storage Gen1
description: Dowiedz się, jak uzyskać uwierzytelnianie między usługami Azure Data Lake Storage Gen1 przy użyciu Azure Active Directory przy użyciu języka Python
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref, devx-track-python
ms.openlocfilehash: 95da32c6f684946bf6d394c282246f2f329b8620
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87873980"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Uwierzytelnianie między usługami Azure Data Lake Storage Gen1 przy użyciu języka Python
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

Ten artykuł zawiera informacje na temat sposobu używania zestawu SDK języka Python do uwierzytelniania między usługami przy użyciu Azure Data Lake Storage Gen1. W przypadku uwierzytelniania użytkownika końcowego za pomocą Data Lake Storage Gen1 przy użyciu języka Python zobacz [uwierzytelnianie użytkowników końcowych za pomocą Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Wymagania wstępne

* Język **Python**. Możesz pobrać środowisko Python [tutaj](https://www.python.org/downloads/). W tym artykule używany jest język Python 3.6.2.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Utwórz Azure Active Directory aplikacji "Web"**. Należy wykonać kroki opisane w temacie [uwierzytelnianie między usługami Data Lake Storage Gen1 przy użyciu Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalacja modułów

Aby można było korzystać z Data Lake Storage Gen1 przy użyciu języka Python, należy zainstalować trzy moduły.

* Moduł `azure-mgmt-resource`, który obejmuje moduły platformy Azure dla usługi Active Directory itp.
* `azure-mgmt-datalake-store`Moduł, który obejmuje operacje zarządzania kontem Data Lake Storage Gen1. Aby uzyskać więcej informacji na temat tego modułu, zobacz [Azure Data Lake Storage Gen1 informacje dotyczące modułu zarządzania](/python/api/azure-mgmt-datalake-store/).
* `azure-datalake-store`Moduł, który obejmuje operacje systemu plików Data Lake Storage Gen1. Aby uzyskać więcej informacji na temat tego modułu, zobacz artykuł dotyczący [modułu systemu plików Azure-datalake-Store](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/).

Użyj następujących poleceń, aby zainstalować moduły.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

1. W wybranym środowisku IDE utwórz nową aplikację w języku Python, na przykład **mysample.py**.

2. Dodaj następujący fragment kodu, aby zaimportować wymagane moduły

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Zapisz zmiany w aplikacji mysample.py.

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Uwierzytelnianie między usługami z kluczem tajnym klienta w celu zarządzania kontami

Ten fragment kodu służy do uwierzytelniania za pomocą usługi Azure AD na potrzeby operacji zarządzania kontami na Data Lake Storage Gen1 takich jak tworzenie konta Data Lake Storage Gen1, usuwanie konta Data Lake Storage Gen1 itd. Poniższego fragmentu kodu można użyć do uwierzytelniania aplikacji w sposób nieinteraktywny przy użyciu klucza tajnego klienta dla aplikacji/nazwy głównej usługi istniejącej aplikacji sieci Web usługi Azure AD.

```python
authority_host_uri = 'https://login.microsoftonline.com'
tenant = '<TENANT>'
authority_uri = authority_host_uri + '/' + tenant
RESOURCE = 'https://management.core.windows.net/'
client_id = '<CLIENT_ID>'
client_secret = '<CLIENT_SECRET>'

context = adal.AuthenticationContext(authority_uri, api_version=None)
mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)
```

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Uwierzytelnianie między usługami z wpisem tajnym klienta dla operacji systemu plików

Poniższy fragment kodu służy do uwierzytelniania za pomocą usługi Azure AD dla operacji na systemie plików na Data Lake Storage Gen1 takich jak tworzenie folderów, przekazywanie plików itp. Poniższego fragmentu kodu można użyć do uwierzytelniania aplikacji w sposób nieinteraktywny przy użyciu klucza tajnego klienta dla aplikacji/nazwy głównej usługi. Użyj tej metody wraz z istniejącą aplikacją internetową usługi Azure AD.

```python
tenant = '<TENANT>'
RESOURCE = 'https://datalake.azure.net/'
client_id = '<CLIENT_ID>'
client_secret = '<CLIENT_SECRET>'

adlCreds = lib.auth(tenant_id = tenant,
                client_secret = client_secret,
                client_id = client_id,
                resource = RESOURCE)
```

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Storage Gen1 such as create a Data Lake Storage Gen1 account, delete a Data Lake Storage Gen1 account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id) -->

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób użycia uwierzytelniania między usługami w celu uwierzytelniania za pomocą Data Lake Storage Gen1 przy użyciu języka Python. Teraz możesz zapoznać się z następującymi artykułami, które mówią, jak używać języka Python do pracy z Data Lake Storage Gen1.

* [Operacje zarządzania kontem na Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-get-started-python.md)
* [Operacje na danych na Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-data-operations-python.md)
