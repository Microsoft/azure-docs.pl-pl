---
title: Definiowanie przechowywanych zasad dostępu przy użyciu platformy .NET — Azure Storage
description: Dowiedz się, jak definiować przechowywane zasady dostępu przy użyciu biblioteki klienckiej .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "68990743"
---
# <a name="define-a-stored-access-policy-with-net"></a>Definiowanie przechowywanych zasad dostępu za pomocą platformy .NET

Przechowywane zasady dostępu zapewniają dodatkowy poziom kontroli nad sygnaturami dostępu współdzielonego na poziomie usług (SAS) po stronie serwera. Zdefiniowanie przechowywanych zasad dostępu służy do grupowania sygnatur dostępu współdzielonego i zapewnienia dodatkowych ograniczeń dla sygnatur dostępu współdzielonego, które są powiązane z zasadami. Za pomocą przechowywanych zasad dostępu można zmienić czas rozpoczęcia, czas wygaśnięcia lub uprawnienia dla SAS lub odwołać je po wydaniu.
  
 Następujące zasoby magazynu obsługują zasady dostępu przechowywane:  
  
- Kontenery obiektów blob  
- Udziały plików  
- Kolejki  
- Tabele  
  
> [!NOTE]
> Przechowywane zasady dostępu w kontenerze mogą być skojarzone z podpisem dostępu współdzielonego w celu przyznania mu uprawnień do samego kontenera lub do obiektów blob, które zawiera. Podobnie przechowywane zasady dostępu w udziale plików mogą być kojarzone z podpisem dostępu współdzielonego lub do plików, które zawiera.  
>
> Zasady dostępu przechowywane są obsługiwane tylko dla SAS usługi. Przechowywane zasady dostępu nie są obsługiwane w przypadku kont SAS lub delegowania uprawnień użytkowników dla konta.  

## <a name="create-a-stored-access-policy"></a>Tworzenie zasad dostępu przechowywanego

Poniższy kod tworzy zasady dostępu przechowywane w kontenerze. Zasad dostępu można użyć do określenia ograniczeń dla SAS usługi w kontenerze lub jego obiektach Blob.

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
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

## <a name="see-also"></a>Zobacz także

- [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md)
- [Definiowanie przechowywanych zasad dostępu](/rest/api/storageservices/define-stored-access-policy)

