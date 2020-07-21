---
title: Wspólne lokalizowanie maszyn wirtualnych z systemem Linux
description: Dowiedz się więcej na temat sposobu lokalizowania zasobów maszyn wirtualnych platformy Azure w celu zwiększenia opóźnienia.
ms.service: virtual-machines
ms.topic: article
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 0a28b238701eeb0572149fb55e1633cdd40ec0a4
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86511028"
---
# <a name="co-locate-resources-for-improved-latency"></a>Wspólne Lokalizowanie zasobów w celu uzyskania lepszych opóźnień

Podczas wdrażania aplikacji na platformie Azure rozproszenie wystąpień między regionami lub strefami dostępności tworzy opóźnienie sieci, co może mieć wpływ na ogólną wydajność aplikacji. 

## <a name="proximity-placement-groups"></a>Grupy umieszczania w pobliżu

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Następne kroki

Wdróż maszynę wirtualną w [grupie umieszczania sąsiedztwa](proximity-placement-groups.md) przy użyciu interfejsu wiersza polecenia platformy Azure.

Dowiedz się, jak [testować opóźnienia sieci](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Dowiedz się, jak [zoptymalizować przepływność sieci](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Dowiedz się, jak [używać grup umieszczania zbliżeniowe z aplikacjami SAP](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
