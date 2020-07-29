---
title: Dv4 i Dsv4 — seria Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii Dv4 i Dsv4.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 32520a458a797ee9945603dd86c9efe359e4d1f6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291169"
---
# <a name="dv4-and-dsv4-series"></a>Serie Dv4 i Dsv4

Seria Dv4 i Dsv4 jest uruchamiana na &reg; &reg; procesorach Intel Xeon Platinum 8272CL (kaskad Lake) w konfiguracji wielowątkowej, co zapewnia lepszą wartość dla większości obciążeń ogólnego przeznaczenia. Funkcja IT oferuje stałą prędkość zegara Turbo wynoszącą 3,4 GHz. 

> [!NOTE]
> W przypadku często zadawanych pytań zapoznaj się z tematem [rozmiary maszyn wirtualnych platformy Azure bez lokalnego dysku tymczasowego](azure-vms-no-temp-disk.md).
## <a name="dv4-series"></a>Seria Dv4

Rozmiary serii Dv4 są uruchamiane w technologii Intel &reg; Xeon &reg; Platinum 8272CL (Kaskada Lake). Rozmiary serii Dv4 oferują kombinację vCPU, pamięci i opcji magazynu zdalnego dla większości obciążeń produkcyjnych. Maszyny wirtualne z serii Dv4 są wyposażone w [ &reg; technologię wielowątkowości Intel](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html).

Magazyn danych zdalnych jest rozliczany osobno od maszyn wirtualnych. Aby korzystać z dysków magazynu Premium Storage, użyj rozmiarów Dsv4. Liczniki cen i rozliczeń dla rozmiarów Dsv4 są takie same jak dla serii Dv4.


> [!IMPORTANT]
> Te nowe rozmiary są obecnie dostępne tylko w publicznej wersji zapoznawczej. W [tym miejscu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u)możesz wyDv4ać te serie i Dsv4. 


ACU: 195-210

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

Migracja na żywo: obsługiwane

Aktualizacje z zachowaniem pamięci: obsługiwane

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

Rozmiary serii Dsv4 są uruchamiane w technologii Intel &reg; Xeon &reg; Platinum 8272CL (Kaskada Lake). Rozmiary serii Dv4 oferują kombinację vCPU, pamięci i opcji magazynu zdalnego dla większości obciążeń produkcyjnych. Maszyny wirtualne z serii Dsv4 są wyposażone w [ &reg; technologię wielowątkowości Intel](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html). Magazyn danych zdalnych jest rozliczany osobno od maszyn wirtualnych.

> [!IMPORTANT]
> Te nowe rozmiary są obecnie dostępne tylko w publicznej wersji zapoznawczej. W [tym miejscu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u)możesz wyDv4ać te serie i Dsv4. 

ACU: 195-210

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Migracja na żywo: obsługiwane

Aktualizacje z zachowaniem pamięci: obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność pamięci podręcznej: IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych|Oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | Tylko Magazyn zdalny | 4 | 19000/120 (50) | 3000/48 | 2|1000 |
| Standard_D4s_v4 | 4 | 16 | Tylko Magazyn zdalny | 8 | 38500/242 (100) | 6400/96 | 2|2000 |
| Standard_D8s_v4 | 8 | 32 | Tylko Magazyn zdalny | 16 | 77000/485 (200) | 12800/192 | 4|4000 |
| Standard_D16s_v4 | 16 | 64  | Tylko Magazyn zdalny | 32 | 154000/968 (400) | 25600/384 | 8|8000 |
| Standard_D32s_v4 | 32 | 128 | Tylko Magazyn zdalny | 32 | 308000/1936 (800) | 51200/768 | 8|16000 |
| Standard_D48s_v4 | 48 | 192 | Tylko Magazyn zdalny | 32 | 462000/2904 (1200) | 76800/1152 | 8|24000 |
| Standard_D64s_v4 | 64 | 256 | Tylko Magazyn zdalny | 32 | 615000/3872 (1600) | 80000/1200 | 8|30000 |
