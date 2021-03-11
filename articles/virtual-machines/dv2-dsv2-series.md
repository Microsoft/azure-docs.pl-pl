---
title: Dv2 i DSv2 — seria Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii Dv2 i Dsv2.
author: joelpelley
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: a3dee22a1ee793641e48be6edb1b33a00219217e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560314"
---
# <a name="dv2-and-dsv2-series"></a>Serie Dv2 i DSv2

Serie Dv2 i DSv2, kolejne z serii D, oferują bardziej wydajny procesor CPU i optymalną konfigurację procesora CPU i pamięci, które są odpowiednie dla większości obciążeń produkcyjnych. Seria Dv2 jest o około 35% szybsza niż seria D. Seria Dv2 jest uruchamiana na Intel® Xeon® Platinum 8272CL (Kaskada Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) lub Intel® Xeon® E5-2673 v3 2,4 (Haswell), z technologią Intel Turbo do wzrostu technologii 2,0. Konfiguracje pamięci i dysków serii Dv2 są takie same jak w przypadku serii D.

## <a name="dv2-series"></a>Seria Dv2

Rozmiary serii Dv2 są uruchamiane na platformie Intel® Xeon® Platinum 8272CL (Kaskada Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake) lub Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) lub Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) procesora z technologią Intel Turbo — rozwiązanie 2,0.

[ACU](acu.md): 210-250<br>
[Premium Storage](premium-storage-performance.md): nieobsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): nieobsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): obsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): obsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1<br>
[Przyspieszone sieci](../virtual-network/create-vm-accelerated-networking-cli.md): obsługiwane (*wymaga co najmniej 4 vCPU*)<br>
[Dyski tymczasowe systemu operacyjnego](ephemeral-os-disks.md): nieobsługiwane <br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu: IOPS/Odczyt MB/s/zapis MB/s | Maks. liczba dysków danych | Przepływność: operacje we/wy | Maksymalna liczba kart sieciowych | Oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|---|
| Standardowa_D1_v2 | 1  | 3,5 | 50  | 3000/46/23    | 4  | 4 x 500  | 2|750   |
| Standardowa_D2_v2 | 2  | 7   | 100 | 6000/93/46    | 8  | 8 x 500  | 2|1500  |
| Standardowa_D3_v2 | 4  | 14  | 200 | 12000/187/93  | 16 | 16 x 500 | 4|3000  |
| Standardowa_D4_v2 | 8  | 28  | 400 | 24000/375/187 | 32 | 32 x 500 | 8|6000  |
| Standardowa_D5_v2 | 16 | 56  | 800 | 48000/750/375 | 64 | 64x500 | 8|12000 |

## <a name="dsv2-series"></a>Seria DSv2

Rozmiary serii DSv2 są uruchamiane na platformie Intel® Xeon® Platinum 8272CL (Kaskada Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake) lub Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) lub procesora Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) z technologią Intel Turbo — rozwiązanie 2,0 i korzystanie z magazynu Premium Storage.

[ACU](acu.md): 210-250<br>
[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): obsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): obsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>
[Przyspieszone sieci](../virtual-network/create-vm-accelerated-networking-cli.md): obsługiwane (*wymaga co najmniej 4 vCPU*)<br>
[Dyski tymczasowe systemu operacyjnego](ephemeral-os-disks.md): obsługiwane <br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu w pamięci podręcznej i temp: IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych|Oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|---|
| Standardowa_DS1_v2 | 1  | 3,5 | 7   | 4  | 4000/32 (43)    | 3200/48   | 2|750   |
| Standard_DS2_v2 | 2  | 7   | 14  | 8  | 8000/64 (86)    | 6400/96   | 2|1500  |
| Standardowa_DS3_v2 | 4  | 14  | 28  | 16 | 16000/128 (172) | 12800/192 | 4|3000  |
| Standardowa_DS4_v2 | 8  | 28  | 56  | 32 | 32000/256 (344) | 25600/384 | 8|6000  |
| Standard_DS5_v2 | 16 | 56  | 112 | 64 | 64000/512 (688) | 51200/768 | 8|12000 |

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
