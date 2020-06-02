---
title: Rozmiary maszyn wirtualnych platformy Azure — ogólnego przeznaczenia | Microsoft Docs
description: Wyświetla różne rozmiary ogólnego przeznaczenia dostępne dla maszyn wirtualnych na platformie Azure. Wyświetla listę informacji o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych, a także przepływności magazynu i przepustowości sieci dla rozmiarów w tej serii.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: mimckitt
ms.openlocfilehash: b5e597eebda6a730d301eefdbd2d7e6859f5a7cc
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248315"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych ogólnego przeznaczenia

Rozmiary maszyn wirtualnych ogólnego przeznaczenia zapewniają zrównoważony współczynnik procesora CPU do pamięci. Idealne rozwiązanie na potrzeby testowania i wdrażania, małych i średnich baz danych oraz serwerów internetowych o małym lub średnim ruchu. Ten artykuł zawiera informacje o ofertach dla obliczeń ogólnego przeznaczenia.

- Maszyny wirtualne z [serii Av2](av2-series.md) można wdrażać na różnych typach sprzętu i procesorach. Maszyny wirtualne z serii a mają wydajność procesora CPU i konfiguracje pamięci najlepiej dopasowane do obciążeń poziomu wejścia, takich jak programowanie i testowanie. Rozmiar jest ograniczany w zależności od sprzętu, aby zapewnić spójną wydajność procesora dla uruchomionego wystąpienia niezależnie od sprzętu, na którym jest ono wdrożone. Aby określić sprzęt fizyczny, na którym jest wdrażany dany rozmiar, utwórz zapytanie o sprzęt wirtualny z poziomu maszyny wirtualnej. Przykładowe przypadki użycia obejmują serwery deweloperskie i testowe, serwery sieci Web o małym ruchu, małe i średnie bazy danych, sprawdzanie poprawności koncepcji i repozytoria kodu.

  > [!NOTE]
  > Maszyny wirtualne A8 – A11 są planowane do wycofania w dniu 3/2021. Aby uzyskać więcej informacji, zobacz [Przewodnik migracji HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

- [Seria B](sizes-b-series-burstable.md) Maszyny wirtualne doskonale nadają się do obciążeń, które nie wymagają pełnej wydajności procesora CPU, takich jak serwery sieci Web, małe bazy danych i środowiska deweloperskie i testowe. Te obciążenia zwykle mają wymagania dotyczące wydajności. Seria B zapewnia tym klientom możliwość kupowania rozmiaru maszyny wirtualnej przy użyciu ceny linii bazowej, która pozwala na wystąpienie maszyny wirtualnej do kompilowania kredytów, gdy maszyna wirtualna korzysta z mniejszej niż jej wydajność podstawowa. Po rozliczeniu na maszynę wirtualną, maszyna wirtualna może przekroczyć obciążenie maszyny wirtualnej za pomocą do 100% procesora, gdy aplikacja wymaga wyższych wydajności procesora CPU.

- [Serie Dav4 i Dasv4](dav4-dasv4-series.md) to nowe rozmiary wykorzystujące procesor AMD 2.35 GHz EPYC<sup>TM</sup> 7452 w konfiguracji wielowątkowej z maksymalnie 256 MB pamięci podręcznej L3 o pojemności 8 MB pamięci podręcznej L3 do każdego 8 rdzeni, które zwiększają możliwości obsługi obciążeń ogólnego przeznaczenia. Serie Dav4 i Dasv4 mają takie same konfiguracje pamięci i dysków jak seria D & Dsv3.

- [Seria DCv2](dcv2-series.md) może pomóc w ochronie poufności i integralności danych oraz kodu podczas przetwarzania w chmurze publicznej. Te maszyny są obsługiwane przez najnowszą generację procesora Intel XEON E-2288G z technologią SGX. Dzięki technologii zwiększania procesora Intel Turbo te maszyny mogą mieć wartość do 5,0 GHz. Wystąpienia serii DCv2 umożliwiają klientom tworzenie bezpiecznych aplikacji opartych na enklawy w celu ochrony kodu i danych, gdy jest używany.

- [Dv2 i Dsv2 — seria](dv2-dsv2-series.md) Maszyny wirtualne, kolejne z serii D, oferują bardziej wydajny procesor i optymalną konfigurację procesora CPU i pamięci, które są odpowiednie dla większości obciążeń produkcyjnych. Seria Dv2 jest o około 35% szybsza niż seria D. Seria Dv2 jest uruchamiana na procesorach Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) lub procesora Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) z technologią Intel Turbo — rozwiązanie 2,0. Konfiguracje pamięci i dysków serii Dv2 są takie same jak w przypadku serii D.

- [Dv3 i Dsv3 — seria](dv3-dsv3-series.md) Maszyny wirtualne działają na procesorach Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) lub Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) w konfiguracji wielowątkowej, co zapewnia lepszą wartość propozycji dla większości obciążeń ogólnego przeznaczenia. Pamięć została rozszerzona (od ~ 3,5 GiB/vCPU do 4 GiB/vCPU), podczas gdy limity dysku i sieci zostały skorygowane w oparciu o rdzeń, aby dostosować je do przechodzenia do wielowątkowości. Seria Dv3 nie ma już rozmiaru maszyn wirtualnych z serii D/Dv2, które zostały przeniesione do zoptymalizowanej pod kątem pamięci [i serii Esv3](ev3-esv3-series.md).

- [Ddv4 i Ddsv4 — seria](ddv4-ddsv4-series.md) Maszyny wirtualne działają na procesorach drugiej generacji Intel &reg; Xeon &reg; Platinum 8272CL (kaskad Lake), co zapewnia lepszą wartość dla większości obciążeń ogólnego przeznaczenia. Funkcja IT oferuje stałą szybkość zegara Turbo o częstotliwości 3,4 GHz i oferuje [ &reg; technologię Intel Turbo](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) [— technologia &reg; wielowątkowości](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) 2,0 i Intel [ &reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). 


## <a name="other-sizes"></a>Inne rozmiary

- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
