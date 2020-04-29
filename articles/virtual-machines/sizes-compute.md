---
title: Rozmiary maszyn wirtualnych platformy Azure — zoptymalizowane pod kątem obliczeń | Microsoft Docs
description: Wyświetla listę różnych rozmiarów zoptymalizowanych pod kątem obliczeń dostępnych dla maszyn wirtualnych na platformie Azure. Wyświetla listę informacji o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych, a także przepływności magazynu i przepustowości sieci dla rozmiarów w tej serii.
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
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: e08d593f641c42f9ad605fda013206e70a34e52f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81269639"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych zoptymalizowane pod kątem obliczeń

Rozmiary maszyn wirtualnych zoptymalizowane pod kątem obliczeń mają duży stosunek procesora CPU do pamięci. Te rozmiary są dobre dla serwerów sieci Web o średnim ruchu, urządzeń sieciowych, procesów wsadowych i serwerów aplikacji. Ten artykuł zawiera informacje na temat liczby procesorów wirtualnych vCPU, dysków danych i kart interfejsu sieciowego. Zawiera również informacje o przepływności magazynu i przepustowości sieci dla każdego rozmiaru w ramach tego grupowania.

[Seria Fsv2](fsv2-series.md) jest oparta na procesorze Intel® Xeon® Platinum 8168. Oferuje on stałą prędkość zegara Turbo o częstotliwości 3,4 GHz i maksymalną jednordzeniową częstotliwość Turbo 3,7 GHz. Instrukcje Intel® AVX-512 są nowe w skalowalnych procesorach Intel. Te instrukcje umożliwiają zwiększenie wydajności do obciążeń przetwarzania wektorowego zarówno w przypadku operacji zmiennoprzecinkowych o pojedynczej, jak i podwójnej precyzji. Innymi słowy, są one bardzo szybkie w przypadku obciążeń obliczeniowych.

Na niższą cenę za godzinę cennika seria Fsv2 jest najlepszą wartością wydajności w portfolio Azure w oparciu o jednostkę obliczeniową platformy Azure (ACU) na vCPU.

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
