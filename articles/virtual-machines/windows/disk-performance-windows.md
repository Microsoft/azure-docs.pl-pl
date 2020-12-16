---
title: Wydajność maszyny wirtualnej i dysku — Windows
description: Dowiedz się więcej o tym, jak maszyny wirtualne i ich dołączone dyski działają w połączeniu z wydajnością w systemie Windows.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: dec8b0cc33a9fffa7cac1ac9261b3c38ef5a6449
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584123"
---
# <a name="virtual-machine-and-disk-performance-windows"></a>Wydajność maszyn wirtualnych i dysków (Windows)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Limity pamięci podręcznej niebuforowanej maszyny wirtualnej
 Maszyny wirtualne, które są włączone w ramach usługi Premium Storage i obsługują buforowanie magazynu w warstwie Premium, mają dwa różne limity przepustowości magazynu. Przejdźmy na przykład na Standard_D8s_v3 maszynie wirtualnej. Oto dokumentacja dotycząca [serii Dsv3](../dv3-dsv3-series.md) i Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Uruchommy test porównawczy dla tej maszyny wirtualnej i kombinacji dysków, która spowoduje utworzenie działania we/wy i Dowiedz się więcej na temat sposobu testowania operacji we/wy magazynu na platformie Azure w [tym miejscu](disks-benchmarks.md). W narzędziu testowego można zobaczyć, że połączenie maszyny wirtualnej i dysku jest w stanie osiągnąć 22 800 operacji we/wy na sekundę:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]