---
title: Włączanie i zarządzanie nietrwałego usuwania dla obiektów BLOB
titleSuffix: Azure Storage
description: Włącz nietrwałe usuwanie obiektów blob, aby ułatwić odzyskiwanie danych w przypadku ich błędnego zmodyfikowania lub usunięcia.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: a74860b7adf4dade5aedc71a4960595cbe55eaf0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995305"
---
# <a name="enable-and-manage-soft-delete-for-blobs"></a>Włączanie i zarządzanie nietrwałego usuwania dla obiektów BLOB

Usuwanie nietrwałe obiektów BLOB chroni dane przed przypadkowym lub błędnym modyfikacją lub usunięciem. Po włączeniu usuwania nietrwałego obiektów BLOB dla konta magazynu, obiektów blob, wersji obiektów blob i migawek w ramach tego konta magazynu mogą zostać odzyskane po ich usunięciu w okresie przechowywania określonym przez użytkownika.

Jeśli istnieje możliwość, że dane mogą zostać przypadkowo zmodyfikowane lub usunięte przez aplikację lub innego użytkownika konta magazynu, firma Microsoft zaleca włączenie usuwania nietrwałego obiektu BLOB. W tym artykule pokazano, jak włączyć usuwanie nietrwałe dla obiektów BLOB. Aby uzyskać więcej informacji na temat usuwania nietrwałego obiektów blob, zobacz [usuwanie nietrwałe dla obiektów BLOB](soft-delete-blob-overview.md).

Aby dowiedzieć się, jak włączyć opcję usuwania nietrwałego dla kontenerów, zobacz [Włączanie i zarządzanie usuwaniem nietrwałym dla kontenerów](soft-delete-container-enable.md).

## <a name="enable-blob-soft-delete"></a>Włącz usuwanie nietrwałe obiektów BLOB

# <a name="portal"></a>[Portal](#tab/azure-portal)

Włącz usuwanie nietrwałe dla obiektów BLOB na koncie magazynu przy użyciu Azure Portal:

1. W witrynie [Azure Portal](https://portal.azure.com/) przejdź do swojego konta magazynu.
1. Znajdź opcję **ochrony danych** w obszarze **BLOB Service**.
1. Ustaw właściwość **usuwania nietrwałego obiektu BLOB** na wartość *włączone*.
1. W obszarze **zasady przechowywania** Określ, jak długo usuwane nietrwałe obiekty blob są przechowywane przez usługę Azure Storage.
1. Zapisz zmiany.

![Zrzut ekranu witryny Azure Portal z wybraną usługą obiektów BLOB ochrony danych.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-configuration.png)

Aby wyświetlić nietrwałe usunięte obiekty blob, zaznacz pole wyboru **Pokaż usunięte obiekty blob** .

![Zrzut ekranu strony usługi obiektów BLOB ochrony danych z wyróżnioną opcją Pokaż usunięte obiekty blob.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted.png)

Aby wyświetlić nietrwałe usunięte migawki dla danego obiektu BLOB, wybierz obiekt BLOB, a następnie kliknij pozycję **Wyświetl migawki**.

![Zrzut ekranu strony usługi obiektów BLOB ochrony danych z wyróżnioną opcją Wyświetl migawki.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Upewnij się, że zaznaczone jest pole wyboru **Pokaż usunięte migawki** .

![Zrzut ekranu strony wyświetlanie migawek z wyróżnioną opcją Pokaż usunięte obiekty blob.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Po kliknięciu nietrwałego usuniętego obiektu BLOB lub migawki Zwróć uwagę na nowe właściwości obiektu BLOB. Wskazują one, kiedy obiekt został usunięty i ile dni pozostało do momentu stałego wygaśnięcia migawki obiektu BLOB lub obiektu BLOB. Jeśli niewygładzony obiekt usunięty nie jest migawką, będzie również można cofnąć jego usunięcie.

![Zrzut ekranu przedstawiający szczegóły usuniętego obiektu nietrwałego.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-properties.png)

Należy pamiętać, że usunięcie obiektu BLOB spowoduje również cofnięcie usunięcia wszystkich skojarzonych migawek. Aby cofnąć usunięcie nieusuniętych migawek nietrwałych dla aktywnego obiektu BLOB, kliknij obiekt BLOB i wybierz pozycję **Cofnij usunięcie wszystkich migawek**.

![Zrzut ekranu przedstawiający szczegóły nietrwałego usuniętego obiektu BLOB.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Po cofnięciu usunięcia migawek obiektu BLOB można kliknąć pozycję **Podwyższ poziom** , aby skopiować migawkę na głównym obiekcie blob, a tym samym przywrócić obiekt BLOB do migawki.

![Zrzut ekranu przedstawiający stronę wyświetlanie migawek z wyróżnioną opcją podwyższania poziomu.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby włączyć usuwanie nietrwałe, zaktualizuj właściwości usługi klienta obiektów BLOB. Poniższy przykład umożliwia usuwanie nietrwałe dla podzbioru kont w ramach subskrypcji:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```

Można sprawdzić, czy usuwanie nietrwałe zostało włączone przy użyciu następującego polecenia:

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

Aby odzyskiwać obiekty blob przypadkowo usunięte, wywołaj metodę **Undelete BLOB na tych obiektach** BLOB. Należy pamiętać, że wywoływanie **usuwania obiektów BLOB**, zarówno aktywnych, jak i nietrwałych usuniętych obiektów blob, spowoduje przywrócenie wszystkich skojarzonych nietrwałych migawek usuniętych jako aktywne. Poniższy przykład wywołuje **cofanie usunięcia obiektu BLOB** dla wszystkich nietrwałych usuniętych i aktywnych obiektów BLOB w kontenerze:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Aby znaleźć bieżące zasady przechowywania nietrwałego usuwania, użyj następującego polecenia:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-CLI)

Aby włączyć usuwanie nietrwałe, zaktualizuj właściwości usługi klienta obiektów blob:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Aby sprawdzić, czy usuwanie nietrwałe jest włączone, użyj następującego polecenia: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

Aby włączyć usuwanie nietrwałe, zaktualizuj właściwości usługi klienta obiektów blob:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Aby włączyć usuwanie nietrwałe, zaktualizuj właściwości usługi klienta obiektów blob:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_EnableSoftDelete":::

Aby odzyskać obiekty blob, które zostały przypadkowo usunięte, wywołaj Cofnięcie usunięcia na tych obiektach Blob. Należy pamiętać, że wywołanie **cofnięcia usunięcia**, zarówno w aktywnych, jak i niemiękkich obiektach Blob, spowoduje przywrócenie wszystkich skojarzonych nietrwałych migawek usuniętych jako aktywne. Poniższy przykład wywołuje Cofnięcie usunięcia wszystkich nietrwałych usuniętych i aktywnych obiektów BLOB w kontenerze:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Aby odzyskać do określonej wersji obiektu BLOB, najpierw Wywołaj metodę Undelete na obiekcie blob, a następnie skopiuj żądaną migawkę na obiekt BLOB. Poniższy przykład odzyskuje blokowy obiekt BLOB do ostatnio wygenerowanej migawki:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobVersion":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Aby włączyć usuwanie nietrwałe, zaktualizuj właściwości usługi klienta obiektów blob:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Aby odzyskiwać obiekty blob przypadkowo usunięte, wywołaj metodę **Undelete BLOB na tych obiektach** BLOB. Należy pamiętać, że wywoływanie **usuwania obiektów BLOB**, zarówno aktywnych, jak i nietrwałych usuniętych obiektów blob, spowoduje przywrócenie wszystkich skojarzonych nietrwałych migawek usuniętych jako aktywne. Poniższy przykład wywołuje **cofanie usunięcia obiektu BLOB** dla wszystkich usuniętych nietrwałych i aktywnych obiektów BLOB w kontenerze:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Aby odzyskać do określonej wersji obiektu BLOB, najpierw Wywołaj operację **cofania usuwania obiektu BLOB** , a następnie skopiuj żądaną migawkę na obiekt BLOB. Poniższy przykład odzyskuje blokowy obiekt BLOB do ostatnio wygenerowanej migawki:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

## <a name="next-steps"></a>Następne kroki

- [Usuwanie nietrwałe dla magazynu obiektów BLOB](./soft-delete-blob-overview.md)
- [Przechowywanie wersji obiektów BLOB](versioning-overview.md)