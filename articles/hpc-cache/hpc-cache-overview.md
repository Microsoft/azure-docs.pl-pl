---
title: Omówienie usługi Azure HPC cache
description: Opisuje pamięć podręczną platformy Azure HPC, rozwiązanie dostępu do pliku do obliczeń o wysokiej wydajności
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 03/11/2021
ms.author: v-erkel
ms.custom: references_regions
ms.openlocfilehash: bbb0ca345dc76f566dd55956911703e2c9bf0183
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103471817"
---
# <a name="what-is-azure-hpc-cache"></a>Co to jest usługa Azure HPC Cache?

Pamięć podręczna Azure HPC umożliwia przyspieszenie dostępu do danych na potrzeby zadań obliczeniowych o wysokiej wydajności (HPC). Buforowanie plików na platformie Azure powoduje, że pamięć podręczna Azure HPC zapewnia skalowalność przetwarzania w chmurze w istniejącym przepływie pracy. Tej usługi można używać nawet w przypadku przepływów pracy, w których dane są przechowywane w łączach sieci WAN, takich jak w lokalnym środowisku magazynu (NAS) podłączonym do sieci centrum danych.

Pamięć podręczna Azure HPC jest łatwa do uruchomienia i monitorowania z Azure Portal. Istniejący magazyn NFS lub nowe kontenery obiektów BLOB mogą stać się częścią zagregowanej przestrzeni nazw, co sprawia, że dostęp klienta jest prosty nawet w przypadku zmiany miejsca docelowego magazynu zaplecza.

## <a name="overview-video"></a>Klip wideo z omówieniem

[![Miniatura wideo: Przegląd pamięci podręcznej platformy Azure HPC — kliknij, aby odwiedzić stronę wideo](media/video-1-overview.png)](https://azure.microsoft.com/resources/videos/hpc-cache-overview/)

Kliknij obraz powyżej, aby obejrzeć [krótkie omówienie pamięci podręcznej platformy Azure HPC](https://azure.microsoft.com/resources/videos/hpc-cache-overview/).

## <a name="use-cases"></a>Przypadki zastosowań

Pamięć podręczna Azure HPC zwiększa produktywność w przypadku przepływów pracy takich jak:

* Przepływ pracy dostępu do pliku z dużym dostępem do odczytu
* Dane przechowywane w magazynie dostępnym NFS, obiekt blob platformy Azure lub oba
* Farmy obliczeniowe do 75 000 rdzeni procesora CPU

Pamięć podręczna Azure HPC można dodać do wielu różnych przepływów pracy w wielu branżach. Wszystkie systemy, w których duża liczba maszyn musi uzyskać dostęp do zestawu plików na dużą skalę i z małym opóźnieniem, będzie korzystne dla tej usługi. Poniższe sekcje zawierają konkretne przykłady.

### <a name="visual-effects-vfx-rendering"></a>Renderowanie efektów wizualnych (VFX)

W przypadku multimediów i rozrywki pamięć podręczna Azure HPC może przyspieszyć dostęp do danych dla projektów renderowania o kluczowym znaczeniu. Przepływy pracy renderowania VFX często wymagają przetwarzania ostatnich minut przez dużą liczbę węzłów obliczeniowych. Dane dla tych przepływów pracy zwykle znajdują się w lokalnym środowisku usługi NAS. Pamięć podręczna Azure HPC może buforować te dane plików w chmurze w celu zmniejszenia opóźnień i zwiększenia elastyczności renderowania na żądanie.

### <a name="life-sciences"></a>Nauki przyrodnicze

Wiele przepływów pracy nauki przyrodniczej może korzystać z buforowania plików skalowalnych w poziomie.

Instytut badawczy, który chce przenieść swoje przepływy pracy analizy genomiki do platformy Azure, może je łatwo przenieść przy użyciu pamięci podręcznej platformy Azure HPC. Ponieważ pamięć podręczna zapewnia dostęp do plików POSIX, nie są konieczne żadne zmiany po stronie klienta, aby uruchomić swój istniejący przepływ pracy klienta w chmurze.

Pamięć podręczna Azure HPC może być również wykorzystywana w celu zwiększenia wydajności zadań, takich jak analiza pomocnicza, Symulacja farmakologiczna lub Analiza obrazu oparta na AI.

### <a name="financial-services-analytics"></a>Analiza usług finansowych

Wdrożenie pamięci podręcznej platformy Azure HPC może ułatwić przyspieszenie obliczeń analizy ilościowej, obciążeń związanych z analizą ryzyka i symulacji Monte Carlo, aby zapewnić firmom usług finansowych lepszy wgląd w podejmowanie decyzji strategicznych.

## <a name="region-availability"></a>Dostępność w danym regionie

Odwiedź stronę [globalne infrastruktury platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=hpc-cache) , aby dowiedzieć się, gdzie dostępna jest pamięć podręczna Azure HPC.

Pamięć podręczna Azure HPC znajduje się w jednym regionie. Może uzyskać dostęp do danych przechowywanych w innych regionach w przypadku połączenia ich z kontenerami obiektów BLOB znajdującymi się w tym miejscu. Pamięć podręczna nie przechowuje na stałe danych klientów.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj [stronę produktu usługi Azure HPC cache](https://azure.microsoft.com/services/hpc-cache) , aby dowiedzieć się więcej o jej możliwościach
* Informacje o [wymaganiach wstępnych](hpc-cache-prerequisites.md) dotyczących produktu
* [Tworzenie pamięci podręcznej platformy Azure HPC](hpc-cache-create.md) z poziomu Azure Portal
