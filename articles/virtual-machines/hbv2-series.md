---
title: HBv2 — seria Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii HBv2.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 92de7f93939f6673e2133f6eaf8086a1dcdd1964
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102616251"
---
# <a name="hbv2-series"></a>Seria HBv2

Maszyny wirtualne z serii HBv2 są zoptymalizowane pod kątem aplikacji, które są oparte na przepustowości pamięci, takich jak dynamika płynów, skończone analizy elementów i symulacja zbiornika. HBv2 maszyny wirtualne z funkcją 120 AMD EPYC 7742 rdzeni procesora, 4 GB pamięci RAM na rdzeń procesora CPU i bez jednoczesnego wielowątkowości. Każda maszyna wirtualna w HBv2 zapewnia do 340 GB/s przepustowości pamięci oraz do 4 teraflopa obliczeń FP64.

Funkcja maszyn wirtualnych z serii HBv2 200 GB/s, karta Mellanox HDR InfiniBand. Te maszyny wirtualne są połączone w nieblokującym drzewie FAT na potrzeby zoptymalizowanej i spójnej wydajności RDMA. Te maszyny wirtualne obsługują Routing adaptacyjny i transport podłączony dynamicznie (DCT), a także transporty standardowe RC i UD. Te funkcje rozszerzają wydajność, skalowalność i spójność aplikacji, a ich użycie jest zalecane.

[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): nieobsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): nieobsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>
[Przyspieszona sieć](../virtual-network/create-vm-accelerated-networking-cli.md): obsługiwane ([Dowiedz się więcej](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965) o wydajności i potencjalnych problemach) <br>
[Dyski tymczasowe systemu operacyjnego](ephemeral-os-disks.md): nieobsługiwane <br>
<br>

| Rozmiar | Procesor wirtualny | Procesor | Pamięć (GiB) | Przepustowość pamięci GB/s | Podstawowa częstotliwość procesora CPU (GHz) | Częstotliwość wszystkich rdzeni (GHz, szczyt) | Częstotliwość jednordzeniowa (GHz, szczytowa) | Wydajność RDMA (GB/s) | Obsługa MPI | Magazyn tymczasowy (GiB) | Maks. liczba dysków danych | Maksymalna vNICs sieci Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | 7V12 AMD EPYC | 456 | 350 | 2.45 | 3,1 | 3.3 | 200 | Wszystko | 480 + 960 | 8 | 8 |

Dowiedz się więcej o podstawowej [architekturze, topologii maszyn wirtualnych](./workloads/hpc/hbv2-series-overview.md)i oczekiwanej [wydajności](./workloads/hpc/hbv2-performance.md) maszyny wirtualnej z serii HBv2.

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Inne rozmiary

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o najnowszych anonsach, przykładach obciążeń HPC i wynikach wydajności na [blogach społecznościowych usługi Azure COMPUTE Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Aby zapoznać się z widokiem architektury w przypadku uruchamiania obciążeń HPC, zobacz [wysoka wydajność obliczeń (HPC) na platformie Azure](/azure/architecture/topics/high-performance-computing/).
- Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
