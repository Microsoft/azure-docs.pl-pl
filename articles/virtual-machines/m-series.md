---
title: Seria M Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii M.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 03/31/2020
ms.author: jushiman
ms.openlocfilehash: 4050a74dfef1fb31939ba716cfbaf78577f1aaef
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84675948"
---
# <a name="m-series"></a>Seria M

Seria M oferuje dużą liczbę vCPU (do 128 procesorów wirtualnych vCPU) i dużą ilość pamięci (do 3,8 TiB). Jest on również idealny dla bardzo dużych baz danych lub innych aplikacji, które korzystają z dużej liczby vCPU i dużych ilości pamięci. Rozmiary serii M są obsługiwane zarówno w &reg; procesorach Intel Xeon, &reg; E7-8890 v3, jak i w technologii Intel &reg; Xeon &reg; Platinum 8280M (Kaskada Lake).

Funkcja maszyny wirtualnej serii M z &reg; technologią Intel Hyper-Threading.

ACU: 160–180

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Migracja na żywo: nieobsługiwane

Aktualizacje z zachowaniem pamięci: nieobsługiwane

Akcelerator zapisu: [obsługiwane](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu w pamięci podręcznej i temp: IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_M8ms                    | 8   | 218,75 | 256   | 8  | 10000/100 (793)     | 5000/125   | 4/2000  |
| Standard_M16ms                   | 16  | 437,5  | 512   | 16 | 20000/200 (1587)    | 10000/250  | 8/4000  |
| Standard_M32ts                   | 32  | 192    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M32ls                   | 32  | 256    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M32ms                   | 32  | 875    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M64s <sup>1</sup>       | 64  | 1024   | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M64ls <sup>1</sup>      | 64  | 512    | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M64ms <sup>1</sup>      | 64  | 1792   | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M128s <sup>1</sup>    | 128 | 2048   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8/30000 |
| Standard_M128ms <sup>1, 2</sup>   | 128 | 3892   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8/30000 |
| Standard_M64 <sup>1</sup>        | 64  | 1024   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8/16000 |
| Standard_M64m <sup>1</sup>       | 64  | 1792   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8/16000 |
| Standard_M128 <sup>1</sup>     | 128 | 2048   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8/32000 |
| Standard_M128m <sup>1</sup>    | 128 | 3892   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8/32000 |

<sup>1</sup> więcej niż 64 vCPU wymaga jednego z tych obsługiwanych systemów operacyjnych gościa: Windows Server 2016, Ubuntu 16,04 LTS, SLES 12 SP2 i Red Hat Enterprise Linux, CentOS 7,3 lub Oracle Linux 7,3 z prze4.2.1.

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
