---
title: Wyświetlanie listy kontenerów obiektów BLOB przy użyciu platformy .NET — Azure Storage
description: Dowiedz się, jak wyświetlać listę kontenerów obiektów BLOB na koncie usługi Azure Storage za pomocą biblioteki klienckiej platformy .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: a12fc991734fe74e450aa14a477f3a4500ba659c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96937257"
---
# <a name="list-blob-containers-with-net"></a>Wyświetlanie listy kontenerów obiektów BLOB przy użyciu platformy .NET

Po wyświetleniu listy kontenerów na koncie usługi Azure Storage w kodzie możesz określić szereg opcji zarządzania wynikami zwracanymi z usługi Azure Storage. W tym artykule przedstawiono sposób wyświetlania listy kontenerów przy użyciu [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage).  

## <a name="understand-container-listing-options"></a>Informacje o opcjach wyświetlania kontenera

Aby wyświetlić listę kontenerów na koncie magazynu, wywołaj jedną z następujących metod:

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

- [GetBlobContainers](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainers)
- [GetBlobContainersAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainersasync)

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

---

Przeciążenia tych metod zawierają dodatkowe opcje zarządzania kontenerami zwracanymi przez operację tworzenia listy. Te opcje są opisane w poniższych sekcjach.

### <a name="manage-how-many-results-are-returned"></a>Zarządzanie liczbą zwracanych wyników

Domyślnie operacja tworzenia listy zwraca do 5000 wyników jednocześnie. Aby zwrócić mniejszy zestaw wyników, podaj wartość różną od zera dla rozmiaru strony wyników do zwrócenia.

Jeśli konto magazynu zawiera więcej niż 5000 kontenerów lub jeśli określono rozmiar strony w taki sposób, że operacja tworzenia listy zwróci podzestaw kontenerów na koncie magazynu, usługa Azure Storage zwraca *token kontynuacji* z listą kontenerów. Token kontynuacji jest wartością nieprzezroczystą, która służy do pobierania następnego zestawu wyników z usługi Azure Storage.

W kodzie Sprawdź wartość w polu token kontynuacji, aby określić, czy jest ona pusta (dla programu .NET V12), czy null (dla platformy .NET v11 i wcześniejszych). Gdy token kontynuacji ma wartość null, zestaw wyników jest zakończony. Jeśli token kontynuacji nie ma wartości null, następnie Wywołaj metodę wystaw ponownie, przekazując token kontynuacji, aby pobrać następny zestaw wyników, dopóki token kontynuacji nie będzie miał wartości null.

### <a name="filter-results-with-a-prefix"></a>Filtruj wyniki przy użyciu prefiksu

Aby odfiltrować listę kontenerów, Określ ciąg dla `prefix` parametru. Ciąg prefiksu może zawierać jeden lub więcej znaków. Usługa Azure Storage zwraca tylko te kontenery, których nazwy rozpoczynają się od tego prefiksu.

### <a name="return-metadata"></a>Metadane zwrotne

Aby zwrócić metadane kontenera z wynikami, określ wartość **metadanych** dla wyliczenia [BlobContainerTraits](/dotnet/api/azure.storage.blobs.models.blobcontainertraits) (dla .NET V12) lub Wyliczenie [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) (dla programu .NET v11 i wcześniejszych). Usługa Azure Storage obejmuje metadane z każdym zwracanym kontenerem, dlatego nie trzeba również pobierać metadanych kontenera.

## <a name="example-list-containers"></a>Przykład: kontenery list

Poniższy przykład asynchronicznie wyświetla listę kontenerów na koncie magazynu, które zaczynają się od określonego prefiksu. Przykład zawiera listę kontenerów, które zaczynają się od określonego prefiksu i zwraca określoną liczbę wyników dla wywołania operacji wyświetlania. Następnie używa tokenu kontynuacji, aby uzyskać następny segment wyników. Przykład zwraca również metadane kontenera z wynikami.

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="Snippet_ListContainers":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix,
                                                        int? segmentSize)
{
    Console.WriteLine("List containers beginning with prefix {0}, plus container metadata:", prefix);

    BlobContinuationToken continuationToken = null;
    ContainerResultSegment resultSegment;

    try
    {
        do
        {
            // List containers beginning with the specified prefix,
            // returning segments of 5 results each.
            // Passing in null for the maxResults parameter returns the maximum number of results (up to 5000).
            // Requesting the container's metadata as part of the listing operation populates the metadata,
            // so it's not necessary to call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, segmentSize, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Zobacz też

- [Lista kontenerów](/rest/api/storageservices/list-containers2)
- [Wyliczanie zasobów obiektów BLOB](/rest/api/storageservices/enumerating-blob-resources)
