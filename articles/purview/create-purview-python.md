---
title: 'Szybki Start: Tworzenie konta usługi kontrolą przy użyciu języka Python'
description: Utwórz konto usługi Azure kontrolą za pomocą języka Python.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: python
ms.topic: quickstart
ms.date: 04/02/2021
ms.openlocfilehash: f8ac611d25507913d6d5f2e2dd289ea52ce4ae9f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387510"
---
# <a name="quickstart-create-a-purview-account-using-python"></a>Szybki Start: Tworzenie konta usługi kontrolą przy użyciu języka Python

W tym przewodniku szybki start utworzysz konto usługi kontrolą przy użyciu języka Python. 

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Własna [dzierżawa usługi Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Twoje konto musi mieć uprawnienia do tworzenia zasobów w ramach subskrypcji

* Jeśli masz **Azure Policy** blokujące wszystkie aplikacje z tworzenia **konta magazynu** i **przestrzeni nazw EventHub**, musisz utworzyć wyjątek zasad przy użyciu tagu, który można wprowadzić podczas procesu tworzenia konta kontrolą. Głównym powodem jest to, że dla każdego utworzonego konta kontrolą należy utworzyć zarządzaną grupę zasobów i w ramach tej grupy zasobów, konto magazynu i przestrzeń nazw EventHub. Aby uzyskać więcej informacji, zobacz [Portal tworzenia katalogu](create-catalog-portal.md)


## <a name="install-the-python-package"></a>Instalacja pakietu języka Python

1. Otwórz terminal lub wiersz polecenia z uprawnieniami administratora. 
2. Najpierw zainstaluj pakiet języka Python dla zasobów zarządzania platformy Azure:

    ```python
    pip install azure-mgmt-resource
    ```
3. Aby zainstalować pakiet języka Python dla programu kontrolą, uruchom następujące polecenie:

    ```python
    pip install azure-mgmt-purview
    ```

    [Zestaw SDK języka Python dla programu kontrolą](https://github.com/Azure/azure-sdk-for-python) obsługuje języki Python 2,7, 3,3, 3,4, 3,5, 3,6 i 3,7.

4. Aby zainstalować pakiet języka Python na potrzeby uwierzytelniania tożsamości platformy Azure, uruchom następujące polecenie:

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > Pakiet "Azure-Identity" może mieć konflikt z elementem "Azure-CLI" w niektórych typowych zależnościach. Jeśli zapoznajesz się z jakimkolwiek problemem z uwierzytelnianiem, Usuń "Azure-CLI" i jego zależności lub użyj czystej maszyny bez instalowania pakietu "Azure-CLI", aby to zrobić.
    
## <a name="create-a-purview-client"></a>Tworzenie klienta kontrolą

1. Utwórz plik o nazwie **purview.py**. Dołącz poniższe instrukcje, aby dodać odwołania do przestrzeni nazw.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    ```

2. Dodaj do metody **Main** następujący kod, który tworzy wystąpienie klasy PurviewManagementClient. Ten obiekt jest używany do tworzenia konta kontrolą, usuwania konta kontrolą, sprawdzania dostępności nazwy i innych operacji dostawcy zasobów.
 
 ```python
    def main():
    
    # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Location name, where Purview account must be created.
    location = '<location name>'    

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    ```

## <a name="create-a-purview-account"></a>Utwórz konto kontrolą

Dodaj do metody **Main** następujący kod, który tworzy **konto kontrolą**. Jeśli grupa zasobów już istnieje, dodaj komentarz do pierwszej instrukcji `create_or_update`.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location =location )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", pa.name, " Id: " , pa.id ," tags: " , pa.tags)  
    except:
        print("Error")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)      
        
```

Teraz dodaj poniższą instrukcję w celu wywołania metody **Main** po uruchomieniu programu:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Pełny skrypt

Oto pełny kod języka Python:

```python
    
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    
     # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location ="southcentralus" )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", purview_name, " Id: " , pa.id ," tags: " , pa.tags) 
    except:
        print("Error in submitting job to create account")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)    

# Start the main method
main()
```

## <a name="run-the-code"></a>Uruchamianie kodu

Skompiluj i uruchom aplikację, a następnie zweryfikuj wykonywanie potoku.

Konsola wypisuje postęp tworzenia fabryki danych, połączonej usługi, zestawów danych, potoku i uruchomienia potoku. Poczekaj na wyświetlenie szczegółów uruchomienia działania kopiowania z rozmiarem odczytanych/zapisanych danych. Następnie użyj narzędzi takich jak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) , aby sprawdzić, czy obiekty blob zostały skopiowane do "outputBlobPath" z "inputBlobPath", jak określono w zmiennych.

Oto przykładowe dane wyjściowe:

```console
location: southcentralus  Purview Account Name:  purviewpython7  Id:  /subscriptions/8c2c7b23-848d-40fe-b817-690d79ad9dfd/resourceGroups/Demo_Catalog/providers/Microsoft.Purview/accounts/purviewpython7  tags:  None
Creating
Creating
Succeeded
```

## <a name="verify-the-output"></a>Sprawdzanie danych wyjściowych

Przejdź do strony **kont kontrolą** w Azure Portal i Sprawdź konto utworzone przy użyciu powyższego kodu. 

## <a name="delete-purview-account"></a>Usuń konto kontrolą

Aby usunąć konto kontrolą, Dodaj następujący kod do programu:

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## <a name="next-steps"></a>Następne kroki

Kod w tym samouczku tworzy konto kontrolą i usuwa konto kontrolą. Teraz można pobrać zestaw SDK języka Python i poznać inne akcje dostawcy zasobów, które można wykonać dla konta kontrolą.

Przejdź do następnego artykułu, aby dowiedzieć się, jak umożliwić użytkownikom dostęp do konta usługi Azure kontrolą. 

> [!div class="nextstepaction"]
> [Dodawanie użytkowników do konta usługi Azure kontrolą](catalog-permissions.md)
