---
title: Włączanie obsługi wersji obiektów blob i zarządzanie nimi
titleSuffix: Azure Storage
description: Dowiedz się, jak włączyć obsługę wersji obiektów blob w Azure Portal lub przy użyciu Azure Resource Manager szablonu.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: f6a1d315342ea98ccaf1382630eccca876ada3f1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870197"
---
# <a name="enable-and-manage-blob-versioning"></a>Włączanie obsługi wersji obiektów blob i zarządzanie nimi

Przechowywanie wersji magazynu obiektów blob można włączyć, aby automatycznie utrzymywać poprzednie wersje obiektu blob po jego zmodyfikowaniu lub usunięciu. Po włączeniu obsługi wersji obiektów blob można przywrócić wcześniejszą wersję obiektu blob, aby odzyskać dane, jeśli zostały błędnie zmodyfikowane lub usunięte.

W tym artykule pokazano, jak włączyć lub wyłączyć przechowywanie wersji obiektów blob dla konta magazynu przy użyciu Azure Portal lub szablonu Azure Resource Manager magazynu. Aby dowiedzieć się więcej na temat wersjonarowania obiektów blob, [zobacz Blob versioning (Wersja obiektów blob).](versioning-overview.md)

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>Włączanie obsługi wersji obiektów blob

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby włączyć przechowywanie wersji obiektów blob dla konta magazynu w Azure Portal:

1. Przejdź do konta magazynu w portalu.
1. W **Blob service** wybierz pozycję **Ochrona danych.**
1. W sekcji **Wersjonowanie** wybierz pozycję **Włączone.**

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Zrzut ekranu przedstawiający sposób włączania obsługi wersji obiektów blob w Azure Portal":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby włączyć przechowywanie wersji obiektów blob dla konta magazynu przy użyciu programu PowerShell, najpierw zainstaluj moduł [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) w wersji 2.3.0 lub nowszej. Następnie wywołaj [polecenie Update-AzStorageBlobServiceProperty,](/powershell/module/az.storage/update-azstorageblobserviceproperty) aby włączyć obsługę wersji, jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości w nawiasach kątowych własnymi wartościami:

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

Aby włączyć przechowywanie wersji obiektów blob dla konta magazynu za pomocą interfejsu wiersza polecenia platformy Azure, najpierw zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej. Następnie wywołaj [polecenie az storage account blob-service-properties update,](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) aby włączyć przechowywanie wersji, jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości w nawiasach kątowych własnymi wartościami:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-versioning true
```

# <a name="template"></a>[Szablon](#tab/template)

Aby włączyć obsługę wersji obiektów blob za pomocą szablonu, utwórz szablon z **właściwością IsVersioningEnabled** na wartość **true**. W poniższych krokach opisano sposób tworzenia szablonu w Azure Portal.

1. W Azure Portal wybierz pozycję **Utwórz zasób.**
1. W **polu Wyszukaj w witrynie Marketplace** wpisz wdrożenie **szablonu**, a następnie naciśnij **klawisz ENTER.**
1. Wybierz **Template deployment**, wybierz **pozycję Utwórz,** a następnie wybierz pozycję Build your own template in the editor (Utwórz własny szablon w **edytorze).**
1. W edytorze szablonów wklej następujący kod JSON. Zastąp symbol zastępczy `<accountName>` nazwą konta magazynu.
1. Zapisz szablon.
1. Określ grupę zasobów konta, a następnie wybierz przycisk **Kup,** aby wdrożyć szablon i włączyć obsługę wersji obiektów blob.

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

Aby uzyskać więcej informacji na temat wdrażania zasobów za pomocą szablonów w Azure Portal, zobacz [Wdrażanie zasobów za pomocą Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Modyfikowanie obiektu blob w celu wyzwolenia nowej wersji

Poniższy przykład kodu pokazuje, jak wyzwolić tworzenie nowej wersji za pomocą biblioteki klienta usługi Azure Storage dla platformy .NET w wersji [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) lub nowszej. Przed uruchomieniem tego przykładu upewnij się, że włączono przechowywanie wersji dla konta magazynu.

Przykład tworzy blokowy obiekt blob, a następnie aktualizuje metadane obiektu blob. Zaktualizowanie metadanych obiektu blob wyzwala utworzenie nowej wersji. Przykład pobiera wersję początkową i bieżącą oraz pokazuje, że tylko bieżąca wersja zawiera metadane.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_UpdateVersionedBlobMetadata":::

## <a name="list-blob-versions"></a>Lista wersji obiektów blob

Aby wyświetlić listę wersji obiektów blob lub migawek za pomocą biblioteki klienta platformy .NET v12, określ parametr [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) w polu **Wersja.**

Poniższy przykład kodu pokazuje, jak wyświetlić listę wersji obiektów blob za pomocą biblioteki klienta usługi Azure Storage dla platformy .NET w wersji [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) lub nowszej. Przed uruchomieniem tego przykładu upewnij się, że włączono przechowywanie wersji dla konta magazynu.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobVersions":::

## <a name="next-steps"></a>Następne kroki

- [Wersjonarowanie obiektów blob](versioning-overview.md)
- [Soft delete for Azure Storage blobs](./soft-delete-blob-overview.md) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)