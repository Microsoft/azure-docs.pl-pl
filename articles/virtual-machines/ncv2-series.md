---
title: NCv2 — seria Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii NCv2.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 3643fbabef08d890ce121d41a9bc1eb40c88459d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78273924"
---
# <a name="ncv2-series"></a>Seria NCv2

Maszyny wirtualne z serii NCv2 są obsługiwane przez procesory [NVIDIA Tesla P100](https://www.nvidia.com/data-center/tesla-p100/) GPU. Te procesory GPU mogą zapewnić ponad 2. wydajność obliczeniową serii NC. Klienci mogą korzystać z tych zaktualizowanych procesorów GPU dla tradycyjnych obciążeń HPC, takich jak modelowanie zbiorników, sekwencjonowanie DNA, analiza białka, symulacje Monte Carlo i inne. Poza procesorami GPU maszyny wirtualne serii NCv2 są również obsługiwane przez procesory Intel Xeon E5-2690 v4 (Broadwell).

Konfiguracja NC24rs v2 zapewnia małe opóźnienia i wysoką przepływność interfejsu sieciowego zoptymalizowane pod kątem ściśle sprzężonych równoległych obciążeń obliczeniowych.

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Migracja na żywo: nieobsługiwane

Aktualizacje z zachowaniem pamięci: nieobsługiwane

> [!IMPORTANT]
> W przypadku tej serii maszyn wirtualnych limit przydziału vCPU (rdzeń) w subskrypcji jest początkowo ustawiany na 0 w każdym regionie. [Zażądaj zwiększenia limitu przydziału vCPU](../azure-supportability/resource-manager-core-quotas-request.md) dla tej serii w [dostępnym regionie](https://azure.microsoft.com/regions/services/).
>
| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v2    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v2   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2 * | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 procesor GPU = jedna karta P100.

*Obsługa technologii RDMA

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

Aby skorzystać z możliwości procesora GPU maszyn wirtualnych z serii N, należy zainstalować sterowniki graficznego procesora NVIDIA.

[Rozszerzenie sterownika NVIDIA GPU](./extensions/hpccompute-gpu-windows.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub Grid na maszynie wirtualnej z serii N. Zainstaluj rozszerzenie lub Zarządzaj nim przy użyciu Azure Portal lub narzędzi, takich jak szablony Azure PowerShell lub Azure Resource Manager. Zapoznaj się z [dokumentacją rozszerzenia sterownika graficznego procesora NVIDIA](./extensions/hpccompute-gpu-windows.md) dla obsługiwanych systemów operacyjnych i kroków wdrażania. Aby uzyskać ogólne informacje o rozszerzeniach maszyn wirtualnych, zobacz [rozszerzenia i funkcje maszyny wirtualnej platformy Azure](./extensions/overview.md).

W przypadku wybrania opcji ręcznego instalowania sterowników procesora GPU firmy NVIDIA należy zapoznać się z tematem Konfiguracja [sterownika procesora GPU dla systemu Windows](./windows/n-series-driver-setup.md) lub [n-Series](./linux/n-series-driver-setup.md) w systemie Linux dla obsługiwanych systemów operacyjnych, sterowników, instalacji i kroków weryfikacyjnych.

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
