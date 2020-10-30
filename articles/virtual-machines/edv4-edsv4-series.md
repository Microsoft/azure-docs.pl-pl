---
title: Serie Edv4 i Edsv4
description: Specyfikacje dotyczące maszyn wirtualnych z serii Ev4, Edv4, Esv4 i Edsv4.
author: brbell
ms.author: brbell
ms.reviewer: jushiman
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 5d02085fe4850dacf6e876b42cf301df2ea76713
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043339"
---
# <a name="edv4-and-edsv4-series"></a>Serie Edv4 i Edsv4

Serie Edv4 i Edsv4 są uruchamiane na &reg; &reg; procesorach Intel Xeon Platinum 8272CL (kaskad Lake) w konfiguracji wielowątkowej i są idealnym rozwiązaniem w przypadku różnych aplikacji przedsiębiorstwa intensywnie korzystających z pamięci oraz funkcji do 504 GIB pamięci RAM [, &reg; technologii Intel Turbo do zwiększania poziomu 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [ &reg; technologii intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) i [intel &reg; Advanced Vector Extensions 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Obsługują one również [technologię Intel &reg; głębokiego uczenia](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Te nowe rozmiary maszyn wirtualnych będą miały 50% większego magazynu lokalnego, a także lepszą liczbę operacji we/wy na dysku lokalnym dla odczytu i zapisu w porównaniu do rozmiarów [EV3/Esv3](./ev3-esv3-series.md) z [maszynami wirtualnymi Gen2](./generation-2.md). Oferuje ona wszystkie podstawowe szybkości zegara Turbo 3,4 GHz. 

## <a name="edv4-series"></a>Seria Edv4

Rozmiary serii Edv4 są uruchamiane na &reg; &reg; procesorach Intel Xeon Platinum 8272CL (Kaskada Lake). Rozmiary maszyn wirtualnych Edv4 do 504 GiB pamięci RAM oprócz szybkiego i dużego lokalnego magazynu SSD (do 2 400 GiB). Te maszyny wirtualne są idealnym rozwiązaniem dla aplikacji i aplikacji przedsiębiorstwa intensywnie korzystających z pamięci, które korzystają z małych opóźnień i magazynu lokalnego o dużej szybkości. Do maszyn wirtualnych Edv4 można dołączyć standardowy magazyn dyskowy dysków SSD i standardowy HDD. 

[ACU](acu.md): 195 – 210<br>
[Premium Storage](premium-storage-performance.md): nieobsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): nieobsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): obsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): obsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | <sup>**</sup> Maksymalna przepływność magazynu w pamięci podręcznej i tymczasowej: IOPS/MB/s | Maksymalna liczba kart sieciowych|Oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v4  | 2 | 16 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_E4d_v4  | 4 | 32 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_E8d_v4 | 8 | 64 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_E16d_v4 | 16 | 128 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_E20d_v4 | 20 | 160 | 750 | 32 | 193000/1211  | 8|10 000 |
| Standard_E32d_v4 | 32 | 256 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_E48d_v4 | 48 | 384 | 1800 | 32 | 462000/2904 | 8|24000 |
| Standard_E64d_v4 | 64 | 504 | 2400 | 32 | 615000/3872 | 8|30000 |


<sup>**</sup> Te wartości IOPs można zagwarantować przy użyciu [maszyn wirtualnych Gen2](generation-2.md)

## <a name="edsv4-series"></a>Seria Edsv4

Rozmiary serii Edsv4 są uruchamiane na &reg; &reg; procesorach Intel Xeon Platinum 8272CL (Kaskada Lake). Rozmiary maszyn wirtualnych Edsv4 do 504 GiB pamięci RAM oprócz szybkiego i dużego lokalnego magazynu SSD (do 2 400 GiB). Te maszyny wirtualne są idealnym rozwiązaniem dla aplikacji i aplikacji przedsiębiorstwa intensywnie korzystających z pamięci, które korzystają z małych opóźnień i magazynu lokalnego o dużej szybkości.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): obsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): obsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | <sup>**</sup> Maksymalna przepływność magazynu w pamięci podręcznej i temp: IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych|Oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4  | 2 | 16 | 75 | 4 | 19000/120 (50) | 3200/48 | 2|1000 |
| Standard_E4ds_v4  | 4 | 32 | 150 | 8 | 38500/242 (100) | 6400/96 | 2|2000 |
| Standard_E8ds_v4 | 8 | 64 | 300 | 16 | 77000/485 (200) | 12800/192 | 4|4000 |
| Standard_E16ds_v4 | 16 | 128 | 600 | 32 | 154000/968 (400) | 25600/384 | 8|8000 |
| Standard_E20ds_v4 | 20 | 160 | 750 | 32 | 193000/1211 (500)  | 32000/480  | 8|10 000 |
| Standard_E32ds_v4 | 32 | 256 | 1200 | 32 | 308000/1936 (800) | 51200/768  | 8|16000 |
| Standard_E48ds_v4 | 48 | 384 | 1800 | 32 | 462000/2904 (1200) | 76800/1152 | 8|24000 |
| Standard_E64ds_v4 <sup>1</sup> | 64 | 504 | 2400 | 32 | 615000/3872 (1600) | 80000/1200 | 8|30000 |

dostępne są <sup>1</sup> [ograniczone rozmiary rdzeni)](./constrained-vcpu.md).


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
