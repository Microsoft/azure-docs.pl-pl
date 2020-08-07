---
title: Seria HC Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii HC.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: b73051ab7cc122f1f371ddac9c77e7243bfa0d73
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87923547"
---
# <a name="hc-series"></a>Seria HC

Maszyny wirtualne z serii HC są zoptymalizowane pod kątem aplikacji opartych na gęstym obliczaniu, takich jak niejawne ograniczone analizy elementów, biocząsteczkowa i obliczeniowa. Funkcja maszyn wirtualnych HC 44 rdzeni procesora Intel Xeon Platinum 8168, 8 GB pamięci RAM na rdzeń procesora CPU i bez wielowątkowości. Platforma Intel Xeon Platinum obsługuje bogate ekosystemy narzędzi programistycznych firmy Intel, takich jak biblioteka jądra matematycznych firmy Intel.

ACU: 297-315

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Migracja na żywo: nieobsługiwane

Aktualizacje z zachowaniem pamięci: nieobsługiwane

| Rozmiar | Procesor wirtualny | Procesor | Pamięć (GB) | Przepustowość pamięci GB/s | Podstawowa częstotliwość procesora CPU (GHz) | Częstotliwość wszystkich rdzeni (GHz, szczyt) | Częstotliwość jednordzeniowa (GHz, szczytowa) | Wydajność RDMA (GB/s) | Obsługa MPI | Magazyn tymczasowy (GB) | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Wszyscy | 700 | 4 | 1 |

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
- Aby zapoznać się z ogólnym widokiem architektury uruchamiania obciążeń HPC, zobacz [wysoka wydajność obliczeń (HPC) na platformie Azure](/azure/architecture/topics/high-performance-computing/).
- Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
