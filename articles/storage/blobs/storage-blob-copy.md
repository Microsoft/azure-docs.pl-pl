---
title: Kopiowanie obiektu BLOB za pomocą platformy .NET — Azure Storage
description: Dowiedz się, jak skopiować obiekt BLOB na koncie usługi Azure Storage za pomocą biblioteki klienckiej platformy .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 144ad1a2f9d9b94deca1462d217aa16baca75846
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018896"
---
# <a name="copy-a-blob-with-net"></a>Kopiowanie obiektu BLOB za pomocą platformy .NET

W tym artykule przedstawiono sposób kopiowania obiektu BLOB za pomocą konta usługi Azure Storage. Pokazuje również, jak przerwać asynchroniczne operacje kopiowania. Przykładowy kod używa [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-copying-blobs"></a>Informacje o kopiowaniu obiektów BLOB

W przypadku kopiowania obiektu BLOB w ramach tego samego konta magazynu jest to operacja synchroniczna. Podczas kopiowania między kontami jest to operacja asynchroniczna. Metody [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) i [STARTCOPYASYNC](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) zwracają wartość identyfikatora kopii, która jest używana do sprawdzania stanu lub przerywania operacji kopiowania.

Źródłowy obiekt BLOB operacji kopiowania może być blokowym obiektem BLOB, obiektem BLOB, obiektem BLOB lub migawką. Jeśli docelowy obiekt BLOB już istnieje, musi być tego samego typu obiektów BLOB co źródłowy obiekt BLOB. Istniejący docelowy obiekt BLOB zostanie nadpisany.

Nie można zmodyfikować docelowego obiektu BLOB, gdy operacja kopiowania jest w toku. Docelowy obiekt BLOB może mieć tylko jedną zaległą operację kopiowania obiektu BLOB. Innymi słowy, obiekt BLOB nie może być miejscem docelowym dla wielu oczekujących operacji kopiowania.

Cały źródłowy obiekt BLOB lub plik jest zawsze kopiowany. Kopiowanie zakresu bajtów lub zestawu bloków nie jest obsługiwane.

Po skopiowaniu obiektu BLOB właściwości systemu są kopiowane do docelowego obiektu BLOB z tymi samymi wartościami.

Dla wszystkich typów obiektów BLOB można sprawdzić Właściwość [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) w docelowym obiekcie blob, aby uzyskać stan operacji kopiowania. Końcowy obiekt BLOB zostanie zatwierdzony po zakończeniu kopiowania.

Operacja kopiowania może przyjmować jedną z następujących form:

- Można skopiować źródłowy obiekt BLOB do docelowego obiektu BLOB z inną nazwą. Docelowy obiekt BLOB może być istniejącym obiektem BLOB tego samego typu obiektów BLOB (blok, dołączanie lub strona) lub może być nowym obiektem BLOB utworzonym przez operację kopiowania.
- Można skopiować źródłowy obiekt BLOB do docelowego obiektu BLOB o takiej samej nazwie, co skutecznie zastępuje docelowy obiekt BLOB. Taka operacja kopiowania usuwa wszystkie niezatwierdzone bloki i zastępuje metadane docelowego obiektu BLOB.
- Plik źródłowy można skopiować do docelowego obiektu BLOB za pomocą usługi plików platformy Azure. Docelowy obiekt BLOB może być istniejącym blokowym obiektem BLOB lub może być nowym blokowym obiektem BLOB utworzonym przez operację kopiowania. Kopiowanie plików do stronicowych obiektów blob lub dołączanie obiektów BLOB nie jest obsługiwane.
- Migawkę można skopiować za pomocą jej podstawowego obiektu BLOB. Podwyższanie poziomu migawki do pozycji podstawowego obiektu BLOB pozwala przywrócić wcześniejszą wersję obiektu BLOB.
- Migawkę można skopiować do docelowego obiektu BLOB z inną nazwą. Otrzymany docelowy obiekt BLOB jest zapisywalnym obiektem BLOB, a nie z migawką.

## <a name="copy-a-blob"></a>Kopiowanie obiektu BLOB

Aby skopiować obiekt BLOB, wywołaj jedną z następujących metod:

- [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
- [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

Poniższy przykład kodu pobiera odwołanie do obiektu BLOB utworzonego wcześniej i kopiuje go do nowego obiektu BLOB w tym samym kontenerze:

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
            Console.WriteLine();
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

## <a name="abort-a-blob-copy-operation"></a>Przerwij operację kopiowania obiektu BLOB

Przerwanie operacji kopiowania powoduje, że docelowy obiekt BLOB o zerowej długości dla blokowych obiektów blob, Dołącz obiekty blob i stronicowe obiekty blob. Jednak metadane dla docelowego obiektu BLOB będą mieć skopiowane nowe wartości ze źródłowego obiektu BLOB lub ustawione jawnie w wywołaniu [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) lub [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) . Aby zachować oryginalne metadane przed kopią, wykonaj migawkę docelowego obiektu BLOB przed wywołaniem `StartCopy` lub `StartCopyAsync` .

Gdy przerywasz trwającą operację kopiowania obiektów blob, [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) docelowego obiektu BLOB jest ustawiony na [CopyStatus. przerwany](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet).

Metody [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) i [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) anulują trwającą operację kopiowania obiektów blob i opuszczają docelowy obiekt BLOB o zerowej długości i pełnych metadanych.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Kolejne kroki

Poniższe tematy zawierają informacje o kopiowaniu obiektów blob i przerywaniu trwających operacji kopiowania przy użyciu interfejsów API REST platformy Azure.

- [Kopiowanie obiektu blob](/rest/api/storageservices/copy-blob)
- [Przerwij Kopiowanie obiektu BLOB](/rest/api/storageservices/abort-copy-blob)
