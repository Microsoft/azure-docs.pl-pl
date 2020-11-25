---
title: 'Szybki Start: Biblioteka usługi Azure Blob Storage V12 — Python'
description: W tym przewodniku szybki start dowiesz się, jak używać biblioteki klienckiej usługi Azure Blob Storage w wersji 12 dla języka Python, aby utworzyć kontener i obiekt BLOB w magazynie obiektów BLOB (Object). Następnie dowiesz się, jak pobrać obiekt blob na komputer lokalny i jak wyświetlać listę wszystkich obiektów blob w kontenerze.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: dbac832165c57e14e737372e12c4f4b2f3039c0d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995265"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu SDK V12 języka Python

W tym przewodniku szybki start nauczysz się zarządzać obiektami BLOB przy użyciu języka Python. Obiekty blob są obiektami, które mogą przechowywać duże ilości danych tekstowych lub binarnych, w tym obrazy, dokumenty, multimedia strumieniowe i dane archiwalne. Będziesz przekazywać, pobierać i wyświetlać listy obiektów blob, a następnie tworzyć i usuwać kontenery.

Dodatkowe zasoby:

* [Dokumentacja referencyjna interfejsu API](/python/api/azure-storage-blob)
* [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)
* [Pakiet (indeks pakietu języka Python)](https://pypi.org/project/azure-storage-blob/)
* [Samples](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Konto usługi Azure Storage. [Utwórz konto magazynu](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/) 2,7, 3,5 lub nowszy.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji omówiono przygotowanie projektu do pracy z biblioteką klienta usługi Azure Blob Storage V12 dla języka Python.

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

1. W obszarze *obiekt BLOB — szybki start V12* katalog Utwórz inny katalog o nazwie *dane*. Jest to miejsce, w którym są tworzone i przechowywane pliki danych obiektów BLOB.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Zainstaluj pakiet

Mimo że w katalogu aplikacji, zainstaluj bibliotekę klienta usługi Azure Blob Storage dla pakietu języka Python za pomocą `pip install` polecenia.

```console
pip install azure-storage-blob
```

To polecenie powoduje zainstalowanie biblioteki klienta usługi Azure Blob Storage dla pakietu języka Python i wszystkich bibliotek, od których jest ona zależna. W tym przypadku jest to tylko podstawowa Biblioteka platformy Azure dla języka Python.

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwórz nowy plik tekstowy w edytorze kodu
1. Dodaj `import` instrukcje
1. Utwórz strukturę dla programu, w tym podstawową obsługę wyjątków

    Oto kod:

    ```python
    import os, uuid
    from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient, __version__

    try:
        print("Azure Blob storage v" + __version__ + " - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

1. Zapisz nowy plik jako *BLOB-QuickStart-V12.py* w katalogu *BLOB-Start-V12* .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektów

Usługa Azure Blob Storage jest zoptymalizowana pod kątem przechowywania dużych ilości danych bez struktury. Dane bez struktury są danymi, które nie są zgodne z żadnym modelem lub definicją danych, jak na przykład dane tekstowe lub binarne. Magazyn obiektów blob oferuje trzy typy zasobów:

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

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienckiej usługi Azure Blob Storage dla języka Python:

* [Pobieranie parametrów połączenia](#get-the-connection-string)
* [Tworzenie kontenera](#create-a-container)
* [Przekazywanie obiektów BLOB do kontenera](#upload-blobs-to-a-container)
* [Wyświetlanie listy obiektów blob w kontenerze](#list-the-blobs-in-a-container)
* [Pobieranie obiektów blob](#download-blobs)
* [Usuwanie kontenera](#delete-a-container)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera parametry połączenia dla konta magazynu ze zmiennej środowiskowej utworzonej w sekcji [Konfigurowanie parametrów połączenia magazynu](#configure-your-storage-connection-string) .

Dodaj ten kod wewnątrz `try` bloku:

```python
# Retrieve the connection string for use with the application. The storage
# connection string is stored in an environment variable on the machine
# running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-container"></a>Tworzenie kontenera

Określ nazwę nowego kontenera. Poniższy kod dołącza wartość identyfikatora UUID do nazwy kontenera, aby upewnić się, że jest ona unikatowa.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać więcej informacji o nazewnictwie kontenerów i obiektów blob, zobacz temat [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Utwórz wystąpienie klasy [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) przez wywołanie metody [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) . Następnie Wywołaj metodę [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) , aby faktycznie utworzyć kontener na koncie magazynu.

Dodaj ten kod na końcu `try` bloku:

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>Przekazywanie obiektów BLOB do kontenera

Następujący fragment kodu:

1. Tworzy plik tekstowy w katalogu lokalnym.
1. Pobiera odwołanie do obiektu [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) przez wywołanie metody [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) na [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) z sekcji [Create a Container](#create-a-container) .
1. Przekazuje lokalny plik tekstowy do obiektu BLOB, wywołując metodę [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) .

Dodaj ten kod na końcu `try` bloku:

```python
# Create a file in local data directory to upload and download
local_path = "./data"
local_file_name = "quickstart" + str(uuid.uuid4()) + ".txt"
upload_file_path = os.path.join(local_path, local_file_name)

# Write text to the file
file = open(upload_file_path, 'w')
file.write("Hello, World!")
file.close()

# Create a blob client using the local file name as the name for the blob
blob_client = blob_service_client.get_blob_client(container=container_name, blob=local_file_name)

print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)

# Upload the created file
with open(upload_file_path, "rb") as data:
    blob_client.upload_blob(data)
```

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Utwórz listę obiektów BLOB w kontenerze, wywołując metodę [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) . W takim przypadku tylko jeden obiekt BLOB został dodany do kontenera, więc operacja tworzenia listy zwraca tylko jeden obiekt BLOB.

Dodaj ten kod na końcu `try` bloku:

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Pobieranie obiektów blob

Pobierz utworzony wcześniej obiekt BLOB, wywołując metodę [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) . Przykładowy kod dodaje sufiks "DOWNLOAD" do nazwy pliku, aby można było zobaczyć oba pliki w lokalnym systemie plików.

Dodaj ten kod na końcu `try` bloku:

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in the data directory
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>Usuwanie kontenera

Poniższy kod czyści zasoby utworzone przez aplikację przez usunięcie całego kontenera przy użyciu metody [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) . Możesz również usunąć pliki lokalne, jeśli chcesz.

Aplikacja wstrzymuje się do wprowadzania danych przez użytkownika `input()` , wywołując przed usunięciem obiektu BLOB, kontenera i plików lokalnych. Jest to dobry szansa, aby sprawdzić, czy zasoby zostały utworzone prawidłowo, przed ich usunięciem.

Dodaj ten kod na końcu `try` bloku:

```python
# Clean up
print("\nPress the Enter key to begin clean up")
input()

print("Deleting blob container...")
container_client.delete_container()

print("Deleting the local source and downloaded files...")
os.remove(upload_file_path)
os.remove(download_file_path)

print("Done")
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy plik testowy w folderze lokalnym i przekazuje go do magazynu obiektów BLOB. Przykład następnie wyświetla listę obiektów BLOB w kontenerze i pobiera plik z nową nazwą, aby można było porównać stare i nowe pliki.

Przejdź do katalogu zawierającego plik *BLOB-QuickStart-V12.py* , a następnie wykonaj następujące polecenie, `python` Aby uruchomić aplikację.

```console
python blob-quickstart-v12.py
```

Dane wyjściowe aplikacji są podobne do następujących:

```output
Azure Blob storage v12 - Python quickstart sample

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

Przed rozpoczęciem procesu oczyszczania Sprawdź, czy w folderze *danych* są dwa pliki. Możesz je otworzyć i sprawdzić, czy są identyczne.

Po zweryfikowaniu plików naciśnij klawisz **Enter** , aby usunąć pliki testowe i zakończyć demonstrację.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób przekazywania, pobierania i wyświetlania listy obiektów BLOB za pomocą języka Python.

Aby wyświetlić przykładowe aplikacje dla magazynu obiektów blob, przejdź do:

> [!div class="nextstepaction"]
> [Przykłady języka Python zestawu SDK usługi Azure Blob Storage V12](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Aby dowiedzieć się więcej, zobacz [biblioteki klienta usługi Azure Storage dla języka Python](/azure/developer/python/sdk/storage/overview).
* Samouczki, przykłady, Przewodniki Szybki Start i inne dokumenty można znaleźć [na platformie Azure dla deweloperów języka Python](/azure/python/).