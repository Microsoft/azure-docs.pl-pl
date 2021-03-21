---
title: Przeodwodnione dane obiektów blob z warstwy Archiwum
description: Zapisz obiekty blob z magazynu archiwum, aby uzyskać dostęp do danych obiektów BLOB. Skopiuj zarchiwizowany obiekt BLOB do warstwy online.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 2f0ddca9cbd7d85909b1d86e68b92fa1d847476d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103225085"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Przeodwodnione dane obiektów blob z warstwy Archiwum

Gdy obiekt BLOB znajduje się w warstwie dostępu archiwizowania, jest traktowany jako przełączony do trybu offline i nie można go odczytać ani modyfikować. Metadane obiektu BLOB pozostają w trybie online i są dostępne, umożliwiając wyświetlenie listy obiektów blob i jego właściwości. Odczytywanie i modyfikowanie danych obiektów BLOB jest możliwe tylko w przypadku warstw online, takich jak gorąca lub chłodna. Dostępne są dwie opcje pobierania i uzyskiwania dostępu do danych przechowywanych w warstwie dostępu archiwizowania.

1. [Ponownie Zapisz zarchiwizowany obiekt BLOB w warstwie online](#rehydrate-an-archived-blob-to-an-online-tier) — przeciąganie obiektu BLOB archiwalnego na gorącą lub chłodną przez zmianę jego warstwy przy użyciu operacji [ustawiania warstwy obiektów BLOB](/rest/api/storageservices/set-blob-tier) .
2. [Kopiuj zarchiwizowany obiekt BLOB do warstwy online](#copy-an-archived-blob-to-an-online-tier) — Utwórz nową kopię obiektu BLOB archiwum przy użyciu operacji [copy BLOB](/rest/api/storageservices/copy-blob) . Określ inną nazwę obiektu BLOB i warstwę docelową gorącą lub chłodną.

 Aby uzyskać więcej informacji o warstwach, zobacz [Azure Blob Storage: warstwy dostępu gorąca, chłodna i archiwalna](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Przeciąganie zarchiwizowanego obiektu BLOB do warstwy online

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

### <a name="lifecycle-management"></a>Zarządzanie cyklem życia

Ponownego wypełniania obiekt BLOB nie zmienia jego `Last-Modified` czasu. Za pomocą funkcji [zarządzania cyklem życia](storage-lifecycle-management-concepts.md) można utworzyć scenariusz, w którym obiekt BLOB jest ponownie usuwany, a następnie zasady zarządzania cyklem życia przenosiją obiekt BLOB z powrotem do archiwum, ponieważ `Last-Modified` czas przekracza próg ustawiony dla zasad. Aby uniknąć tego scenariusza, użyj metody *[copy a archiwalny obiekt BLOB do warstwy online](#copy-an-archived-blob-to-an-online-tier)* . Metoda Copy Tworzy nowe wystąpienie obiektu BLOB ze zaktualizowanym `Last-Modified` czasem i nie wyzwala zasad zarządzania cyklem życia.

## <a name="monitor-rehydration-progress"></a>Postęp ponownego uzupełniania monitorów

Podczas ponownego wypełniania Użyj operacji pobierania właściwości obiektu BLOB, aby sprawdzić atrybut **stanu archiwum** i potwierdzić, kiedy zmiana warstwy zostanie zakończona. Właściwość ta ma wartość „rehydrate-pending-to-hot” (ponowne wypełnianie w celu przejścia do warstwy gorącej) lub „rehydrate-pending-to-cool” (ponowne wypełnianie w celu przejścia do warstwy chłodnej) w zależności od warstwy docelowej. Po zakończeniu tego procesu właściwość obiektu blob „stan archiwum” jest usuwana, a wartość właściwości **Warstwa dostępu** odpowiada nowej warstwie Gorąca lub Chłodna.

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Kopiowanie zarchiwizowanego obiektu blob do warstwy online

Jeśli nie chcesz ponownie odwodnionić obiektu BLOB archiwum, możesz wykonać operację [kopiowania obiektu BLOB](/rest/api/storageservices/copy-blob) . Oryginalny obiekt BLOB pozostanie niezmodyfikowany w archiwum, podczas gdy nowy obiekt BLOB zostanie utworzony w warstwie gorąca lub chłodna w trybie online, na którym będziesz pracować. W operacji **kopiowania obiektu BLOB** można również ustawić opcjonalną Właściwość *x-MS---* ------------------Priority na wartość standardowa lub wysoka, aby określić priorytet tworzenia kopii obiektu BLOB.

Kopiowanie obiektu BLOB z archiwum może zająć kilka godzin, w zależności od wybranego priorytetu rehydratacji. W tle operacja **kopiowania obiektu BLOB** odczytuje źródłowy obiekt BLOB archiwum, aby utworzyć nowy obiekt BLOB w trybie online w wybranej warstwie docelowej. Nowy obiekt BLOB może być widoczny podczas wyświetlania listy obiektów blob, ale dane nie są dostępne do momentu zakończenia odczytu ze źródłowego obiektu BLOB archiwum, a dane są zapisywane w nowym docelowym obiekcie blob online. Nowy obiekt BLOB jest niezależną kopią, a jakakolwiek modyfikacja lub usunięcie nie ma wpływu na źródłowy obiekt BLOB archiwum.

> [!IMPORTANT]
> Nie usuwaj źródłowego obiektu BLOB, dopóki kopia nie zostanie pomyślnie ukończona w miejscu docelowym. Jeśli źródłowy obiekt BLOB zostanie usunięty, docelowy obiekt BLOB nie może zakończyć kopiowania i będzie pusty. Aby określić stan operacji kopiowania, można sprawdzić *stan x-MS-Copy-status* .

Obiekty blob archiwalne mogą być kopiowane tylko do warstwy docelowej online w ramach tego samego konta magazynu. Kopiowanie obiektu BLOB archiwum do innego obiektu BLOB archiwum nie jest obsługiwane. W poniższej tabeli przedstawiono możliwości operacji **kopiowania obiektu BLOB** .

|                                           | **Źródło warstwy gorącej**   | **Źródło warstwy chłodnej** | **Źródło warstwy Archiwum**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **Miejsce docelowe warstwy gorąca**                  | Obsługiwane             | Obsługiwane            | Obsługiwane w ramach tego samego konta; oczekiwanie na odwodne               |
| **Miejsce docelowe warstwy chłodnej**                 | Obsługiwane             | Obsługiwane            | Obsługiwane w ramach tego samego konta; oczekiwanie na odwodne               |
| **Miejsce docelowe warstwy Archiwum**              | Obsługiwane             | Obsługiwane            | Nieobsługiwane         |

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Ponownego wypełniania obiekty blob z archiwum do warstwy gorąca lub chłodna są obciążane jako operacje odczytu i pobierania danych. Użycie wysokiego priorytetu zapewnia wyższe koszty operacji i pobierania danych w porównaniu z priorytetem standardowym. Uzupełnianie o wysokim priorytecie jest wyświetlane jako osobny wiersz na rachunku. Jeśli żądanie o wysokim priorytecie zwracające obiekt BLOB archiwum z kilku gigabajtów trwa ponad 5 godzin, nie zostanie naliczona opłata za stawkę za pobieranie o wysokim priorytecie. Jednak standardowe stawki za pobieranie nadal są stosowane, ponieważ oddanie ma priorytet w innych żądaniach.

Kopiowanie obiektów blob z archiwum do warstwy gorąca lub chłodna podlega opłatom za operacje odczytu i pobieranie danych. Dla operacji zapisu jest naliczana opłata za utworzenie nowej kopii obiektu BLOB. Opłaty za wczesne usunięcie nie mają zastosowania podczas kopiowania do obiektu BLOB w trybie online, ponieważ źródłowy obiekt BLOB nie jest modyfikowany w warstwie archiwum. Opłaty za pobieranie o wysokim priorytecie mają zastosowanie w przypadku wybrania tej wartości.

Obiekty blob w warstwie archiwum powinny być przechowywane przez co najmniej 180 dni. Usunięcie lub ponownego wypełniania zarchiwizowanych obiektów BLOB przed 180 dni spowoduje naliczenie opłat za wczesne usunięcie.

> [!NOTE]
> Aby uzyskać więcej informacji na temat cen blokowych obiektów blob i uzupełniania danych, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/). Aby uzyskać więcej informacji na temat opłat za transfer danych wychodzących, zobacz [szczegóły cennika transferów danych](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart-scenarios"></a>Scenariusze typu Szybki start

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Umożliwia dehydratacji obiektu BLOB archiwalnego do warstwy online
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W Azure Portal Wyszukaj i wybierz pozycję **wszystkie zasoby**.

1. Wybierz swoje konto magazynu.

1. Wybierz kontener, a następnie wybierz obiekt BLOB.

1. We **właściwościach obiektu BLOB** wybierz pozycję **Zmień warstwę**.

1. Wybierz warstwę dostępu **gorąca** lub **chłodna** . 

1. Wybierz priorytet rehydratacji **Standard** lub **High**.

1. Wybierz pozycję **Zapisz** u dołu.

![Zmień ](media/storage-tiers/blob-access-tier.png)
 ![ stan sprawdzania warstwy konta magazynu](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Poniższy skrypt programu PowerShell może służyć do zmiany warstwy obiektów BLOB archiwum obiektów BLOB. `$rgName`Zmienna musi być zainicjowana przy użyciu nazwy grupy zasobów. `$accountName`Zmienna musi zostać zainicjowana przy użyciu nazwy konta magazynu. `$containerName`Zmienna musi być zainicjowana przy użyciu nazwy kontenera. `$blobName`Zmienna musi być zainicjowana przy użyciu nazwy obiektu BLOB. 
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
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", "Standard")
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Kopiuj obiekt BLOB archiwalny do nowego obiektu BLOB za pomocą warstwy online
Poniższy skrypt programu PowerShell może służyć do kopiowania archiwum BLOB do nowego obiektu BLOB w ramach tego samego konta magazynu. `$rgName`Zmienna musi być zainicjowana przy użyciu nazwy grupy zasobów. `$accountName`Zmienna musi zostać zainicjowana przy użyciu nazwy konta magazynu. `$srcContainerName`Zmienne i `$destContainerName` muszą być inicjowane przy użyciu nazw kontenerów. `$srcBlobName`Zmienne i `$destBlobName` muszą być inicjowane przy użyciu nazw obiektów BLOB. 
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

* [Dowiedz się więcej o warstwach Blob Storage](storage-blob-storage-tiers.md)
* [Sprawdzanie cen gorąca, chłodna i archiwalna w usłudze BLOB Storage i kontach GPv2 według regionów](https://azure.microsoft.com/pricing/details/storage/)
* [Zarządzanie cyklem życia magazynu usługi Azure Blob Storage](storage-lifecycle-management-concepts.md)
* [Sprawdzanie ceny transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/)