---
title: Określ klucz dostarczony przez klienta w żądaniu usługi BLOB Storage za pomocą platformy .NET — Azure Storage
description: Dowiedz się, jak określić klucz dostarczony przez klienta w żądaniu usługi BLOB Storage przy użyciu platformy .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/20/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 50d592d0020ae1b5a704296ef68f5153f0207714
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427579"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Określ klucz dostarczony przez klienta w żądaniu do magazynu obiektów BLOB za pomocą platformy .NET

Klienci, którzy wysyłają żądania do usługi Azure Blob Storage, mają możliwość zapewnienia klucza szyfrowania dla pojedynczego żądania. Dołączenie klucza szyfrowania żądania zapewnia szczegółową kontrolę nad ustawieniami szyfrowania operacji usługi BLOB Storage. Klucze dostarczone przez klienta mogą być przechowywane w Azure Key Vault lub w innym magazynie kluczy.

W tym artykule pokazano, jak określić klucz dostarczony przez klienta na żądanie przy użyciu platformy .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Aby dowiedzieć się więcej o sposobie uwierzytelniania przy użyciu biblioteki klienta tożsamości platformy Azure z usługi Azure Storage, zapoznaj się z sekcją **uwierzytelnianie przy użyciu biblioteki tożsamości platformy Azure** w artykule [Autoryzuj dostęp do obiektów blob i kolejek przy użyciu tożsamości Azure Active Directory i zarządzanych dla zasobów platformy Azure](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>Przykład: Użyj klucza dostarczonego przez klienta do przekazania obiektu BLOB

Poniższy przykład tworzy klucz dostarczony przez klienta i używa tego klucza do przekazania obiektu BLOB. Kod przekazuje blok, a następnie zatwierdza listę zablokowanych w celu zapisania obiektu BLOB w usłudze Azure Storage.

```csharp
async static Task UploadBlobWithClientKey(string accountName, string containerName,
    string blobName, Stream data, byte[] key)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri accountUri = new Uri("https://" + accountName + blobServiceEndpointSuffix);

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = new CustomerProvidedKey(key)
    };

    // Create a client object for the Blob service, including options.
    BlobServiceClient serviceClient = new BlobServiceClient(accountUri, 
        new DefaultAzureCredential(), options);

    // Create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient = serviceClient.GetBlobContainerClient(containerName);

    // Create a new block blob client object.
    // The blob client retains the credential and client options.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>Następne kroki

- [Podaj klucz szyfrowania dla żądania do magazynu obiektów BLOB](encryption-customer-provided-keys.md)
- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](../common/storage-service-encryption.md)
