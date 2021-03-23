---
title: 'Szybki Start: Azure Blob Storage Library V12 — Python'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć kontener i obiekt BLOB w magazynie obiektów BLOB (Object) przy użyciu biblioteki klienta Blob Storage platformy Azure w wersji 12 dla języka Python. Następnie dowiesz się, jak pobrać obiekt blob na komputer lokalny i jak wyświetlać listę wszystkich obiektów blob w kontenerze.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/28/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: cb71d70f6869cc539702426a245aa7e7cd965913
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800554"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu SDK V12 języka Python

W tym przewodniku szybki start nauczysz się zarządzać obiektami BLOB przy użyciu języka Python. Obiekty blob są obiektami, które mogą przechowywać duże ilości danych tekstowych lub binarnych, w tym obrazy, dokumenty, multimedia strumieniowe i dane archiwalne. Będziesz przekazywać, pobierać i wyświetlać listy obiektów blob, a następnie tworzyć i usuwać kontenery.

Więcej zasobów:

* [Dokumentacja referencyjna interfejsu API](/python/api/azure-storage-blob)
* [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)
* [Pakiet (indeks pakietu języka Python)](https://pypi.org/project/azure-storage-blob/)
* [Samples](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Konto usługi Azure Storage. [Utwórz konto magazynu](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/) 2,7 lub 3.6 +.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji omówiono przygotowanie projektu do pracy z usługą Azure Blob Storage Client Library V12 for Python.

### <a name="create-the-project"></a>Tworzenie projektu

Tworzenie aplikacji w języku Python o nazwie *BLOB-Start-V12*.

1. W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla projektu.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Przejdź do nowo utworzonego katalogu *obiektów BLOB — szybki start-V12* .

    ```console
    cd blob-quickstart-v12
    ```

1. W obszarze *obiekt BLOB — szybki start V12* katalog Utwórz inny katalog o nazwie *dane*. Ten katalog jest miejscem, w którym będą tworzone i przechowywane pliki danych obiektów BLOB.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Zainstaluj pakiet

Mimo że w katalogu aplikacji, zainstaluj bibliotekę kliencką platformy Azure Blob Storage dla pakietu języka Python za pomocą `pip install` polecenia.

```console
pip install azure-storage-blob
```

To polecenie służy do instalowania biblioteki klienta Blob Storage platformy Azure dla pakietu języka Python oraz wszystkich bibliotek, od których jest ona zależna. W tym przypadku jest to tylko podstawowa Biblioteka platformy Azure dla języka Python.

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwórz nowy plik tekstowy w edytorze kodu
1. Dodaj `import` instrukcje
1. Utwórz strukturę dla programu, w tym podstawową obsługę wyjątków

    Oto kod:

    :::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/app_framework.py":::

1. Zapisz nowy plik jako *BLOB-QuickStart-V12.py* w katalogu *BLOB-Start-V12* .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektów

Usługa Azure Blob Storage jest zoptymalizowana pod kątem przechowywania dużych ilości danych bez struktury. Dane bez struktury to dane, które nie są zgodne z konkretnym modelem danych lub definicją, takimi jak dane tekstowe lub binarne. Magazyn obiektów blob oferuje trzy typy zasobów:

* Konto magazynu
* Kontener na koncie magazynu
* Obiekt BLOB w kontenerze

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu obiektów blob](./media/storage-blobs-introduction/blob1.png)

Użyj następujących klas języka Python do korzystania z tych zasobów:

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): `BlobServiceClient` Klasa umożliwia manipulowanie zasobami usługi Azure Storage i kontenerami obiektów BLOB.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): `ContainerClient` Klasa umożliwia manipulowanie kontenerami usługi Azure Storage i ich obiektami BLOB.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): `BlobClient` Klasa umożliwia manipulowanie obiektami BLOB usługi Azure Storage.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienta Blob Storage platformy Azure dla języka Python:

* [Pobieranie parametrów połączenia](#get-the-connection-string)
* [Tworzenie kontenera](#create-a-container)
* [Przekazywanie obiektów BLOB do kontenera](#upload-blobs-to-a-container)
* [Wyświetlanie listy obiektów blob w kontenerze](#list-the-blobs-in-a-container)
* [Pobieranie obiektów blob](#download-blobs)
* [Usuwanie kontenera](#delete-a-container)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera parametry połączenia konta magazynu ze zmiennej środowiskowej utworzonej w sekcji [Konfigurowanie parametrów połączenia magazynu](#configure-your-storage-connection-string) .

Dodaj ten kod wewnątrz `try` bloku:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Tworzenie kontenera

Określ nazwę nowego kontenera. Poniższy kod dołącza wartość identyfikatora UUID do nazwy kontenera, aby upewnić się, że jest ona unikatowa.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać więcej informacji o nazewnictwie kontenerów i obiektów blob, zobacz temat [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Utwórz wystąpienie klasy [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) przez wywołanie metody [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) . Następnie Wywołaj metodę [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) , aby faktycznie utworzyć kontener na koncie magazynu.

Dodaj ten kod na końcu `try` bloku:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Przekazywanie obiektów BLOB do kontenera

Następujący fragment kodu:

1. Tworzy katalog lokalny do przechowywania plików danych.
1. Tworzy plik tekstowy w katalogu lokalnym.
1. Pobiera odwołanie do obiektu [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) przez wywołanie metody [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) na [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) z sekcji [Create a Container](#create-a-container) .
1. Przekazuje lokalny plik tekstowy do obiektu BLOB, wywołując metodę [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) .

Dodaj ten kod na końcu `try` bloku:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Utwórz listę obiektów BLOB w kontenerze, wywołując metodę [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) . W takim przypadku tylko jeden obiekt BLOB został dodany do kontenera, więc operacja tworzenia listy zwraca tylko jeden obiekt BLOB.

Dodaj ten kod na końcu `try` bloku:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Pobieranie obiektów blob

Pobierz utworzony wcześniej obiekt BLOB, wywołując metodę [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) . Przykładowy kod dodaje sufiks "DOWNLOAD" do nazwy pliku, aby można było zobaczyć oba pliki w lokalnym systemie plików.

Dodaj ten kod na końcu `try` bloku:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Usuwanie kontenera

Poniższy kod czyści zasoby utworzone przez aplikację przez usunięcie całego kontenera przy użyciu metody [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) . Możesz również usunąć pliki lokalne, jeśli chcesz.

Aplikacja wstrzymuje się do wprowadzania danych przez użytkownika `input()` , wywołując przed usunięciem obiektu BLOB, kontenera i plików lokalnych. Przed usunięciem zasobów Sprawdź, czy zostały one utworzone prawidłowo.

Dodaj ten kod na końcu `try` bloku:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CleanUp":::

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy plik testowy w folderze lokalnym i przekazuje go do usługi Azure Blob Storage. Przykład następnie wyświetla listę obiektów BLOB w kontenerze i pobiera plik z nową nazwą. Można porównać stare i nowe pliki.

Przejdź do katalogu zawierającego plik *BLOB-QuickStart-V12.py* , a następnie wykonaj następujące polecenie, `python` Aby uruchomić aplikację.

```console
python blob-quickstart-v12.py
```

Dane wyjściowe aplikacji są podobne do następujących:

```output
Azure Blob Storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Przed rozpoczęciem procesu oczyszczania Sprawdź, czy w folderze *danych* istnieją dwa pliki. Można je otworzyć i sprawdzić, czy są one identyczne.

Po zweryfikowaniu plików naciśnij klawisz **Enter** , aby usunąć pliki testowe i zakończyć demonstrację.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób przekazywania, pobierania i wyświetlania listy obiektów BLOB za pomocą języka Python.

Aby wyświetlić przykładowe aplikacje dla magazynu obiektów blob, przejdź do:

> [!div class="nextstepaction"]
> [Przykłady języka Python w usłudze Azure Blob Storage SDK V12](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Aby dowiedzieć się więcej, zobacz [biblioteki klienta usługi Azure Storage dla języka Python](/azure/developer/python/sdk/storage/overview).
* Samouczki, przykłady, Przewodniki Szybki Start i inne dokumenty można znaleźć [na platformie Azure dla deweloperów języka Python](/azure/python/).
