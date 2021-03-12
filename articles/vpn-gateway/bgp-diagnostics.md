---
title: Wyświetlanie stanu i metryk protokołu BGP
titleSuffix: Azure VPN Gateway
description: Wyświetlanie ważnych informacji związanych z protokołem BGP na potrzeby rozwiązywania problemów.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: sample
ms.date: 03/10/2021
ms.author: alzam
ms.openlocfilehash: f97bbccc980705699af822ba2730233239cdfd5f
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103148850"
---
# <a name="view-bgp-metrics-and-status"></a>Wyświetl metryki i stan BGP

Metryki i Stany protokołu BGP można wyświetlić za pomocą Azure Portal lub przy użyciu Azure PowerShell.

## <a name="azure-portal"></a>Azure Portal

W Azure Portal można wyświetlić elementy równorzędne BGP, trasy i trasy anonsowane. Możesz również pobrać pliki CSV zawierające te dane.

1. W [Azure Portal](https://portal.azure.com)przejdź do bramy sieci wirtualnej.
1. W obszarze **monitorowanie** wybierz pozycję **elementy równorzędne BGP** , aby otworzyć stronę elementy równorzędne BGP.

   :::image type="content" source="./media/bgp-diagnostics/bgp-portal.jpg" alt-text="Zrzut ekranu metryk w Azure Portal.":::

### <a name="learned-routes"></a>Informacje o trasach

1. Możesz wyświetlić do 50 wystawionych tras w portalu.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes.jpg" alt-text="Zrzut ekranu przedstawiający trasy.":::

1. Możesz również pobrać plik trasy z zauczeniem. Jeśli masz więcej niż 50 zapoznawczych tras, jedynym sposobem wyświetlania wszystkich z nich jest pobranie i przejrzenie pliku CSV. Aby pobrać, wybierz pozycję **Pobierz trasy zapoznaj**.

   :::image type="content" source="./media/bgp-diagnostics/download-routes.jpg" alt-text="Zrzut ekranu przedstawiający pobieranie tras zapamiętanych.":::
1. Następnie Wyświetl plik.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes-file.jpg" alt-text="Zrzut ekranu przedstawiający pobrane trasy.":::

### <a name="advertised-routes"></a>Anonsowane trasy

1. Aby wyświetlić trasy anonsowane, wybierz pozycję **...** na końcu sieci, którą chcesz wyświetlić, a następnie kliknij pozycję **Wyświetl anonsowane trasy**.

   :::image type="content" source="./media/bgp-diagnostics/select-route.png" alt-text="Zrzut ekranu przedstawiający sposób wyświetlania anonsowanych tras." lightbox="./media/bgp-diagnostics/route-expand.png":::
1. Na stronie **trasy anonsowane do elementu równorzędnego** można wyświetlić do 50 anonsowanych tras.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes.jpg" alt-text="Zrzut ekranu przedstawiający trasy anonsowane.":::
1. Możesz również pobrać plik anonsowanych tras. Jeśli masz więcej niż 50 anonsowanych tras, jedynym sposobem wyświetlania wszystkich z nich jest pobranie i przejrzenie pliku CSV. Aby pobrać, wybierz pozycję **Pobierz anonsowane trasy**.

   :::image type="content" source="./media/bgp-diagnostics/download-advertised.jpg" alt-text="Zrzut ekranu przedstawiający wybór pobranych anonsowanych tras.":::
1. Następnie Wyświetl plik.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes-file.jpg" alt-text="Zrzut ekranu przedstawiający pobrane trasy anonsowane.":::

### <a name="bgp-peers"></a>Elementy równorzędne protokołu BGP

1. Możesz wyświetlić do 50 elementów równorzędnych BGP w portalu.

   :::image type="content" source="./media/bgp-diagnostics/peers.jpg" alt-text="Zrzut ekranu elementów równorzędnych protokołu BGP.":::
1. Można również pobrać plik elementów równorzędnych protokołu BGP. Jeśli masz więcej niż 50 elementów równorzędnych BGP, jedynym sposobem wyświetlania wszystkich z nich jest pobranie i przejrzenie pliku CSV. Aby pobrać, wybierz pozycję **Pobierz elementy równorzędne BGP** na stronie portalu.

   :::image type="content" source="./media/bgp-diagnostics/download-peers.jpg" alt-text="Zrzut ekranu przedstawiający pobieranie elementów równorzędnych BGP.":::
1. Następnie Wyświetl plik.

   :::image type="content" source="./media/bgp-diagnostics/peers-file.jpg" alt-text="Zrzut ekranu przedstawiający pobrane elementy równorzędne BGP.":::

## <a name="powershell"></a>PowerShell

Użyj **Get-AzVirtualNetworkGatewayBGPPeerStatus** , aby wyświetlić wszystkie elementy równorzędne BGP i stan.

[!INCLUDE [VPN Gateway PowerShell instructions](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBgpPeerStatus -ResourceGroupName resourceGroup -VirtualNetworkGatewayName gatewayName

Asn               : 65515
ConnectedDuration : 9.01:04:53.5768637
LocalAddress      : 10.1.0.254
MessagesReceived  : 14893
MessagesSent      : 14900
Neighbor          : 10.0.0.254
RoutesReceived    : 1
State             : Connected
```

Użyj **Get-AzVirtualNetworkGatewayLearnedRoute** , aby wyświetlić wszystkie trasy przechodzące przez bramę do protokołu BGP.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName resourceGroup -VirtualNetworkGatewayname gatewayName

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.1.0.0/16
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.0.0.254/32
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       : 65515
LocalAddress : 10.1.0.254
Network      : 10.0.0.0/16
NextHop      : 10.0.0.254
Origin       : EBgp
SourcePeer   : 10.0.0.254
Weight       : 32768
```

Użyj **Get-AzVirtualNetworkGatewayAdvertisedRoute** , aby wyświetlić wszystkie trasy, które Brama anonsuje do swoich elementów równorzędnych za pomocą protokołu BGP.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName gatewayName -ResourceGroupName resourceGroupName -Peer 10.0.0.254
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat protokołu BGP, zobacz [Konfigurowanie protokołu BGP dla VPN Gateway](bgp-howto.md).
