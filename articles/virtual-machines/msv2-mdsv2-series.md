---
title: Średnia seria pamięci Msv2/Mdsv2 — Azure Virtual Machines
description: Specyfikacje maszyn wirtualnych z serii Msv2.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: d85623184ad52fb0d4acd4c49d08badfaf886b30
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728234"
---
# <a name="msv2-and-mdsv2-series-medium-memory"></a>Średnia pamięć z serii Msv2 i Mdsv2

Maszyny wirtualne Msv2 i Mdsv2 z średnią pamięcią mają procesor Intel® Xeon® Turbo 8280 (Cascade Lake) z podstawową częstotliwością 2,7 GHz i 4,0 GHz z częstotliwością turbo z jednym rdzeniem. Dzięki tym maszynom wirtualnych klienci osiągają większą elastyczność dzięki dyskom lokalnym i bezdyskowym. Klienci mają również dostęp do zestawu nowych izolowanych rozmiarów maszyn wirtualnych o większej mocy procesora i pamięci, które mogą sięgają 192 procesorów wirtualnych z 4 TiB pamięci. 


Maszyny wirtualne z serii Msv2 i Mdsv2 są tylko 2. generacji i obsługują podzestaw obrazów obsługiwanych przez generację 2. Poniżej znajduje się pełna lista obsługiwanych obrazów dla serii Msv2 i Mdsv2.  

- Windows Server 2019 lub nowszy
- SUSE Linux Enterprise Server 12 z dodatkiem SP4 lub nowszym SUSE Linux Enterprise Server 15 z dodatkiem SP1 lub nowszym
- Red Hat Enterprise Linux 7.6, 7.7, 8.1 lub nowszy 
- Oracle Enterprise Linux 7.7 lub nowszy

Aby uzyskać więcej informacji na temat maszyn wirtualnych generacji 2, zobacz Support for generation 2 VMs on Azure (Obsługa maszyn wirtualnych [2. generacji na platformie Azure).](./generation-2.md)



[Premium Storage:](premium-storage-performance.md)obsługiwane<br>
[Premium Storage buforowania:](premium-storage-performance.md)obsługiwane<br>
[migracja na żywo:](maintenance-and-updates.md)nie są obsługiwane<br>
[Aktualizacje zachowujące pamięć:](maintenance-and-updates.md)nie są obsługiwane<br>
[Obsługa generacji maszyny wirtualnej:](generation-2.md)generacja 2<br>
[akcelerator zapisu:](./how-to-enable-write-accelerator.md)obsługiwane<br>
[Przyspieszona sieć:](../virtual-network/create-vm-accelerated-networking-cli.md)obsługiwane<br>
[Efemeracyjne dyski systemu operacyjnego:](ephemeral-os-disks.md)nie są obsługiwane <br>
<br>
 
## <a name="msv2-medium-memory-diskless"></a>Bezdyskowy dysk w średnich pamięciach MSV2 

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność dysku bez pamięci: liczba IOPS/MB/s | Maksymalna liczba kart sieciowych | Oczekiwana przepustowość sieci (Mb/s) | 
|---|---|---|---|---|---|---|---|
| Standard_M32ms_v2 | 32 | 875 | 0 | 32 |  20000/500 | 8 | 8000 | 
| Standard_M64s_v2 | 64 | 1024 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M64ms_v2 | 64 | 1792 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M128s_v2 | 128 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M128ms_v2 | 128 | 3892 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192is_v2 | 192 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192ims_v2 | 192 | 4096 | 0 | 64 | 80000/2000 | 8 | 30000 | 

## <a name="mdsv2-medium-memory-with-disk"></a>Średnia pamięć mdsv2 z dyskiem  

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalny dysk danych | Maksymalna przepływność pamięci podręcznej i magazynu tymczasowego: liczba IOPS/MB/s | Maksymalna przepływność dysku bez pamięci: liczba IOPS/MB/s | Maksymalna liczba kart sieciowych | Oczekiwana przepustowość sieci (Mb/s) | 
|---|---|---|---|---|---|---|---|---|
| Standard_M32dms_v2 | 32 | 875 | 1024 | 32 | 40000/400 | 20000/500 | 8 | 8000 | 
| Standard_M64ds_v2 | 64 | 1024 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M64dms_v2 | 64 | 1792 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M128ds_v2 | 128 | 2048 | 4096 | 64 |160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M128dms_v2 | 128 | 3892 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192ids_v2 | 192 | 2048 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192idms_v2 | 192 | 4096 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Inne rozmiary i informacje

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

Kalkulator cen: [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, [jak jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównaniu wydajności obliczeniowej między jednostkami SKU platformy Azure.
