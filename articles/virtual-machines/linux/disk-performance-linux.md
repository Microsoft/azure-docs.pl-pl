---
title: Wydajność maszyny wirtualnej i dysku
description: Dowiedz się więcej o tym, jak maszyny wirtualne i ich dołączone dyski działają w połączeniu z wydajnością
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 3b849640bc7adb38fe51c3c7a9dda2d1d14f35fa
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017254"
---
# <a name="virtual-machine-and-disk-performance"></a>Wydajność maszyny wirtualnej i dysku
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Limity pamięci podręcznej niebuforowanej maszyny wirtualnej
Maszyny wirtualne, które są włączone w Premium Storage, i obsługują buforowanie magazynu w warstwie Premium mają dwa różne limity przepustowości magazynu. Przejdźmy na przykład na Standard_D8s_v3 maszynie wirtualnej. Oto dokumentacja dotycząca [serii Dsv3](../dv3-dsv3-series.md) i Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Uruchommy test porównawczy dla tej maszyny wirtualnej i kombinacji dysków, która spowoduje utworzenie działania we/wy i Dowiedz się więcej na temat sposobu testowania operacji we/wy magazynu na platformie Azure w [tym miejscu](disks-benchmarks.md). W narzędziu testowego można zobaczyć, że połączenie maszyny wirtualnej i dysku jest w stanie osiągnąć 22 800 operacji we/wy na sekundę:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]