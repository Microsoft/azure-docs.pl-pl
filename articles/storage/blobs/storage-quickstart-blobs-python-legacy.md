---
title: 'Szybki Start: Biblioteka kliencka usługi Azure Blob Storage wersja 2.1 dla języka Python'
description: Ten przewodnik Szybki start przedstawia tworzenie konta magazynu i kontenera w magazynie obiektów (blob). Następnie użyj biblioteki klienta usługi Storage w wersji 2.1 dla języka Python, aby przekazać obiekt BLOB do magazynu Azure, pobrać obiekt BLOB i wyświetlić listę obiektów BLOB w kontenerze.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 34205caf03d2f2d7255f75ea6203c5572c4c429b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95523311"
---
# <a name="quickstart-manage-blobs-with-python-v21-sdk"></a>Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu SDK języka Python v 2.1

W tym przewodniku szybki start nauczysz się zarządzać obiektami BLOB przy użyciu języka Python. Obiekty blob są obiektami, które mogą przechowywać duże ilości danych tekstowych lub binarnych, w tym obrazy, dokumenty, multimedia strumieniowe i dane archiwalne. Będziesz przekazywać, pobierać i wyświetlać listy obiektów blob, a następnie tworzyć i usuwać kontenery.

> [!NOTE]
> Ten przewodnik Szybki Start używa starszej wersji biblioteki klienta usługi Azure Blob Storage. Aby rozpocząć pracę z najnowszą wersją, zobacz [Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu SDK języka Python V12](storage-quickstart-blobs-python.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Konto usługi Azure Storage. [Utwórz konto magazynu](../common/storage-account-create.md).
- Język [Python](https://www.python.org/downloads/).
- [Zestaw SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-sdk-for-python).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

[Przykładowa aplikacja](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) używana w tym przewodniku Szybki start to podstawowa aplikacja w języku Python.  

Użyj następującego polecenia [git](https://git-scm.com/) , aby pobrać aplikację do środowiska deweloperskiego. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Aby przejrzeć program Python, Otwórz plik *example.py* w katalogu głównym repozytorium.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu

W aplikacji podaj nazwę konta magazynu i klucz konta, aby utworzyć obiekt `BlockBlobService`.

1. Otwórz plik *example.py* w Eksploratorze rozwiązań w środowisku IDE.

1. Zastąp `accountname` `accountkey` wartości i nazwą konta magazynu i kluczem:

    ```python
    block_blob_service = BlockBlobService(
        account_name='accountname', account_key='accountkey')
    ```

1. Zapisz i zamknij plik.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Przykładowy program tworzy plik testowy w folderze *dokumenty* , przekazuje plik do magazynu obiektów blob, wyświetla listę obiektów BLOB w pliku i pobiera plik z nową nazwą.

1. Zainstaluj zależności:

    ```console
    pip install azure-storage-blob==2.1.0
    ```

1. Przejdź do przykładowej aplikacji:

    ```console
    cd storage-blobs-python-quickstart
    ```

1. Uruchom przykład:

    ```console
    python example.py
    ```

    Zostaną wyświetlone komunikaty podobne do następujących danych wyjściowych:
  
    ```output
    Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    List blobs in the container
             Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Downloading blob to     C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
    ```

1. Przed kontynuowaniem przejdź do folderu *dokumenty* i sprawdź, czy są dwa pliki.

    * *QuickStart_\<universally-unique-identifier\>*
    * *QuickStart_ \<universally-unique-identifier\> _DOWNLOADED*

1. Możesz je otworzyć i sprawdzić, czy są takie same.

    Możesz również użyć narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://storageexplorer.com). Jest to dobre do wyświetlania plików w usłudze BLOB Storage. Eksplorator usługi Azure Storage to bezpłatne narzędzie Międzyplatformowe, które pozwala uzyskać dostęp do informacji o koncie magazynu. 

1. Po przeszukaniu plików naciśnij dowolny klawisz, aby zakończyć próbkę i usunąć pliki testowe.

## <a name="learn-about-the-sample-code"></a>Dowiedz się więcej na temat przykładowego kodu

Teraz, gdy wiesz już, jak działa aplikacja przykładowa, otwórz plik *example.py* i przyjrzyj się kodowi.

### <a name="get-references-to-the-storage-objects"></a>Pobieranie odwołań do obiektów magazynu

Ta sekcja poświęcona jest tworzeniu wystąpień obiektów, tworzeniu nowego kontenera, a następnie konfigurowaniu uprawnień w kontenerze, tak aby obiekty blob były publiczne. Nastąpi wywołanie kontenera `quickstartblobs` . 

```python
# Create the BlockBlockService that the system uses to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```

Najpierw należy utworzyć odwołania do obiektów używane w celu uzyskania dostępu do magazynu obiektów blob i zarządzania nim. Te obiekty są powiązane i każdy obiekt jest używany przez kolejny na liście.

* Utwórz wystąpienie obiektu **BlockBlobService**, który wskazuje usługę Blob service na koncie magazynu. 

* Utwórz wystąpienie obiektu **CloudBlobContainer** reprezentujące kontener, do którego uzyskujesz dostęp. System używa kontenerów do organizowania obiektów BLOB w taki sposób, jak foldery na komputerze są używane do organizowania plików.

Gdy istnieje już kontener CloudBlobContainer, utwórz wystąpienie obiektu **CloudBlockBlob** wskazujące na konkretny obiekt blob, którego chcesz użyć. Następnie możesz przekazywać, pobierać i kopiować obiekt blob zgodnie z potrzebami.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać więcej informacji na temat nazw kontenerów i obiektów blob, zobacz [nazywanie i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="upload-blobs-to-the-container"></a>Przekazywanie obiektów blob do kontenera

Usługa Blob Storage obsługuje blokowe, uzupełnialne i stronicowe obiekty blob. Blokowe obiekty blob mogą mieć rozmiar nawet do 4,7 TB i mogą to być dowolne pliki, od arkuszy kalkulacyjnych programu Excel po duże pliki wideo. Możesz użyć Dołącz obiekty blob do rejestrowania, gdy chcesz zapisać w pliku, a następnie kontynuować dodawanie dodatkowych informacji. Stronicowe obiekty blob są używane głównie dla plików wirtualnych dysków twardych (VHD), które są zapleczem infrastruktury jako usługi (maszyny wirtualne IaaS). Blokowe obiekty blob są używane najczęściej. Ten przewodnik Szybki Start używa blokowych obiektów BLOB.

Aby przekazać plik do obiektu blob, uzyskaj pełną ścieżkę pliku, łącząc nazwę katalogu i nazwę pliku na dysku lokalnym. Następnie możesz przekazać plik do określonej ścieżki przy użyciu metody `create_blob_from_path`. 

Przykładowy kod tworzy plik lokalny, którego system używa do przekazywania i pobierania, przechowując plik przekazywany przez system jako *full_path_to_file* i nazwę obiektu blob jako *local_file_name*. Ten przykład przekazuje plik do kontenera o nazwie `quickstartblobs` :

```python
# Create a file in Documents to test the upload and download.
local_path = os.path.expanduser("~\Documents")
local_file_name = "QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file = os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file, 'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name.
block_blob_service.create_blob_from_path(
    container_name, local_file_name, full_path_to_file)
```

Istnieje kilka metod przekazywania, których można użyć z usługą Blob Storage. Na przykład w przypadku strumienia pamięci możesz użyć metody `create_blob_from_stream` zamiast metody `create_blob_from_path`. 

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Poniższy kod tworzy `generator` dla `list_blobs` metody. Kod przechodzi przez listę obiektów BLOB w kontenerze i drukuje ich nazwy do konsoli programu.

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Pobieranie obiektów blob


Pobierz obiekty blob na dysk lokalny przy użyciu `get_blob_to_path` metody.
Poniższy kod pobiera wcześniej przekazany obiekt BLOB. System dołącza *_DOWNLOADED* do nazwy obiektu BLOB, aby można było zobaczyć oba pliki na dysku lokalnym.

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, local_file_name.replace(
   '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli nie potrzebujesz już obiektów blob przekazanych podczas pracy z tym przewodnikiem Szybki start, możesz usunąć cały kontener za pomocą metody `delete_container`. Aby zamiast tego usunąć pojedyncze pliki, użyj metody `delete_blob`.

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="resources-for-developing-python-applications-with-blobs"></a>Zasoby używane do tworzenia aplikacji Python z obiektami blob

Aby uzyskać więcej informacji na temat programowania w języku Python za pomocą usługi BLOB Storage, zobacz następujące dodatkowe zasoby:

### <a name="binaries-and-source-code"></a>Pliki binarne i kod źródłowy

- W witrynie GitHub wyświetl [kod źródłowy biblioteki klienta Python](https://github.com/Azure/azure-storage-python) dla usługi Azure Storage, a następnie pobierz i zainstaluj go.

### <a name="client-library-reference-and-samples"></a>Dokumentacja i przykłady dotyczące biblioteka klienta

- Aby uzyskać więcej informacji na temat biblioteki klienta języka Python, zobacz [biblioteki usługi Azure Storage dla języka Python](/python/api/overview/azure/storage).
- Zapoznaj się z [przykładami magazynu Blob Storage](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) napisanymi przy użyciu biblioteki klienta Python.

## <a name="next-steps"></a>Następne kroki
 
W tym przewodniku Szybki start przedstawiono metodę transferowania plików między dyskiem lokalnym i usługą Azure Blob Storage przy użyciu języka Python. 

Aby uzyskać więcej informacji na temat Eksplorator usługi Storage i obiektów blob, zobacz [Zarządzanie zasobami usługi Azure Blob Storage za pomocą usługi Eksplorator usługi Storage](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).