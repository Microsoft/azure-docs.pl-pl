---
title: Współlokowanie maszyn wirtualnych w celu zwiększenia opóźnienia
description: Dowiedz się, jak współlokowanie zasobów maszyny Wirtualnej platformy Azure może zwiększyć opóźnienia.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 066b6d10dbe07b902abfd15565c5ccf1a5e9c115
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083176"
---
# <a name="co-locate-resource-for-improved-latency"></a>Kololokuj zasób w celu zwiększenia opóźnienia

Podczas wdrażania aplikacji na platformie Azure rozmieszczanie wystąpień w różnych regionach lub strefach dostępności tworzy opóźnienie sieci, co może mieć wpływ na ogólną wydajność aplikacji. 


## <a name="proximity-placement-groups"></a>Grupy umieszczania w pobliżu 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Następne kroki

Wdrażanie maszyny Wirtualnej do [grupy miejsc docelowych zbliżeniowych](proximity-placement-groups.md) przy użyciu programu Azure PowerShell.

Dowiedz się, jak [przetestować opóźnienie sieci](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Dowiedz się, jak [zoptymalizować przepustowość sieci.](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)  

Dowiedz się, jak [korzystać z grup miejsc docelowych zbliżeniowych w aplikacjach SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)