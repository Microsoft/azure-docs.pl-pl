---
title: Programowanie dla Azure Files przy użyciu języka Python | Microsoft Docs
description: Dowiedz się, jak opracowywać aplikacje i usługi w języku Python, które używają Azure Files do przechowywania danych plików.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/08/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: 8bef69037fad8bf8ee9537e90f26ca967560b9d2
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876101"
---
# <a name="develop-for-azure-files-with-python"></a>Develop for Azure Files with Python (Tworzenie oprogramowania dla usługi Azure Files przy użyciu języka Python)

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Poznaj podstawy używania języka Python do tworzenia aplikacji lub usług, które używają Azure Files do przechowywania danych plików. Utwórz prostą aplikację konsolową i Dowiedz się, jak wykonywać podstawowe działania za pomocą języka Python i Azure Files:

- Tworzenie udziałów plików platformy Azure
- Tworzenie katalogów
- Wyliczanie plików i katalogów w udziale plików platformy Azure
- Przekazywanie, pobieranie i usuwanie pliku
- Tworzenie kopii zapasowych udziałów plików za pomocą migawek

> [!NOTE]
> Ponieważ do Azure Files można uzyskać dostęp za pośrednictwem protokołu SMB, można napisać proste aplikacje, które uzyskują dostęp do udziału plików platformy Azure przy użyciu standardowych klas i funkcji we/wy języka Python. W tym artykule opisano sposób pisania aplikacji korzystających z zestawu SDK języka Python Azure Files Storage, który używa [interfejsu API REST Azure Files](/rest/api/storageservices/file-service-rest-api) do komunikowania się z Azure Files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Pobierz i Zainstaluj zestaw SDK usługi Azure Storage dla języka Python

> [!NOTE]
> Jeśli uaktualniasz z zestawu SDK usługi Azure Storage dla języka Python w wersji 0,36 lub starszej, Odinstaluj starszy zestaw SDK przy użyciu programu `pip uninstall azure-storage` przed zainstalowaniem najnowszego pakietu.

# <a name="python-v12"></a>[V12 Python](#tab/python)

[Biblioteka klienta usługi Azure File Storage V12. x dla języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share) wymaga języka Python 2,7 lub 3,5.

# <a name="python-v2"></a>[Python v2](#tab/python2)

[Zestaw SDK usługi Azure Storage dla języka Python](https://github.com/azure/azure-storage-python) wymaga języka Python 2,7, 3,3, 3,4, 3,5 lub 3,6.

---

## <a name="install-via-pypi"></a>Zainstaluj za pomocą PyPI

Aby zainstalować program za pośrednictwem indeksu pakietów języka Python (PyPI), wpisz:

# <a name="python-v12"></a>[V12 Python](#tab/python)

```console
pip install azure-storage-file-share
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-file
```

### <a name="view-the-sample-application"></a>Wyświetlanie przykładowej aplikacji

Aby wyświetlić i uruchomić przykładową aplikację, która pokazuje, jak używać języka Python z Azure Files, zobacz [Azure Storage: wprowadzenie z Azure Files w języku Python](https://github.com/Azure-Samples/storage-file-python-getting-started).

Aby uruchomić przykładową aplikację, upewnij się, że zainstalowano `azure-storage-file` `azure-storage-common` pakiety i.

---

## <a name="set-up-your-application-to-use-azure-files"></a>Skonfiguruj aplikację do używania Azure Files

Dodaj następujący element w górnej części pliku źródłowego języka Python, aby użyć fragmentów kodu w tym artykule.

# <a name="python-v12"></a>[V12 Python](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_Imports":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
from azure.storage.file import FileService
```

---

## <a name="set-up-a-connection-to-azure-files"></a>Skonfiguruj połączenie z usługą Azure Files

# <a name="python-v12"></a>[V12 Python](#tab/python)

[ShareServiceClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareserviceclient) umożliwia korzystanie z udziałów, katalogów i plików. Poniższy kod tworzy `ShareServiceClient` Obiekt przy użyciu parametrów połączenia konta magazynu.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateShareServiceClient":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Obiekt [FileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) umożliwia korzystanie z udziałów, katalogów i plików. Poniższy kod tworzy `FileService` Obiekt przy użyciu nazwy konta magazynu i klucza konta. Zastąp wartości `<myaccount>` i `<mykey>` nazwą i kluczem konta.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

---

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure

# <a name="python-v12"></a>[V12 Python](#tab/python)

Poniższy przykład kodu używa obiektu [ShareClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient) , aby utworzyć udział, jeśli nie istnieje.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateFileShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Poniższy przykład kodu używa obiektu [FileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) , aby utworzyć udział, jeśli nie istnieje.

```python
file_service.create_share('myshare')
```

---

## <a name="create-a-directory"></a>Tworzenie katalogu

Magazyn można organizować przez umieszczenie plików wewnątrz podkatalogów zamiast wszystkich z nich w katalogu głównym.

# <a name="python-v12"></a>[V12 Python](#tab/python)

Poniższa metoda tworzy katalog w katalogu głównym określonego udziału plików przy użyciu obiektu [ShareDirectoryClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharedirectoryclient) .

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateDirectory":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Poniższy kod utworzy podkatalog o nazwie *sampledir* w katalogu głównym.

```python
file_service.create_directory('myshare', 'sampledir')
```

---

## <a name="upload-a-file"></a>Przekazywanie pliku

W tej sekcji dowiesz się, jak przekazać plik z magazynu lokalnego do usługi Azure File Storage.

# <a name="python-v12"></a>[V12 Python](#tab/python)

Poniższa metoda przekazuje zawartość określonego pliku do określonego katalogu w określonym udziale plików platformy Azure.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_UploadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Udział plików platformy Azure zawiera co najmniej katalog główny, w którym znajdują się pliki. Aby utworzyć plik i przekazać dane, użyj metod [create_file_from_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-path-share-name--directory-name--file-name--local-file-path--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object---timeout-none-), [create_file_from_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-stream-share-name--directory-name--file-name--stream--count--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--), [create_file_from_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-bytes-share-name--directory-name--file-name--file--index-0--count-none--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--)lub [create_file_from_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-text-share-name--directory-name--file-name--text--encoding--utf-8---content-settings-none--metadata-none--validate-content-false--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--) . Są to metody wysokiego poziomu, które wykonują niezbędne fragmenty, gdy rozmiar danych przekracza 64 MB.

`create_file_from_path` przekazuje zawartość pliku z określonej ścieżki i `create_file_from_stream` przekazuje zawartość z już otwartego pliku/strumienia. `create_file_from_bytes` przekazuje tablicę bajtów i `create_file_from_text` przekazuje określoną wartość tekstową przy użyciu określonego kodowania (wartość domyślna to UTF-8).

Poniższy przykład przekazuje zawartość pliku *sunset.png* do **pliku z plikami** .

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Wyliczanie plików i katalogów w udziale plików platformy Azure

# <a name="python-v12"></a>[V12 Python](#tab/python)

Aby wyświetlić listę plików i katalogów w podkatalogu, użyj metody [list_directories_and_files](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#list-directories-and-files-directory-name-none--name-starts-with-none--marker-none----kwargs-) . Ta metoda zwraca funkcję autostronicowania. Poniższy kod wyprowadza **nazwy** poszczególnych plików i podkatalogów w określonym katalogu do konsoli programu.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListFilesAndDirs":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Aby wyświetlić listę plików i katalogów w udziale, użyj metody [list_directories_and_files](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#list-directories-and-files-share-name--directory-name-none--num-results-none--marker-none--timeout-none--prefix-none--snapshot-none-) . Ta metoda zwraca generator. Poniższy kod wyprowadza **nazwy** poszczególnych plików i katalogów w udziale do konsoli programu.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

---

## <a name="download-a-file"></a>Pobieranie pliku

# <a name="python-v12"></a>[V12 Python](#tab/python)

Aby pobrać dane z pliku, użyj [download_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#download-file-offset-none--length-none----kwargs-).

Poniższy przykład ilustruje użycie `download_file` w celu pobrania zawartości określonego pliku i zapisania jej lokalnie z **pobranym** parametrem z prefiksem.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Aby pobrać dane z pliku, należy użyć [get_file_to_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-path-share-name--directory-name--file-name--file-path--open-mode--wb---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-stream-share-name--directory-name--file-name--stream--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-bytes-share-name--directory-name--file-name--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)lub [get_file_to_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-text-share-name--directory-name--file-name--encoding--utf-8---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-). Są to metody wysokiego poziomu, które wykonują niezbędne fragmenty, gdy rozmiar danych przekracza 64 MB.

W poniższym przykładzie pokazano, `get_file_to_path` jak pobrać zawartość pliku mój **plik** i zapisać go w pliku *out-sunset.png* .

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

---

## <a name="create-a-share-snapshot"></a>Tworzenie migawki udziału

Można utworzyć kopię całego udziału plików w czasie.

# <a name="python-v12"></a>[V12 Python](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Utwórz migawkę udziału z metadanymi**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

---

## <a name="list-shares-and-snapshots"></a>Wyświetl listę udziałów i migawek

Można wyświetlić listę wszystkich migawek dla określonego udziału.

# <a name="python-v12"></a>[V12 Python](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListSharesAndSnapshots":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

---

## <a name="browse-share-snapshot"></a>Przeglądaj migawkę udziału

Można przeglądać poszczególne migawki udziałów, aby pobierać pliki i katalogi z tego punktu w czasie.

# <a name="python-v12"></a>[V12 Python](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_BrowseSnapshotDir":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

---

## <a name="get-file-from-share-snapshot"></a>Pobierz plik z migawki udziału

Plik można pobrać z migawki udziału. Dzięki temu można przywrócić poprzednią wersję pliku.

# <a name="python-v12"></a>[V12 Python](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadSnapshotFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

---

## <a name="delete-a-single-share-snapshot"></a>Usuń migawkę pojedynczego udziału
Można usunąć migawkę pojedynczego udziału.

# <a name="python-v12"></a>[V12 Python](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

---

## <a name="delete-a-file"></a>Usuwanie pliku

# <a name="python-v12"></a>[V12 Python](#tab/python)

Aby usunąć plik, wywołaj [delete_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#delete-file---kwargs-).

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Aby usunąć plik, wywołaj [delete_file](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#delete-file-share-name--directory-name--file-name--timeout-none-).

```python
file_service.delete_file('myshare', None, 'myfile')
```

---

## <a name="delete-share-when-share-snapshots-exist"></a>Usuń udział w przypadku istnienia migawek udziałów

# <a name="python-v12"></a>[V12 Python](#tab/python)

Aby usunąć udział zawierający migawki, wywołaj [delete_share](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#delete-share-delete-snapshots-false----kwargs-) z `delete_snapshots=True` .

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Nie można usunąć udziału zawierającego migawki, chyba że wszystkie migawki zostaną usunięte jako pierwsze.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

---

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak manipulować Azure Files przy użyciu języka Python, Skorzystaj z poniższych linków, aby dowiedzieć się więcej.

- [Centrum deweloperów języka Python](/azure/developer/python/)
- [Interfejs API REST usług Azure Storage](/rest/api/azure/)
- [Zestaw SDK Microsoft Azure Storage dla języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
