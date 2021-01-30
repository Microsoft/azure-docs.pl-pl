---
title: Maszyny wirtualne z serii Dv2 i DSv2 — platforma Azure Virtual Machines
description: Specyfikacje dotyczące maszyn wirtualnych z serii Dv2 i DSv2.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: b6f99f1f58faece3ab286b2bc3cb571cdcba87de
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99088908"
---
# <a name="memory-optimized-dv2-and-dsv2-series"></a>Zoptymalizowane pod kątem pamięci Dv2 i Dsv2

Dv2 i Dsv2 — kolejne z serii D, które oferują bardziej wydajny procesor. Rozmiary serii DSv2 są uruchamiane na procesorach Intel® Xeon® Platinum 8272CL (Kaskada Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) lub Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) lub procesora Intel® Xeon® E5-2673 v3 2,4 GHz. Konfiguracje pamięci i dysków serii Dv2 są takie same jak w przypadku serii D.

## <a name="dv2-series-11-15"></a>Dv2 — Seria 11-15

Rozmiary serii Dv2 są uruchamiane na procesorach Intel® Xeon® Platinum 8272CL (Kaskada Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) lub Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) lub procesora Intel® Xeon® E5-2673 v3 2,4 GHz.

[ACU](acu.md): 210 – 250<br>
[Premium Storage](premium-storage-performance.md): nieobsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): nieobsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): obsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): obsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1<br>
[Przyspieszone sieci](../virtual-network/create-vm-accelerated-networking-cli.md): obsługiwane<br>
[Dyski tymczasowe systemu operacyjnego](ephemeral-os-disks.md): nieobsługiwane <br>
<br> 

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu: IOPS/Odczyt MB/s/zapis MB/s | Maksymalna liczba dysków danych/przepływność: IOPS | Maksymalna liczba kart sieciowych|Oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|
| Standardowa_D11_v2 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2|1500  |
| Standardowa_D12_v2 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4|3000  |
| Standardowa_D13_v2 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8|6000  |
| Standardowa_D14_v2 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8|12000 |
| Standard_D15_v2 <sup>1</sup> | 20 | 140 | 1000 | 60000/937/468 | 64/64x500 | 8|25000 <sup>2</sup> |

<sup>1</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.
<sup>2</sup> 25000 MB/s z przyspieszoną siecią.

## <a name="dsv2-series-11-15"></a>DSv2 — Seria 11-15

Rozmiary serii DSv2 są uruchamiane na procesorach Intel® Xeon® Platinum 8272CL (Kaskada Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) lub Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) lub procesora Intel® Xeon® E5-2673 v3 2,4 GHz.

[ACU](acu.md): 210 – 250 <sup>1</sup><br>
[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): obsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): obsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>
[Przyspieszone sieci](../virtual-network/create-vm-accelerated-networking-cli.md): obsługiwane<br>
[Dyski tymczasowe systemu operacyjnego](ephemeral-os-disks.md): obsługiwane <br>
<br> 

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu w pamięci podręcznej i temp: IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych|Oczekiwana przepustowość sieci (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |---|
| Standard_DS11_v2 <sup>3</sup> | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/96   | 2|1500  |
| Standard_DS12_v2 <sup>3</sup> | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/192 | 4|3000  |
| Standard_DS13_v2 <sup>3</sup> | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/384 | 8|6000  |
| Standard_DS14_v2 <sup>3</sup> | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/768 | 8|12000 |
| Standard_DS15_v2 <sup>2</sup> | 20 | 140 | 280 | 64 | 80000/640 (720) | 64000/960 | 8|25000 <sup>4</sup> |

<sup>1</sup> maksymalna przepływność dysku (IOPS lub MB/s) możliwa dla maszyny wirtualnej serii DSv2 może być ograniczona przez liczbę, rozmiar i rozkładanie dołączonych dysków.  Aby uzyskać szczegółowe informacje, zobacz [projektowanie pod kątem wysokiej wydajności](./premium-storage-performance.md).
<sup>2</sup>  wystąpienie jest izolowane do sprzętu opartego na architekturze Intel Haswell i przeznaczone dla jednego klienta.  
dostępne są <sup>3</sup> ograniczone rozmiary rdzeni.  
<sup>4</sup> 25000 MB/s z przyspieszoną siecią.

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