---
title: Tworzenie lub usuwanie kontenera obiektów BLOB przy użyciu platformy .NET — Azure Storage
description: Dowiedz się, jak utworzyć lub usunąć kontener obiektów BLOB na koncie usługi Azure Storage za pomocą biblioteki klienckiej platformy .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/22/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: a17ab97dbfa1819154695f4c287b59db90f34334
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89018998"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Tworzenie lub usuwanie kontenera w usłudze Azure Storage przy użyciu platformy .NET

Obiekty blob w usłudze Azure Storage są zorganizowane w kontenery. Aby można było przekazać obiekt BLOB, należy najpierw utworzyć kontener. W tym artykule przedstawiono sposób tworzenia i usuwania kontenerów za pomocą [biblioteki klienckiej usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="name-a-container"></a>Nazwij kontener

Nazwa kontenera musi być prawidłową nazwą DNS, ponieważ stanowi część unikatowego identyfikatora URI używanego do adresowania kontenera lub jego obiektów BLOB. Po nadaniu nazwy kontenera postępuj zgodnie z następującymi regułami:

- Nazwy kontenerów mogą mieć długość od 3 do 63 znaków.
- Nazwy kontenerów muszą zaczynać się literą lub cyfrą i mogą zawierać tylko małe litery, cyfry i znak kreski (-).
- W nazwach kontenerów nie są dozwolone dwa lub więcej następujących po sobie znaków kreski.

Identyfikator URI dla kontenera ma następujący format:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć kontener, należy wywołać jedną z następujących metod:

# <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

- [Utwórz](/dotnet/api/azure.storage.blobs.blobcontainerclient.create)
- [Metoda async](/dotnet/api/azure.storage.blobs.blobcontainerclient.createasync)
- [Metodę createifnotexists](/dotnet/api/azure.storage.blobs.blobcontainerclient.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.createifnotexistsasync)

# <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

- [Utwórz](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [Metoda async](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [Metodę createifnotexists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)
---

Metody **Create** i **xmlasync** zgłaszają wyjątek, jeśli kontener o tej samej nazwie już istnieje.

Metody **metodę createifnotexists** i **CreateIfNotExistsAsync** zwracają wartość logiczną wskazującą, czy kontener został utworzony. Jeśli kontener o tej samej nazwie już istnieje, te metody zwracają **wartość false** , aby wskazać, że nowy kontener nie został utworzony.

Kontenery są tworzone bezpośrednio pod kontem magazynu. Nie można zagnieżdżać jednego kontenera poniżej innego.

W poniższym przykładzie jest tworzony kontener asynchronicznie:

# <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateSampleContainerAsync":::

# <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```
---

## <a name="create-the-root-container"></a>Tworzenie kontenera głównego

Kontener główny służy jako domyślny kontener dla konta magazynu. Każde konto magazynu może mieć jeden kontener główny, który musi mieć nazwę *$root*. Kontener główny musi być jawnie utworzony lub usunięty.

Można odwoływać się do obiektu BLOB przechowywanego w kontenerze głównym bez dołączania nazwy kontenera głównego. Kontener główny umożliwia odwoływanie się do obiektu BLOB na najwyższym poziomie w hierarchii kont magazynu. Na przykład można odwołać się do obiektu BLOB znajdującego się w kontenerze głównego w następujący sposób:

`https://myaccount.blob.core.windows.net/default.html`

Poniższy przykład tworzy synchronicznie kontener główny:

# <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateRootContainer":::

# <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```
---

## <a name="delete-a-container"></a>Usuwanie kontenera

Aby usunąć kontener w programie .NET, należy użyć jednej z następujących metod:

# <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

- [Usuwanie](/dotnet/api/azure.storage.blobs.blobcontainerclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexistsasync)

# <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

- [Usuwanie](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)
---

Metody **delete** i **DeleteAsync** zgłaszają wyjątek, jeśli kontener nie istnieje.

Metody **DeleteIfExists** i **DeleteIfExistsAsync** zwracają wartość logiczną wskazującą, czy kontener został usunięty. Jeśli określony kontener nie istnieje, wówczas te metody zwracają **wartość false** , aby wskazać, że kontener nie został usunięty.

Po usunięciu kontenera nie można utworzyć kontenera o tej samej nazwie przez co *najmniej* 30 sekund. Próba utworzenia kontenera o takiej samej nazwie zakończy się niepowodzeniem z kodem błędu HTTP 409 (konflikt). Wszystkie inne operacje dotyczące kontenera lub obiektów blob, które zawiera, będą kończyć się niepowodzeniem z kodem błędu HTTP 404 (nie znaleziono).

Poniższy przykład usuwa określony kontener i obsługuje wyjątek, jeśli kontener nie istnieje:

# <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteSampleContainerAsync":::

# <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
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

Poniższy przykład pokazuje, jak usunąć wszystkie kontenery, które zaczynają się od określonego prefiksu.

# <a name="net-v12"></a>[\.V12 netto](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteContainersWithPrefixAsync":::

# <a name="net-v11"></a>[\.V11 netto](#tab/dotnetv11)

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            await container.DeleteAsync();
        }

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

- [Operacja tworzenia kontenera](/rest/api/storageservices/create-container)
- [Operacja usuwania kontenera](/rest/api/storageservices/delete-container)
