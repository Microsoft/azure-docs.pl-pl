---
title: Funkcje magazynu obiektów BLOB dostępne w Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informacje o funkcjach magazynu obiektów blob, których można używać z Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 05/10/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 5be7c9800076cfaf361a3e752162e14d10f22655
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650745"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Funkcje magazynu obiektów BLOB dostępne w Azure Data Lake Storage Gen2

Blob Storage funkcje, takie jak [rejestrowanie diagnostyczne](../common/storage-analytics-logging.md), [warstwy dostępu](storage-blob-storage-tiers.md)i [zasady zarządzania cyklem BLOB Storage](storage-lifecycle-management-concepts.md) , teraz działają z kontami, które mają hierarchiczną przestrzeń nazw. W związku z tym można włączyć hierarchiczne przestrzenie nazw na kontach magazynu obiektów Blob bez utraty dostępu do tych funkcji.

Ta tabela zawiera listę funkcji magazynu obiektów blob, których można używać z Azure Data Lake Storage Gen2. Elementy, które pojawiają się w tych tabelach, zmienią się w miarę upływu czasu, gdy obsługa będzie nadal rozszerzana. Aby dowiedzieć się więcej o określonych problemach związanych ze stanem wersji zapoznawczej funkcji, zobacz artykuł dotyczący [znanych problemów](data-lake-storage-known-issues.md) .

## <a name="supported-blob-storage-features"></a>Obsługiwane funkcje usługi Blob Storage

> [!NOTE]
> Poziom pomocy technicznej odnosi się tylko do tego, w jaki sposób funkcja jest obsługiwana w Data Lake Storage Gen2. 
>
> [Konta usługi BlockBlobStorage z wydajnością Premium](storage-blob-create-account-block-blob.md) dla Data Lake Storage Gen2 są obecnie dostępne w publicznej wersji zapoznawczej. Poziomy pomocy technicznej dla tych typów kont są wyświetlane w kolumnie **BlockBlobStorage (Premium)** .

|Funkcja Blob Storage |Ogólnego przeznaczenia w wersji 2 |BlockBlobStorage (wersja Premium) |Pokrewne artykuły: |
|---------------|-------------------|---|
|Warstwa dostępu Gorąca|Ogólnie dostępne|Nieobsługiwane|[Usługa Azure Blob Storage: warstwy dostępu Gorąca, Chłodna i Archiwum](storage-blob-storage-tiers.md)|
|Warstwa dostępu Chłodna|Ogólnie dostępne|Nieobsługiwane|[Usługa Azure Blob Storage: warstwy dostępu Gorąca, Chłodna i Archiwum](storage-blob-storage-tiers.md)|
|Zdarzenia|Ogólnie dostępne|Wersja zapoznawcza|[Reagowanie na zdarzenia usługi Blob Storage](storage-blob-event-overview.md)|
|Metryki (klasyczne)|Ogólnie dostępne|Nieobsługiwane|[Metryki usługi Azure Storage Analytics (klasyczne)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Metryki w usłudze Azure Monitor|Ogólnie dostępne|Wersja zapoznawcza|[Metryki usługi Azure Storage w usłudze Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Polecenia programu PowerShell dotyczące magazynu obiektów BLOB|Ogólnie dostępne|Wersja zapoznawcza|[Szybki Start: przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą programu PowerShell](storage-quickstart-blobs-powershell.md)|
|Polecenie interfejsu wiersza polecenia platformy Azure dla usługi BLOB Storage|Ogólnie dostępne|Wersja zapoznawcza|[Szybki Start: Tworzenie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą interfejsu wiersza polecenia platformy Azure](storage-quickstart-blobs-cli.md)|
|Interfejsy API magazynu obiektów BLOB|Ogólnie dostępne|Wersja zapoznawcza|[Szybki Start: Biblioteka kliencka usługi Azure Blob Storage V12 dla platformy .NET](storage-quickstart-blobs-dotnet.md)<br>[Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu SDK V12 języka Java](storage-quickstart-blobs-java.md)<br>[Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu SDK V12 języka Python](storage-quickstart-blobs-python.md)<br>[Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu SDK V12 języka JavaScript w programie Node. js](storage-quickstart-blobs-nodejs.md)|
|Dzienniki diagnostyczne|Ogólnie dostępne|Wersja zapoznawcza <div role="complementary" aria-labelledby="diagnostic-logging"><sup>1</sup></div> |[Rejestrowanie usługi Azure Storage Analytics](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Logowanie Azure Monitor|Wersja zapoznawcza |Jeszcze nieobsługiwane|[Monitorowanie usługi Azure Storage](../common/monitor-storage.md)|
|Archiwizowanie warstwy dostępu|Wersja zapoznawcza|Nieobsługiwane|[Usługa Azure Blob Storage: warstwy dostępu Gorąca, Chłodna i Archiwum](storage-blob-storage-tiers.md)|
|Zasady zarządzania cyklem życia|Wersja zapoznawcza|Jeszcze nieobsługiwane|[Zarządzanie cyklem życia magazynu usługi Azure Blob Storage](storage-lifecycle-management-concepts.md)|
|Źródło zmian|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Obsługa kanału informacyjnego zmiany w usłudze Azure Blob Storage](storage-blob-change-feed.md)|
|Tryb failover konta|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Odzyskiwanie po awarii i tryb failover konta](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Lista ACL kontenera obiektów BLOB|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Ustawianie listy ACL kontenerów](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Domeny niestandardowe|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Mapowanie domeny niestandardowej na punkt końcowy usługi Azure Blob Storage](storage-custom-domain-name.md)|
|Niezmienny magazyn|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Przechowywanie kluczowych dla działalności danych obiektów blob z niezmiennym magazynem](storage-blob-immutable-storage.md)|
|Migawki|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Tworzenie migawki obiektu BLOB w programie .NET i zarządzanie nią](storage-blob-snapshots.md)|
|Usuwanie nietrwałe|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Soft delete for Azure Storage blobs](storage-blob-soft-delete.md) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)|
|Statyczne witryny internetowe|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Hostowanie statycznej witryny internetowej w usłudze Azure Storage](storage-blob-static-website.md)|

<div id="diagnostic-logging"><sup>1</sup> W przypadku blokowych bloków BLOB dla kont magazynu nie można włączyć dzienników diagnostycznych (klasycznych) przy użyciu Azure Portal. Włącz je za pomocą programu PowerShell.</div>

## <a name="see-also"></a>Zobacz także

- [Znane problemy z Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Usługi platformy Azure, które obsługują Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Platformy Open source obsługujące Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Dostęp z wieloprotokołem do Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)