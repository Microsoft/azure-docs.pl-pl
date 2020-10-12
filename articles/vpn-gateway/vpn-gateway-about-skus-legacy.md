---
title: Starsza jednostka SKU bramy sieci wirtualnej platformy Azure
description: Jak korzystać ze starych jednostek SKU bramy sieci wirtualnej; Basic, standard i HighPerformance.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 9c5e6d5aca51bd560a46837ba47de86362665773
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84687790"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Praca z jednostkami SKU bramy sieci wirtualnej (starszą wersją SKU)

Ten artykuł zawiera informacje o starszych (starych) jednostkach SKU bramy sieci wirtualnej. Starsze jednostki SKU nadal działają w obu modelach wdrażania dla bram sieci VPN, które zostały już utworzone. Klasyczne bramy sieci VPN nadal korzystają ze starszych wersji SKU, zarówno dla istniejących bram, jak i dla nowych bram. Podczas tworzenia nowych bram sieci VPN Menedżer zasobów Użyj nowych jednostek SKU bramy. Aby uzyskać informacje na temat nowych jednostek SKU, zobacz [Informacje o VPN Gateway](vpn-gateway-about-vpngateways.md).

## <a name="gateway-skus"></a><a name="gwsku"></a>Jednostki SKU bramy

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

Cennik starszej bramy można wyświetlić w sekcji **Virtual Network Gateways** , która znajduje się na [stronie z cennikiem ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="estimated-aggregate-throughput-by-sku"></a><a name="agg"></a>Szacowana agregowana przepływność według jednostki SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="supported-configurations-by-sku-and-vpn-type"></a><a name="config"></a>Obsługiwane konfiguracje według jednostki SKU i typu sieci VPN

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize-a-gateway"></a><a name="resize"></a>Zmiana rozmiaru bramy

Można zmienić rozmiar bramy do jednostki SKU bramy w tej samej rodzinie SKU. Na przykład jeśli masz standardową jednostkę SKU, możesz zmienić rozmiar do jednostki SKU HighPerformance. Nie można jednak zmienić rozmiaru bramy sieci VPN między starymi jednostkami SKU i nowymi rodzinami SKU. Na przykład nie można przejść ze standardowej jednostki SKU do jednostki SKU VpnGw2 ani podstawowej jednostki SKU do VpnGw1.

### <a name="resource-manager"></a>Resource Manager

Aby zmienić rozmiar bramy dla Menedżer zasobówgo modelu wdrażania przy użyciu programu PowerShell, użyj następującego polecenia:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

Możesz również zmienić rozmiar bramy w Azure Portal.

### <a name="classic"></a><a name="classicresize"></a>Motyw

Aby zmienić rozmiar bramy dla klasycznego modelu wdrażania, należy użyć poleceń cmdlet programu PowerShell do zarządzania usługami. Użyj następującego polecenia:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change-to-the-new-gateway-skus"></a><a name="change"></a>Zmień na nowe jednostki SKU bramy

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat nowych jednostek SKU bramy, zobacz [jednostki SKU bramy](vpn-gateway-about-vpngateways.md#gwsku).

Więcej informacji o ustawieniach konfiguracji znajduje się w temacie [Informacje o ustawieniach konfiguracji VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).
