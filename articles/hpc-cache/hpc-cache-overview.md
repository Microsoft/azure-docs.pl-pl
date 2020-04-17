---
title: Omówienie usługi Azure HPC Cache
description: W tym artykule opisano usługę Azure HPC Cache , rozwiązanie akceleratora dostępu do plików do obliczeń o wysokiej wydajności
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: 084c33874b474fc1789df93e088d3cec4263eac9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536645"
---
# <a name="what-is-azure-hpc-cache"></a>Co to jest usługa Azure HPC Cache?

Usługa Azure HPC Cache przyspiesza dostęp do danych w celu wykonywania zadań związanych z obliczeniami o wysokiej wydajności (HPC). Buforując pliki na platformie Azure, pamięć podręczna HPC firmy Azure zapewnia skalowalność przetwarzania w chmurze do istniejącego przepływu pracy. Tej usługi można używać nawet w przypadku przepływów pracy, w których dane są przechowywane za pośrednictwem łączy WAN, na przykład w lokalnym środowisku magazynu podłączonego do sieci (NAS).

Pamięć podręczna HPC platformy Azure jest łatwa do uruchamiania i monitorowania z witryny Azure portal. Istniejący magazyn systemu plików NFS lub nowe kontenery obiektów Blob mogą stać się częścią zagregowanego obszaru nazw, co ułatwia dostęp klienta, nawet jeśli zmienisz miejsce docelowe magazynu zaplecza.

## <a name="use-cases"></a>Przypadki zastosowań

Pamięć podręczna HPC usługi Azure zwiększa produktywność najlepiej w przypadku przepływów pracy, takich jak:

* Przepływ pracy dostępu do plików o dużej przełkowej
* Dane przechowywane w magazynie dostępnym dla plików NFS, obiekcie Blob platformy Azure lub obu tych
* Farmy obliczeniowe do 75 000 rdzeni procesora

Pamięć podręczna HPC azure można dodać do wielu różnych przepływów pracy w wielu branżach. Każdy system, w którym duża liczba maszyn musi uzyskać dostęp do zestawu plików na dużą skalę i z małym opóźnieniem, skorzysta z tej usługi. Poniższe sekcje podają konkretne przykłady.

### <a name="visual-effects-vfx-rendering"></a>Renderowanie efektów wizualnych (VFX)

W mediach i rozrywce pamięć podręczna HPC usługi Azure może przyspieszyć dostęp do danych w przypadku projektów renderowania o krytycznym znaczeniu czasowym. Przepływy pracy renderowania VFX często wymagają przetwarzania w ostatniej chwili przez dużą liczbę węzłów obliczeniowych. Dane dla tych przepływów pracy zazwyczaj znajdują się w lokalnym środowisku NAS. Usługa Azure HPC Cache może buforować te dane plików w chmurze, aby zmniejszyć opóźnienia i zwiększyć elastyczność renderowania na żądanie.

### <a name="life-sciences"></a>Nauki przyrodnicze

Wiele przepływów pracy z zakresu nauk przyrodniczych może korzystać ze skalowania w poziomie buforowania plików.

Instytut badawczy, który chce przenieść swoje przepływy pracy analizy genomu na platformę Azure, może łatwo je przenieść za pomocą pamięci podręcznej HPC platformy Azure. Ponieważ pamięć podręczna zapewnia dostęp do plików POSIX, do uruchomienia istniejącego przepływu pracy klienta w chmurze nie są potrzebne żadne zmiany po stronie klienta.

Usługa Azure HPC Cache może być również wykorzystana do zwiększenia wydajności w zadaniach, takich jak analiza pomocnicza, symulacja farmakologiczna lub analiza obrazu oparta na sztucznej inteligencji.

### <a name="financial-services-analytics"></a>Analiza usług finansowych

Wdrożenie pamięci podręcznej HPC platformy Azure może pomóc przyspieszyć obliczenia analizy ilościowej, obciążenia związane z analizą ryzyka i symulacje Monte Carlo, aby zapewnić firmom świadczącym usługi finansowe lepszy wgląd w podejmowanie strategicznych decyzji.

## <a name="region-availability"></a>Dostępność w danym regionie

Pamięć podręczna HPC usługi Azure jest dostępna w następujących regionach platformy Azure:

| Ameryka Północna      | Europa         | Azja            | Australia      |
|--------------------|----------------|-----------------|----------------|
| Wschodnie stany USA            | Europa Północna   | Korea Środkowa   | Australia Wschodnia |
| Wschodnie stany USA 2          | Europa Zachodnia    | Azja Południowo-Wschodnia  |               |
| Południowo-środkowe stany USA | | | |
| Zachodnie stany USA 2        | | | |

[Funkcja kluczy zarządzanych przez klienta](customer-keys.md) jest obsługiwana tylko w następujących regionach:

* Wschodnie stany USA
* Południowo-środkowe stany USA
* Zachodnie stany USA 2

Sprawdź [stronę produktu Usługi Azure HPC Cache,](https://azure.microsoft.com/services/hpc-cache) aby uzyskać najnowsze informacje o dostępności.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj [stronę produktu Usługi Azure HPC Cache,](https://azure.microsoft.com/services/hpc-cache) aby dowiedzieć się więcej o jej możliwościach
* Dowiedz się więcej o [wymaganiach wstępnych produktu](hpc-cache-prereqs.md)
* [Tworzenie pamięci podręcznej HPC platformy Azure](hpc-cache-create.md) z witryny Azure portal
