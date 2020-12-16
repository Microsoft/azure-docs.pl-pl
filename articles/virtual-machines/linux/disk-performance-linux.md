---
title: Wydajność maszyn wirtualnych i dysków — system Linux
description: Dowiedz się więcej o tym, jak maszyny wirtualne i ich dołączone dyski działają w połączeniu z wydajnością w systemie Linux.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 2c48672bcfd8c552b36e3ae0807135924669c1d9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591858"
---
# <a name="virtual-machine-and-disk-performance-linux"></a>Wydajność maszyn wirtualnych i dysków (Linux)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Limity pamięci podręcznej niebuforowanej maszyny wirtualnej
Maszyny wirtualne, które są włączone dla buforowania Premium Storage i Premium Storage, mają dwa różne limity przepustowości magazynu. Przyjrzyjmy się Standard_D8s_v3 maszynie wirtualnej na przykład. Oto dokumentacja dotycząca [serii Dsv3](../dv3-dsv3-series.md) i Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Uruchommy test porównawczy dla tej maszyny wirtualnej i kombinacji dysków, która tworzy działanie we/wy. Aby dowiedzieć się, jak testować we/wy magazynu na platformie Azure, zobacz [test porównawczy aplikacji na Azure Disk Storage](disks-benchmarks.md). W narzędziu testowego można zobaczyć, że połączenie maszyny wirtualnej i dysku można osiągnąć 22 800 operacji we/wy na sekundę:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]
