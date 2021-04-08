---
title: 'Azure VPN Gateway: anonsowanie tras niestandardowych dla klientów sieci VPN P2S'
description: Procedura anonsowania tras niestandardowych na klientach punkt-lokacja
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: a02bd5519b776a063646c11be2a34366fe429f99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89392395"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Anonsowanie tras niestandardowych klientom sieci VPN P2S

Możesz chcieć anonsować trasy niestandardowe wszystkim klientom sieci VPN typu punkt-lokacja. Na przykład jeśli włączono punkty końcowe magazynu w sieci wirtualnej i chcesz, aby użytkownicy zdalni mieli dostęp do tych kont magazynu za pośrednictwem połączenia sieci VPN. Adres IP punktu końcowego magazynu można anonsować wszystkim użytkownikom zdalnym w taki sposób, aby ruch do konta magazynu przeszedł przez tunel VPN, a nie publiczny Internet.

![Przykład połączenia obejmującego wiele lokacji w usłudze Azure VPN Gateway](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>Aby anonsować trasy niestandardowe

Aby anonsować trasy niestandardowe, użyj `Set-AzVirtualNetworkGateway cmdlet` . Poniższy przykład pokazuje, jak anonsować adres IP dla [tabel kont magazynu contoso](https://contoso.table.core.windows.net).

1. Wyślij polecenie ping do *contoso.Table.Core.Windows.NET* i Zanotuj adres IP. Na przykład:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Uruchom następujące polecenia programu PowerShell:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Aby dodać wiele tras niestandardowych, użyj przecinka i spacji w celu rozdzielenia adresów. Na przykład:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Aby wyświetlić trasy niestandardowe

Użyj poniższego przykładu, aby wyświetlić trasy niestandardowe:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>Aby usunąć trasy niestandardowe

Użyj poniższego przykładu, aby usunąć trasy niestandardowe:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje dotyczące routingu P2S, zobacz [Informacje o routingu punkt-lokacja](vpn-gateway-about-point-to-site-routing.md).
