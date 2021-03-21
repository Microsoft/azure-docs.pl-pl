---
title: Tworzenie sygnatury dostępu współdzielonego konta przy użyciu platformy .NET
titleSuffix: Azure Storage
description: Dowiedz się, jak utworzyć sygnaturę dostępu współdzielonego (SAS) konta za pomocą biblioteki klienckiej platformy .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/12/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 2918b845430a6fc6dc59eca7041c114fc9d06515
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97092214"
---
# <a name="create-an-account-sas-with-net"></a>Tworzenie sygnatury dostępu współdzielonego konta przy użyciu platformy .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

W tym artykule pokazano, jak za pomocą klucza konta magazynu utworzyć sygnaturę dostępu współdzielonego konta przy użyciu [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage).

## <a name="create-an-account-sas"></a>Tworzenie sygnatury dostępu współdzielonego konta

### <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

Sygnatura dostępu współdzielonego konta jest podpisana przy użyciu klucza dostęp do konta. Użyj klasy [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) , aby utworzyć poświadczenia używane do podpisywania sygnatury dostępu współdzielonego. Następnie utwórz nowy obiekt [AccountSasBuilder](/dotnet/api/azure.storage.sas.accountsasbuilder) i Wywołaj [ToSasQueryParameters](/dotnet/api/azure.storage.sas.accountsasbuilder.tosasqueryparameters) , aby uzyskać ciąg tokenu sygnatury dostępu współdzielonego.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetAccountSASToken":::

### <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

Aby utworzyć sygnaturę dostępu współdzielonego konta dla kontenera, wywołaj metodę [CloudStorageAccount. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature) .

Poniższy przykład kodu tworzy sygnaturę dostępu współdzielonego konta, która jest prawidłowa dla usług BLOB i plików, i przyznaje Klientowi uprawnienia do odczytu, zapisu i wyświetlania listy w celu uzyskania dostępu do interfejsów API na poziomie usługi. Sygnatura dostępu współdzielonego konta ogranicza protokół do protokołu HTTPS, więc żądanie musi być nawiązywane przy użyciu protokołu HTTPS. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use Shared Key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<storage-account>;AccountKey=<account-key>";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

---

## <a name="use-an-account-sas-from-a-client"></a>Używanie sygnatury dostępu współdzielonego konta z klienta

Aby za pomocą sygnatury dostępu współdzielonego konta uzyskać dostęp do interfejsów API na poziomie usługi dla Blob service, Utwórz obiekt klienta Blob service przy użyciu sygnatury dostępu współdzielonego i punktu końcowego magazynu obiektów BLOB dla konta magazynu.

### <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_UseAccountSAS":::

### <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

W tym fragmencie kodu Zastąp `<storage-account>` symbol zastępczy nazwą konta magazynu.

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

---

## <a name="next-steps"></a>Następne kroki

- [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md)
- [Tworzenie sygnatury dostępu współdzielonego konta](/rest/api/storageservices/create-account-sas)
