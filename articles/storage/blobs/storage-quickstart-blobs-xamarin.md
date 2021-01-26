---
title: 'Szybki Start: Azure Blob Storage Library V12 — Xamarin'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć kontener i obiekt BLOB w magazynie obiektów BLOB (Object) przy użyciu biblioteki klienta Blob Storage platformy Azure w wersji 12 z platformą Xamarin. Następnie dowiesz się, jak pobrać obiekt BLOB do urządzenia przenośnego i jak wyświetlić listę wszystkich obiektów BLOB w kontenerze.
author: codemillmatt
ms.author: masoucou
ms.date: 10/09/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: dab938a526aa89f6fe5a014e10869bd8da8b475d
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98802363"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-with-xamarin"></a>Szybki Start: V12 biblioteki klienta platformy Azure z Blob Storage platformą Xamarin

Rozpocznij pracę z biblioteką klienta usługi Azure Blob Storage V12 przy użyciu platformy Xamarin. Usługa Azure Blob Storage to rozwiązanie magazynu obiektów firmy Microsoft dla chmury. Postępuj zgodnie z instrukcjami, aby zainstalować pakiet, i wypróbuj przykładowy kod dla podstawowych zadań. Magazyn obiektów blob jest zoptymalizowany pod kątem przechowywania olbrzymich ilości danych bez struktury.

Użyj usługi Azure Blob Storage Client Library V12 z platformą Xamarin, aby:

* Tworzenie kontenera
* Przekazywanie obiektu blob do usługi Azure Storage
* Wyświetl listę wszystkich obiektów BLOB w kontenerze
* Pobieranie obiektu BLOB do urządzenia
* Usuwanie kontenera

Linki odwołań:

* [Dokumentacja referencyjna interfejsu API](/dotnet/api/azure.storage.blobs)
* [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [Przykład](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Konto magazynu platformy Azure — [Tworzenie konta magazynu](../common/storage-account-create.md)
* Visual Studio z [programowaniem dla urządzeń przenośnych na potrzeby obciążeń .NET](/xamarin/get-started/installation/?pivots=windows) lub [Visual Studio dla komputerów Mac](/visualstudio/mac/installation?view=vsmac-2019)

## <a name="setting-up"></a>Konfigurowanie
    
W tej sekcji omówiono przygotowywanie projektu do pracy z usługą Azure Blob Storage Client Library V12 z platformą Xamarin.
    
### <a name="create-the-project"></a>Tworzenie projektu

1. Otwórz program Visual Studio i Utwórz pustą aplikację Forms.
1. Nadaj mu nazwę: BlobQuickstartV12

### <a name="install-the-package"></a>Zainstaluj pakiet

1. W okienku Eksplorator rozwiązań kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania**.
1. Wyszukaj **platformę Azure. Storage. blob** i zainstaluj najnowszą stabilną wersję we wszystkich projektach w rozwiązaniu.

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu **BlobQuickstartV12** :

1. Otwórz plik *MainPage. XAML* w edytorze
1. Usuń wszystko między `<ContentPage></ContentPage>` elementami i zastąp je poniższymi:

```xaml
<StackLayout HorizontalOptions="Center" VerticalOptions="Center">

    <Button x:Name="uploadButton" Text="Upload Blob" Clicked="Upload_Clicked"  IsEnabled="False"/>
    <Button x:Name="listButton" Text="List Blobs" Clicked="List_Clicked"  IsEnabled="False" />
    <Button x:Name="downloadButton" Text="Download Blob" Clicked="Download_Clicked"  IsEnabled="False" />
    <Button x:Name="deleteButton" Text="Delete Container" Clicked="Delete_Clicked" IsEnabled="False" />

    <Label Text="" x:Name="resultsLabel" HorizontalTextAlignment="Center" Margin="0,20,0,0" TextColor="Red" />
        
</StackLayout>
```

[!INCLUDE [storage-quickstart-credentials-xamarin-include](../../../includes/storage-quickstart-credentials-xamarin-include.md)]

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

Te przykładowe fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienta Blob Storage platformy Azure dla platformy .NET w aplikacji Xamarin. Forms:

* [Utwórz zmienne na poziomie klasy](#create-class-level-variables)
* [Tworzenie kontenera](#create-a-container)
* [Przekazywanie obiektów BLOB do kontenera](#upload-blobs-to-a-container)
* [Wyświetlanie listy obiektów blob w kontenerze](#list-the-blobs-in-a-container)
* [Pobieranie obiektów blob](#download-blobs)
* [Usuwanie kontenera](#delete-a-container)

### <a name="create-class-level-variables"></a>Utwórz zmienne na poziomie klasy

Poniższy kod deklaruje kilka zmiennych na poziomie klasy. Muszą oni komunikować się z usługą Azure Blob Storage w całej pozostałej części tego przykładu.

Są to oprócz parametrów połączenia dla konta magazynu ustawionych w sekcji [Konfigurowanie parametrów połączenia magazynu](#configure-your-storage-connection-string) .

Dodaj ten kod jako zmienne poziomu klasy w pliku *MainPage.XAML.cs* :

```csharp
string storageConnectionString = "{set in the Configure your storage connection string section}";
string fileName = $"{Guid.NewGuid()}-temp.txt";

BlobServiceClient client;
BlobContainerClient containerClient;
BlobClient blobClient;
```

### <a name="create-a-container"></a>Tworzenie kontenera

Określ nazwę nowego kontenera. Poniższy kod dołącza wartość identyfikatora GUID do nazwy kontenera, aby upewnić się, że jest ona unikatowa.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać więcej informacji o nazewnictwie kontenerów i obiektów blob, zobacz temat [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Utwórz wystąpienie klasy [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) . Następnie Wywołaj metodę [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) , aby utworzyć kontener na koncie magazynu.

Dodaj ten kod do pliku *MainPage.XAML.cs* :

```csharp
protected async override void OnAppearing()
{            
    string containerName = $"quickstartblobs{Guid.NewGuid()}";
    
    client = new BlobServiceClient(storageConnectionString);
    containerClient = await client.CreateBlobContainerAsync(containerName);

    resultsLabel.Text = "Container Created\n";

    blobClient = containerClient.GetBlobClient(fileName);

    uploadButton.IsEnabled = true;
}
```

### <a name="upload-blobs-to-a-container"></a>Przekazywanie obiektów BLOB do kontenera

Następujący fragment kodu:

1. Tworzy `MemoryStream` tekst.
1. Przekazuje tekst do obiektu BLOB, wywołując funkcję [UploadAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.uploadblobasync#Azure_Storage_Blobs_BlobContainerClient_UploadBlobAsync_System_String_System_IO_Stream_System_Threading_CancellationToken_) klasy [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) , przekazując ją zarówno do nazwy pliku, jak i `MemoryStream` tekstu. Jeśli obiekt blob nie istnieje, zostanie utworzony przez metodę. W przeciwnym razie zostanie zastąpiony.

Dodaj ten kod do pliku *MainPage.XAML.cs* :

```csharp
async void Upload_Clicked(object sender, EventArgs e)
{                                    
    using MemoryStream memoryStream = new MemoryStream(Encoding.UTF8.GetBytes("Hello World!"));

    await containerClient.UploadBlobAsync(fileName, memoryStream);

    resultsLabel.Text += "Blob Uploaded\n";

    uploadButton.IsEnabled = false;
    listButton.IsEnabled = true;
}
```

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Utwórz listę obiektów BLOB w kontenerze, wywołując metodę [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) . W takim przypadku tylko jeden obiekt BLOB został dodany do kontenera, więc operacja tworzenia listy zwraca tylko jeden obiekt BLOB.

Dodaj ten kod do pliku *MainPage.XAML.cs* :

```csharp
async void List_Clicked(object sender, EventArgs e)
{            
    await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
    {
        resultsLabel.Text += blobItem.Name + "\n";                
    }

    listButton.IsEnabled = false;
    downloadButton.IsEnabled = true;
}
```

### <a name="download-blobs"></a>Pobieranie obiektów blob

Pobierz utworzony wcześniej obiekt BLOB, wywołując metodę [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) . Przykładowy kod kopiuje `Stream` reprezentację obiektu BLOB najpierw do `MemoryStream` a, a następnie do, `StreamReader` Aby można było wyświetlić tekst.

Dodaj ten kod do pliku *MainPage.XAML.cs* :

```csharp
async void Download_Clicked(object sender, EventArgs e)
{
    BlobDownloadInfo downloadInfo = await blobClient.DownloadAsync();

    using MemoryStream memoryStream = new MemoryStream();
    
    await downloadInfo.Content.CopyToAsync(memoryStream);
    memoryStream.Position = 0;

    using StreamReader streamReader = new StreamReader(memoryStream);

    resultsLabel.Text += "Blob Contents: \n";
    resultsLabel.Text += await streamReader.ReadToEndAsync();
    resultsLabel.Text += "\n";

    downloadButton.IsEnabled = false;
    deleteButton.IsEnabled = true;
}
```

### <a name="delete-a-container"></a>Usuwanie kontenera

Poniższy kod czyści zasoby utworzone przez aplikację przez usunięcie całego kontenera przy użyciu [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync).

Aplikacja najpierw wyświetli prośbę o potwierdzenie przed usunięciem obiektu BLOB i kontenera. Jest to dobry szansa, aby sprawdzić, czy zasoby zostały utworzone prawidłowo, przed ich usunięciem.

Dodaj ten kod do pliku *MainPage.XAML.cs* :

```csharp
async void Delete_Clicked(object sender, EventArgs e)
{            
    var deleteContainer = await Application.Current.MainPage.DisplayAlert("Delete Container",
        "You are about to delete the container proceeed?", "OK", "Cancel");

    if (deleteContainer == false)
        return;

    await containerClient.DeleteAsync();

    resultsLabel.Text += "Container Deleted";

    deleteButton.IsEnabled = false;
}
```

## <a name="run-the-code"></a>Uruchamianie kodu

Gdy aplikacja zostanie uruchomiona, najpierw utworzy kontener w postaci, w jakiej jest wyświetlany. Następnie należy kliknąć przyciski, aby przekazać, wyświetlić, pobrać obiekty blob i usunąć kontener.

Aby uruchomić aplikację w systemie Windows, naciśnij klawisz F5. Aby uruchomić aplikację na komputerze Mac, naciśnij klawisze cmd + ENTER.

Aplikacja zapisuje się na ekranie po każdej operacji. Dane wyjściowe aplikacji są podobne do poniższego przykładu:

```output
Container Created
Blob Uploaded
98d9a472-8e98-4978-ba4f-081d69d2e6f8-temp.txt
Blob Contents:
Hello World!
Container Deleted
```

Przed rozpoczęciem procesu oczyszczania Sprawdź, czy dane wyjściowe zawartości obiektu BLOB na ekranie pasują do wartości, która została przekazana.

Po zweryfikowaniu wartości Potwierdź monit o usunięcie kontenera i Zakończ pokaz.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób przekazywania, pobierania i wyświetlania listy obiektów BLOB za pomocą usługi Azure Blob Storage Client Library V12 z platformą Xamarin.

Aby wyświetlić przykładowe aplikacje dla magazynu obiektów blob, przejdź do:

> [!div class="nextstepaction"]
> [Przykład platformy Azure Blob Storage SDK V12 Xamarin](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

* Samouczki, przykłady, Przewodniki Szybki Start i inne dokumenty można znaleźć w witrynie [Azure for Mobile Developers](/azure/mobile-apps).
* Aby dowiedzieć się więcej na temat platformy Xamarin, zobacz [wprowadzenie do platformy Xamarin](/xamarin/get-started/).
