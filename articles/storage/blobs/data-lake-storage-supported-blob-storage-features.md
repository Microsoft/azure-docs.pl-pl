---
title: Funkcje magazynu obiektów BLOB dostępne w Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informacje o funkcjach magazynu obiektów blob, których można używać z Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: d25a44aa70b9638017856595c7431e897d035c33
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576119"
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
|Metryki w usłudze Azure Monitor|Ogólnie dostępne|Wersja zapoznawcza|[Azure Storage metrics in Azure Monitor (Metryki usługi Azure Storage w usłudze Azure Monitor)](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Polecenia programu PowerShell dotyczące magazynu obiektów BLOB|Ogólnie dostępne|Wersja zapoznawcza|[Szybki Start: przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą programu PowerShell](storage-quickstart-blobs-powershell.md)|
|Polecenie interfejsu wiersza polecenia platformy Azure dla usługi BLOB Storage|Ogólnie dostępne|Wersja zapoznawcza|[Szybki Start: Tworzenie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą interfejsu wiersza polecenia platformy Azure](storage-quickstart-blobs-cli.md)|
|Interfejsy API magazynu obiektów BLOB|Ogólnie dostępne|Wersja zapoznawcza|[Szybki start: Biblioteka kliencka usługi Azure Blob Storage V12 dla platformy .NET](storage-quickstart-blobs-dotnet.md)<br>[Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu SDK V12 języka Java](storage-quickstart-blobs-java.md)<br>[Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu SDK V12 języka Python](storage-quickstart-blobs-python.md)<br>[Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu SDK języka JavaScript V12 w Node.js](storage-quickstart-blobs-nodejs.md)|
|Dzienniki diagnostyczne|Ogólnie dostępne|Wersja zapoznawcza <div role="complementary" aria-labelledby="diagnostic-logging"><sup>1</sup></div> |[Rejestrowanie analityki magazynu platformy Azure](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Archiwizowanie warstwy dostępu|Ogólnie dostępne|Nieobsługiwane|[Usługa Azure Blob Storage: warstwy dostępu Gorąca, Chłodna i Archiwum](storage-blob-storage-tiers.md)|
|Zasady zarządzania cyklem życia|Ogólnie dostępne|Jeszcze nieobsługiwane|[Zarządzanie cyklem życia magazynu usługi Azure Blob Storage](storage-lifecycle-management-concepts.md)|
|Logowanie Azure Monitor|Wersja zapoznawcza |Jeszcze nieobsługiwane|[Monitorowanie usługi Azure Storage](../common/monitor-storage.md)|
|Migawki|Wersja zapoznawcza|Jeszcze nieobsługiwane|[Migawki obiektów BLOB](snapshots-overview.md)|
|Statyczne witryny internetowe|Wersja zapoznawcza|Jeszcze nieobsługiwane|[Hostowanie statycznej witryny internetowej w usłudze Azure Storage](storage-blob-static-website.md)|
|Niezmienny magazyn|Wersja zapoznawcza|Jeszcze nieobsługiwane|[Przechowywanie kluczowych dla działalności danych obiektów blob z niezmiennym magazynem](storage-blob-immutable-storage.md)|
|Usuwanie nietrwałe kontenera|Wersja zapoznawcza|Wersja zapoznawcza|[Usuwanie nietrwałe dla kontenerów (wersja zapoznawcza)](soft-delete-container-overview.md)|
|Usuwanie nietrwałe obiektów BLOB|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Usuwanie nietrwałe dla obiektów blob](storage-blob-soft-delete.md)|
|Blobfuse|Wersja zapoznawcza|Jeszcze nieobsługiwane|[Jak zainstalować magazyn obiektów BLOB jako system plików za pomocą blobfuse](storage-how-to-mount-container-linux.md)|
|Tryb failover konta|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Odzyskiwanie po awarii i tryb failover konta](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Lista ACL kontenera obiektów BLOB|Jeszcze nieobsługiwane<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|Jeszcze nieobsługiwane<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|[Ustawianie listy ACL kontenerów](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Klucze zarządzane przez klienta|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Klucze zarządzane przez klienta dla szyfrowania usługi Azure Storage](../common/customer-managed-keys-overview.md)|
|Niestandardowe domeny|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Mapowanie domeny niestandardowej na punkt końcowy usługi Azure Blob Storage](storage-custom-domain-name.md)|
|Zakresy szyfrowania|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Tworzenie zakresów szyfrowania i zarządzanie nimi (wersja zapoznawcza)](encryption-scope-manage.md)|
|Źródło zmian|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Obsługa kanału informacyjnego zmiany w usłudze Azure Blob Storage](storage-blob-change-feed.md)|
|Replikacja obiektów|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Konfiguruj replikację obiektów dla blokowych obiektów BLOB (wersja zapoznawcza)](object-replication-configure.md)|
|Przechowywanie wersji obiektów BLOB|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Włączanie obsługi wersji obiektów blob i zarządzanie nimi (wersja zapoznawcza)](versioning-enable.md)|

<div id="diagnostic-logging"><sup>1</sup> w przypadku kont magazynu blokowych obiektów BLOB w warstwie Premium nie można włączyć obsługi dzienników diagnostycznych (klasycznych) przy użyciu Azure Portal. Włącz je za pomocą programu PowerShell.</div><br>

<div id="blob-container-ACL"><sup>2</sup> listy ACL można ustawić w folderze głównym kontenera, ale nie w samym kontenerze.</div><br>

<div id="preview-form"><sup>3</sup> Aby korzystać z migawek, niezmiennego magazynu lub statycznych witryn internetowych z Data Lake Storage Gen2, musisz zarejestrować się w wersji zapoznawczej, wypełniając ten <a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>formularz</a>.  </div>

## <a name="see-also"></a>Zobacz też

- [Znane problemy z Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Usługi platformy Azure, które obsługują Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Platformy Open source obsługujące Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Dostęp z wieloprotokołem do Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)
