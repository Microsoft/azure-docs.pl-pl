---
title: Tworzenie zasad dostępu przechowywanych przy użyciu platformy .NET
titleSuffix: Azure Storage
description: Utwórz przechowywane zasady dostępu za pomocą usługi Azure Storage i platformy .NET. Wykonuj dodatkowe poziomy kontroli nad sygnaturami dostępu współdzielonego na poziomie usług na serwerze.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 6cd6147fbe38710bcefd580e71be1d6f5d446a21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89010753"
---
# <a name="create-a-stored-access-policy-with-net"></a>Tworzenie zasad dostępu przechowywanych przy użyciu platformy .NET

Przechowywane zasady dostępu zapewniają dodatkowy poziom kontroli nad sygnaturami dostępu współdzielonego na poziomie usług (SAS) po stronie serwera. Zdefiniowanie przechowywanych zasad dostępu służy do grupowania sygnatur dostępu współdzielonego i zapewnienia dodatkowych ograniczeń dla sygnatur dostępu współdzielonego, które są powiązane z zasadami. Za pomocą przechowywanych zasad dostępu można zmienić czas rozpoczęcia, czas wygaśnięcia lub uprawnienia dla SAS lub odwołać je po wydaniu.
  
Następujące zasoby usługi Azure Storage obsługują zasady dostępu przechowywane:  
  
- Kontenery obiektów blob  
- Udziały plików  
- Kolejki  
- Tabele  
  
> [!NOTE]
> Przechowywane zasady dostępu w kontenerze mogą być skojarzone z podpisem dostępu współdzielonego w celu przyznania mu uprawnień do samego kontenera lub do obiektów blob, które zawiera. Podobnie przechowywane zasady dostępu w udziale plików mogą być kojarzone z podpisem dostępu współdzielonego lub do plików, które zawiera.  
>
> Zasady dostępu przechowywane są obsługiwane tylko dla SAS usługi. Przechowywane zasady dostępu nie są obsługiwane w przypadku kont SAS lub delegowania uprawnień użytkowników dla konta.  

Aby uzyskać więcej informacji na temat przechowywanych zasad dostępu, zobacz [Definiowanie przechowywanych zasad dostępu](/rest/api/storageservices/define-stored-access-policy).

## <a name="create-a-stored-access-policy"></a>Tworzenie zasad dostępu przechowywanego

Podstawową operacją REST do utworzenia przechowywanych zasad dostępu jest [ustawienie listy ACL kontenerów](/rest/api/storageservices/set-container-acl). Należy autoryzować operację, aby utworzyć zasady dostępu przechowywane za pośrednictwem klucza współużytkowanego przy użyciu kluczy dostępu konta w parametrach połączenia. Autoryzacja operacji **ustawiania listy ACL kontenera** przy użyciu poświadczeń usługi Azure AD nie jest obsługiwana. Aby uzyskać więcej informacji, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

Poniższe przykłady kodu tworzą zasady dostępu przechowywane w kontenerze. Zasad dostępu można użyć do określenia ograniczeń dla SAS usługi w kontenerze lub jego obiektach Blob.

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Aby utworzyć przechowywane zasady dostępu w kontenerze z wersją 12 biblioteki klienta .NET dla usługi Azure Storage, należy wywołać jedną z następujących metod:

- [BlobContainerClient.SetAccessPolicy](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy)
- [BlobContainerClient.SetAccessPolicyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicyasync)

Poniższy przykład tworzy zasady dostępu przechowywane, które obowiązują przez jeden dzień i przyznają uprawnienia do odczytu/zapisu:

```csharp
async static Task CreateStoredAccessPolicyAsync(string containerName)
{
    string connectionString = "";

    // Use the connection string to authorize the operation to create the access policy.
    // Azure AD does not support the Set Container ACL operation that creates the policy.
    BlobContainerClient containerClient = new BlobContainerClient(connectionString, containerName);

    try
    {
        await containerClient.CreateIfNotExistsAsync();

        // Create one or more stored access policies.
        List<BlobSignedIdentifier> signedIdentifiers = new List<BlobSignedIdentifier>
        {
            new BlobSignedIdentifier
            {
                Id = "mysignedidentifier",
                AccessPolicy = new BlobAccessPolicy
                {
                    StartsOn = DateTimeOffset.UtcNow.AddHours(-1),
                    ExpiresOn = DateTimeOffset.UtcNow.AddDays(1),
                    Permissions = "rw"
                }
            }
        };
        // Set the container's access policy.
        await containerClient.SetAccessPolicyAsync(permissions: signedIdentifiers);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.ErrorCode);
        Console.WriteLine(e.Message);
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}
```

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Aby utworzyć przechowywane zasady dostępu w kontenerze z wersją 12 biblioteki klienta .NET dla usługi Azure Storage, należy wywołać jedną z następujących metod:

- [CloudBlobContainer. Set— uprawnienia](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [CloudBlobContainer. SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

Poniższy przykład tworzy zasady dostępu przechowywane, które obowiązują przez jeden dzień i przyznają uprawnienia do odczytu, zapisu i listy:

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

---

## <a name="see-also"></a>Zobacz też

- [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md)
- [Definiowanie przechowywanych zasad dostępu](/rest/api/storageservices/define-stored-access-policy)
- [Konfiguracja parametrów połączenia usługi Azure Storage](storage-configure-connection-string.md)
