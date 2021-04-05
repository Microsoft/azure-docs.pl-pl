---
title: Zarządzanie właściwościami i metadanymi dla obiektu BLOB za pomocą platformy .NET i usługi Azure Storage
description: Informacje dotyczące ustawiania i pobierania właściwości systemu oraz przechowywania niestandardowych metadanych w obiektach Blob na koncie usługi Azure Storage za pomocą biblioteki klienckiej platformy .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/25/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 984fb00e163a090534da1fb41850dcfef6c5d516
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95521533"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>Zarządzanie właściwościami i metadanymi obiektów BLOB przy użyciu platformy .NET

Poza danymi, które zawierają, obiekty blob obsługują właściwości systemu i metadane zdefiniowane przez użytkownika. W tym artykule pokazano, jak zarządzać właściwościami systemu i metadanymi zdefiniowanymi przez użytkownika za pomocą [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage).

## <a name="about-properties-and-metadata"></a>Informacje o właściwościach i metadanych

- **Właściwości systemu**: właściwości systemu istnieją w każdym zasobie magazynu obiektów BLOB. Niektóre z nich mogą być odczytywane lub ustawiane, a inne są tylko do odczytu. W obszarze okładek niektóre właściwości systemu odpowiadają określonym standardowym nagłówkom HTTP. Biblioteka klienta usługi Azure Storage dla platformy .NET zachowuje te właściwości.

- **Metadane zdefiniowane przez użytkownika**: metadane zdefiniowane przez użytkownika składają się z co najmniej jednej pary nazwa-wartość określonej dla zasobu usługi BLOB Storage. Możesz użyć metadanych do przechowywania dodatkowych wartości z zasobem. Wartości metadanych są tylko do celów własnych i nie mają wpływu na sposób zachowania zasobu.

> [!NOTE]
> Tagi indeksu obiektów BLOB zapewniają również możliwość przechowywania dowolnych atrybutów klucza/wartości zdefiniowanych przez użytkownika wraz z zasobem usługi Azure Blob Storage. Podobnie jak w przypadku metadanych, tylko Tagi indeksu obiektów BLOB są automatycznie indeksowane i przeszukiwane przez natywną usługę obiektów BLOB. Metadane nie mogą być indeksowane i badane, chyba że zostanie wykorzystana inna usługa, taka jak Azure Search.
>
> Aby dowiedzieć się więcej na temat tej funkcji, zobacz temat [Zarządzanie danymi w usłudze Azure Blob Storage i indeks obiektów BLOB (wersja zapoznawcza)](storage-manage-find-blobs.md).

## <a name="set-and-retrieve-properties"></a>Ustawianie i pobieranie właściwości

Poniższy przykład kodu ustawia `ContentType` `ContentLanguage` Właściwości systemu i w obiekcie blob.

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Aby ustawić właściwości obiektu BLOB, wywołaj [SetHttpHeaders](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheaders) lub [SetHttpHeadersAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheadersasync). Wszystkie właściwości niejawnie ustawione są wyczyszczone. Poniższy przykład kodu najpierw pobiera istniejące właściwości obiektu BLOB, a następnie używa ich do wypełniania nagłówków, które nie są aktualizowane.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_SetBlobProperties":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
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

Poniższy przykład kodu pobiera właściwości systemu obiektu BLOB i wyświetla niektóre wartości.

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobProperties":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Pobieranie metadanych i wartości właściwości dla zasobu usługi BLOB Storage jest procesem dwuetapowym. Aby można było odczytać te wartości, należy je jawnie pobrać poprzez wywołanie `FetchAttributes` `FetchAttributesAsync` metody lub. Wyjątkiem od tej zasady jest, że `Exists` metody i `ExistsAsync` wywołują odpowiednią `FetchAttributes` metodę w ramach okładek. Po wywołaniu jednej z tych metod nie trzeba również wywoływać `FetchAttributes` .

> [!IMPORTANT]
> Jeśli okaże się, że wartość właściwości lub metadanych zasobu magazynu nie została wypełniona, sprawdź, czy kod wywołuje `FetchAttributes` `FetchAttributesAsync` metodę lub.

Aby uzyskać właściwości obiektu BLOB, wywołaj `FetchAttributes` `FetchAttributesAsync` metodę lub w obiekcie blob, aby wypełnić `Properties` Właściwość.

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
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

## <a name="set-and-retrieve-metadata"></a>Ustawianie i Pobieranie metadanych

Można określić metadane jako jedną lub więcej par nazwa-wartość w obiekcie blob lub zasobie kontenera. Aby ustawić metadane, Dodaj pary nazwa-wartość do `Metadata` kolekcji w zasobie. Następnie należy wywołać jedną z następujących metod, aby zapisać wartości:

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

- [Setmetadata](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadata)
- [SetMetadataAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadataasync)

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

- [Setmetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)
---

Pary nazwa/wartość metadanych są prawidłowymi nagłówkami HTTP i powinny być zgodne ze wszystkimi ograniczeniami dotyczącymi nagłówków HTTP. Nazwy metadanych muszą być Prawidłowymi nazwami nagłówków HTTP i prawidłowymi identyfikatorami języka C#, mogą zawierać tylko znaki ASCII i powinny być traktowane jako bez uwzględniania wielkości liter. Zakodowane w [formacie base64](/dotnet/api/system.convert.tobase64string) lub wartości metadanych [kodu URL](/dotnet/api/system.web.httputility.urlencode) zawierające znaki nienależące do zestawu znaków ASCII.

Nazwa metadanych musi być zgodna z konwencjami nazewnictwa dla identyfikatorów C#. Nazwy metadanych zachowują przypadek używany, gdy zostały utworzone, ale bez uwzględniania wielkości liter podczas ustawiania lub odczytywania. Jeśli co najmniej dwa nagłówki metadanych o tej samej nazwie są przesyłane do zasobu, usługa Azure Blob Storage zwraca kod błędu HTTP 400 (Nieprawidłowe żądanie).

Poniższy przykład kodu ustawia metadane w obiekcie blob. Jedna wartość jest ustawiana za pomocą `Add` metody kolekcji. Druga wartość jest ustawiana za pomocą niejawnej składni klucz/wartość.

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_AddBlobMetadata":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
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

Poniższy przykład kodu odczytuje metadane w obiekcie blob.

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Aby pobrać metadane, wywołaj metodę [GetProperties](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getproperties) lub [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getpropertiesasync) na obiekcie blob lub kontenerze w celu wypełnienia kolekcji [metadanych](/dotnet/api/azure.storage.blobs.models.blobproperties.metadata) , a następnie odczytaj wartości, jak pokazano w poniższym przykładzie. Metody **GetProperties** pobierają właściwości obiektu BLOB i metadane w pojedynczym wywołaniu. Różni się to od interfejsów API REST, które wymagają oddzielnych wywołań do [pobierania właściwości obiektów BLOB](/rest/api/storageservices/get-blob-properties) i [pobierania metadanych obiektów BLOB](/rest/api/storageservices/get-blob-metadata).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobMetadata":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Aby pobrać metadane, wywołaj `FetchAttributes` metodę lub do `FetchAttributesAsync` obiektu BLOB lub kontenera w celu wypełnienia `Metadata` kolekcji, a następnie odczytaj wartości, jak pokazano w poniższym przykładzie.

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
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

## <a name="see-also"></a>Zobacz też

- [Operacja ustawiania właściwości obiektu BLOB](/rest/api/storageservices/set-blob-properties)
- [Operacja pobierania właściwości obiektu BLOB](/rest/api/storageservices/get-blob-properties)
- [Operacja ustawiania metadanych obiektu BLOB](/rest/api/storageservices/set-blob-metadata)
- [Operacja pobierania metadanych obiektu BLOB](/rest/api/storageservices/get-blob-metadata)