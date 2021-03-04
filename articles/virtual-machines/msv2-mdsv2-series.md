---
title: Msv2 — seria (wersja zapoznawcza) — Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii Msv2.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: 986b02ee1127bc929ce34518226424ba06d24b89
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101549"
---
# <a name="msv2-and-mdsv2-series-medium-memory-preview"></a>Pamięć średniej z serii Msv2 i Mdsv2 (wersja zapoznawcza)


> [!IMPORTANT]
> Dołącz do wersji zapoznawczej, wypełniając formularz pod adresem **https://aka.ms/Mv2MedMemoryPreview** .  

Seria maszyn wirtualnych Msv2 i Mdsv2 średniej ilości pamięci oferuje procesor Intel® Xeon® Platinum 8280 (Kaskada Lake) z podstawową częstotliwością bazową 2,7 GHz i 4,0 pojedynczej częstotliwości Turbo. Dzięki tym maszynom wirtualnym klienci uzyskują większą elastyczność dzięki możliwościom dysków lokalnych i bezdyskowych. Klienci mają również dostęp do zestawu nowych izolowanych rozmiarów maszyn wirtualnych o większej liczbie procesorów i pamięci, które wychodzą do 192 vCPU z 4 TiB pamięci. 


Maszyny wirtualne z serii Msv2 i Mdsv2 są tylko 2 generacji i obsługują podzbiór obrazów obsługiwanych przez generację 2. Zapoznaj się z pełną listą obsługiwanych obrazów dla serii Msv2 i Mdsv2.  

- System Windows Server 2019 lub nowszy
- SUSE Linux Enterprise Server 12 z dodatkiem SP4 lub nowszym lub SUSE Linux Enterprise Server 15 SP1 i nowsze
- Red Hat Enterprise Linux 7,6, 7,7, 8,1 lub nowszy 
- Oracle Enterprise Linux 7,7 lub nowszy

Aby uzyskać więcej informacji na temat maszyn wirtualnych 2. generacji, zobacz [Obsługa maszyn wirtualnych 2. generacji na platformie Azure](./generation-2.md).



[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): nieobsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): nieobsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 2<br>
[Akcelerator zapisu](./how-to-enable-write-accelerator.md): obsługiwane<br>
[Przyspieszone sieci](../virtual-network/create-vm-accelerated-networking-cli.md): obsługiwane<br>
[Dyski tymczasowe systemu operacyjnego](ephemeral-os-disks.md): nieobsługiwane <br>
<br>
 
## <a name="msv2-medium-memory-diskless"></a>Msv2 pamięci średniej 

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych | Oczekiwana przepustowość sieci (MB/s) | 
|---|---|---|---|---|---|---|---|
| Standard_M32ms_v2 | 32 | 875 | 0 | 32 |  20000/500 | 8 | 8000 | 
| Standard_M64s_v2 | 64 | 1024 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M64ms_v2 | 64 | 1792 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M128s_v2 | 128 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M128ms_v2 | 128 | 3892 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192is_v2 | 192 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192ims_v2 | 192 | 4096 | 0 | 64 | 80000/2000 | 8 | 30000 | 

## <a name="mdsv2-medium-memory-with-disk"></a>Mdsv2 średniej pamięci z dyskiem  

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalny dysk danych | Maksymalna przepływność magazynu w pamięci podręcznej i tymczasowej: IOPS/MB/s | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych | Oczekiwana przepustowość sieci (MB/s) | 
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

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
