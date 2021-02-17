---
title: Serie Dv3 i Dsv3
description: Specyfikacje dotyczące maszyn wirtualnych z serii Dv3 i Dsv3.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: jushiman
ms.openlocfilehash: 8faf86037bc5c2be4ab83adcbcd7e992840d329a
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557771"
---
# <a name="dv3-and-dsv3-series"></a>Serie Dv3 i Dsv3

Seria Dv3 jest uruchamiana na Intel® Xeon® Platinum 8272CL (Kaskada Lake), procesor Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) lub procesor Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) w konfiguracji wielowątkowej. Pamięć została rozszerzona (od ~ 3,5 GiB/vCPU do 4 GiB/vCPU), podczas gdy limity dysku i sieci zostały skorygowane w oparciu o rdzeń, aby dostosować je do przechodzenia do wielowątkowości. Seria Dv3 nie ma już rozmiaru maszyn wirtualnych z serii D/Dv2, które zostały przeniesione do zoptymalizowanej pod kątem pamięci [i serii Esv3](ev3-esv3-series.md).

Przykładowe przypadki użycia serii D obejmują aplikacje klasy korporacyjnej, relacyjne bazy danych, buforowanie w pamięci i analizy.

## <a name="dv3-series"></a>Seria Dv3

Rozmiary serii Dv3 są uruchamiane na procesorach Intel® Xeon® Platinum 8272CL (Kaskada Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) lub procesora Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) z [ &reg; technologią Intel Turbo](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)— rozwiązanie 2,0. Rozmiary serii Dv3 oferują kombinację procesora wirtualnego vCPU, pamięci i magazynu tymczasowego spełniającą potrzeby większości obciążeń produkcyjnych.

Opłaty za magazyn dysków danych są naliczane oddzielnie od opłat za maszyny wirtualne. Aby korzystać z dysków magazynu Premium Storage, użyj rozmiarów Dsv3. Liczniki cen i rozliczeń dla rozmiarów Dsv3 są takie same jak dla serii Dv3.

Maszyny wirtualne z serii Dv3 oferują technologię Intel® Hyper-Threading.

[ACU](acu.md): 160-190<br>
[Premium Storage](premium-storage-performance.md): nieobsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): nieobsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): obsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): obsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1<br>
[Przyspieszone sieci](../virtual-network/create-vm-accelerated-networking-cli.md): obsługiwane (*wymaga co najmniej 4 vCPU*)<br>
[Dyski tymczasowe systemu operacyjnego](ephemeral-os-disks.md): nieobsługiwane <br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu: IOPS/Odczyt MB/s/zapis MB/s | Maksymalna liczba kart sieciowych/przepustowość sieci |
|---|---|---|---|---|---|---|
| Standardowa_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2/1000  |
| Standardowa_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standardowa_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standardowa_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96000/1000/500 | 8/30000 |

## <a name="dsv3-series"></a>Seria Dsv3

Rozmiary serii Dsv3 są uruchamiane na procesorach Intel® Xeon® Platinum 8272CL (Kaskada Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) lub procesora Intel® Xeon® E5-v3 2,4 GHz (2673) z [ &reg; technologią Intel Turbo](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) — rozwiązanie 2,0 i korzystanie z magazynu Premium Storage. Rozmiary serii Dsv3 oferują kombinację procesora wirtualnego vCPU, pamięci i magazynu tymczasowego spełniającą potrzeby większości obciążeń produkcyjnych.

Maszyny wirtualne z serii Dsv3 oferują technologię Intel® Hyper-Threading.

[ACU](acu.md): 160-190<br>
[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): obsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): obsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>
[Przyspieszone sieci](../virtual-network/create-vm-accelerated-networking-cli.md): obsługiwane (*wymaga co najmniej 4 vCPU*)<br>
[Dyski tymczasowe systemu operacyjnego](ephemeral-os-disks.md): obsługiwane <br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu w pamięci podręcznej i temp: IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność magazynu w pamięci podręcznej i tymczasowej: IOPS/s<sup>1</sup> | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna przepływność dysków niebuforowanych w pamięci podręcznej: liczba operacji we/wy na sekundę<sup>1</sup> | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|---|---|
| Standardowa_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 4000/100    |3200/48    | 4000/100   | 2/1000  |
| Standardowa_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 8000/200    |6400/96    | 8000/200   | 2/2000  |
| Standardowa_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 16000/400   |12800/192  | 16000/400  | 4/4000  |
| Standardowa_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 32000/800   |25600/384  | 32000/800  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 64000/1600  |51200/768  | 64000/1600 | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 96000/2000  |76800/1152 | 80000/2000 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 128000/2000 |80000/1200 | 80000/2000 | 8/30000 |

<sup>1</sup>  maszyny wirtualne z serii Dsv3 mogą [rozszeregować](./disk-bursting.md) wydajność dysków i uzyskać maksymalnie 30 minut w danym momencie.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Inne rozmiary i informacje

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)
- [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/)
- Aby uzyskać więcej informacji na temat typów dysków, zobacz, [jakie typy dysków są dostępne na platformie Azure?](disks-types.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.