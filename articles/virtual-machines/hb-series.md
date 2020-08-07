---
title: Seria HB
description: Specyfikacje dotyczące maszyn wirtualnych z serii HB.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: e735b389b5e223e558736f5eaa3c393f8bc29004
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926267"
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
| Standard_HB60rs | 60 | PROCESOR AMD EPYC 7551 | 240 | 263 | 2,0 | 2.55 | 2.55 | 100 | Wszyscy | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Inne rozmiary

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [konfigurowaniu maszyn wirtualnych](./workloads/hpc/configure.md), [włączeniu funkcji InfiniBand](./workloads/hpc/enable-infiniband.md), [konfigurowaniu MPI](./workloads/hpc/setup-mpi.md)i optymalizowaniu aplikacji HPC dla platformy Azure w ramach [obciążeń HPC](./workloads/hpc/overview.md).
- Przeczytaj o najnowszych anonsach i niektórych przykładach HPC oraz wyniki na [blogach społecznościowych usługi Azure COMPUTE](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Aby zapoznać się z widokiem architektury w przypadku uruchamiania obciążeń HPC, zobacz [wysoka wydajność obliczeń (HPC) na platformie Azure](/azure/architecture/topics/high-performance-computing/).
- Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
