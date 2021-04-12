---
title: Seria NVv4
description: Specyfikacje dotyczące maszyn wirtualnych z serii NVv4.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: 152e25fec4ee7b6181e2da58a9a4b0562a918151
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102609196"
---
# <a name="nvv4-series"></a>Seria NVv4 

Maszyny wirtualne z serii NVv4 są obsługiwane przez procesory [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU i AMD EPYC 7V12 (Rzym). Dzięki serii NVv4 platforma Azure wprowadza maszyny wirtualne ze częściowym procesorami GPU. Wybierz maszynę wirtualną o odpowiednim rozmiarze dla przyspieszanych przez procesor GPU aplikacji graficznych i pulpitów wirtualnych, rozpoczynając od 1/8. procesora GPU z 2 GiB bufora klatek do pełnego procesora GPU z 16 GiB buforem ramek. NVv4 maszyny wirtualne obecnie obsługują tylko system operacyjny gościa systemu Windows.

<br>

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): nieobsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): nieobsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>
[Przyspieszone sieci](../virtual-network/create-vm-accelerated-networking-cli.md): obsługiwane<br>
[Dyski tymczasowe systemu operacyjnego](ephemeral-os-disks.md): obsługiwane <br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 / 1000 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 / 2000 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 / 4000 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 / 8000 |

<sup>1</sup> maszyny wirtualne z serii NVv4 — technologia wielowątkowego rozwiązania AMD

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

Aby skorzystać z możliwości procesora GPU dla maszyn wirtualnych z serii NVv4 platformy Azure z systemem Windows, należy zainstalować sterowniki procesora GPU AMD.

Aby ręcznie zainstalować sterowniki procesora GPU AMD, zobacz [Instalator sterowników procesora GPU AMD dla systemu Windows](./windows/n-series-amd-driver-setup.md) w przypadku obsługiwanych systemów operacyjnych, sterowników, instalacji i kroków weryfikacyjnych.

## <a name="other-sizes"></a>Inne rozmiary

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
