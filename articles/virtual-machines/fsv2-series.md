---
title: Seria Fsv2
description: Specyfikacje dotyczące maszyn wirtualnych z serii Fsv2.
author: brbell
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: d633d631a8d905532601ec15700ff80eed644efe
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920160"
---
# <a name="fsv2-series"></a>Seria Fsv2

Seria Fsv2 jest uruchamiana na procesorach Intel® Xeon® Platinum 8272CL (Kaskada Lake) oraz procesorach Intel® Xeon® Platinum 8168 (Skylake). Oferuje on stałą prędkość zegara Turbo o częstotliwości 3,4 GHz i maksymalną jednordzeniową częstotliwość Turbo 3,7 GHz. Instrukcje Intel® AVX-512 są nowe w skalowalnych procesorach Intel. Te instrukcje umożliwiają zwiększenie wydajności do obciążeń przetwarzania wektorowego zarówno w przypadku operacji zmiennoprzecinkowych o pojedynczej, jak i podwójnej precyzji. Innymi słowy, są one bardzo szybkie w przypadku obciążeń obliczeniowych.

Maszyny wirtualne z serii Fsv2 oferują technologię Intel® Hyper-Threading.

[ACU](acu.md): 195 – 210<br>
[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): obsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): obsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>
[Przyspieszone sieci](../virtual-network/create-vm-accelerated-networking-cli.md): obsługiwane<br>
<br>

| Rozmiar | vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu w pamięci podręcznej i temp: IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych|Oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 2|875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 2|1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 4|3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 4|7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 8|14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 8|21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 8|28000 |
| Standard_F72s_v2<sup>1, 2</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 8|30000 |

<sup>1</sup> użycie więcej niż 64 vCPU wymaga jednego z obsługiwanych systemów operacyjnych gościa:

- System Windows Server 2016 lub nowszy
- Ubuntu 16,04 LTS lub nowszy z dostrojonym jądrem platformy Azure (jądrem 4,15 lub nowszym)
- SLES 12 z dodatkiem SP2 lub nowszym
- RHEL lub CentOS w wersji 6,7 do 6,10 przy użyciu dostarczonej przez firmę Microsoft pakietu LIS (lub nowszego)
- RHEL lub CentOS w wersji 7,3 z pakietem LIS dostarczonym przez firmę Microsoft (lub nowszym)
- RHEL lub CentOS w wersji 7,6 lub nowszej
- Oracle Linux z UEK4 lub nowszym
- Debian 9 z jądrem, Debian 10 lub nowszym
- CoreOS z jądrem 4,14 lub nowszym

<sup>2</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.

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