---
title: Usuwanie nietrwałe dla obiektów BLOB usługi Azure Storage | Microsoft Docs
description: Usługa Azure Storage oferuje teraz nietrwałe usuwanie obiektów blob, dzięki czemu można łatwiej odzyskać dane, gdy są one błędnie modyfikowane lub usuwane przez aplikację lub innego użytkownika konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4deae235ed15d02874ab5cb3470c62e934324364
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80234293"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Soft delete for Azure Storage blobs (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)

Usługa Azure Storage oferuje teraz nietrwałe usuwanie obiektów blob, dzięki czemu można łatwiej odzyskać dane, gdy są one błędnie modyfikowane lub usuwane przez aplikację lub innego użytkownika konta magazynu.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-soft-delete-works"></a>Jak działa usuwanie nietrwałe

Po włączeniu funkcja usuwania nietrwałego umożliwia zapisanie i odzyskanie danych po usunięciu obiektów blob lub migawek obiektów BLOB. Ta ochrona rozciąga się na dane obiektów blob, które są wymazywane jako wynik zastępowania.

Po usunięciu danych następuje przejście do nietrwałego stanu usuniętego zamiast wyczyszczenia. Gdy usuwanie nietrwałe jest włączone i zastąpi dane, generowana jest nietrwała migawka, która umożliwia zapisanie stanu nadpisanych danych. Usunięte obiekty nietrwałe są niewidoczne, chyba że są jawnie wymienione. Możesz skonfigurować ilość czasu, przez który nietrwale usunięte dane będą możliwe do odzyskania, zanim definitywnie wygasną.

Usuwanie nietrwałe jest zgodne z poprzednimi wersjami, więc nie musisz wprowadzać żadnych zmian w aplikacjach, aby korzystać z ochrony tej funkcji. Jednak [odzyskiwanie danych](#recovery) wprowadza nowy, **cofający Usuwanie interfejsu API obiektów BLOB** .

### <a name="configuration-settings"></a>Ustawienia konfiguracji

Podczas tworzenia nowego konta usuwanie nietrwałe jest domyślnie wyłączone. Usuwanie nietrwałe jest również domyślnie wyłączone dla istniejących kont magazynu. Funkcję można włączać i wyłączać w dowolnym momencie w trakcie okresu istnienia konta magazynu.

Nadal będzie można uzyskiwać dostęp do nietrwałych danych usuniętych i odzyskiwać je, gdy ta funkcja jest wyłączona, przy założeniu, że nietrwałe usunięte dane zostały zapisane, gdy funkcja była wcześniej włączona. Po włączeniu usuwania nietrwałego należy również skonfigurować okres przechowywania.

Okres przechowywania wskazuje ilość czasu, przez który nietrwałe usunięte dane są przechowywane i dostępne do odzyskania. W przypadku obiektów blob i migawek obiektów blob, które są jawnie usuwane, zegar okresu przechowywania jest uruchamiany po usunięciu danych. W przypadku nietrwałych usuniętych migawek wygenerowanych przez funkcję usuwania nietrwałego, gdy dane są zastępowane, Zegar rozpocznie się po wygenerowaniu migawki. Obecnie można zachować nietrwałe dane usunięte z przedziału od 1 do 365 dni.

Okres przechowywania nietrwałego usuwania można zmienić w dowolnym momencie. Zaktualizowany okres przechowywania będzie dotyczył tylko nowo usuniętych danych. Wcześniej usunięte dane wygaśnie na podstawie okresu przechowywania, który został skonfigurowany podczas usuwania tych danych. Próba usunięcia nietrwałego usuniętego obiektu nie wpłynie na jego czas wygaśnięcia.

### <a name="saving-deleted-data"></a>Zapisywanie usuniętych danych

Nietrwałe usuwanie zachowuje dane w wielu przypadkach, gdy obiekty blob lub migawki obiektów BLOB są usuwane lub zastępowane.

Gdy obiekt BLOB zostanie zastąpiony przy użyciu funkcji **Put BLOB**, **Put Block**, **Put Block list**lub **copy BLOB** , migawka stanu obiektu BLOB przed operacją zapisu jest generowana automatycznie. Ta migawka jest usuwaną nietrwałą migawką; jest on niewidoczny, jeśli nie zostaną jawnie wymienione nietrwałe obiekty usunięte. Zobacz sekcję [odzyskiwanie](#recovery) , aby dowiedzieć się, jak wyświetlić nietrwałe obiekty usunięte.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Usunięte dane nietrwałe są szare, a aktywne dane są niebieskie. Ostatnio zapisywane dane są wyświetlane poniżej starszych danych. Gdy B0 jest zastępowany przez B1, generowana jest nietrwałe migawka B0. Gdy B1 zostanie zastąpiona przez B2, generowana jest niewygładzona migawka z B1.*

> [!NOTE]  
> Funkcja usuwania nietrwałego umożliwia zastępowanie ochrony operacji kopiowania, gdy jest włączona dla konta docelowego obiektu BLOB.

> [!NOTE]  
> Usuwanie nietrwałe nie zapewnia ochrony przed zastąpieniem obiektów BLOB w warstwie archiwum. Jeśli obiekt BLOB w archiwum zostanie zastąpiony nowym obiektem BLOB w dowolnej warstwie, zastąpiony obiekt BLOB zostanie trwale wygasł.

Gdy **obiekt BLOB jest usuwany** w migawce, migawka jest oznaczona jako nietrwała. Nie Wygenerowano nowej migawki.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Usunięte dane nietrwałe są szare, a aktywne dane są niebieskie. Ostatnio zapisywane dane są wyświetlane poniżej starszych danych. Po wywołaniu **obiektu BLOB Snapshot** B0 stanie się migawką, a B1 jest aktywnym stanem obiektu BLOB. Po usunięciu migawki B0 jest ona oznaczona jako nietrwała.*

Kiedy **obiekt BLOB Delete** jest wywoływany na bazowym obiekcie BLOB (dowolny obiekt BLOB, który nie jest samym migawką), ten obiekt BLOB jest oznaczony jako usunięty. Spójne z poprzednim zachowaniem, wywołanie **usuwania obiektów BLOB** na obiekcie blob, który ma aktywne migawki, zwraca błąd. Wywołanie metody **delete BLOB** na obiekcie blob z nietrwałymi usuniętymi migawkami nie powoduje zwrócenia błędu. Nadal można usunąć obiekt BLOB i wszystkie jego migawki w ramach jednej operacji, gdy jest włączona funkcja usuwania nietrwałego. Spowoduje to oznaczenie podstawowego obiektu BLOB i migawek jako nietrwałego usunięcia.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Usunięte dane nietrwałe są szare, a aktywne dane są niebieskie. Ostatnio zapisywane dane są wyświetlane poniżej starszych danych. W tym miejscu jest wykonywane wywołanie **usuwania obiektu BLOB** , aby usunąć B2 i wszystkie skojarzone migawki. Aktywne obiekty blob, B2 i wszystkie skojarzone migawki są oznaczane jako nietrwałe usunięte.*

> [!NOTE]  
> Po nadpisaniu nietrwałego usuniętego obiektu BLOB nietrwała usunięta migawka stanu obiektu BLOB przed operacją zapisu jest generowana automatycznie. Nowy obiekt BLOB dziedziczy warstwę nadpisywanego obiektu BLOB.

Usuwanie nietrwałe nie zapisuje danych w przypadku usuwania kontenerów lub kont, a także gdy metadane obiektów blob i właściwości obiektu BLOB są zastępowane. Aby chronić konto magazynu przed błędnym usunięciem, można skonfigurować blokadę przy użyciu Azure Resource Manager. Zapoznaj się z artykułem Azure Resource Manager [Zablokuj zasoby, aby zapobiec nieoczekiwanym zmianom](../../azure-resource-manager/management/lock-resources.md) .

W poniższej tabeli przedstawiono oczekiwane zachowanie podczas włączania usuwania nietrwałego:

| Operacja interfejsu API REST | Typ zasobu | Opis | Zmiana w zachowaniu |
|--------------------|---------------|-------------|--------------------|
| [Usuwanie](/rest/api/storagerp/StorageAccounts/Delete) | Konto | Usuwa konto magazynu, w tym wszystkie kontenery i obiekty blob, które zawiera.                           | Bez zmian. Kontenery i obiekty blob w usuniętym koncie nie są możliwe do odzyskania. |
| [Usuwanie kontenera](/rest/api/storageservices/delete-container) | Kontener | Usuwa kontener, w tym wszystkie obiekty blob, które zawiera. | Bez zmian. Nie da się odzyskać obiektów BLOB w usuniętym kontenerze. |
| [Wstawianie obiektu blob](/rest/api/storageservices/put-blob) | Blokowe, dołączanie i stronicowe obiekty blob | Tworzy nowy obiekt BLOB lub zastępuje istniejący obiekt BLOB w kontenerze | Jeśli jest używany do zastępowania istniejącego obiektu BLOB, automatycznie generowana jest migawka stanu obiektu BLOB przed wywołaniem. Dotyczy to również wcześniej nietrwałego usuniętego obiektu BLOB, jeśli i tylko wtedy, gdy jest zastępowany przez obiekt BLOB tego samego typu (blok, dołączanie lub strona). Jeśli zostanie on zastąpiony przez obiekt BLOB innego typu, wszystkie istniejące nietrwałe dane usunięte zostaną trwale wygasłe. |
| [Usuwanie obiektu blob](/rest/api/storageservices/delete-blob) | Blokowe, dołączanie i stronicowe obiekty blob | Oznacza obiekt BLOB lub migawkę obiektu BLOB do usunięcia. Obiekt BLOB lub migawka został później usunięty podczas wyrzucania elementów bezużytecznych | Jeśli zostanie użyta do usunięcia migawki obiektu BLOB, ta migawka jest oznaczona jako nietrwała. Jeśli jest używany do usuwania obiektu BLOB, ten obiekt BLOB jest oznaczony jako usunięty. |
| [Kopiowanie obiektu blob](/rest/api/storageservices/copy-blob) | Blokowe, dołączanie i stronicowe obiekty blob | Kopiuje źródłowy obiekt BLOB do docelowego obiektu BLOB na tym samym koncie magazynu lub na innym koncie magazynu. | Jeśli jest używany do zastępowania istniejącego obiektu BLOB, automatycznie generowana jest migawka stanu obiektu BLOB przed wywołaniem. Dotyczy to również wcześniej nietrwałego usuniętego obiektu BLOB, jeśli i tylko wtedy, gdy jest zastępowany przez obiekt BLOB tego samego typu (blok, dołączanie lub strona). Jeśli zostanie on zastąpiony przez obiekt BLOB innego typu, wszystkie istniejące nietrwałe dane usunięte zostaną trwale wygasłe. |
| [Umieść blok](/rest/api/storageservices/put-block) | Blokowe obiekty blob | Tworzy nowy blok, który ma zostać przekazany jako część blokowego obiektu BLOB. | Jeśli używany do zatwierdzania bloku do obiektu BLOB, który jest aktywny, nie ma zmian. Jeśli jest używany do zatwierdzania bloku do obiektu BLOB, który jest usuwany nietrwale, tworzony jest nowy obiekt BLOB, a migawka jest generowana automatycznie w celu przechwycenia stanu nietrwałego usuniętego obiektu BLOB. |
| [Umieść listę zablokowanych](/rest/api/storageservices/put-block-list) | Blokowe obiekty blob | Zatwierdza obiekt BLOB przez określenie zestawu identyfikatorów bloków, które składają się na blokowy obiekt BLOB. | Jeśli jest używany do zastępowania istniejącego obiektu BLOB, automatycznie generowana jest migawka stanu obiektu BLOB przed wywołaniem. Dotyczy to również poprzednio nietrwałego usuniętego obiektu BLOB, jeśli i tylko wtedy, gdy jest to blokowy obiekt BLOB. Jeśli zostanie on zastąpiony przez obiekt BLOB innego typu, wszystkie istniejące nietrwałe dane usunięte zostaną trwale wygasłe. |
| [Umieść stronę](/rest/api/storageservices/put-page) | Stronicowe obiekty blob | Zapisuje zakres stron na stronie obiektu BLOB. | Bez zmian. Dane stronicowego obiektu BLOB zastępowane lub wyczyszczone przy użyciu tej operacji nie zostały zapisane i nie są możliwe do odzyskania. |
| [Dołącz blok](/rest/api/storageservices/append-block) | Obiekty blob dołączania | Zapisuje blok danych na końcu dołączanego obiektu BLOB | Bez zmian. |
| [Ustawianie właściwości obiektu BLOB](/rest/api/storageservices/set-blob-properties) | Blokowe, dołączanie i stronicowe obiekty blob | Ustawia wartości dla właściwości systemu zdefiniowanych dla obiektu BLOB. | Bez zmian. Nie da się odzyskać właściwości obiektu BLOB z zastępowaniem. |
| [Ustawianie metadanych obiektu BLOB](/rest/api/storageservices/set-blob-metadata) | Blokowe, dołączanie i stronicowe obiekty blob | Ustawia metadane zdefiniowane przez użytkownika dla określonego obiektu BLOB jako jedną lub więcej par nazwa-wartość. | Bez zmian. Nie ma możliwości odzyskania nadpisanych metadanych obiektów BLOB. |

Należy zauważyć, że wywołanie "Put Page", aby zastąpić lub wyczyścić zakresy obiektu BLOB stronicowania, nie spowoduje automatycznego generowania migawek. Dyski maszyny wirtualnej są obsługiwane przez stronicowe obiekty blob i wykorzystują **stronę Put** do zapisu danych.

### <a name="recovery"></a>Odzyskiwanie

Wywołanie operacji [cofnięcia usunięcia obiektu](/rest/api/storageservices/undelete-blob) BLOB dla nietrwałego, usuniętego obiektu typu Base spowoduje przywrócenie tego elementu i wszystkich skojarzonych nietrwałych usuniętych migawek jako aktywnych. Wywołanie `Undelete Blob` operacji na aktywnym podstawowym obiekcie blob spowoduje przywrócenie wszystkich skojarzonych nietrwałych migawek usuniętych jako aktywne. Gdy migawki są przywracane jako aktywne, wyglądają jak migawki wygenerowane przez użytkownika; nie zastępuje podstawowego obiektu BLOB.

Aby przywrócić obiekt BLOB do konkretnej usuniętej nietrwałej migawki, `Undelete Blob` można wywołać podstawowy obiekt BLOB. Następnie można skopiować migawkę za pośrednictwem teraz aktywnego obiektu BLOB. Możesz również skopiować migawkę do nowego obiektu BLOB.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Usunięte dane nietrwałe są szare, a aktywne dane są niebieskie. Ostatnio zapisywane dane są wyświetlane poniżej starszych danych. W tym miejscu **obiekt BLOB Undelete** jest wywoływany w obiekcie blob B, a tym samym przywracasz podstawowy obiekt BLOB, B1 i wszystkie skojarzone migawki, tutaj tylko B0 jako aktywny. W drugim kroku B0 jest kopiowany przez podstawowy obiekt BLOB. Ta operacja kopiowania generuje niewygładzoną migawkę z B1.*

Aby wyświetlić nietrwałe usunięte obiekty blob i migawki obiektów blob, możesz dołączyć usunięte dane do **listy obiektów BLOB**. Można wybrać opcję wyświetlania tylko usuniętych nietrwałych obiektów blob lub do dołączenia nietrwałych usuniętych migawek obiektów BLOB. W przypadku wszystkich nietrwałych danych usuniętych można wyświetlić czas, w którym dane zostały usunięte, a także liczbę dni, po których dane będą trwale wygasłe.

### <a name="example"></a>Przykład

Poniżej znajduje się dane wyjściowe konsoli skryptu .NET, który przekazuje, zastępuje, migawka, usuwa i przywraca obiekt BLOB o nazwie *HelloWorld* po włączeniu usuwania nietrwałego:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

Zapoznaj się z sekcją [następne kroki](#next-steps) , aby uzyskać wskaźnik do aplikacji, która wygenerowała te dane wyjściowe.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Wszystkie usunięte nietrwałe dane są rozliczane według tej samej stawki co aktywne dane. Nie zostanie naliczona opłata za dane, które zostały trwale usunięte po upływie skonfigurowanego okresu przechowywania. Aby uzyskać bardziej szczegółowy szczegółowe migawek i sposób ich naliczania, zobacz [Opis sposobu naliczania opłat za migawki](storage-blob-snapshots.md).

Nie będą naliczane opłaty za transakcje związane z automatyczną generowaniem migawek. Opłaty są naliczane za **cofanie usuwania transakcji obiektów BLOB** według stawki za operacje zapisu.

Aby uzyskać więcej szczegółowych informacji na temat cen usługi Azure Blob Storage ogólnie, zapoznaj się z [cennikiem usługi azure BLOB Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Po wstępnym włączeniu usuwania nietrwałego zalecamy użycie małego okresu przechowywania, aby lepiej zrozumieć, jak ta funkcja wpłynie na rachunek.

## <a name="get-started"></a>Wprowadzenie

Poniższe kroki pokazują, jak rozpocząć pracę z usuwaniem nietrwałym.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Włącz usuwanie nietrwałe dla obiektów BLOB na koncie magazynu przy użyciu Azure Portal:

1. W [Azure Portal](https://portal.azure.com/)wybierz konto magazynu. 

2. Przejdź do opcji **Ochrona danych** w obszarze **BLOB Service**.

3. Kliknij pozycję **włączone** w obszarze **usuwanie nietrwałego obiektu BLOB**

4. Wprowadź liczbę dni, które mają zostać *zachowane* w ramach **zasad przechowywania**

5. Wybierz przycisk **Zapisz** , aby potwierdzić ustawienia ochrony danych

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Aby wyświetlić nietrwałe usunięte obiekty blob, zaznacz pole wyboru **Pokaż usunięte obiekty blob** .

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Aby wyświetlić nietrwałe usunięte migawki dla danego obiektu BLOB, wybierz obiekt BLOB, a następnie kliknij pozycję **Wyświetl migawki**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Upewnij się, że zaznaczone jest pole wyboru **Pokaż usunięte migawki** .

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Po kliknięciu nietrwałego usuniętego obiektu BLOB lub migawki Zwróć uwagę na nowe właściwości obiektu BLOB. Wskazują one, kiedy obiekt został usunięty i ile dni pozostało do momentu stałego wygaśnięcia migawki obiektu BLOB lub obiektu BLOB. Jeśli niewygładzony obiekt usunięty nie jest migawką, będzie również można cofnąć jego usunięcie.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Należy pamiętać, że usunięcie obiektu BLOB spowoduje również cofnięcie usunięcia wszystkich skojarzonych migawek. Aby cofnąć usunięcie nieusuniętych migawek nietrwałych dla aktywnego obiektu BLOB, kliknij obiekt BLOB i wybierz pozycję **Cofnij usunięcie wszystkich migawek**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Po cofnięciu usunięcia migawek obiektu BLOB można kliknąć pozycję **Podwyższ poziom** , aby skopiować migawkę na głównym obiekcie blob, a tym samym przywrócić obiekt BLOB do migawki.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[Narzędzia](#tab/azure-powershell)

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

Aby odzyskać obiekty blob, które zostały przypadkowo usunięte, wywołaj Cofnięcie usunięcia na tych obiektach Blob. Należy pamiętać, że wywoływanie **usuwania obiektów BLOB**, zarówno aktywnych, jak i nietrwałych usuniętych obiektów blob, spowoduje przywrócenie wszystkich skojarzonych nietrwałych migawek usuniętych jako aktywne. Poniższy przykład wywołuje Cofnięcie usunięcia wszystkich nietrwałych usuniętych i aktywnych obiektów BLOB w kontenerze:

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

# <a name="net"></a>[.NET](#tab/net)

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

Aby odzyskać obiekty blob, które zostały przypadkowo usunięte, wywołaj Cofnięcie usunięcia na tych obiektach Blob. Należy pamiętać, że wywoływanie **usuwania obiektów BLOB**, zarówno aktywnych, jak i nietrwałych usuniętych obiektów blob, spowoduje przywrócenie wszystkich skojarzonych nietrwałych migawek usuniętych jako aktywne. Poniższy przykład wywołuje Cofnięcie usunięcia wszystkich nietrwałych usuniętych i aktywnych obiektów BLOB w kontenerze:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Aby odzyskać do określonej wersji obiektu BLOB, najpierw Wywołaj metodę Undelete na obiekcie blob, a następnie skopiuj żądaną migawkę na obiekt BLOB. Poniższy przykład odzyskuje blokowy obiekt BLOB do ostatnio wygenerowanej migawki:

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

## <a name="special-considerations"></a>Uwagi specjalne

Jeśli istnieje prawdopodobieństwo, że dane są przypadkowo modyfikowane lub usuwane przez aplikację lub innego użytkownika konta magazynu, zaleca się włączenie usuwania nietrwałego. Włączenie usuwania nietrwałego dla często zamienionych danych może spowodować zwiększenie opłat za pojemność magazynu i zwiększenie opóźnień podczas tworzenia listy obiektów BLOB. Aby wyeliminować ten dodatkowy koszt i czas oczekiwania, można przechowywać często zastąpione dane na osobnym koncie magazynu, w którym jest wyłączone usuwanie nietrwałe. 

## <a name="faq"></a>Najczęściej zadawane pytania

### <a name="for-which-storage-services-can-i-use-soft-delete"></a>Dla których usług magazynu można korzystać z usuwania nietrwałego?

Obecnie usuwanie nietrwałe jest dostępne tylko dla magazynu obiektów BLOB (Object).

### <a name="is-soft-delete-available-for-all-storage-account-types"></a>Czy jest możliwe usuwanie nietrwałe dla wszystkich typów kont magazynu?

Tak, usuwanie nietrwałe jest dostępne dla kont usługi BLOB Storage, a także dla obiektów BLOB w ramach kont magazynu ogólnego przeznaczenia (zarówno GPv1, jak i GPv2). Obsługiwane są zarówno typy kont w warstwie Standardowa, jak i Premium. Usuwanie nietrwałe jest dostępne dla dysków niezarządzanych, które są stronicowymi obiektami BLOB w ramach okładek. Usuwanie nietrwałe nie jest dostępne dla dysków zarządzanych.

### <a name="is-soft-delete-available-for-all-storage-tiers"></a>Czy dla wszystkich warstw magazynowania jest dostępne usuwanie nietrwałe?

Tak, funkcja usuwania nietrwałego jest dostępna dla wszystkich warstw magazynowania, w tym gorąca, chłodna i archiwalna. Jednak usuwanie nietrwałe nie zapewnia ochrony przed zastąpieniem obiektów BLOB w warstwie archiwum.

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Czy można użyć zestawu API warstwy obiektów BLOB do warstwy obiektów blob z nietrwałymi usuniętymi migawkami?

Tak. Usunięte nietrwałe migawki pozostaną w oryginalnej warstwie, ale podstawowy obiekt BLOB zostanie przeniesiony do nowej warstwy. 

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>Dla kont magazynu w warstwie Premium obowiązuje limit migawek obiektów BLOB równy 100. Czy liczba nietrwałych usuniętych migawek zbliża się do tego limitu?

Nie. nietrwałe usunięte migawki nie są wliczane do tego limitu.

### <a name="can-i-turn-on-soft-delete-for-existing-storage-accounts"></a>Czy mogę włączyć usuwanie nietrwałe dla istniejących kont magazynu?

Tak, usuwanie nietrwałe można skonfigurować dla istniejących i nowych kont magazynu.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Czy usunięcie całego konta lub kontenera z włączonym usuwaniem nietrwałego spowoduje zapisanie wszystkich skojarzonych obiektów BLOB?

Nie, jeśli usuniesz całe konto lub kontener, wszystkie skojarzone obiekty blob zostaną trwale usunięte. Aby uzyskać więcej informacji na temat ochrony konta magazynu przed przypadkowym usunięciem, zobacz [blokowanie zasobów, aby zapobiec nieoczekiwanym zmianom](../../azure-resource-manager/management/lock-resources.md).

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Czy można wyświetlić metryki pojemności dla usuniętych danych?

Nietrwałe usunięte dane są dołączane jako część całkowitej pojemności konta magazynu. Aby uzyskać więcej informacji o śledzeniu i monitorowaniu pojemności magazynu, zobacz [analityka magazynu](../common/storage-analytics.md).

### <a name="if-i-turn-off-soft-delete-will-i-still-be-able-to-access-soft-deleted-data"></a>Czy wyłączenie usuwania nietrwałego spowoduje, że nadal będzie można uzyskać dostęp do usuniętych nietrwałych danych?

Tak. nadal będzie można uzyskiwać dostęp do niewygasłych nieaktualnych usuniętych danych i odzyskiwać je w przypadku wyłączenia usuwania nietrwałego.

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Czy mogę odczytywać i kopiować nietrwałe usunięte migawki mojego obiektu BLOB?  

Tak, ale najpierw należy wywołać cofanie usunięcia obiektu BLOB.

### <a name="is-soft-delete-available-for-all-blob-types"></a>Czy dla wszystkich typów obiektów BLOB jest dostępne nietrwałe usuwanie?

Tak, usuwanie nietrwałe jest dostępne dla blokowych obiektów blob, dołączania obiektów blob i stronicowych obiektów BLOB.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>Czy dla dysków maszyny wirtualnej jest dostępne usuwanie nietrwałe?  

Usuwanie nietrwałe jest dostępne dla dysków niezarządzanych w warstwie Premium i standardowa, które są stronicowymi obiektami BLOB w ramach okładek. Funkcja usuwania nietrwałego ułatwia odzyskanie danych usuniętych przez operacje **usuwania obiektów BLOB**, **Put obiektów BLOB**, **Put bloków list**, **Put bloków** i **kopiowania obiektów BLOB** . Dane zastąpione przez wywołanie **strony** nie są możliwe do odzyskania.

Maszyna wirtualna platformy Azure zapisuje dane na dysku niezarządzanym przy użyciu wywołań do **umieszczania na stronie**, dlatego użycie nietrwałego usunięcia umożliwia cofnięcie operacji zapisu na dysku niezarządzanym z maszyny wirtualnej platformy Azure nie jest obsługiwanym scenariuszem.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Czy muszę zmienić istniejące aplikacje, aby użyć usuwania nietrwałego?

Można korzystać z usuwania nietrwałego niezależnie od używanej wersji interfejsu API. Jednak w celu wyświetlania i odzyskiwania nietrwałych usuniętych obiektów blob i migawek obiektów BLOB konieczne będzie użycie wersji 2017-07-29 [interfejsu API REST usług Storage](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) lub nowszego. Firma Microsoft zaleca, aby zawsze używać najnowszej wersji interfejsu API usługi Azure Storage.

## <a name="next-steps"></a>Następne kroki

* [Przykładowy kod platformy .NET](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [Interfejs API REST usługi Blob Service](/rest/api/storageservices/blob-service-rest-api)
* [Replikacja usługi Azure Storage](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Projektowanie aplikacji o wysokiej dostępności przy użyciu usługi RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Odzyskiwanie po awarii i tryb failover konta magazynu (wersja zapoznawcza) w usłudze Azure Storage](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
