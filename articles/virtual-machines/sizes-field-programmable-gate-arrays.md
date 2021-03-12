---
title: Rozmiary maszyn wirtualnych platformy Azure dla nieprogramowalnych tablic bram (FPGA)
description: Wyświetla listę różnych FPGA zoptymalizowanych rozmiarów dostępnych dla maszyn wirtualnych na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych oraz o przepływności magazynu i przepustowości sieci dla rozmiarów w tej serii.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-fpga
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: vikancha
ms.openlocfilehash: d9eb0d5bc93cbe9c2a7cbae56c336115bb227b04
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557679"
---
# <a name="fpga-optimized-virtual-machine-sizes"></a>FPGA zoptymalizowane rozmiary maszyn wirtualnych

Zoptymalizowane rozmiary maszyn wirtualnych FPGA są wyspecjalizowanymi maszynami wirtualnymi z jedną lub wieloma FPGA. Te rozmiary są przeznaczone do obciążeń intensywnie korzystających z obliczeń. Ten artykuł zawiera informacje o liczbie i typie FPGA, procesorów wirtualnych vCPU, dysków danych i kartach sieciowych. Przepustowość magazynu i przepustowość sieci są również uwzględniane dla każdego rozmiaru w ramach grupowania.

- Rozmiary [serii np](np-series.md) są zoptymalizowane pod kątem obciążeń, w tym wnioskowania o uczenie maszynowe, transkodowania wideo i wyszukiwania w bazie danych & analizy. Seria NPa jest obsługiwana przez akceleratory U250 Xilinx.


## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

- Aby uzyskać dostęp do maszyn wirtualnych z serii N, zobacz [produkty dostępne według regionów](https://azure.microsoft.com/regions/services/).

- Maszyny wirtualne z serii N można wdrożyć tylko w modelu wdrażania Menedżer zasobów.

- Jeśli chcesz wdrożyć więcej niż kilka maszyn wirtualnych serii N, weź pod uwagę subskrypcję z opcją płatność zgodnie z rzeczywistym użyciem lub inne opcje zakupu. Jeśli używasz [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), możesz użyć ograniczonej liczby rdzeni obliczeniowych platformy Azure.

- Może być konieczne zwiększenie limitu przydziału rdzeni (na region) w ramach subskrypcji platformy Azure i zwiększenie oddzielnego przydziału dla rdzeni NP. Aby zażądać zwiększenia limitu przydziału, [Otwórz bezpłatnie żądanie pomocy technicznej w trybie online](../azure-portal/supportability/how-to-create-azure-support-request.md) . Limity domyślne mogą się różnić w zależności od kategorii subskrypcji.

## <a name="other-sizes"></a>Inne rozmiary

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Przyspieszenie procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
