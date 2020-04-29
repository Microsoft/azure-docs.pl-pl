---
title: HB-seria-Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii HB.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 600f10e81742e9bb66c800b747fd7b2dc062754d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78164835"
---
# <a name="hb-series"></a>Seria HB

Maszyny wirtualne z serii HB są zoptymalizowane pod kątem aplikacji korzystających z przepustowości pamięci, takich jak dynamika płynów, jawnej, skończonej analizy elementów i modelowania pogody. HB VM funkcja 60 AMD EPYC 7551 rdzeni procesora, 4 GB pamięci RAM na rdzeń procesora CPU i bez jednoczesnego wielowątkowości. Maszyna wirtualna HB oferuje do 260 GB/s przepustowości pamięci.

ACU: 199-216

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Migracja na żywo: nieobsługiwane

Aktualizacje z zachowaniem pamięci: nieobsługiwane

| Rozmiar | Procesor wirtualny | Procesor | Pamięć (GB) | Przepustowość pamięci GB/s | Podstawowa częstotliwość procesora CPU (GHz) | Częstotliwość wszystkich rdzeni (GHz, szczyt) | Częstotliwość jednordzeniowa (GHz, szczytowa) | Wydajność RDMA (GB/s) | Obsługa MPI | Magazyn tymczasowy (GB) | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | PROCESOR AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Wszystkie | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.