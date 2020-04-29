---
title: Bezpieczny dostęp do danych aplikacji
titleSuffix: Azure Storage
description: Używanie tokenów SAS, szyfrowania i protokołu HTTPS w celu zabezpieczenia danych aplikacji w chmurze.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.custom: mvc
ms.openlocfilehash: 13a2a0bcc362a13b0c42650509d356f613527cfc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061327"
---
# <a name="secure-access-to-application-data"></a>Bezpieczny dostęp do danych aplikacji

Ten samouczek jest trzecią częścią serii. Zawiera informacje o sposobach zabezpieczania dostępu do konta magazynu. 

Część trzecia serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Używanie tokenów SAS do uzyskiwania dostępu do obrazów miniatury
> * Włączanie szyfrowania po stronie serwera
> * Włączanie transportu tylko przy użyciu protokołu HTTPS

Usługa [Azure Blob Storage](../common/storage-introduction.md#blob-storage) to niezawodna usługa do przechowywania plików dla aplikacji. Ten samouczek uzupełnia informacje zawarte w [poprzednim temacie][previous-tutorial] i pokazuje, jak zabezpieczyć dostęp do konta magazynu z poziomu aplikacji internetowej. Po zakończeniu obrazy będą szyfrowane, a aplikacja internetowa będzie używać tokenów SAS w celu uzyskania dostępu do obrazów miniatury.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, konieczne jest ukończenie poprzedniego samouczka na temat usługi Storage: [Automate resizing uploaded images using Event Grid (Automatyczna zmiana rozmiaru przekazanych obrazów przy użyciu usługi Event Grid)][previous-tutorial].

## <a name="set-container-public-access"></a>Włączanie dostępu publicznego do kontenera

W tej części serii samouczków do uzyskania dostępu do miniatur zostaną użyte tokeny SAS. W tym kroku ustawisz wartość `off` w konfiguracji publicznego dostępu do kontenera *thumbnails*.

```azurecli-interactive 
blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
    --name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \
    --account-name $blobStorageAccount \
    --account-key $blobStorageAccountKey \
    --name thumbnails \
    --public-access off
```

## <a name="configure-sas-tokens-for-thumbnails"></a>Konfigurowanie tokenów SAS dla miniatur

W części pierwszej serii tej serii samouczków aplikacja internetowa wyświetlała obrazy z publicznego kontenera. W tej części serii są używane tokeny sygnatur dostępu współdzielonego (SAS) do pobierania obrazów miniatur. Tokeny SAS umożliwiają ograniczenie dostępu do kontenera lub obiektu blob na podstawie adresu IP, protokołu, interwałów czasowych lub przyznanych uprawnień. Aby uzyskać więcej informacji na temat SAS, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../common/storage-sas-overview.md).

W tym przykładzie repozytorium kodu źródłowego korzysta z gałęzi `sasTokens`, która zawiera zaktualizowany kod przykładowy. Usuń istniejące wdrożenie kodu z usługi GitHub za pomocą polecenia [az webapp deployment source delete](/cli/azure/webapp/deployment/source). Następnie skonfiguruj wdrożenie aplikacji internetowej z usługi GitHub za pomocą polecenia [az webapp deployment source config](/cli/azure/webapp/deployment/source).

W poniższym poleceniu wartość `<web-app>` to nazwa aplikacji internetowej.

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
    --resource-group myResourceGroup --branch sasTokens --manual-integration \
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

Gałąź `sasTokens` repozytorium aktualizuje plik `StorageHelper.cs`. Zastępuje zadanie `GetThumbNailUrls` przykładowym kodem pokazanym poniżej. Zaktualizowane zadanie pobiera miniaturowe adresy URL za pomocą [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) , aby określić czas rozpoczęcia, czas wygaśnięcia i uprawnienia dla tokenu SAS. Po wdrożeniu aplikacja internetowa pobiera miniatury przy użyciu adresu URL i tokenu SAS. Zaktualizowane zadanie jest pokazane na poniższym przykładzie:

```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create a URI to the storage account
    Uri accountUri = new Uri("https://" + _storageConfig.AccountName + ".blob.core.windows.net/");

    // Create BlobServiceClient from the account URI
    BlobServiceClient blobServiceClient = new BlobServiceClient(accountUri);

    // Get reference to the container
    BlobContainerClient container = blobServiceClient.GetBlobContainerClient(_storageConfig.ThumbnailContainer);

    if (container.Exists())
    {
        // Set the expiration time and permissions for the container.
        // In this case, the start time is specified as a few 
        // minutes in the past, to mitigate clock skew.
        // The shared access signature will be valid immediately.
        BlobSasBuilder sas = new BlobSasBuilder
        {
            Resource = "c",
            BlobContainerName = _storageConfig.ThumbnailContainer,
            StartsOn = DateTimeOffset.UtcNow.AddMinutes(-5),
            ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
        };

        sas.SetPermissions(BlobContainerSasPermissions.All);

        // Create StorageSharedKeyCredentials object by reading
        // the values from the configuration (appsettings.json)
        StorageSharedKeyCredential storageCredential =
            new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

        // Create a SAS URI to the storage account
        UriBuilder sasUri = new UriBuilder(accountUri);
        sasUri.Query = sas.ToSasQueryParameters(storageCredential).ToString();

        foreach (BlobItem blob in container.GetBlobs())
        {
            // Create the URI using the SAS query token.
            string sasBlobUri = container.Uri + "/" +
                                blob.Name + sasUri.Query;

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(sasBlobUri);
        }
    }
    return await Task.FromResult(thumbnailUrls);
}
```

W poprzednim zadaniu użyto następujących klas, właściwości i metod:

| Klasa | Właściwości | Metody |
|-------|------------|---------|
|[StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) |  |  |
|[BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) |  |[GetBlobContainerClient](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainerclient) |
|[BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) | [Adresu](/dotnet/api/azure.storage.blobs.blobcontainerclient.uri) |[Exists](/dotnet/api/azure.storage.blobs.blobcontainerclient.exists) <br> [Getblobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) |
|[BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) |  | [SetPermissions](/dotnet/api/azure.storage.sas.blobsasbuilder.setpermissions) <br> [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) |
|[BlobItem](/dotnet/api/azure.storage.blobs.models.blobitem) | [Nazwa](/dotnet/api/azure.storage.blobs.models.blobitem.name) |  |
|[UriBuilder](/dotnet/api/system.uribuilder) | [Zapytanie](/dotnet/api/system.uribuilder.query) |  |
|[Staw](/dotnet/api/system.collections.generic.list-1) | | [Dodaj](/dotnet/api/system.collections.generic.list-1.add) |

## <a name="server-side-encryption"></a>Szyfrowanie po stronie serwera

[Szyfrowanie usługi Azure Storage (SSE)](../common/storage-service-encryption.md) pomaga chronić dane. Usługa SSE szyfruje dane magazynowane, obsługując szyfrowanie, odszyfrowywanie i zarządzanie kluczami. Wszystkie dane są szyfrowane za pomocą 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednego z najsilniejszych szyfrów blokowych.

Usługa SSE automatycznie szyfruje dane we wszystkich warstwach wydajności (Standardowa i Premium), wszystkich modelach wdrażania (model usługi Azure Resource Manager i model klasyczny) oraz wszystkich usługach Azure Storage (Blob, Queue, Table i File). 

## <a name="enable-https-only"></a>Włączanie używania tylko protokołu HTTPS

Aby mieć pewność, że żądania danych wysyłane do i z konta magazynu są bezpieczne, można zezwolić tylko na żądania używające protokołu HTTPS. Zaktualizuj protokół wymagany przez konto magazynu przy użyciu polecenia [az storage account update](/cli/azure/storage/account).

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Sprawdź połączenie, używając polecenia `curl` i protokołu `HTTP`.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Teraz, gdy wymagany jest bezpieczny transfer, otrzymasz następujący komunikat:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>Następne kroki

W trzeciej części serii przedstawiono, sposób zabezpieczania dostępu do konta magazynu, w tym następujące czynności:

> [!div class="checklist"]
> * Używanie tokenów SAS do uzyskiwania dostępu do obrazów miniatury
> * Włączanie szyfrowania po stronie serwera
> * Włączanie transportu tylko przy użyciu protokołu HTTPS

Przejdź do czwartej części serii, aby dowiedzieć się, jak monitorować aplikację magazynu w chmurze i rozwiązywać problemy.

> [!div class="nextstepaction"]
> [Monitorowanie i rozwiązywanie problemów aplikacji magazynu w chmurze](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json
