---
title: NCas T4 v3 — seria
description: Specyfikacje dotyczące maszyn wirtualnych z serii v3 NCas T4.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: vikancha
ms.openlocfilehash: 5f31148a811ac1a7789cb81d744b46b847105d5c
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917407"
---
# <a name="ncast4_v3-series"></a>Seria NCasT4_v3 

Maszyny wirtualne z serii NCasT4_v3 są obsługiwane przez procesory [NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) GPU i AMD EPYC 7V12 (Rzym). Maszyny wirtualne są wyposażone w maksymalnie 4 procesory GPU T4 z 16 GB pamięci, do 64 rdzeni procesora AMD EPYC 7V12 (Rzym) i 440 GiB pamięci systemowej. Te maszyny wirtualne są idealnym rozwiązaniem do wdrażania usług AI, takich jak inferencing w czasie rzeczywistym żądań generowanych przez użytkowników, lub interaktywnych obciążeń graficznych i wizualizacji przy użyciu sterownika siatki firmy NVIDIA i technologii wirtualnej procesora GPU. Standardowe obciążenia procesora GPU oparte na CUDA, TensorRT, Caffe, ONNX i innych strukturach, a także technologiach opartych na procesorze GPU, oparte na technologii OpenGL i DirectX, można wdrożyć ekonomicznie, z bliską bliskością użytkowników w serii NCasT4_v3.

<br>

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): nieobsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): nieobsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>
[Przyspieszone sieci](../virtual-network/create-vm-accelerated-networking-cli.md): obsługiwane<br>
NVIDIA NVLink Interconnect: nieobsługiwane<br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 / 8000 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4 / 8000  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8 / 8000  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8 / 32000  |


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
