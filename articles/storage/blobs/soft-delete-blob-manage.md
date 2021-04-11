---
title: Zarządzanie i przywracanie nietrwałych usuniętych obiektów BLOB
titleSuffix: Azure Storage
description: Zarządzaj i przywracaj nietrwałe usunięte obiekty blob i migawki przy użyciu Azure Portal lub bibliotek klienckich usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9d5ef85d947ae999fd94ba5a6e9cdb00baec9786
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556151"
---
# <a name="manage-and-restore-soft-deleted-blobs"></a>Zarządzanie i przywracanie nietrwałych usuniętych obiektów BLOB

Usuwanie nietrwałe obiektów BLOB chroni pojedynczy obiekt BLOB, jego wersje, migawki i metadane przed przypadkowym usunięciem lub zastępowaniem przez utrzymywanie usuniętych danych w systemie przez określony czas. W okresie przechowywania można przywrócić obiekt BLOB do jego stanu podczas usuwania. Po upływie okresu przechowywania obiekt BLOB zostanie trwale usunięty. Aby uzyskać więcej informacji na temat usuwania nietrwałego obiektów blob, zobacz [usuwanie nietrwałe dla obiektów BLOB](soft-delete-blob-overview.md).

Usuwanie nietrwałe obiektów BLOB jest częścią kompleksowej strategii ochrony danych obiektów BLOB. Aby dowiedzieć się więcej na temat zaleceń firmy Microsoft dotyczących ochrony danych, zobacz [Omówienie ochrony danych](data-protection-overview.md).

## <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Zarządzanie niemiękkimi, usuniętymi obiektami BLOB za pomocą Azure Portal

Azure Portal służy do wyświetlania i przywracania usuniętych, nietrwałych obiektów blob i migawek.

### <a name="view-deleted-blobs"></a>Wyświetlanie usuniętych obiektów BLOB

Gdy obiekty blob są usuwane nietrwale, domyślnie są niewidoczne w Azure Portal. Aby wyświetlić nietrwałe usunięte obiekty blob, przejdź do strony **Przegląd** kontenera i przełącz ustawienie **Pokaż usunięte obiekty blob** . Nietrwałe usunięte obiekty blob są wyświetlane ze stanem **usunięte**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blobs-list-portal.png" alt-text="Zrzut ekranu przedstawiający sposób wyświetlania nietrwałych usuniętych obiektów BLOB w Azure Portal":::

Następnie wybierz usunięty obiekt BLOB z listy obiektów blob, aby wyświetlić jego właściwości. Na karcie **Przegląd** należy zauważyć, że stan obiektu BLOB jest ustawiony na wartość **usunięte**. W portalu jest również wyświetlana liczba dni, po których obiekt BLOB zostanie trwale usunięty.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-properties-portal.png" alt-text="Zrzut ekranu przedstawiający właściwości nieusuniętego obiektu BLOB w Azure Portal":::

### <a name="view-deleted-snapshots"></a>Wyświetl usunięte migawki

Usunięcie obiektu BLOB spowoduje również usunięcie wszystkich migawek skojarzonych z obiektem BLOB. Jeśli obiekt BLOB, który został usunięty, zawiera migawki, usunięte migawki mogą być również wyświetlane w portalu. Wyświetl właściwości nietrwałego obiektu BLOB, a następnie przejdź do karty **migawki** i Przełącz **Pokaż usunięte migawki**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-snapshots-portal.png" alt-text="Zrzut ekranu przedstawiający ":::

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Przywracanie nietrwałych obiektów usuniętych, gdy przechowywanie wersji jest wyłączone

Aby przywrócić nietrwały usunięty obiekt BLOB w Azure Portal, gdy nie włączono obsługi wersji obiektu BLOB, najpierw Wyświetl właściwości obiektu BLOB, a następnie wybierz przycisk **Cofnij usunięcie** na karcie **Przegląd** . Przywrócenie obiektu BLOB spowoduje również przywrócenie wszystkich migawek usuniętych w okresie przechowywania nietrwałego.

:::image type="content" source="media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal.png" alt-text="Zrzut ekranu przedstawiający sposób przywracania nietrwałego usuniętego obiektu BLOB w Azure Portal":::

Aby podwyższyć poziom nietrwałej migawki do podstawowego obiektu BLOB, najpierw upewnij się, że przywrócono nietrwałe migawki obiektu BLOB. Wybierz przycisk **Cofnij usunięcie** , aby przywrócić nietrwałe usunięte migawki obiektu BLOB, nawet jeśli sam obiekt BLOB nie został usunięty. Następnie wybierz migawkę, aby podwyższyć poziom i użyj przycisku **Podnieś poziom migawki** , aby zastąpić podstawowy obiekt BLOB zawartością migawki.

:::image type="content" source="media/soft-delete-blob-manage/promote-snapshot.png" alt-text="Zrzut ekranu przedstawiający sposób podwyższania poziomu migawki do podstawowego obiektu BLOB":::

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Przywróć nietrwałe usunięte obiekty blob po włączeniu obsługi wersji

Aby przywrócić obiekt BLOB usunięty z nietrwałego w Azure Portal po włączeniu obsługi wersji, wybierz obiekt BLOB, który został usunięty, aby wyświetlić jego właściwości, a następnie wybierz kartę **wersje** . Wybierz wersję, którą chcesz podnieść jako bieżącą wersję, a następnie wybierz pozycję **Utwórz bieżącą wersję**.  

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-promote-version-portal.png" alt-text="Zrzut ekranu przedstawiający sposób podwyższania poziomu wersji w celu przywrócenia obiektu BLOB w Azure Portal":::

Aby przywrócić usunięte wersje lub migawki po włączeniu obsługi wersji, Wyświetl właściwości obiektu BLOB, a następnie wybierz przycisk **Cofnij usunięcie** na karcie **Przegląd** .

> [!NOTE]
> Po włączeniu obsługi wersji, wybranie przycisku **Cofnij usunięcie** w usuniętym obiekcie blob spowoduje przywrócenie wszystkich nieusuniętych wersji lub migawek, ale nie spowoduje przywrócenia podstawowego obiektu BLOB. Aby przywrócić podstawowy obiekt BLOB, należy podwyższyć poziom poprzedniej wersji.

## <a name="manage-soft-deleted-blobs-with-code"></a>Zarządzanie niemiękkimi, usuniętymi obiektami BLOB za pomocą kodu

Korzystając z bibliotek klienckich usługi Azure Storage, można przywrócić nietrwale usunięty obiekt BLOB lub migawkę. W poniższych przykładach pokazano, jak używać biblioteki klienckiej platformy .NET.

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Przywracanie nietrwałych obiektów usuniętych, gdy przechowywanie wersji jest wyłączone

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Aby przywrócić usunięte obiekty blob, gdy wersja nie jest włączona, wywołaj operację [cofania usuwania obiektów](/rest/api/storageservices/undelete-blob) BLOB na tych obiektach Blob. Operacja **cofnięcia usunięcia obiektu BLOB** przywraca usunięte nietrwałe obiekty blob i wszystkie usunięte migawki skojarzone z tymi obiektami BLOB.

Wywołanie **obiektu BLOB Undelete** na obiekcie blob, który nie został usunięty, nie ma żadnego wpływu. Poniższy przykład wywołuje **cofa usunięcie obiektu BLOB na wszystkich obiektach** BLOB w kontenerze i przywraca nieusunięte obiekty blob i ich migawki:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Aby przywrócić określoną wersję, najpierw Wywołaj operację **cofania usuwania obiektu** BLOB na podstawowym serwerze obiektów blob lub wersji, a następnie skopiuj żądaną wersję za pomocą bazowego obiektu BLOB. Poniższy przykład przywraca blokowy obiekt BLOB do ostatnio zapisanej wersji:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobSnapshot":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Aby przywrócić usunięte obiekty blob, gdy wersja nie jest włączona, wywołaj operację [cofania usuwania obiektów](/rest/api/storageservices/undelete-blob) BLOB na tych obiektach Blob. Operacja **cofnięcia usunięcia obiektu BLOB** przywraca usunięte nietrwałe obiekty blob i wszystkie usunięte migawki skojarzone z tymi obiektami BLOB.

Wywołanie **obiektu BLOB Undelete** na obiekcie blob, który nie został usunięty, nie ma żadnego wpływu. Poniższy przykład wywołuje **cofa usunięcie obiektu BLOB na wszystkich obiektach** BLOB w kontenerze i przywraca nieusunięte obiekty blob i ich migawki:

```csharp
// Restore all blobs in a container.
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Aby przywrócić konkretną migawkę, najpierw Wywołaj operację **cofania usuwania obiektu** BLOB na podstawowym obiekcie blob, a następnie skopiuj żądaną migawkę przez podstawowy obiekt BLOB. Poniższy przykład przywraca blokowy obiekt BLOB do ostatnio wygenerowanej migawki:

```csharp
// Restore the block blob.
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container, prefixed by the blob name.
IEnumerable<IListBlobItem> allBlobSnapshots = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Copy the most recently generated snapshot to the base blob.
CloudBlockBlob copySource = allBlobSnapshots.First(snapshot => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)snapshot).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Przywróć nietrwałe usunięte obiekty blob po włączeniu obsługi wersji

Aby przywrócić usunięty przez program obiekt BLOB, gdy jest włączona obsługa wersji, skopiuj poprzednią wersję za pomocą bazowego obiektu BLOB z operacją [copy](/rest/api/storageservices/copy-blob) BLOB lub [Kopiuj obiekt BLOB z adresu URL](/rest/api/storageservices/copy-blob-from-url) .  

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RestorePreviousVersion":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Nie dotyczy. Obsługa wersji obiektów BLOB jest obsługiwana tylko w bibliotekach klienta usługi Azure Storage w wersji 12. x lub nowszej.

---

## <a name="next-steps"></a>Następne kroki

- [Usuwanie nietrwałe dla magazynu obiektów BLOB](./soft-delete-blob-overview.md)
- [Włączanie usuwania nietrwałego dla obiektów blob](soft-delete-blob-enable.md)
- [Przechowywanie wersji obiektów BLOB](versioning-overview.md)
