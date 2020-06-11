---
title: Seria EV3 i Esv3
description: Specyfikacje dotyczące maszyn wirtualnych z serii EV3 i Esv3.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 0121f73da911a9346fd1f142d98246709631a742
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84678107"
---
# <a name="ev3-and-esv3-series"></a>Serie Ev3 i Esv3

EV3 i Esv3 — funkcja serii Intel® Xeon® 8171M 2,1 GHz (Skylake) lub Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) w konfiguracji wielowątkowej, zapewniając lepszą wartość dla większości obciążeń ogólnego przeznaczenia, a także dostosowanie EV3 do wyrównania z maszynami wirtualnymi ogólnego przeznaczenia dla większości innych chmur.  Pamięć została rozszerzona (od 7 GiB/vCPU do 8 GiB/vCPU), podczas gdy limity dysku i sieci zostały skorygowane dla poszczególnych rdzeni, aby dostosować je do przenoszenia do wielowątkowości. EV3 to kolejne rozmiary maszyn wirtualnych o dużej ilości pamięci w rodzinach D/Dv2.

## <a name="ev3-series"></a>Seria Ev3

Wystąpienia serii EV3 są oparte na funkcjach Intel® Xeon® 8171M 2,1 GHz (Skylake) lub procesora Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) i technologii Intel Turbo do zwiększania wydajności 2,0. Wystąpienia serii Ev3 są idealne dla aplikacji korporacyjnych intensywnie korzystających z pamięci.

Opłaty za magazyn dysków danych są naliczane oddzielnie od opłat za maszyny wirtualne. Aby korzystać z dysków magazynu Premium Storage, użyj rozmiarów ESv3. Liczniki cen i rozliczeń dla rozmiarów ESv3 są takie same jak dla serii Ev3.

Funkcja Hyper-Threading maszyny wirtualnej z serii EV3 firmy Intel®.

ACU: 160–190

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

Migracja na żywo: obsługiwane

Aktualizacje z zachowaniem pamięci: obsługiwane

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

Wystąpienia serii Esv3 są oparte na funkcjach technologii Intel® Xeon® 8171M 2,1 GHz (Skylake) lub procesora Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), 2,0 technologii Intel Turbo do zwiększania jakości i korzystania z magazynu Premium Storage. Wystąpienia serii Esv3 są idealne dla aplikacji korporacyjnych intensywnie korzystających z pamięci.

Funkcja Hyper-Threading maszyny wirtualnej z serii Esv3 firmy Intel®.

ACU: 160–190

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Migracja na żywo: obsługiwane

Aktualizacje z zachowaniem pamięci: obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu w pamięci podręcznej i temp: IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|
| Standardowa_E2s_v3 | 2 | 16 | 32 | 4 | 4000/32 (50) | 3200/48 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>  | 4  | 32  | 64  | 8  | 8000/64 (100)   | 6400/96   | 2/2000 |
| Standard_E8s_v3 <sup>1</sup>  | 8  | 64  | 128 | 16 | 16000/128 (200) | 12800/192 | 4/4000 |
| Standard_E16s_v3 <sup>1</sup> | 16 | 128 | 256 | 32 | 32000/256 (400) | 25600/384 | 8/8000 |
| Standard_E20s_v3 | 20 | 160 | 320 | 32 | 40000/320 (400) | 32000/480 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

dostępne są <sup>1</sup> ograniczone rozmiary rdzeni.

<sup>2</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
