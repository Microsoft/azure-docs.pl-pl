---
title: Tworzenie sygnatury dostępu współdzielonego usługi dla kontenera lub obiektu BLOB
titleSuffix: Azure Storage
description: Dowiedz się, jak utworzyć sygnaturę dostępu współdzielonego (SAS) usługi dla kontenera lub obiektu BLOB przy użyciu biblioteki klienckiej platformy .NET lub zestawu SDK V12 języka JavaScript
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: c38581a23f6714c0676b3b3c9c8481205b14a374
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027205"
---
# <a name="create-a-service-sas-for-a-container-or-blob"></a>Tworzenie sygnatury dostępu współdzielonego usługi dla kontenera lub obiektu BLOB

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

W tym artykule pokazano, jak za pomocą klucza konta magazynu utworzyć sygnaturę dostępu współdzielonego dla kontenera lub obiektu BLOB za pomocą [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage).

## <a name="create-a-service-sas-for-a-blob-container"></a>Tworzenie sygnatury dostępu współdzielonego usługi dla kontenera obiektów BLOB

Poniższy przykład kodu tworzy sygnaturę dostępu współdzielonego dla kontenera. Jeśli zostanie podana nazwa istniejących zasad dostępu, te zasady są skojarzone ze skojarzonymi ze standardami SAS. Jeśli nie podano żadnych przechowywanych zasad dostępu, kod tworzy skojarzenie wielodostępne ad hoc w kontenerze.

### <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

Sygnatura dostępu współdzielonego usługi jest podpisana przy użyciu klucza dostęp do konta. Użyj klasy [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) , aby utworzyć poświadczenia używane do podpisywania sygnatury dostępu współdzielonego. Następnie utwórz nowy obiekt [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) i Wywołaj [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) , aby uzyskać ciąg tokenu sygnatury dostępu współdzielonego.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetContainerSasUri":::

### <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

Aby utworzyć sygnaturę dostępu współdzielonego usługi dla kontenera, wywołaj metodę [CloudBlobContainer. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature) .

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, 
                                         string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define
        // the parameters of an ad hoc SAS, and to construct a shared access policy
        // that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed
            // to be the time when the storage service receives the request. Omitting
            // the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container,
        // setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case,
        // all of the constraints for the shared access signature are specified
        // on the stored access policy, which is provided by name. It is also possible
        // to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

# <a name="javascript-v12"></a>[V12 JavaScript](#tab/javascript)

Sygnatura dostępu współdzielonego usługi jest podpisana przy użyciu klucza dostęp do konta. Użyj klasy [StorageSharedKeyCredential](/javascript/api/@azure/storage-blob/storagesharedkeycredential) , aby utworzyć poświadczenia używane do podpisywania sygnatury dostępu współdzielonego. Następnie wywołaj funkcję [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) , podając wymagane opcje, aby uzyskać ciąg tokenu SAS.

```javascript
function getContainerSasUri(containerClient, sharedKeyCredential, storedPolicyName) {
    const sasOptions = {
        containerName: containerClient.containerName,
        permissions: ContainerSASPermissions.parse("c")
    };

    if (storedPolicyName == null) {
        sasOptions.startsOn = new Date();
        sasOptions.expiresOn = new Date(new Date().valueOf() + 3600 * 1000);
    } else {
        sasOptions.identifier = storedPolicyName;
    }

    const sasToken = generateBlobSASQueryParameters(sasOptions, sharedKeyCredential).toString();
    console.log(`SAS token for blob container is: ${sasToken}`);

    return `${containerClient.url}?${sasToken}`;
}
```

---

## <a name="create-a-service-sas-for-a-blob"></a>Tworzenie sygnatury dostępu współdzielonego usługi dla obiektu BLOB

Poniższy przykład kodu tworzy sygnaturę dostępu współdzielonego dla obiektu BLOB. Jeśli zostanie podana nazwa istniejących zasad dostępu, te zasady są skojarzone ze skojarzonymi ze standardami SAS. Jeśli nie podano żadnych przechowywanych zasad dostępu, kod tworzy w obiekcie blob ad hoc SAS.

### <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

Sygnatura dostępu współdzielonego usługi jest podpisana przy użyciu klucza dostęp do konta. Użyj klasy [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) , aby utworzyć poświadczenia używane do podpisywania sygnatury dostępu współdzielonego. Następnie utwórz nowy obiekt [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) i Wywołaj [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) , aby uzyskać ciąg tokenu sygnatury dostępu współdzielonego.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetBlobSasUri":::

### <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

Aby utworzyć sygnaturę dostępu współdzielonego usługi dla obiektu BLOB, wywołaj metodę [polecenia cloudblob. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) .

```csharp
private static string GetBlobSasUri(CloudBlobContainer container,
                                    string blobName,
                                    string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters
        // of an ad hoc SAS, and to construct a shared access policy that is saved to
        // the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be
            // the time when the storage service receives the request. Omitting the start time
            // for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read |
                          SharedAccessBlobPermissions.Write |
                          SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob,
        // setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints
        // for the SAS are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

# <a name="javascript-v12"></a>[V12 JavaScript](#tab/javascript)

Aby utworzyć sygnaturę dostępu współdzielonego usługi dla obiektu BLOB, wywołaj metodę [polecenia cloudblob. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) .

Aby utworzyć sygnaturę dostępu współdzielonego usługi dla obiektu BLOB, wywołaj funkcję [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) , która udostępnia wymagane opcje.

```javascript
function getBlobSasUri(containerClient, blobName, sharedKeyCredential, storedPolicyName) {
    const sasOptions = {
        containerName: containerClient.containerName,
        blobName: blobName
    };

    if (storedPolicyName == null) {
        sasOptions.startsOn = new Date();
        sasOptions.expiresOn = new Date(new Date().valueOf() + 3600 * 1000);
        sasOptions.permissions = BlobSASPermissions.parse("r");
    } else {
        sasOptions.identifier = storedPolicyName;
    }

    const sasToken = generateBlobSASQueryParameters(sasOptions, sharedKeyCredential).toString();
    console.log(`SAS token for blob is: ${sasToken}`);

    return `${containerClient.getBlockBlobClient(blobName).url}?${sasToken}`;
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

[!INCLUDE [storage-blob-javascript-resources-include](../../../includes/storage-blob-javascript-resources-include.md)]

## <a name="next-steps"></a>Następne kroki

- [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../common/storage-sas-overview.md)
- [Tworzenie sygnatury dostępu współdzielonego usługi](/rest/api/storageservices/create-service-sas)
