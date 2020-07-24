---
title: Testy porównawcze aplikacji na Azure Disk Storage
description: Poznaj proces tworzenia testów porównawczych aplikacji na platformie Azure.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: c1dfcad5a4fb6611543e6421e82889f239f14951
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87010834"
---
# <a name="benchmark-your-application-on-azure-disk-storage"></a>Test porównawczy aplikacji na Azure Disk Storage

Testy porównawcze to proces symulowania różnych obciążeń w aplikacji i mierzenia wydajności aplikacji dla każdego obciążenia. Wykonaj kroki opisane w [artykule projektowanie pod kątem wysokiej wydajności](premium-storage-performance.md). Uruchamiając narzędzia do testów porównawczych na maszynach wirtualnych obsługujących aplikację, można określić poziomy wydajności, które mogą osiągnąć aplikacja Premium Storage. W tym artykule przedstawiono przykłady testów porównawczych standardowej maszyny wirtualnej DS14, która została zainicjowana przy użyciu dysków Premium Storage platformy Azure.

W systemach Windows i Linux użyto odpowiednio wspólnych narzędzi do tworzenia testów porównawczych IOMeter i FIO. Narzędzia te duplikują wiele wątków symulowania produkcji, takich jak obciążenie i pomiar wydajności systemu. Za pomocą narzędzi można także skonfigurować parametry, takie jak rozmiar bloku i głębokość kolejki, które normalnie nie można zmienić dla aplikacji. Zapewnia to większą elastyczność w zakresie zwiększania wydajności maszyny wirtualnej o dużej skali z dyskami w warstwie Premium dla różnych typów obciążeń aplikacji. Aby dowiedzieć się więcej na temat każdego narzędzia testowego, odwiedź stronę [IOMeter](http://www.iometer.org/) i [FIO](http://freecode.com/projects/fio).

Aby postępować zgodnie z poniższymi przykładami, należy utworzyć standardową maszynę wirtualną DS14 i dołączyć do niej 11 Premium Storage dysków. Z 11 dysków należy skonfigurować 10 dysków z buforowaniem hosta jako "none" i umieścić je w woluminie o nazwie NoCacheWrites. Skonfiguruj buforowanie hosta jako "ReadOnly" na pozostałym dysku i Utwórz wolumin o nazwie CacheReads z tym dyskiem. Za pomocą tej konfiguracji można zobaczyć maksymalną wydajność odczytu i zapisu ze standardowej maszyny wirtualnej DS14. Aby uzyskać szczegółowe instrukcje dotyczące tworzenia maszyny wirtualnej DS14 z dyskami Premium, przejdź do [projektowania pod kątem wysokiej wydajności](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Następne kroki

Zajrzyj do naszego artykułu na temat [projektowania pod kątem wysokiej wydajności](premium-storage-performance.md).

W tym artykule opisano tworzenie listy kontrolnej podobnej do istniejącej aplikacji dla prototypu. Korzystając z narzędzi testowych, można symulować obciążenia i mierzyć wydajność aplikacji prototypowej. Dzięki temu można określić, które oferty dysku mogą być zgodne lub przekraczać wymagania dotyczące wydajności aplikacji. Następnie można zaimplementować te same wytyczne dla aplikacji produkcyjnej.