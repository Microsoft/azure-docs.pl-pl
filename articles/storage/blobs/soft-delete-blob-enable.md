---
title: Włączanie usuwania nietrwałego dla obiektów blob
titleSuffix: Azure Storage
description: Włącz usuwanie nieumyślne dla obiektów blob, aby chronić dane obiektów blob przed przypadkowym usunięciem lub zastąpieniem.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4a8d1f872ca042429276b8f0e1112bc5837d8e38
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869279"
---
# <a name="enable-soft-delete-for-blobs"></a>Włączanie usuwania nietrwałego dla obiektów blob

Usuwanie nieumyślne obiektu blob chroni pojedynczy obiekt blob oraz jego wersje, migawki i metadane przed przypadkowym usunięciem lub zastąpieniem przez utrzymywanie usuniętych danych w systemie przez określony czas. W okresie przechowywania można przywrócić stan obiektu blob podczas usuwania. Po upływie okresu przechowywania obiekt blob zostanie trwale usunięty. Aby uzyskać więcej informacji na temat usuwania nie softowego obiektu blob, zobacz [Usuwanie nieukońcowe dla obiektów blob](soft-delete-blob-overview.md).

Usuwanie nie softowe obiektów blob jest częścią kompleksowej strategii ochrony danych dla danych obiektów blob. Aby dowiedzieć się więcej na temat zaleceń firmy Microsoft dotyczących ochrony danych, zobacz [Omówienie ochrony danych](data-protection-overview.md).

## <a name="enable-blob-soft-delete"></a>Włączanie usuwania nie softowego obiektu blob

Usuwanie nie softowe obiektów blob jest domyślnie wyłączone dla nowego konta magazynu. Usuwanie nie softowe dla konta magazynu można w dowolnym momencie włączyć lub wyłączyć przy użyciu interfejsu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby włączyć usuwanie nie softowe obiektów blob dla konta magazynu przy użyciu Azure Portal, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) przejdź do swojego konta magazynu.
1. Znajdź opcję **Ochrona danych** w **obszarze Blob service**.
1. W sekcji **Odzyskiwanie** wybierz pozycję **Włącz usuwanie nie softowe dla obiektów blob**.
1. Określ okres przechowywania od 1 do 365 dni. Firma Microsoft zaleca minimalny okres przechowywania na siedem dni.
1. Zapisz zmiany.

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Zrzut ekranu przedstawiający sposób włączania usuwania nie soft Azure Portal":::

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby włączyć usuwanie nie softowe obiektów blob za pomocą programu PowerShell, wywołaj polecenie [Enable-AzStorageBlobDeleteRetentionPolicy,](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) określając okres przechowywania w dniach.

Poniższy przykład umożliwia usuwanie nie softowe obiektów blob i ustawia okres przechowywania na siedem dni. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

Aby sprawdzić bieżące ustawienia usuwania nie softowego obiektu blob, wywołaj polecenie [Get-AzStorageBlobServiceProperty:](/powershell/module/az.storage/get-azstorageblobserviceproperty)

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-CLI)

Aby włączyć usuwanie nie softowe obiektów blob za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [az storage account blob-service-properties update,](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) określając okres przechowywania w dniach.

Poniższy przykład umożliwia usuwanie nieechowe obiektów blob i ustawia okres przechowywania na siedem dni. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Aby sprawdzić bieżące ustawienia usuwania nie programowego obiektu blob, wywołaj [polecenie az storage account blob-service-properties show:](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_show)

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Następne kroki

- [Usuwanie nietrwałe dla obiektów blob](soft-delete-blob-overview.md)
- [Zarządzanie nieukońcamiatywnie usuniętymi obiektami blob i ich przywracanie](soft-delete-blob-manage.md)
