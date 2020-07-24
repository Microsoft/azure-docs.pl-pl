---
title: Włączanie obsługi wersji obiektów blob i zarządzanie nimi (wersja zapoznawcza)
titleSuffix: Azure Storage
description: Dowiedz się, jak włączyć obsługę wersji obiektów BLOB w Azure Portal lub przy użyciu szablonu Azure Resource Manager.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 7ff0bb6d593a199bd249441cf484d1f0580774ac
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086051"
---
# <a name="enable-and-manage-blob-versioning"></a>Włączanie obsługi wersji obiektów blob i zarządzanie nimi

W dowolnym momencie możesz włączyć lub wyłączyć obsługę wersji obiektów BLOB (wersja zapoznawcza) dla konta magazynu, korzystając z szablonu Azure Portal lub Azure Resource Manager.

## <a name="enable-blob-versioning"></a>Włączanie obsługi wersji obiektów blob

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby włączyć obsługę wersji obiektów BLOB w Azure Portal:

1. Przejdź do swojego konta magazynu w portalu.
1. W obszarze **BLOB Service**wybierz pozycję **Ochrona danych**.
1. W sekcji **przechowywanie wersji** wybierz pozycję **włączone**.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Zrzut ekranu przedstawiający sposób włączania obsługi wersji obiektów BLOB w Azure Portal":::

# <a name="template"></a>[Szablon](#tab/template)

Aby włączyć obsługę wersji obiektów BLOB przy użyciu szablonu, Utwórz szablon z właściwością **IsVersioningEnabled** na **wartość true**. Poniższe kroki opisują sposób tworzenia szablonu w Azure Portal.

1. W Azure Portal wybierz pozycję **Utwórz zasób**.
1. W obszarze **Wyszukaj w portalu Marketplace**wpisz **wdrożenie szablonu**, a następnie naciśnij klawisz **Enter**.
1. Wybierz **Template Deployment**, wybierz pozycję **Utwórz**, a następnie wybierz opcję **Kompiluj własny szablon w edytorze**.
1. W edytorze szablonów wklej poniższy kod JSON. Zastąp symbol zastępczy `<accountName>` nazwą konta magazynu.
1. Zapisz szablon.
1. Określ grupę zasobów konta, a następnie wybierz przycisk **Kup** , aby wdrożyć szablon i włączyć obsługę wersji obiektów BLOB.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Aby uzyskać więcej informacji na temat wdrażania zasobów przy użyciu szablonów w Azure Portal, zobacz [wdrażanie zasobów przy użyciu Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Modyfikowanie obiektu BLOB w celu wyzwolenia nowej wersji

Poniższy przykład kodu pokazuje, jak wyzwolić Tworzenie nowej wersji za pomocą biblioteki klienta usługi Azure Storage dla platformy .NET w wersji [12.5.0-Preview. 5](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.0-preview.5) lub nowszej. Przed uruchomieniem tego przykładu upewnij się, że włączono obsługę wersji dla konta magazynu.

Przykład tworzy blokowy obiekt BLOB, a następnie aktualizuje metadane obiektu BLOB. Aktualizacja metadanych obiektu BLOB wyzwala Tworzenie nowej wersji. Przykład Pobiera wersję początkową i bieżącą wersję oraz pokazuje, że tylko bieżąca wersja zawiera metadane.

```csharp
public static async Task UpdateVersionedBlobMetadata(string containerName, string blobName)
{
    // Create a new service client from the connection string.
    BlobServiceClient blobServiceClient = new BlobServiceClient(ConnectionString);

    // Create a new container client.
    BlobContainerClient containerClient = blobServiceClient.GetBlobContainerClient(containerName);

    try
    {
        // Create the container.
        await containerClient.CreateIfNotExistsAsync();

        // Upload a block blob.
        BlockBlobClient blockBlobClient = containerClient.GetBlockBlobClient(blobName);

        string blobContents = string.Format("Block blob created at {0}.", DateTime.Now);
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        string initalVersionId;
        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            Response<BlobContentInfo> uploadResponse = await blockBlobClient.UploadAsync(stream, null, default);

            // Get the version ID for the current version.
            initalVersionId = uploadResponse.Value.VersionId;
        }

        // Update the blob's metadata to trigger the creation of a new version.
        Dictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "key", "value" },
            { "key1", "value1" }
        };

        Response<BlobInfo> metadataResponse = await blockBlobClient.SetMetadataAsync(metadata);

        // Get the version ID for the new current version.
        string newVersionId = metadataResponse.Value.VersionId;

        // Request metadata on the previous version.
        BlockBlobClient initalVersionBlob = blockBlobClient.WithVersion(initalVersionId);
        Response<BlobProperties> propertiesResponse = await initalVersionBlob.GetPropertiesAsync();
        PrintMetadata(propertiesResponse);

        // Request metadata on the current version.
        BlockBlobClient newVersionBlob = blockBlobClient.WithVersion(newVersionId);
        Response<BlobProperties> newPropertiesResponse = await newVersionBlob.GetPropertiesAsync();
        PrintMetadata(newPropertiesResponse);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}

static void PrintMetadata(Response<BlobProperties> propertiesResponse)
{
    if (propertiesResponse.Value.Metadata.Count > 0)
    {
        Console.WriteLine("Metadata values for version {0}:", propertiesResponse.Value.VersionId);
        foreach (var item in propertiesResponse.Value.Metadata)
        {
            Console.WriteLine("Key:{0}  Value:{1}", item.Key, item.Value);
        }
    }
    else
    {
        Console.WriteLine("Version {0} has no metadata.", propertiesResponse.Value.VersionId);
    }
}
```

## <a name="next-steps"></a>Następne kroki

- [Przechowywanie wersji obiektów BLOB (wersja zapoznawcza)](versioning-overview.md)
- [Soft delete for Azure Storage blobs](soft-delete-overview.md) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)
