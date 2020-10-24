---
title: Wydajność maszyny wirtualnej i dysku
description: Dowiedz się więcej o tym, jak maszyny wirtualne i ich dołączone dyski działają w połączeniu z wydajnością
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 092368bb66784a00d5116da0b6be6513f8ebb261
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518081"
---
# <a name="virtual-machine-and-disk-performance"></a>Wydajność maszyny wirtualnej i dysku
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Limity pamięci podręcznej niebuforowanej maszyny wirtualnej
Maszyny wirtualne, które są włączone dla buforowania Premium Storage i Premium Storage, mają dwa różne limity przepustowości magazynu. Przyjrzyjmy się Standard_D8s_v3 maszynie wirtualnej na przykład. Oto dokumentacja dotycząca [serii Dsv3](../dv3-dsv3-series.md) i Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Uruchommy test porównawczy dla tej maszyny wirtualnej i kombinacji dysków, która tworzy działanie we/wy. Aby dowiedzieć się, jak testować we/wy magazynu na platformie Azure, zobacz [test porównawczy aplikacji na Azure Disk Storage](disks-benchmarks.md). W narzędziu testowego można zobaczyć, że połączenie maszyny wirtualnej i dysku można osiągnąć 22 800 operacji we/wy na sekundę:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]
