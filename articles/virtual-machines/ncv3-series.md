---
title: Seria NCV3 — seria Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii seria NCV3.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: be76b2b6398048925ce308b421fcd776a7b7acbb
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306402"
---
# <a name="ncv3-series"></a>Seria NCv3

Maszyny wirtualne z serii seria NCV3 są obsługiwane przez procesory NVIDIA Tesla V100 GPU. Te procesory GPU mogą zapewnić 1,5 x wydajności obliczeniowej serii NCv2. Klienci mogą korzystać z tych zaktualizowanych procesorów GPU dla tradycyjnych obciążeń HPC, takich jak modelowanie zbiorników, sekwencjonowanie DNA, analiza białka, symulacje Monte Carlo i inne. Konfiguracja NC24rs v3 zapewnia małe opóźnienia i wysoką przepływność interfejsu sieciowego zoptymalizowane pod kątem ściśle sprzężonych równoległych obciążeń obliczeniowych. Poza procesorami GPU maszyny wirtualne serii seria NCV3 są również obsługiwane przez procesory Intel Xeon E5-2690 v4 (Broadwell).

[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Ultra disks](disks-types.md#ultra-disk): obsługiwane ([Dowiedz się więcej](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312) o dostępności, użyciu i wydajności) <br>
[Migracja na żywo](maintenance-and-updates.md): nieobsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): nieobsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>
[Przyspieszone sieci](../virtual-network/create-vm-accelerated-networking-cli.md): nieobsługiwane<br>
[Dyski tymczasowe systemu operacyjnego](ephemeral-os-disks.md): obsługiwane <br>
NVIDIA NVLink Interconnect: nieobsługiwane<br>

> [!IMPORTANT]
> W przypadku tej serii maszyn wirtualnych limit przydziału vCPU (rdzeń) w subskrypcji jest początkowo ustawiany na 0 w każdym regionie. [Zażądaj zwiększenia limitu przydziału vCPU](../azure-portal/supportability/resource-manager-core-quotas-request.md) dla tej serii w [dostępnym regionie](https://azure.microsoft.com/regions/services/). Te jednostki SKU nie są dostępne dla subskrypcji platformy Azure w wersji próbnej lub Visual Studio. Poziom subskrypcji może nie obsługiwać wybierania ani wdrażania tych jednostek SKU. 
>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v3    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v3 * | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 procesor GPU = jedna karta V100.

*Obsługa technologii RDMA

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

Aby skorzystać z możliwości procesora GPU maszyn wirtualnych z serii N, należy zainstalować sterowniki graficznego procesora NVIDIA.

[Rozszerzenie sterownika NVIDIA GPU](./extensions/hpccompute-gpu-windows.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub Grid na maszynie wirtualnej z serii N. Zainstaluj rozszerzenie lub Zarządzaj nim przy użyciu Azure Portal lub narzędzi, takich jak szablony Azure PowerShell lub Azure Resource Manager. Zapoznaj się z [dokumentacją rozszerzenia sterownika graficznego procesora NVIDIA](./extensions/hpccompute-gpu-windows.md) dla obsługiwanych systemów operacyjnych i kroków wdrażania. Aby uzyskać ogólne informacje o rozszerzeniach maszyn wirtualnych, zobacz [rozszerzenia i funkcje maszyny wirtualnej platformy Azure](./extensions/overview.md).

W przypadku wybrania opcji ręcznego instalowania sterowników procesora GPU firmy NVIDIA należy zapoznać się z tematem Konfiguracja [sterownika procesora GPU dla systemu Windows](./windows/n-series-driver-setup.md) lub [n-Series](./linux/n-series-driver-setup.md) w systemie Linux dla obsługiwanych systemów operacyjnych, sterowników, instalacji i kroków weryfikacyjnych.

## <a name="other-sizes"></a>Inne rozmiary

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
