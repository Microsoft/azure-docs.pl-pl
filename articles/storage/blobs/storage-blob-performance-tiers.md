---
title: Blokuj warstwy wydajności magazynu obiektów BLOB (Azure Storage)
description: W tym artykule omówiono różnice między warstwami wydajności warstwy Premium i standardowa dla magazynu blokowych obiektów blob platformy Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: cf0b5d29e0dc375a07fe024ef0763c8200275055
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880712"
---
# <a name="performance-tiers-for-block-blob-storage"></a>Warstwy wydajności dla magazynu blokowych obiektów blob

W miarę jak przedsiębiorstwa wdrażają aplikacje natywne w chmurze, ważne jest, aby dysponować opcjami oszczędnego magazynowania danych na różnych poziomach wydajności.

Magazyn blokowych obiektów blob platformy Azure oferuje dwie różne warstwy wydajności:

- **Premium**: zoptymalizowane pod kątem wysokich stawek transakcji i jednocyfrowe opóźnienie magazynu
- **Standard**: zoptymalizowane pod kątem wysokiej wydajności i wysokiej przepływności

Poniższe uwagi dotyczą różnych warstw wydajności:

| Warstwowy |Wydajność standardowa  |Wydajność warstwy Premium  |
|---------|---------|---------|
|Dostępność w danym regionie     |   Wszystkie regiony      | W obszarze [Wybieranie regionów](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|Obsługiwane [typy kont magazynu](../common/storage-account-overview.md#types-of-storage-accounts)     |     Ogólnego przeznaczenia w wersji 2, BlobStorage, ogólnego przeznaczenia w wersji 1    |    BlockBlobStorage     |
|Obsługuje [blokowe obiekty blob o wysokiej przepływności](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    Tak, co więcej niż 4 PutBlock MiB lub rozmiary PutBlob     |    Tak, o rozmiarze większym niż 256 KiB PutBlock lub PutBlob    |
|Nadmiarowość     |     Zobacz [typy kont magazynu](../common/storage-account-overview.md#types-of-storage-accounts)   |  Obecnie obsługuje tylko Magazyn lokalnie nadmiarowy (LRS) i magazyn stref-redudant (ZRS)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup> Magazyn strefowo nadmiarowy (ZRS) jest dostępny w wybranych regionach dla kont magazynu blokowych obiektów BLOB wydajności w warstwie Premium.</div>

W odniesieniu do kosztów wydajność Premium zapewnia zoptymalizowane ceny dla aplikacji z wysoką stawką transakcji, aby [zmniejszyć łączny koszt magazynu](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) dla tych obciążeń.

## <a name="premium-performance"></a>Wydajność warstwy Premium

Blok wydajności w warstwie Premium magazynu obiektów BLOB udostępnia dane za pośrednictwem sprzętu o wysokiej wydajności. Dane są przechowywane na dyskach półprzewodnikowych (dysków SSD), które są zoptymalizowane pod kątem małych opóźnień. Dysków SSD zapewniają wyższą przepływność w porównaniu z tradycyjnymi dyskami twardymi.

Magazyn wydajności w warstwie Premium jest idealnym rozwiązaniem w przypadku obciążeń wymagających szybkiego i spójnego czasu odpowiedzi. Jest to najlepsze rozwiązanie w przypadku obciążeń, które wykonują wiele małych transakcji. Przykładowe obciążenia obejmują:

- **Interaktywne obciążenia**. Te obciążenia wymagają natychmiastowej aktualizacji i opinii użytkowników, takich jak handel elektroniczny i aplikacje do mapowania. Na przykład w aplikacji handlu elektronicznego mniej często oglądane elementy mogą nie być buforowane. Jednak muszą być natychmiast wyświetlane na żądanie klienta.

- **Analiza**. W scenariuszu IoT wiele mniejszych operacji zapisu może być wypychanych do chmury co sekundę. Duże ilości danych mogą być pobierane, zagregowane na potrzeby analizy, a następnie usuwane prawie natychmiast. Możliwości dużego pozyskiwania magazynu blokowych obiektów BLOB w warstwie Premium sprawiają, że jest to wydajne dla tego typu obciążenia.

- **Sztuczna inteligencja/Uczenie maszynowe (AI/ml)**. AI/ML zajmuje się zużyciem i przetwarzaniem różnych typów danych, na przykład wizualizacji, mowy i tekstu. Ten typ obciążeń obliczeniowych o wysokiej wydajności zajmuje się dużymi ilościami danych, które wymagają szybkiej reakcji i wydajnego czasu pozyskiwania danych.

- **Przekształcanie danych**. Procesy wymagające stałej edycji, modyfikacji i konwersji danych wymagają natychmiastowego aktualizowania. Dla dokładnej reprezentacji danych konsumenci tych danych muszą zobaczyć te zmiany bezpośrednio.

## <a name="standard-performance"></a>Wydajność standardowa

Standardowa wydajność obsługuje różne [warstwy dostępu](storage-blob-storage-tiers.md) do przechowywania danych w najbardziej opłacalny sposób. Jest ona zoptymalizowana pod kątem wysokiej wydajności i wysokiej przepływności w przypadku dużych zestawów danych.

- **Kopie zapasowe i zestawy danych odzyskiwania po awarii**. Standardowa wydajność magazynu oferuje oszczędne warstwy, dzięki czemu jest idealnym przypadkiem użycia zarówno dla krótkoterminowych, jak i długoterminowych zestawów danych odzyskiwania, kopii zapasowych i archiwizowania danych zgodności.

- **Zawartość multimedialna**. Obrazy i filmy wideo często są najczęściej używane podczas pierwszego tworzenia i przechowywania, ale ten typ zawartości jest używany rzadziej, jak w przypadku starszej wersji. Standardowa wydajność magazynu oferuje odpowiednie warstwy dla potrzeb zawartości multimedialnej. 

- **Przetwarzanie zbiorcze danych**. Te rodzaje obciążeń są odpowiednie dla magazynu w warstwie Standardowa, ponieważ wymagają one ekonomicznego magazynu o wysokiej przepływności, a nie z niewielkim opóźnieniem. Duże, surowe zestawy danych są przygotowywane do przetwarzania i ostatecznie migrowane do warstw chłodnicy.

## <a name="migrate-from-standard-to-premium"></a>Migrowanie z wersji Standard do Premium

Nie można przekonwertować istniejącego konta magazynu wydajności standardowej na konto magazynu blokowych obiektów BLOB o wydajności Premium. Aby przeprowadzić migrację do konta magazynu wydajności w warstwie Premium, należy utworzyć konto BlockBlobStorage i przeprowadzić migrację danych do nowego konta. Aby uzyskać więcej informacji, zobacz [Tworzenie konta BlockBlobStorage](storage-blob-create-account-block-blob.md).

Aby skopiować obiekty blob między kontami magazynu, można użyć najnowszej wersji narzędzia wiersza polecenia [AzCopy](../common/storage-use-azcopy-v10.md#transfer-data) . Inne narzędzia, takie jak Azure Data Factory, są również dostępne do przenoszenia i przekształcania danych.

## <a name="blob-lifecycle-management"></a>Zarządzanie cyklem życia obiektów BLOB

Zarządzanie cyklem życia magazynu obiektów BLOB zawiera rozbudowane zasady oparte na regułach:

- **Premium**: wygasa dane po zakończeniu cyklu życia.
- **Standard**: przechodzenie danych do najlepszej warstwy dostępu i wygasają dane na koniec cyklu życia.

Aby dowiedzieć się więcej, zobacz [Zarządzanie cyklem życia usługi Azure Blob Storage](storage-lifecycle-management-concepts.md).

Nie można przenieść danych przechowywanych na koncie magazynu blokowych obiektów BLOB w warstwie Premium między warstwami gorąca, chłodna i archiwalna. Można jednak kopiować obiekty blob z konta blokowego usługi BLOB Storage do warstwy dostępu gorąca na *innym* koncie. Aby skopiować dane na inne konto, użyj [bloku Put z adresu URL](/rest/api/storageservices/put-block-from-url) API lub [AzCopy v10](../common/storage-use-azcopy-v10.md). Interfejs **Put bloku z adresu URL** synchronicznie kopiuje dane na serwerze. Wywołanie kończy się tylko po przeniesieniu wszystkich danych z oryginalnej lokalizacji serwera do lokalizacji docelowej.

## <a name="next-steps"></a>Następne kroki

Oceń gorące, chłodne i archiwalne na kontach GPv2 i BLOB Storage.

- [Dowiedz się więcej na temat ponownego wypełniania danych obiektów BLOB w warstwie Archiwum](storage-blob-rehydration.md)
- [Ocena użycia bieżących kont magazynu przez włączenie metryk usługi Azure Storage](./monitor-blob-storage.md)
- [Sprawdzanie cen gorąca, chłodna i archiwalna w usłudze BLOB Storage i kontach GPv2 według regionów](https://azure.microsoft.com/pricing/details/storage/)
- [Sprawdzanie ceny transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/)
