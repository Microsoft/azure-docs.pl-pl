---
title: Ddv4 i Ddsv4 — seria Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii Dv4, Ddv4, Dsv4 i Ddsv4.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 691584f46059032ecd02a10964b819598d1b3b09
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2020
ms.locfileid: "84484586"
---
# <a name="ddv4-and-ddsv4-series"></a>Serie Ddv4 i Ddsv4

Seria Ddv4 i Ddsv4 jest uruchamiana na &reg; &reg; procesorach Intel Xeon Platinum 8272CL (kaskad Lake) w konfiguracji wielowątkowej, co zapewnia lepszą wartość dla większości obciążeń ogólnego przeznaczenia. Funkcja ta oferuje stałą szybkość zegara Turbo wynoszącą 3,4 GHz, [technologię Intel &reg; Turbo do zwiększania poziomu 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [ &reg; technologii Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) i [Intel &reg; Advanced Vector Extensions 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Obsługują one również [technologię Intel &reg; głębokiego uczenia](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Te nowe rozmiary maszyn wirtualnych będą miały 50% większego magazynu lokalnego, a także lepszą liczbę operacji we/wy na dysku lokalnym dla odczytu i zapisu w porównaniu do rozmiarów [Dv3/Dsv3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series) z [maszynami wirtualnymi Gen2](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2).

Przypadki użycia serii D obejmują aplikacje klasy korporacyjnej, relacyjne bazy danych, buforowanie w pamięci i analizy.

## <a name="ddv4-series"></a>Seria Ddv4

Rozmiary serii Ddv4 są uruchamiane w technologii Intel &reg; Xeon &reg; Platinum 8272CL (Kaskada Lake). Seria Ddv4 oferuje kombinację vCPU, pamięci i dysku tymczasowego dla większości obciążeń produkcyjnych.

Nowe rozmiary maszyn wirtualnych Ddv4 obejmują szybki i większy lokalny magazyn SSD (do 2 400 GiB) i są przeznaczone dla aplikacji, które korzystają z małych opóźnień, dużej szybkości magazynu lokalnego, takich jak aplikacje wymagające szybkiego odczytu/zapisu w magazynie tymczasowym lub wymagające tymczasowego magazynu dla pamięci podręcznej lub plików tymczasowych. Do maszyn wirtualnych Ddv4 można dołączyć standardowy magazyn dysków SSD i standardowy HDD. Magazyn danych zdalnych jest rozliczany osobno od maszyn wirtualnych.

ACU: 195-210

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

Migracja na żywo: obsługiwane

Aktualizacje z zachowaniem pamięci: obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu w pamięci podręcznej i tymczasowej: IOPS/MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|
| Standard_D2d_v4 | 2 | 8 | 75 | 4 | 19000/120 | 2/1000 |
| Standard_D4d_v4 | 4 | 16 | 150 | 8 | 38500/242 | 2/2000 |
| Standard_D8d_v4 | 8 | 32 | 300 | 16 | 77000/485 | 4/4000 |
| Standard_D16d_v4 | 16 | 64 | 600 | 32 | 154000/968 | 8/8000 |
| Standard_D32d_v4 | 32 | 128 | 1200 | 32 | 308000/1936 | 8/16000 |
| Standard_D48d_v4 | 48 | 192 | 1800 | 32 | 462000/2904 | 8/24000 |
| Standard_D64d_v4 | 64 | 256 | 2400 | 32 | 615000/3872 | 8/30000 |

## <a name="ddsv4-series"></a>Seria Ddsv4

Seria Ddsv4 jest uruchamiana na Intel &reg; Xeon &reg; Platinum 8272CL (Kaskada Lake). Seria Ddsv4 oferuje kombinację vCPU, pamięci i dysku tymczasowego dla większości obciążeń produkcyjnych.

Nowe rozmiary maszyn wirtualnych Ddsv4 obejmują szybki i większy lokalny magazyn SSD (do 2 400 GiB) i są przeznaczone dla aplikacji, które korzystają z małych opóźnień, dużej szybkości magazynu lokalnego, takich jak aplikacje wymagające szybkiego odczytu/zapisu w magazynie tymczasowym lub wymagające tymczasowego magazynu dla pamięci podręcznej lub plików tymczasowych. 

 > [!NOTE]
 >Liczniki cen i rozliczeń dla rozmiarów Ddsv4 są takie same jak dla serii Ddv4.

ACU: 195-210

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Migracja na żywo: obsługiwane

Aktualizacje z zachowaniem pamięci: obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepustowość magazynu w pamięci podręcznej i tymczasowej: IOPS/MB/s (pamięć podręczna w GiB) | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_D2ds_v4 | 2 | 8 | 75 | 4 | 19000/120 (50) | 3000/48 | 2/1000 |
| Standard_D4ds_v4 | 4 | 16 | 150 | 8 | 38500/242 (100) | 6400/96 | 2/2000 |
| Standard_D8ds_v4 | 8 | 32 | 300 | 16 | 77000/485 (200) | 12800/192 | 4/4000 |
| Standard_D16ds_v4 | 16 | 64 | 600 | 32 | 154000/968 (400) | 25600/384 | 8/8000 |
| Standard_D32ds_v4 | 32 | 128 | 1200 | 32 | 308000/1936 (800) | 51200/768 | 8/16000 |
| Standard_D48ds_v4 | 48 | 192 | 1800 | 32 | 462000/2904 (1200) | 76800/1152 | 8/24000 |
| Standard_D64ds_v4 | 64 | 256 | 2400 | 32 | 615000/3872 (1600) | 80000/1200 | 8/30000 |

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
