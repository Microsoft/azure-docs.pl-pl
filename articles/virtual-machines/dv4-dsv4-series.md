---
title: Dv4 i Dsv4 — seria Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii Dv4 i Dsv4.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: cc6ce30855d17f45636e0df04978fed88dcecff7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549145"
---
# <a name="dv4-and-dsv4-series"></a>Serie Dv4 i Dsv4

Seria Dv4 i Dsv4 jest uruchamiana na &reg; &reg; procesorach Intel Xeon Platinum 8272CL (kaskad Lake) w konfiguracji wielowątkowej, co zapewnia lepszą wartość dla większości obciążeń ogólnego przeznaczenia. Oferuje ona wszystkie podstawowe szybkości zegara Turbo 3,4 GHz. 

> [!NOTE]
> W przypadku często zadawanych pytań zapoznaj się z tematem  [rozmiary maszyn wirtualnych platformy Azure bez lokalnego dysku tymczasowego](azure-vms-no-temp-disk.md).
## <a name="dv4-series"></a>Seria Dv4

Rozmiary serii Dv4 są uruchamiane w technologii Intel &reg; Xeon &reg; Platinum 8272CL (Kaskada Lake). Rozmiary serii Dv4 oferują kombinację vCPU, pamięci i opcji magazynu zdalnego dla większości obciążeń produkcyjnych. Maszyny wirtualne z serii Dv4 są wyposażone w [ &reg; technologię Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html).

Magazyn danych zdalnych jest rozliczany osobno od maszyn wirtualnych. Aby korzystać z dysków magazynu Premium Storage, użyj rozmiarów Dsv4. Liczniki cen i rozliczeń dla rozmiarów Dsv4 są takie same jak dla serii Dv4.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): nieobsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): nieobsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): obsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): obsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1<br>
[Przyspieszone sieci](../virtual-network/create-vm-accelerated-networking-cli.md): obsługiwane (*wymaga co najmniej 4 vCPU*)<br>
[Dyski tymczasowe systemu operacyjnego](ephemeral-os-disks.md): nieobsługiwane <br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych|Oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|
| Standard_D2_v4 | 2 | 8 | Tylko Magazyn zdalny | 4 | 2|1000 |
| Standard_D4_v4 | 4 | 16  | Tylko Magazyn zdalny | 8 | 2|2000 |
| Standard_D8_v4 | 8 | 32 | Tylko Magazyn zdalny | 16 | 4|4000 |
| Standard_D16_v4 | 16 | 64 | Tylko Magazyn zdalny | 32 | 8|8000 |
| Standard_D32_v4 | 32 | 128 | Tylko Magazyn zdalny | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | Tylko Magazyn zdalny | 32 | 8|24000 |
| Standard_D64_v4 | 64 | 256 | Tylko Magazyn zdalny | 32 | 8|30000 |

## <a name="dsv4-series"></a>Seria Dsv4

Rozmiary serii Dsv4 są uruchamiane w technologii Intel &reg; Xeon &reg; Platinum 8272CL (Kaskada Lake). Rozmiary serii Dv4 oferują kombinację vCPU, pamięci i opcji magazynu zdalnego dla większości obciążeń produkcyjnych. Maszyny wirtualne z serii Dsv4 są wyposażone w [ &reg; technologię Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html). Magazyn danych zdalnych jest rozliczany osobno od maszyn wirtualnych.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): obsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): obsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>
[Przyspieszone sieci](../virtual-network/create-vm-accelerated-networking-cli.md): obsługiwane (*wymaga co najmniej 4 vCPU*)<br>
[Dyski tymczasowe systemu operacyjnego](ephemeral-os-disks.md): nieobsługiwane <br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych|Oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | Tylko Magazyn zdalny | 4 | 3200/48 | 2|1000 |
| Standard_D4s_v4 | 4 | 16 | Tylko Magazyn zdalny | 8 | 6400/96 | 2|2000 |
| Standard_D8s_v4 | 8 | 32 | Tylko Magazyn zdalny | 16 | 12800/192 | 4|4000 |
| Standard_D16s_v4 | 16 | 64  | Tylko Magazyn zdalny | 32 | 25600/384 | 8|8000 |
| Standard_D32s_v4 | 32 | 128 | Tylko Magazyn zdalny | 32 | 51200/768 | 8|16000 |
| Standard_D48s_v4 | 48 | 192 | Tylko Magazyn zdalny | 32 | 76800/1152 | 8|24000 |
| Standard_D64s_v4 | 64 | 256 | Tylko Magazyn zdalny | 32 | 80000/1200 | 8|30000 |
