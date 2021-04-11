---
title: Pobierz typ konta magazynu i nazwę jednostki SKU z platformą .NET
titleSuffix: Azure Storage
description: Dowiedz się, jak uzyskać typ konta usługi Azure Storage i nazwę jednostki SKU przy użyciu biblioteki klienckiej platformy .NET.
services: storage
author: twooley
ms.author: twooley
ms.date: 11/12/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 26ce4a77e02e62a1a204529ce8652fbba3aab2c9
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277151"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Pobierz typ konta magazynu i nazwę jednostki SKU z platformą .NET

W tym artykule pokazano, jak uzyskać typ konta usługi Azure Storage i nazwę jednostki SKU dla obiektu BLOB przy użyciu [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage).

## <a name="about-account-type-and-sku-name"></a>Informacje o typie konta i nazwie jednostki SKU

**Typ konta**: prawidłowe typy kont to `BlobStorage` , `BlockBlobStorage` , `FileStorage` , `Storage` i `StorageV2` . [Omówienie konta usługi Azure Storage](storage-account-overview.md) zawiera więcej informacji, w tym opisy różnych kont magazynu.

**Nazwa jednostki SKU**: prawidłowe nazwy jednostek SKU obejmują,,,,,, `Premium_LRS` `Premium_ZRS` `Standard_GRS` `Standard_GZRS` `Standard_LRS` `Standard_RAGRS` `Standard_RAGZRS` , i `Standard_ZRS` . Nazwy jednostek SKU są rozróżniane wielkości liter i są polami ciągów w [klasie SkuName](/dotnet/api/microsoft.azure.management.storage.models.skuname).

## <a name="retrieve-account-information"></a>Pobierz informacje o koncie

Poniższy przykład kodu pobiera i wyświetla właściwości konta tylko do odczytu.

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Aby uzyskać typ konta magazynu i nazwę jednostki SKU skojarzone z obiektem BLOB, wywołaj metodę [GetAccountInfo](/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfo) lub [GetAccountInfoAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfoasync) .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Account.cs" id="Snippet_GetAccountInfo":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Aby uzyskać typ konta magazynu i nazwę jednostki SKU skojarzone z obiektem BLOB, wywołaj metodę [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties) lub [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync) .

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o innych operacjach, które można wykonać na koncie magazynu za pomocą [Azure Portal](https://portal.azure.com) i interfejsu API REST platformy Azure.

- [Operacja pobrania informacji o koncie (REST)](/rest/api/storageservices/get-account-information)
