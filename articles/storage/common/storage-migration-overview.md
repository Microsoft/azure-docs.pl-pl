---
title: Przewodnik migracji usługi Azure Storage
description: Omówienie migracji magazynu zawiera podstawowe wskazówki dotyczące migracji magazynu
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: common
ms.openlocfilehash: f6f00075c7c66679281d776f9472ec4a1a590d76
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231592"
---
# <a name="azure-storage-migration-overview"></a>Omówienie migracji usługi Azure Storage

W tym artykule omówiono migracje magazynu na platformę Azure i przedstawiono wskazówki dotyczące następujących scenariuszy migracji magazynu:

- Migracja danych bez struktury, takich jak pliki i obiekty
- Migracja urządzeń opartych na blokach, takich jak dyski i sieci magazynowania (San)

## <a name="migration-of-unstructured-data"></a>Migracja danych bez struktury

Migracja danych bez struktury obejmuje następujące scenariusze:

- Migracja plików z magazynu dołączanego do sieci (NAS) do jednej z ofert plików platformy Azure:
  - [Azure Files](https://azure.microsoft.com/services/storage/files/)
  - [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)
  - [rozwiązania niezależnych dostawców oprogramowania (ISV)](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview).
- Migracja obiektów z rozwiązań magazynu obiektów do platformy magazynu obiektów Azure:
  - [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)
  - [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

### <a name="migration-phases"></a>Etapy migracji

Pełna migracja składa się z kilku różnych faz: odnajdywania, oceny i migracji.

![Diagram przedstawiający fazy odnajdywania, oceny i migracji](./media/storage-migration-overview/migration-phases.png)

#### <a name="discovery-phase"></a>Faza odnajdywania

W fazie odnajdywania należy określić wszystkie źródła, które muszą zostać zmigrowane, takie jak udziały SMB, eksporty NFS lub przestrzenie nazw obiektów. Tę fazę można wykonać ręcznie lub za pomocą narzędzi automatycznych.

#### <a name="assessment-phase"></a>Faza oceny

Faza oceny ma kluczowe znaczenie w zrozumieniu dostępnych opcji migracji. Aby zmniejszyć ryzyko podczas migracji i uniknąć częstych pułapeków, wykonaj następujące trzy kroki:

| Etapy fazy oceny                     | Opcje                                                                          |
|--------------------------------------------|----------------------------------------------------------------------------------|
| **Wybierz docelową usługę magazynu**            | — Blob Storage platformy Azure i Data Lake Storage<br>-Azure Files<br>-Azure NetApp Files<br>— Rozwiązania niezależnych dostawców oprogramowania |
| **Wybierz metodę migracji**                  | — Online<br>— Offline<br> -Kombinacji obu                                  |
| **Wybieranie najlepszego narzędzia do migracji dla zadania** | — Narzędzia komercyjne (Azure i ISV)<br> -Open Source                             


Istnieje kilka komercyjnych narzędzi, które mogą pomóc w fazie oceny. Zobacz [macierz porównania](../solution-integration/validated-partners/data-management/migration-tools-comparison.md).

##### <a name="choose-a-target-storage-service"></a>Wybierz docelową usługę magazynu

Wybór docelowej usługi magazynu zależy od aplikacji lub użytkowników, którzy uzyskują dostęp do danych. Właściwy wybór zależy od aspektów technicznych i finansowych. Najpierw należy przeprowadzić ocenę techniczną, aby ocenić możliwe cele i określić, które usługi spełniają wymagania. Następnie należy przeprowadzić ocenę finansową, aby określić najlepszy wybór.

Aby ułatwić wybranie docelowej usługi magazynu dla migracji, należy oszacować następujące aspekty każdej usługi:

- Obsługa protokołów
- Charakterystyki wydajności
- Limity docelowej usługi magazynu

Poniższy diagram przedstawia uproszczone drzewo decyzyjne, które ułatwia przeprowadzenie przez zalecaną usługę plików platformy Azure. Jeśli natywne usługi platformy Azure nie spełniają wymagań, będą [dostępne różne rozwiązania niezależnych dostawców oprogramowania (ISV)](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview) .

Po zakończeniu oceny technicznej i wybraniu odpowiedniego celu należy przeprowadzić ocenę kosztów, aby określić najbardziej opłacalną opcję.

![Podstawowe drzewo decyzyjne na temat wybierania prawidłowej usługi plików](./media/storage-migration-overview/files-decision-tree.png)

Aby utrzymać uproszczone drzewo decyzyjne, limity docelowej usługi magazynu nie są uwzględniane na diagramie. Aby dowiedzieć się więcej o bieżących limitach i określić, czy należy zmodyfikować opcje na podstawie ich, zobacz:

- [Limity kont magazynu](/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)
- [Limity Blob Storage](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-blob-storage-limits)
- [Cele dotyczące skalowalności i wydajności usługi Azure Files](/azure/storage/files/storage-files-scale-targets)
- [Limity zasobów Azure NetApp Files](/azure/azure-netapp-files/azure-netapp-files-resource-limits)

Jeśli którykolwiek z limitów przyniesie zablokowany do korzystania z usługi, platforma Azure obsługuje kilku dostawców magazynu oferujących rozwiązania w portalu Azure Marketplace. Aby uzyskać informacje na temat zweryfikowanych partnerów niezależnego dostawcy oprogramowania, które udostępniają usługi plików, zobacz [partnerzy usługi Azure Storage dla magazynu podstawowego i dodatkowego](/azure/storage/solution-integration/validated-partners/primary-secondary-storage/partner-overview).

##### <a name="select-the-migration-method"></a>Wybierz metodę migracji

Istnieją dwie podstawowe metody migracji magazynu.

- W **trybie online**. Metoda online używa sieci do migracji danych. Można korzystać z publicznej sieci Internet lub [usługi Azure ExpressRoute](/azure/expressroute/expressroute-introduction) . Jeśli usługa nie ma publicznego punktu końcowego, należy używać sieci VPN z publicznym Internetem.
- **Stanie.** Metoda offline używa jednego z [Azure Data Box](https://azure.microsoft.com/services/databox/) urządzeń.

Decyzja o użyciu metody online w przeciwieństwie do metody offline zależy od dostępnej przepustowości sieci. Metoda online jest preferowana w przypadkach, gdy istnieje wystarczająca przepustowość sieci do przeprowadzenia migracji w ramach wymaganej osi czasu.

Istnieje możliwość użycia kombinacji obu metod, metody offline dla początkowej migracji zbiorczej i metody online do migracji przyrostowej zmian. Używanie obu metod jednocześnie wymaga wysokiego poziomu koordynacji i nie jest zalecane z tego powodu. Jeśli zdecydujesz się używać obu metod, Izoluj zestawy danych, które są migrowane w trybie online z zestawów danych, które są migrowane do trybu offline.

Aby uzyskać więcej informacji o różnych metodach i wskazówkach dotyczących migracji, zobacz [Wybieranie rozwiązania platformy Azure na potrzeby transferu danych](/azure/storage/common/storage-choose-data-transfer-solution) i [migrowania do udziałów plików platformy Azure](/azure/storage/files/storage-files-migration-overview).

##### <a name="choose-the-best-migration-tool-for-the-job"></a>Wybieranie najlepszego narzędzia do migracji dla zadania

Istnieją różne narzędzia migracji, których można użyć do przeprowadzenia migracji. Niektóre z nich są typu open source, takie jak AzCopy, Robocopy, xcopy i rsync, a inne są komercyjne. Lista dostępnych narzędzi komercyjnych i porównania między nimi jest dostępna w naszej [macierzy porównawczej](../solution-integration/validated-partners/data-management/migration-tools-comparison.md).

Narzędzia Open Source są odpowiednie dla migracji na małą skalę. Aby można było przeprowadzić migrację z serwerów plików systemu Windows do Azure Files, firma Microsoft zaleca rozpoczęcie od Azure Files natywnej możliwości i przy użyciu [Azure File Sync](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-file-sync). W przypadku bardziej złożonych migracji składających się z różnych źródeł, dużej pojemności lub specjalnych wymagań, takich jak ograniczanie lub szczegółowe raportowanie z funkcjami inspekcji, najlepszym wyborem są narzędzia komercyjne. Te narzędzia ułatwiają migrację i znacząco zmniejszają ryzyko. Większość narzędzi komercyjnych może również przeprowadzić odnajdywanie, co zapewnia cenne dane wejściowe dla oceny.

#### <a name="migration-phase"></a>Faza migracji

Faza migracji to ostatni krok migracji, który umożliwia przenoszenie i Migrowanie danych. Zwykle w fazie migracji można wykonać kilka razy, aby ułatwić przełączenie. Faza migracji składa się z następujących kroków:

1. **Migracja początkowa.** Krok migracji początkowej migruje wszystkie dane ze źródła do obiektu docelowego. Ten krok migruje zbiorcze dane, które wymagają migracji.
2. **Ponownej synchronizacji.** Operacja ponownej synchronizacji migruje wszystkie dane, które zostały zmienione po kroku początkowej migracji. Ten krok można powtórzyć kilka razy, jeśli wprowadzono wiele zmian. Celem uruchamiania wielu operacji ponownej synchronizacji jest skrócenie czasu, jaki zajmuje ostatni krok. W przypadku danych nieaktywnych i danych, które nie mają zmian (takich jak dane kopii zapasowej lub archiwum), można pominąć ten krok.
3. **Ostateczne** przełączenie. Ostatni krok przełączania przełącza aktywne użycie danych z lokalizacji źródłowej do obiektu docelowego i powoduje przełączenie źródła.

Czas trwania migracji danych bez struktury zależy od kilku aspektów. Poza wybraną metodą najbardziej krytyczne czynniki są łącznym rozmiarem dystrybucji danych i rozmiaru pliku. Im większy zbiór danych, tym dłuższy czas migracji. Im mniejszy średni rozmiar pliku, tym dłuższy czas migracji. W przypadku dużej liczby małych plików Rozważ zarchiwizowanie ich w większych plikach (takich jak plik. tar lub. zip), w razie potrzeby, aby skrócić łączny czas migracji.

## <a name="migration-of-block-based-devices"></a>Migracja urządzeń opartych na blokach

Migracja urządzeń opartych na blokach odbywa się zazwyczaj w ramach migracji do maszyny wirtualnej lub hosta fizycznego. Jest to typowym pojęciem koncepcji opóźniania decyzji dotyczących magazynu blokowego do momentu migracji. Podejmowanie tych decyzji przed czasem z odpowiednimi kwestiami dotyczącymi wymagań związanych z obciążeniami prowadzi do płynnej migracji do chmury.

Aby poznać obciążenia do migracji i podejścia do wykonania, zapoznaj się z [dokumentacją Azure Disk Storage](/azure/virtual-machines/disks-types)i zasobami na [stronie Disk Storage produktu](https://azure.microsoft.com/services/storage/disks/#resources). Możesz dowiedzieć się, które dyski spełniają Twoje wymagania, oraz najnowsze funkcje, takie jak przetwarzanie [dysków](/azure/virtual-machines/disk-bursting). Informacje o sposobach migrowania maszyn wirtualnych wraz z bazowymi urządzeniami opartymi na blokach znajdują się w dokumentacji [Azure Migrate](/azure/migrate/) .

## <a name="see-also"></a>Zobacz też

- [Wybieranie rozwiązania platformy Azure do transferu danych](/azure/storage/common/storage-choose-data-transfer-solution)
- [Porównanie narzędzi do migracji komercyjnej](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)
- [Migracja do udziałów plików usługi Azure File](/azure/storage/files/storage-files-migration-overview)
- [Migrowanie do Data Lake Storage przy użyciu platformy WANdisco LiveData dla platformy Azure](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Kopiowanie lub przenoszenie danych do usługi Azure Storage za pomocą AzCopy](https://aka.ms/azcopy)
- [Migrowanie dużych zestawów danych do usługi Azure Blob Storage za pomocą AzReplicate](https://github.com/Azure/AzReplicate/tree/master/)