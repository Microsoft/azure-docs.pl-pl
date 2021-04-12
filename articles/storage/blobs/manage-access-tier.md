---
title: Zarządzanie warstwą dostępu obiektu BLOB na koncie usługi Azure Storage
description: Dowiedz się, jak zmienić warstwę obiektu BLOB na koncie GPv2 lub Blob Storage
author: twooley
ms.author: twooley
ms.date: 01/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: cd46f5b5a8847150bd56c1daeaac470f9afd2d19
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278545"
---
# <a name="manage-the-access-tier-of-a-blob-in-an-azure-storage-account"></a>Zarządzanie warstwą dostępu obiektu BLOB na koncie usługi Azure Storage

Każdy obiekt BLOB ma domyślną warstwę dostępu (gorąca, chłodna lub archiwalna). Obiekt BLOB przyjmuje domyślną warstwę dostępu do konta usługi Azure Storage, na którym został utworzony. Konta Blob Storage i GPv2 uwidaczniają atrybut **Warstwa dostępu** na poziomie konta. Ten atrybut określa domyślną warstwę dostępu dla dowolnego obiektu BLOB, który nie został jawnie ustawiony na poziomie obiektu. W przypadku obiektów z zestawem warstwy na poziomie obiektu warstwa konta nie będzie stosowana. Warstwę archiwum można stosować tylko na poziomie obiektu. Można przełączać się między warstwami dostępu w dowolnym momencie, wykonując poniższe kroki.

## <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Zmienianie warstwy obiektu BLOB na koncie GPv2 lub Blob Storage

W poniższych scenariuszach użyto Azure Portal lub programu PowerShell:

# <a name="portal"></a>[Portal](#tab/portal)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W Azure Portal Wyszukaj i wybierz pozycję **wszystkie zasoby**.

1. Wybierz swoje konto magazynu.

1. Wybierz kontener, a następnie wybierz obiekt BLOB.

1. We **właściwościach obiektu BLOB** wybierz pozycję **Zmień warstwę**.

1. Wybierz warstwę dostępu **gorąca**, **chłodna** lub **archiwalna** . Jeśli obiekt BLOB znajduje się obecnie w archiwum i chcesz go umieścić w warstwie online, możesz również wybrać priorytet rehydratacji dla warstwy **standardowa** lub **wysoka**.

1. Wybierz pozycję **Zapisz** u dołu.

![Zmień warstwę obiektów BLOB w Azure Portal](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Za pomocą poniższego skryptu programu PowerShell można zmienić warstwę obiektów BLOB. `$rgName`Zmienna musi być zainicjowana przy użyciu nazwy grupy zasobów. `$accountName`Zmienna musi zostać zainicjowana przy użyciu nazwy konta magazynu. `$containerName`Zmienna musi być zainicjowana przy użyciu nazwy kontenera. `$blobName`Zmienna musi być zainicjowana przy użyciu nazwy obiektu BLOB.

```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```

---

## <a name="next-steps"></a>Następne kroki

- [Jak zarządzać domyślną warstwą dostępu do konta usługi Azure Storage](../common/manage-account-default-access-tier.md)
- [Dowiedz się więcej na temat ponownego wypełniania danych obiektów BLOB w warstwie Archiwum](storage-blob-rehydration.md)
- [Sprawdzanie cen warstw Gorąca, Chłodna i Archiwum na kontach usługi Blob Storage i GPv2 według regionu](https://azure.microsoft.com/pricing/details/storage/)
