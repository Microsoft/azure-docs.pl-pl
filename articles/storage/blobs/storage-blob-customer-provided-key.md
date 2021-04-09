---
title: Określ klucz dostarczony przez klienta w żądaniu usługi BLOB Storage za pomocą platformy .NET — Azure Storage
description: Dowiedz się, jak określić klucz dostarczony przez klienta w żądaniu usługi BLOB Storage przy użyciu platformy .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: c3096da8b3c83dbfe8cfdd6a5fa4d177241334de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693515"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Określ klucz dostarczony przez klienta w żądaniu do magazynu obiektów BLOB za pomocą platformy .NET

Klienci, którzy wysyłają żądania do usługi Azure Blob Storage, mają możliwość udostępnienia klucza szyfrowania AES-256 dla pojedynczego żądania. Dołączenie klucza szyfrowania żądania zapewnia szczegółową kontrolę nad ustawieniami szyfrowania operacji usługi BLOB Storage. Klucze dostarczone przez klienta mogą być przechowywane w Azure Key Vault lub w innym magazynie kluczy.

W tym artykule pokazano, jak określić klucz dostarczony przez klienta na żądanie przy użyciu platformy .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Aby dowiedzieć się więcej o sposobie uwierzytelniania przy użyciu biblioteki klienta tożsamości platformy Azure, zobacz [Biblioteka klienta tożsamości platformy Azure dla platformy .NET](/dotnet/api/overview/azure/identity-readme).

## <a name="use-a-customer-provided-key-to-write-to-a-blob"></a>Używanie klucza dostarczonego przez klienta do zapisywania w obiekcie blob

Poniższy przykład udostępnia klucz AES-256 podczas przekazywania obiektu BLOB za pomocą biblioteki klienta V12 dla magazynu obiektów BLOB. W przykładzie zastosowano obiekt [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) , aby autoryzować żądanie zapisu w usłudze Azure AD, ale można również autoryzować żądanie przy użyciu poświadczeń klucza współużytkowanego.

```csharp
async static Task UploadBlobWithClientKey(Uri blobUri,
                                          Stream data,
                                          byte[] key,
                                          string keySha256)
{
    // Create a new customer-provided key.
    // Key must be AES-256.
    var cpk = new CustomerProvidedKey(key);

    // Check the key's encryption hash.
    if (cpk.EncryptionKeyHash != keySha256)
    {
        throw new InvalidOperationException("The encryption key is corrupted.");
    }

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = cpk
    };

    // Create the client object with options specified.
    BlobClient blobClient = new BlobClient(
        blobUri,
        new DefaultAzureCredential(),
        options);

    // If the container may not exist yet,
    // create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient =
        blobClient.GetParentBlobContainerClient();

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
