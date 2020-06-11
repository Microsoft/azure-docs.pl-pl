---
title: Seria NV — Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii NV.
author: vikancha
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: d6fef5726630ed04c0d0f4bec1189069b0ceb993
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84674707"
---
# <a name="nv-series"></a>Seria NV

Maszyny wirtualne z serii NV są obsługiwane przez procesory [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) i technologię NVIDIA GRID dla aplikacji klasycznych i pulpitów wirtualnych, na których klienci mogą wizualizować swoje dane lub symulacje. Użytkownicy mogą wizualizować przepływy pracy intensywnie korzystających z grafiki w wystąpieniach TRWAŁych w celu uzyskania najwyższej jakości grafiki i dodatkowo uruchamiania obciążeń o pojedynczej precyzji, takich jak kodowanie i renderowanie. Maszyny wirtualne z serii NV są również obsługiwane przez procesory Intel Xeon E5-2690 v3 (Haswell).

Każdy procesor GPU w wystąpieniach TRWAŁych zawiera licencję siatki. Ta licencja zapewnia elastyczność używania wystąpienia TRWAŁEgo jako wirtualnej stacji roboczej dla jednego użytkownika lub 25 jednoczesnych użytkowników może połączyć się z MASZYNą wirtualną w scenariuszu aplikacji wirtualnej.

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

Migracja na żywo: nieobsługiwane

Aktualizacje z zachowaniem pamięci: nieobsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych | Wirtualne stacje robocze | Aplikacje wirtualne |
|---|---|---|---|---|---|---|---|---|---|
| Standardowa_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standardowa_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standardowa_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 procesor GPU = połowa karty M60.

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
