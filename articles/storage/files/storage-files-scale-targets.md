---
title: Cele dotyczące skalowalności i wydajności usługi Azure Files
description: Dowiedz się więcej o skalowalności i wydajności obiektów docelowych dla usług Azure Files, w tym pojemności, szybkości żądań oraz limitów przepustowości przychodzącej i wychodzącej.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 46c46faf8f7ee52978ae5542ab7ebd72a41b8357
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536448"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Cele dotyczące skalowalności i wydajności usługi Azure Files

[Usługa Azure Files](storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem standardowego protokołu SMB. W tym artykule omówiono cele skalowalności i wydajności dla usług Azure Files i Azure File Sync.

Cele skalowalności i wydajności wymienione w tym miejscu są wysokiej klasy obiekty docelowe, ale mogą mieć wpływ na inne zmienne we wdrożeniu. Na przykład przepływność pliku może być również ograniczona przez dostępną przepustowość sieci, a nie tylko serwery obsługujące usługę Azure Files. Zdecydowanie zaleca się przetestowanie wzorca użycia, aby ustalić, czy skalowalność i wydajność usług Azure Files spełniają twoje wymagania. Zobowiązujemy się również do zwiększania tych limitów w czasie. Nie wahaj się przekazać nam opinii, w komentarzach poniżej lub na [temat treści Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), o których ograniczeniach chcesz nas zwiększyć.

## <a name="azure-storage-account-scale-targets"></a>Obiekty docelowe skalowania konta magazynu platformy Azure

Zasobem nadrzędnym dla udziału plików platformy Azure jest konto magazynu platformy Azure. Konto magazynu reprezentuje pulę magazynu na platformie Azure, która może być używana przez wiele usług magazynu, w tym usługi Azure Files, do przechowywania danych. Inne usługi, które przechowują dane na kontach magazynu to Usługa Azure Blob storage, usługa Azure Queue storage i usługa Azure Table storage. Następujące obiekty docelowe dotyczą wszystkich usług magazynowania przechowujących dane na koncie magazynu:

[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Wykorzystanie konta magazynu ogólnego przeznaczenia z innych usług magazynu wpływa na udziały plików platformy Azure na koncie magazynu. Na przykład jeśli osiągniesz maksymalną pojemność konta magazynu magazynu usługi Azure Blob, nie będzie można utworzyć nowych plików w udziale plików platformy Azure, nawet jeśli twój udział plików platformy Azure jest poniżej maksymalnego rozmiaru udziału.

## <a name="azure-files-scale-targets"></a>Obiekty docelowe skalowania plików platformy Azure

Istnieją trzy kategorie ograniczeń, które należy wziąć pod uwagę w przypadku plików platformy Azure: konta magazynu, udziały i pliki.

Na przykład: w przypadku udziałów plików premium pojedynczy udział może osiągnąć 100 000 operacji We/Wy, a pojedynczy plik może skalować do 5000 operacji We/Wy. Tak więc, jeśli masz trzy pliki w jednym udziale, maksymalna liczba we/wy, które możesz uzyskać z tego udziału, wynosi 15 000.

### <a name="standard-storage-account-limits"></a>Standardowe limity kont magazynu

Zobacz sekcję [cele skalowania konta magazynu platformy Azure](#azure-storage-account-scale-targets) dla tych limitów.

### <a name="premium-filestorage-account-limits"></a>Limity kont Usługi FileStorage premium

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> Limity kont magazynu dotyczą wszystkich udziałów. Skalowanie do maks dla kont FileStorage jest osiągalne tylko wtedy, gdy na konto FileStorage przypada tylko jedna akcja.

### <a name="file-share-and-file-scale-targets"></a>Obiekty docelowe udziału plików i skali plików

> [!NOTE]
> Standardowe udziały plików większe niż 5 TiB mają pewne ograniczenia. Aby uzyskać listę ograniczeń i instrukcji umożliwiających włączenie większych rozmiarów udziałów plików, zobacz [sekcję Włączanie większych udziałów plików w standardowych udziałach plików](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) w przewodniku planowania.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Cele skalowania usługi Azure File Sync

Usługa Azure File Sync została zaprojektowana z myślą o nieograniczonym użyciu, ale nieograniczone użycie nie zawsze jest możliwe. Poniższa tabela wskazuje granice testów firmy Microsoft, a także wskazuje, które obiekty docelowe są twarde limity:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Metryki wydajności usługi Azure File Sync

Ponieważ agent synchronizacji plików platformy Azure działa na komputerze z systemem Windows Server, który łączy się z udziałami plików platformy Azure, wydajność efektywnej synchronizacji zależy od wielu czynników w infrastrukturze: Windows Server i podstawowej konfiguracji dysku, przepustowości sieci między serwerem a magazynem platformy Azure, rozmiaru pliku, całkowitego rozmiaru zestawu danych i aktywności w zestawie danych. Ponieważ usługa Azure File Sync działa na poziomie pliku, charakterystyka wydajności rozwiązania opartego na synchronizacji plików platformy Azure jest lepiej mierzona w liczbie obiektów (plików i katalogów) przetwarzanych na sekundę.

W przypadku usługi Azure File Sync wydajność ma kluczowe znaczenie w dwóch etapach:

1. **Początkowe jednorazowe inicjowanie obsługi administracyjnej:** Aby zoptymalizować wydajność wstępnego inicjowania obsługi administracyjnej, zapoznaj się [z dołączaniem z synchronizacją plików platformy Azure,](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) aby uzyskać optymalne szczegóły dotyczące wdrażania.
2. **Ciągła synchronizacja:** Po początkowo rozstawienia danych w udziałach plików platformy Azure usługa Azure File Sync utrzymuje synchronizację wielu punktów końcowych.

Aby ułatwić planowanie wdrożenia dla każdego z etapów, poniżej znajdują się wyniki zaobserwowane podczas wewnętrznych testów w systemie z konfiguracją

| Konfiguracja systemu |  |
|-|-|
| Procesor CPU | 64 rdzenie wirtualne z pamięcią podręczną 64 MiB L3 |
| Memory (Pamięć) | 128 GiB |
| Dysk | Dyski SAS z macierzem RAID 10 z pamięcią podręczną z baterią |
| Sieć | Sieć 1 Gb/s |
| Obciążenie | Serwer plików ogólnego przeznaczenia|

| Początkowe jednorazowe inicjowanie obsługi administracyjnej  |  |
|-|-|
| Liczba obiektów | 25 milionów obiektów |
| Rozmiar zestawu danych| ~4,7 TiB |
| Średni rozmiar pliku | ~200 KiB (Największy plik: 100 GiB) |
| Prześlij przepływność | 20 obiektów na sekundę na grupę synchronizacji |
| Przepływność pobierania obszaru nazw* | 400 obiektów na sekundę |

*Po utworzeniu nowego punktu końcowego serwera agent usługi Azure File Sync nie pobiera żadnej zawartości pliku. Najpierw synchronizuje pełny obszar nazw, a następnie wyzwala wycofanie w tle, aby pobrać pliki w całości lub, jeśli warstwa w chmurze jest włączona, z zasadami warstwowymi w chmurze ustawionymi w punkcie końcowym serwera.

| Trwa synchronizacja  |   |
|-|--|
| Liczba zsynchronizowanych obiektów| 125 000 obiektów (~1% churn) |
| Rozmiar zestawu danych| 50 Gib |
| Średni rozmiar pliku | ~500 KiB |
| Prześlij przepływność | 20 obiektów na sekundę na grupę synchronizacji |
| Pełna przepustowość pobierania* | 60 obiektów na sekundę |

*Jeśli warstwa w chmurze jest włączona, prawdopodobnie będziesz obserwować lepszą wydajność, ponieważ pobierane są tylko niektóre dane plików. Usługa Azure File Sync pobiera dane plików w pamięci podręcznej tylko wtedy, gdy są one zmieniane w dowolnym z punktów końcowych. W przypadku plików warstwowych lub nowo utworzonych agent nie pobiera danych plików, a zamiast tego synchronizuje tylko obszar nazw ze wszystkimi punktami końcowymi serwera. Agent obsługuje również częściowe pobieranie plików warstwowych, ponieważ są one dostępne dla użytkownika. 

> [!Note]  
> Powyższe liczby nie wskazują na wydajność, której doświadczysz. Rzeczywista wydajność będzie zależeć od wielu czynników, jak opisano na początku tej sekcji.

Jako ogólny przewodnik dla wdrożenia, należy pamiętać o kilku rzeczach:

- Przepływność obiektu jest w przybliżeniu skalowana proporcjonalnie do liczby grup synchronizacji na serwerze. Dzielenie danych na wiele grup synchronizacji na serwerze daje lepszą przepustowość, która jest również ograniczona przez serwer i sieć.
- Przepływność obiektu jest odwrotnie proporcjonalna do przepływności MiB na sekundę. W przypadku mniejszych plików będzie większa przepływność pod względem liczby obiektów przetwarzanych na sekundę, ale niższa przepustowość MiB na sekundę. Z drugiej strony w przypadku większych plików otrzymasz mniej obiektów przetwarzanych na sekundę, ale wyższą przepustowość MiB na sekundę. Przepływność MiB na sekundę jest ograniczona przez obiekty docelowe skali usługi Azure Files.

## <a name="see-also"></a>Zobacz też

- [Planowanie wdrażania usługi Pliki Azure](storage-files-planning.md)
- [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)
