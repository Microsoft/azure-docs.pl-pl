---
title: Programowanie dla Azure Files przy użyciu języka Python | Microsoft Docs
description: Dowiedz się, jak opracowywać aplikacje i usługi w języku Python, które używają Azure Files do przechowywania danych plików.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 139e3009722761172b7bbd57805a7f5b07e55fc0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "68699394"
---
# <a name="develop-for-azure-files-with-python"></a>Develop for Azure Files with Python (Tworzenie oprogramowania dla usługi Azure Files przy użyciu języka Python)
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

W tym samouczku przedstawiono podstawowe informacje dotyczące używania języka Python do tworzenia aplikacji lub usług, które używają Azure Files do przechowywania danych plików. W tym samouczku utworzymy prostą aplikację konsolową i pokażemy, jak wykonywać podstawowe działania za pomocą języka Python i Azure Files:

* Tworzenie udziałów plików platformy Azure
* Tworzenie katalogów
* Wyliczanie plików i katalogów w udziale plików platformy Azure
* Przekazywanie, pobieranie i usuwanie pliku

> [!Note]  
> Ponieważ do Azure Files można uzyskać dostęp za pośrednictwem protokołu SMB, można napisać proste aplikacje, które uzyskują dostęp do udziału plików platformy Azure przy użyciu standardowych klas i funkcji we/wy języka Python. W tym artykule opisano sposób pisania aplikacji korzystających z zestawu SDK usługi Azure Storage w języku Python, który używa [interfejsu API REST Azure Files](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) , aby komunikować się z Azure Files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Pobierz i Zainstaluj zestaw SDK usługi Azure Storage dla języka Python

[Zestaw SDK usługi Azure Storage dla języka Python](https://github.com/azure/azure-storage-python) wymaga języka Python 2,7, 3,3, 3,4, 3,5 lub 3,6.
 
## <a name="install-via-pypi"></a>Zainstaluj za pomocą PyPi

Aby zainstalować program za pośrednictwem indeksu pakietów języka Python (PyPI), wpisz:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Jeśli uaktualniasz z zestawu SDK usługi Azure Storage dla języka Python w wersji 0,36 lub starszej, Odinstaluj starszy `pip uninstall azure-storage` zestaw SDK przy użyciu programu przed zainstalowaniem najnowszego pakietu.

Aby zapoznać się z alternatywnymi metodami instalacji, odwiedź [zestaw SDK usługi Azure Storage dla języka Python w witrynie GitHub](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Wyświetlanie przykładowej aplikacji
Aby wyświetlić i uruchomić przykładową aplikację, która pokazuje, jak używać języka Python z Azure Files, zobacz [Azure Storage: wprowadzenie z Azure Files w języku Python](https://github.com/Azure-Samples/storage-file-python-getting-started). 

Aby uruchomić przykładową aplikację, upewnij się, że zainstalowano `azure-storage-file` pakiety `azure-storage-common` i.

## <a name="set-up-your-application-to-use-azure-files"></a>Skonfiguruj aplikację do używania Azure Files
Dodaj następujące elementy w górnej części każdego pliku źródłowego języka Python, w którym chcesz programowo uzyskać dostęp do usługi Azure Storage.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Skonfiguruj połączenie z usługą Azure Files 
`FileService` Obiekt umożliwia korzystanie z udziałów, katalogów i plików. Poniższy kod tworzy `FileService` Obiekt przy użyciu nazwy konta magazynu i klucza konta. Zastąp wartości `<myaccount>` i `<mykey>` nazwą i kluczem konta.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure
W poniższym przykładzie kodu można użyć `FileService` obiektu, aby utworzyć udział, jeśli nie istnieje.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Tworzenie katalogu
Magazyn można również organizować, umieszczając pliki w podkatalogach zamiast ich wszystkich w katalogu głównym. Azure Files umożliwia utworzenie tylu katalogów, ile zezwoli Twoje konto. Poniższy kod utworzy podkatalog o nazwie **sampledir** w katalogu głównym.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Wyliczanie plików i katalogów w udziale plików platformy Azure
Aby wyświetlić listę plików i katalogów w udziale, użyj metody **list\_\_katalogów i\_plików** . Ta metoda zwraca generator. Poniższy kod wyprowadza **nazwy** poszczególnych plików i katalogów w udziale do konsoli programu.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Przekazywanie pliku 
Udział plików platformy Azure zawiera co najmniej katalog główny, w którym znajdują się pliki. W tej sekcji dowiesz się, jak przekazać plik z magazynu lokalnego do katalogu głównego udziału.

Aby utworzyć plik i przekazać dane, użyj `create_file_from_path`metod, `create_file_from_stream` `create_file_from_bytes` lub. `create_file_from_text` Są to metody wysokiego poziomu, które wykonują niezbędne fragmenty, gdy rozmiar danych przekracza 64 MB.

`create_file_from_path`przekazuje zawartość pliku z określonej ścieżki i `create_file_from_stream` przekazuje zawartość z już otwartego pliku/strumienia. `create_file_from_bytes`przekazuje tablicę bajtów i `create_file_from_text` przekazuje określoną wartość tekstową przy użyciu określonego kodowania (wartość domyślna to UTF-8).

Poniższy przykład przekazuje zawartość pliku **słońca. png** **do pliku.**

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>Pobieranie pliku
Aby pobrać dane z pliku, `get_file_to_path`Użyj, `get_file_to_stream`, `get_file_to_bytes`, lub. `get_file_to_text` Są to metody wysokiego poziomu, które wykonują niezbędne fragmenty, gdy rozmiar danych przekracza 64 MB.

W poniższym przykładzie pokazano `get_file_to_path` , jak pobrać zawartość pliku mój **plik** i zapisać go w pliku **out-Sunset. png** .

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Usuwanie pliku
Na koniec, aby usunąć plik, wywołaj `delete_file`polecenie.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Utwórz migawkę udziału
Można utworzyć kopię całego udziału plików w czasie.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Utwórz migawkę udziału z metadanymi**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Wyświetl listę udziałów i migawek 
Można wyświetlić listę wszystkich migawek dla określonego udziału.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Przeglądaj migawkę udziału
Zawartość każdej migawki udziału można przeglądać, aby pobierać pliki i katalogi z tego punktu w czasie.

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Pobierz plik z migawki udziału
Możesz pobrać plik z migawki udziału dla scenariusza przywracania.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Usuń migawkę pojedynczego udziału  
Można usunąć migawkę pojedynczego udziału.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Usuń udział w przypadku istnienia migawek udziałów
Nie można usunąć udziału zawierającego migawki, chyba że wszystkie migawki zostaną usunięte jako pierwsze.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Następne kroki
Teraz, gdy już wiesz, jak manipulować Azure Files przy użyciu języka Python, Skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/)
* [Interfejs API REST usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179355)
* [Zestaw SDK Microsoft Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python)
