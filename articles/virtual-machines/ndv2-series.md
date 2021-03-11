---
title: Seria NDv2
description: Specyfikacje dotyczące maszyn wirtualnych z serii NDv2.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 2f062aa43132543a2c149039eb50c0a0ab22d83d
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618563"
---
# <a name="updated-ndv2-series"></a>Zaktualizowana seria NDv2

Maszyna wirtualna z serii NDv2 to nowa dołączenie do rodziny procesorów GPU przeznaczonej do potrzeb najbardziej wymagających obciążeń procesora GPU, uczenia maszynowego, symulacji i wdrożenia HPC.

NDv2 jest obsługiwany przez 8 NVIDIA Tesla V100 NVLINK-podłączonych procesorów GPU, z których każdy ma 32 GB pamięci GPU. Każda maszyna wirtualna NDv2 ma także 40 rdzenie procesora Intel Xeon Platinum 8168 (Skylake) i 672 GiB pamięci systemowej.

Wystąpienia NDv2 zapewniają doskonałą wydajność obciążeń HPC i AI wykorzystujących jądra CUDA z zoptymalizowanymi procesorami GPU, a także wiele narzędzi AI, ML i analitycznych, które obsługują przyspieszenie procesora GPU, takie jak TensorFlow, Pytorch, Caffe, RAPIDS i inne platformy.

Krytycznie, NDv2 jest zbudowany na potrzeby skalowania w górę i skalowalnego w górę (włącznie z 8 procesorami GPU na maszynę wirtualną) i skalowania w poziomie (z obsługą wielu maszyn wirtualnych współpracujących) obciążeń. Seria NDv2 obsługuje teraz sieci 100-Gigabit InfiniBand EDR zaplecza, podobnie jak te dostępne w serii HB maszyny wirtualnej HPC, aby umożliwić klastrowanie o wysokiej wydajności dla scenariuszy równoległych, w tym rozmieszczonych szkoleń dla AI i ML. Ta sieć zaplecza obsługuje wszystkie główne protokoły InfiniBand, włącznie z tymi, które są używane przez biblioteki NCCL2 firmy NVIDIA, umożliwiając bezproblemowe klastrowanie procesorów GPU.

> [!IMPORTANT]
> Podczas [włączania funkcji InfiniBand](./workloads/hpc/enable-infiniband.md) na maszynie wirtualnej ND40rs_v2 należy użyć sterownika 1.0.0.1 Mellanox OFED.
>
> Ze względu na zwiększoną ilość pamięci GPU Nowa maszyna wirtualna ND40rs_v2 wymaga użycia [maszyn wirtualnych 2. generacji](./generation-2.md) i obrazów w portalu Marketplace. 
>
> Uwaga: ND40s_v2 z 16 GB pamięci na procesor GPU nie jest już dostępny do wersji zapoznawczej i został zastąpiony przez zaktualizowane ND40rs_v2.

<br>

[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): nieobsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): nieobsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 2<br>
[Przyspieszone sieci](../virtual-network/create-vm-accelerated-networking-cli.md): obsługiwane<br>
[Dyski tymczasowe systemu operacyjnego](ephemeral-os-disks.md): obsługiwane <br>
InfiniBand: obsługiwane<br>
NVIDIA NVLink Interconnect: obsługiwane<br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna przepustowość sieci | Maksymalna liczba kart sieciowych |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 32 | 32 | 80000/800 | 24000 MB/s | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

Aby skorzystać z możliwości procesora GPU maszyn wirtualnych z serii N, należy zainstalować sterowniki graficznego procesora NVIDIA.

[Rozszerzenie sterownika NVIDIA GPU](./extensions/hpccompute-gpu-linux.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub Grid na maszynie wirtualnej z serii N. Zainstaluj rozszerzenie lub Zarządzaj nim przy użyciu Azure Portal lub narzędzi, takich jak szablony Azure PowerShell lub Azure Resource Manager. Aby uzyskać ogólne informacje o rozszerzeniach maszyn wirtualnych, zobacz [rozszerzenia i funkcje maszyny wirtualnej platformy Azure](./extensions/overview.md).

W przypadku ręcznego instalowania sterowników procesora GPU firmy NVIDIA należy zapoznać się z tematem [Konfiguracja sterownika procesora GPU dla systemu Linux](./linux/n-series-driver-setup.md).

## <a name="other-sizes"></a>Inne rozmiary

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
