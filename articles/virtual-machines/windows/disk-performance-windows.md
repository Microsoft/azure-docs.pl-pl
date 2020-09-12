---
title: Wydajność maszyny wirtualnej i dysku
description: Dowiedz się więcej o tym, jak maszyny wirtualne i ich dołączone dyski działają w połączeniu z wydajnością
author: albecker1
ms.author: albecker
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: b3ef2c2c3b130478a8b2d3b3c3ce44a7c65b87fe
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664150"
---
# <a name="virtual-machine-and-disk-performance"></a>Wydajność maszyny wirtualnej i dysku
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Limity pamięci podręcznej niebuforowanej maszyny wirtualnej
 Maszyny wirtualne, które są włączone w ramach usługi Premium Storage i obsługują buforowanie magazynu w warstwie Premium, mają dwa różne limity przepustowości magazynu. Przejdźmy na przykład na Standard_D8s_v3 maszynie wirtualnej. Oto dokumentacja dotycząca [serii Dsv3](../dv3-dsv3-series.md) i Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]
