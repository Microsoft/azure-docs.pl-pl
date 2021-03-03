---
title: Rozmiary maszyn wirtualnych platformy Azure — procesor GPU | Microsoft Docs
description: Wyświetla różne rozmiary procesora GPU dostępne dla maszyn wirtualnych na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych oraz o przepływności magazynu i przepustowości sieci dla rozmiarów w tej serii.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: b88cc2d70d38d84436beaff41b5b40b9c06debca
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695553"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych zoptymalizowane pod kątem procesorów GPU

Rozmiary maszyn wirtualnych zoptymalizowane pod kątem procesora GPU są wyspecjalizowanymi maszynami wirtualnymi z pojedynczym, wieloma lub ułamkowymi procesorami GPU. Te rozmiary są przeznaczone do obciążeń intensywnie korzystających z operacji obliczeniowych, intensywnie korzystających z grafiki i wizualizacji. Ten artykuł zawiera informacje o liczbie i typie procesorów GPU, procesorów wirtualnych vCPU, dysków danych i kart interfejsu sieciowego. Przepustowość magazynu i przepustowość sieci są również uwzględniane dla każdego rozmiaru w ramach grupowania.

- Rozmiary serii [Seria NCV3](ncv3-series.md) i [NC T4_v3](nct4-v3-series.md) są zoptymalizowane pod kątem aplikacji z przyspieszeniem procesora GPU intensywnie korzystających z mocy obliczeniowej. Przykładami są aplikacje i symulacje oparte na CUDA i OpenCL, a następnie w programie AI i uczeniu głębokiego. Seria NC T4 V3 jest skoncentrowana na obciążeniach wnioskowania z procesorem GPU Tesla T4 i procesorami AMD EPYC2 Rzym. Seria Seria NCV3 koncentruje się na obciążeniach obliczeniowych o wysokiej wydajności i pojemnościach AI z procesorem GPU Tesla V100.

- Rozmiar [serii NDv2](ndv2-series.md) koncentruje się na skalowalnych i skalowalnych w poziomie aplikacjach szkoleniowych edukacyjnych. Seria NDv2 używa procesorów NVIDIA Volta V100 oraz procesora Intel Xeon Platinum 8168 (Skylake).

- Rozmiary [serii NV](nv-series.md) i [NVv3](nvv3-series.md) są zoptymalizowane i zaprojektowane pod kątem zdalnego wizualizacji, przesyłania strumieniowego, gier, kodowania i infrastruktury VDI przy użyciu struktur, takich jak OpenGL i DirectX. Te maszyny wirtualne są obsługiwane przez procesor GPU NVIDIA Tesla M60.

- [Seria NVv4](nvv4-series.md) Rozmiary maszyn wirtualnych zoptymalizowane i zaprojektowane pod kątem infrastruktury VDI i zdalnej wizualizacji. W przypadku procesorów GPU z podziałem na partycje NVv4 oferuje odpowiedni rozmiar obciążeń wymagających mniejszych zasobów procesora GPU. Te maszyny wirtualne są obsługiwane przez procesor GPU z procesorem AMD Radeon Instinct MI25. Maszyny wirtualne NVv4 obsługują obecnie tylko system operacyjny gościa systemu Windows.

## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

Aby skorzystać z możliwości procesora GPU maszyn wirtualnych z serii N, należy zainstalować sterowniki NVIDIA lub AMD GPU.

- W przypadku maszyn wirtualnych, które są obsługiwane przez graficzne procesory NVIDIA, [rozszerzenie sterownika NVIDIA GPU](./extensions/hpccompute-gpu-windows.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub siatki. Zainstaluj rozszerzenie lub Zarządzaj nim przy użyciu Azure Portal lub narzędzi, takich jak szablony Azure PowerShell lub Azure Resource Manager. Zapoznaj się z [dokumentacją rozszerzenia sterownika graficznego procesora NVIDIA](./extensions/hpccompute-gpu-windows.md) dla obsługiwanych systemów operacyjnych i kroków wdrażania. Aby uzyskać ogólne informacje o rozszerzeniach maszyn wirtualnych, zobacz [rozszerzenia i funkcje maszyny wirtualnej platformy Azure](./extensions/overview.md).   

   Alternatywnie możesz zainstalować sterowniki NVIDIA GPU ręcznie. Zobacz [Instalowanie sterowników NVIDIA GPU na maszynach wirtualnych serii n z systemem Windows](./windows/n-series-driver-setup.md) lub [Instalowanie sterowników NVIDIA GPU na maszynach wirtualnych serii n z systemem Linux](./linux/n-series-driver-setup.md) dla obsługiwanych systemów operacyjnych, sterowników, instalacji i kroków weryfikacji.

- W przypadku maszyn wirtualnych, które są obsługiwane przez procesory GPU AMD, zobacz [Instalowanie sterowników AMD GPU na maszynach wirtualnych serii N z systemem Windows](./windows/n-series-amd-driver-setup.md) dla obsługiwanych systemów operacyjnych, sterowników, instalacji i kroków weryfikacyjnych.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

- Aby uzyskać dostęp do maszyn wirtualnych z serii N, zobacz [produkty dostępne według regionów](https://azure.microsoft.com/regions/services/).

- Maszyny wirtualne z serii N można wdrożyć tylko w modelu wdrażania Menedżer zasobów.

- Maszyny wirtualne serii N różnią się w zależności od typu magazynu platformy Azure obsługiwanego dla ich dysków. Maszyny wirtualne NC i NV obsługują tylko dyski maszyn wirtualnych, które są obsługiwane przez standardową Disk Storage (dysk twardy). Wszystkie inne maszyny wirtualne procesora GPU obsługują dyski maszyn wirtualnych, które są obsługiwane przez standardowe Disk Storage i Disk Storage w warstwie Premium (SSD).

- Jeśli chcesz wdrożyć więcej niż kilka maszyn wirtualnych serii N, weź pod uwagę subskrypcję z opcją płatność zgodnie z rzeczywistym użyciem lub inne opcje zakupu. Jeśli używasz [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), możesz użyć ograniczonej liczby rdzeni obliczeniowych platformy Azure.

- Może być konieczne zwiększenie przydziału rdzeni (na region) w ramach subskrypcji platformy Azure i zwiększenie oddzielnego przydziału rdzeni NC, NCv2, Seria NCV3, ND, NDv2, NV lub NVv2. Aby zażądać zwiększenia limitu przydziału, [Otwórz bezpłatnie żądanie pomocy technicznej w trybie online](../azure-portal/supportability/how-to-create-azure-support-request.md) . Limity domyślne mogą się różnić w zależności od kategorii subskrypcji.

## <a name="other-sizes"></a>Inne rozmiary

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
