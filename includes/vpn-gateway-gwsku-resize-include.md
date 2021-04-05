---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a3ae2a876d6a3772d941fec0b8a1ea3f537e60c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010865"
---
Można użyć `Resize-AzVirtualNetworkGateway` polecenia cmdlet programu PowerShell w celu uaktualnienia lub obniżenia poziomu SKU Generation1 lub Generation2 (wszystkie jednostki SKU VpnGw można zmienić z wyjątkiem podstawowych jednostek SKU). Jeśli używasz podstawowej jednostki SKU bramy, [zamiast tego użyj tych instrukcji](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) , aby zmienić rozmiar bramy.

Poniższy przykład programu PowerShell pokazuje jednostkę SKU bramy, która jest zmieniana na VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```