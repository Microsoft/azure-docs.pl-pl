---
title: Seria EV3 i Esv3
description: Specyfikacje dotyczące maszyn wirtualnych z serii EV3 i Esv3.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: lahugh
ms.openlocfilehash: 698b95dc5b38e1d365a80878a5057dee357d81af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91649579"
---
# <a name="ev3-and-esv3-series"></a>Serie Ev3 i Esv3

Seria EV3 i Esv3 działa na Intel® Xeon® Platinum 8272CL (Kaskada Lake), lub Intel® Xeon® 8171M 2,1 GHz (Skylake) lub procesor Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) w konfiguracji wielowątkowej z funkcją Hyper-Threading, co zapewnia lepszą wartość w przypadku większości obciążeń ogólnego przeznaczenia i dostosowanie EV3 do wyrównania z maszynami wirtualnymi ogólnego przeznaczenia dla większości innych chmur.  Pamięć została rozszerzona (od 7 GiB/vCPU do 8 GiB/vCPU), podczas gdy limity dysku i sieci zostały skorygowane dla poszczególnych rdzeni, aby dostosować je do przenoszenia do wielowątkowości. EV3 to kolejne rozmiary maszyn wirtualnych o dużej ilości pamięci w rodzinach D/Dv2.

## <a name="ev3-series"></a>Seria Ev3

Wystąpienia serii EV3 są uruchamiane na Intel® Xeon® Platinum 8272CL (Kaskada Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) lub Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell 2,0) Wystąpienia serii Ev3 są idealne dla aplikacji korporacyjnych intensywnie korzystających z pamięci.

Opłaty za magazyn dysków danych są naliczane oddzielnie od opłat za maszyny wirtualne. Aby korzystać z dysków magazynu Premium Storage, użyj rozmiarów ESv3. Liczniki cen i rozliczeń dla rozmiarów ESv3 są takie same jak dla serii Ev3.

Technologia technologii EV3 firmy Intel® Hyper-Threading.

[ACU](acu.md): 160 – 190<br>
[Premium Storage](premium-storage-performance.md): nieobsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): nieobsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): obsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): obsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1<br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna liczba kart sieciowych / przepustowość sieci |
|---|---|---|---|---|---|---|
| Standardowa_E2_v3  | 2  | 16  | 50   | 4  | 3000/46/23     | 2/1000  |
| Standardowa_E4_v3  | 4  | 32  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standardowa_E8_v3  | 8  | 64  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standardowa_E16_v3 | 16 | 128 | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_E20_v3 | 20 | 160 | 500  | 32 | 30000/469/234  | 8/10000 |
| Standardowa_E32_v3 | 32 | 256 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_E48_v3 | 48 | 384 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standardowa_E64_v3 | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |
| Standard_E64i_v3 <sup>1, 2</sup> | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |

dostępne są <sup>1</sup> ograniczone rozmiary rdzeni.

<sup>2</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.

## <a name="esv3-series"></a>Seria Esv3

Wystąpienia serii Esv3 są uruchamiane na procesorach Intel® Xeon® Platinum 8272CL (Kaskada Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) lub Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), funkcja firmy Intel Turbo zwiększa technologię 2,0 i korzysta z magazynu Premium Storage. Wystąpienia serii Esv3 są idealne dla aplikacji korporacyjnych intensywnie korzystających z pamięci.

Technologia technologii Esv3 firmy Intel® Hyper-Threading.

[ACU](acu.md): 160-190<br>
[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): obsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): obsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu w pamięci podręcznej i temp: IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Przepływność magazynu w pamięci podręcznej i tymczasowej: IOPS/MB/s<sup>3</sup> | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę |  Przepływność dysków niebuforowanych w pamięci podręcznej: liczba IOPS/MB/s<sup>3</sup>| Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|---|---|
| Standardowa_E2s_v3                | 2  | 16  | 32  | 4  | 4000/32 (50)       | 4000/100    | 3200/48    | 4000/100 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>   | 4  | 32  | 64  | 8  | 8000/64 (100)      | 8000/200    | 6400/96    | 8000/200 | 2/2000 |
| Standard_E8s_v3 <sup>1</sup>   | 8  | 64  | 128 | 16 | 16000/128 (200)    | 16000/400   | 12800/192  | 16000/400 | 4/4000 |
| Standard_E16s_v3 <sup>1</sup>  | 16 | 128 | 256 | 32 | 32000/256 (400)    | 32000/800   | 25600/384  | 32000/800 | 8/8000 |
| Standard_E20s_v3               | 20 | 160 | 320 | 32 | 40000/320 (400)    | 40000/1000  | 32000/480  | 40000/1000 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 64000/1600  | 51200/768  | 64000/1600 | 8/16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 96000/2000  | 76800/1152 | 80000/2000 | 8/24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 128000/2000 | 80000/1200 | 80000/2000 | 8/30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 128000/2000 | 80000/1200 | 80000/2000 | 8/30000 |

dostępne są <sup>1</sup> ograniczone rozmiary rdzeni.

<sup>2</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.

<sup>3</sup> maszyny wirtualne z serii Esv3 mogą [rozszeregować](linux/disk-bursting.md) wydajność dysków i uzyskać maksymalnie 30 minut w danym momencie.

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