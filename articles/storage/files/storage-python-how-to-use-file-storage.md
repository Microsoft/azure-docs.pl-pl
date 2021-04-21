---
title: Programowanie dla Azure Files za pomocą języka Python | Microsoft Docs
description: Dowiedz się, jak opracowywać aplikacje i usługi języka Python, które Azure Files do przechowywania danych plików.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/08/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: 8739bfaf1a41758ef3267c71cba883ef2445c39d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817796"
---
# <a name="develop-for-azure-files-with-python"></a>Develop for Azure Files with Python (Tworzenie oprogramowania dla usługi Azure Files przy użyciu języka Python)

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Poznaj podstawy używania języka Python do tworzenia aplikacji lub usług, które używają Azure Files do przechowywania danych plików. Utwórz prostą aplikację konsoli i dowiedz się, jak wykonywać podstawowe akcje za pomocą języka Python i języka Azure Files:

- Tworzenie udziałów plików platformy Azure
- Tworzenie katalogów
- Wyliczanie plików i katalogów w udziałach plików platformy Azure
- Przekazywanie, pobieranie i usuwanie pliku
- Tworzenie kopii zapasowych udziałów plików przy użyciu migawek

> [!NOTE]
> Ponieważ Azure Files są dostępne za pośrednictwem SMB, istnieje możliwość pisania prostych aplikacji, które mają dostęp do udziału plików platformy Azure przy użyciu standardowych klas i funkcji we/wy języka Python. W tym artykule opisano sposób pisania aplikacji, które używają zestawu SDK języka Python dla usługi Azure Files Storage, który używa interfejsu [API REST](/rest/api/storageservices/file-service-rest-api) usługi Azure Files do Azure Files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Pobieranie i instalowanie zestawu SDK usługi Azure Storage dla języka Python

> [!NOTE]
> Jeśli uaktualniasz zestaw SDK usługi Azure Storage dla języka Python w wersji 0.36 lub starszej, odinstaluj starszy zestaw SDK przy użyciu narzędzia przed `pip uninstall azure-storage` zainstalowaniem najnowszego pakietu.

# <a name="azure-python-sdk-v12"></a>[Zestaw Azure Python SDK w wersji 12](#tab/python)

Biblioteka [klienta usługi Azure File Storage w wersji 12.x](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share) dla języka Python wymaga języka Python w wersji 2.7 lub 3.6 lub wersji 3.6 lub więcej.

# <a name="azure-python-sdk-v2"></a>[Zestaw Azure Python SDK w wersji 2](#tab/python2)

Zestaw [SDK usługi Azure Storage dla języka Python](https://github.com/azure/azure-storage-python) wymaga języka Python w wersji 2.7 lub 3.6 lub wersji 6 lub więcej.

---

## <a name="install-via-pypi"></a>Instalowanie za pośrednictwem interfejsu PyPI

Aby zainstalować za pośrednictwem PyPI (Python Package Index), wpisz:

# <a name="azure-python-sdk-v12"></a>[Zestaw Azure Python SDK w wersji 12](#tab/python)

```console
pip install azure-storage-file-share
```

# <a name="azure-python-sdk-v2"></a>[Zestaw Azure Python SDK w wersji 2](#tab/python2)

```console
pip install azure-storage-file
```

### <a name="view-the-sample-application"></a>Wyświetlanie przykładowej aplikacji

Aby wyświetlić i uruchomić przykładową aplikację, która pokazuje, jak używać języka Python z usługą Azure Files, zobacz [Azure Storage: Wprowadzenie with Azure Files in Python (Usługa Azure Storage:](https://github.com/Azure-Samples/storage-file-python-getting-started)Wprowadzenie z Azure Files języku Python).

Aby uruchomić przykładową aplikację, upewnij się, że zainstalowano pakiety `azure-storage-file` i `azure-storage-common` .

---

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurowanie aplikacji do korzystania z Azure Files

Dodaj następujący kod w górnej części pliku źródłowego języka Python, aby użyć fragmentów kodu w tym artykule.

# <a name="azure-python-sdk-v12"></a>[Zestaw Azure Python SDK w wersji 12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_Imports":::

# <a name="azure-python-sdk-v2"></a>[Zestaw Azure Python SDK w wersji 2](#tab/python2)

```python
from azure.storage.file import FileService
```

---

## <a name="set-up-a-connection-to-azure-files"></a>Konfigurowanie połączenia z usługą Azure Files

# <a name="azure-python-sdk-v12"></a>[Zestaw Azure Python SDK w wersji 12](#tab/python)

[ShareServiceClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareserviceclient) umożliwia pracę z udziałami, katalogami i plikami. Poniższy kod tworzy obiekt `ShareServiceClient` przy użyciu parametrów połączenia konta magazynu.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateShareServiceClient":::

# <a name="azure-python-sdk-v2"></a>[Zestaw Azure Python SDK w wersji 2](#tab/python2)

Obiekt [FileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) umożliwia pracę z udziałami, katalogami i plikami. Poniższy kod tworzy obiekt `FileService` przy użyciu nazwy konta magazynu i klucza konta. Zastąp wartości `<myaccount>` i `<mykey>` nazwą i kluczem konta.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

---

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure

# <a name="azure-python-sdk-v12"></a>[Zestaw Azure Python SDK w wersji 12](#tab/python)

Poniższy przykład kodu używa obiektu [ShareClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient) do utworzenia udziału, jeśli nie istnieje.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateFileShare":::

# <a name="azure-python-sdk-v2"></a>[Zestaw Azure Python SDK w wersji 2](#tab/python2)

Poniższy przykład kodu używa obiektu [FileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) do utworzenia udziału, jeśli nie istnieje.

```python
file_service.create_share('myshare')
```

---

## <a name="create-a-directory"></a>Tworzenie katalogu

Magazyn można organizować, umieszczając pliki w podkatalogach, zamiast umieszczać je wszystkie w katalogu głównym.

# <a name="azure-python-sdk-v12"></a>[Zestaw Azure Python SDK w wersji 12](#tab/python)

Następująca metoda tworzy katalog w katalogu głównym określonego udziału plików przy użyciu [obiektu ShareDirectoryClient.](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharedirectoryclient)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateDirectory":::

# <a name="azure-python-sdk-v2"></a>[Zestaw Azure Python SDK w wersji 2](#tab/python2)

Poniższy kod utworzy podkatalog o nazwie *sampledir* w katalogu głównym.

```python
file_service.create_directory('myshare', 'sampledir')
```

---

## <a name="upload-a-file"></a>Przekazywanie pliku

W tej sekcji dowiesz się, jak przekazać plik z magazynu lokalnego do usługi Azure File Storage.

# <a name="azure-python-sdk-v12"></a>[Zestaw Azure Python SDK w wersji 12](#tab/python)

Następująca metoda służy do przekazywania zawartości określonego pliku do określonego katalogu w określonym udziału plików platformy Azure.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_UploadFile":::

# <a name="azure-python-sdk-v2"></a>[Zestaw Azure Python SDK w wersji 2](#tab/python2)

Udział plików platformy Azure zawiera co najmniej katalog główny, w którym mogą znajdować się pliki. Aby utworzyć plik i przekazać dane, użyj metod [create_file_from_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-path-share-name--directory-name--file-name--local-file-path--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object---timeout-none-), [create_file_from_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-stream-share-name--directory-name--file-name--stream--count--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--), [create_file_from_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-bytes-share-name--directory-name--file-name--file--index-0--count-none--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--)lub [create_file_from_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-text-share-name--directory-name--file-name--text--encoding--utf-8---content-settings-none--metadata-none--validate-content-false--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--) . Są to metody wysokiego poziomu, które wykonują niezbędne fragmenty, gdy rozmiar danych przekracza 64 MB.

`create_file_from_path` Program przekaże zawartość pliku z określonej ścieżki i przekaże zawartość `create_file_from_stream` z już otwartego pliku/strumienia. `create_file_from_bytes` Program przekaże tablicę bajtów i przekaże określoną wartość tekstową przy użyciu określonego kodowania (wartość domyślna `create_file_from_text` to UTF-8).

W poniższym przykładzie zawartość pliku *sunset.png* do **pliku myfile.**

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this file in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Wyliczanie plików i katalogów w udziałach plików platformy Azure

# <a name="azure-python-sdk-v12"></a>[Zestaw Azure Python SDK w wersji 12](#tab/python)

Aby wyświetlić listę plików i katalogów w podkatalogu, użyj [list_directories_and_files](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#list-directories-and-files-directory-name-none--name-starts-with-none--marker-none----kwargs-) metody . Ta metoda zwraca iterowalny automatyczny podział na strony. Poniższy kod wyprowadza nazwę **każdego pliku** i podkatalogu w określonym katalogu do konsoli.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListFilesAndDirs":::

# <a name="azure-python-sdk-v2"></a>[Zestaw Azure Python SDK w wersji 2](#tab/python2)

Aby wyświetlić listę plików i katalogów w udziału, użyj [list_directories_and_files](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#list-directories-and-files-share-name--directory-name-none--num-results-none--marker-none--timeout-none--prefix-none--snapshot-none-) metody . Ta metoda zwraca generator. Poniższy kod wyprowadza nazwę **każdego** pliku i katalogu w udziału do konsoli programu .

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

---

## <a name="download-a-file"></a>Pobieranie pliku

# <a name="azure-python-sdk-v12"></a>[Zestaw Azure Python SDK w wersji 12](#tab/python)

Aby pobrać dane z pliku, użyj [download_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#download-file-offset-none--length-none----kwargs-).

W poniższym przykładzie pokazano użycie funkcji , aby pobrać zawartość określonego pliku i zapisać ją lokalnie za pomocą pobranego pliku , który jest dołączany `download_file` do nazwy pliku. 

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadFile":::

# <a name="azure-python-sdk-v2"></a>[Zestaw Azure Python SDK w wersji 2](#tab/python2)

Aby pobrać dane z pliku, użyj get_file_to_path [,](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-path-share-name--directory-name--file-name--file-path--open-mode--wb---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-) [get_file_to_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-stream-share-name--directory-name--file-name--stream--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-bytes-share-name--directory-name--file-name--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)lub [get_file_to_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-text-share-name--directory-name--file-name--encoding--utf-8---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-). Są to metody wysokiego poziomu, które wykonują niezbędne fragmenty, gdy rozmiar danych przekracza 64 MB.

W poniższym przykładzie pokazano używanie narzędzia do pobierania zawartości pliku myfile i przechowywania jej w `get_file_to_path` *out-sunset.png* pliku. 

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

---

## <a name="create-a-share-snapshot"></a>Tworzenie migawki udziału

Możesz utworzyć kopię całego udziału plików w czasie.

# <a name="azure-python-sdk-v12"></a>[Zestaw Azure Python SDK w wersji 12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateSnapshot":::

# <a name="azure-python-sdk-v2"></a>[Zestaw Azure Python SDK w wersji 2](#tab/python2)

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Tworzenie migawki udziału z metadanymi**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

---

## <a name="list-shares-and-snapshots"></a>Tworzenie listy udziałów i migawek

Możesz wyświetlić listę wszystkich migawek dla określonego udziału.

# <a name="azure-python-sdk-v12"></a>[Zestaw Azure Python SDK w wersji 12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListSharesAndSnapshots":::

# <a name="azure-python-sdk-v2"></a>[Zestaw Azure Python SDK w wersji 2](#tab/python2)

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

---

## <a name="browse-share-snapshot"></a>Przeglądanie migawki udziału

Każdą migawkę udziału można przeglądać w celu pobrania plików i katalogów od tego momentu.

# <a name="azure-python-sdk-v12"></a>[Zestaw Azure Python SDK w wersji 12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_BrowseSnapshotDir":::

# <a name="azure-python-sdk-v2"></a>[Zestaw Azure Python SDK w wersji 2](#tab/python2)

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

---

## <a name="get-file-from-share-snapshot"></a>Uzyskiwanie pliku z migawki udziału

Plik można pobrać z migawki udziału. Dzięki temu można przywrócić poprzednią wersję pliku.

# <a name="azure-python-sdk-v12"></a>[Zestaw Azure Python SDK w wersji 12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadSnapshotFile":::

# <a name="azure-python-sdk-v2"></a>[Zestaw Azure Python SDK w wersji 2](#tab/python2)

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

---

## <a name="delete-a-single-share-snapshot"></a>Usuwanie migawki pojedynczego udziału
Możesz usunąć migawkę pojedynczego udziału.

# <a name="azure-python-sdk-v12"></a>[Zestaw Azure Python SDK w wersji 12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteSnapshot":::

# <a name="azure-python-sdk-v2"></a>[Zestaw Azure Python SDK w wersji 2](#tab/python2)

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

---

## <a name="delete-a-file"></a>Usuwanie pliku

# <a name="azure-python-sdk-v12"></a>[Zestaw Azure Python SDK w wersji 12](#tab/python)

Aby usunąć plik, [wywołaj](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#delete-file---kwargs-)delete_file .

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteFile":::

# <a name="azure-python-sdk-v2"></a>[Zestaw Azure Python SDK w wersji 2](#tab/python2)

Aby usunąć plik, [wywołaj](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#delete-file-share-name--directory-name--file-name--timeout-none-)delete_file .

```python
file_service.delete_file('myshare', None, 'myfile')
```

---

## <a name="delete-share-when-share-snapshots-exist"></a>Usuwanie udziału, gdy istnieją migawki udziałów

# <a name="azure-python-sdk-v12"></a>[Zestaw Azure Python SDK w wersji 12](#tab/python)

Aby usunąć udział zawierający migawki, [wywołaj](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#delete-share-delete-snapshots-false----kwargs-) delete_share za pomocą . `delete_snapshots=True`

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteShare":::

# <a name="azure-python-sdk-v2"></a>[Zestaw Azure Python SDK w wersji 2](#tab/python2)

Udziału zawierającego migawki nie można usunąć, chyba że wszystkie migawki zostaną najpierw usunięte.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

---

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak manipulować Azure Files języka Python, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

- [Centrum deweloperów języka Python](/azure/developer/python/)
- [Interfejs API REST usług Azure Storage](/rest/api/azure/)
- [Microsoft Azure Storage SDK dla języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
