---
title: Wprowadzenie do usługi BLOB Storage przy użyciu programu Visual Studio (ASP.NET Core)
description: Jak rozpocząć pracę z usługą Azure Blob Storage w projekcie programu Visual Studio ASP.NET Core po utworzeniu konta magazynu przy użyciu usług połączonych programu Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 81df41470c893f569fd17345e8bdf4b29641ec64
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "72298833"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Rozpoczynanie pracy z usługą Azure Blob Storage i usługami połączonymi programu Visual Studio (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

W tym artykule opisano, jak rozpocząć korzystanie z usługi Azure Blob Storage w programie Visual Studio po utworzeniu lub odwołaniu się do konta usługi Azure Storage w projekcie ASP.NET Core przy użyciu funkcji **usługi połączonej** programu Visual Studio. Operacja **połączone usługi** instaluje odpowiednie pakiety NuGet w celu uzyskania dostępu do usługi Azure Storage w projekcie i dodaje parametry połączenia dla konta magazynu do plików konfiguracji projektu. (Zobacz [dokumentację magazynu](https://azure.microsoft.com/documentation/services/storage/) , aby uzyskać ogólne informacje na temat usługi Azure Storage).

Azure Blob Storage to usługa służąca do przechowywania dużych ilości danych bez struktury, do których można uzyskiwać dostęp z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Pojedynczy obiekt BLOB może być dowolnym rozmiarem. Obiekty blob mogą być takie jak obrazy, pliki audio i wideo, dane pierwotne i pliki dokumentów. W tym artykule opisano sposób rozpoczynania pracy z usługą BLOB Storage po utworzeniu konta usługi Azure Storage przy użyciu **usług połączonych** programu Visual Studio w projekcie ASP.NET Core.

Równie jak pliki aktywne w folderach, Magazyn obiektów BLOB na żywo w kontenerach. Po utworzeniu obiektu BLOB utworzysz jeden lub więcej kontenerów w tym obiekcie blob. Na przykład w obiekcie blob o nazwie "album" można utworzyć kontenery o nazwie "images" do przechowywania obrazów i inne o nazwie "audio" do przechowywania plików audio. Po utworzeniu kontenerów można przekazać do nich poszczególne pliki. Zobacz [Szybki Start: przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB przy użyciu platformy .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md) , aby uzyskać więcej informacji na temat programistycznego manipulowania obiektami BLOB.

Niektóre interfejsy API usługi Azure Storage są asynchroniczne, a w kodzie w tym artykule przyjęto, że metody asynchroniczne są używane. Aby uzyskać więcej informacji, zobacz [programowanie asynchroniczne](https://docs.microsoft.com/dotnet/csharp/async) .

## <a name="access-blob-containers-in-code"></a>Dostęp do kontenerów obiektów BLOB w kodzie

Aby programowo uzyskać dostęp do obiektów BLOB w projektach ASP.NET Core, należy dodać następujący kod, jeśli jeszcze nie istnieje:

1. Dodaj wymagane `using` instrukcje:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Pobierz `CloudStorageAccount` obiekt reprezentujący informacje o koncie magazynu. Użyj poniższego kodu, aby uzyskać parametry połączenia magazynu i informacje o koncie magazynu z konfiguracji usługi platformy Azure:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Użyj `CloudBlobClient` obiektu, aby pobrać `CloudBlobContainer` odwołanie do istniejącego kontenera na koncie magazynu:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Tworzenie kontenera w kodzie

Możesz również użyć, `CloudBlobClient` aby utworzyć kontener na koncie magazynu, wywołując: `CreateIfNotExistsAsync`

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Aby pliki w kontenerze były dostępne dla wszystkich, ustaw kontener jako publiczny:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Przekazywanie obiektu blob do kontenera

Aby przekazać plik obiektu BLOB do kontenera, Pobierz odwołanie do kontenera i użyj go, aby uzyskać odwołanie do obiektu BLOB. Następnie Przekaż dowolny strumień danych do tego odwołania, wywołując `UploadFromStreamAsync` metodę. Ta operacja tworzy obiekt BLOB, jeśli jeszcze nie istnieje, i zastępuje istniejący obiekt BLOB. 

```cs
// Get a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with the contents of a local file
// named "myfile".
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Aby wyświetlić listę obiektów BLOB w kontenerze, najpierw Uzyskaj odwołanie do kontenera, a następnie `ListBlobsSegmentedAsync` Wywołaj metodę, aby pobrać obiekty blob i/lub znajdujące się w niej katalogi. Aby uzyskać `IListBlobItem`dostęp do bogatego zestawu właściwości i metod dla zwracanego elementu, przerzutowanie go na obiekt `CloudBlockBlob`, `CloudPageBlob`lub. `CloudBlobDirectory` Jeśli nie znasz typu obiektu BLOB, użyj sprawdzenia typu, aby określić, które z nich rzutować.

```cs
BlobContinuationToken token = null;
do
{
    BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
    token = resultSegment.ContinuationToken;

    foreach (IListBlobItem item in resultSegment.Results)
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
        }

        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
        }

        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }
} while (token != null);
```

Zobacz [Szybki Start: przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB przy użyciu platformy .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) , aby poznać inne sposoby wyświetlania zawartości kontenera obiektów BLOB.

## <a name="download-a-blob"></a>Pobieranie obiektu blob

Aby pobrać obiekt BLOB, należy najpierw pobrać odwołanie do obiektu BLOB, a następnie wywołać `DownloadToStreamAsync` metodę. W poniższym przykładzie zastosowano `DownloadToStreamAsync` metodę transferu zawartości obiektu BLOB do obiektu strumienia, który można następnie zapisać jako plik lokalny.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Zobacz [Szybki Start: przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB przy użyciu platformy .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) , aby uzyskać inne sposoby zapisywania obiektów BLOB jako plików.

## <a name="delete-a-blob"></a>Usuwanie obiektu blob

Aby usunąć obiekt BLOB, należy najpierw pobrać odwołanie do obiektu BLOB, a następnie wywołać `DeleteAsync` metodę:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
