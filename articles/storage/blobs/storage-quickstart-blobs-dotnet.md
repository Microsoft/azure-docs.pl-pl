---
title: 'Szybki start: biblioteka Azure Blob Storage v12 — .NET'
description: W tym przewodniku Szybki start dowiesz się, jak używać biblioteki klienta Azure Blob Storage w wersji 12 dla platformy .NET w celu utworzenia kontenera i obiektu blob w magazynie obiektów blob (obiekt). Następnie dowiesz się, jak pobrać obiekt blob na komputer lokalny i jak wyświetlać listę wszystkich obiektów blob w kontenerze.
author: twooley
ms.author: twooley
ms.date: 03/03/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: f913b33d0bea425a24d2fd336c9d065978606e82
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869261"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Szybki start: Azure Blob Storage klienta w wersji 12 dla programu .NET

Wprowadzenie do biblioteki Azure Blob Storage klienta w wersji 12 dla programu .NET. Azure Blob Storage to rozwiązanie firmy Microsoft do magazynowania obiektów dla chmury. Wykonaj kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Magazyn obiektów blob jest zoptymalizowany pod kątem przechowywania olbrzymich ilości danych bez struktury.

Użyj biblioteki Azure Blob Storage klienta w wersji 12 dla programu .NET, aby:

* Tworzenie kontenera
* Przekazywanie obiektu blob do usługi Azure Storage
* Lista wszystkich obiektów blob w kontenerze
* Pobieranie obiektu blob na komputer lokalny
* Usuwanie kontenera

Dodatkowe zasoby:

* [Dokumentacja referencyjna interfejsu API](/dotnet/api/azure.storage.blobs)
* [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [Samples](../common/storage-samples-dotnet.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/)
* Konto usługi Azure Storage [— tworzenie konta magazynu](../common/storage-account-create.md)
* Bieżące [zestaw .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) dla twojego systemu operacyjnego. Pamiętaj, aby pobrać zestaw SDK, a nie środowisko uruchomieniowe.

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji ojęliśmy proces przygotowywania projektu do pracy z Azure Blob Storage klienta w wersji 12 dla programu .NET.

### <a name="create-the-project"></a>Tworzenie projektu

Utwórz aplikację .NET Core o *nazwie BlobQuickstartV12.*

1. W oknie konsoli (takim jak cmd, PowerShell lub Bash) użyj polecenia , aby utworzyć nową aplikację konsolową o nazwie `dotnet new` *BlobQuickstartV12.* To polecenie tworzy prosty projekt języka C# "Hello world" z pojedynczym plikiem źródłowym: *Program.cs.*

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Przejdź do nowo utworzonego *katalogu BlobQuickstartV12.*

   ```console
   cd BlobQuickstartV12
   ```

1. W katalogu *BlobQuickstartV12* utwórz inny katalog o *nazwie data*. W tym miejscu będą tworzone i przechowywane pliki danych obiektów blob.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instalowanie pakietu

W katalogu aplikacji zainstaluj bibliotekę klienta Azure Blob Storage pakietu .NET przy użyciu `dotnet add package` polecenia .

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwórz *plik Program.cs* w edytorze.
1. Usuń `Console.WriteLine("Hello World!");` instrukcje .
1. Dodaj `using` dyrektywy.
1. Zaktualizuj deklarację `Main` metody w celu obsługi asynchronicznej.

    Oto kod:

    :::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/app_framework.cs":::

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektu

Azure Blob Storage jest zoptymalizowany pod kątem przechowywania ogromnych ilości danych bez struktury. Dane bez struktury są danymi, które nie są zgodne z żadnym modelem lub definicją danych, jak na przykład dane tekstowe lub binarne. Magazyn obiektów blob oferuje trzy typy zasobów:

* Konto magazynu
* Kontener na koncie magazynu
* Obiekt blob w kontenerze

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu obiektów blob](./media/storage-blobs-introduction/blob1.png)

Użyj następujących klas .NET, aby wchodzić w interakcje z tymi zasobami:

* [BlobServiceClient:](/dotnet/api/azure.storage.blobs.blobserviceclient)klasa umożliwia manipulowanie zasobami i kontenerami obiektów `BlobServiceClient` blob usługi Azure Storage.
* [BlobContainerClient:](/dotnet/api/azure.storage.blobs.blobcontainerclient)klasa umożliwia manipulowanie kontenerami `BlobContainerClient` usługi Azure Storage i ich obiektami blob.
* [BlobClient:](/dotnet/api/azure.storage.blobs.blobclient) `BlobClient` klasa umożliwia manipulowanie obiektami blob usługi Azure Storage.
* [BlobDownloadInfo:](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo) `BlobDownloadInfo` klasa reprezentuje właściwości i zawartość zwróconą podczas pobierania obiektu blob.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące czynności przy użyciu Azure Blob Storage klienta dla programu .NET:

* [Pobieranie parametrów połączenia](#get-the-connection-string)
* [Tworzenie kontenera](#create-a-container)
* [Przekazywanie obiektów blob do kontenera](#upload-blobs-to-a-container)
* [Wyświetlanie listy obiektów blob w kontenerze](#list-the-blobs-in-a-container)
* [Pobieranie obiektów blob](#download-blobs)
* [Usuwanie kontenera](#delete-a-container)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera ciąg połączenia dla konta magazynu ze zmiennej środowiskowej utworzonej w sekcji Konfigurowanie [parametrów połączenia](#configure-your-storage-connection-string) magazynu.

Dodaj ten kod wewnątrz `Main` metody :

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Tworzenie kontenera

Wybierz nazwę nowego kontenera. Poniższy kod dołącza wartość identyfikatora GUID do nazwy kontenera, aby upewnić się, że jest unikatowa.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać więcej informacji o nazewnictwie kontenerów i obiektów blob, zobacz temat [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Utwórz wystąpienie klasy [BlobServiceClient.](/dotnet/api/azure.storage.blobs.blobserviceclient) Następnie wywołaj [metodę CreateBlobContainerAsync,](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) aby utworzyć kontener na koncie magazynu.

Dodaj ten kod na końcu `Main` metody :

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Przekazywanie obiektów blob do kontenera

Poniższy fragment kodu:

1. Tworzy plik tekstowy w lokalnym *katalogu* danych.
1. Pobiera odwołanie do obiektu [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) przez wywołanie metody [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) w kontenerze z [sekcji Tworzenie kontenera.](#create-a-container)
1. Przekazanie lokalnego pliku tekstowego do obiektu blob przez wywołanie [metody UploadAsync.](/dotnet/api/azure.storage.blobs.blobclient.uploadasync#Azure_Storage_Blobs_BlobClient_UploadAsync_System_IO_Stream_System_Boolean_System_Threading_CancellationToken_) Jeśli obiekt blob nie istnieje, zostanie utworzony przez metodę. W przeciwnym razie zostanie zastąpiony.

Dodaj ten kod na końcu `Main` metody :

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Aby wyświetlić listę obiektów blob w kontenerze, wywołaj metodę [GetBlobsAsync.](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) W tym przypadku do kontenera został dodany tylko jeden obiekt blob, więc operacja listingowania zwraca tylko ten jeden obiekt blob.

Dodaj ten kod na końcu `Main` metody :

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Pobieranie obiektów blob

Pobierz utworzony wcześniej obiekt blob, wywołując [metodę DownloadAsync.](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync) Przykładowy kod dodaje sufiks "POBRANE" do nazwy pliku, aby można było zobaczyć oba pliki w lokalnym systemie plików.

Dodaj ten kod na końcu `Main` metody :

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Usuwanie kontenera

Poniższy kod czyści zasoby utworzone przez aplikację przez usunięcie całego kontenera przy użyciu narzędzia [DeleteAsync.](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteasync) Spowoduje to również usunięcie plików lokalnych utworzonych przez aplikację.

Aplikacja wstrzymuje wprowadzanie danych przez użytkownika przez wywołanie metody przed usunięciem obiektu `Console.ReadLine` blob, kontenera i plików lokalnych. Jest to dobra okazja do sprawdzenia, czy zasoby zostały faktycznie utworzone prawidłowo, zanim zostaną usunięte.

Dodaj ten kod na końcu `Main` metody :

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja utworzy plik testowy w lokalnym *folderze* danych i przekaże go do usługi Blob Storage. Następnie przykład wyświetla listę obiektów blob w kontenerze i pobiera plik z nową nazwą, aby można było porównać stare i nowe pliki.

Przejdź do katalogu aplikacji, a następnie skompilować i uruchomić aplikację.

```console
dotnet build
```

```console
dotnet run
```

Dane wyjściowe aplikacji są podobne do następującego przykładu:

```output
Azure Blob Storage v12 - .NET quickstart sample

Uploading to Blob storage as blob:
         https://mystorageacct.blob.core.windows.net/quickstartblobs60c70d78-8d93-43ae-954d-8322058cfd64/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Listing blobs...
        quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Downloading blob to
        ./data/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31DOWNLOADED.txt

Press any key to begin clean up
Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Przed rozpoczęciem procesu oczyszczania sprawdź, czy w *folderze danych* są dwa pliki. Możesz je otworzyć i sprawdzić, czy są identyczne.

Po zweryfikowaniu plików naciśnij klawisz **Enter,** aby usunąć pliki testowe i zakończyć pokaz.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano następujące czynności: przekazywanie, pobieranie i wyświetlanie listy obiektów blob przy użyciu platformy .NET.

Aby wyświetlić przykładowe aplikacje usługi Blob Storage, przejdź do:

> [!div class="nextstepaction"]
> [przykłady Azure Blob Storage .NET zestawu SDK w wersji 12](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* Samouczki, przykłady, krótkie starty i inne dokumenty można znaleźć na stronie Platformy Azure dla deweloperów platformy .NET i [.NET Core.](/dotnet/azure/)
* Aby dowiedzieć się więcej na temat platformy .NET Core, zobacz [Get started with .NET in 10 minutes (Rozpoczynanie pracy z platformą .NET w 10 minut)](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/intro).
