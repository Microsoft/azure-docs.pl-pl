---
title: Azure Data Lake Storage Gen2 zestawu SDK języka Python dla plików & list ACL
description: Użyj języka Python zarządzanie katalogami oraz list kontroli dostępu do plików i katalogów (ACL) na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).
author: normesta
ms.service: storage
ms.date: 09/10/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: 7bbdf7961a934245b71829b7b50fc62c5b069d6b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913287"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Używanie języka Python do zarządzania katalogami, plikami i listami ACL w Azure Data Lake Storage Gen2

W tym artykule pokazano, jak używać języka Python do tworzenia katalogów, plików i uprawnień w ramach kont magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS) i zarządzać nimi. 

[Pakiet (indeks pakietu języka Python)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [Dokumentacja](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  interfejsu API Mapowanie Gen1 do [Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Przekaż opinię](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Konto magazynu z włączoną hierarchiczną przestrzenią nazw (SNS). Postępuj zgodnie z [tymi](../common/storage-account-create.md) instrukcjami, aby je utworzyć.

## <a name="set-up-your-project"></a>konfigurowanie projektu

Zainstaluj Azure Data Lake Storageą bibliotekę kliencką dla języka Python za pomocą narzędzia [PIP](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Dodaj te instrukcje importu na początku pliku kodu.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Połącz z kontem

Aby użyć fragmentów kodu w tym artykule, należy utworzyć wystąpienie **DataLakeServiceClient** reprezentujące konto magazynu. 

### <a name="connect-by-using-an-account-key"></a>Nawiązywanie połączenia przy użyciu klucza konta

Jest to najprostszy sposób nawiązywania połączenia z kontem. 

Ten przykład tworzy wystąpienie **DataLakeServiceClient** przy użyciu klucza konta.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Zastąp wartość symbolu zastępczego `storage_account_name` nazwą konta magazynu.

- Zastąp `storage_account_key` wartość symbolu zastępczego kluczem dostępu do konta magazynu.

### <a name="connect-by-using-azure-active-directory-ad"></a>Łączenie przy użyciu Azure Active Directory (AD)

Aby uwierzytelnić aplikację w usłudze Azure AD, możesz użyć [biblioteki klienta tożsamości platformy Azure dla języka Python](https://pypi.org/project/azure-identity/) .

Ten przykład tworzy wystąpienie **DataLakeServiceClient** przy użyciu identyfikatora klienta, klucza tajnego klienta i identyfikatora dzierżawy.  Aby uzyskać te wartości, zobacz [pozyskiwanie tokenu z usługi Azure AD w celu autoryzowania żądań z aplikacji klienckiej](../common/storage-auth-aad-app.md).

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Aby uzyskać więcej przykładów, zobacz [Biblioteka klienta tożsamości platformy Azure dla](https://pypi.org/project/azure-identity/) dokumentacji języka Python.

## <a name="create-a-container"></a>Tworzenie kontenera

Kontener działa jako system plików dla plików. Można utworzyć jeden, wywołując metodę **FileSystemDataLakeServiceClient.create_file_system** .

Ten przykład tworzy kontener o nazwie `my-file-system` .

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz odwołanie do katalogu, wywołując metodę **FileSystemClient.create_directory** .

Ten przykład dodaje katalog o nazwie `my-directory` do kontenera. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>Zmienianie nazwy lub przenoszenie katalogu

Zmień nazwę lub Przenieś katalog, wywołując metodę **DataLakeDirectoryClient.rename_directory** . Przekaż ścieżkę do żądanego katalogu jako parametr. 

Ten przykład zmienia nazwę podkatalogu na nazwę `my-subdirectory-renamed` .

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>Usuwanie katalogu

Usuń katalog, wywołując metodę **DataLakeDirectoryClient.delete_directory** .

Ten przykład usuwa katalog o nazwie `my-directory` .  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```


## <a name="upload-a-file-to-a-directory"></a>Przekaż plik do katalogu 

Najpierw Utwórz odwołanie do pliku w katalogu docelowym, tworząc wystąpienie klasy **DataLakeFileClient** . Przekaż plik, wywołując metodę **DataLakeFileClient.append_data** . Upewnij się, że ukończono przekazywanie, wywołując metodę **DataLakeFileClient.flush_data** .

Ten przykład przekazuje plik tekstowy do katalogu o nazwie `my-directory` .   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'rb')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

> [!TIP]
> Jeśli rozmiar pliku jest duży, kod będzie musiał wykonać wiele wywołań metody **DataLakeFileClient.append_data** . Zamiast tego Rozważ użycie metody **DataLakeFileClient.upload_data** . Dzięki temu można przekazać cały plik w jednym wywołaniu. 

## <a name="upload-a-large-file-to-a-directory"></a>Przekaż duży plik do katalogu

Użyj metody **DataLakeFileClient.upload_data** , aby przekazać duże pliki bez konieczności wykonywania wielu wywołań do metody **DataLakeFileClient.append_data** .

```python
def upload_file_to_directory_bulk():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        local_file = open("C:\\file-to-upload.txt",'rb')

        file_contents = local_file.read()

        file_client.upload_data(file_contents, overwrite=True)

    except Exception as e:
      print(e) 
```

## <a name="download-from-a-directory"></a>Pobierz z katalogu 

Otwórz plik lokalny do zapisu. Następnie Utwórz wystąpienie **DataLakeFileClient** , które reprezentuje plik, który chcesz pobrać. Wywołaj **DataLakeFileClient.read_file** , aby odczytać bajty z pliku, a następnie zapisać te bajty do pliku lokalnego. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        download = file_client.download_file()

        downloaded_bytes = download.readall()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

Wyświetlanie zawartości katalogu przez wywołanie metody **FileSystemClient.get_paths** , a następnie Wyliczenie przez wyniki.

W tym przykładzie program drukuje ścieżkę każdego podkatalogu i pliku, który znajduje się w katalogu o nazwie `my-directory` .

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="manage-access-control-lists-acls"></a>Zarządzanie listami kontroli dostępu (ACL)

Uprawnienia dostępu do katalogów i plików można uzyskiwać, ustawiać i aktualizować.

> [!NOTE]
> Jeśli używasz usługi Azure Active Directory (Azure AD) do autoryzacji dostępu, upewnij się, że podmiot zabezpieczeń ma przypisaną [rolę właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Aby dowiedzieć się więcej na temat sposobu stosowania uprawnień ACL i skutków ich zmiany, zobacz  [Kontrola dostępu w Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="manage-directory-acls"></a>Zarządzanie listami ACL katalogów

Pobierz listę kontroli dostępu (ACL) katalogu, wywołując metodę **DataLakeDirectoryClient.get_access_control** i ustawiając listę ACL, wywołując metodę **DataLakeDirectoryClient.set_access_control** .

> [!NOTE]
> Jeśli aplikacja autoryzuje dostęp przy użyciu Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń używany przez aplikację do autoryzacji dostępu ma przypisaną [rolę właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Aby dowiedzieć się więcej na temat sposobu stosowania uprawnień ACL i skutków ich zmiany, zobacz  [Kontrola dostępu w Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

Ten przykład pobiera i ustawia listę ACL katalogu o nazwie `my-directory` . Ciąg `rwxr-xrw-` przyznaje uprawnienia Odczyt, zapis i wykonywanie przez użytkownika, który ma grupę będącą właścicielem, ma uprawnienia tylko do odczytu i wykonania, i daje wszystkim innym uprawnienie do odczytu i zapisu.

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

Możesz również uzyskać i ustawić listę kontroli dostępu dla katalogu głównego kontenera. Aby uzyskać katalog główny, wywołaj metodę **FileSystemClient._get_root_directory_client** .

### <a name="manage-file-permissions"></a>Zarządzanie uprawnieniami plików

Pobierz listę kontroli dostępu (ACL) pliku, wywołując metodę **DataLakeFileClient.get_access_control** i ustawiając listę ACL, wywołując metodę **DataLakeFileClient.set_access_control** .

> [!NOTE]
> Jeśli aplikacja autoryzuje dostęp przy użyciu Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń używany przez aplikację do autoryzacji dostępu ma przypisaną [rolę właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Aby dowiedzieć się więcej na temat sposobu stosowania uprawnień ACL i skutków ich zmiany, zobacz  [Kontrola dostępu w Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

Ten przykład pobiera i ustawia listę ACL pliku o nazwie `my-file.txt` . Ciąg `rwxr-xrw-` przyznaje uprawnienia Odczyt, zapis i wykonywanie przez użytkownika, który ma grupę będącą właścicielem, ma uprawnienia tylko do odczytu i wykonania, i daje wszystkim innym uprawnienie do odczytu i zapisu.

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

### <a name="set-an-acl-recursively"></a>Ustawianie listy ACL rekursywnie

Można dodawać, aktualizować i usuwać listy ACL cyklicznie na istniejących elementach podrzędnych katalogu nadrzędnego bez konieczności wprowadzania tych zmian indywidualnie dla każdego elementu podrzędnego. Więcej informacji można znaleźć w sekcji [Ustawianie list kontroli dostępu (ACL) rekursywnie dla Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

## <a name="see-also"></a>Zobacz też

* [Dokumentacja referencyjna interfejsu API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
* [Pakiet (indeks pakietu języka Python)](https://pypi.org/project/azure-storage-file-datalake/)
* [Samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Mapowanie Gen1 do Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Przekaż opinię](https://github.com/Azure/azure-sdk-for-python/issues)