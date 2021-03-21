---
title: Lsv2 — seria Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii Lsv2.
author: sasha-melamed
ms.service: virtual-machines
ms.subservice: vm-sizes-storage
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 787f9ada921e1580a34431568e87c41496b37f6a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563204"
---
# <a name="lsv2-series"></a>Seria Lsv2

Funkcja serii Lsv2 ma wysoką przepływność, małe opóźnienia, bezpośrednio mapowane lokalne magazyny interfejsu NVMe działające na [procesorze AMD EPYC<sup>TM</sup> 7551](https://www.amd.com/en/products/epyc-7000-series) i wszystkie podstawowe usprawnienia 2.55 GHz oraz maksymalne zwiększenie wydajności 3,0 GHz. Maszyny wirtualne serii Lsv2 mają rozmiary od 8 do 80 vCPU w jednoczesnej konfiguracji wielowątkowej.  Istnieje 8 GiB pamięci na vCPU i jeden dysk 1.92 TB interfejsu NVMe o pojemności M. 2 na 8 procesorów wirtualnych vCPU, z maksymalnie 19,2 TB (10X 1.92 TB) dostępnej w L80s v2.

> [!NOTE]
> Maszyny wirtualne z serii Lsv2 są zoptymalizowane pod kątem używania dysku lokalnego w węźle dołączonym bezpośrednio do maszyny wirtualnej zamiast korzystania z trwałych dysków danych. Pozwala to na większą liczbę operacji we/wy na sekundę dla obciążeń. Seria Lsv2 i ls nie obsługuje tworzenia lokalnej pamięci podręcznej w celu zwiększenia liczby operacji we/wy osiągalnej przez trwałe dyski danych.
>
> Wysoka przepływność i operacje we/wy na dysku lokalnym sprawia, że maszyny wirtualne z serii Lsv2 są idealne dla magazynów NoSQL, takich jak Apache Cassandra i MongoDB, które replikują dane między wieloma maszynami wirtualnymi w celu osiągnięcia trwałości w przypadku awarii pojedynczej maszyny wirtualnej.
>
> Aby dowiedzieć się więcej, zobacz Optymalizacja wydajności na maszynach wirtualnych z serii Lsv2 dla [systemu Windows](../virtual-machines/windows/storage-performance.md) lub [Linux](../virtual-machines/linux/storage-performance.md).  

[ACU](acu.md): 150-175<br>
[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): nieobsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): nieobsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): nieobsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>
Rozrywanie: obsługiwane<br>
[Przyspieszone sieci](../virtual-network/create-vm-accelerated-networking-cli.md): obsługiwane<br>
[Dyski tymczasowe systemu operacyjnego](ephemeral-os-disks.md): nieobsługiwane <br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć (GiB) | Dysk tymczasowy<sup>1</sup> (GIB) | Dyski interfejsu NVMe<sup>2</sup> | Przepływność dysków interfejsu NVMe<sup>3</sup> (odczyt operacji we/wy/s) | Przepływność dysku danych w pamięci podręcznej (IOPs/MB/s)<sup>4</sup> | Maksymalna przepływność dysku danych w niebuforowanej pamięci (IOPs/MB/s)<sup>5</sup>| Maksymalna liczba dysków danych | Maksymalna liczba kart sieciowych | Oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1x 1.92 TB  | 400000/2000  | 8000/160   | 8000/1280 | 16 | 2 | 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2 1.92 TB  | 800000/4000  | 16000/320  | 16000/1280 | 32 | 4 | 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4x 1.92 TB  | 1,5 m/8000    | 32000/640  | 32000/1280 | 32 | 8 | 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6X 1.92 TB  | 2.2 m/14000   | 48000/960  | 48000/2000 | 32 | 8 | 16000 + |
| Standard_L64s_v2  | 64 | 512 | 640 |  8x 1.92 TB  | 2.9 m/16000   | 64000/1280 | 64000/2000 | 32 | 8 | 16000 + |
| Standard_L80s_v2<sup>6</sup> | 80 | 640 | 800 | 10X 1.92 TB | 3.8 m/20000 | 80000/1400 | 80000/2000 | 32 | 8 | 16000 + |

<sup>1</sup> maszyny wirtualne z serii Lsv2 mają standardowy dysk zasobów oparty na interfejsie SCSI na potrzeby STRONICOWANIA systemu operacyjnego/użycia pliku wymiany (D: w systemie Windows,/dev/sdb on Linux). Ten dysk udostępnia 80 GiB magazynu, 4 000 IOPS i 80 liczbę MB/s dla każdego 8 procesorów wirtualnych vCPU (np. Standard_L80s_v2 zapewnia 800 GiB w przypadku operacji wejścia/wyjścia na sekundę i 40 000 MB/s). Dzięki temu dyski interfejsu NVMe mogą być w pełni przeznaczone do użytku aplikacji. Ten dysk jest nieulotny i wszystkie dane zostaną utracone przy zatrzymaniu/cofnięciu przydziału.

<sup>2</sup> lokalne dyski interfejsu NVMe są tymczasowe, dane zostaną utracone na tych dyskach, jeśli zatrzymasz/ZWOLNIsz maszynę wirtualną.

<sup>3</sup> Technologia bezpośredniego interfejsu NVMe funkcji Hyper-V zapewnia nieograniczoną dostęp do lokalnych dysków interfejsu NVMe, które są bezpiecznie mapowane do przestrzeni maszyny wirtualnej gościa.  Osiągnięcie maksymalnej wydajności wymaga użycia najnowszej WS2019 kompilacja lub Ubuntu 18,04 lub 16,04 z portalu Azure Marketplace.  Wydajność zapisu zależy od rozmiaru operacji we/wy, obciążenia dysku i wykorzystania pojemności.

<sup>4</sup> maszyny wirtualne z serii Lsv2 nie zapewniają pamięci podręcznej hosta dla dysku danych, ponieważ nie korzystają z obciążeń Lsv2.

<sup>5</sup> maszyny wirtualne z serii Lsv2 [mogą](./disk-bursting.md) przetworzyć wydajność dysku przez maksymalnie 30 minut w danym momencie. 

<sup>6</sup> maszyny wirtualne z ponad 64 procesorów wirtualnych vCPU wymagają jednego z obsługiwanych systemów operacyjnych gościa:

- System Windows Server 2016 lub nowszy
- Ubuntu 16,04 LTS lub nowszy z dostrojonym jądrem platformy Azure (jądrem 4,15 lub nowszym)
- SLES 12 z dodatkiem SP2 lub nowszym
- RHEL lub CentOS w wersji 6,7 do 6,10 przy użyciu dostarczonej przez firmę Microsoft pakietu LIS (lub nowszego)
- RHEL lub CentOS w wersji 7,3 z pakietem LIS dostarczonym przez firmę Microsoft (lub nowszym)
- RHEL lub CentOS w wersji 7,6 lub nowszej
- Oracle Linux z UEK4 lub nowszym
- Debian 9 z jądrem, Debian 10 lub nowszym
- CoreOS z jądrem 4,14 lub nowszym

## <a name="size-table-definitions"></a>Definicje tabel rozmiaru

- Pojemność magazynu jest podawana w jednostkach GiB (1024^3 bajtów). Podczas porównywania dysków mierzonych w GB (1000^3 bajtów) z dyskami mierzonymi w GiB (1024^3 bajtów) należy pamiętać, że pojemność podawana w GiB może wydawać się mniejsza. Na przykład 1023 GiB = 1098,4 GB.
- Przepływność dysku mierzona jest jako liczba operacji wejścia/wyjścia na sekundę i MB/s, gdzie 1 MB/s = 10^6 bajtów/s.
- Jeśli chcesz uzyskać najlepszą wydajność dla maszyn wirtualnych, należy ograniczyć liczbę dysków danych do 2 dysków na vCPU.
- **Oczekiwana przepustowość sieci** to maksymalna zagregowana [przepustowość przyalokowana na typ maszyny wirtualnej](../virtual-network/virtual-machine-network-throughput.md) dla wszystkich kart sieciowych dla wszystkich miejsc docelowych. Górne limity nie są gwarantowane, ale mają stanowić wskazówkę do wybierania właściwego typu maszyny wirtualnej dla planowanej aplikacji. Rzeczywista wydajność sieci będzie zależeć od wielu czynników, takich jak przeciążenie sieci, obciążenie aplikacji oraz ustawienia sieci. Aby uzyskać informacje na temat optymalizowania przepływności sieci, zobacz [Optimizing network throughput for Windows and Linux (Optymalizowanie przepływności sieci dla systemów Windows i Linux)](../virtual-network/virtual-network-optimize-network-bandwidth.md). Aby uzyskać oczekiwaną wydajność sieci w systemie Linux lub Windows, konieczne może być wybranie konkretnej wersji maszyny wirtualnej lub jej zoptymalizowanie. Aby uzyskać więcej informacji, zobacz [How to reliably test for virtual machine throughput (Jak wiarygodnie przetestować przepływność maszyny wirtualnej)](../virtual-network/virtual-network-bandwidth-testing.md).


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