---
title: 'Szybki start: biblioteka Azure Blob Storage v12 — Python'
description: W tym przewodniku Szybki start dowiesz się, jak używać biblioteki klienta Azure Blob Storage w wersji 12 dla języka Python do tworzenia kontenera i obiektu blob w magazynie obiektów blob (obiekt). Następnie dowiesz się, jak pobrać obiekt blob na komputer lokalny i jak wyświetlać listę wszystkich obiektów blob w kontenerze.
author: twooley
ms.author: twooley
ms.date: 01/28/2021
ms.topic: quickstart
ms.service: storage
ms.subservice: blobs
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 9a6ccbe5482ac8ffe484c3155334cba1b50ba0dc
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535658"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Szybki start: zarządzanie obiektami blob przy użyciu zestawu Python SDK w wersji 12

Z tego przewodnika Szybki start dowiesz się, jak zarządzać obiektami blob przy użyciu języka Python. Obiekty blob to obiekty, które mogą przechowywać duże ilości danych tekstowych lub binarnych, w tym obrazy, dokumenty, nośniki strumieniowe i dane archiwum. Przekażesz, pobierzesz i utworzysz listę obiektów blob, a także utworzysz i usuniesz kontenery.

Więcej zasobów:

* [Dokumentacja referencyjna interfejsu API](/python/api/azure-storage-blob)
* [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)
* [Pakiet (indeks pakietów języka Python)](https://pypi.org/project/azure-storage-blob/)
* [Samples](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Konto usługi Azure Storage. [Utwórz konto magazynu.](../common/storage-account-create.md)
- [Python](https://www.python.org/downloads/) 2.7 lub 3.6+.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji odpowiedzialności za przygotowanie projektu do pracy z biblioteką klienta Azure Blob Storage w wersji 12 dla języka Python.

### <a name="create-the-project"></a>Tworzenie projektu

Utwórz aplikację w języku Python *o nazwie blob-quickstart-v12.*

1. W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog dla projektu.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Przejdź do nowo utworzonego *katalogu blob-quickstart-v12.*

    ```console
    cd blob-quickstart-v12
    ```

1. W katalogu *blob-quickstart-v12* utwórz inny katalog o *nazwie data*. W tym katalogu będą tworzone i przechowywane pliki danych obiektów blob.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instalowanie pakietu

W katalogu aplikacji zainstaluj bibliotekę klienta Azure Blob Storage dla pakietu języka Python przy użyciu `pip install` polecenia .

```console
pip install azure-storage-blob
```

To polecenie instaluje Azure Blob Storage klienta dla pakietu języka Python i wszystkich bibliotek, od których zależy. W tym przypadku jest to po prostu podstawowa biblioteka platformy Azure dla języka Python.

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwieranie nowego pliku tekstowego w edytorze kodu
1. Dodawanie `import` instrukcji
1. Tworzenie struktury programu, w tym podstawowej obsługi wyjątków

    Oto kod:

    :::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/app_framework.py":::

1. Zapisz nowy plik jako *blob-quickstart-v12.py* w katalogu *blob-quickstart-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektów

Azure Blob Storage jest zoptymalizowany pod kątem przechowywania ogromnych ilości danych bez struktury. Dane nieustrukturyzowane to dane, które nie są zgodne z konkretnym modelem lub definicją danych, takimi jak dane tekstowe lub binarne. Magazyn obiektów blob oferuje trzy typy zasobów:

* Konto magazynu
* Kontener na koncie magazynu
* Obiekt blob w kontenerze

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu obiektów blob](./media/storage-blobs-introduction/blob1.png)

Użyj następujących klas języka Python, aby wchodzić w interakcje z tymi zasobami:

* [BlobServiceClient:](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) `BlobServiceClient` klasa umożliwia manipulowanie zasobami usługi Azure Storage i kontenerami obiektów blob.
* [ContainerClient:](/python/api/azure-storage-blob/azure.storage.blob.containerclient) `ContainerClient` klasa umożliwia manipulowanie kontenerami usługi Azure Storage i ich obiektami blob.
* [BlobClient:](/python/api/azure-storage-blob/azure.storage.blob.blobclient) `BlobClient` klasa umożliwia manipulowanie obiektami blob usługi Azure Storage.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące zadania przy użyciu Azure Blob Storage klienta dla języka Python:

* [Pobieranie parametrów połączenia](#get-the-connection-string)
* [Tworzenie kontenera](#create-a-container)
* [Przekazywanie obiektów blob do kontenera](#upload-blobs-to-a-container)
* [Wyświetlanie listy obiektów blob w kontenerze](#list-the-blobs-in-a-container)
* [Pobieranie obiektów blob](#download-blobs)
* [Usuwanie kontenera](#delete-a-container)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera ciąg połączenia konta magazynu ze zmiennej środowiskowej utworzonej w sekcji [Konfigurowanie parametrów połączenia](#configure-your-storage-connection-string) magazynu.

Dodaj ten kod wewnątrz `try` bloku:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Tworzenie kontenera

Wybierz nazwę nowego kontenera. Poniższy kod dołącza wartość UUID do nazwy kontenera, aby upewnić się, że jest unikatowa.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać więcej informacji o nazewnictwie kontenerów i obiektów blob, zobacz temat [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Utwórz wystąpienie klasy [BlobServiceClient,](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) wywołując [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) metody . Następnie wywołaj metodę [create_container,](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) aby faktycznie utworzyć kontener na koncie magazynu.

Dodaj ten kod na końcu `try` bloku:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Przekazywanie obiektów blob do kontenera

Poniższy fragment kodu:

1. Tworzy katalog lokalny do przechowywania plików danych.
1. Tworzy plik tekstowy w katalogu lokalnym.
1. Pobiera odwołanie do obiektu [BlobClient,](/python/api/azure-storage-blob/azure.storage.blob.blobclient) wywołując metodę [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) obiektu [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) z [sekcji Tworzenie kontenera.](#create-a-container)
1. Przekazanie lokalnego pliku tekstowego do obiektu blob przez wywołanie [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) metody .

Dodaj ten kod na końcu `try` bloku:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Aby wyświetlić listę obiektów blob w kontenerze, wywołaj [metodę list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) . W tym przypadku tylko jeden obiekt blob został dodany do kontenera, więc operacja listingowania zwraca tylko ten jeden obiekt blob.

Dodaj ten kod na końcu `try` bloku:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Pobieranie obiektów blob

Pobierz utworzony wcześniej obiekt blob, wywołując [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) metody . Przykładowy kod dodaje sufiks "POBIERZ" do nazwy pliku, aby można było zobaczyć oba pliki w lokalnym systemie plików.

Dodaj ten kod na końcu `try` bloku:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Usuwanie kontenera

Poniższy kod czyści zasoby utworzone przez aplikację przez usunięcie całego kontenera przy użyciu [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) metody . Jeśli chcesz, możesz również usunąć pliki lokalne.

Aplikacja wstrzymuje wprowadzanie danych przez użytkownika przez wywołanie metody przed usunięciem obiektu `input()` blob, kontenera i plików lokalnych. Przed usunięciem upewnij się, że zasoby zostały utworzone poprawnie.

Dodaj ten kod na końcu `try` bloku:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CleanUp":::

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja utworzy plik testowy w folderze lokalnym i przekaże go do Azure Blob Storage. Następnie przykład wyświetla listę obiektów blob w kontenerze i pobiera plik z nową nazwą. Można porównać stare i nowe pliki.

Przejdź do katalogu zawierającego *blob-quickstart-v12.py,* a następnie wykonaj następujące `python` polecenie, aby uruchomić aplikację.

```console
python blob-quickstart-v12.py
```

Dane wyjściowe aplikacji są podobne do następującego przykładu:

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

Przed rozpoczęciem procesu oczyszczania sprawdź, czy w *folderze danych* są dwa pliki. Możesz je otworzyć i sprawdzić, czy są identyczne.

Po zweryfikowaniu plików naciśnij klawisz **Enter,** aby usunąć pliki testowe i zakończyć pokaz.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób przekazywania, pobierania i wysyłania listy obiektów blob przy użyciu języka Python.

Aby wyświetlić przykładowe aplikacje usługi Blob Storage, przejdź do:

> [!div class="nextstepaction"]
> [Azure Blob Storage przykłady dla języka Python dla zestawu SDK w wersji 12](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Aby dowiedzieć się więcej, zobacz [Biblioteki klienta usługi Azure Storage dla języka Python.](/azure/developer/python/sdk/storage/overview)
* Samouczki, przykłady, poradniki Szybki start i inne dokumenty można znaleźć na stronie [Azure for Python Developers (Platforma Azure dla deweloperów języka Python).](/azure/python/)
