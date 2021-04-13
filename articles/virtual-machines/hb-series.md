---
title: Seria HB
description: Specyfikacje dotyczące maszyn wirtualnych z serii HB.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: db3a50108fb8c08371653c08164764cc0a3d492e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309751"
---
# <a name="hb-series"></a>Seria HB

Maszyny wirtualne z serii HB są zoptymalizowane pod kątem aplikacji, które są sterowane przepustowością pamięci, takich jak dynamika płynów, jawnie skończoną analiza elementów i modelowanie pogody. HB VM funkcja 60 AMD EPYC 7551 rdzeni procesora, 4 GB pamięci RAM na rdzeń procesora CPU i bez jednoczesnego wielowątkowości. Maszyna wirtualna HB oferuje do 260 GB/s przepustowości pamięci.

Funkcja maszyn wirtualnych z serii HB 100 GB/s Mellanox EDR InfiniBand. Te maszyny wirtualne są połączone w nieblokującym drzewie FAT na potrzeby zoptymalizowanej i spójnej wydajności RDMA. Te maszyny wirtualne obsługują Routing adaptacyjny i transport podłączony dynamicznie (DCT), a także transporty standardowe RC i UD. Te funkcje rozszerzają wydajność, skalowalność i spójność aplikacji, a ich użycie jest zalecane.

[ACU](acu.md): 199-216<br>
[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Ultra disks](disks-types.md#ultra-disk): obsługiwane ([Dowiedz się więcej](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312) o dostępności, użyciu i wydajności) <br>
[Migracja na żywo](maintenance-and-updates.md): nieobsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): nieobsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>
[Przyspieszona sieć](../virtual-network/create-vm-accelerated-networking-cli.md): obsługiwane ([Dowiedz się więcej](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965) o wydajności i potencjalnych problemach) <br>
[Dyski tymczasowe systemu operacyjnego](ephemeral-os-disks.md): nieobsługiwane <br>
<br>

| Rozmiar | Procesor wirtualny | Procesor | Pamięć (GiB) | Przepustowość pamięci GB/s | Podstawowa częstotliwość procesora CPU (GHz) | Częstotliwość wszystkich rdzeni (GHz, szczyt) | Częstotliwość jednordzeniowa (GHz, szczytowa) | Wydajność RDMA (GB/s) | Obsługa MPI | Magazyn tymczasowy (GiB) | Maks. liczba dysków danych | Maksymalna vNICs sieci Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | PROCESOR AMD EPYC 7551 | 228 | 263 | 2.0 | 2.55 | 2.55 | 100 | Wszystko | 700 | 4 | 8 |

Dowiedz się więcej na temat:
- [Architektura architektury i topologii maszyn wirtualnych](./workloads/hpc/hb-series-overview.md)
- Obsługiwany [stos oprogramowania](./workloads/hpc/hb-series-overview.md#software-specifications) , w tym obsługiwane systemy operacyjne
- Oczekiwana [wydajność](./workloads/hpc/hb-series-performance.md) maszyny wirtualnej z serii HB

[!INCLUDE [hpc-include.md](./workloads/hpc/includes/hpc-include.md)]

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
