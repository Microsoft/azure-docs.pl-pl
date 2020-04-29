---
title: Pobierz typ konta magazynu i nazwę jednostki SKU z platformą .NET
titleSuffix: Azure Storage
description: Dowiedz się, jak uzyskać typ konta usługi Azure Storage i nazwę jednostki SKU przy użyciu biblioteki klienckiej platformy .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 1495ed55c24a0f94b911d60d1db0f32940ea134a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79137062"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Pobierz typ konta magazynu i nazwę jednostki SKU z platformą .NET

W tym artykule pokazano, jak uzyskać typ konta usługi Azure Storage i nazwę jednostki SKU dla obiektu BLOB przy użyciu [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

Informacje o koncie są dostępne w wersjach usługi począwszy od wersji 2018-03-28.

## <a name="about-account-type-and-sku-name"></a>Informacje o typie konta i nazwie jednostki SKU

**Typ konta**: prawidłowe typy kont to `BlobStorage`, `BlockBlobStorage`, `FileStorage` `Storage`, i `StorageV2`. [Omówienie konta usługi Azure Storage](storage-account-overview.md) zawiera więcej informacji, w tym opisy różnych kont magazynu.

**Nazwa jednostki SKU**: prawidłowe nazwy jednostek `Premium_LRS`SKU `Premium_ZRS`obejmują `Standard_GRS`, `Standard_GZRS`, `Standard_LRS` `Standard_RAGRS`,, `Standard_RAGZRS`,, `Standard_ZRS`, i. Nazwy jednostek SKU są rozróżniane wielkości liter i są polami ciągów w [klasie SkuName](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet).

## <a name="retrieve-account-information"></a>Pobierz informacje o koncie

Aby uzyskać typ konta magazynu i nazwę jednostki SKU skojarzone z obiektem BLOB, wywołaj metodę [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) lub [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet) .

Poniższy przykład kodu pobiera i wyświetla właściwości konta tylko do odczytu.

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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o innych operacjach, które można wykonać na koncie magazynu za pomocą [Azure Portal](https://portal.azure.com) i interfejsu API REST platformy Azure.

- [Operacja pobrania informacji o koncie (REST)](/rest/api/storageservices/get-account-information)
