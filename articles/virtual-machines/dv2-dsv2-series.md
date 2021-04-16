---
title: Serie Dv2 i DSv2 — Azure Virtual Machines
description: Specyfikacje maszyn wirtualnych z serii Dv2 i Dsv2.
author: joelpelley
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 71a4cebcc11657566f65f53508df18efe822c409
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514788"
---
# <a name="dv2-and-dsv2-series"></a>Serie Dv2 i DSv2

Seria Dv2 i DSv2, kontynuacja oryginalnej serii D, cechują się bardziej wydajnym procesorem CPU i optymalną konfiguracją procesora CPU do pamięci, dzięki czemu są odpowiednie dla większości obciążeń produkcyjnych. Seria Dv2 jest o około 35% szybsza niż seria D. Seria Dv2 działa na urządzeniach Intel® Xeon® (Kaskada Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® Procesory E5-2673 v4 2,3 GHz (Broadwell) lub Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) z technologią Intel Turbo Boost 2.0. Konfiguracje pamięci i dysków serii Dv2 są takie same jak w przypadku serii D.

## <a name="dv2-series"></a>Seria Dv2

Rozmiary serii Dv2 działają na urządzeniach Intel® Xeon® Dvr 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) lub Intel® Xeon® <2> procesory E5-2673 v4 2,3 GHz (Broadwell) lub Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) z technologią Intel Turbo Boost 2.0.

[ACU:](acu.md)210–250<br>
[Premium Storage:](premium-storage-performance.md)nie jest obsługiwana<br>
[Premium Storage buforowania:](premium-storage-performance.md)nie jest obsługiwane<br>
[migracja na żywo:](maintenance-and-updates.md)obsługiwane<br>
[Aktualizacje zachowujące pamięć:](maintenance-and-updates.md)obsługiwane<br>
[Obsługa generacji maszyny wirtualnej:](generation-2.md)generacja 1<br>
[Przyspieszona sieć:](../virtual-network/create-vm-accelerated-networking-cli.md)obsługiwana *(wymaga co najmniej 2 procesorów wirtualnych)*<br>
[Efemeracyjne dyski systemu operacyjnego:](ephemeral-os-disks.md)nie są obsługiwane <br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu tymczasowego: liczba IOPS/odczyt MB/s/zapis MB/s | Maks. liczba dysków danych | Przepływność: IOPS | Maksymalna liczba kart sieciowych | Oczekiwana przepustowość sieci (Mb/s) |
|---|---|---|---|---|---|---|---|---|
| Standardowa_D1_v2 | 1  | 3,5 | 50  | 3000/46/23    | 4  | 4 x 500  | 2|750   |
| Standardowa_D2_v2 | 2  | 7   | 100 | 6000/93/46    | 8  | 8 x 500  | 2|1500  |
| Standardowa_D3_v2 | 4  | 14  | 200 | 12000/187/93  | 16 | 16 x 500 | 4|3000  |
| Standardowa_D4_v2 | 8  | 28  | 400 | 24000/375/187 | 32 | 32 x 500 | 8|6000  |
| Standardowa_D5_v2 | 16 | 56  | 800 | 48000/750/375 | 64 | 64x500 | 8|12000 |

## <a name="dsv2-series"></a>Seria DSv2

Rozmiary serii DSv2 działają na procesorach Intel® Xeon®Hz 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) lub Intel® Xeon® ® E5-2673 v4 2,3 GHz (Broadwell) lub Procesory Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) z technologią Intel Turbo Boost 2.0 i korzystają z magazynu Premium Storage.

[ACU:](acu.md)210–250<br>
[Premium Storage:](premium-storage-performance.md)Obsługiwane<br>
[Premium Storage buforowania:](premium-storage-performance.md)obsługiwane<br>
[migracja na żywo:](maintenance-and-updates.md)Obsługiwane<br>
[Aktualizacje zachowujące pamięć:](maintenance-and-updates.md)obsługiwane<br>
[Obsługa generacji maszyn wirtualnych:](generation-2.md)generacja 1 i 2<br>
[Przyspieszona sieć:](../virtual-network/create-vm-accelerated-networking-cli.md)obsługiwana *(wymaga co najmniej 2 procesorów wirtualnych)*<br>
[Efemeracyjne dyski systemu operacyjnego:](ephemeral-os-disks.md)obsługiwane <br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność pamięci podręcznej i magazynu tymczasowego: liczba IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność dysku bez pamięci: liczba IOPS/MB/s | Maksymalna liczba kart sieciowych|Oczekiwana przepustowość sieci (Mb/s) |
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

Więcej informacji na temat typów dysków: [Typy dysków](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, [jak jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównaniu wydajności obliczeniowej między jednostkami SKU platformy Azure.
