---
title: NCas T4 v3 — seria
description: Specyfikacje dotyczące maszyn wirtualnych z serii v3 NCas T4.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: vikancha
ms.openlocfilehash: 73d583cb371876cefc10c982dbe66f6e15bddf03
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88932614"
---
# <a name="ncast4_v3-series"></a>Seria NCasT4_v3 

Maszyny wirtualne z serii NCasT4_v3 są obsługiwane przez procesory [NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) GPU i AMD EPYC 7V12 (Rzym). Maszyny wirtualne są wyposażone w maksymalnie 4 procesory GPU T4 z 16 GB pamięci, do 64 rdzeni procesora AMD EPYC 7V12 (Rzym) i 440 GiB pamięci systemowej. Te maszyny wirtualne są idealne do uruchamiania obciążeń ML i AI wykorzystujących CUDA, TensorFlow, Pytorch, Caffe i inne platformy lub obciążenia grafiki przy użyciu technologii NVIDIA GRID. Seria NCasT4_v3 jest idealnym rozwiązaniem do uruchamiania obciążeń wnioskowania.

<br>

ACU: 230-260

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Migracja na żywo: nieobsługiwane

Aktualizacje z zachowaniem pamięci: nieobsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

Aby skorzystać z możliwości procesora GPU dla maszyn wirtualnych z serii NCasT4_v3 platformy Azure z systemem Windows lub Linux, należy zainstalować sterowniki NVIDIA GPU.

Aby ręcznie zainstalować sterowniki procesora GPU firmy NVIDIA, zobacz [konfigurację sterowników procesora GPU dla systemu Windows](./windows/n-series-driver-setup.md) w przypadku obsługiwanych systemów operacyjnych, sterowników, instalacji i kroków weryfikacji.

## <a name="other-sizes"></a>Inne rozmiary

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
