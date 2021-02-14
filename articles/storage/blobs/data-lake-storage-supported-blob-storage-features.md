---
title: Funkcje magazynu obiektów BLOB dostępne w Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informacje o funkcjach magazynu obiektów blob, których można używać z Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: normesta
ms.openlocfilehash: 769163dd09ec29c2c206d0273e71de27732fb9db
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516282"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Funkcje magazynu obiektów BLOB dostępne w Azure Data Lake Storage Gen2

Blob Storage funkcje, takie jak [rejestrowanie diagnostyczne](../common/storage-analytics-logging.md), [warstwy dostępu](storage-blob-storage-tiers.md)i [zasady zarządzania cyklem BLOB Storage](storage-lifecycle-management-concepts.md) , teraz działają z kontami, które mają hierarchiczną przestrzeń nazw. W związku z tym można włączyć hierarchiczne przestrzenie nazw na kontach magazynu obiektów Blob bez utraty dostępu do tych funkcji.

Ta tabela zawiera listę funkcji magazynu obiektów blob, których można używać z Azure Data Lake Storage Gen2. Elementy, które pojawiają się w tych tabelach, zmienią się w miarę upływu czasu, gdy obsługa będzie nadal rozszerzana. Aby dowiedzieć się więcej o określonych problemach związanych ze stanem wersji zapoznawczej funkcji, zobacz artykuł dotyczący [znanych problemów](data-lake-storage-known-issues.md) .

## <a name="supported-blob-storage-features"></a>Obsługiwane funkcje usługi Blob Storage

W poniższej tabeli przedstawiono, w jaki sposób każda funkcja magazynu obiektów BLOB jest obsługiwana w Data Lake Storage Gen2. Istnieje kolumna dla warstw wydajności warstwy Standardowa i [Premium](premium-tier-for-data-lake-storage.md) . 

|Funkcja Blob Storage |Standardowa (Standard) |Premium |Pokrewne artykuły: |
|---------------|-------------------|---|
|Warstwa dostępu Gorąca|Ogólnie dostępne|Nieobsługiwane|[Usługa Azure Blob Storage: warstwy dostępu Gorąca, Chłodna i Archiwum](storage-blob-storage-tiers.md)|
|Warstwa dostępu Chłodna|Ogólnie dostępne|Nieobsługiwane|[Usługa Azure Blob Storage: warstwy dostępu Gorąca, Chłodna i Archiwum](storage-blob-storage-tiers.md)|
|Zdarzenia|Ogólnie dostępne|Ogólnie dostępne|[Reagowanie na zdarzenia usługi Blob Storage](storage-blob-event-overview.md)|
|Metryki (klasyczne)|Ogólnie dostępne|Ogólnie dostępne|[Metryki usługi Azure Storage Analytics (klasyczne)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Metryki w usłudze Azure Monitor|Ogólnie dostępne|Wersja zapoznawcza|[Azure Storage metrics in Azure Monitor (Metryki usługi Azure Storage w usłudze Azure Monitor)](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Polecenia programu PowerShell dotyczące magazynu obiektów BLOB|Ogólnie dostępne|Ogólnie dostępne|[Szybki Start: przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą programu PowerShell](storage-quickstart-blobs-powershell.md)|
|Polecenie interfejsu wiersza polecenia platformy Azure dla usługi BLOB Storage|Ogólnie dostępne|Ogólnie dostępne|[Szybki Start: Tworzenie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą interfejsu wiersza polecenia platformy Azure](storage-quickstart-blobs-cli.md)|
|Interfejsy API magazynu obiektów BLOB|Ogólnie dostępne|Ogólnie dostępne|[Szybki start: Biblioteka kliencka usługi Azure Blob Storage V12 dla platformy .NET](storage-quickstart-blobs-dotnet.md)<br>[Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu SDK V12 języka Java](storage-quickstart-blobs-java.md)<br>[Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu SDK V12 języka Python](storage-quickstart-blobs-python.md)<br>[Szybki Start: Zarządzanie obiektami BLOB za pomocą zestawu SDK języka JavaScript V12 w Node.js](storage-quickstart-blobs-nodejs.md)|
|Dzienniki diagnostyczne|Ogólnie dostępne|Wersja zapoznawcza |[Rejestrowanie usługi Azure Storage Analytics](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Archiwizowanie warstwy dostępu|Ogólnie dostępne|Nieobsługiwane|[Usługa Azure Blob Storage: warstwy dostępu Gorąca, Chłodna i Archiwum](storage-blob-storage-tiers.md)|
|Zasady zarządzania cyklem życia (Obsługa warstw)|Ogólnie dostępne|Jeszcze nieobsługiwane|[Zarządzanie cyklem życia magazynu usługi Azure Blob Storage](storage-lifecycle-management-concepts.md)|
|Zasady zarządzania cyklem życia (usuwanie obiektu BLOB)|Ogólnie dostępne|Ogólnie dostępne|[Zarządzanie cyklem życia magazynu usługi Azure Blob Storage](storage-lifecycle-management-concepts.md)|
|Logowanie Azure Monitor|Wersja zapoznawcza |Wersja zapoznawcza|[Monitorowanie usługi Azure Storage](./monitor-blob-storage.md)|
|Migawki|Wersja zapoznawcza<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Wersja zapoznawcza<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Migawki obiektów BLOB](snapshots-overview.md)|
|Statyczne witryny internetowe|Wersja zapoznawcza<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Wersja zapoznawcza<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Hostowanie statycznej witryny internetowej w usłudze Azure Storage](storage-blob-static-website.md)|
|Niezmienny magazyn|Wersja zapoznawcza<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Wersja zapoznawcza<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Przechowywanie danych obiektów blob kluczowych dla działalności za pomocą magazynu niezmiennego](storage-blob-immutable-storage.md)|
|Usuwanie nietrwałe kontenera|Wersja zapoznawcza|Wersja zapoznawcza|[Usuwanie nietrwałe dla kontenerów (wersja zapoznawcza)](soft-delete-container-overview.md)|
|Spis usługi Azure Storage|Wersja zapoznawcza|Wersja zapoznawcza|[Używanie spisu usługi Azure Storage do zarządzania danymi obiektów BLOB (wersja zapoznawcza)](blob-inventory.md)|
|Niestandardowe domeny|Wersja zapoznawcza<div role="complementary" aria-labelledby="preview-form-2"><sup>2</sup></div>|Wersja zapoznawcza<div role="complementary" aria-labelledby="preview-form-2"><sup>2</sup></div>|[Mapowanie domeny niestandardowej na punkt końcowy usługi Azure Blob Storage](storage-custom-domain-name.md)|
|Usuwanie nietrwałe obiektów BLOB|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Usuwanie nietrwałe dla obiektów blob](./soft-delete-blob-overview.md)|
|Blobfuse|Ogólnie dostępne|Ogólnie dostępne|[Jak zainstalować magazyn obiektów BLOB jako system plików za pomocą blobfuse](storage-how-to-mount-container-linux.md)|
|Anonimowy dostęp publiczny |Ogólnie dostępne|Ogólnie dostępne| Zobacz [Konfigurowanie anonimowego publicznego dostępu do odczytu dla kontenerów i obiektów BLOB](anonymous-read-access-configure.md).|
|Tryb failover konta zarządzanego przez klienta|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Odzyskiwanie po awarii i tryb failover konta](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Klucze dostarczone przez klienta|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Podaj klucz szyfrowania dla żądania do magazynu obiektów BLOB](encryption-customer-provided-keys.md)|
|Zakresy szyfrowania|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Tworzenie zakresów szyfrowania i zarządzanie nimi (wersja zapoznawcza)](encryption-scope-manage.md)|
|Źródło zmian|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Obsługa kanału informacyjnego zmiany w usłudze Azure Blob Storage](storage-blob-change-feed.md)|
|Replikacja obiektów|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Konfiguruj replikację obiektów dla blokowych obiektów BLOB](object-replication-configure.md)|
|Przechowywanie wersji obiektów BLOB|Jeszcze nieobsługiwane|Jeszcze nieobsługiwane|[Włączanie obsługi wersji obiektów blob i zarządzanie nimi](versioning-enable.md)|

<div id="preview-form"><sup>1</sup> Aby korzystać z migawek, niezmiennego magazynu lub statycznych witryn internetowych z Data Lake Storage Gen2, musisz zarejestrować się w wersji zapoznawczej, wypełniając ten <a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>formularz</a>.  </div>
<div id="preview-form-2"><sup>2</sup> Niestandardowa nazwa domeny może być mapowana tylko na punkt końcowy usługi BLOB Service lub statycznej witryny internetowej. Punkt końcowy magazynu Data Lake nie jest obsługiwany. </a> .  </div>

## <a name="see-also"></a>Zobacz też

- [Znane problemy z Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Usługi platformy Azure, które obsługują Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Platformy Open source obsługujące Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Dostęp z wieloprotokołem do Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)