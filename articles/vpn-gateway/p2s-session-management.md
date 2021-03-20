---
title: 'Azure VPN Gateway: zarządzanie sesjami sieci VPN typu punkt-lokacja'
description: Ten artykuł ułatwia wyświetlanie i rozłączanie sesji sieci VPN typu punkt-lokacja.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/23/2020
ms.author: cherylmc
ms.openlocfilehash: 2f2184507e17e3ecae40bb33be4202c183d32b77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91274237"
---
# <a name="point-to-site-vpn-session-management"></a>Zarządzanie sesjami sieci VPN typu punkt-lokacja

Bramy sieci wirtualnej platformy Azure zapewniają łatwy sposób wyświetlania i rozłączania bieżących sesji sieci VPN typu punkt-lokacja. Ten artykuł pomaga wyświetlać i odłączać bieżące sesje.

>[!NOTE]
>Stan sesji jest aktualizowany co 5 minut. Nie jest natychmiast aktualizowana.
>

## <a name="portal"></a>Portal

Aby wyświetlić i rozłączyć sesję w portalu:

1. Przejdź do bramy sieci VPN.
1. W sekcji **monitorowanie** wybierz **sesje punkt-lokacja**.

   :::image type="content" source="./media/p2s-session-management/portal.png" alt-text="Przykład portalu":::
1. Wszystkie bieżące sesje można wyświetlić w WindowPane.
1. Wybierz pozycję **"..."** dla sesji, którą chcesz odłączyć, a następnie wybierz pozycję **Rozłącz**.

## <a name="powershell"></a>PowerShell

Aby wyświetlić i rozłączyć sesję przy użyciu programu PowerShell:

1. Uruchom następujące polecenie programu PowerShell, aby wyświetlić listę aktywnych sesji:

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayVpnClientConnectionHealth -VirtualNetworkGatewayName <name of the gateway>  -ResourceGroupName <name of the resource group>
   ```
1. Skopiuj **VpnConnectionId** sesji, którą chcesz rozłączyć.

   :::image type="content" source="./media/p2s-session-management/powershell.png" alt-text="Przykład programu PowerShell":::
1. Aby rozłączyć sesję, uruchom następujące polecenie:

   ```azurepowershell-interactive
   Disconnect-AzVirtualNetworkGatewayVpnConnection -VirtualNetworkGatewayName <name of the gateway> -ResourceGroupName <name of the resource group> -VpnConnectionId <VpnConnectionId of the session>
   ```

## <a name="next-steps"></a>Następne kroki

Więcej informacji o połączeniach typu punkt-lokacja znajduje się w temacie [Informacje o sieci VPN typu punkt-lokacja](point-to-site-about.md).
