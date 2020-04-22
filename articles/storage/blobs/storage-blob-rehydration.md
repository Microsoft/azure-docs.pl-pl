---
title: Nawadnianie danych obiektów blob z warstwy archiwum
description: Nawadniaj swoje obiekty blob z magazynu archiwum, aby uzyskać dostęp do danych.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 82ea4ad23e3207f5641ade196f69595cd1e7b323
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684095"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Nawadnianie danych obiektów blob z warstwy archiwum

Gdy obiekt blob znajduje się w warstwie dostępu do archiwum, jest uważany za w trybie offline i nie można go odczytać ani zmodyfikować. Metadane obiektu blob pozostają w trybie online i dostępne, co pozwala na listę obiektów blob i jego właściwości. Odczytywanie i modyfikowanie danych obiektów blob jest dostępne tylko w warstwach online, takich jak hot lub cool. Istnieją dwie opcje pobierania i uzyskiwania dostępu do danych przechowywanych w warstwie dostępu do archiwum.

1. [Nawadniaj zarchiwizowane obiekty blob do warstwy online](#rehydrate-an-archived-blob-to-an-online-tier) — nawadniaj obiekt blob archiwum na gorąco lub chłodzić, zmieniając jego warstwę przy użyciu operacji [Set Blob Tier.](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)
2. [Kopiowanie zarchiwizowanego obiektu blob do warstwy online](#copy-an-archived-blob-to-an-online-tier) — tworzenie nowej kopii obiektu blob archiwum przy użyciu operacji [Kopiowanie obiektu blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob) Określ inną nazwę obiektu blob i warstwę docelową gorącej lub chłodnej.

 Aby uzyskać więcej informacji na temat warstw, zobacz [Azure Blob storage: hot, cool i archiwum warstwy dostępu.](storage-blob-storage-tiers.md)

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Nawadnianie zarchiwizowanego obiektu blob do warstwy online

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Kopiowanie zarchiwizowanego obiektu blob do warstwy online

Jeśli nie chcesz ponownie nawilżać obiektu blob archiwum, możesz wykonać operację [kopiowania obiektu blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob) Oryginalny obiekt blob pozostanie niezmodyfikowany w archiwum, podczas gdy nowy obiekt blob jest tworzony w warstwie online hot lub cool, nad którymi możesz pracować. W operacji Kopiuj obiekt blob można również ustawić opcjonalną właściwość *x-ms-rehydrate-priority* na Standard lub High, aby określić priorytet, przy którym ma zostać utworzona kopia obiektu blob.

Kopiowanie obiektu blob z archiwum może potrwać wiele godzin, w zależności od wybranego priorytetu ponownego nawodnienia. W tle operacja **Kopiowania obiektu blob** odczytuje źródłowy obiekt blob archiwum, aby utworzyć nowy obiekt blob online w wybranej warstwie docelowej. Nowy obiekt blob może być widoczny podczas listy obiektów blob, ale dane nie są dostępne, dopóki odczyt z źródłowego obiektu blob archiwum jest kompletna, a dane są zapisywane w nowym docelowym obiektu blob online. Nowy obiekt blob jest niezależną kopią i wszelkie modyfikacje lub usunięcie do niego nie wpływa na źródłowego obiektu blob archiwum.

Obiekty blob archiwum można kopiować tylko do warstw docelowych online na tym samym koncie magazynu. Kopiowanie obiektu blob archiwum do innego obiektu blob archiwum nie jest obsługiwane. W poniższej tabeli przedstawiono możliwości CopyBlob.

|                                           | **Źródło warstwy gorąca**   | **Fajne źródło poziomu** | **Źródło warstwy Archiwum**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **Miejsce docelowe warstwy gorąca**                  | Obsługiwane             | Obsługiwane            | Obsługiwane na tym samym koncie; oczekujące nawładnie nawadnianie               |
| **Chłodne miejsce docelowe poziomu**                 | Obsługiwane             | Obsługiwane            | Obsługiwane na tym samym koncie; oczekujące nawładnie nawadnianie               |
| **Miejsce docelowe warstwy archiwum**              | Obsługiwane             | Obsługiwane            | Nieobsługiwane         |

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Ponowne nawadnianie obiektów blob z archiwum do warstw gorących lub chłodnych jest naliczane jako operacje odczytu i pobieranie danych. Za pomocą wysokiego priorytetu ma wyższe koszty operacji i pobierania danych w porównaniu do standardowego priorytetu. Nawodnienie o wysokim priorytecie jest wyświetlane jako osobny element zamówienia na rachunku. Jeśli żądanie o wysokim priorytecie, aby zwrócić archiwum obiektu blob kilku gigabajtów trwa ponad 5 godzin, nie zostanie naliczona opłata za pobieranie o wysokim priorytecie. Jednak standardowe stawki pobierania nadal mają zastosowanie, ponieważ nawodnienie było traktowane priorytetowo w stosunku do innych wniosków.

Kopiowanie obiektów blob z archiwum do warstw gorących lub chłodnych jest naliczane jako operacje odczytu i pobieranie danych. Operacja zapisu jest naliczana za utworzenie nowej kopii obiektu blob. Opłaty za wcześniejsze usunięcie nie mają zastosowania podczas kopiowania do obiektu blob online, ponieważ źródłowy obiekt blob pozostaje niezmieniony w warstwie archiwum. Opłaty za pobieranie o wysokim priorytecie mają zastosowanie, jeśli jest zaznaczona.

Obiekty BLOB w warstwie archiwum powinny być przechowywane przez co najmniej 180 dni. Usuwanie lub ponowne nawadnianie zarchiwizowanych obiektów blob przed 180 dniami spowoduje naliczenie opłat za wcześniejsze usunięcie.

> [!NOTE]
> Aby uzyskać więcej informacji na temat cen bloków obiektów blob i ponownego nawodnienia danych, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/). Aby uzyskać więcej informacji na temat opłat za transfer danych [wychodzących,](https://azure.microsoft.com/pricing/details/data-transfers/)zobacz Szczegóły cen transferów danych .

## <a name="quickstart-scenarios"></a>Scenariusze typu Szybki start

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Nawadnianie obiektu blob archiwum do warstwy online
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W witrynie Azure portal wyszukaj i wybierz pozycję **Wszystkie zasoby**.

1. Wybierz swoje konto magazynu.

1. Wybierz kontener, a następnie wybierz obiekt blob.

1. We **właściwościach obiektu Blob**wybierz pozycję **Zmień warstwę**.

1. Wybierz warstwę dostępu **Gorąca** lub **Chłodna.** 

1. Wybierz priorytet nawadniania **standardu** lub **wysokiego**.

1. Wybierz **pozycję Zapisz** u dołu.

![Zmienianie warstwy](media/storage-tiers/blob-access-tier.png)
![konta magazynu Sprawdź stan ponownego nawadniania](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Następujący skrypt programu PowerShell może służyć do zmiany warstwy obiektów blob obiektu blob archiwum. Zmienna `$rgName` musi zostać zainicjowana z nazwą grupy zasobów. Zmienna `$accountName` musi zostać zainicjowana z nazwą konta magazynu. Zmienna `$containerName` musi zostać zainicjowana z nazwą kontenera. Zmienna `$blobName` musi zostać zainicjowana z nazwą obiektu blob. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", “Standard”)
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Kopiowanie obiektu blob archiwum do nowego obiektu blob z warstwą online
Poniższy skrypt programu PowerShell może służyć do kopiowania obiektu blob archiwum do nowego obiektu blob w ramach tego samego konta magazynu. Zmienna `$rgName` musi zostać zainicjowana z nazwą grupy zasobów. Zmienna `$accountName` musi zostać zainicjowana z nazwą konta magazynu. `$srcContainerName` Zmienne `$destContainerName` i muszą być inicjowane z nazwami kontenerów. `$srcBlobName` Zmienne `$destBlobName` i muszą być inicjowane z nazwami obiektów blob. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o warstwach magazynu obiektów Blob](storage-blob-storage-tiers.md)
* [Sprawdzanie cen gorących, chłodnych i archiwalnych w magazynach obiektów Blob i kontach GPv2 według regionów](https://azure.microsoft.com/pricing/details/storage/)
* [Zarządzanie cyklem życia magazynu usługi Azure Blob Storage](storage-lifecycle-management-concepts.md)
* [Sprawdzanie ceny transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/)
