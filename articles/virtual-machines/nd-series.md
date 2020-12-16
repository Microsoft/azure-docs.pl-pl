---
title: Seria ND — Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii ND.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: f37341233eec45a22fe92450e69d486e61f26eb4
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585313"
---
# <a name="nd-series"></a>Seria ND

Maszyny wirtualne z serii ND to nowe dodanie do rodziny procesorów GPU zaprojektowanej dla systemu AI i obciążeń głębokiego uczenia. Oferują one doskonałą wydajność na potrzeby szkoleń i wnioskowania. Wystąpienia ND są obsługiwane przez procesory [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) i procesory Intel Xeon E5-2690 v4 (Broadwell). Te wystąpienia zapewniają doskonałą wydajność operacji zmiennoprzecinkowych o pojedynczej precyzji dla obciążeń AI wykorzystujących Microsoft Cognitive Toolkit, TensorFlow, Caffe i inne platformy. Seria ND oferuje również znacznie większy rozmiar pamięci procesora GPU (24 GB), co umożliwia dopasowanie znacznie większych modeli sieci neuronowych. Podobnie jak w przypadku serii NC, Seria ND oferuje konfigurację z dodatkową siecią o małym opóźnieniu i wysokiej przepływności za pośrednictwem funkcji RDMA oraz łączności InfiniBand, dzięki czemu można uruchamiać zadania szkoleniowe o dużej skali obejmujące wiele procesorów GPU.

[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): nieobsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): nieobsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>
NVIDIA NVLink Interconnect: nieobsługiwane<br>

> [!IMPORTANT]
> Dla tej serii maszyn wirtualnych limit przydziału vCPU (rdzeń) na region w ramach subskrypcji jest początkowo ustawiony na 0. [Zażądaj zwiększenia limitu przydziału vCPU](../azure-portal/supportability/resource-manager-core-quotas-request.md) dla tej serii w [dostępnym regionie](https://azure.microsoft.com/regions/services/).
>
| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 24 | 32 | 80000/800 | 8 |
| Standard_ND24rs * | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 procesor GPU = jedna karta P40.

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
