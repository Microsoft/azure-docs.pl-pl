---
title: Test porównawczy aplikacji na Azure Disk Storage
description: Poznaj proces tworzenia testów porównawczych aplikacji na platformie Azure.
author: roygara
ms.author: rogarana
ms.date: 01/29/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: bfda14acc2e50005e25faafa3037805af871c1df
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094600"
---
# <a name="benchmark-a-disk"></a>Test porównawczy dysku

Testy porównawcze to proces symulowania różnych obciążeń w aplikacji i mierzenia wydajności aplikacji dla każdego obciążenia. Korzystając z kroków opisanych w [artykule projektowanie pod kątem wysokiej wydajności](premium-storage-performance.md), zostały zebrane wymagania dotyczące wydajności aplikacji. Uruchamiając narzędzia porównawcze na maszynach wirtualnych obsługujących aplikację, można określić poziomy wydajności, które aplikacja może osiągnąć przy użyciu dysków SSD Premium. W tym artykule przedstawiono przykłady testów porównawczych Standard_D8ds_v4 maszynę wirtualną, która została zainicjowana przy użyciu usługi Azure Premium dysków SSD.

W systemach Windows i Linux użyto odpowiednio wspólnych narzędzi do tworzenia testów porównawczych narzędzia diskspd i FIO. Narzędzia te duplikują wiele wątków symulowania produkcji, takich jak obciążenie i pomiar wydajności systemu. Za pomocą narzędzi można także skonfigurować parametry, takie jak rozmiar bloku i głębokość kolejki, które normalnie nie można zmienić dla aplikacji. Zapewnia to większą elastyczność w zakresie maksymalnego poziomu wydajności maszyny wirtualnej o dużej skali z obsługą warstwy Premium dysków SSD dla różnych typów obciążeń aplikacji. Aby dowiedzieć się więcej na temat każdego narzędzia testowego, odwiedź stronę [Narzędzia diskspd](https://github.com/Microsoft/diskspd/wiki/) i [FIO](http://freecode.com/projects/fio).

Aby postępować zgodnie z poniższymi przykładami, Utwórz Standard_D8ds_v4 i Dołącz cztery warstwy Premium dysków SSD do maszyny wirtualnej. Z czterech dysków należy skonfigurować trzy z buforowaniem hosta jako "none" i umieścić je w woluminie o nazwie NoCacheWrites. Skonfiguruj buforowanie hosta jako "ReadOnly" na pozostałym dysku i Utwórz wolumin o nazwie CacheReads z tym dyskiem. Za pomocą tej konfiguracji można zobaczyć maksymalną wydajność odczytu i zapisu z maszyny wirtualnej Standard_D8ds_v4. Aby uzyskać szczegółowe instrukcje dotyczące tworzenia Standard_D8ds_v4 przy użyciu dysków SSD Premium, zobacz [projektowanie pod kątem wysokiej wydajności](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Następne kroki

Zajrzyj do naszego artykułu na temat [projektowania pod kątem wysokiej wydajności](premium-storage-performance.md).

W tym artykule opisano tworzenie listy kontrolnej podobnej do istniejącej aplikacji dla prototypu. Korzystając z narzędzi testowych, można symulować obciążenia i mierzyć wydajność aplikacji prototypowej. Dzięki temu można określić, które oferty dysku mogą być zgodne lub przekraczać wymagania dotyczące wydajności aplikacji. Następnie można zaimplementować te same wytyczne dla aplikacji produkcyjnej.
