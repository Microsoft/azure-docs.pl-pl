---
title: Kopiowanie obiektu BLOB za pomocą interfejsów API usługi Azure Storage
description: Dowiedz się, jak skopiować obiekt BLOB przy użyciu bibliotek klienckich usługi Azure Storage.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/08/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 28b23555f97e58dde038c5e3e12a4b741b81776d
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2021
ms.locfileid: "98059045"
---
# <a name="copy-a-blob-with-azure-storage-client-libraries"></a>Kopiowanie obiektu BLOB za pomocą bibliotek klienckich usługi Azure Storage

W tym artykule przedstawiono sposób kopiowania obiektu BLOB na koncie usługi Azure Storage. Pokazuje również, jak przerwać asynchroniczne operacje kopiowania. Przykładowy kod używa bibliotek klienckich usługi Azure Storage.

## <a name="about-copying-blobs"></a>Informacje o kopiowaniu obiektów BLOB

W przypadku kopiowania obiektu BLOB w ramach tego samego konta magazynu jest to operacja synchroniczna. Kopiowanie między kontami jest operacją asynchroniczną.

Źródłowy obiekt BLOB operacji kopiowania może być blokowym obiektem BLOB, obiektem BLOB, obiektem BLOB lub migawką. Jeśli docelowy obiekt BLOB już istnieje, musi być tego samego typu obiektów BLOB co źródłowy obiekt BLOB. Istniejący docelowy obiekt BLOB zostanie nadpisany.

Nie można zmodyfikować docelowego obiektu BLOB, gdy operacja kopiowania jest w toku. Docelowy obiekt BLOB może mieć tylko jedną zaległą operację kopiowania. Innymi słowy, obiekt BLOB nie może być miejscem docelowym dla wielu oczekujących operacji kopiowania.

Cały źródłowy obiekt BLOB lub plik jest zawsze kopiowany. Kopiowanie zakresu bajtów lub zestawu bloków nie jest obsługiwane.

Po skopiowaniu obiektu BLOB właściwości systemu są kopiowane do docelowego obiektu BLOB z tymi samymi wartościami.

Operacja kopiowania może przyjmować jedną z następujących form:

- Skopiuj źródłowy obiekt BLOB do docelowego obiektu BLOB z inną nazwą. Docelowy obiekt BLOB może być istniejącym obiektem BLOB tego samego typu obiektów BLOB (blok, dołączanie lub strona) lub może być nowym obiektem BLOB utworzonym przez operację kopiowania.
- Skopiuj źródłowy obiekt BLOB do docelowego obiektu BLOB o takiej samej nazwie, co skutecznie zastępuje docelowy obiekt BLOB. Taka operacja kopiowania usuwa wszystkie niezatwierdzone bloki i zastępuje metadane docelowego obiektu BLOB.
- Skopiuj plik źródłowy w usłudze plików platformy Azure do docelowego obiektu BLOB. Docelowy obiekt BLOB może być istniejącym blokowym obiektem BLOB lub może być nowym blokowym obiektem BLOB utworzonym przez operację kopiowania. Kopiowanie plików do stronicowych obiektów blob lub dołączanie obiektów BLOB nie jest obsługiwane.
- Skopiuj migawkę ze swojego podstawowego obiektu BLOB. Podwyższanie poziomu migawki do pozycji podstawowego obiektu BLOB pozwala przywrócić wcześniejszą wersję obiektu BLOB.
- Skopiuj migawkę do docelowego obiektu BLOB z inną nazwą. Otrzymany docelowy obiekt BLOB jest zapisywalnym obiektem BLOB, a nie z migawką.

## <a name="copy-a-blob"></a>Kopiowanie obiektu BLOB

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Aby skopiować obiekt BLOB, wywołaj jedną z następujących metod:

- [StartCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuri)
- [StartCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuriasync)

`StartCopyFromUri`Metody i `StartCopyFromUriAsync` zwracają obiekt [CopyFromUriOperation](/dotnet/api/azure.storage.blobs.models.copyfromurioperation) zawierający informacje o operacji kopiowania.

Poniższy przykład kodu pobiera [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) reprezentujący wcześniej utworzony obiekt BLOB i kopiuje go do nowego obiektu BLOB w tym samym kontenerze:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_CopyBlob":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Aby skopiować obiekt BLOB, wywołaj jedną z następujących metod:

- [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy)
- [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync)

`StartCopy`Metody i `StartCopyAsync` zwracają wartość identyfikatora kopii, która jest używana do sprawdzania stanu lub przerywania operacji kopiowania.

Poniższy przykład kodu pobiera odwołanie do wcześniej utworzonego obiektu BLOB i kopiuje go do nowego obiektu BLOB w tym samym kontenerze:

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

        // Lease the source blob for the copy operation 
        // to prevent another client from modifying it.
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

# <a name="python-v12"></a>[V12 Python](#tab/python)

Aby skopiować obiekt BLOB, wywołaj metodę [start_copy_from_url](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#start-copy-from-url-source-url--metadata-none--incremental-copy-false----kwargs-) . `start_copy_from_url`Metoda zwraca słownik zawierający informacje o operacji kopiowania.

Poniższy przykład kodu pobiera [BlobClient](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient) reprezentujący wcześniej utworzony obiekt BLOB i kopiuje go do nowego obiektu BLOB w tym samym kontenerze:

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_BlobCopy":::

---

## <a name="abort-a-copy-operation"></a>Przerwij operację kopiowania

Przerwanie operacji kopiowania powoduje, że docelowy obiekt BLOB o zerowej długości. Jednak metadane dla docelowego obiektu BLOB będą mieć skopiowane nowe wartości ze źródłowego obiektu BLOB lub ustawione jawnie podczas operacji kopiowania. Aby zachować oryginalne metadane przed kopią, należy utworzyć migawkę docelowego obiektu BLOB przed wywołaniem jednej z metod kopiowania.

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Sprawdź Właściwość [BlobProperties. CopyStatus](/dotnet/api/azure.storage.blobs.models.blobproperties.copystatus) w docelowym obiekcie blob, aby uzyskać stan operacji kopiowania. Końcowy obiekt BLOB zostanie zatwierdzony po zakończeniu kopiowania.

Po przerwaniu operacji kopiowania stan kopiowania docelowego obiektu BLOB jest ustawiony na [CopyStatus. przerwany](/dotnet/api/microsoft.azure.storage.blob.copystatus).

Metody [AbortCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuri) i [AbortCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuriasync) anulują trwającą operację kopiowania.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_StopBlobCopy":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Sprawdź Właściwość [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status) w docelowym obiekcie blob, aby uzyskać stan operacji kopiowania. Końcowy obiekt BLOB zostanie zatwierdzony po zakończeniu kopiowania.

Po przerwaniu operacji kopiowania stan kopiowania docelowego obiektu BLOB jest ustawiony na [CopyStatus. przerwany](/dotnet/api/microsoft.azure.storage.blob.copystatus).

Metody [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy) i [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync) anulują trwającą operację kopiowania.

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

# <a name="python-v12"></a>[V12 Python](#tab/python)

Sprawdź wpis "status" w słowniku [CopyProperties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) zwróconym przez metodę [get_blob_properties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#get-blob-properties---kwargs-) , aby uzyskać stan operacji kopiowania. Końcowy obiekt BLOB zostanie zatwierdzony po zakończeniu kopiowania.

Po przerwaniu operacji kopiowania [stan](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) ma wartość "przerwane".

Metoda [abort_copy](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#abort-copy-copy-id----kwargs-) anuluje trwającą operację kopiowania.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_StopBlobCopy":::

---

## <a name="azure-sdks"></a>Zestawy Azure SDK

Uzyskaj więcej informacji na temat zestawów Azure SDK:

 - [Zestaw Azure SDK dla platformy .NET](https://github.com/azure/azure-sdk-for-net)
 - [Zestaw Azure SDK dla języka Java](https://github.com/azure/azure-sdk-for-java)
 - [Zestaw Azure SDK dla środowiska Python](https://github.com/azure/azure-sdk-for-python)
 - [Zestaw Azure SDK dla języka JavaScript](https://github.com/azure/azure-sdk-for-js)

## <a name="next-steps"></a>Następne kroki

Poniższe tematy zawierają informacje o kopiowaniu obiektów blob i przerywaniu trwających operacji kopiowania przy użyciu interfejsów API REST platformy Azure.

- [Kopiowanie obiektu blob](/rest/api/storageservices/copy-blob)
- [Przerwij Kopiowanie obiektu BLOB](/rest/api/storageservices/abort-copy-blob)
