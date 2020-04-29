---
title: Fsv2 — seria Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii Fsv2.
services: virtual-machines
author: brbell
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: f28c6b61aee3c8cbc078db1c2cfb48ed1fba4554
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78164852"
---
# <a name="fsv2-series"></a>Seria Fsv2

Seria Fsv2 jest oparta na procesorze Intel® Xeon® Platinum 8168. Oferuje on stałą prędkość zegara Turbo o częstotliwości 3,4 GHz i maksymalną jednordzeniową częstotliwość Turbo 3,7 GHz. Instrukcje Intel® AVX-512 są nowe w skalowalnych procesorach Intel. Te instrukcje umożliwiają zwiększenie wydajności do obciążeń przetwarzania wektorowego zarówno w przypadku operacji zmiennoprzecinkowych o pojedynczej, jak i podwójnej precyzji. Innymi słowy, są one bardzo szybkie w przypadku obciążeń obliczeniowych.

Maszyny wirtualne z serii Fsv2 są wyposażone w technologię Hyper-Threading firmy Intel®.

ACU: 195 – 210

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Migracja na żywo: obsługiwane

Aktualizacje z zachowaniem pamięci: obsługiwane

| Rozmiar | vCPU | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu w pamięci podręcznej i temp: IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 2/875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 2/1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 4/3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 4/7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 8/14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 8/21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 8/28000 |
| Standard_F72s_v2<sup>1, 2</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 8/30000 |

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

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
