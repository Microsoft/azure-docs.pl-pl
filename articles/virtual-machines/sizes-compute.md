---
title: Rozmiary maszyn wirtualnych platformy Azure — zoptymalizowane pod kątem obliczeń | Microsoft Docs
description: Wyświetla listę różnych rozmiarów zoptymalizowanych pod kątem obliczeń dostępnych dla maszyn wirtualnych na platformie Azure. Wyświetla listę informacji o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych, a także przepływności magazynu i przepustowości sieci dla rozmiarów w tej serii.
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-compute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 01eba7bff571a8db25591b8bfa5c5e712511588f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557696"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych zoptymalizowane pod kątem obliczeń

Rozmiary maszyn wirtualnych zoptymalizowane pod kątem obliczeń mają duży stosunek procesora CPU do pamięci. Te rozmiary są dobre dla serwerów sieci Web o średnim ruchu, urządzeń sieciowych, procesów wsadowych i serwerów aplikacji. Ten artykuł zawiera informacje na temat liczby procesorów wirtualnych vCPU, dysków danych i kart interfejsu sieciowego. Zawiera również informacje o przepływności magazynu i przepustowości sieci dla każdego rozmiaru w ramach tego grupowania.

[Seria Fsv2](fsv2-series.md) jest uruchamiana na procesorach Intel® Xeon® Platinum 8272CL (Kaskada Lake) oraz procesorach Intel® Xeon® Platinum 8168 (Skylake). Oferuje on stałą prędkość zegara Turbo o częstotliwości 3,4 GHz i maksymalną jednordzeniową częstotliwość Turbo 3,7 GHz. Instrukcje Intel® AVX-512 są nowe w skalowalnych procesorach Intel. Te instrukcje umożliwiają zwiększenie wydajności do obciążeń przetwarzania wektorowego zarówno w przypadku operacji zmiennoprzecinkowych o pojedynczej, jak i podwójnej precyzji. Innymi słowy, są one bardzo szybkie w przypadku obciążeń obliczeniowych.

Na niższą cenę za godzinę cennika seria Fsv2 jest najlepszą wartością wydajności w portfolio Azure w oparciu o jednostkę obliczeniową platformy Azure (ACU) na vCPU.

## <a name="other-sizes"></a>Inne rozmiary

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.

Aby uzyskać więcej informacji na temat nazw maszyn wirtualnych platformy Azure, zobacz [konwencje nazewnictwa maszyn wirtualnych platformy Azure](./vm-naming-conventions.md).