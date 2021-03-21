---
title: Zagadnienia dotyczące wydajności systemu plików NFS 3,0 w usłudze Azure Blob Storage (wersja zapoznawcza) | Microsoft Docs
description: Optymalizacja wydajności żądań magazynu sieciowego systemu plików (NFS) 3,0 przy użyciu zaleceń opisanych w tym artykule.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 28fb5ffc0ce9c554294eaca9e460694856cb95a0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589875"
---
# <a name="network-file-system-nfs-30-performance-considerations-in-azure-blob-storage-preview"></a>Zagadnienia dotyczące wydajności systemu plików NFS 3,0 w usłudze Azure Blob Storage (wersja zapoznawcza)

Magazyn obiektów BLOB obsługuje teraz Protokół 3,0 systemu plików NFS. Ten artykuł zawiera zalecenia, które pomagają zoptymalizować wydajność żądań magazynu. Aby dowiedzieć się więcej o obsłudze systemu NFS 3,0 na platformie Azure Blob Storage, zobacz temat [Obsługa protokołu sieciowego systemu plików (NFS) 3,0 w usłudze Azure Blob Storage (wersja zapoznawcza)](network-file-system-protocol-support.md).

> [!NOTE]
> Obsługa protokołu NFS 3,0 w usłudze Azure Blob Storage jest w publicznej wersji zapoznawczej. Obsługuje ona konta magazynu GPV2 z wydajnością warstwy Standardowa w następujących regionach: Australia Wschodnia, Korea Środkowa i Południowo-środkowe stany USA. Wersja zapoznawcza obsługuje również blokowy obiekt BLOB z warstwą wydajności Premium we wszystkich regionach publicznych.

## <a name="add-clients-to-increase-throughput"></a>Dodawanie klientów w celu zwiększenia przepływności 

Platforma Azure Blob Storage skaluje się liniowo do momentu osiągnięcia maksymalnego limitu ruchu wychodzącego i przychodzącego konta magazynu. W związku z tym aplikacje mogą osiągać większą przepływność przy użyciu większej liczby klientów.  Aby wyświetlić limity ruchu wychodzącego i transferu danych w ramach konta magazynu, zobacz [cele skalowalności i wydajności dla kont magazynu w warstwie Standardowa](../common/scalability-targets-standard-account.md).

Poniższy wykres pokazuje, jak zwiększa się przepustowość w miarę dodawania większej liczby klientów. Na tym wykresie klient jest maszyną wirtualną, a konto używa standardowej warstwy wydajności. 

> [!div class="mx-imgBorder"]
> ![Wydajność standardowa](./media/network-file-system-protocol-support-performance/standard-performance-tier.png)

Poniższy wykres pokazuje ten sam efekt w przypadku zastosowania do konta, które korzysta z warstwy wydajności Premium.

> [!div class="mx-imgBorder"]
> ![Wydajność warstwy Premium](./media/network-file-system-protocol-support-performance/premium-performance-tier.png)

## <a name="use-premium-performance-tier-for-small-scale-applications"></a>Korzystanie z warstwy wydajności Premium dla aplikacji w małych skali

Nie wszystkie aplikacje można skalować w górę, dodając więcej klientów. Dla tych aplikacji [konto usługi Azure Premium Block BLOB Storage](storage-blob-create-account-block-blob.md) oferuje spójne stawki za małe opóźnienia i duże transakcje. Konto magazynu blokowych obiektów BLOB w warstwie Premium może osiągać maksymalną przepustowość przy mniejszej liczbie wątków i klientów. Na przykład w przypadku pojedynczego klienta konto magazynu blokowych obiektów BLOB w warstwie Premium może osiągnąć **2,3 x** przepustowość w porównaniu z tym samym ustawieniem używanym z kontem magazynu ogólnego przeznaczenia w wersji 2. 

Na każdym z pasków na poniższym wykresie przedstawiono różnicę w osiąganej przepustowości między kontami magazynu wydajności w warstwie Premium i standardowa. W miarę zwiększania się liczby klientów różnica zmniejsza się.  

> [!div class="mx-imgBorder"]
> ![Wydajność względna](./media/network-file-system-protocol-support-performance/relative-performance.png)

## <a name="avoid-frequent-overwrites-on-data"></a>Unikaj częstego zastępowanie danych

Ukończenie operacji zastępowania trwa dłużej niż w przypadku nowej operacji zapisu. Wynika to z faktu, że operacja zastępowania NFS, szczególnie częściowa Edycja pliku w miejscu, jest kombinacją kilku podstawowych operacji obiektów blob: operacji odczytu, modyfikacji i zapisu. W związku z tym aplikacja wymagająca częstego wprowadzania zmian nie jest odpowiednia dla kont usługi BLOB Storage z włączonym systemem plików NFS. 

## <a name="other-best-practice-recommendations"></a>Inne zalecenia dotyczące najlepszych rozwiązań 

- Używaj maszyn wirtualnych z wystarczającą przepustowością sieci.

- Użyj wielu punktów instalacji, gdy Twoje obciążenia zezwalają na to.

- Użyj możliwie największej liczby wątków.

- Używaj dużych rozmiarów bloków.

- Przekształć żądania magazynu z klienta znajdującego się w tym samym regionie co konto magazynu. Może to poprawić opóźnienia sieci.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o obsłudze systemu NFS 3,0 na platformie Azure Blob Storage, zobacz temat [Obsługa protokołu sieciowego systemu plików (NFS) 3,0 w usłudze Azure Blob Storage (wersja zapoznawcza)](network-file-system-protocol-support.md).

- Aby rozpocząć, zobacz [Instalowanie usługi BLOB Storage przy użyciu protokołu NFS (Network File System) 3,0 (wersja zapoznawcza)](network-file-system-protocol-support-how-to.md).
