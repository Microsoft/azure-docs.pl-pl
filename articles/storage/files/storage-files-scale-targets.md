---
title: Cele dotyczące skalowalności i wydajności usługi Azure Files
description: Poznaj elementy docelowe skalowalności i wydajności dla Azure Files, w tym pojemność, szybkość żądania oraz limity przepustowości ruchu przychodzącego i wychodzącego.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 57d04fff069e7cd7d766125bc7364cf4648911ad
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948351"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Cele dotyczące skalowalności i wydajności usługi Azure Files

[Azure Files](storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem standardowego protokołu SMB. W tym artykule omówiono elementy docelowe skalowalności i wydajności dla Azure Files i Azure File Sync.

Elementy docelowe skalowalności i wydajności wymienione w tym miejscu są obiektami docelowymi wysokiej klasy, ale mogą mieć wpływ inne zmienne we wdrożeniu. Na przykład przepływność pliku może być również ograniczona przez dostępną przepustowość sieci, a nie tylko serwery obsługujące usługę Azure Files. Zdecydowanie zalecamy testowanie wzorca użycia w celu określenia, czy skalowalność i wydajność Azure Files spełniają Twoje wymagania. Ponadto firma Microsoft dokłada starań, aby zwiększyć te limity w miarę upływu czasu. Nie niechętnie zezwalają, aby przekazać nam swoją opinię, albo w poniższych komentarzach lub w [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), o które limity chcesz zobaczyć.

## <a name="azure-storage-account-scale-targets"></a>Cele skalowania konta usługi Azure Storage

Zasób nadrzędny dla udziału plików platformy Azure to konto usługi Azure Storage. Konto magazynu reprezentuje pulę magazynu na platformie Azure, która może być używana przez wiele usług magazynu, w tym Azure Files, do przechowywania danych. Inne usługi, które przechowują dane na kontach magazynu, to Azure Blob Storage, Azure queue storage i Azure Table Storage. Następujące cele dotyczą wszystkich usług magazynu przechowujących dane na koncie magazynu:

[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Użycie konta magazynu ogólnego zastosowania z innych usług magazynu ma wpływ na udziały plików platformy Azure na Twoim koncie magazynu. Jeśli na przykład osiągniesz maksymalną pojemność konta magazynu za pomocą usługi Azure Blob Storage, nie będziesz mieć możliwości tworzenia nowych plików w udziale plików platformy Azure, nawet jeśli udział plików platformy Azure jest mniejszy niż maksymalny rozmiar udziału.

## <a name="azure-files-scale-targets"></a>Elementy docelowe skalowania Azure Files

Istnieją trzy kategorie ograniczeń, które należy wziąć pod uwagę w Azure Files: konta magazynu, udziały i pliki.

Na przykład: z udziałami plików w warstwie Premium pojedynczy udział może osiągać 100 000 operacji we/wy, a pojedynczy plik może skalować do 5 000 operacji we/wy na sekundę. Dlatego jeśli masz trzy pliki w jednym udziale, Maksymalna liczba IOPS, którą można uzyskać z tego udziału, to 15 000.

### <a name="standard-storage-account-limits"></a>Limity kont magazynu w warstwie Standardowa

Zapoznaj się z sekcją [cele skalowania konta usługi Azure Storage](#azure-storage-account-scale-targets) , aby uzyskać te limity.

### <a name="premium-filestorage-account-limits"></a>Limity kont FileStorage Premium

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> Limity konta magazynu dotyczą wszystkich udziałów. Skalowanie w górę do maksimum dla kont FileStorage jest osiągalne tylko wtedy, gdy istnieje tylko jeden udział na konto FileStorage.

### <a name="file-share-and-file-scale-targets"></a>Elementy docelowe udziału plików i skalowania plików

> [!NOTE]
> Standardowe udziały plików większe niż 5 TiB mają pewne ograniczenia. Listę ograniczeń i instrukcji dotyczących włączania większych rozmiarów udziałów plików można znaleźć w sekcji Włączanie udziałów plików [w warstwie Standardowa w](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) podręczniku planowania.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Cele skalowania usługi Azure File Sync

Azure File Sync został zaprojektowany z myślą o celu nieograniczonego użycia, ale nieograniczone użycie nie zawsze jest możliwe. Poniższa tabela wskazuje granice testowania firmy Microsoft, a także wskazuje, które cele są sztywne:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Metryki wydajności usługi Azure File Sync

Ponieważ agent Azure File Sync działa na komputerze z systemem Windows Server, który nawiązuje połączenie z udziałami plików platformy Azure, efektywna wydajność synchronizacji zależy od wielu czynników w infrastrukturze: system Windows Server i podstawowa Konfiguracja dysku, przepustowość sieci między serwerem a magazynem platformy Azure, rozmiar pliku, całkowity rozmiar zestawu danych i działanie w zestawie danych. Ponieważ Azure File Sync działa na poziomie pliku, charakterystyka wydajności rozwiązania opartego na Azure File Sync jest lepiej mierzona w liczbie obiektów (plików i katalogów) przetworzonych na sekundę.

W przypadku Azure File Sync wydajność ma kluczowe znaczenie w dwóch etapach:

1. **Początkowe wstępne Inicjowanie obsługi**: aby zoptymalizować wydajność początkowej aprowizacji, zapoznaj się z tematem [dołączania do Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) , aby uzyskać szczegółowe informacje na temat optymalnego wdrożenia.
2. Aktualna **Synchronizacja**: po początkowym umieszczeniu danych w udziałach plików platformy Azure Azure File Sync synchronizuje wiele punktów końcowych.

Aby ułatwić Planowanie wdrożenia dla każdego z etapów, poniżej przedstawiono wyniki zaobserwowane podczas wewnętrznego testowania w systemie z konfiguracją

| Konfiguracja systemu | Szczegóły |
|-|-|
| Procesor CPU | 64 rdzeni wirtualnych z 64 MiB L3 pamięci podręcznej |
| Pamięć | 128 GiB |
| Dysk | Dyski SAS z macierzą RAID 10 z zapasową pamięcią podręczną |
| Sieć | Sieć 1 GB/s |
| Obciążenie | Ogólnego przeznaczenia serwera plików|

| Początkowe Inicjowanie obsługi po raz pierwszy  | Szczegóły |
|-|-|
| Liczba obiektów | 25 000 000 obiektów |
| Rozmiar zestawu danych| ~ 4,7 TiB |
| Średni rozmiar pliku | ~ 200 KiB (największy plik: 100 GiB) |
| Początkowe Wyliczenie zmian w chmurze | 7 obiektów na sekundę  |
| Przepływność przekazywania | 20 obiektów na sekundę na grupę synchronizacji |
| Przepływność pobierania przestrzeni nazw | 400 obiektów na sekundę |

### <a name="initial-one-time-provisioning"></a>Początkowe Inicjowanie obsługi po raz pierwszy

**Początkowe Wyliczenie zmian w chmurze**: po utworzeniu nowej grupy synchronizacji, początkowym wyliczeniem zmian w chmurze jest pierwszy krok, który zostanie wykonany. W tym procesie system wylicza wszystkie elementy w udziale plików platformy Azure. W trakcie tego procesu nie będzie żadnych działań synchronizacji, co oznacza, że żadne elementy nie zostaną pobrane z punktu końcowego w chmurze do punktu końcowego serwera i żadne elementy nie zostaną przekazane z punktu końcowego serwera do punktu końcowego w chmurze. Działanie synchronizacji zostanie wznowione po zakończeniu początkowego wyliczenia zmian w chmurze.
Szybkość działania wynosi 7 obiektów na sekundę. Klienci mogą oszacować czas trwania wstępnego wyliczania zmian w chmurze, określając liczbę elementów w udziale chmury i korzystając z następującej formuły, aby uzyskać czas w dniach. Czas (w dniach) dla początkowej wyliczenia chmury = (liczba obiektów w punkcie końcowym w chmurze)/(7*60*60 * 24)

**Przepływność pobierania przestrzeni nazw** Po dodaniu nowego punktu końcowego serwera do istniejącej grupy synchronizacji Agent Azure File Sync nie pobiera żadnej zawartości pliku z punktu końcowego w chmurze. Najpierw synchronizuje pełną przestrzeń nazw, a następnie wyzwala odwołanie w tle w celu pobrania plików, w całości lub, jeśli włączono obsługę warstw w chmurze, do zasad obsługi warstw w chmurze ustawionych w punkcie końcowym serwera.


| Synchronizacja bieżąca  | Szczegóły  |
|-|--|
| Liczba zsynchronizowanych obiektów| 125 000 obiektów (~ 1% zmian) |
| Rozmiar zestawu danych| 50 GiB |
| Średni rozmiar pliku | ~ 500 KiB |
| Przepływność przekazywania | 20 obiektów na sekundę na grupę synchronizacji |
| Przepływność pełnego pobierania * | 60 obiektów na sekundę |

* Jeśli włączono obsługę warstw w chmurze, prawdopodobnie zobaczysz lepszą wydajność, ponieważ pobierane są tylko niektóre z danych plików. Azure File Sync pobiera tylko dane plików z pamięci podręcznej, gdy zostaną zmienione na dowolnym z punktów końcowych. W przypadku wszystkich warstwowych lub nowo utworzonych plików Agent nie pobiera danych plików, a zamiast tego synchronizuje przestrzeń nazw ze wszystkimi punktami końcowymi serwera. Agent obsługuje również częściowe pobieranie plików warstwowych, które są dostępne dla użytkownika. 

> [!Note]  
> Powyższe numery nie są wskaźnikami wydajności, które będą używane. Rzeczywista wydajność będzie zależeć od wielu czynników, jak opisano na początku tej sekcji.

Ogólnie rzecz biorąc, należy pamiętać o kilku kwestiach:

- Przepływność obiektu jest około skalowana proporcjonalnie do liczby grup synchronizacji na serwerze. Dzielenie danych na wiele grup synchronizacji na serwerze daje lepszą przepływność, co jest również ograniczone przez serwer i sieć.
- Przepływność obiektu jest odwrotnie proporcjonalna do przepływności MiB na sekundę. W przypadku mniejszych plików można zwiększyć przepływność w zakresie liczby obiektów przetworzonych na sekundę, ale mniejszą przepływność MiB na sekundę. Z kolei w przypadku większych plików można uzyskać mniej obiektów przetworzonych na sekundę, ale wyższą przepływność MiB na sekundę. Przepływność MiB na sekundę jest ograniczona przez elementy docelowe skalowania Azure Files.

## <a name="see-also"></a>Zobacz też

- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
- [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)
