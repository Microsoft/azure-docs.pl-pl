---
title: HBv3 — seria Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii HBv3.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 21576b6714c05e5a0ed703c7a1452103c2c4b355
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103472842"
---
# <a name="hbv3-series"></a>Seria HBv3

Maszyny wirtualne z serii HBv3 są zoptymalizowane pod kątem aplikacji HPC, takich jak dynamika płynów, jawne i niejawne ograniczone analizy elementów, modelowanie pogody, przetwarzanie sejsmiczne, Symulacja zbiornika i symulacja RTL. HBv3 maszyny wirtualne mają do 120 rdzenie procesora AMD EPYC™ 7003 serii (Mediolan), 448 GB pamięci RAM i bez wielowątkowości. Maszyny wirtualne z serii HBv3 zapewniają również 350 GB/s przepustowości pamięci, do 32 MB pamięci podręcznej L3 na rdzeń, do 7 GB/s z urządzenia blokowego wydajności dysków SSD i częstotliwości zegara do 3,675 GHz. 

Wszystkie maszyny wirtualne z serii HBv3 są wyposażone w funkcję 200 GB/s w formacie HDR InfiniBand z sieci NVIDIA w celu włączenia obciążeń MPI w skali komputera. Te maszyny wirtualne są połączone w nieblokującym drzewie FAT na potrzeby zoptymalizowanej i spójnej wydajności RDMA. Sieć szkieletowa HDR InfiniBand obsługuje również adaptacyjne Routing i transport podłączony dynamicznie (DCT, w przypadku dodatkowych do standardowych wersji RC i transportów UD). Te funkcje rozszerzają wydajność, skalowalność i spójność aplikacji, a ich użycie jest zdecydowanie zalecane.

[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): nieobsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): nieobsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>
[Przyspieszona sieć](../virtual-network/create-vm-accelerated-networking-cli.md): wkrótce<br>
[Dyski tymczasowe systemu operacyjnego](ephemeral-os-disks.md): nieobsługiwane <br>


|Rozmiar |Procesor wirtualny |Procesor |Pamięć (GiB) |Przepustowość pamięci GB/s |Podstawowa częstotliwość procesora CPU (GHz) |Częstotliwość wszystkich rdzeni (GHz, szczyt) |Częstotliwość jednordzeniowa (GHz, szczytowa) |Wydajność RDMA (GB/s) |Obsługa MPI |Magazyn tymczasowy (GiB) |Maks. liczba dysków danych |Maksymalna vNICs sieci Ethernet |
|----|----|----|----|----|----|----|----|----|----|----|----|----|
|Standard_HB120rs_v3    |120 |7V13 AMD EPYC |448 |350 |2.45 |3,1 |3,675 |200 |Wszystko |2 * 960 |32 |8 |
|Standard_HB120 — 96rs_v3 |96  |7V13 AMD EPYC |448 |350 |2.45 |3,1 |3,675 |200 |Wszystko |2 * 960 |32 |8 |
|Standard_HB120 — 64rs_v3 |64  |7V13 AMD EPYC |448 |350 |2.45 |3,1 |3,675 |200 |Wszystko |2 * 960 |32 |8 |
|Standard_HB120 — 32rs_v3 |32  |7V13 AMD EPYC |448 |350 |2.45 |3,1 |3,675 |200 |Wszystko |2 * 960 |32 |8 |
|Standard_HB120 — 16rs_v3 |16  |7V13 AMD EPYC |448 |350 |2.45 |3,1 |3,675 |200 |Wszystko |2 * 960 |32 |8 |


Dowiedz się więcej na następujące tematy:
- [Architektura bazowa i topologia maszyny wirtualnej](./workloads/hpc/hbv3-series-overview.md)
- [Obsługiwany stos oprogramowania](./workloads/hpc/hbv3-series-overview.md#software-specifications) , w tym obsługiwane systemy operacyjne
- Oczekiwana [wydajność](./workloads/hpc/hbv3-performance.md) maszyny wirtualnej serii HBv3.

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