---
title: Jak skonfigurować OpenVPN dla platformy Azure VPN Gateway
description: Dowiedz się, jak włączyć protokół OpenVPN na platformie Azure VPN Gateway dla środowiska punkt-lokacja.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 137e4e1372ef1af3319c0b9af7ba965fffcb9e34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584044"
---
# <a name="configure-openvpn-for-point-to-site-vpn-gateways"></a>Konfigurowanie OpenVPN dla bram sieci VPN typu punkt-lokacja

W tym artykule opisano konfigurowanie **protokołu OpenVPN®** na platformie Azure VPN Gateway. Możesz użyć portalu lub instrukcji programu PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

* W tym artykule przyjęto założenie, że masz już środowisko pracy typu punkt-lokacja. Jeśli go nie masz, utwórz go przy użyciu jednej z poniższych metod.

  * [Portal — Tworzenie punktu na lokację](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell — Tworzenie punktu na lokację](vpn-gateway-howto-point-to-site-rm-ps.md)

* Sprawdź, czy Brama sieci VPN nie korzysta z podstawowej jednostki SKU. Podstawowa jednostka SKU nie jest obsługiwana w przypadku OpenVPN.

## <a name="portal"></a>Portal

1. W portalu przejdź do **bramy sieci wirtualnej — > konfigurację punkt-lokacja**.
1. W polu **Typ tunelu** wybierz pozycję **OpenVPN (SSL)** z listy rozwijanej.

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="Z listy rozwijanej wybierz pozycję OpenVPN SSL":::
1. Zapisz zmiany i przejdź do **następnych kroków**.

## <a name="powershell"></a>PowerShell

1. Włącz OpenVPN na bramie, korzystając z następującego przykładu:

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. Przejdź do **następnych kroków**.

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować klientów dla usługi OpenVPN, zobacz [Konfigurowanie klientów OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" jest znakiem towarowym OpenVPN Inc.**
