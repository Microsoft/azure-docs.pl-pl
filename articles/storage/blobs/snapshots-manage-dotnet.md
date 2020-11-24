---
title: Tworzenie migawki obiektu BLOB w programie .NET i zarządzanie nią
titleSuffix: Azure Storage
description: Dowiedz się, jak używać biblioteki klienckiej .NET do tworzenia migawki obiektu BLOB w trybie tylko do odczytu w celu utworzenia kopii zapasowej danych obiektu BLOB w danym momencie.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ae1d72c30fb7c45c92f24ec5132859a45c6ae953
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95543378"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Tworzenie migawki obiektu BLOB w programie .NET i zarządzanie nią

Migawka to wersja obiektu BLOB tylko do odczytu, która jest wykonywana w danym momencie. W tym artykule przedstawiono sposób tworzenia migawek obiektów blob i zarządzania nimi przy użyciu [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage).

Aby uzyskać więcej informacji na temat migawek obiektów BLOB w usłudze Azure Storage, zobacz [migawki obiektów BLOB](snapshots-overview.md).

## <a name="create-a-snapshot"></a>Tworzenie migawki

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Aby utworzyć migawkę blokowego obiektu BLOB przy użyciu wersji 12. x biblioteki klienta usługi Azure Storage dla platformy .NET, należy użyć jednej z następujących metod:

- [Utwórz migawkę](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshotasync)

Poniższy przykład kodu pokazuje, jak utworzyć migawkę z wersją 12. x. Dołącz odwołanie do biblioteki [Azure. Identity](https://www.nuget.org/packages/azure.identity) , aby użyć poświadczeń usługi Azure AD w celu autoryzowania żądań do usługi.

```csharp
private static async Task CreateBlockBlobSnapshot(string accountName, string containerName, string blobName, Stream data)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri containerUri = new Uri("https://" + accountName + blobServiceEndpointSuffix + "/" + containerName);

    // Get a container client object and create the container.
    BlobContainerClient containerClient = new BlobContainerClient(containerUri,
        new DefaultAzureCredential());
    await containerClient.CreateIfNotExistsAsync();

    // Get a blob client object.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Upload text to create a block blob.
        await blobClient.UploadAsync(data);

        // Add blob metadata.
        IDictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "ApproxBlobCreatedDate", DateTime.UtcNow.ToString() },
            { "FileType", "text" }
        };
        await blobClient.SetMetadataAsync(metadata);

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        await blobClient.CreateSnapshotAsync();
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Aby utworzyć migawkę blokowego obiektu BLOB przy użyciu wersji 11. x biblioteki klienta usługi Azure Storage dla platformy .NET, należy użyć jednej z następujących metod:

- [Utwórz migawkę](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

Poniższy przykład kodu pokazuje, jak utworzyć migawkę z wersją 11. x. Ten przykład określa dodatkowe metadane podczas tworzenia migawki.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        Console.WriteLine(snapshot.SnapshotQualifiedStorageUri.PrimaryUri);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

## <a name="delete-snapshots"></a>Usuwanie migawek

Aby usunąć obiekt BLOB, należy najpierw usunąć wszystkie migawki tego obiektu BLOB. Migawkę można usunąć pojedynczo lub określić, że wszystkie migawki zostaną usunięte po usunięciu źródłowego obiektu BLOB. Jeśli spróbujesz usunąć obiekt BLOB, który nadal zawiera migawki, zostanie zwrócony błąd.

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Aby usunąć obiekt BLOB i jego migawki przy użyciu wersji 12. x biblioteki klienta usługi Azure Storage dla platformy .NET, należy użyć jednej z następujących metod i uwzględnić Wyliczenie [DeleteSnapshotsOption](/dotnet/api/azure.storage.blobs.models.deletesnapshotsoption) :

- [Usuwanie](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexistsasync)

Poniższy przykład kodu pokazuje, jak usunąć obiekt BLOB i jego migawki w programie .NET, gdzie `blobClient` jest obiekt typu [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)):

```csharp
await blobClient.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, default);
```

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Aby usunąć obiekt BLOB i jego migawki przy użyciu wersji 11. x biblioteki klienta usługi Azure Storage dla platformy .NET, należy użyć jednej z następujących metod usuwania obiektów blob i uwzględnić Wyliczenie [DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) :

- [Usuwanie](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

Poniższy przykład kodu pokazuje, jak usunąć obiekt BLOB i jego migawki w programie .NET, gdzie `blockBlob` jest obiektem typu [CloudBlockBlob] [dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

---

## <a name="next-steps"></a>Następne kroki

- [Migawki obiektów BLOB](snapshots-overview.md)
- [Wersje obiektów BLOB](versioning-overview.md)
- [Usuwanie nietrwałe dla obiektów blob](./soft-delete-blob-overview.md)