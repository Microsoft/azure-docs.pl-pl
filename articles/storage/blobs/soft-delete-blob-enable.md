---
title: Włączanie usuwania nietrwałego dla obiektów blob
titleSuffix: Azure Storage
description: Włącz nietrwałe usuwanie obiektów BLOB w celu ochrony danych obiektów BLOB przed przypadkowym usunięciem lub zastępowaniem.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 11323f2aec05935b9dc45187ed54597e61af924d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554125"
---
# <a name="enable-soft-delete-for-blobs"></a>Włączanie usuwania nietrwałego dla obiektów blob

Usuwanie nietrwałe obiektów BLOB chroni pojedynczy obiekt BLOB, jego wersje, migawki i metadane przed przypadkowym usunięciem lub zastępowaniem przez utrzymywanie usuniętych danych w systemie przez określony czas. W okresie przechowywania można przywrócić obiekt BLOB do jego stanu podczas usuwania. Po upływie okresu przechowywania obiekt BLOB zostanie trwale usunięty. Aby uzyskać więcej informacji na temat usuwania nietrwałego obiektów blob, zobacz [usuwanie nietrwałe dla obiektów BLOB](soft-delete-blob-overview.md).

Usuwanie nietrwałe obiektów BLOB jest częścią kompleksowej strategii ochrony danych obiektów BLOB. Aby dowiedzieć się więcej na temat zaleceń firmy Microsoft dotyczących ochrony danych, zobacz [Omówienie ochrony danych](data-protection-overview.md).

## <a name="enable-blob-soft-delete"></a>Włącz usuwanie nietrwałe obiektów BLOB

Usuwanie nietrwałe obiektów BLOB jest domyślnie wyłączone dla nowego konta magazynu. Można włączyć lub wyłączyć usuwanie nietrwałe dla konta magazynu w dowolnym momencie przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby włączyć usuwanie nietrwałe obiektów BLOB dla konta magazynu przy użyciu Azure Portal, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) przejdź do swojego konta magazynu.
1. Znajdź opcję **ochrony danych** w obszarze **BLOB Service**.
1. W sekcji **odzyskiwanie** wybierz pozycję **Włącz usuwanie nietrwałe dla obiektów BLOB**.
1. Określ okres przechowywania od 1 do 365 dni. Firma Microsoft zaleca minimalny okres przechowywania wynoszący siedem dni.
1. Zapisz zmiany.

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Zrzut ekranu przedstawiający sposób włączania usuwania nietrwałego w Azure Portal":::

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby włączyć usuwanie nietrwałe obiektów BLOB za pomocą programu PowerShell, wywołaj polecenie [enable-AzStorageBlobDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) , określając okres przechowywania w dniach.

Poniższy przykład umożliwia usunięcie nietrwałego obiektu BLOB i ustawia okres przechowywania na siedem dni. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

Aby sprawdzić bieżące ustawienia dla usuwania nietrwałego obiektu BLOB, wywołaj polecenie [Get-AzStorageBlobServiceProperty](/powershell/module/az.storage/get-azstorageblobserviceproperty) :

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-CLI)

Aby włączyć usuwanie nietrwałe obiektów BLOB za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage account BLOB-Service-Properties Update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) , określając okres przechowywania w dniach.

Poniższy przykład umożliwia usunięcie nietrwałego obiektu BLOB i ustawia okres przechowywania na siedem dni. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Aby sprawdzić bieżące ustawienia dla usuwania nietrwałego obiektu BLOB, wywołaj polecenie [AZ Storage account BLOB-Service-Properties show](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_show) :

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Następne kroki

- [Usuwanie nietrwałe dla obiektów blob](soft-delete-blob-overview.md)
- [Zarządzanie i przywracanie nietrwałych usuniętych obiektów BLOB](soft-delete-blob-manage.md)
