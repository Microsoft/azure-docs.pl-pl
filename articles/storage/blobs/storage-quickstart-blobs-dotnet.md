---
title: 'Szybki Start: Azure Blob Storage Library V12 — .NET'
description: W tym przewodniku szybki start dowiesz się, jak używać biblioteki klienckiej usługi Azure Blob Storage w wersji 12 dla platformy .NET do tworzenia kontenera i obiektu BLOB w magazynie obiektów BLOB (Object). Następnie dowiesz się, jak pobrać obiekt blob na komputer lokalny i jak wyświetlać listę wszystkich obiektów blob w kontenerze.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/03/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: bb26a865ab8b8beba99fcba51e2d05e166b1e84b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095361"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Szybki Start: V12 biblioteki klienta Blob Storage platformy Azure dla platformy .NET

Rozpocznij pracę z biblioteką klienta Blob Storage platformy Azure V12 dla platformy .NET. Usługa Azure Blob Storage to rozwiązanie magazynu obiektów firmy Microsoft dla chmury. Postępuj zgodnie z instrukcjami, aby zainstalować pakiet, i wypróbuj przykładowy kod dla podstawowych zadań. Magazyn obiektów blob jest zoptymalizowany pod kątem przechowywania olbrzymich ilości danych bez struktury.

Użyj usługi Azure Blob Storage Client Library V12 for .NET, aby:

* Tworzenie kontenera
* Przekazywanie obiektu blob do usługi Azure Storage
* Wyświetl listę wszystkich obiektów BLOB w kontenerze
* Pobieranie obiektu BLOB na komputer lokalny
* Usuwanie kontenera

Dodatkowe zasoby:

* [Dokumentacja referencyjna interfejsu API](/dotnet/api/azure.storage.blobs)
* [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [Samples](../common/storage-samples-dotnet.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Konto magazynu platformy Azure — [Tworzenie konta magazynu](../common/storage-account-create.md)
* Bieżąca [zestaw .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) dla danego systemu operacyjnego. Pamiętaj, aby pobrać zestaw SDK, a nie środowisko uruchomieniowe.

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji omówiono przygotowanie projektu do pracy z usługą Azure Blob Storage Client Library V12 for .NET.

### <a name="create-the-project"></a>Tworzenie projektu

Utwórz aplikację platformy .NET Core o nazwie *BlobQuickstartV12*.

1. W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie *BlobQuickstartV12*. To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: *program. cs*.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Przejdź do nowo utworzonego katalogu *BlobQuickstartV12* .

   ```console
   cd BlobQuickstartV12
   ```

1. Na stronie katalogu *BlobQuickstartV12* Utwórz inny katalog o nazwie *dane*. Jest to miejsce, w którym są tworzone i przechowywane pliki danych obiektów BLOB.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Zainstaluj pakiet

Gdy nadal znajduje się w katalogu aplikacji, zainstaluj pakiet Azure Blob Storage Client Library for .NET przy użyciu `dotnet add package` polecenia.

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwórz plik *program. cs* w edytorze.
1. Usuń `Console.WriteLine("Hello World!");` instrukcję.
1. Dodaj `using` dyrektywy.
1. Zaktualizuj `Main` deklarację metody, aby obsługiwała asynchroniczne.

    Oto kod:

    :::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/app_framework.cs":::

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektów

Usługa Azure Blob Storage jest zoptymalizowana pod kątem przechowywania dużych ilości danych bez struktury. Dane bez struktury są danymi, które nie są zgodne z żadnym modelem lub definicją danych, jak na przykład dane tekstowe lub binarne. Magazyn obiektów blob oferuje trzy typy zasobów:

* Konto magazynu
* Kontener na koncie magazynu
* Obiekt BLOB w kontenerze

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu obiektów blob](./media/storage-blobs-introduction/blob1.png)

Użyj następujących klas platformy .NET do korzystania z tych zasobów:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): `BlobServiceClient` Klasa umożliwia manipulowanie zasobami usługi Azure Storage i kontenerami obiektów BLOB.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): `BlobContainerClient` Klasa umożliwia manipulowanie kontenerami usługi Azure Storage i ich obiektami BLOB.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): `BlobClient` Klasa umożliwia manipulowanie obiektami BLOB usługi Azure Storage.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): `BlobDownloadInfo` Klasa reprezentuje właściwości i zawartość zwróconą przez pobranie obiektu BLOB.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta Blob Storage platformy Azure dla platformy .NET:

* [Pobieranie parametrów połączenia](#get-the-connection-string)
* [Tworzenie kontenera](#create-a-container)
* [Przekazywanie obiektów BLOB do kontenera](#upload-blobs-to-a-container)
* [Wyświetlanie listy obiektów blob w kontenerze](#list-the-blobs-in-a-container)
* [Pobieranie obiektów blob](#download-blobs)
* [Usuwanie kontenera](#delete-a-container)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera parametry połączenia dla konta magazynu ze zmiennej środowiskowej utworzonej w sekcji [Konfigurowanie parametrów połączenia magazynu](#configure-your-storage-connection-string) .

Dodaj ten kod wewnątrz `Main` metody:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Tworzenie kontenera

Określ nazwę nowego kontenera. Poniższy kod dołącza wartość identyfikatora GUID do nazwy kontenera, aby upewnić się, że jest ona unikatowa.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać więcej informacji o nazewnictwie kontenerów i obiektów blob, zobacz temat [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Utwórz wystąpienie klasy [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) . Następnie Wywołaj metodę [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) , aby utworzyć kontener na koncie magazynu.

Dodaj ten kod na końcu `Main` metody:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Przekazywanie obiektów BLOB do kontenera

Następujący fragment kodu:

1. Tworzy plik tekstowy w lokalnym katalogu *danych* .
1. Pobiera odwołanie do obiektu [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) przez wywołanie metody [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) kontenera z sekcji [Create a Container](#create-a-container) .
1. Przekazuje lokalny plik tekstowy do obiektu BLOB, wywołując metodę [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync#Azure_Storage_Blobs_BlobClient_UploadAsync_System_IO_Stream_System_Boolean_System_Threading_CancellationToken_) . Jeśli obiekt blob nie istnieje, zostanie utworzony przez metodę. W przeciwnym razie zostanie zastąpiony.

Dodaj ten kod na końcu `Main` metody:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Utwórz listę obiektów BLOB w kontenerze, wywołując metodę [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) . W takim przypadku tylko jeden obiekt BLOB został dodany do kontenera, więc operacja tworzenia listy zwraca tylko jeden obiekt BLOB.

Dodaj ten kod na końcu `Main` metody:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Pobieranie obiektów blob

Pobierz utworzony wcześniej obiekt BLOB, wywołując metodę [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) . Przykładowy kod dodaje sufiks "pobrane" do nazwy pliku, aby można było zobaczyć oba pliki w lokalnym systemie plików.

Dodaj ten kod na końcu `Main` metody:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Usuwanie kontenera

Poniższy kod czyści zasoby utworzone przez aplikację przez usunięcie całego kontenera przy użyciu [DeleteAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteasync). Powoduje również usunięcie plików lokalnych utworzonych przez aplikację.

Aplikacja wstrzymuje się do wprowadzania danych przez użytkownika `Console.ReadLine` , wywołując przed usunięciem obiektu BLOB, kontenera i plików lokalnych. Jest to dobry szansa, aby sprawdzić, czy zasoby zostały prawidłowo utworzone, zanim zostaną usunięte.

Dodaj ten kod na końcu `Main` metody:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy plik testowy w lokalnym folderze *danych* i przekazuje go do magazynu obiektów BLOB. Przykład następnie wyświetla listę obiektów BLOB w kontenerze i pobiera plik z nową nazwą, aby można było porównać stare i nowe pliki.

Przejdź do katalogu aplikacji, a następnie Skompiluj i uruchom aplikację.

```console
dotnet build
```

```console
dotnet run
```

Dane wyjściowe aplikacji są podobne do następujących:

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

Przed rozpoczęciem procesu oczyszczania Sprawdź, czy w folderze *danych* są dwa pliki. Możesz je otworzyć i sprawdzić, czy są identyczne.

Po zweryfikowaniu plików naciśnij klawisz **Enter** , aby usunąć pliki testowe i zakończyć demonstrację.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano następujące czynności: przekazywanie, pobieranie i wyświetlanie listy obiektów blob przy użyciu platformy .NET.

Aby wyświetlić przykładowe aplikacje dla magazynu obiektów blob, przejdź do:

> [!div class="nextstepaction"]
> [Przykłady usługi Azure Blob Storage SDK V12 .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* Samouczki, przykłady, Przewodniki Szybki Start i inne dokumenty można znaleźć w witrynie [Azure dla deweloperów .NET i .NET Core](/dotnet/azure/).
* Aby dowiedzieć się więcej na temat platformy .NET Core, zobacz [Get started with .NET in 10 minutes (Rozpoczynanie pracy z platformą .NET w 10 minut)](https://www.microsoft.com/net/learn/get-started/).
