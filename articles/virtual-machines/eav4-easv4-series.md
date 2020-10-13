---
title: Seria Eav4 i Easv4
description: Specyfikacje dotyczące maszyn wirtualnych z serii Eav4 i Easv4.
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: f95ffb662d830c432d3286eca8c935e2737f8043
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91649562"
---
# <a name="eav4-and-easv4-series"></a>Serie Eav4 i Easv4

Serie Eav4 i Easv4 wykorzystują procesor<sup>AMD 7452 z</sup> 2.35 GHz EPYC, w konfiguracji wielowątkowej, z maksymalnie 256 MB pamięci podręcznej L3, zwiększając opcje uruchamiania większości zoptymalizowanych pod kątem pamięci. Serie Eav4 i Easv4 mają takie same konfiguracje pamięci i dysków jak seria EV3 & Esv3.

## <a name="eav4-series"></a>Seria Eav4

[ACU](acu.md): 230 – 260<br>
[Premium Storage](premium-storage-performance.md): nieobsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): nieobsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): obsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): obsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1<br>
<br>

Rozmiary serii Eav4 są oparte na procesorze 2.35 GHz AMD EPYC<sup>TM</sup> 7452, który może osiągnąć podwyższaną maksymalną częstotliwość 3.35 GHz i korzystać z dysku SSD Premium. Rozmiary serii Eav4 są idealne dla aplikacji korporacyjnych intensywnie korzystających z pamięci. Opłaty za magazyn dysków danych są naliczane oddzielnie od opłat za maszyny wirtualne. Aby korzystać z dysku SSD Premium, użyj rozmiarów serii Easv4. Liczniki cen i rozliczeń dla rozmiarów Easv4 są takie same jak dla serii Eav3.

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba kart sieciowych | Oczekiwana przepustowość sieci (MB/s) |
| -----|-----|-----|-----|-----|-----|-----|-----|
| Standardowa \_ E2A \_ v4|2|16|50|4|3000 / 46 / 23|2 | 1000 |
| Standardowa \_ E4A \_ v4|4|32|100|8|6000 / 93 / 46|2 | 2000 |
| Standardowa \_ E8a \_ v4|8|64|200|16|12000 / 187 / 93|4 | 4000 |
| Standardowa \_ E16a \_ v4|16|128|400|32|24000 / 375 / 187|8 | 8000 |
| Standardowa \_ E20a \_ v4|20|160|500|32|30000/468/234|8 | 10 000 |
| Standardowa \_ E32a \_ v4|32|256|800|32|48000 / 750 / 375|8 | 16000 |
| Standardowa \_ E48a \_ v4|48|384|1200|32|96000/1000 (500)|8 | 24000 |
| Standardowa \_ E64a \_ v4|64|512|1600|32|96000/1000 (500)|8 | 30000 |
| Standardowa \_ E96a \_ v4|96|672|2400|32|96000/1000 (500)|8 | 30000 |

## <a name="easv4-series"></a>Seria Easv4

[ACU](acu.md): 230 – 260<br>
[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): obsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): obsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1<br>
<br>

Rozmiary serii Easv4 są oparte na procesorze 2.35 GHz AMD EPYC<sup>TM</sup> 7452, który może osiągnąć podwyższaną maksymalną częstotliwość 3.35 GHz i korzystać z dysku SSD Premium. Rozmiary serii Easv4 są idealne dla aplikacji korporacyjnych intensywnie korzystających z pamięci.

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych | Oczekiwana przepustowość sieci (MB/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 (50)|3200/48|2 | 1000 |
| Standard_E4as_v4|4|32|64|8|8000/64 (100)|6400/96|2 | 2000 |
| Standard_E8as_v4|8|64|128|16|16000/128 (200)|12800/192|4 | 4000 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400)|25600/384|8 | 8000 |
| Standard_E20as_v4|20|160|320|32|40000/320 (500)|32000/480|8 | 10 000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800)|51200/768|8 | 16000 |
| Standard_E48as_v4|48|384|768|32|96000/1020 (1200)|76800/1148|8 | 24000 |
| Standard_E64as_v4|64|512|1024|32|128000/1020 (1600)|80000/1200|8 | 30000 |
| Standard_E96as_v4|96|672|1344|32|192000/1020 (2400)|80000/1200|8 | 30000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Inne rozmiary i informacje

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

Kalkulator cen: [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/)

Więcej informacji na temat typów dysków: [typy dysków](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
