---
title: Włączanie obsługi wersji obiektów blob i zarządzanie nimi
titleSuffix: Azure Storage
description: Dowiedz się, jak włączyć obsługę wersji obiektów BLOB w Azure Portal lub przy użyciu szablonu Azure Resource Manager.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 5b6bd16eacf4b1bbb7b93f5500813e7fa9dc7eef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100095849"
---
# <a name="enable-and-manage-blob-versioning"></a>Włączanie obsługi wersji obiektów blob i zarządzanie nimi

Możesz włączyć przechowywanie wersji magazynu obiektów blob, aby automatycznie zachować poprzednie wersje obiektu BLOB po jego zmodyfikowaniu lub usunięciu. Po włączeniu obsługi wersji obiektów BLOB można przywrócić wcześniejszą wersję obiektu BLOB, aby odzyskać dane, jeśli są one błędnie modyfikowane lub usuwane.

W tym artykule przedstawiono sposób włączania lub wyłączania obsługi wersji obiektów BLOB dla konta magazynu przy użyciu szablonu Azure Portal lub Azure Resource Manager. Aby dowiedzieć się więcej na temat przechowywania wersji obiektów blob, zobacz temat [przechowywanie wersji obiektów BLOB](versioning-overview.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>Włączanie obsługi wersji obiektów blob

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby włączyć obsługę wersji obiektów BLOB dla konta magazynu w Azure Portal:

1. Przejdź do swojego konta magazynu w portalu.
1. W obszarze **BLOB Service** wybierz pozycję **Ochrona danych**.
1. W sekcji **przechowywanie wersji** wybierz pozycję **włączone**.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Zrzut ekranu przedstawiający sposób włączania obsługi wersji obiektów BLOB w Azure Portal":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby włączyć obsługę wersji obiektów BLOB dla konta magazynu przy użyciu programu PowerShell, najpierw zainstaluj moduł [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) w wersji 2.3.0 lub nowszej. Następnie Wywołaj polecenie [Update-AzStorageBlobServiceProperty](/powershell/module/az.storage/update-azstorageblobserviceproperty) , aby włączyć obsługę wersji, jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości w nawiasach ostrych własnymi wartościami:

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -IsVersioningEnabled $true
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby włączyć obsługę wersji obiektów BLOB dla konta magazynu przy użyciu interfejsu wiersza polecenia platformy Azure, najpierw zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej. Następnie Wywołaj polecenie [AZ Storage account BLOB-Service-Properties Update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) , aby włączyć obsługę wersji, jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości w nawiasach ostrych własnymi wartościami:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-versioning true
```

# <a name="template"></a>[Szablon](#tab/template)

Aby włączyć obsługę wersji obiektów BLOB przy użyciu szablonu, Utwórz szablon z właściwością **IsVersioningEnabled** na **wartość true**. Poniższe kroki opisują sposób tworzenia szablonu w Azure Portal.

1. W Azure Portal wybierz pozycję **Utwórz zasób**.
1. W obszarze **Wyszukaj w portalu Marketplace** wpisz **wdrożenie szablonu**, a następnie naciśnij klawisz **Enter**.
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

Poniższy przykład kodu pokazuje, jak wyzwolić Tworzenie nowej wersji za pomocą biblioteki klienta usługi Azure Storage dla platformy .NET w wersji [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) lub nowszej. Przed uruchomieniem tego przykładu upewnij się, że włączono obsługę wersji dla konta magazynu.

Przykład tworzy blokowy obiekt BLOB, a następnie aktualizuje metadane obiektu BLOB. Aktualizacja metadanych obiektu BLOB wyzwala Tworzenie nowej wersji. Przykład Pobiera wersję początkową i bieżącą wersję oraz pokazuje, że tylko bieżąca wersja zawiera metadane.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_UpdateVersionedBlobMetadata":::

## <a name="list-blob-versions"></a>Wyświetl listę wersji obiektów BLOB

Aby wyświetlić listę wersji lub migawek obiektów BLOB za pomocą biblioteki klienta .NET V12, określ parametr [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) w polu **wersja** .

Poniższy przykład kodu pokazuje, jak wyświetlić listę wersji BLOB za pomocą biblioteki klienta usługi Azure Storage dla platformy .NET w wersji [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) lub nowszej. Przed uruchomieniem tego przykładu upewnij się, że włączono obsługę wersji dla konta magazynu.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobVersions":::

## <a name="next-steps"></a>Następne kroki

- [Przechowywanie wersji obiektów BLOB](versioning-overview.md)
- [Soft delete for Azure Storage blobs](./soft-delete-blob-overview.md) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)