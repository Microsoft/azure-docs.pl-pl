---
title: 'Szybki Start: Azure Blob Storage Library V12 — C++'
description: W tym przewodniku szybki start dowiesz się, jak używać biblioteki klienckiej usługi Azure Blob Storage w wersji 12 dla języka C++ do tworzenia kontenera i obiektu BLOB w magazynie obiektów BLOB (Object). Następnie dowiesz się, jak pobrać obiekt blob na komputer lokalny i jak wyświetlać listę wszystkich obiektów blob w kontenerze.
author: twooley
ms.author: twooley
ms.date: 10/21/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: e6832cc835a464a2a3d17d5ed286e71b17bc980e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312284"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-c"></a>Szybki Start: usługa Azure Blob Storage Client Library V12 for C++

Rozpocznij pracę z biblioteką klienta usługi Azure Blob Storage V12 dla języka C++. Usługa Azure Blob Storage to rozwiązanie magazynu obiektów firmy Microsoft dla chmury. Postępuj zgodnie z instrukcjami, aby zainstalować pakiet, i wypróbuj przykładowy kod dla podstawowych zadań. Blob Storage jest zoptymalizowany pod kątem przechowywania dużych ilości danych bez struktury.

Użyj usługi Azure Blob Storage Client Library V12 for C++, aby:

- Tworzenie kontenera
- Przekazywanie obiektu blob do usługi Azure Storage
- Wyświetl listę wszystkich obiektów BLOB w kontenerze
- Pobieranie obiektu BLOB na komputer lokalny
- Usuwanie kontenera

Zasoby:

- [Dokumentacja referencyjna interfejsu API](https://azure.github.io/azure-sdk-for-cpp/storage.html)
- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage)
- [Samples](../common/storage-samples-c-plus-plus.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/)
- [Konto usługi Azure Storage](../common/storage-account-create.md)
- [Kompilator języka C++](https://azure.github.io/azure-sdk/cpp_implementation.html#supported-platforms)
- [CMake](https://cmake.org/)
- [Menedżer pakietów Vcpkg-C i C++](https://github.com/microsoft/vcpkg/blob/master/docs/README.md)
- [LibCurl](https://curl.haxx.se/libcurl/)
- [LibXML2](http://www.xmlsoft.org/)

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji omówiono przygotowanie projektu do pracy z usługą Azure Blob Storage Client Library V12 for C++.

### <a name="install-the-packages"></a>Zainstaluj pakiety

Jeśli jeszcze tego nie zrobiono, zainstaluj pakiety LibCurl i LibXML2 za pomocą `vcpkg install` polecenia.

```console
vcpkg.exe install libxml2:x64-windows curl:x64-windows
```

Postępuj zgodnie z instrukcjami w witrynie GitHub, aby uzyskać i skompilować [zestaw Azure SDK dla języka C++](https://github.com/Azure/azure-sdk-for-cpp/).

### <a name="create-the-project"></a>Tworzenie projektu

W programie Visual Studio Utwórz nową aplikację konsolową w języku C++ dla systemu Windows o nazwie *BlobQuickstartV12*.

:::image type="content" source="./media/quickstart-blobs-c-plus-plus/vs-create-project.jpg" alt-text="Okno dialogowe programu Visual Studio służące do konfigurowania nowej aplikacji konsolowej dla systemu Windows w języku C++":::

Dodaj następujące biblioteki do projektu:

- libcurl. lib
- libxml2. lib
- bcrypt. lib
- Biblioteka crypt32. lib
- WS2_32. lib
- Azure-Core. lib
- Azure-Storage-Common. lib
- Azure-Storage-Blobs. lib

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektów

Usługa Azure Blob Storage jest zoptymalizowana pod kątem przechowywania dużych ilości danych bez struktury. Dane bez struktury to dane, które nie są zgodne z konkretnym modelem danych lub definicją, takimi jak dane tekstowe lub binarne. Blob Storage oferuje trzy typy zasobów:

- Konto magazynu
- Kontener na koncie magazynu
- Obiekt BLOB w kontenerze

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury Blob Storage](./media/storage-blobs-introduction/blob1.png)

Użyj następujących klas języka C++ do korzystania z tych zasobów:

- [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_service_client.html): `BlobServiceClient` Klasa umożliwia manipulowanie zasobami usługi Azure Storage i kontenerami obiektów BLOB.
- [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html): `BlobContainerClient` Klasa umożliwia manipulowanie kontenerami usługi Azure Storage i ich obiektami BLOB.
- [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html): `BlobClient` Klasa umożliwia manipulowanie obiektami BLOB usługi Azure Storage. Jest to klasa podstawowa dla wszystkich wyspecjalizowanych klas obiektów BLOB.
- [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html): `BlockBlobClient` Klasa umożliwia manipulowanie blokami BLOB usługi Azure Storage.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienta Blob Storage platformy Azure dla języka C++:

- [Dodaj pliki dołączenia](#add-include-files)
- [Pobieranie parametrów połączenia](#get-the-connection-string)
- [Tworzenie kontenera](#create-a-container)
- [Przekazywanie obiektów BLOB do kontenera](#upload-blobs-to-a-container)
- [Wyświetlanie listy obiektów blob w kontenerze](#list-the-blobs-in-a-container)
- [Pobieranie obiektów blob](#download-blobs)
- [Usuwanie kontenera](#delete-a-container)

### <a name="add-include-files"></a>Dodaj pliki dołączenia

Z katalogu projektu:

1. Otwórz plik rozwiązania *BlobQuickstartV12. sln* w programie Visual Studio
1. W programie Visual Studio Otwórz plik źródłowy *BlobQuickstartV12. cpp*
1. Usuń kod `main` , który został wygenerowany automatycznie
1. Dodaj `#include` instrukcje

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_Includes":::

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera parametry połączenia dla konta magazynu ze zmiennej środowiskowej utworzonej w temacie [Konfigurowanie parametrów połączenia magazynu](#configure-your-storage-connection-string).

Dodaj następujący kod w `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Tworzenie kontenera

Utwórz wystąpienie klasy [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html) przez wywołanie funkcji [CreateFromConnectionString](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ad103d1e3a7ce7c53a82da887d12ce6fe) . Następnie Wywołaj polecenie [Utwórz](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#a22a1e001068a4ec52bb1b6bd8b52c047) , aby utworzyć rzeczywisty kontener na koncie magazynu.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać więcej informacji o nazewnictwie kontenerów i obiektów blob, zobacz temat [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Dodaj ten kod na końcu `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Przekazywanie obiektów BLOB do kontenera

Następujący fragment kodu:

1. Deklaruje ciąg zawierający "Hello Azure!".
1. Pobiera odwołanie do obiektu [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html) przez wywołanie [GetBlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#acd8c68e3f37268fde0010dd478ff048f) w kontenerze z sekcji [Create a Container](#create-a-container) .
1. Przekazuje ciąg do obiektu BLOB, wywołując funkcję funkcji [LoadFrom](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html#af93af7e37f8806e39481596ef253f93d) . Ta funkcja tworzy obiekt BLOB, jeśli jeszcze nie istnieje, lub aktualizuje go, jeśli jest to możliwe.

Dodaj ten kod na końcu `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_UploadBlob":::

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Utwórz listę obiektów BLOB w kontenerze, wywołując funkcję [ListBlobsFlatSegment](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ac7712bc46909dc061d6bf554b496550c) . Do kontenera został dodany tylko jeden obiekt BLOB, więc operacja zwraca tylko ten obiekt BLOB.

Dodaj ten kod na końcu `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Pobieranie obiektów blob

Pobierz właściwości przekazanego obiektu BLOB. Następnie zadeklaruj i Zmień rozmiar nowego `std::string` obiektu za pomocą właściwości przekazanego obiektu BLOB. Pobierz utworzony wcześniej obiekt BLOB do nowego `std::string` obiektu, wywołując funkcję [DownloadTo](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#aa844f37a8c216f3cb0f27912b114c4d2) w klasie bazowej [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html) . Na koniec Wyświetl pobrane dane obiektów BLOB.

Dodaj ten kod na końcu `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DownloadBlob":::

### <a name="delete-a-blob"></a>Usuwanie obiektu BLOB

Poniższy kod usuwa obiekt BLOB z kontenera Blob Storage platformy Azure, wywołując funkcję [BlobClient. Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#a621eabcc8d23893ca1eb106494198615) .

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteBlob":::

### <a name="delete-a-container"></a>Usuwanie kontenera

Poniższy kod czyści zasoby utworzone przez aplikację przez usunięcie całego kontenera przy użyciu [BlobContainerClient. Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#aa6b1db52697ae92e9a1227e2e02a5178).

Dodaj ten kod na końcu `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy kontener i przekazuje plik tekstowy do usługi Azure Blob Storage. W przykładzie zostanie wyświetlona lista obiektów BLOB w kontenerze, plik zostanie pobrany i zostanie wyświetlona zawartość pliku. Na koniec aplikacja usuwa obiekt BLOB i kontener.

Dane wyjściowe aplikacji są podobne do następujących:

```output
Azure Blob Storage v12 - C++ quickstart sample
Creating container: myblobcontainer
Uploading blob: blob.txt
Listing blobs...
Blob name: blob.txt
Downloaded blob contents: Hello Azure!
Deleting blob: blob.txt
Deleting container: myblobcontainer
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób przekazywania, pobierania i wyświetlania listy obiektów BLOB przy użyciu języka C++. Zawarto również informacje na temat tworzenia i usuwania kontenera Blob Storage platformy Azure.

Aby wyświetlić przykład Blob Storage w języku C++, przejdź do:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 for C++ — przykład](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-blobs/sample)
